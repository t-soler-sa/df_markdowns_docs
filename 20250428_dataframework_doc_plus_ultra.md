# DataplatFormFramework


---
## File: ./setup.py
```python
from setuptools import setup, find_packages

setup(
    name='data_framework',
    version='1',
    description='Sample',
    license="MIT",
    author='WMI',
    author_email='sample@sample',
    python_requires='>=3.9',
    packages=find_packages(
        where='src',
        exclude=['tests', 'tests.*']
    ),
    package_dir={'': 'src'},
    include_package_data=True
)
```


---
## File: ./src/data_framework/dataflow/business.py
```python
from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface


class StagingToBusiness(DataFlowInterface):

    def __init__(self):
        super().__init__()


class CommonToBusiness(DataFlowInterface):

    def __init__(self):
        super().__init__()
```


---
## File: ./src/data_framework/dataflow/common.py
```python
from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface


class StagingToCommon(DataFlowInterface):

    def __init__(self):
        super().__init__()

```


---
## File: ./src/data_framework/dataflow/landing.py
```python
# landing.py
"""
Coordinates the data ingestion from the Landing layer into the Raw layer
within the Data Platform Framework.

This module implements the ProcessingCoordinator class responsible for:
- Reading files from landing storage
- Validating incoming files
- Detecting duplicates based on previous files
- Transforming files (e.g., XML/Excel/JSON) if needed
- Writing validated data to the Raw layer
"""

from data_framework.modules.dataflow.interface_dataflow import (
    DataFlowInterface,
    ExecutionMode
)
from data_framework.modules.config.model.flows import (
    DateLocated,
    LandingFileFormat
)
from data_framework.modules.storage.core_storage import Storage
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from data_framework.modules.storage.interface_storage import Layer
from data_framework.modules.exception.landing_exceptions import (
    FileProcessError,
    FileReadError,
    InvalidDateRegexError,
    InvalidRegexGroupError,
    InvalidFileError
)
from data_framework.modules.utils import regex as regex_utils
import re
import hashlib
import json
from typing import Tuple
from datetime import datetime
from io import BytesIO
from pathlib import Path
from zipfile import ZipFile
import tarfile
from pandas import read_xml, read_excel


class ProcessingCoordinator(DataFlowInterface):

    def __init__(self):
        super().__init__()

        self.storage = Storage()
        self.catalogue = CoreCatalogue()
        self.parameters = self.config.parameters

    def process(self):

        if self.parameters.execution_mode == ExecutionMode.DELTA:
            self.process_file()
        else:
            prefix = f'{self.config.project_id}/on_demand/'
            response = self.storage.list_files(layer=Layer.LANDING, prefix=prefix)
            for s3_key in response.result:
                current_file = Path(s3_key).name

                pattern = self.incoming_file.filename_pattern
                valid_filename = bool(re.match(pattern, current_file))

                if valid_filename:
                    try:
                        self.logger.info(f'[PROCESSING] {current_file}')
                        self.parameters.source_file_path = s3_key
                        self.process_file()
                        self.logger.info(f'[DONE] {current_file}')
                    except Exception:
                        self.logger.info(f'[ERROR] {current_file}')
                else:
                    self.logger.info(f'[SKIP] {current_file}')

    def process_file(self):
        try:
            # Read file from S3
            file_contents = self.read_data()
            # Obtain file date
            file_date = self.obtain_file_date()
            # TODO: eliminar notificación
            self.notifications.send_notification(
                notification_name='file_arrival',
                arguments={
                    'dataflow': self.parameters.dataflow,
                    'process': self.parameters.process,
                    'file_name': Path(self.parameters.source_file_path).name,
                    'file_date': file_date
                }
            )
            # Apply controls
            response = self.quality_controls.validate(
                layer=Layer.LANDING,
                table_config=self.config.processes.landing_to_raw.output_file,
                framework_controls=True,
                file_date=file_date,
                file_contents=file_contents,
                file_name=Path(self.parameters.source_file_path).name
            )
            if response.overall_result:
                process_file = True
                # Compare with the previous file
                if self.incoming_file.compare_with_previous_file:
                    process_file = self.compare_with_previous_file(file_contents)
                if process_file:
                    # Create partitions
                    partitions = self.create_partitions(file_date)
                    # Save file in raw table
                    self.write_data(file_contents, partitions, file_date)
                    if self.config.processes.landing_to_raw.cancel_next_stage == False:
                        self.payload_response.next_stage = True
                self.payload_response.success = True
            else:
                raise InvalidFileError(file_path=self.parameters.source_file_path)
        except Exception:
            raise FileProcessError(file_path=self.parameters.source_file_path)

    def read_data(self) -> dict:
        try:
            response = self.storage.read(
                layer=Layer.LANDING,
                key_path=self.parameters.source_file_path
            )
            s3_file_content = BytesIO(response.data)
            filename = Path(self.parameters.source_file_path).name
            file_contents = {
                filename: {
                    'content': s3_file_content,
                    'validate': True
                }
            }
            if self.incoming_file.zipped == 'zip':
                file_contents[filename]['validate'] = False
                with ZipFile(s3_file_content, 'r') as z:
                    for filename in z.namelist():
                        with z.open(filename) as f:
                            file_contents[filename] = {
                                'content': BytesIO(f.read()),
                                'validate': True
                            }
            elif self.incoming_file.zipped == 'tar':
                file_contents[filename]['validate'] = False
                with tarfile.open(fileobj=s3_file_content, mode='r') as t:
                    for filename in t.getnames():
                        content = t.extractfile(filename).read()
                        file_contents[filename] = {
                            'content': BytesIO(content),
                            'validate': True
                        }
            return file_contents
        except Exception:
            raise FileReadError(file_path=self.parameters.source_file_path)

    def obtain_file_date(self) -> str:
        specifications = self.incoming_file.specifications
        if specifications.date_located == DateLocated.FILENAME:
            filename = Path(self.parameters.source_file_path).name
            pattern = specifications.date_located_filename.regex
            match = re.search(pattern, filename)
            if not match:
                raise InvalidDateRegexError(filename=filename, pattern=pattern)
            elif match.groupdict():
                # Custom year-month-day order
                try:
                    year = match.group('year')
                    month = match.group('month')
                    day = match.group('day')
                except IndexError:
                    raise InvalidRegexGroupError(pattern=pattern)
            else:
                # Default year-month-day order
                year, month, day = match.groups()
            return f'{year}-{month}-{day}'
        elif specifications.date_located == DateLocated.COLUMN:
            # TODO: implementar
            raise NotImplementedError('Feature date_located = column is not implemented yet')

    def compare_with_previous_file(self, file_contents: dict) -> bool:
        prefix = f'{self.config.project_id}/processed'
        response = self.storage.list_files(Layer.LANDING, prefix)
        if response.success:
            incoming_filename = Path(self.parameters.source_file_path).name
            last_file_key = self.get_last_processed_file_key(incoming_filename, response.result)
            if last_file_key:
                self.logger.info('Comparing with last processed file')
                incoming_file_content = file_contents[incoming_filename]['content']
                last_file_content = BytesIO(
                    self.storage.read(
                        layer=Layer.LANDING,
                        key_path=last_file_key
                    ).data
                )
                incoming_file_hash = self.get_file_hash(incoming_file_content)
                last_file_hash = self.get_file_hash(last_file_content)
                if incoming_file_hash == last_file_hash:
                    self.logger.info('Incoming file and last processed file are the same')
                    return False
        return True

    def get_last_processed_file_key(self, incoming_filename: str, file_keys: str) -> str:
        date_pattern = r'insert_date=(\d{4}-\d{2}-\d{2})/insert_time=(\d{2}:\d{2}:\d{2})'
        matching_files = []
        for file_key in file_keys:
            if file_key.endswith(incoming_filename):
                match = re.search(date_pattern, file_key)
                if match:
                    insert_date = match.group(1)
                    insert_time = match.group(2)
                    insert_datetime = datetime.strptime(
                        f'{insert_date} {insert_time}',
                        '%Y-%m-%d %H:%M:%S'
                    )
                    matching_files.append((insert_datetime, file_key))
        if len(matching_files) > 0:
            matching_files.sort(reverse=True, key=lambda x: x[0])
            return matching_files[0][1]
        else:
            return ''

    def get_file_hash(self, file_content: BytesIO, chunk_size: int = 8000):
        file_content.seek(0)
        hasher = hashlib.sha256()
        while chunk := file_content.read(chunk_size):
            hasher.update(chunk)
        hash_code = hasher.hexdigest()
        return hash_code

    def create_partitions(self, file_date: str) -> dict:
        partitions = {}
        partition_field = self.output_file.partition_field
        response = self.catalogue.create_partition(
            self.output_file.database_relation,
            self.output_file.table,
            partition_field,
            file_date
        )
        if response.success:
            partitions[partition_field] = file_date
        return partitions

    def write_data(self, file_contents: dict, partitions: dict, file_date: str) -> None:
        for filename, file_data in file_contents.items():
            if file_data['validate']:
                filename, file_content = self.normalize_file_content(
                    filename,
                    file_data['content'],
                    file_date
                )
                self.storage.write(
                    layer=Layer.RAW,
                    database=self.output_file.database,
                    table=self.output_file.table,
                    data=file_content,
                    partitions=partitions,
                    filename=filename
                )
                self.payload_response.file_name = filename
                self.payload_response.file_date = file_date

    def normalize_file_content(self, filename: str, file_content: BytesIO, file_date: str) -> Tuple[str, BytesIO]:
        file_content.seek(0)
        if self.incoming_file.file_format == LandingFileFormat.XML:
            return self.convert_xml_to_parquet(filename, file_content)
        elif self.incoming_file.file_format == LandingFileFormat.EXCEL:
            return self.convert_excel_to_parquet(filename, file_content)
        elif self.incoming_file.file_format == LandingFileFormat.JSON:
            file_content = self.convert_json_to_json_lines(file_content, file_date)
            return filename, file_content
        else:
            return filename, file_content

    def convert_xml_to_parquet(self, filename: str, file_content: BytesIO) -> Tuple[str, BytesIO]:
        parquet_filename = regex_utils.change_file_extension(filename, '.parquet')
        self.logger.info(f'Converting XML file {filename} to parquet file {parquet_filename}')
        df = read_xml(
            file_content,
            encoding=self.incoming_file.xml_specs.encoding,
            xpath=self.incoming_file.xml_specs.xpath,
            parser='etree',
            dtype=str
        )
        df = df.fillna('')
        parquet_file_content = BytesIO()
        df.to_parquet(parquet_file_content, index=False)
        parquet_file_content.seek(0)
        return parquet_filename, parquet_file_content

    def convert_excel_to_parquet(self, filename: str, file_content: BytesIO) -> Tuple[str, BytesIO]:
        parquet_filename = regex_utils.change_file_extension(filename, '.parquet')
        self.logger.info(f'Converting Excel file {filename} to parquet file {parquet_filename}')
        df = read_excel(file_content, dtype=str)
        df = df.fillna('')
        parquet_file_content = BytesIO()
        df.to_parquet(parquet_file_content, index=False)
        parquet_file_content.seek(0)
        return parquet_filename, parquet_file_content

    def convert_json_to_json_lines(self, file_content: BytesIO, file_date: str) -> BytesIO:
        encoding = self.incoming_file.json_specs.encoding
        json_content = json.load(file_content)
        json_lines_content = BytesIO()
        if isinstance(json_content, list):
            for item in json_content:
                item[self.output_file.partition_field] = file_date
                json_lines_content.write(json.dumps(item).encode(encoding) + b'\n')
        elif isinstance(json_content, dict):
            json_content[self.output_file.partition_field] = file_date
            json_lines_content.write(json.dumps(json_content).encode(encoding))
        json_lines_content.seek(0)
        return json_lines_content


if __name__ == '__main__':
    stb = ProcessingCoordinator()
    stb.process()

```


---
## File: ./src/data_framework/dataflow/output.py
```python
from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface, OutputResponse
from data_framework.modules.storage.core_storage import Storage
from data_framework.modules.storage.interface_storage import Layer
from data_framework.modules.config.model.flows import OutputReport
from data_framework.modules.exception.output_exceptions import (
    OutputError,
    OutputGenerationError,
    NoOutputDataError
)
from pyspark.sql import DataFrame
from datetime import datetime
from zoneinfo import ZoneInfo
from io import BytesIO
import re

TIME_ZONE = ZoneInfo('Europe/Madrid')


class ProcessingCoordinator(DataFlowInterface):

    def __init__(self):
        super().__init__()
        self.storage = Storage()

    def process(self) -> dict:
        self.payload_response.success = True
        # Generate all outputs
        for config_output in self.output_reports:
            try:
                self.generate_output_file(config_output)
            except Exception as e:
                error = OutputGenerationError(output_name=config_output.name, error_message=str(e))
                self.logger.error(error.format_exception())
                output = OutputResponse(
                    name=config_output.name,
                    success=False,
                    error=error
                )
                self.payload_response.outputs.append(output)
                self.payload_response.success = False
            else:
                output = OutputResponse(
                    name=config_output.name,
                    success=True
                )
                self.payload_response.outputs.append(output)
        if not self.payload_response.success:
            failed_outputs = self.payload_response.get_failed_outputs()
            raise OutputError(failed_outputs=failed_outputs)

    def generate_output_file(self, config_output: OutputReport) -> None:
        self.logger.info(f'Generating output {config_output.name}')
        # Obtain data
        df = self.retrieve_data(config_output)
        # Upload output data to S3
        if df and not df.isEmpty():
            self.write_data_to_file(df, config_output)
            self.logger.info(f'Output {config_output.name} generated successfully')
        else:
            raise NoOutputDataError(output_name=config_output.name)

    def retrieve_data(self, config_output: OutputReport) -> DataFrame:
        """
        Function to build sql a retrieve the dataframe with the data
        """
        if config_output.columns_alias:
            columns = [
                f"{column} as {column_alias}"
                for column, column_alias in zip(config_output.columns, config_output.columns_alias)
            ]
        else:
            columns = config_output.columns
        if config_output.where:
            _filter = self.format_string(config_output.where)
            self.logger.info(
                f'Obtaining data from {config_output.source_table.full_name} with filter {_filter}'
            )
        else:
            _filter = None
            self.logger.info(
                f'Obtaining data from {config_output.source_table.full_name}'
            )
        response = self.data_process.read_table(
            config_output.source_table.database_relation, config_output.source_table.table, _filter, columns
        )
        return response.data

    def write_data_to_file(self, df: DataFrame, config_output: OutputReport) -> None:
        """
        Function to write the dataframe with the data in storage
        """
        filename = self.format_string(config_output.filename_pattern, config_output.filename_date_format)
        output_folder = self.parse_output_folder(config_output.name)
        file_path = f"{self.config.project_id}/{output_folder}/inbound/{filename}"
        self.logger.info(f"Saving output '{config_output.name}' in {file_path}")

        file_to_save = BytesIO()

        if config_output.csv_specs:
            pdf = df.toPandas()
            pdf.to_csv(
                file_to_save,
                sep=config_output.csv_specs.delimiter,
                header=config_output.csv_specs.header,
                index=config_output.csv_specs.index,
                encoding=config_output.csv_specs.encoding
            )

        if config_output.json_specs:
            response = Storage.base_layer_path(layer=Layer.OUTPUT)
            tmp_write_path = f"{response.base_path}/{self.config.project_id}/{output_folder}/tmp/"
            df.coalesce(1).write.mode("overwrite").json(path=tmp_write_path)

            tmp_read_path = f"{self.config.project_id}/{output_folder}/tmp/"
            response = Storage.list_files(layer=Layer.OUTPUT, prefix=tmp_read_path)
            path_output_file = next((path for path in response.result if ".json" in path), "")
            response = Storage.read(layer=Layer.OUTPUT, key_path=path_output_file)

            file = response.data

            if config_output.replaces:
                file = file.decode('utf-8')
                for replace_dict in config_output.replaces:
                    for replace, new_value in replace_dict.items():
                        file = file.replace(replace, new_value)
                file = file.encode('utf-8')

            file_to_save = BytesIO(file)

        response = self.storage.write_to_path(Layer.OUTPUT, file_path, file_to_save.getvalue())
        if response.success:
            self.notifications.send_notification(
                notification_name='output_generated',
                arguments={
                    'dataflow': self.config.parameters.dataflow,
                    'process': self.config.parameters.process,
                    'output_name': config_output.name,
                    'file_path': file_path
                }
            )

    @staticmethod
    def parse_output_folder(output_folder: str) -> str:
        return re.sub(
            r'\s+', '_',
            re.sub(
                r'[^a-z\s]', '',
                output_folder.lower().strip()
            )
        )

    def format_string(self, string_to_format: str, date_format: str = '%Y-%m-%d') -> str:
        # TODO: permitir argumentos custom
        formatted_string = string_to_format.format(
            file_date=self.config.parameters.file_date,
            file_name=self.config.parameters.file_name,
            current_date=datetime.now(TIME_ZONE).strftime(date_format)
        )
        return formatted_string


if __name__ == '__main__':
    output = ProcessingCoordinator()
    response = output.process()

```


---
## File: ./src/data_framework/dataflow/staging.py
```python
from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface


class RawToStaging(DataFlowInterface):

    def __init__(self):
        super().__init__()

```


---
## File: ./src/data_framework/modules/catalogue/core_catalogue.py
```python
from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.catalogue.interface_catalogue import (
    CatalogueInterface,
    SchemaResponse,
    GenericResponse
)
from typing import Union


class CoreCatalogue:

    @LazyClassProperty
    def _catalogue(cls) -> CatalogueInterface:
        from data_framework.modules.catalogue.integrations.aws_glue import CatalogueAWSGlue
        return CatalogueAWSGlue()

    @staticmethod
    def create_partition(
        database: str,
        table: str,
        partition_field: str,
        value: Union[str, int]
    ) -> GenericResponse:
        return CoreCatalogue._catalogue.create_partition(
            database,
            table,
            partition_field,
            value
        )

    @staticmethod
    def get_schema(database: str, table: str) -> SchemaResponse:
        return CoreCatalogue._catalogue.get_schema(
            database,
            table
        )

```


---
## File: ./src/data_framework/modules/catalogue/integrations/aws_glue.py
```python
from data_framework.modules.catalogue.interface_catalogue import (
    CatalogueInterface,
    SchemaResponse,
    GenericResponse,
    Column,
    Schema
)
from data_framework.modules.exception.aws_exceptions import GlueError
from data_framework.modules.exception.catalogue_exceptions import (
    CreatePartitionError,
    InvalidPartitionFieldError,
    SchemaError
)
from data_framework.modules.utils.logger import logger
from data_framework.modules.config.core import config
from typing import Union, Any
import boto3


class CatalogueAWSGlue(CatalogueInterface):

    def __init__(self):
        self.cache = {}
        self.logger = logger
        self.config = config()
        self.glue_client = boto3.client('glue', region_name=config().parameters.region)

    def create_partition(
        self,
        database: str,
        table: str,
        partition_field: str,
        partition_value: Union[str, int]
    ) -> GenericResponse:
        """
        Function to create new partition in a table
        :param database: name of database of the table
        :param table: name of the table
        :param partition_field: name of the partition
        :param partition_value: value od the partition
        :return: GenericResponse
        """
        try:
            response = self.get_schema(database, table)
            l_cols_part = [
                column.name
                for column in response.schema.columns
                if column.ispartitioned is True
            ]
            if partition_field not in l_cols_part:
                raise InvalidPartitionFieldError(
                    database=database,
                    table=table,
                    partition_field=partition_field
                )
            else:
                try:
                    l_partitions_table = self.glue_client.get_partitions(
                        DatabaseName=database,
                        TableName=table
                    )
                except Exception as e:
                    raise GlueError(error_message=f'Error obtaining partitions of {database}.{table}: {e}')
                l_partitions_table_values = [elem['Values'] for elem in l_partitions_table['Partitions']]
                if [partition_value] in l_partitions_table_values:
                    self.logger.info(
                        f'Partition {partition_field}={partition_value} already exists in {database}.{table}'
                    )
                else:
                    table_gl = self._get_glue_table(database, table)
                    stg_desc_table_gl = table_gl['Table']['StorageDescriptor']
                    stg_desc = stg_desc_table_gl.copy()
                    location = stg_desc['Location']
                    location += f"/{partition_field}={partition_value}"
                    stg_desc['Location'] = location
                    partition_desc_l = [{
                        'Values': [partition_value],
                        'StorageDescriptor': stg_desc,
                        'Parameters': {}
                    }]
                    response_gc = self.glue_client.batch_create_partition(
                        DatabaseName=database,
                        TableName=table,
                        PartitionInputList=partition_desc_l
                    )
                    if 'Errors' in response_gc and response_gc['Errors']:
                        raise GlueError(error_message=str(response_gc['Errors']))
                    else:
                        self.logger.info(
                            f'Partition {partition_field}={partition_value} successfully created in {database}.{table}'
                        )
        except Exception:
            raise CreatePartitionError(
                database=database,
                table=table,
                partition_field=partition_field,
                partition_value=partition_value
            )
        else:
            return GenericResponse(success=True, error=None)

    def get_schema(self, database: str, table: str) -> SchemaResponse:
        """
        Function to get schema(list of columns(name and type) of a table
        :param database: name of database of the table
        :param table: name of the table
        :return: SchemaResponse
        """
        try:
            cache_key = f'schema.{database}.{table}'
            if cache_key in self.cache:
                return self.cache.get(cache_key)
            table_gl = self._get_glue_table(database, table)
            l_columns = table_gl['Table']['StorageDescriptor']['Columns']
            l_names = [column['Name'] for column in l_columns]
            l_types = [column['Type'] for column in l_columns]
            l_ispartitioned = [False for _ in l_columns]
            if table_gl['Table'].get('PartitionKeys'):
                l_partition_keys = table_gl['Table']['PartitionKeys']
                l_partition_keys_names = [column['Name'] for column in l_partition_keys]
                l_partition_keys_types = [column['Type'] for column in l_partition_keys]
                l_partition_keys_ispartitioned = [True for _ in l_partition_keys]
                l_names.extend(l_partition_keys_names)
                l_types.extend(l_partition_keys_types)
                l_ispartitioned.extend(l_partition_keys_ispartitioned)
            n_cols = len(l_names)
            l_order = [number+1 for number in range(n_cols)]
            l_schema_zip = list(zip(l_names, l_types, l_order, l_ispartitioned))
            l_schema = [Column(elem[0], elem[1], elem[2], elem[3]) for elem in l_schema_zip]
            schema = Schema(columns=l_schema)
            response = SchemaResponse(success=True, error=None, schema=schema)
            self.cache[cache_key] = response
            return response
        except Exception:
            raise SchemaError(database=database, table=table)

    def _get_glue_table(self, database: str, table: str) -> Any:
        try:
            response = self.glue_client.get_table(
                DatabaseName=database,
                Name=table
            )
            return response
        except Exception as e:
            raise GlueError(error_message=f'Error obtaining table {database}.{table}: {e}')

```


---
## File: ./src/data_framework/modules/catalogue/interface_catalogue.py
```python
from abc import ABC, abstractmethod
from typing import Union, Optional, List, Dict
from dataclasses import dataclass


@dataclass
class Column:
    name: str
    type: str
    order: int
    ispartitioned: bool


@dataclass
class Schema:
    columns: (Column)

    def get_column_names(self, partitioned: bool = False) -> List[str]:
        if partitioned:
            return [column.name for column in self.columns]
        else:
            return [column.name for column in self.columns if not column.ispartitioned]

    def get_type_columns(self, partitioned: bool = False) -> List[str]:
        if partitioned:
            return [column.type for column in self.columns]
        else:
            return [column.type for column in self.columns if not column.ispartitioned]

    def get_column_type_mapping(self, partitioned: bool = False) -> Dict[str, str]:
        if partitioned:
            return {
                column.name.lower(): column.type
                for column in self.columns
            }
        else:
            return {
                column.name.lower(): column.type
                for column in self.columns
                if not column.ispartitioned
            }


@dataclass
class SchemaResponse:
    success: bool
    error: Optional[str] = None
    schema: Optional[Schema] = None


@dataclass
class GenericResponse:
    success: bool
    error: Optional[str] = None


class CatalogueInterface(ABC):

    @abstractmethod
    def create_partition(
        self,
        database: str,
        table: str,
        artition_field: str,
        partition_value: Union[str, int]
    ) -> GenericResponse:
        # Abstract class to define the basic storage interface
        pass

    @abstractmethod
    def get_schema(self, database: str, table: str) -> SchemaResponse:
        # Abstract method to write data to a specific location
        pass

```


---
## File: ./src/data_framework/modules/code/filter_kwargs.py
```python
from typing import Dict, Any
from inspect import signature


def filter_function_kwargs(kwargs: Dict[str, Any], function: Any) -> Dict[str, Any]:
    filtered_kwargs = {
        key: value
        for key, value in kwargs.items()
        if key in signature(function).parameters
    }
    return filtered_kwargs

```


---
## File: ./src/data_framework/modules/code/lazy_class_property.py
```python
class LazyClassProperty:

    def __init__(self, func):
        self.func = func
        self.attr_name = f"_{func.__name__}"

    def __get__(self, instance, owner):
        if not hasattr(owner, self.attr_name):
            setattr(owner, self.attr_name, self.func(owner))
        return getattr(owner, self.attr_name)

```


---
## File: ./src/data_framework/modules/config/core.py
```python
from typing import Type, TypeVar, Union, get_type_hints, get_origin, get_args
from data_framework.modules.config.model.flows import (
    Processes,
    LandingToRaw,
    ToOutput,
    IncomingFileLandingToRaw,
    DateLocatedFilename,
    Parameters,
    CSVSpecs,
    XMLSpecs,
    JSONSpecs,
    Config,
    DatabaseTable,
    ProcessingSpecifications,
    Hardware,
    SparkConfiguration,
    OutputReport,
    GenericProcess,
    TableDict,
    CSVSpecsReport,
    JSONSpecsReport,
    VolumetricExpectation,
    Platform,
    Technologies,
    Environment,
    ProcessVars,
    Casting,
    Transformation,
    DataControl
)
from data_framework.modules.notification.interface_notifications import (
    NotificationDict,
    DataFrameworkNotifications,
    Notification,
    NotificationsParameters
)
from data_framework.modules.exception.config_exceptions import (
    ConfigError,
    ConfigFileNotFoundError,
    ConfigParseError,
    AccountNotFoundError,
    ParameterParseError
)
from data_framework.modules.exception.aws_exceptions import STSError
import threading
import os
import sys
from enum import Enum
import json
from pathlib import Path
import zipfile
import boto3

T = TypeVar('T')


def config(parameters: dict = None, reset: bool = False) -> Config:
    if ConfigSetup._instancia is None or reset:
        return ConfigSetup(parameters)._instancia.config
    else:
        return ConfigSetup._instancia.config


class ConfigSetup:

    _instancia = None
    _lock = threading.Lock()
    _environment: None

    _models = (
        Processes, LandingToRaw, GenericProcess, ToOutput, CSVSpecs, XMLSpecs, JSONSpecs, IncomingFileLandingToRaw,
        DateLocatedFilename, DatabaseTable, ProcessingSpecifications,
        Hardware, SparkConfiguration,
        OutputReport, CSVSpecsReport, JSONSpecsReport,
        VolumetricExpectation, Notification, Casting, Transformation, DataControl,
        DataFrameworkNotifications, NotificationsParameters
    )

    def __new__(cls, *args, **kwargs):
        if cls._instancia is None:
            with cls._lock:
                if cls._instancia is None:
                    cls._instancia = super(ConfigSetup, cls).__new__(cls)
        return cls._instancia

    def __init__(self, parameters: dict = None):
        try:
            if not parameters:
                parameters = {}
                try:
                    for i in range(1, len(sys.argv), 2):
                        key = sys.argv[i].replace('--', '').replace('-', '_')
                        value = sys.argv[i+1]
                        parameters[key] = value
                except Exception:
                    raise ParameterParseError(arguments=sys.argv)

            data_framework_config = ConfigSetup.read_data_framework_config()
            parameters['bucket_prefix'] = data_framework_config['s3_bucket_prefix']
            platform = data_framework_config.get('platform', Platform.DATA_PLATFORM.value)
            if platform == Platform.INFINITY.value and not parameters.get('local_file'):
                # Custom configurarion for Infinity execution
                # TODO: remove when migrating to Data Platform
                parameters['dataflow'] = 'default'
                parameters['process'] = 'landing_to_raw'
                self._instancia.config = Config(
                    processes=Processes(
                        landing_to_raw=LandingToRaw(
                            incoming_file=None,
                            output_file=None,
                            processing_specifications=ProcessingSpecifications(
                                technology=Technologies.LAMBDA,
                            )
                        )
                    ),
                    environment=Environment(data_framework_config['environment']),
                    platform=Platform.INFINITY,
                    parameters=ConfigSetup.parse_to_model(
                        model=Parameters,
                        json_file=parameters
                    ),
                    project_id=Platform.INFINITY.value
                )
            else:
                dataflow_config = ConfigSetup.read_dataflow_config(
                    dataflow=parameters.get('dataflow'),
                    local_file=parameters.get('local_file'),
                    environment=data_framework_config['environment'],
                    platform=platform
                )

                self._instancia.config = ConfigSetup.parse_to_model(
                    model=Config,
                    parameters=parameters,
                    json_file=dataflow_config,
                    environment=data_framework_config['environment']
                )
        except Exception:
            self._instancia.config = None
            raise ConfigError()

    @classmethod
    def read_data_framework_config(cls) -> dict:
        # Obtain AWS account ID
        try:
            sts_client = boto3.client('sts', region_name=os.environ["AWS_REGION"])
            sts_client = boto3.client(
                'sts',
                region_name=os.environ["AWS_REGION"],
                endpoint_url=f'https://sts.{os.environ["AWS_REGION"]}.amazonaws.com'
            )
            response = sts_client.get_caller_identity()
            account_id = response['Account']
        except Exception:
            raise STSError(error_message='Error obtaining AWS account ID from STS for config setup')
        # Read data framework config file
        config_json = cls.read_config_file(
            absolute_path='data_framework/modules/config/data_framework_config.json',
            relative_path='data_framework_config.json'
        )
        # Search account ID in config file
        current_config = config_json.get(account_id)
        if not current_config:
            raise AccountNotFoundError(
                account_id=account_id, available_ids=list(config_json.keys())
            )
        else:
            return current_config

    @classmethod
    def read_notifications_config(cls) -> dict:
        # Read data framework notifications file
        notifications_config = cls.read_config_file(
            absolute_path='data_framework/modules/notification/data_framework_notifications.json',
            relative_path='../notification/data_framework_notifications.json'
        )
        return notifications_config

    @classmethod
    def read_config_file(cls, absolute_path: str, relative_path: str) -> dict:
        try:
            current_path = Path(__file__).resolve()
            if 'data_framework.zip' in current_path.parts:
                config_path = absolute_path
                zip_index = current_path.parts.index('data_framework.zip')
                zip_path = Path(*current_path.parts[:zip_index+1])
                with zipfile.ZipFile(zip_path, 'r') as z:
                    with z.open(config_path) as file:
                        config_json = dict(json.loads(file.read()))
            else:
                config_path = (current_path.parent / relative_path).resolve()
                with open(config_path) as file:
                    config_json = dict(json.loads(file.read()))
            return config_json
        except FileNotFoundError:
            raise ConfigFileNotFoundError(config_file_path=config_path)

    @classmethod
    def read_dataflow_config(cls, dataflow: str, local_file: str, environment: str, platform: str) -> dict:
        try:
            if local_file is not None:
                transformation_path = local_file
                with open(transformation_path) as file:
                    config_json = dict(json.loads(file.read()))
            else:
                path_absolute = Path(__file__).resolve()
                transformation_path = str(path_absolute.parent.parent.parent.parent.parent) + '/transformation.zip'
                with zipfile.ZipFile(transformation_path, 'r') as z:
                    with z.open('transformation.json') as file:
                        config_json = dict(json.loads(file.read()))
        except Exception:
            raise ConfigFileNotFoundError(config_file_path=transformation_path)
        dataflows = config_json.get('dataflows')
        common_flow_json = dataflows.get('default')
        current_flow_json = dataflows.get(dataflow, None)
        if current_flow_json is None:
            current_flow_json = common_flow_json
        else:
            current_flow_json = cls.merged_current_dataflow_with_default(
                current_dataflow=current_flow_json,
                default=common_flow_json
            )
        current_flow_json['environment'] = environment
        current_flow_json['platform'] = platform
        current_flow_json['project_id'] = config_json.get('project_id')
        current_flow_json['data_framework_notifications'] = cls.read_notifications_config()
        return current_flow_json

    @classmethod
    def merged_current_dataflow_with_default(cls, current_dataflow: dict, default: dict) -> dict:

        merged = current_dataflow.copy()

        for key, value in default.items():
            if key in merged and isinstance(merged[key], dict) and isinstance(value, dict):
                merged[key] = cls.merged_current_dataflow_with_default(merged[key], value)
            elif key in merged and isinstance(merged[key], list) and isinstance(value, list):
                merged[key] = merged[key] + value
            elif merged.get(key) is None:
                merged[key] = value

        return merged

    @classmethod
    def parse_to_model(cls, model: Type[T], json_file: dict, environment: str, parameters: dict = None) -> T:
        # TODO: refactorizar
        fieldtypes = get_type_hints(model)
        kwargs = {}
        model_instantiated = None

        try:
            for field, field_type in fieldtypes.items():
                default_value = None
                if isinstance(field_type, type) and issubclass(field_type, cls._models) and json_file:
                    kwargs[field] = cls.parse_to_model(
                        model=field_type,
                        json_file=json_file.get(field),
                        environment=environment
                    )
                elif isinstance(field_type, type) and issubclass(field_type, Enum) and json_file:
                    value = json_file.get(field)
                    if value:
                        kwargs[field] = field_type(value)
                    else:
                        kwargs[field] = field_type(getattr(model, field))
                elif isinstance(field_type, type) and issubclass(field_type, (TableDict)) and json_file:
                    tables = {
                        table_name: cls.parse_to_model(
                            model=DatabaseTable,
                            json_file=config,
                            environment=environment
                        )
                        for table_name, config in json_file.get(field, {}).items()
                    }
                    kwargs[field] = TableDict(tables)
                elif isinstance(field_type, type) and issubclass(field_type, (NotificationDict)) and json_file:
                    notifications = {
                        notification_name: cls.parse_to_model(
                            model=Notification,
                            json_file=config,
                            environment=environment
                        )
                        for notification_name, config in json_file.get(field, {}).items()
                    }
                    kwargs[field] = NotificationDict(notifications)
                elif isinstance(field_type, type) and issubclass(field_type, (Parameters)):
                    kwargs[field] = cls.parse_to_model(model=field_type, json_file=parameters, environment=environment)
                elif ProcessVars in get_args(field_type):
                    default = {'default': {}, 'develop': {}, 'preproduction': {}, 'production': {}}
                    all_vars = json_file.get(field, default)
                    variables = cls.merged_current_dataflow_with_default(
                        current_dataflow=all_vars.get(environment),
                        default=all_vars.get('default')
                    )

                    kwargs[field] = ProcessVars(_variables=variables)
                elif get_origin(field_type) is Union and any(model in get_args(field_type) for model in cls._models):
                    field_model = [model for model in cls._models if model in get_args(field_type)][0]
                    if json_file.get(field):
                        kwargs[field] = cls.parse_to_model(
                            model=field_model,
                            json_file=json_file.get(field),
                            environment=environment
                        )
                    elif type(None) in get_args(field_type):
                        if hasattr(model, field):
                            kwargs[field] = getattr(model, field)
                        else:
                            kwargs[field] = None
                elif get_origin(field_type) is list and any(model in get_args(field_type) for model in cls._models) and json_file:
                    field_model = [model for model in cls._models if model in get_args(field_type)][0]
                    if json_file.get(field):
                        kwargs[field] = [
                            cls.parse_to_model(
                                model=field_model.get_subclass_from_dict(field_item),
                                json_file=field_item,
                                environment=environment
                            )
                            if field_model in [Transformation, DataControl] else
                            cls.parse_to_model(model=field_model, json_file=field_item, environment=environment)
                            for field_item in json_file.get(field)
                        ]
                elif get_origin(field_type) is list and all(issubclass(item, Enum) for item in get_args(field_type)) and json_file:
                    kwargs[field] = [get_args(field_type)[0](value) for value in json_file.get(field)]
                else:
                    if hasattr(model, field):
                        default_value = getattr(model, field)
                    elif get_origin(field_type) is list:
                        default_value = []
                    if json_file:
                        kwargs[field] = json_file.get(field, default_value)
                    else:
                        kwargs[field] = default_value
            model_instantiated = model(**kwargs)
        except Exception:
            raise ConfigParseError(field=field, field_type=str(field_type))

        return model_instantiated

```


---
## File: ./src/data_framework/modules/config/model/flows.py
```python
from data_framework.modules.storage.interface_storage import Database, Layer
from data_framework.modules.notification.interface_notifications import (
    NotificationDict,
    DataFrameworkNotifications
)
from data_framework.modules.exception.config_exceptions import (
    EmptyProcessConfigError,
    ProcessNotFoundError,
    TableKeyError,
    TableConfigNotFoundError
)
from data_framework.modules.exception.data_process_exceptions import (
    TransformationNotImplementedError,
    DataControlNotImplementedError
)
from data_framework.modules.utils import regex as regex_utils
from dataclasses import dataclass, field, fields
from enum import Enum
from typing import Optional, List, Tuple, Union
import os


class Environment(Enum):
    LOCAL = "local"
    DEVELOP = "develop"
    PREPRODUCTION = "preproduction"
    PRODUCTION = "production"

class Platform(Enum):
    # TODO: remove when migrating Infinity to Data Platform
    DATA_PLATFORM = "data_platform"
    INFINITY = "infinity"

class DateLocated(Enum):
    FILENAME = "filename"
    COLUMN = "column"

class Technologies(Enum):
    LAMBDA = "lambda"
    EMR = "emr"

class LandingFileFormat(Enum):
    CSV = "csv"
    JSON = "json"
    # TODO: JSON Lines implementation
    JSON_LINES = "json_lines"
    EXCEL = "xls"
    XML = "xml"

class OutputFileFormat(Enum):
    CSV = "csv"
    JSON = "json"

class ExecutionMode(Enum):
    DELTA = "delta"
    FULL = "full"

    @property
    def is_delta(self) -> bool:
        return self.value == 'delta'

class JSONSpectFormat(Enum):
    LINES = "lines"
    COLUMNS = "columns"

class CastingStrategy(Enum):
    ONE_BY_ONE = "one_by_one"
    DYNAMIC = "dynamic"

class TransformationType(Enum):
    PARSE_DATES = "parse_dates"
    FIX_CHARACTERS = "fix_incompatible_characters"
    ADD_FILENAME_COLUMN = "add_filename_column"

class DataControlType(Enum):
    MASTER_FIELDS = "master_fields"

@dataclass
class Hardware:
    ram: int = 4
    cores: int = 2
    disk: int = 20

@dataclass
class VolumetricExpectation:
    data_size_gb: float = 0.1
    avg_file_size_mb: int = 100

@dataclass
class SparkConfiguration:
    full_volumetric_expectation: VolumetricExpectation = field(default_factory=VolumetricExpectation)
    delta_volumetric_expectation: VolumetricExpectation = field(default_factory=VolumetricExpectation)
    delta_custom: Optional[dict] = field(default_factory=dict)
    full_custom: Optional[dict] = field(default_factory=dict)

    @property
    def volumetric_expectation(self) -> VolumetricExpectation:

        from data_framework.modules.config.core import config

        if config().parameters.execution_mode == ExecutionMode.FULL:
            return self.full_volumetric_expectation

        return self.delta_volumetric_expectation

    @property
    def custom_config(self) -> dict:

        from data_framework.modules.config.core import config

        if config().parameters.execution_mode == ExecutionMode.FULL:
            return self.full_custom

        return self.delta_custom

    @property
    def config(self) -> dict:

        from data_framework.modules.config.core import config

        if (
            config().parameters.execution_mode == ExecutionMode.FULL and self.full_custom
            or config().parameters.execution_mode == ExecutionMode.DELTA and self.delta_custom
        ):
            return self.custom_config
        else:

            from data_framework.modules.data_process.integrations.spark.dynamic_config import DynamicConfig

            volumetric = self.volumetric_expectation
            return DynamicConfig.recommend_spark_config(
                    dataset_size_gb=volumetric.data_size_gb,
                    avg_file_size_mb=volumetric.avg_file_size_mb
                )

@dataclass
class ProcessingSpecifications:
    technology: Technologies = Technologies.EMR
    spark_configuration: SparkConfiguration = field(default_factory=SparkConfiguration)


@dataclass
class DateLocatedFilename:
    regex: str


class InterfaceSpecs:

    @property
    def read_config(self) -> dict:
        raise NotImplementedError('It is mandatory to implement read_config property')


@dataclass
class XMLSpecs(InterfaceSpecs):
    encoding: str
    xpath: str
    date_located: DateLocated
    date_located_filename: DateLocatedFilename

    @property
    def read_config(self) -> dict:
        return {}


@dataclass
class JSONSpecs(InterfaceSpecs):
    encoding: str
    date_located: DateLocated
    date_located_filename: DateLocatedFilename
    source_level: Optional[str]

    @property
    def read_config(self) -> dict:
        return {
            'encoding': self.encoding
        }

    @property
    def levels(self) -> List[str]:
        if self.source_level:
            return self.source_level.split('.')
        else:
            return []

@dataclass
class CSVSpecs(InterfaceSpecs):
    header_position: int
    header: bool
    encoding: str
    delimiter: str
    date_located: DateLocated
    date_located_filename: DateLocatedFilename
    escape: Optional[str] = None
    comment: Optional[str] = None
    null_value: Optional[str] = None
    nan_value: Optional[str] = None
    special_character: Optional[str] = None
    multiline: bool = False

    @property
    def read_config(self) -> dict:
        config = {
            "header": str(self.header).lower(),
            "encoding": self.encoding,
            "sep": self.delimiter,
            "multiLine": str(self.multiline).lower()
        }
        if self.special_character:
            config["quote"] = self.special_character
        if self.escape:
            config["escape"] = self.escape
        if self.comment:
            config["comment"] = self.comment
        if self.null_value:
            config["nullValue"] = self.null_value
        if self.nan_value:
            config["nanValue"] = self.nan_value
        return config


@dataclass
class CSVSpecsReport:
    header: bool
    index: bool
    encoding: str
    delimiter: str


@dataclass
class JSONSpecsReport:
    format: JSONSpectFormat = JSONSpectFormat.LINES


@dataclass
class Parameters:
    dataflow: str
    process: str
    table: str
    source_file_path: str
    bucket_prefix: str
    file_name: Optional[str]
    file_date: Optional[str]
    execution_id: Optional[str]
    execution_mode: ExecutionMode = ExecutionMode.DELTA

    @property
    def region(self) -> str:
        return os.environ["AWS_REGION"]


@dataclass
class IncomingFileLandingToRaw:
    zipped: Optional[str]
    file_format: LandingFileFormat
    filename_pattern: str
    filename_unzipped_pattern: Optional[str]
    csv_specs: Optional[CSVSpecs]
    xml_specs: Optional[XMLSpecs]
    json_specs: Optional[JSONSpecs]
    compare_with_previous_file: Optional[bool] = False

    @property
    def specifications(self) -> Union[CSVSpecs, XMLSpecs, JSONSpecs]:
        if self.file_format == LandingFileFormat.XML:
            return self.xml_specs
        elif self.file_format == LandingFileFormat.JSON:
            return self.json_specs
        else:
            return self.csv_specs

@dataclass
class DataControl:
    type: DataControlType

    @classmethod
    def get_subclass_from_dict(cls, control: dict):
        control_type = control.get('type')
        control_mapping = {
            DataControlType.MASTER_FIELDS.value: MasterFieldsDataControl
        }
        subclass = control_mapping.get(control_type)
        if not subclass:
            raise DataControlNotImplementedError(
                control=control_type,
                available_types=list(control_mapping.keys())
            )
        return subclass

@dataclass
class MasterFieldsDataControl(DataControl):
    column: str

@dataclass
class Transformation:
    type: TransformationType

    @classmethod
    def get_subclass_from_dict(cls, transformation: dict):
        transformation_type = transformation.get('type')
        transformation_mapping = {
            TransformationType.PARSE_DATES.value: ParseDatesTransformation,
            TransformationType.FIX_CHARACTERS.value: FixCharactersTransformation,
            TransformationType.ADD_FILENAME_COLUMN.value: AddFilenameColumnTransformation,
        }
        subclass = transformation_mapping.get(transformation_type)
        if not subclass:
            raise TransformationNotImplementedError(
                transformation=transformation_type,
                available_types=list(transformation_mapping.keys())
            )
        return subclass


@dataclass
class ParseDatesTransformation(Transformation):
    columns: List[str]
    source_format: List[str]
    target_format: str = "yyyy-MM-dd"


@dataclass
class FixCharactersTransformation(Transformation):
    pass


@dataclass
class AddFilenameColumnTransformation(Transformation):
    target_column: str = "internal_file_name"


@dataclass
class Casting:
    strategy: CastingStrategy = CastingStrategy.ONE_BY_ONE
    add_missing_columns: bool = False
    master_table: Optional[str] = None
    transformations: List[Transformation] = field(default_factory=list)


@dataclass
class DatabaseTable:
    database: Database
    table: str
    primary_keys: Optional[list] = field(default_factory=list)
    casting: Casting = field(default_factory=Casting)
    data_controls: List[DataControl] = field(default_factory=list)
    partition_field: str = "data_date"

    @property
    def database_relation(self) -> str:
        from data_framework.modules.config.core import config
        if config().platform == Platform.INFINITY:
            return self.database.value
        else:
            return f'rl_{self.database.value}'

    @property
    def full_name(self) -> str:
        return f'{self.database_relation}.{self.table}'

    @property
    def sql_where(self) -> str:
        from data_framework.modules.config.core import config

        if config().parameters.execution_mode == ExecutionMode.DELTA and self.partition_field:
            return f"{self.partition_field} = '{config().parameters.file_date}'"
        return ""


@dataclass
class TableDict:
    tables: Tuple[str, DatabaseTable]

    def table(self, table_key: str) -> Union[DatabaseTable, None]:
        table_info = self.tables.get(table_key)
        if not table_info:
            raise TableKeyError(
                table_key=table_key,
                available_table_keys=list(self.tables.keys())
            )
        return table_info

    def table_key(self, database: str, table: str) -> Union[str, None]:
        for table_key, database_table in self.tables.items():
            if database_table.database.value == database and database_table.table == table:
                return table_key
        raise TableConfigNotFoundError(database=database, table=table)


@dataclass
class LandingToRaw:
    incoming_file: IncomingFileLandingToRaw
    output_file: DatabaseTable
    processing_specifications: ProcessingSpecifications = field(default_factory=ProcessingSpecifications)
    notifications: NotificationDict = field(default_factory=NotificationDict)
    cancel_next_stage: bool = False

    def search_table_config(self, database: str, table: str) -> Union[DatabaseTable, None]:
        if database == self.output_file.database.value and table == self.output_file.table:
            return self.output_file
        else:
            return None


@dataclass
class ProcessVars:
    _variables: dict = field(default_factory=dict)

    def get_variable(self, name: str):
        return self._variables.get(name)


@dataclass
class GenericProcess:
    source_tables: TableDict
    target_tables: TableDict
    processing_specifications: ProcessingSpecifications = field(default_factory=ProcessingSpecifications)
    notifications: NotificationDict = field(default_factory=NotificationDict)
    vars: Optional[ProcessVars] = field(default_factory=ProcessVars)

    def search_table_config(self, database: str, table: str) -> Union[DatabaseTable, None]:
        try:
            table_key = self.source_tables.table_key(database, table)
            table_config = self.source_tables.table(table_key)
            return table_config
        except Exception:
            try:
                table_key = self.target_tables.table_key(database, table)
                table_config = self.target_tables.table(table_key)
                return table_config
            except Exception:
                return None


@dataclass
class OutputReport:
    name: str
    source_table: DatabaseTable
    columns: List[str]
    file_format: OutputFileFormat
    filename_pattern: str
    csv_specs: Optional[CSVSpecsReport]
    json_specs: Optional[JSONSpecsReport]
    replaces: Optional[List[dict]]
    description: Optional[str]
    where: Optional[str]
    columns_alias: Optional[List[str]] = field(default_factory=list)
    filename_date_format: Optional[str] = '%Y-%m-%d'


@dataclass
class ToOutput:
    output_reports: List[OutputReport] = field(default_factory=list)
    processing_specifications: ProcessingSpecifications = field(default_factory=ProcessingSpecifications)
    notifications: NotificationDict = field(default_factory=NotificationDict)

    def search_table_config(self, database: str, table: str) -> None:
        return None


@dataclass
class Processes:
    landing_to_raw: Optional[LandingToRaw]
    raw_to_staging: Optional[GenericProcess] = None
    staging_to_common: Optional[GenericProcess] = None
    staging_to_business: Optional[GenericProcess] = None
    common_to_business: Optional[GenericProcess] = None
    common_to_output: Optional[ToOutput] = None
    business_to_output: Optional[ToOutput] = None


@dataclass
class Config:
    processes: Processes
    environment: Environment
    # TODO: remove when migrating Infinity to Data Platform
    platform: Platform
    parameters: Parameters
    project_id: str
    data_framework_notifications: DataFrameworkNotifications

    @property
    def has_next_process(self) -> bool:
        processes = [field.name for field in fields(Processes)]
        current_process_index = processes.index(self.parameters.process)
        posible_processes_begind_index = current_process_index + 1

        possible_processes = processes[posible_processes_begind_index:]
        next_processes = [process for process in possible_processes if getattr(self.processes, process) is not None]

        return not next_processes

    @property
    def is_first_process(self) -> bool:
        procesess = [field.name for field in fields(Processes)]
        next_processes = [process for process in procesess if getattr(self.processes, process) is not None]
        current_process_index = next_processes.index(self.parameters.process)

        return current_process_index == 0

    def current_process_config(self) -> Union[LandingToRaw, GenericProcess, ToOutput]:
        try:
            current_process = self.parameters.process
            current_process_config = getattr(self.processes, current_process)
            if current_process_config is None:
                raise EmptyProcessConfigError(process=current_process)
            return current_process_config
        except AttributeError:
            raise ProcessNotFoundError(
                process=current_process,
                available_processes=list(self.processes.__dict__.keys())
            )

    @property
    def source_layer(self) -> Layer:
        current_process = self.parameters.process
        layer_name = regex_utils.extract_substring(current_process, r'^[a-z]+')
        return Layer(layer_name)

    @property
    def target_layer(self) -> Layer:
        current_process = self.parameters.process
        layer_name = regex_utils.extract_substring(current_process, r'[a-z]+$')
        return Layer(layer_name)

```


---
## File: ./src/data_framework/modules/data_process/core_data_process.py
```python
from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.data_process.interface_data_process import (
    DataProcessInterface,
    ReadResponse,
    WriteResponse
)
from data_framework.modules.config.core import config
from data_framework.modules.config.model.flows import Technologies, DatabaseTable
from typing import List, Any


class CoreDataProcess(object):

    @LazyClassProperty
    def _data_process(cls) -> DataProcessInterface:
        technology = config().current_process_config().processing_specifications.technology
        if technology == Technologies.EMR:
            from data_framework.modules.data_process.integrations.spark.spark_data_process import SparkDataProcess

            return SparkDataProcess()
        elif technology == Technologies.LAMBDA:
            from data_framework.modules.data_process.integrations.pandas.pandas_data_process import PandasDataProcess

            return PandasDataProcess()

    @classmethod
    def merge(cls, dataframe: Any, table_config: DatabaseTable, custom_strategy: str = None) -> WriteResponse:
        return cls._data_process.merge(
            dataframe=dataframe,
            table_config=table_config,
            custom_strategy=custom_strategy
        )
    
    @classmethod
    def insert_overwrite(cls, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        return cls._data_process.insert_overwrite(
            dataframe=dataframe,
            table_config=table_config
        )

    @classmethod
    def datacast(
        cls,
        table_source: DatabaseTable,
        table_target: DatabaseTable
    ) -> ReadResponse:
        return cls._data_process.datacast(
            table_source=table_source,
            table_target=table_target
        )

    @classmethod
    def read_table(cls, database: str, table: str, filter: str = None, columns: List[str] = None) -> ReadResponse:
        return cls._data_process.read_table(database=database, table=table, filter=filter, columns=columns)

    @classmethod
    def delete_from_table(cls, table_config: DatabaseTable, _filter: str) -> WriteResponse:
        return cls._data_process.delete_from_table(table_config=table_config, _filter=_filter)

    @classmethod
    def insert_dataframe(cls, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        return cls._data_process.insert_dataframe(dataframe=dataframe, table_config=table_config)

    @classmethod
    def join(
        cls,
        df_1: Any,
        df_2: Any,
        how: str,
        left_on: List[str],
        right_on: List[str] = None,
        left_suffix: str = '_df_1',
        right_suffix: str = '_df_2'
    ) -> ReadResponse:
        return cls._data_process.join(
            df_1=df_1,
            df_2=df_2,
            how=how,
            left_on=left_on,
            right_on=right_on,
            left_suffix=left_suffix,
            right_suffix=right_suffix
        )

    @classmethod
    def create_dataframe(cls, data: Any, schema: str = None) -> ReadResponse:
        return cls._data_process.create_dataframe(data=data, schema=schema)

    @classmethod
    def query(cls, sql: str) -> ReadResponse:
        return cls._data_process.query(sql=sql)

    @classmethod
    def overwrite_columns(
        cls,
        dataframe: Any,
        columns: List[str],
        custom_column_suffix: str,
        default_column_suffix: str,
        drop_columns: bool = True
    ) -> ReadResponse:
        return cls._data_process.overwrite_columns(
            dataframe=dataframe,
            columns=columns,
            custom_column_suffix=custom_column_suffix,
            default_column_suffix=default_column_suffix,
            drop_columns=drop_columns
        )

    @classmethod
    def unfold_string_values(cls, dataframe: Any, column_name: str, separator: str) -> ReadResponse:
        return cls._data_process.unfold_string_values(
            dataframe=dataframe, column_name=column_name, separator=separator
        )

    @classmethod
    def add_dynamic_column(
        cls,
        dataframe: Any,
        new_column: str,
        reference_column: str,
        available_columns: List[str],
        default_value: Any = None
    ) -> ReadResponse:
        return cls._data_process.add_dynamic_column(
            dataframe=dataframe,
            new_column=new_column,
            reference_column=reference_column,
            available_columns=available_columns,
            default_value=default_value
        )

    @classmethod
    def stack_columns(
        cls,
        dataframe: Any,
        source_columns: List[str],
        target_columns: List[str]
    ) -> ReadResponse:
        return cls._data_process.stack_columns(
            dataframe=dataframe,
            source_columns=source_columns,
            target_columns=target_columns
        )

    @classmethod
    def is_empty(cls, dataframe: Any) -> bool:
        return cls._data_process.is_empty(dataframe=dataframe)

    @classmethod
    def count_rows(cls, dataframe: Any) -> int:
        return cls._data_process.count_rows(dataframe=dataframe)

    @classmethod
    def select_columns(cls, dataframe: Any, columns: List[str]) -> ReadResponse:
        return cls._data_process.select_columns(dataframe=dataframe, columns=columns)

    @classmethod
    def show_dataframe(cls, dataframe: Any) -> WriteResponse:
        return cls._data_process.show_dataframe(dataframe=dataframe)

```


---
## File: ./src/data_framework/modules/data_process/helpers/athena.py
```python
from data_framework.modules.config.core import config
from data_framework.modules.utils.logger import logger
from data_framework.modules.config.model.flows import Platform
from data_framework.modules.storage.interface_storage import Layer
from data_framework.modules.storage.core_storage import Storage
from data_framework.modules.exception.aws_exceptions import AthenaError
import boto3
from pandas import read_csv, DataFrame
from time import sleep
from io import BytesIO
from typing import Union


class AthenaClient:

    def __init__(self):
        self.logger = logger
        self.athena_client = boto3.client('athena', region_name=config().parameters.region)
        self.storage = Storage()
        # TODO: remove when migrating Infinity to Data Platform
        self.layer = (
            Layer.TEMP if config().platform == Platform.INFINITY
            else Layer.ATHENA
        )
        self.output_path = f's3://{config().parameters.bucket_prefix}-{self.layer.value}/{config().project_id}'

    def execute_query(self, query: str, read_output: bool = True) -> Union[DataFrame, None]:
        try:
            response = self.athena_client.start_query_execution(
                QueryString=query,
                ResultConfiguration={'OutputLocation': self.output_path}
            )
            query_execution_id = response['QueryExecutionId']
            output_location = self.wait_for_query_to_complete(query_execution_id)
            if read_output:
                df = self.get_query_results(output_location)
                return df
        except Exception:
            raise AthenaError(error_message=f'Error executing the following query: {query}')

    def wait_for_query_to_complete(self, query_execution_id: str) -> str:
        while True:
            response = self.athena_client.get_query_execution(QueryExecutionId=query_execution_id)
            status = response['QueryExecution']['Status']['State']
            if status == 'SUCCEEDED':
                output_location = response['QueryExecution']['ResultConfiguration']['OutputLocation']
                return output_location
            elif status == 'FAILED':
                error = response['QueryExecution']['Status']['AthenaError']['ErrorMessage']
                raise AthenaError(error_message=f'Query execution has failed. Error: {error}')
            elif status == 'CANCELLED':
                raise AthenaError(error_message='Query execution has been cancelled')
            else:
                sleep(2)

    def get_query_results(self, output_location: str) -> DataFrame:
        result_path = output_location.replace('s3://', '').split('/', 1)[1]
        response = self.storage.read(self.layer, result_path)
        return read_csv(BytesIO(response.data))

```


---
## File: ./src/data_framework/modules/data_process/helpers/cast.py
```python
"""
"""

from data_framework.modules.utils.logger import logger
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from data_framework.modules.config.model.flows import DatabaseTable
from data_framework.modules.exception.data_process_exceptions import CastQueryError
from typing import Any, Dict, List
import pandas as pd
from pandas import DataFrame, Series


class Cast:

    def __init__(self):
        self.logger = logger
        self.catalogue = CoreCatalogue()

    def cast_columns(self, column_name: str, column_type: str) -> str:
        if column_type in ('int', 'bigint', 'double', 'float', 'date', 'timestamp') or column_type.startswith('decimal'):
            query = f'TRY_CAST({column_name} AS {column_type.upper()}) AS {column_name}'
        elif 'struct<' in column_type or 'array<' in column_type:
            query = f"FROM_JSON({column_name}, '{column_type}') AS {column_name}"
        elif column_type == 'boolean':
            query = f"""
                (
                    CASE WHEN LOWER({column_name}) in ('true', 't', 'yes', 'y', 'si', 's', '1') THEN true
                    WHEN LOWER({column_name}) IN ('false', 'f', 'no', 'n', '0') THEN false
                    ELSE null END
                ) AS {column_name}
            """
        else:
            query = f'{column_name}'
        return query

    def build_datacast_query(
        self,
        source_columns: List[str],
        table_target: DatabaseTable,
        add_missing_columns: bool,
        view_name: str = 'data_to_cast'
    ) -> str:
        try:
            source_columns = [column.lower() for column in source_columns]
            # Obtain the target schema with the type of each column
            schema_target = self.catalogue.get_schema(table_target.database_relation, table_target.table)
            target_types = schema_target.schema.get_column_type_mapping(partitioned=True)
            # Cast each column to its target type
            if add_missing_columns:
                [
                    target_types.update({column: 'string'})
                    for column in source_columns
                    if column not in target_types
                ]
                casted_columns = [
                    self.cast_columns(
                        column_name=f"`{column}`",
                        column_type=target_type
                    ) if column in source_columns
                    else f"NULL AS `{column}`"
                    for column, target_type in target_types.items()
                ]
            else:
                casted_columns = [
                    self.cast_columns(
                        column_name=f"`{column}`",
                        column_type=target_type
                    )
                    for column, target_type in target_types.items()
                    if column in source_columns
                ]
            # Build SQL query
            query = f"SELECT {', '.join(casted_columns)} FROM {view_name}"
            return query
        except Exception:
            raise CastQueryError()

    def get_query_to_insert_dataframe(self, dataframe: DataFrame, table_config: DatabaseTable) -> str:
        try:
            # Obtain data types of the target table
            response = self.catalogue.get_schema(table_config.database_relation, table_config.table)
            target_schema = response.schema
            target_columns = target_schema.get_column_names(partitioned=True)
            target_types = target_schema.get_type_columns(partitioned=True)
            column_types = {column: _type for column, _type in zip(target_columns, target_types)}
            # Select dataframe needed columns
            dataframe = dataframe[target_columns]
            # Cast dataframe values
            rows_list = dataframe.apply(self.cast_df_row, axis=1, args=(column_types,))
            rows = ', '.join(rows_list)
            # Build INSERT query
            columns = ', '.join(dataframe.columns)
            query = f"""
                INSERT INTO {table_config.database_relation}.{table_config.table}
                ({columns}) VALUES {rows};
            """
            return query
        except Exception:
            raise CastQueryError()

    def cast_df_row(self, row: Series, column_types: Dict[str, str]) -> str:
        values = ', '.join(
            self.cast_df_value(value, column_types[column])
            for column, value in row.items()
        )
        return f'({values})'

    def cast_df_value(self, value: Any, _type: str) -> str:
        if pd.isna(value):
            return 'NULL'
        elif _type == 'string':
            return f"'{value}'"
        elif _type == 'timestamp':
            return f"timestamp '{value.strftime('%Y-%m-%d %H:%M:%S.%f')}'"
        elif _type == 'date':
            return f"date('{value.strftime('%Y-%m-%d')}')"
        else:
            return str(value)

```


---
## File: ./src/data_framework/modules/data_process/helpers/json.py
```python
from data_framework.modules.config.core import config
from data_framework.modules.exception.data_process_exceptions import JsonParseError
from typing import List, Dict, Any
from io import BytesIO
import json


class JsonParser:

    def __init__(self):
        self.json_specs = config().processes.landing_to_raw.incoming_file.json_specs
        self.formatted_keys_cache = []

    def parse_json_files(self, files: List[BytesIO]) -> List[Dict[Any, Any]]:
        try:
            data = []
            for file in files:
                for line in file.readlines():
                    json_data = json.loads(line)
                    data += self._flatten_json(json_data)
            return data
        except Exception:
            raise JsonParseError()

    def _flatten_json(self, json_data: Dict[Any, Any]) -> List[Dict[Any, Any]]:
        try:
            for level in self.json_specs.levels:
                if level == '*':
                    json_data = self._flatten_all_items(json_data, level)
                else:
                    json_data = self._flatten_specific_item(json_data, level)
            return json_data
        except KeyError:
            raise KeyError(f'Path {self.json_specs.source_level} not found in JSON file')

    def _flatten_all_items(self, json_data: Dict[Any, Any], level: str) -> Dict[Any, Any]:
        if isinstance(json_data, dict):
            json_data = self._get_nested_items(json_data, level)
        elif isinstance(json_data, list):
            json_data = [
                nested_item
                for item in json_data
                for nested_item in self._get_nested_items(item, level)
            ]
        return json_data

    def _flatten_specific_item(self, json_data: Dict[Any, Any], level: str) -> Dict[Any, Any]:
        if isinstance(json_data, dict):
            remaining_items = self._get_remaining_items(json_data, level)
            json_data = json_data[level]
            if isinstance(json_data, dict):
                json_data.update(remaining_items)
            elif isinstance(json_data, list):
                [item.update(remaining_items) for item in json_data if isinstance(item, dict)]
        elif isinstance(json_data, list):
            new_json_data = []
            for item in json_data:
                remaining_items = self._get_remaining_items(item, level)
                item = item[level].copy()
                if isinstance(item, dict):
                    item.update(remaining_items)
                    new_json_data.append(item)
                elif isinstance(item, list):
                    [element.update(remaining_items) for element in item if isinstance(element, dict)]
                    new_json_data += item
            json_data = new_json_data
        return json_data

    def _get_nested_items(self, dictionary: Dict[Any, Any], current_level: str) -> List[Dict[Any, Any]]:
        nested_items = []
        single_items = {}
        for key, value in dictionary.items():
            if isinstance(value, dict):
                nested_items.append(value)
            else:
                single_items.update({self._format_key_path(key, current_level): str(value)})
        [nested_item.update(single_items) for nested_item in nested_items]
        return nested_items

    def _get_remaining_items(self, dictionary: Dict[Any, Any], current_level: str) -> Dict[str, str]:
        remaining_items = {
            self._format_key_path(key, current_level): str(value)
            for key, value in dictionary.items()
            if key != current_level
        }
        return remaining_items

    def _format_key_path(self, key: str, current_level: str) -> str:
        if key in self.formatted_keys_cache:
            return key
        previous_levels = self.json_specs.levels[:self.json_specs.levels.index(current_level)]
        if previous_levels:
            previous_levels = [level for level in previous_levels if level != '*']
            key_path = '_'.join(previous_levels)
            formatted_key = f'{key_path}_{key}'
        else:
            formatted_key = key
        self.formatted_keys_cache.append(formatted_key)
        return formatted_key

```


---
## File: ./src/data_framework/modules/data_process/integrations/pandas/pandas_data_process.py
```python
from data_framework.modules.data_process.interface_data_process import (
    DataProcessInterface,
    ReadResponse,
    WriteResponse,
)
from data_framework.modules.data_process.helpers.athena import AthenaClient
from data_framework.modules.data_process.helpers.cast import Cast
from data_framework.modules.utils.logger import logger
from data_framework.modules.config.model.flows import DatabaseTable
from data_framework.modules.exception.data_process_exceptions import (
    ReadDataError,
    WriteDataError,
    DataProcessError
)
from typing import List, Any
import pandas as pd
from pandas import DataFrame


class PandasDataProcess(DataProcessInterface):

    def __init__(self):
        self.athena = AthenaClient()

    def merge(self, dataframe: DataFrame, table_config: DatabaseTable, custom_strategy: str = None) -> WriteResponse:
        raise NotImplementedError('Function merge not available in Pandas data process')

    def datacast(
        self,
        table_source: DatabaseTable,
        table_target: DatabaseTable
    ) -> ReadResponse:
        raise NotImplementedError('Function datacast not available in Pandas data process')

    def read_table(self, database: str, table: str, filter: str = None, columns: List[str] = None) -> ReadResponse:
        # TODO: use DatabaseTable instead of database and table strings
        try:
            if columns:
                columns_str = ', '.join(columns)
                query = f"SELECT {columns_str} FROM {database}.{table}"
            else:
                query = f"SELECT * FROM {database}.{table}"
            if filter:
                query += f" WHERE {filter}"
            df = self.athena.execute_query(query)
            return ReadResponse(success=True, error=None, data=df)
        except Exception:
            raise ReadDataError(query=query)

    def delete_from_table(self, table_config: DatabaseTable, _filter: str) -> WriteResponse:
        raise NotImplementedError('Function delete_from_table not available in Pandas data process')

    def insert_dataframe(self, dataframe: DataFrame, table_config: DatabaseTable) -> WriteResponse:
        try:
            query = Cast().get_query_to_insert_dataframe(dataframe, table_config)
            self.athena.execute_query(query, read_output=False)
            return WriteResponse(success=True, error=None)
        except Exception:
            raise WriteDataError(database=table_config.database_relation, table=table_config.table)

    def join(
        self,
        df_1: DataFrame,
        df_2: DataFrame,
        how: str,
        left_on: List[str],
        right_on: List[str] = None,
        left_suffix: str = '_df_1',
        right_suffix: str = '_df_2'
    ) -> ReadResponse:
        try:
            if how not in ['inner', 'left', 'right', 'outer']:
                raise ValueError(
                    f'Invalid parameter value: how={how}. Allowed values: inner, left, right, outer'
                )
            if not right_on:
                right_on = left_on
            if len(left_on) != len(right_on):
                raise ValueError(
                    'Number of columns in left_on and right_on parameters must be the same. ' +
                    f'left_on: {len(left_on)} columns. right_on: {len(right_on)} columns'
                )
            else:
                # Perform join
                df_result = df_1.merge(
                    df_2,
                    left_on=left_on,
                    right_on=right_on,
                    how=how,
                    suffixes=(left_suffix, right_suffix)
                )
            return ReadResponse(success=True, error=None, data=df_result)
        except Exception:
            raise DataProcessError(error_message='Error performing join of two dataframes')

    def create_dataframe(self, data: Any, schema: str = None) -> ReadResponse:
        try:
            if schema:
                # TODO: implementar
                raise NotImplementedError(
                    'Function create_dataframe with custom schema feature not available in Pandas data process'
                )
            else:
                df = pd.DataFrame(data)
            return ReadResponse(success=True, error=None, data=df)
        except Exception:
            raise DataProcessError(error_message='Error creating dataframe')

    def query(self, sql: str) -> ReadResponse:
        try:
            df = self.athena.execute_query(sql)
            return ReadResponse(success=True, error=None, data=df)
        except Exception:
            raise ReadDataError(query=sql)

    def overwrite_columns(
        self,
        dataframe: DataFrame,
        columns: List[str],
        custom_column_suffix: str,
        default_column_suffix: str,
        drop_columns: bool = True
    ) -> ReadResponse:
        try:
            for column in columns:
                custom_column = column + custom_column_suffix
                default_column = column + default_column_suffix
                dataframe[column] = dataframe[custom_column].fillna(dataframe[default_column])
                if drop_columns:
                    dataframe = dataframe.drop([custom_column, default_column], axis=1)
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError()

    def unfold_string_values(self, dataframe: DataFrame, column_name: str, separator: str) -> ReadResponse:
        raise NotImplementedError('Function unfold_string_values not available in Pandas data process')

    def add_dynamic_column(
        self,
        dataframe: DataFrame,
        new_column: str,
        reference_column: str,
        available_columns: List[str],
        default_value: Any = None
    ) -> ReadResponse:
        raise NotImplementedError('Function add_dynamic_column not available in Pandas data process')

    def stack_columns(
        self,
        dataframe: DataFrame,
        source_columns: List[str],
        target_columns: List[str]
    ) -> ReadResponse:
        try:
            if len(target_columns) != 2:
                raise ValueError(f'Must specify two columns as target_columns. Found {target_columns}')
            dataframe = dataframe.reset_index(names='index')
            dataframe = pd.melt(
                dataframe,
                id_vars=['index'],
                value_vars=source_columns,
                var_name=target_columns[0],
                value_name=target_columns[1]
            )
            dataframe = dataframe[target_columns]
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError()

    def is_empty(self, dataframe: DataFrame) -> bool:
        if dataframe is not None:
            return dataframe.empty
        else:
            return True

    def count_rows(self, dataframe: DataFrame) -> int:
        return len(dataframe)

    def select_columns(self, dataframe: DataFrame, columns: List[str]) -> ReadResponse:
        try:
            dataframe = dataframe[columns]
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError('Error selecting columns of a dataframe')

    def show_dataframe(self, dataframe: DataFrame) -> WriteResponse:
        pd.set_option('display.max_rows', None)
        pd.set_option('display.max_columns', None)
        logger.info(dataframe)
        pd.reset_option('display.max_rows')
        pd.reset_option('display.max_columns')

```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/data_controls/master_fields.py
```python
from data_framework.modules.config.core import config
from pyspark.sql import DataFrame
import pyspark.sql.functions as f
from data_framework.modules.data_process.core_data_process import CoreDataProcess
from data_framework.modules.config.model.flows import (
    MasterFieldsDataControl,
    DatabaseTable
)
from data_framework.modules.notification.core_notifications import CoreNotifications

def master_fields(df_source: DataFrame, data_control: MasterFieldsDataControl, target_table: DatabaseTable):
    data_process = CoreDataProcess()

    df_distinct = df_source.select(data_control.column).distinct()
    rows = df_distinct.collect()

    source_fields = [row.statistic for row in rows]

    df_master_fields = data_process.query(
        sql=f"""
            SELECT
                field AS field
            FROM
                iceberg_catalog.rl_funds_common.master_fields
            WHERE
                target = '{target_table.table}'
        """
    ).data

    rows = df_master_fields.collect()
    master_fields = [row.field for row in rows]

    different_fields = list(set(source_fields) - set(master_fields))

    if different_fields:
        sql_fields = []
        for different_field in different_fields:
            sql_fields.append(f"SELECT '{different_field}' AS field")
        
        sql_final = f"""
            WITH base AS (
                SELECT
                    '{target_table.table}' AS target,
                    'common' AS layer,
                    field,
                    'string' AS type,
                    'pending' AS status,
                    CURRENT_DATE() AS inserted_at,
                    null AS updated_at
                FROM (
                    {' UNION '.join(sql_fields)}
                )
            )
            MERGE INTO iceberg_catalog.rl_funds_common.master_fields AS master_fields
            USING base ON
                base.target = master_fields.target
                AND base.layer = master_fields.layer
                AND base.field = master_fields.field
            WHEN NOT MATCHED THEN
                INSERT *
        """

        data_process.query(sql=sql_final)

        notifications = CoreNotifications()

        notifications.send_notification(
            notification_name='master_fields',
            arguments={
                'dataflow': config().parameters.dataflow,
                'process': config().parameters.process,
                'table_name': target_table.table,
                'fields': ', '.join(different_fields)[:350]
            }
        )
```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/dynamic_config.py
```python
import json

class DynamicConfig:
    
# def apply_iceberg_recommendations(config: Dict[str, str], iceberg_meta: Dict[str, Any], dataset_size_gb: float) -> Dict[str, str]:
#     if iceberg_meta["snapshot_count"] > 40:
#         config.update({
#             "spark.sql.iceberg.expire.snapshots.enabled": "true",
#             "spark.sql.iceberg.expire.snapshots.retention-interval": "7d"
#         })
#     else:
#         config.update({
#             "spark.sql.iceberg.expire.snapshots.enabled": "true",
#             "spark.sql.iceberg.expire.snapshots.retention-interval": "30d"
#         })
        
#     if iceberg_meta["table_size_gb"] > 300:
#         config.update({
#             "spark.sql.iceberg.compaction.enabled": "true",
#             "spark.sql.iceberg.compaction.target-file-size-bytes": str(512 * 1024 * 1024),
#             "spark.sql.iceberg.compaction.parallelism": "8",
#             "spark.sql.iceberg.merge-snapshot.parallelism": "8"
#         })
#     else:
#         config.update({
#             "spark.sql.iceberg.compaction.enabled": "true",
#             "spark.sql.iceberg.compaction.target-file-size-bytes": str(256 * 1024 * 1024),
#             "spark.sql.iceberg.compaction.parallelism": "4",
#             "spark.sql.iceberg.merge-snapshot.parallelism": "4"
#         })
        
#     return config

# def get_emr_serverless_app_info(application_id: str, region_name: str = "us-east-1") -> Dict[str, Any]:
#     client = boto3.client("emr-serverless", region_name=region_name)
#     response = client.get_application(applicationId=application_id)
#     app_info = response.get("application", {})
#     max_cap = app_info.get("maximumCapacity", {})
#     max_cpu_str = max_cap.get("cpu", "100")
#     max_memory_str = max_cap.get("memory", "512")
#     max_cpu = int(max_cpu_str.split(":")[1])
#     max_memory_gb = int(max_memory_str.split(":")[1])
#     return {
#         "max_cpu": max_cpu,
#         "max_memory_gb": max_memory_gb
#     }

    @classmethod
    def determine_cores(cls, dataset_size_gb: float) -> int:
        if dataset_size_gb < 1:
            return 2
        elif dataset_size_gb < 10:
            return 2
        elif dataset_size_gb < 100:
            return 4
        elif dataset_size_gb < 500:
            return 8
        elif dataset_size_gb < 1000:
            return 12
        else:
            return 16

    @classmethod   
    def determine_driver_settings(cls, dataset_size_gb: float, max_memory_per_executor_gb: int) -> (int, int):
        if dataset_size_gb < 1:
            driver_memory_gb = 2
            driver_cores = 1
        elif dataset_size_gb < 10:
            driver_memory_gb = min(4, max_memory_per_executor_gb)
            driver_cores = 2
        elif dataset_size_gb < 500:
            driver_memory_gb = min(8, max_memory_per_executor_gb)
            driver_cores = 2
        else:
            driver_memory_gb = min(16, max_memory_per_executor_gb)
            driver_cores = 4
        return driver_memory_gb, driver_cores

    @classmethod
    def determine_disk_size(cls, dataset_size_gb: float) -> int:
        # Clamps between 20 and 200
        if dataset_size_gb < 1:
            disk = 20
        elif dataset_size_gb < 10:
            disk = 50
        elif dataset_size_gb < 200:
            disk = 100
        elif dataset_size_gb < 500:
            disk = 200
        else:
            disk = 200
        
        return max(20, min(disk, 200))
        
    @classmethod
    def determine_memory_overhead_factor(cls, dataset_size_gb: float) -> float:
        if dataset_size_gb < 1:
            return 0.1
        elif dataset_size_gb < 10:
            return 0.15
        elif dataset_size_gb < 100:
            return 0.2
        elif dataset_size_gb < 500:
            return 0.25
        else:
            return 0.3
    
    @classmethod
    def adjust_resources_to_emr_serverless_constraints(cls, cpu: int, memory_gb: int) -> (int, int):
        # Adjust CPU and memory to EMR Serverless resource constraints
        # 1 vCPU -> 2-8GB (1GB increments)
        # 2 vCPU -> 4-16GB (1GB increments)
        # 4 vCPU -> 8-30GB (1GB increments)
        # 8 vCPU ->16-60GB (4GB increments)
        # 16 vCPU ->32-120GB (8GB increments)

        if cpu <= 1:
            cpu = 1
            min_mem, max_mem, increment = 2, 8, 1
        elif cpu <= 2:
            cpu = 2
            min_mem, max_mem, increment = 4, 16, 1
        elif cpu <= 4:
            cpu = 4
            min_mem, max_mem, increment = 8, 30, 1
        elif cpu <= 8:
            cpu = 8
            min_mem, max_mem, increment = 16, 60, 4
        else:
            cpu = 16
            min_mem, max_mem, increment = 32, 120, 8

        if memory_gb < min_mem:
            memory_gb = min_mem
        elif memory_gb > max_mem:
            memory_gb = max_mem

        # Align memory to the allowed increment
        if increment > 1:
            remainder = memory_gb % increment
            if remainder != 0:
                # Round down to the nearest allowed increment
                memory_gb = memory_gb - remainder
                if memory_gb < min_mem:
                    memory_gb = min_mem

        return cpu, memory_gb
        
    @classmethod
    def recommend_spark_config(
        cls,
        dataset_size_gb: float = 2,
        job_type: str = "batch",
        optimization_goal: str = "cost",
        avg_file_size_mb: int = 500,
        max_executors: int = 20,
        max_memory_per_executor_gb: int = 120,
        emr_application_id: str = None,
        num_instances: int = 20
    ):
        
        # Memory for executors
        additional_memory_gb = int(dataset_size_gb // 10)
        executor_memory_gb = min(2 + additional_memory_gb, max_memory_per_executor_gb)
        
        # Cores executors
        base_cores = cls.determine_cores(dataset_size_gb)
        # Driver
        driver_memory_gb, driver_cores = cls.determine_driver_settings(dataset_size_gb, max_memory_per_executor_gb)
        
        # Partitions
        base_partitions = max(20, int(dataset_size_gb * 20))
        desired_partitions_by_file_size = max(50, int((avg_file_size_mb / 256) * base_partitions))
        shuffle_partitions = max(base_partitions, desired_partitions_by_file_size)
        
        if dataset_size_gb > 500:
            shuffle_partitions = max(shuffle_partitions, 2000)
            
        # Disco
        disk_gb = cls.determine_disk_size(dataset_size_gb)
        memory_overhead_factor = cls.determine_memory_overhead_factor(dataset_size_gb)

        # Adjust executor resources to EMR Serverless constraints
        adj_executor_cpu, adj_executor_mem = cls.adjust_resources_to_emr_serverless_constraints(base_cores, executor_memory_gb)
        # Adjust driver resources as well
        adj_driver_cpu, adj_driver_mem = cls.adjust_resources_to_emr_serverless_constraints(driver_cores, driver_memory_gb)
        
        config = {
            "spark.dynamicAllocation.enabled": "true",
            "spark.dynamicAllocation.initialExecutors": "3",
            "spark.dynamicAllocation.minExecutors": "2",
            "spark.dynamicAllocation.maxExecutors": str(max_executors)
            #"spark.executor.extraJavaOptions": "XX:+UseG1GC -XX:InitiatingHeapOccupancyPercent=20 -XX:+UnlockDiagnosticVMOptions"
        }
        
        if job_type == "batch":
            config["spark.sql.autoBroadcastJoinThreshold"] = "-1"
        elif job_type == "interactive":
            shuffle_partitions = min(shuffle_partitions, 200)
            
        if optimization_goal == "throughput":
            config["spark.sql.parquet.compression.codec"] = "snappy"
            if num_instances < 10:
                config["spark.dynamicAllocation.maxExecutors"] = str(min(max_executors, 20))
        elif optimization_goal == "cost":
            if num_instances > 10:
                config["spark.dynamicAllocation.maxExecutors"] = str(min(max_executors, 30))
            else:
                config["spark.dynamicAllocation.maxExecutors"] = str(min(max_executors, 15))
                
        config.update({
            "spark.executor.memory": f"{adj_executor_mem}g",
            "spark.executor.cores": str(adj_executor_cpu),
            "spark.driver.memory": f"{adj_driver_mem}g",
            "spark.driver.cores": str(adj_driver_cpu),
            #"spark.shuffle.partitions": str(shuffle_partitions),
            # "spark.executor.memoryOverhead": "3g",
            # "spark.memory.offHeap.enabled": "true",
            # "spark.memory.offHeap.size": "2g",
            # "spark.shuffle.file.buffer": "1m",
            # "spark.reducer.maxSizeInFlight": "96m",
            # "spark.sql.iceberg.handle-timestamp-without-timezone": "true",
            # "spark.sql.iceberg.merge-snapshot.enabled": "true",
            
            # EMR Serverless:
            "spark.emr-serverless.executor.disk": f"{disk_gb}G",
            "spark.emr-serverless.driver.disk": f"{disk_gb}G",
            "spark.emr-serverless.memoryOverheadFactor": str(memory_overhead_factor)
        })
        
        # if dataset_size_gb > 100 and job_type == "batch":
        #     config.update({
        #         "spark.sql.adaptive.enabled": "true",
        #         "spark.sql.adaptive.shuffle.targetPostShuffleInputSize": "256m",
        #         "spark.sql.parquet.enableVectorizedReader": "true"
        #     })
        # elif dataset_size_gb < 1:
        #     config["spark.sql.adaptive.enabled"] = "false"
            
        # InIntegration with EMR Serverless API
        # if emr_application_id is not None:
        #     emr_info = get_emr_serverless_app_info(emr_application_id, region_name)
        #     max_cpu = emr_info["max_cpu"]
        #     max_mem = emr_info["max_memory_gb"]
        #     possible_max_executors_by_memory = max_mem // executor_memory_gb
        #     new_max_executors = min(int(config["spark.dynamicAllocation.maxExecutors"]), possible_max_executors_by_memory)
        #     possible_max_executors_by_cpu = max_cpu // base_cores
        #     new_max_executors = min(new_max_executors, possible_max_executors_by_cpu)
        #     config["spark.dynamicAllocation.maxExecutors"] = str(new_max_executors)
            
        
        return config

```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/spark_data_process.py
```python
from data_framework.modules.data_process.interface_data_process import (
    DataProcessInterface,
    ReadResponse,
    WriteResponse,
)
from data_framework.modules.data_process.integrations.spark import utils as utils
from data_framework.modules.storage.core_storage import Storage
from data_framework.modules.storage.interface_storage import Layer
from data_framework.modules.config.core import config
from data_framework.modules.utils.logger import logger
from data_framework.modules.data_process.helpers.cast import Cast
from data_framework.modules.data_process.helpers.json import JsonParser
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from data_framework.modules.monitoring.core_monitoring import (
    CoreMonitoring,
    MetricNames
)
from data_framework.modules.config.model.flows import (
    DatabaseTable,
    ExecutionMode,
    CastingStrategy,
    LandingFileFormat
)
from data_framework.modules.utils.debug import debug_code
from data_framework.modules.data_process.integrations.spark.dynamic_config import DynamicConfig
from data_framework.modules.exception.data_process_exceptions import (
    ReadDataError,
    WriteDataError,
    DataProcessError,
    CastDataError,
    DeleteDataError,
    SparkConfigurationError
)
from typing import List, Any, Union
from io import BytesIO
from pyspark import SparkConf
from pyspark.sql import SparkSession, DataFrame
from pyspark.sql.functions import when
from pyspark.sql.types import StructType
import pyspark.sql.functions as f
import time
import random
import inspect
import secrets

iceberg_exceptions = ['ConcurrentModificationExceptio', 'CommitFailedException', 'ValidationException']


class SparkDataProcess(DataProcessInterface):

    __iceberg_snapshot_metrics_map = {
        'added-records': MetricNames.TABLE_WRITE_ADDED_RECORDS,
        'added-files-size': MetricNames.TABLE_WRITE_ADDED_SIZE,
        'deleted-records': MetricNames.TABLE_WRITE_DELETED_RECORDS,
        'removed-files-size': MetricNames.TABLE_WRITE_DELETED_SIZE,
        'total-records': MetricNames.TABLE_WRITE_TOTAL_RECORDS,
        'total-files-size': MetricNames.TABLE_WRITE_TOTAL_SIZE
    }

    def __init__(self):
        try:
            # Obtain Spark configuration for the current process
            json_config = config().current_process_config().processing_specifications

            spark_config = SparkConf() \
                .setAppName(f"[{config().parameters.dataflow}] {config().parameters.process}")

            spark_config.setAll([
                # S3
                ("spark.sql.catalog.iceberg_catalog.http-client.apache.max-connections", "2000"),
                ("fs.s3.maxConnections", "100"),
                # Memory
                # ("spark.serializer", "org.apache.spark.serializer.KryoSerializer"),
                # Iceberg
                ("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions"),
                ("spark.sql.catalog.iceberg_catalog.io-impl", "org.apache.iceberg.aws.s3.S3FileIO"),
                ("spark.sql.catalog.iceberg_catalog", "org.apache.iceberg.spark.SparkCatalog"),
                ("spark.jars", "/usr/share/aws/iceberg/lib/iceberg-spark3-runtime.jar"),
                ("spark.sql.catalog.iceberg_catalog.catalog-impl", "org.apache.iceberg.aws.glue.GlueCatalog"),
                ("spark.sql.catalog.iceberg_catalog.warehouse", "default_warehouse/"),
                # Hive
                ("spark.hadoop.hive.exec.dynamic.partition", "true"),
                ("spark.hadoop.hive.exec.dynamic.partition.mode", "nonstrict"),
                (
                    "spark.hadoop.hive.metastore.client.factory.class",
                    "com.amazonaws.glue.catalog.metastore.AWSGlueDataCatalogHiveClientFactory"
                ),
                # AWS Glue
                ("spark.sql.catalog.glue_catalog", "org.apache.iceberg.spark.SparkCatalog"),
                ("spark.sql.catalog.glue_catalog.catalog-impl", "org.apache.iceberg.aws.glue.GlueCatalog"),
                ("spark.sql.catalog.glue_catalog.io-impl", "org.apache.iceberg.aws.s3.S3FileIO"),
                ("spark.sql.catalogImplementation", "hive"),


                ("spark.sql.sources.partitionOverwriteMode", 'DYNAMIC')
            ])

            extra_config = json_config.spark_configuration.config

            spark_config.setAll(pairs=extra_config.items())

            logger.info(spark_config.getAll())

            # Create Spark session
            self.spark = SparkSession.builder \
                .config(conf=spark_config) \
                .enableHiveSupport() \
                .getOrCreate()

            self.secure_random = secrets.SystemRandom()
        except Exception:
            raise SparkConfigurationError()
        # Others
        self.catalogue = CoreCatalogue()
        self.storage = Storage()
        self.__monitoring = CoreMonitoring()

    def _build_complete_table_name(self, database: str, table: str) -> str:
        return f'iceberg_catalog.{database}.{table}'

    def _track_table_metric(self, table_config: DatabaseTable, data_frame: DataFrame = None):

        if data_frame:
            self.__monitoring.track_table_metric(
                name=MetricNames.TABLE_READ_RECORDS,
                database=table_config.database.value,
                table=table_config.table,
                value=float(data_frame.count())
            )
        else:
            table_name = self._build_complete_table_name(
                database=table_config.database_relation,
                table=table_config.table
            )

            iceberg_table = self.spark._jvm.org.apache.iceberg.spark.Spark3Util.loadIcebergTable(
                self.spark._jsparkSession, table_name
            )

            snapshot = iceberg_table.currentSnapshot()

            if snapshot is not None:
                java_summary = snapshot.summary()

                iterator = java_summary.entrySet().iterator()
                while iterator.hasNext():
                    entry = iterator.next()
                    if entry.getKey() in self.__iceberg_snapshot_metrics_map.keys():
                        self.__monitoring.track_table_metric(
                            name=self.__iceberg_snapshot_metrics_map.get(entry.getKey()),
                            database=table_config.database.value,
                            table=table_config.table,
                            value=float(entry.getValue())
                        )

    def merge(self, dataframe: DataFrame, table_config: DatabaseTable, custom_strategy: str = None) -> WriteResponse:
        try:
            ""
            table_name = self._build_complete_table_name(table_config.database_relation, table_config.table)

            source_method = inspect.stack()[2].function

            self.spark.sparkContext.setJobGroup(f"[MERGE] {source_method}", table_name, interruptOnCancel=True)

            view_name = 'data_to_merge'
            # Select only necessary columns of the dataframe
            dataframe = self._select_table_columns(dataframe, table_config)
            # Perform merge
            dataframe.createOrReplaceTempView(view_name)
            sql_update_with_pks = '\n AND '.join([
                f' {view_name}.{field} = {table_name}.{field}' for field in table_config.primary_keys
            ])

            stratgy = """
                WHEN MATCHED THEN
                    UPDATE SET *
                WHEN NOT MATCHED THEN
                    INSERT *
            """

            if custom_strategy:
                stratgy = custom_strategy

            merge_query = f"""
                MERGE INTO {table_name}
                USING {view_name} ON
                    {sql_update_with_pks}
                {stratgy}
            """
            logger.info(f'merge sql \n{merge_query}')
            self._execute_query(merge_query)

            self.spark.sparkContext.setJobGroup("", "", False)

            response = WriteResponse(success=True, error=None)

            utils.apply_data_controls(
                data_frame=dataframe,
                target_table=table_config
            )
            #self._track_table_metric(table_config=table_config)
            return response
        except Exception:
            raise WriteDataError(database=table_config.database_relation, table=table_config.table)

    def insert_overwrite(self, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        try:
            ""
            table_name = self._build_complete_table_name(table_config.database_relation, table_config.table)

            source_method = inspect.stack()[2].function

            self.spark.sparkContext.setJobGroup(f"[INSERT OVERWRITE] {source_method}", table_name, interruptOnCancel=True)

            # Select only necessary columns of the dataframe
            dataframe = self._select_table_columns(dataframe, table_config)

            dataframe.write.format("iceberg").mode('overwrite').save(table_name)

            self.spark.sparkContext.setJobGroup("", "", False)

            response = WriteResponse(success=True, error=None)
            #self._track_table_metric(table_config=table_config)

            utils.apply_data_controls(
                data_frame=dataframe,
                target_table=table_config
            )

            return response
        except Exception:
            raise WriteDataError(database=table_config.database_relation, table=table_config.table)

    def datacast(
        self,
        table_source: DatabaseTable,
        table_target: DatabaseTable
    ) -> ReadResponse:
        try:
            read_path = self.storage.raw_layer_path(
                database=table_source.database,
                table_name=table_source.table
            )

            logger.info(f"Casting strategy > {table_target.casting.strategy}")
            logger.info(f"Read path > {read_path.path}")

            if config().processes.landing_to_raw.incoming_file.file_format == LandingFileFormat.JSON:
                df_raw = self._read_raw_json_file(
                    table_path=read_path.relative_base_path,
                    file_path=read_path.relative_path,
                    casting_strategy=table_target.casting.strategy
                )
            elif table_target.casting.strategy == CastingStrategy.ONE_BY_ONE:
                # First, the raw data is read converting all the fields into strings
                schema_response = self.catalogue.get_schema(table_source.database_relation, table_source.table)
                columns = schema_response.schema.get_column_names(partitioned=True)
                spark_schema = utils.convert_schema_to_strings(columns=columns)
                df_raw = self._read_raw_file(
                    base_path=read_path.base_path,
                    data_path=read_path.path,
                    schema=spark_schema
                )
            elif table_target.casting.strategy == CastingStrategy.DYNAMIC:
                # The schema is inferred directly from the raw data
                df_raw = self._read_raw_file(base_path=read_path.base_path, data_path=read_path.path)

            self._track_table_metric(table_config=table_source, data_frame=df_raw)
            df_raw = utils.apply_transformations(
                df=df_raw,
                transformations=table_target.casting.transformations,
                table_target=table_target
            )
            if table_target.casting.strategy == CastingStrategy.ONE_BY_ONE:
                if debug_code:
                    logger.info('Schema before casting:')
                    df_raw.printSchema()
                # After reading the data as strings, each field is converted to its corresponding data type
                view_name = 'data_to_cast'
                df_raw.createOrReplaceTempView(view_name)
                query = Cast().build_datacast_query(
                    source_columns=df_raw.columns,
                    table_target=table_target,
                    add_missing_columns=table_target.casting.add_missing_columns,
                    view_name=view_name
                )
                df_raw = self._execute_query(query)
                if debug_code:
                    logger.info(f'Casting query: {query}')
                    logger.info('Schema after casting:')
                    df_raw.printSchema()
            return ReadResponse(success=True, error=None, data=df_raw)
        except Exception:
            raise CastDataError(
                source_database=table_source.database_relation,
                source_table=table_source.table,
                target_database=table_target.database_relation,
                target_table=table_target.table,
                casting_strategy=table_target.casting.strategy.value
            )

    def _read_raw_file(self, base_path: str, data_path: str, schema: Union[StructType, None] = None) -> DataFrame:
        incoming_file = config().processes.landing_to_raw.incoming_file
        file_format = incoming_file.file_format
        spark_read_config = incoming_file.specifications.read_config
        final_data_path = None
        if config().parameters.execution_mode == ExecutionMode.DELTA:
            spark_read_config["basePath"] = base_path
            final_data_path = data_path
        else:
            final_data_path = base_path
        logger.info(f"read with spark options {spark_read_config}")
        spark_read = self.spark.read.options(**spark_read_config)
        if schema is not None:
            spark_read = spark_read.schema(schema)
        if file_format == LandingFileFormat.CSV:
            return spark_read.csv(final_data_path)
        else:
            return spark_read.parquet(final_data_path)

    def _read_raw_json_file(self, table_path: str, file_path: str, casting_strategy: CastingStrategy) -> DataFrame:
        # Read JSON files from S3
        if config().parameters.execution_mode == ExecutionMode.DELTA:
            response = self.storage.read(layer=Layer.RAW, key_path=file_path)
            files = [BytesIO(response.data)]
        else:
            files = [
                BytesIO(self.storage.read(layer=Layer.RAW, key_path=file_key).data)
                for file_key in self.storage.list_files(layer=Layer.RAW, prefix=table_path).result
            ]
        # Parse into Python dictionaries
        data = JsonParser().parse_json_files(files)
        # Transform into a DataFrame
        if casting_strategy == CastingStrategy.ONE_BY_ONE:
            # Obtain column names
            columns = set()
            [columns.update(item.keys()) for item in data]
            if debug_code:
                logger.info(f'Columns extracted from JSON file: {columns}')
            # All fields are converted into strings
            schema = utils.convert_schema_to_strings(columns=columns)
            df = self.create_dataframe(data=data, schema=schema).data
            return df
        elif casting_strategy == CastingStrategy.DYNAMIC:
            # Each field type is inferred by Spark
            df = self.create_dataframe(data=data).data
            return df

    def _execute_query(self, query: str) -> DataFrame:
        max_retries = 6

        for attempt in range(max_retries):
            try:
                df_result = self.spark.sql(query)
                break
            except Exception as exception:
                if any(word in str(exception) for word in iceberg_exceptions) and attempt < max_retries - 1:
                    logger.warning(exception)
                    
                    seconds_to_sleep = self.secure_random.randrange(15, 50)
                    logger.warning(f'[RETRY] {attempt} of {max_retries}: {seconds_to_sleep} seconds')
                    time.sleep(seconds_to_sleep)
                else:
                    raise exception
        return df_result

    def read_table(self, database: str, table: str, filter: str = None, columns: List[str] = None) -> ReadResponse:
        # TODO: use DatabaseTable instead of database and table strings
        try:
            table_name = self._build_complete_table_name(database=database, table=table)
            if columns:
                columns_str = ', '.join(columns)
                query = f"SELECT {columns_str} FROM {table_name}"
            else:
                query = f"SELECT * FROM {table_name}"
            if filter:
                query += f" WHERE {filter}"
            df = self._execute_query(query)
            return ReadResponse(success=True, error=None, data=df)
        except Exception:
            raise ReadDataError(query=query)

    def delete_from_table(self, table_config: DatabaseTable, _filter: str) -> WriteResponse:
        try:
            table_name = self._build_complete_table_name(table_config.database_relation, table_config.table)
            query = f"DELETE FROM {table_name} WHERE {_filter}"
            self._execute_query(query)
            return WriteResponse(success=True, error=None)
        except Exception:
            raise DeleteDataError(database=table_config.database_relation, table=table_config.table)

    def insert_dataframe(self, dataframe: DataFrame, table_config: DatabaseTable) -> WriteResponse:
        try:
            table_name = self._build_complete_table_name(table_config.database_relation, table_config.table)
            # Select only necessary columns of the dataframe
            dataframe = self._select_table_columns(dataframe, table_config)
            # Insert dataframe into table
            max_retries = 6
            for attempt in range(max_retries):
                try:
                    dataframe.writeTo(table_name).append()
                    return WriteResponse(success=True, error=None)
                except Exception as exception:
                    if any(word in str(exception) for word in iceberg_exceptions) and attempt < max_retries - 1:
                        logger.warning(exception)

                        seconds_to_sleep = self.secure_random.randrange(15, 50)
                        logger.warning(f'[RETRY] {attempt} of {max_retries}: {seconds_to_sleep} seconds')
                        time.sleep(seconds_to_sleep)
                    else:
                        raise exception
        except Exception:
            raise WriteDataError(database=table_config.database_relation, table=table_config.table)

    def _select_table_columns(self, dataframe: DataFrame, table_config: DatabaseTable) -> DataFrame:
        table_schema = self.catalogue.get_schema(
            database=table_config.database_relation,
            table=table_config.table
        )
        table_columns = table_schema.schema.get_column_names(partitioned=True)
        dataframe = dataframe.select([f.col(column).alias(column) for column in table_columns]).distinct()
        return dataframe

    def join(
        self,
        df_1: DataFrame,
        df_2: DataFrame,
        how: str,
        left_on: List[str],
        right_on: List[str] = None,
        left_suffix: str = '_df_1',
        right_suffix: str = '_df_2'
    ) -> ReadResponse:
        try:
            if how not in ['inner', 'left', 'right', 'outer']:
                raise ValueError(
                    f'Invalid parameter value: how={how}. Allowed values: inner, left, right, outer'
                )
            if not right_on:
                right_on = left_on
            # Make a copy of the dataframes
            df_1 = df_1.alias('df_1')
            df_2 = df_2.alias('df_2')
            # Rename common columns before the join
            common_columns = list(set(df_1.columns) & set(df_2.columns))
            for column in common_columns:
                if column not in left_on:
                    df_1 = df_1.withColumnRenamed(column, column + left_suffix)
                if column not in right_on:
                    df_2 = df_2.withColumnRenamed(column, column + right_suffix)
            # Perform join
            if left_on == right_on:
                df_result = df_1.join(df_2, on=left_on, how=how)
            elif len(left_on) != len(right_on):
                raise ValueError(
                    'Number of columns in left_on and right_on parameters must be the same. ' +
                    f'left_on: {len(left_on)} columns. right_on: {len(right_on)} columns'
                )
            else:
                for left_column, right_column in zip(left_on, right_on):
                    df_2 = df_2.withColumnRenamed(right_column, left_column)
                df_result = df_1.join(df_2, on=left_on, how=how)
            return ReadResponse(success=True, error=None, data=df_result)
        except Exception:
            raise DataProcessError(error_message='Error performing join of two dataframes')

    def create_dataframe(self, data: Any, schema: str = None) -> ReadResponse:
        try:
            df = self.spark.createDataFrame(data, schema)
            return ReadResponse(success=True, error=None, data=df)
        except Exception:
            raise DataProcessError(error_message='Error creating dataframe')

    def query(self, sql: str) -> ReadResponse:
        try:
            df = self._execute_query(sql)
            return ReadResponse(success=True, error=None, data=df)
        except Exception:
            raise ReadDataError(query=sql)

    def overwrite_columns(
        self,
        dataframe: DataFrame,
        columns: List[str],
        custom_column_suffix: str,
        default_column_suffix: str,
        drop_columns: bool = True
    ) -> ReadResponse:
        try:
            for column in columns:
                custom_column = column + custom_column_suffix
                default_column = column + default_column_suffix
                dataframe = dataframe.withColumn(
                    column,
                    f.when(
                        f.col(custom_column).isNull(), f.col(default_column)
                    ).otherwise(f.col(custom_column))
                )
                if drop_columns:
                    dataframe = dataframe.drop(f.col(custom_column), f.col(default_column))
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError()

    def unfold_string_values(self, dataframe: DataFrame, column_name: str, separator: str) -> ReadResponse:
        try:
            values = list(set(dataframe.filter(
                (f.col(column_name).isNotNull()) & (f.col(column_name) != '')
            ).select(
                f.explode(f.split(f.col(column_name), separator))
            ).rdd.flatMap(lambda x: x).collect()))
            return ReadResponse(success=True, error=None, data=values)
        except Exception:
            raise DataProcessError()

    def add_dynamic_column(
        self,
        dataframe: DataFrame,
        new_column: str,
        reference_column: str,
        available_columns: List[str],
        default_value: Any = None
    ) -> ReadResponse:
        try:
            if available_columns:
                # Build conditional expression for the new column
                expression = None
                for column in available_columns:
                    if expression is None:
                        # First item
                        expression = when(f.col(reference_column) == column, f.col(column))
                    else:
                        expression = expression.when(f.col(reference_column) == column, f.col(column))
                expression.otherwise(default_value)
            else:
                expression = f.lit(None)
            dataframe = dataframe.withColumn(new_column, expression)
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError()

    def stack_columns(
        self,
        dataframe: DataFrame,
        source_columns: List[str],
        target_columns: List[str]
    ) -> ReadResponse:
        try:
            if len(target_columns) != 2:
                raise ValueError(f'Must specify two columns as target_columns. Found {target_columns}')
            n_columns = len(source_columns)
            source_columns_str = ', '.join([f"'{column}', {column}" for column in source_columns])
            target_columns_str = ', '.join(target_columns)
            stack_expression = f"stack({n_columns}, {source_columns_str}) as ({target_columns_str})"
            dataframe = dataframe.select(*source_columns).selectExpr(stack_expression)
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError()

    def is_empty(self, dataframe: DataFrame) -> bool:
        if dataframe is not None:
            return dataframe.isEmpty()
        else:
            return True

    def count_rows(self, dataframe: DataFrame) -> int:
        return dataframe.count()

    def select_columns(self, dataframe: DataFrame, columns: List[str]) -> ReadResponse:
        try:
            dataframe = dataframe.select(*columns)
            return ReadResponse(success=True, error=None, data=dataframe)
        except Exception:
            raise DataProcessError('Error selecting columns of a dataframe')

    def show_dataframe(self, dataframe: DataFrame) -> WriteResponse:
        dataframe.show(truncate=False)

```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/transformations/add_filename_column.py
```python
from data_framework.modules.config.model.flows import Transformation
from data_framework.modules.config.core import config
from pyspark.sql import DataFrame
import pyspark.sql.functions as f


def add_filename_column(df: DataFrame, transformation: Transformation) -> DataFrame:
    if config().parameters.execution_mode.is_delta:
        df = df.withColumn(transformation.target_column, f.lit(config().parameters.file_name))
    else:
        pattern = r'^.+/([^/]+\..+)$'
        df = df.withColumn('file_path', f.input_file_name()) \
            .withColumn(transformation.target_column, f.regexp_extract('file_path', pattern, 1))
        df = df.drop('file_path')
    return df

```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/transformations/fix_incompatible_characters.py
```python
from data_framework.modules.config.model.flows import (
    Transformation,
    DatabaseTable
)
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from pyspark.sql import DataFrame
import pyspark.sql.functions as f
from pyspark.sql.types import StringType
import re


def fix_incompatible_characters(
    df: DataFrame,
    transformation: Transformation,
    table_target: DatabaseTable
) -> DataFrame:
    catalogue = CoreCatalogue()
    schema_target = catalogue.get_schema(table_target.database_relation, table_target.table).schema
    target_columns = schema_target.get_column_type_mapping(partitioned=True)

    df_modified = df
    udf_fix_column_incompatible_characters = f.udf(fix_column_incompatible_characters, StringType())

    for field in df.schema.fields:
        new_field_name = re.sub(r'\W', '', field.name)
        target_type = target_columns.get(field.name.lower(), 'string')
        if 'struct<' in target_type:
            df_modified = df_modified.withColumn(
                field.name,
                udf_fix_column_incompatible_characters(f.col(field.name))
            )
        if field.name != new_field_name:
            df_modified = df_modified.withColumnRenamed(field.name, new_field_name)

    return df_modified


def fix_column_incompatible_characters(struct: str) -> str:

    def repl(match):
        key = match.group(1)
        new_key = re.sub(r'\W', '', key).lower()
        return f'"{new_key}"'

    try:
        if struct:
            # key=value => "key": "value"
            struct = re.sub(r'([^{},=\s]+)=([^,}]+)', r'"\1": "\2"', struct)
            # '@key' => "key" or "@key" => "key"
            struct = re.sub(r"[\"']([^\"']+)[\"'](?=\s*:)", repl, struct)
            # 'key': => "key":
            struct = re.sub(r"'([^']+)'(\s*:\s*)", r'"\1"\2', struct)
            # :'value' => :"value"
            struct = re.sub(r"(:\s*)'([^']+)'", r'\1"\2"', struct)
    except Exception as e:
        raise ValueError(f'Error formatting string {struct}: {e}')

    return struct

```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/transformations/parse_dates.py
```python
from data_framework.modules.config.model.flows import Transformation
from pyspark.sql import DataFrame
import pyspark.sql.functions as f


def parse_dates(df: DataFrame, transformation: Transformation) -> DataFrame:
    for column in transformation.columns:
        if column not in df.columns:
            raise ValueError(
                f'Column {column} not found in raw DataFrame. Unable to apply transformation'
            )
        for source_format in transformation.source_format:
            df = df.withColumn(
                column,
                f.when(
                    f.to_date(f.col(column), source_format).isNotNull(),
                    f.date_format(
                        f.to_date(f.col(column), source_format),
                        transformation.target_format
                    )
                ).otherwise(f.col(column))
            )
    return df

```


---
## File: ./src/data_framework/modules/data_process/integrations/spark/utils.py
```python
from data_framework.modules.config.model.flows import (
    Transformation,
    DatabaseTable
)
from data_framework.modules.exception.data_process_exceptions import (
    TransformationNotImplementedError,
    TransformationError,
    DataControlNotImplementedError,
    DataControlError
)
from data_framework.modules.code.filter_kwargs import filter_function_kwargs
from importlib import import_module
from typing import List, Dict, Any
from pyspark.sql import DataFrame
from pyspark.sql.types import (
    StructType, StructField, StringType,
    IntegerType, FloatType, DoubleType,
    BooleanType, DateType, TimestampType
)


def convert_schema_to_strings(columns: List[str]) -> StructType:
    return StructType([
        StructField(column, StringType(), True)
        for column in columns
    ])


def map_to_spark_type(db_type: str):
    mapping = {
        "string": StringType(),
        "varchar": StringType(),
        "char": StringType(),
        "text": StringType(),
        "integer": IntegerType(),
        "int": IntegerType(),
        "bigint": IntegerType(),
        "smallint": IntegerType(),
        "tinyint": IntegerType(),
        "decimal": DoubleType(),
        "numeric": DoubleType(),
        "float": FloatType(),
        "double": DoubleType(),
        "boolean": BooleanType(),
        "date": DateType(),
        "timestamp": TimestampType()
    }
    return mapping.get(db_type.lower(), StringType())


def apply_data_controls(
    data_frame: DataFrame,
    target_table: DatabaseTable
):
    for data_control in target_table.data_controls:
        try:
            function_name = data_control.type.value
            module_name = f'data_framework.modules.data_process.integrations.spark.data_controls.{function_name}'
            module = import_module(module_name)
            transformation_function = getattr(module, function_name)
            transformation_function(
                df_source=data_frame,
                data_control=data_control,
                target_table=target_table
            )
        except (ModuleNotFoundError, AttributeError):
            raise DataControlNotImplementedError(control=function_name)
        except Exception:
            raise DataControlError(control=function_name)


def apply_transformations(
    df: DataFrame,
    transformations: List[Transformation],
    **kwargs: Dict[str, Any]
) -> DataFrame:
    for transformation in transformations:
        try:
            function_name = transformation.type.value
            module_name = f'data_framework.modules.data_process.integrations.spark.transformations.{function_name}'
            module = import_module(module_name)
            transformation_function = getattr(module, function_name)
            args = filter_function_kwargs(kwargs=kwargs, function=transformation_function)
            df = transformation_function(df=df, transformation=transformation, **args)
        except (ModuleNotFoundError, AttributeError):
            raise TransformationNotImplementedError(transformation=function_name)
        except Exception:
            raise TransformationError(transformation=function_name)
    return df

```


---
## File: ./src/data_framework/modules/data_process/interface_data_process.py
```python
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import List, Any
from data_framework.modules.config.model.flows import DatabaseTable


@dataclass
class ReadResponse:
    success: bool
    error: str
    data: Any


@dataclass
class WriteResponse:
    success: bool
    error: str


class DataProcessInterface(ABC):

    @abstractmethod
    def merge(self, dataframe: Any, table_config: DatabaseTable, custom_strategy: str = None) -> WriteResponse:
        pass

    @abstractmethod
    def insert_overwrite(self, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        pass
    
    @abstractmethod
    def datacast(
        self,
        table_source: DatabaseTable,
        table_target: DatabaseTable
    ) -> ReadResponse:
        pass

    @abstractmethod
    def read_table(self, database: str, table: str, filter: str, columns: List[str]) -> ReadResponse:
        pass

    @abstractmethod
    def delete_from_table(self, table_config: DatabaseTable, _filter: str) -> WriteResponse:
        pass

    @abstractmethod
    def insert_dataframe(self, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        pass

    @abstractmethod
    def join(
        self,
        df_1: Any,
        df_2: Any,
        how: str,
        left_on: List[str],
        right_on: List[str] = None,
        left_suffix: str = '_df_1',
        right_suffix: str = '_df_2'
    ) -> ReadResponse:
        pass

    @abstractmethod
    def create_dataframe(self, data: Any, schema: str = None) -> ReadResponse:
        pass

    @abstractmethod
    def query(self, sql: str) -> ReadResponse:
        pass

    @abstractmethod
    def overwrite_columns(
        self,
        dataframe: Any,
        columns: List[str],
        custom_column_suffix: str,
        default_column_suffix: str,
        drop_columns: bool = True
    ) -> ReadResponse:
        pass

    @abstractmethod
    def unfold_string_values(self, dataframe: Any, column_name: str, separator: str) -> ReadResponse:
        pass

    @abstractmethod
    def add_dynamic_column(
        self,
        dataframe: Any,
        new_column: str,
        reference_column: str,
        available_columns: List[str],
        default_value: Any = None
    ) -> ReadResponse:
        pass

    @abstractmethod
    def stack_columns(
        self,
        dataframe: Any,
        source_columns: List[str],
        target_columns: List[str]
    ) -> ReadResponse:
        pass

    @abstractmethod
    def is_empty(self, dataframe: Any) -> bool:
        pass

    @abstractmethod
    def count_rows(self, dataframe: Any) -> int:
        pass

    @abstractmethod
    def select_columns(self, dataframe: Any, columns: List[str]) -> ReadResponse:
        pass

    @abstractmethod
    def show_dataframe(self, dataframe: Any) -> WriteResponse:
        pass

```


---
## File: ./src/data_framework/modules/dataflow/interface_dataflow.py
```python
from abc import ABC
from data_framework.modules.config.core import config, Config
from data_framework.modules.config.model.flows import (
    TableDict,
    DatabaseTable,
    OutputReport,
    ExecutionMode,
    LandingToRaw,
    GenericProcess
)
from data_framework.modules.utils.logger import logger
from data_framework.modules.data_process.core_data_process import CoreDataProcess
from data_framework.modules.validation.core_quality_controls import CoreQualityControls
from data_framework.modules.notification.core_notifications import CoreNotifications
from data_framework.modules.notification.interface_notifications import NotificationToSend
from data_framework.modules.monitoring.core_monitoring import (
    CoreMonitoring,
    Metric,
    MetricNames
)
from data_framework.modules.exception.dataflow_exceptions import (
    DataflowInitializationError,
    PayloadResponseError
)
from data_framework.modules.exception.aws_exceptions import SSMError
from dataclasses import dataclass, asdict, field
from typing import Any, List, Optional
import boto3
import json
import time
import secrets


@dataclass
class DataQualityTable:
    database: str
    table: str


@dataclass
class DataQuality:
    tables: List[DataQualityTable] = field(default_factory=list)


@dataclass
class OutputResponse:
    name: str
    success: bool = False
    error: Any = None


@dataclass
class PayloadResponse:
    success: bool = False
    next_stage: bool = False
    file_name: str = None
    file_date: str = None
    data_quality: DataQuality = field(default_factory=DataQuality)
    outputs: List[OutputResponse] = field(default_factory=list)
    notifications: Optional[List[NotificationToSend]] = field(default_factory=list)

    def get_failed_outputs(self) -> List[str]:
        failed_outputs = [
            output.name
            for output in self.outputs
            if not output.success
        ]
        return failed_outputs

aws_ssm_exceptions = ['TooManyUpdates']

class DataFlowInterface(ABC):

    @property
    def config(self) -> Config:
        return self.__config

    @property
    def logger(self):
        return self.__logger

    @property
    def data_process(self) -> CoreDataProcess:
        return self.__data_process

    @property
    def quality_controls(self) -> CoreQualityControls:
        return self.__quality_controls

    @property
    def notifications(self) -> CoreNotifications:
        return self.__notifications

    @property
    def source_tables(self) -> TableDict:
        return self.__current_process_config.source_tables

    @property
    def target_tables(self) -> TableDict:
        return self.__current_process_config.target_tables

    @property
    def payload_response(self) -> PayloadResponse:
        return self.__payload_response

    @property
    def incoming_file(self) -> DatabaseTable:
        return self.__current_process_config.incoming_file

    @property
    def output_file(self) -> DatabaseTable:
        return self.__current_process_config.output_file

    @property
    def output_reports(self) -> List[OutputReport]:
        return self.__current_process_config.output_reports

    def __init__(self):
        try:
            self.__config = config()
            self.__current_process_config = self.__config.current_process_config()
            self.__logger = logger
            self.__data_process = CoreDataProcess()
            self.__quality_controls = CoreQualityControls()
            self.__notifications = CoreNotifications()
            self.__payload_response = PayloadResponse()
            self.__monitoring = CoreMonitoring()

            self.__ssm_client = boto3.client(
                'ssm',
                region_name=self.config.parameters.region
            )

            if self.config.is_first_process:
                self.__monitoring.track_process_metric(
                    name=MetricNames.DATAFLOW_START_EVENT,
                    value=1
                )

            self.__start_process = time.time()
        except Exception:
            raise DataflowInitializationError()

    def process(self):
        raise NotImplementedError('It is mandatory to implement process() function')

    def vars(self, name: str):
        return self.__current_process_config.vars.get_variable(name=name)

    def read_table_with_casting(
        self,
        name_of_raw_table: str,
        name_of_staging_table_to_casting: str = None,
        apply_quality_controls: bool = True
    ) -> Any:
        input_table = self.source_tables.table(name_of_raw_table)
        name_of_staging_table_to_casting = (
            name_of_staging_table_to_casting
            if name_of_staging_table_to_casting
            else name_of_raw_table
        )
        execution_mode = self.config.parameters.execution_mode
        casting_table = self.target_tables.table(name_of_staging_table_to_casting)
        response = self.data_process.datacast(
            table_source=input_table,
            table_target=casting_table
        )
        df = response.data
        if execution_mode == ExecutionMode.FULL:
            self.logger.info(
                f'[ExecutionMode:{execution_mode.value}] Read from {input_table.full_name}'
            )
        else:
            self.logger.info(
                f"[ExecutionMode:{execution_mode.value}] Read {df.count()} rows " +
                f"from {input_table.full_name} with partition {input_table.sql_where}"
            )
        if apply_quality_controls:
            controls_response = self.quality_controls.validate(
                layer=self.config.source_layer,
                table_config=input_table,
                df_data=df,
                framework_controls=True
            )
            df = controls_response.data
        return df

    def read_table(self, name_of_table: str, apply_quality_controls: bool = True) -> Any:
        input_table = self.source_tables.table(name_of_table)
        execution_mode = self.config.parameters.execution_mode
        sql_where = input_table.sql_where
        if execution_mode == ExecutionMode.FULL.value:
            sql_where = None
        response = self.data_process.read_table(
            database=input_table.database_relation,
            table=input_table.table,
            filter=sql_where
        )
        df = response.data
        if execution_mode == ExecutionMode.FULL.value:
            self.logger.info(
                f'[ExecutionMode:{execution_mode}] Read from {input_table.full_name}'
            )
        else:
            self.logger.info(
                f"[ExecutionMode:{execution_mode}] from {input_table.full_name} with partition {sql_where}"
            )
        if apply_quality_controls:
            controls_response = self.quality_controls.validate(
                layer=self.config.source_layer,
                table_config=input_table,
                df_data=df,
                framework_controls=True
            )
            df = controls_response.data
        return df

    def write(self, df: Any, output_table_key: str) -> None:
        output_table = self.target_tables.table(output_table_key)
        self.data_process.merge(
            df,
            output_table.database_relation,
            output_table.table,
            # TODO: obtain primary keys from Glue table
            output_table.primary_keys
        )
        self.logger.info(f'Successfully inserted data into {output_table.full_name}')

    def save_monitorization(self):

        seconds = time.time() - self.__start_process

        self.__monitoring.track_process_metric(
            name=MetricNames.PROCESS_END_EVENT,
            value=1,
            success=True
        )

        self.__monitoring.track_process_metric(
            name=MetricNames.PROCESS_DURATION,
            value=seconds
        )

        # if self.config.has_next_process == False:
        #     self.__monitoring.track_process_metric(
        #         name=MetricNames.DATAFLOW_END_EVENT,
        #         value=1,
        #         success=True
        #     )

    def save_payload_response(self):
        if self.config.parameters.execution_id == None:
            self.logger.warning("The payload is not saved because the execution_id parameter is not reported")
            return

        try:
            if isinstance(self.__current_process_config, LandingToRaw):
                dq_table = DataQualityTable(
                    database=self.__current_process_config.output_file.database.value,
                    table=self.__current_process_config.output_file.table
                )
                self.payload_response.data_quality.tables.append(dq_table)
            elif isinstance(self.__current_process_config, GenericProcess):
                for tale_name in self.__current_process_config.target_tables.tables:
                    table_info = self.__current_process_config.target_tables.table(table_key=tale_name)
                    dq_table = DataQualityTable(
                        database=table_info.database.value,
                        table=table_info.table
                    )

                    self.payload_response.data_quality.tables.append(dq_table)
            # Add notifications to send
            self.payload_response.notifications = self.__notifications.get_notifications_to_send()
            # Convert to JSON
            payload_json = json.dumps(asdict(self.payload_response), ensure_ascii=False, indent=2)
            self._update_ssm_key(
                value=payload_json
            )
        except Exception:
            raise PayloadResponseError()
        
    def _update_ssm_key(self, value: str):
        max_retries = 6
        secure_random = secrets.SystemRandom()

        ssm_name = (
            f'/dataflow/{self.config.project_id}/' +
            f'{self.config.parameters.dataflow}-{self.config.parameters.process}/result/{self.config.parameters.execution_id}'
        )

        for attempt in range(max_retries):
            try:
                self.__ssm_client.put_parameter(
                    Name=ssm_name,
                    Value=value,
                    Type='String',
                    Overwrite=True
                )
                break
            except Exception as exception:
                if any(word in str(exception) for word in aws_ssm_exceptions) and attempt < max_retries - 1:
                    logger.warning(exception)
                    
                    seconds_to_sleep = secure_random.randrange(15, 50)
                    logger.warning(f'[RETRY] {attempt} of {max_retries}: {seconds_to_sleep} seconds')
                    time.sleep(seconds_to_sleep)
                else:
                    raise SSMError(error_message=f'Error saving parameter {ssm_name} in SSM')

```


---
## File: ./src/data_framework/modules/exception/aws_exceptions.py
```python
"""Definition of exceptions for AWS services"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError


class STSError(DataFrameworkError):
    """Error related to STS"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class GlueError(DataFrameworkError):
    """Error related to Glue"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class AthenaError(DataFrameworkError):
    """Error related to Athena"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class SSMError(DataFrameworkError):
    """Error related to SSM"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class S3Error(DataFrameworkError):
    """Error related to S3"""

    def __init__(self, error_message: str):
        super().__init__(error_message)

```


---
## File: ./src/data_framework/modules/exception/catalogue_exceptions.py
```python
"""Definition of exceptions for catalogue module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError


class CreatePartitionError(DataFrameworkError):
    """Error raised when a partition could not be created in a table"""

    def __init__(
        self,
        database: str,
        table: str,
        partition_field: str,
        partition_value: str
    ):
        super().__init__(
            f'Error creating partition {partition_field}={partition_value} in {database}.{table}'
        )


class InvalidPartitionFieldError(DataFrameworkError):
    """Error raised when the specified partition field does not exist in a table"""

    def __init__(
        self,
        database: str,
        table: str,
        partition_field: str
    ):
        super().__init__(
            f'Field {partition_field} does not exist in {database}.{table}'
        )


class SchemaError(DataFrameworkError):
    """Error raised when a table schema could not be obtained correctly"""

    def __init__(self, database: str, table: str):
        super().__init__(
            f'Error obtaining schema of {database}.{table}'
        )

```


---
## File: ./src/data_framework/modules/exception/config_exceptions.py
```python
"""Definition of exceptions for config module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError
from typing import List


class ConfigError(DataFrameworkError):
    """Base class for all config module exceptions"""

    def __init__(self):
        super().__init__('Error initializing Data Framework config')


class ConfigFileNotFoundError(DataFrameworkError):
    """Error raised when a config file is not found in the specified path"""

    def __init__(self, config_file_path: str):
        super().__init__(f'Config file {config_file_path} not found')


class AccountNotFoundError(DataFrameworkError):
    """Error raised when a specific AWS accound ID is not found in the Data Framework config file"""

    def __init__(self, account_id: str, available_ids: List[str]):
        available_ids = ', '.join(available_ids)
        super().__init__(
            f'AWS account ID {account_id} not found in Data Framework config file. ' +
            f'Available account IDs: {available_ids}'
        )


class ParameterParseError(DataFrameworkError):
    """Error raised when the input parameters could not be parsed correctly"""

    def __init__(self, arguments: List[str]):
        super().__init__(f'Error parsing input arguments {arguments}')


class ConfigParseError(DataFrameworkError):
    """Error raised when the dataflow config could not be parsed correctly"""

    def __init__(self, field: str, field_type: str):
        super().__init__(
            f'Error parsing field \'{field}\' to type {field_type}. ' +
            'Please check this field in your config file'
        )


class EmptyProcessConfigError(DataFrameworkError):
    """Error raised when the configuration of a specific process is empty"""

    def __init__(self, process: str):
        super().__init__(
            f'Configuration of process {process} is empty'
        )


class ProcessNotFoundError(DataFrameworkError):
    """Error raised when a specific process is not found in the dataflow config file"""

    def __init__(self, process: str, available_processes: List[str]):
        available_processes = ', '.join(available_processes)
        super().__init__(
            f'Process {process} not found in config. Available processes: {available_processes}'
        )


class TableKeyError(DataFrameworkError):
    """Error raised when a specific table key is not found in the dataflow config file"""

    def __init__(self, table_key: str, available_table_keys: List[str]):
        available_table_keys = ', '.join(available_table_keys)
        super().__init__(
            f'Table key {table_key} not found in config file. Available table keys: {available_table_keys}'
        )


class TableConfigNotFoundError(DataFrameworkError):
    """Error raised when a specific database and table are not found in the dataflow config file"""

    def __init__(self, database: str, table: str):
        super().__init__(
            f'Table key for {database}.{table} not found in config file'
        )

```


---
## File: ./src/data_framework/modules/exception/data_process_exceptions.py
```python
"""Definition of exceptions for data process module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError
from typing import List


class TransformationNotImplementedError(DataFrameworkError):
    """Error raised when a transformation specified in the config file is not implemented yet"""

    def __init__(self, transformation: str, available_types: List[str] = None):
        error_message = f'Transformation type {transformation} not implemented'
        if available_types:
            available_types = ', '.join(available_types)
            error_message += f'. Available transformations: {available_types}'
        super().__init__(error_message)

class TransformationError(DataFrameworkError):
    """Error raised when a pre-defined transformation fails"""

    def __init__(self, transformation: str):
        super().__init__(f'Error performing {transformation} transformation')

class DataControlNotImplementedError(DataFrameworkError):
    """Error raised when a data control specified in the config file is not implemented yet"""

    def __init__(self, control: str, available_types: List[str] = None):
        error_message = f'Data control type {control} not implemented'
        if available_types:
            available_types = ', '.join(available_types)
            error_message += f'. Available data controls: {available_types}'
        super().__init__(error_message)

class DataControlError(DataFrameworkError):
    """Error raised when a pre-defined control data fails"""

    def __init__(self, control: str):
        super().__init__(f'Error performing {control} data control')

class CastQueryError(DataFrameworkError):
    """Error raised when the generation of a query to cast data fails"""

    def __init__(self):
        super().__init__('Error generating query for data casting')


class CastDataError(DataFrameworkError):
    """Error raised when the casting of a table fails"""

    def __init__(
        self,
        source_database: str,
        source_table: str,
        target_database: str,
        target_table: str,
        casting_strategy: str
    ):
        super().__init__(
            f'Error casting data from table {source_database}.{source_table} ' +
            f'to table {target_database}.{target_table} with strategy {casting_strategy}'
        )


class ReadDataError(DataFrameworkError):
    """Error raised when a query to read data fails"""

    def __init__(self, query: str):
        super().__init__(f'Error reading data with query {query}')


class WriteDataError(DataFrameworkError):
    """Error raised when data could not be written successfully in a table"""

    def __init__(self, database: str, table: str):
        super().__init__(f'Error writing data to {database}.{table}')


class DeleteDataError(DataFrameworkError):
    """Error raised when data could not be deleted successfully from a table"""

    def __init__(self, database: str, table: str):
        super().__init__(f'Error deleting data from {database}.{table}')


class DataProcessError(DataFrameworkError):
    """Error raised when a data transformation fails"""

    def __init__(self, error_message: str = 'Error performing data transformation'):
        super().__init__(error_message)


class SparkConfigurationError(DataFrameworkError):
    """Error raised when Spark configuration fails"""

    def __init__(self, error_message: str = 'Error configuring Spark session'):
        super().__init__(error_message)


class JsonParseError(DataFrameworkError):
    """Error raised when a JSON file could not be parsed correctly"""

    def __init__(self, error_message: str = 'Error parsing JSON file'):
        super().__init__(error_message)

```


---
## File: ./src/data_framework/modules/exception/dataflow_exceptions.py
```python
"""Definition of exceptions for dataflow module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError


class DataflowInitializationError(DataFrameworkError):
    """Error raised when dataflow class could not be initialized correctly"""

    def __init__(self):
        super().__init__('Error initializing dataflow process')


class PayloadResponseError(DataFrameworkError):
    """Error raised when dataflow response could not be generated correctly"""

    def __init__(self):
        super().__init__('Error generating dataflow payload response')

```


---
## File: ./src/data_framework/modules/exception/generic_exceptions.py
```python
"""Definition of generic data framework exceptions"""

import traceback


class DataFrameworkError(Exception):
    """Base class for all Data Framework exceptions"""

    def format_exception(self) -> str:
        error_type = type(self).__name__
        error_message = str(self)
        error_trace = traceback.format_exc()
        return f'Error type: {error_type}\n\nDescription: {error_message}\n\nTrace:\n\n{error_trace}'


class LoggerInitializationError(DataFrameworkError):
    """Error raised when logger could not be configured correctly"""

    def __init__(self):
        super().__init__('Failed to initialize logger')

```


---
## File: ./src/data_framework/modules/exception/landing_exceptions.py
```python
"""Definition of exceptions for landing step"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError


class FileProcessError(DataFrameworkError):
    """Error raised when a file could not be processed correctly"""

    def __init__(self, file_path: str):
        super().__init__(f'Error processing file {file_path}')


class FileReadError(DataFrameworkError):
    """Error raised when a file could not be read correctly"""

    def __init__(self, file_path: str):
        super().__init__(f'Error reading file {file_path}')


class InvalidDateRegexError(DataFrameworkError):
    """Error raised when the date in a filename does not match the specified regex"""

    def __init__(self, filename: str, pattern: str):
        super().__init__(
            f'The date in the filename {filename} does not match the pattern {pattern}. ' +
            'Please check the date regex pattern in your config file'
        )


class InvalidRegexGroupError(DataFrameworkError):
    """Error raised when the groups in the filename date regex are invalid"""

    def __init__(self, pattern: str):
        super().__init__(
            f'The name of the groups in the date regex pattern {pattern} must be year, month and day. ' +
            'Example: (?P<year>\\d{4})_(?P<month>\\d{2})_(?P<day>\\d{2}). ' +
            'Please check the date regex pattern in your config file'
        )


class InvalidFileError(DataFrameworkError):
    """Error raised when a file does not meet the defined quality controls"""

    def __init__(self, file_path: str):
        super().__init__(
            f'The file {file_path} has failed quality controls. ' +
            'Check controls results table for more information'
        )

```


---
## File: ./src/data_framework/modules/exception/notification_exceptions.py
```python
"""Definition of exceptions for notification module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError
from typing import List


class NotificationNotFoundError(DataFrameworkError):
    """Error raised when the system tries to send a notification that is not defined"""

    def __init__(self, notification: str, available_notifications: List[str] = None):
        error_message = f'Notification key {notification} not found'
        if available_notifications:
            available_notifications = ', '.join(available_notifications)
            error_message += f'. Available notification keys: {available_notifications}'
        else:
            error_message += '. No notifications defined'
        super().__init__(error_message)


class DuplicatedNotificationError(DataFrameworkError):
    """Error raised when the user defines a notification with
    the same name as one of the pre-defined notifications"""

    def __init__(self, duplicated_notifications: List[str]):
        duplicated_notifications = ', '.join(duplicated_notifications)
        super().__init__(
            f'The following notifications are already defined in Data Framework: {duplicated_notifications}. ' +
            'Please rename the notifications in your config file'
        )


class NotificationError(DataFrameworkError):
    """Error raised when a notification could not be sent successfully"""

    def __init__(self, notification: str):
        super().__init__(f'Error sending notification {notification}')


class SubjectLimitExceededError(DataFrameworkError):
    """Error raised when the maximum length of a notification subject is exceeded"""

    def __init__(self, notification: str, max_length: int):
        super().__init__(
            f'Subject of the {notification} notification exceeds the {max_length} character limit'
        )


class BodyLimitExceededError(DataFrameworkError):
    """Error raised when the maximum length of a notification body is exceeded"""

    def __init__(self, notification: str, max_length: int):
        super().__init__(
            f'Body of the {notification} notification exceeds the {max_length} character limit'
        )


class NotificationLimitExceededError(DataFrameworkError):
    """Error raised when the maximum number of notifications is exceeded"""

    def __init__(self, max_notifications: int):
        super().__init__(f'The limit of {max_notifications} notifications has been exceeded')

```


---
## File: ./src/data_framework/modules/exception/output_exceptions.py
```python
"""Definition of exceptions for output step"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError
from typing import List


class OutputError(DataFrameworkError):
    """Error raised when one or more output files could not be generated correctly"""

    def __init__(self, failed_outputs: List[str]):
        failed_outputs = ', '.join(failed_outputs)
        super().__init__(
            f'Error generating the following outputs: {failed_outputs}. ' +
            'Check logs for more information'
        )


class OutputGenerationError(DataFrameworkError):
    """Error raised when a specific output file could not be generated correctly"""

    def __init__(self, output_name: str, error_message: str):
        super().__init__(
            f'Error generating output {output_name}: {error_message}'
        )


class NoOutputDataError(DataFrameworkError):
    """Error raised when there is no data to include in a specific output file"""

    def __init__(self, output_name: str):
        super().__init__(
            f'No data available for output {output_name}'
        )

```


---
## File: ./src/data_framework/modules/exception/storage_exceptions.py
```python
"""Definition of exceptions for storage module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError


class StorageError(DataFrameworkError):
    """Error raised when a storage generic operation fails"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class StorageReadError(DataFrameworkError):
    """Error raised when a storage read operation fails"""

    def __init__(self, path: str):
        super().__init__(f'Error reading file {path}')


class StorageWriteError(DataFrameworkError):
    """Error raised when a storage write operation fails"""

    def __init__(self, path: str):
        super().__init__(f'Error writing file to {path}')

```


---
## File: ./src/data_framework/modules/exception/validation_exceptions.py
```python
"""Definition of exceptions for validation module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError
from typing import List


class QualityControlsError(DataFrameworkError):
    """Error raised when data validation fails"""

    def __init__(self, table_name: str):
        super().__init__(f'Error validating data from {table_name}')


class FailedRulesError(DataFrameworkError):
    """Error raised when one or more quality rules fail"""

    def __init__(self, n_failed_rules: int):
        super().__init__(f'There are {n_failed_rules} rules that could not be executed correctly')


class RuleComputeError(DataFrameworkError):
    """Error raised when a rule computation fails"""

    def __init__(self, rule_id: str, rule_type: str):
        super().__init__(f'The computation of rule {rule_id} of type {rule_type} has failed')


class ValidationFunctionNotFoundError(DataFrameworkError):
    """Error raised when the Python function defined for a validation is not found"""

    def __init__(self, function_name: str):
        super().__init__(
            f'Validation function {function_name} not found. ' +
            'Please check that the module or class that you have configure ' +
            'with QualityControls().set_parent() has this function defined'
        )


class QualityRuleNotFoundError(DataFrameworkError):
    """Error raised when the Data Framework rule defined for a validation is not found"""

    def __init__(self, rule_name: str, available_rules: List[str] = None):
        error_message = f'Data Framework rule {rule_name} not found. Please check that the name of the rule is correct'
        if available_rules:
            available_rules = ', '.join(available_rules)
            error_message += f'. Available rules: {available_rules}'
        super().__init__(error_message)


class ParentNotConfiguredError(DataFrameworkError):
    """Error raised when the parent with validation functions is not configured"""

    def __init__(self):
        super().__init__(
            'QualityControls parent is not set. Please define a module or class to serve ' +
            'as source of custom validation functions using QualityControls().set_parent()'
        )


class InvalidThresholdError(DataFrameworkError):
    """Error raised when the threshold defined for a rule is not valid"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class InvalidAlgorithmError(DataFrameworkError):
    """Error raised when the algorithm defined for a rule is not valid"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class InvalidRuleError(DataFrameworkError):
    """Error raised when the definition of a rule is not valid"""

    def __init__(self, error_message: str):
        super().__init__(error_message)


class InvalidDataFrameError(DataFrameworkError):
    """Error raised when the threshold defined for a rule is not valid"""

    def __init__(self, error_message: str):
        super().__init__(error_message)

```


---
## File: ./src/data_framework/modules/monitoring/core_monitoring.py
```python
from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.monitoring.interface_monitoring import (
    MonitoringInterface,
    Metric,
    MetricNames
)
from typing import Union

class CoreMonitoring:

    @LazyClassProperty
    def _monitoring(cls) -> MonitoringInterface:
        from data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring import AWSCloudWatch
        return AWSCloudWatch()

    @classmethod
    def track_metric(
        cls,
        metric: Metric
    ):
        cls._monitoring.track_metric(metric=metric)
    
    @classmethod
    def track_table_metric(
        cls,
        name: MetricNames,
        database: str,
        table: str,
        value: float
    ):
        cls._monitoring.track_table_metric(
            name=name,
            database=database,
            table=table,
            value=value
        )
    
    @classmethod
    def track_process_metric(
        cls,
        name: MetricNames,
        value: float,
        success: bool = None
    ):
        cls._monitoring.track_process_metric(name=name, value=value, success=success)

    @classmethod
    def track_computation_metric(
        cls,
        name: MetricNames,
        value: float
    ):
        cls._monitoring.track_computation_metric(name=name, value=value)
```


---
## File: ./src/data_framework/modules/monitoring/integrations/aws_cloudwatch/aws_cloudwatch_monitoring.py
```python
from data_framework.modules.monitoring.interface_monitoring import (
    MonitoringInterface,
    Metric,
    MetricNames,
    MetricUnits
)
from typing import (
    Union,
    Optional,
    List, Dict, Any
)
from dataclasses import dataclass
from data_framework.modules.config.core import config
from data_framework.modules.utils.logger import logger
import datetime
import boto3
import os
import boto3.data.cloudwatch
from botocore.client import BaseClient

@dataclass
class InternalMetric(Metric):

    @property
    def parse(self) -> Dict:
        return {
            "MetricName": self.name.value,
            "Dimensions": self.dimensions,
            "Timestamp": self._created_at,
            "Value": self.value,
            "Unit": self.name.unit.value
        }
    
class AWSCloudWatch(MonitoringInterface):

    __namespace = "DataFramework/Product"
    __computation_dimensions = [
        {
            'Name': 'ExecutionId', 'Value': 'KjNDxQ'
        },
        {
            'Name': 'ProjectId', 'Value': config().project_id
        },
        {
            'Name': 'DataFlow', 'Value': config().parameters.dataflow
        },
        {
            'Name': 'Process', 'Value': config().parameters.process
        },
        { # TODO: Move to another class (? Cloud -> AWS Parameters?)
            'Name': 'JobId', 'Value': os.environ.get("EMR_SERVERLESS_JOB_ID", "Unknown")
        }
    ]

    __process_dimensions = [
        {
            'Name': 'ExecutionId', 'Value': 'KjNDxQ'
        },
        {
            'Name': 'ProjectId', 'Value': config().project_id
        },
        {
            'Name': 'DataFlow', 'Value': config().parameters.dataflow
        },
        {
            'Name': 'Process', 'Value': config().parameters.process
        },
        { # TODO: Move to another class (? Cloud -> AWS Parameters?)
            'Name': 'JobId', 'Value': os.environ.get("EMR_SERVERLESS_JOB_ID", "Unknown")
        }
    ]

    __table_dimensions = [
        {
            'Name': 'ExecutionId', 'Value': 'KjNDxQ'
        },
        {
            'Name': 'ProjectId', 'Value': config().project_id
        },
        {
            'Name': 'DataFlow', 'Value': config().parameters.dataflow
        },
        {
            'Name': 'Process', 'Value': config().parameters.process
        },
        { # TODO: Move to another class (? Cloud -> AWS Parameters?)
            'Name': 'JobId', 'Value': os.environ.get("EMR_SERVERLESS_JOB_RUN_ID", "Unknown")
        }
    ]

    @property
    def namespace(self) -> str:
        return self.__namespace

    @property
    def client(self) -> BaseClient:
        self._client

    def __init__(self):
        self._client = boto3.client(
            'cloudwatch',
            region_name=config().parameters.region,
            endpoint_url=f'https://monitoring.{os.environ["AWS_REGION"]}.amazonaws.com'
        )

    def track_metric(self, metric: InternalMetric):
        pass
        # response = self._client.put_metric_data(
        #     Namespace=self.namespace,
        #     MetricData=[
        #         metric.parse
        #     ]
        # )

    def track_table_metric(
        self,
        name: MetricNames,
        database: str,
        table: str,
        value: float
    ):
        dimenions = self.__table_dimensions
        
        dimenions = dimenions + [
            {'Name': 'Database', 'Value': database},
            {'Name': 'Table', 'Value': table}
        ]

        metric = InternalMetric(
            name=name,
            value=value,
            dimensions=dimenions
        )

        self.track_metric(metric=metric)
        
    
    def track_process_metric(self, name: MetricNames, value: float, success: bool = None):
        dimenions = self.__process_dimensions
        if success:
            dimenions.append({'Name': 'Success', 'Value': str(success).lower()})

        metric = InternalMetric(
            name=name,
            value=value,
            dimensions=dimenions
        )

        self.track_metric(metric=metric)

    def track_computation_metric(self, name: MetricNames, value: float):
        metric = InternalMetric(
            name=name,
            value=value,
            dimensions=self.__computation_dimensions
        )

        self.track_metric(metric=metric)
```


---
## File: ./src/data_framework/modules/monitoring/interface_monitoring.py
```python
from abc import ABC, abstractmethod
from typing import (
    Union,
    Optional,
    List, Dict, Any
)
from dataclasses import dataclass, field, fields
from enum import Enum
import time
from data_framework.modules.config.core import config
import os

class MetricUnits(Enum):
    SECONDS = "Seconds"
    MEGABYTES = "Megabytes"
    COUNT = "Count"
    PERCENT = "Percent"
    NONE = "None"

class MetricNames(Enum):
    UNKNOWN = "Unknown"

    TABLE_READ_RECORDS = "TableReadRecords"
    TABLE_READ_SIZE = "TableReadSize"

    TABLE_WRITE_ADDED_RECORDS = "TableWriteAddedRecords"
    TABLE_WRITE_ADDED_SIZE = "TableWriteAddedSize"
    TABLE_WRITE_DELETED_RECORDS = "TableWriteDeletedRecords"
    TABLE_WRITE_DELETED_SIZE = "TableWriteDeletedSize"
    TABLE_WRITE_TOTAL_RECORDS = "TableWriteTotalRecords"
    TABLE_WRITE_TOTAL_SIZE = "TableWriteTotalSize"

    DATAFLOW_START_EVENT = "DataFlowStartEvent"
    DATAFLOW_END_EVENT = "DataFlowEndEvent"

    PROCESS_START_EVENT = "ProcessStartEvent"
    PROCESS_END_EVENT = "ProcessEndEvent"
    PROCESS_DURATION = "ProcessDuration"

    @property
    def unit(self) -> MetricUnits:
        if self.name in [
            self.__class__.TABLE_READ_SIZE.name,
            self.__class__.TABLE_WRITE_ADDED_SIZE.name,
            self.__class__.TABLE_WRITE_DELETED_SIZE.name,
            self.__class__.TABLE_WRITE_TOTAL_SIZE.name
        ]:
            return MetricUnits.MEGABYTES
        
        if self.name in [
            self.__class__.PROCESS_DURATION.name
        ]:
            return MetricUnits.SECONDS
        
        if self.name in [
            self.__class__.TABLE_READ_RECORDS.name,
            self.__class__.TABLE_WRITE_ADDED_RECORDS.name,
            self.__class__.TABLE_WRITE_DELETED_RECORDS.name,
            self.__class__.TABLE_WRITE_TOTAL_RECORDS.name
        ]:
            return MetricUnits.COUNT
        
        if self.name in [
            self.__class__.DATAFLOW_START_EVENT.name,
            self.__class__.DATAFLOW_END_EVENT.name,
            self.__class__.PROCESS_START_EVENT.name,
            self.__class__.PROCESS_END_EVENT.name,
            self.__class__.UNKNOWN.name,
        ]:
            return MetricUnits.NONE
        

@dataclass
class Metric:
    _created_at: str = time.strftime("%Y-%m-%dT%H:%M:%SZ", time.gmtime())

    name: MetricNames = MetricNames.UNKNOWN
    value: float = 0
    dimensions: List[Dict[str, str]] = field(default_factory=List)

class MonitoringInterface(ABC):

    @abstractmethod
    def track_metric(
        self,
        metric: Metric
    ):
        # Abstract class to define the basic storage interface
        pass

    @abstractmethod
    def track_table_metric(
        self,
        name: MetricNames,
        database: str,
        table: str,
        value: float
    ):
        pass

    @abstractmethod
    def track_process_metric(
        self,
        name: MetricNames,
        value: float,
        success: bool = None
    ):
        pass

    @abstractmethod
    def track_computation_metric(
        self,
        name: MetricNames,
        value: float
    ):
        pass

```


---
## File: ./src/data_framework/modules/notification/core_notifications.py
```python
from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.notification.interface_notifications import (
    InterfaceNotifications,
    Notification
)
from typing import Any, Dict, List


class CoreNotifications(object):

    @LazyClassProperty
    def _notifications(cls) -> InterfaceNotifications:
        from data_framework.modules.notification.notifications import Notifications
        return Notifications()

    @classmethod
    def send_notification(cls, notification_name: str, arguments: Dict[str, Any]) -> None:
        return cls._notifications.send_notification(
            notification_name=notification_name,
            arguments=arguments
        )

    @classmethod
    def get_notifications_to_send(cls) -> List[Notification]:
        return cls._notifications.get_notifications_to_send()

```


---
## File: ./src/data_framework/modules/notification/interface_notifications.py
```python
from data_framework.modules.exception.notification_exceptions import NotificationNotFoundError
from abc import ABC, abstractmethod
from typing import Any, Dict, List, Union
from dataclasses import dataclass, field
from enum import Enum


class NotificationType(Enum):
    EMAIL = "email"


class Topic(Enum):
    INTERNAL = "internal"
    EXTERNAL = "external"


@dataclass
class Notification:
    type: NotificationType
    topics: List[Topic]
    subject: str
    body: str
    active: bool = True


@dataclass
class NotificationToSend:
    type: str
    topics: List[str]
    subject: str
    body: str


@dataclass
class NotificationDict:
    notifications: Dict[str, Notification] = field(default_factory=dict)

    def get_notification(self, notification_name: str) -> Union[Notification, None]:
        if not self.notifications:
            raise NotificationNotFoundError(notification=notification_name)
        notification = self.notifications.get(notification_name)
        if not notification:
            raise NotificationNotFoundError(
                notification=notification_name,
                available_notifications=list(self.notifications.keys())
            )
        return notification


@dataclass
class NotificationsParameters:
    max_subject_length: int
    max_body_length: int
    max_number_of_notifications: int
    signature: str


@dataclass
class DataFrameworkNotifications:
    notifications: NotificationDict
    parameters: NotificationsParameters


class InterfaceNotifications(ABC):

    @abstractmethod
    def send_notification(self, notification_name: str, arguments: Dict[str, Any]) -> None:
        pass

    @abstractmethod
    def get_notifications_to_send(self) -> List[Notification]:
        pass

```


---
## File: ./src/data_framework/modules/notification/notifications.py
```python
from data_framework.modules.config.core import config
from data_framework.modules.utils.logger import logger
from data_framework.modules.config.model.flows import Environment, ExecutionMode
from data_framework.modules.notification.interface_notifications import (
    InterfaceNotifications,
    NotificationDict,
    NotificationType,
    NotificationToSend,
    Notification
)
from data_framework.modules.exception.notification_exceptions import (
    DuplicatedNotificationError,
    NotificationError,
    NotificationLimitExceededError,
    SubjectLimitExceededError,
    BodyLimitExceededError
)
from typing import Dict, Any, List


class Notifications(InterfaceNotifications):

    def __init__(self):
        self.config = config()
        self.notifications = self._combine_notifications()
        self.notifications_to_send = []

    def _combine_notifications(self) -> NotificationDict:
        default_notifications = self.config.data_framework_notifications.notifications
        custom_notifications = self.config.current_process_config().notifications
        default_keys = default_notifications.notifications.keys()
        custom_keys = custom_notifications.notifications.keys()
        common_keys = set(default_keys) & set(custom_keys)
        if common_keys:
            raise DuplicatedNotificationError(duplicated_notifications=list(common_keys))
        else:
            combined_notifications = NotificationDict({
                **default_notifications.notifications,
                **custom_notifications.notifications
            })
            return combined_notifications

    def send_notification(self, notification_name: str, arguments: Dict[str, Any]) -> None:
        try:
            if self.config.parameters.execution_mode == ExecutionMode.DELTA:
                # Obtain notification info
                notification = self.notifications.get_notification(notification_name)
                if not notification.active:
                    logger.warning(f'Notification {notification_name} is deactivated')
                else:
                    if notification.type == NotificationType.EMAIL:
                        self._send_email_notification(notification, notification_name, arguments)
                    else:
                        raise NotImplementedError(f'Notification type {notification.type.value} not implemented')
                    logger.info(f'Notification {notification_name} sent successfully')
        except Exception:
            raise NotificationError(notification=notification_name)

    def _send_email_notification(
        self,
        notification: Notification,
        notification_name: str,
        arguments: Dict[str, Any]
    ) -> None:
        subject = self._format_subject(notification.subject, arguments)
        body = self._format_body(notification.body, arguments)
        self._validate_subject_length(subject, notification_name)
        self._validate_body_length(body, notification_name)
        notification_to_send = NotificationToSend(
            type=notification.type.value,
            topics=[topic.value for topic in notification.topics],
            subject=subject,
            body=body
        )
        self._add_notification(notification_to_send)

    def _format_subject(self, subject: str, arguments: Dict[str, Any]) -> str:
        if self.config.environment == Environment.DEVELOP:
            return '[DEV]' + subject.format_map(arguments)
        elif self.config.environment == Environment.PREPRODUCTION:
            return '[PRE]' + subject.format_map(arguments)
        else:
            return subject.format_map(arguments)

    def _format_body(self, body: str, arguments: dict) -> str:
        signature = self.config.data_framework_notifications.parameters.signature
        if signature:
            return body.format_map(arguments) + signature
        else:
            return body.format_map(arguments)

    def _validate_subject_length(self, subject: str, notification_name: str) -> None:
        max_subject_len = self.config.data_framework_notifications.parameters.max_subject_length
        if len(subject) > max_subject_len:
            raise SubjectLimitExceededError(notification=notification_name, max_length=max_subject_len)

    def _validate_body_length(self, body: str, notification_name: str) -> None:
        max_body_len = self.config.data_framework_notifications.parameters.max_body_length
        if len(body) > max_body_len:
            raise BodyLimitExceededError(notification=notification_name, max_length=max_body_len)

    def _add_notification(self, notification_to_send: NotificationToSend) -> None:
        max_notifications = self.config.data_framework_notifications.parameters.max_number_of_notifications
        if len(self.notifications_to_send) < max_notifications:
            self.notifications_to_send.append(notification_to_send)
        else:
            raise NotificationLimitExceededError(max_notifications=max_notifications)

    def get_notifications_to_send(self) -> List[NotificationToSend]:
        return self.notifications_to_send

```


---
## File: ./src/data_framework/modules/storage/core_storage.py
```python
from data_framework.modules.config.core import config
from data_framework.modules.config.model.flows import Environment
from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.storage.interface_storage import (
    CoreStorageInterface,
    Database,
    Layer,
    ReadResponse,
    WriteResponse,
    ListResponse,
    PathResponse
)


class Storage:

    @LazyClassProperty
    def _storage(cls) -> CoreStorageInterface:
        if config().environment != Environment.LOCAL:
            from data_framework.modules.storage.integrations.s3_storage import S3Storage
            return S3Storage()
        else:
            from data_framework.modules.storage.integrations.local_storage import LocalStorage
            return LocalStorage()

    @classmethod
    def read(cls, layer: Layer, key_path: str, bucket: str = None) -> ReadResponse:
        return cls._storage.read(layer=layer, key_path=key_path, bucket=bucket)

    @classmethod
    def write(
        cls,
        layer: Layer,
        database: Database,
        table: str,
        data: bytes,
        partitions: str,
        filename: str
    ) -> WriteResponse:
        return cls._storage.write(
            layer=layer,
            database=database,
            table=table,
            data=data,
            partitions=partitions,
            filename=filename
        )

    @classmethod
    def write_to_path(cls, layer: Layer, key_path: str, data: bytes) -> WriteResponse:
        return cls._storage.write_to_path(
            layer=layer,
            key_path=key_path,
            data=data
        )

    @classmethod
    def list_files(cls, layer: Layer, prefix: str) -> ListResponse:
        return cls._storage.list_files(layer=layer, prefix=prefix)

    @classmethod
    def raw_layer_path(cls, database: Database, table_name: str) -> PathResponse:
        response = cls._storage.raw_layer_path(
            database=database,
            table_name=table_name
        )
        return response

    @classmethod
    def base_layer_path(cls, layer: Layer) -> PathResponse:
        return cls._storage.base_layer_path(layer=layer)

```


---
## File: ./src/data_framework/modules/storage/integrations/local_storage.py
```python
from data_framework.modules.utils.logger import logger
from data_framework.modules.config.core import config
from data_framework.modules.storage.interface_storage import (
    CoreStorageInterface,
    Database,
    Layer,
    ReadResponse,
    WriteResponse,
    ListResponse,
    PathResponse
)
from data_framework.modules.exception.storage_exceptions import (
    StorageError,
    StorageReadError,
    StorageWriteError
)
import os


class LocalStorage(CoreStorageInterface):

    def __init__(self):
        self.logger = logger

    def read(self, layer: Layer, key_path: str, bucket: str = None) -> ReadResponse:
        try:
            if bucket is not None:
                folder = bucket
            else:
                folder = self._build_folder_name(layer)
            full_path = os.path.join(folder, os.path.normpath(key_path))
            if os.path.exists(full_path):
                with open(full_path, 'rb') as f:
                    data = f.read()
                return ReadResponse(error=None, success=True, data=data)
            else:
                message = f'Path {full_path} does not exist'
                self.logger.info(message)
                return ReadResponse(error=message, success=False, data=None)
        except Exception:
            return StorageReadError(path=full_path)

    def _build_folder_name(self, layer: Layer) -> str:
        return f'{config().parameters.bucket_prefix}-{layer.value}'

    def _build_file_path(
        self,
        database: Database,
        table: str,
        partitions: dict = {},
        filename: str = ''
    ) -> str:
        if partitions:
            partitions_path = '/'.join([f"{partition_name}={value}" for partition_name, value in partitions.items()])
            return f'{database.value}/{table}/{partitions_path}/{filename}'
        else:
            return f'{database.value}/{table}/{filename}'

    def write(
        self,
        layer: Layer,
        database: Database,
        table: str,
        data: bytes,
        filename: str,
        partitions: dict = None
    ) -> WriteResponse:
        try:
            folder = self._build_folder_name(layer=layer)
            key_path = self._build_file_path(
                database=database,
                table=table,
                partitions=partitions,
                filename=filename
            )
            full_path = os.path.join(folder, os.path.normpath(key_path))
            parent_path = os.path.dirname(full_path)
            os.makedirs(parent_path, exist_ok=True)
            with open(full_path, 'wb') as f:
                f.write(data)
            logger.info(f'Successfully wrote to path: {full_path}')
            return WriteResponse(success=True, error=None)
        except Exception:
            raise StorageWriteError(path=full_path)

    def write_to_path(self, layer: Layer, key_path: str, data: bytes) -> WriteResponse:
        try:
            folder = self._build_folder_name(layer=layer)
            full_path = os.path.join(folder, os.path.normpath(key_path))
            parent_path = os.path.dirname(full_path)
            os.makedirs(parent_path, exist_ok=True)
            with open(full_path, 'wb') as f:
                f.write(data)
            logger.info(f'Successfully wrote to path: {full_path}')
            return WriteResponse(success=True, error=None)
        except Exception:
            raise StorageWriteError(path=full_path)

    def list_files(self, layer: Layer, prefix: str) -> ListResponse:
        try:
            folder = self._build_folder_name(layer=layer)
            full_path = os.path.join(folder, os.path.normpath(prefix))
            if os.path.exists(full_path):
                logger.info(f'Listing files from path {full_path}')
                files_list = [
                    entry.path for entry in os.scandir(full_path) if entry.is_file()
                ]
                return ListResponse(error=None, success=True, result=files_list)
            else:
                message = f'Path {full_path} does not exist'
                self.logger.info(message)
                return ListResponse(error=message, success=False, result=[])
        except Exception:
            raise StorageError(
                error_message=f'Error listing files from {full_path}'
            )

    def raw_layer_path(self, database: Database, table_name: str, data_date: str) -> PathResponse:
        local_folder = self._build_folder_name(layer=Layer.RAW)
        local_path = self._build_file_path(
            database=database,
            table=table_name,
            partitions={"data_date": data_date}
        )
        final_path = f'{local_folder}/{local_path}'
        response = PathResponse(
            success=True,
            error=None,
            bucket=local_folder,
            path=None,
            base_path=final_path,
            relative_path=None,
            relative_base_path=None
        )
        return response

    def base_layer_path(self, layer: Layer) -> PathResponse:
        raise NotImplementedError('Function base_layer_path not implemented in local storage')

```


---
## File: ./src/data_framework/modules/storage/integrations/s3_storage.py
```python
import boto3
from data_framework.modules.utils.logger import logger
from data_framework.modules.config.core import config
from data_framework.modules.storage.interface_storage import (
    CoreStorageInterface,
    Database,
    Layer,
    ReadResponse,
    WriteResponse,
    ListResponse,
    PathResponse
)
from data_framework.modules.exception.aws_exceptions import S3Error
from data_framework.modules.exception.storage_exceptions import (
    StorageError,
    StorageReadError,
    StorageWriteError
)

from data_framework.modules.config.model.flows import (
    ExecutionMode
)
from typing import NamedTuple


class BuildPathResponse(NamedTuple):
    base_path: str
    data_path: str = None


class S3Storage(CoreStorageInterface):

    def __init__(self):
        self.s3 = boto3.client('s3', region_name=config().parameters.region)

    def read(self, layer: Layer, key_path: str, bucket: str = None) -> ReadResponse:
        try:
            if bucket is None:
                bucket = self._build_s3_bucket_name(layer=layer)
            try:
                response = self.s3.get_object(Bucket=bucket, Key=key_path)
            except Exception:
                raise S3Error(error_message='Error obtaining file from S3')
            logger.info(f'Successfully read from path: {key_path}')
            s3_data = response['Body'].read()
            return ReadResponse(data=s3_data, success=True, error=None)
        except Exception:
            raise StorageReadError(path=f's3://{bucket}/{key_path}')

    def _build_s3_bucket_name(self, layer: Layer) -> str:
        return f'{config().parameters.bucket_prefix}-{layer.value}'

    def _build_s3_key_path(
        self,
        database: Database,
        table: str,
        partitions: dict = {},
        filename: str = ''
    ) -> BuildPathResponse:
        if partitions:
            partitions_path = '/'.join([f"{partition_name}={value}" for partition_name, value in partitions.items()])
            return BuildPathResponse(
                data_path=f'{database.value}/{table}/{partitions_path}/{filename}',
                base_path=f'{database.value}/{table}/'
            )
        else:
            return BuildPathResponse(
                data_path=f'{database.value}/{table}/{filename}',
                base_path=f'{database.value}/{table}/'
            )

    def write(
        self,
        layer: Layer,
        database: Database,
        table: str,
        data: bytes,
        filename: str,
        partitions: dict = None
    ) -> WriteResponse:
        try:
            bucket = self._build_s3_bucket_name(layer=layer)
            key_path = self._build_s3_key_path(
                database=database,
                table=table,
                partitions=partitions,
                filename=filename
            )
            try:
                self.s3.put_object(Bucket=bucket, Key=key_path.data_path, Body=data)
            except Exception:
                raise S3Error(error_message='Error uploading file to S3')
            logger.info(f'Successfully wrote to path: {key_path.data_path}')
            return WriteResponse(success=True, error=None)
        except Exception:
            raise StorageWriteError(path=f's3://{bucket}/{key_path.data_path}')

    def write_to_path(self, layer: Layer, key_path: str, data: bytes) -> WriteResponse:
        try:
            bucket = self._build_s3_bucket_name(layer=layer)
            try:
                self.s3.put_object(Bucket=bucket, Key=key_path, Body=data)
            except Exception:
                raise S3Error(error_message='Error uploading file to S3')
            logger.info(f'Successfully wrote to path: {key_path}')
            return WriteResponse(success=True, error=None)
        except Exception:
            raise StorageWriteError(path=f's3://{bucket}/{key_path}')

    def list_files(self, layer: Layer, prefix: str) -> ListResponse:
        try:
            bucket = self._build_s3_bucket_name(layer=layer)
            logger.info(f'Listing files from bucket {bucket} with prefix {prefix}')
            paginator = self.s3.get_paginator('list_objects_v2')
            keys = []
            for pagination_response in paginator.paginate(Bucket=bucket, Prefix=prefix):
                status_code = pagination_response['ResponseMetadata']['HTTPStatusCode']
                if status_code == 200 and 'Contents' in pagination_response:
                    file_keys = [obj['Key'] for obj in pagination_response.get('Contents', [])]
                    keys.extend(file_keys)
            return ListResponse(error=None, success=True, result=keys)
        except Exception:
            raise StorageError(
                error_message=f'Error listing files from {bucket} with prefix {prefix}'
            )

    def raw_layer_path(self, database: Database, table_name: str) -> PathResponse:
        s3_bucket = self._build_s3_bucket_name(layer=Layer.RAW)
        partitions = {}
        if config().parameters.execution_mode == ExecutionMode.DELTA:
            partitions = {
                config().processes.landing_to_raw.output_file.partition_field: config().parameters.file_date
            }
            s3_key = self._build_s3_key_path(
                database=database,
                table=table_name,
                partitions=partitions,
                filename=config().parameters.file_name
            )
        else:
            s3_key = self._build_s3_key_path(database=database, table=table_name)

        response = PathResponse(
            success=True,
            error=None,
            bucket=s3_bucket,
            path=f's3://{s3_bucket}/{s3_key.data_path}',
            base_path=f's3://{s3_bucket}/{s3_key.base_path}',
            relative_path=s3_key.data_path,
            relative_base_path=s3_key.base_path
        )
        return response

    def base_layer_path(self, layer: Layer) -> PathResponse:
        s3_bucket = self._build_s3_bucket_name(layer=layer)
        final_path = f's3://{s3_bucket}'
        response = PathResponse(
            success=True,
            error=None,
            bucket=s3_bucket,
            path=None,
            base_path=final_path,
            relative_path=None,
            relative_base_path=None
        )
        return response

```


---
## File: ./src/data_framework/modules/storage/interface_storage.py
```python
from abc import ABC, abstractmethod
from enum import Enum
from dataclasses import dataclass


class Layer(Enum):
    LANDING = "landing"
    RAW = "raw"
    STAGING = "staging"
    COMMON = "common"
    BUSINESS = "business"
    OUTPUT = "output"
    ATHENA = "athena"
    # TODO: bucket for Athena in Infinity. Remove when migrating to Data Platform
    TEMP = "temp"


class Database(Enum):
    FUNDS_RAW = "funds_raw"
    FUNDS_STAGING = "funds_staging"
    FUNDS_COMMON = "funds_common"
    FUNDS_BUSINESS = "funds_business"
    DATA_QUALITY = "funds_common"
    CONFIG_SCHEMAS = 'df_config_schemas'
    # TODO: DBs for Infinity quality controls. Remove when migrating to Data Platform
    INFINITY_STAGING = "infinity_datalake_staging"
    INFINITY_COMMON = "infinity_datalake_common"
    INFINITY_BUSINESS = "infinity_datalake_business"
    SAM_STG = "sam_stg"
    SAM_DWH = "sam_dwh"


@dataclass
class ReadResponse:
    success: bool
    error: str
    data: bytes


@dataclass
class WriteResponse:
    success: bool
    error: str


@dataclass
class ListResponse:
    success: bool
    error: str
    result: list


@dataclass
class PathResponse:
    success: bool
    error: str
    bucket: str
    path: str
    base_path: str
    relative_path: str
    relative_base_path: str


class CoreStorageInterface(ABC):

    @abstractmethod
    def read(self, layer: Layer, key_path: str, bucket: str = None) -> ReadResponse:
        pass

    @abstractmethod
    def write(
        self,
        layer: Layer,
        database: Database,
        table: str,
        data: bytes,
        partitions: str,
        filename: str
    ) -> WriteResponse:
        pass

    @abstractmethod
    def write_to_path(self, layer: Layer, key_path: str, data: bytes) -> WriteResponse:
        pass

    @abstractmethod
    def list_files(self, layer: Layer, prefix: str) -> ListResponse:
        pass

    @abstractmethod
    def raw_layer_path(self, database: Database, table_name: str) -> PathResponse:
        pass

    @abstractmethod
    def base_layer_path(self, layer: Layer) -> PathResponse:
        pass

```


---
## File: ./src/data_framework/modules/utils/debug.py
```python
from data_framework.modules.config.core import config
from data_framework.modules.config.model.flows import Environment

debug_code = lambda: config().environment != Environment.PRODUCTION

```


---
## File: ./src/data_framework/modules/utils/logger.py
```python
from data_framework.modules.exception.generic_exceptions import LoggerInitializationError
import logging
import threading
import sys

LOG_FORMAT = '%(asctime)s [%(levelname)s][%(filename)s][%(funcName)s][%(lineno)d] %(message)s'
DATE_FORMAT = '%Y-%m-%d %H:%M:%S'


class Logger:
    _instance = None
    _lock = threading.Lock()

    def __new__(cls):
        if cls._instance is None:
            with cls._lock:
                cls._instance = super(Logger, cls).__new__(cls)
                try:
                    app_name = "DF"
                    log_level = logging.INFO
                    logger = logging.getLogger(app_name)
                    logger.setLevel(log_level)

                    formatter = logging.Formatter(fmt=LOG_FORMAT, datefmt=DATE_FORMAT)
                    console_handler = logging.StreamHandler(sys.stdout)
                    console_handler.setLevel(log_level)
                    console_handler.setFormatter(formatter)
                    logger.addHandler(console_handler)

                    cls._instance.logger = logging.getLogger(app_name)
                except Exception:
                    raise LoggerInitializationError()
        return cls._instance


logger = Logger()._instance.logger

```


---
## File: ./src/data_framework/modules/utils/regex.py
```python
import re


def change_file_extension(filename: str, new_extension: str) -> str:
    if not bool(re.match(r'\.', new_extension)):
        new_extension = '.' + new_extension
    extension_pattern = r'\.\w+$'
    if not bool(re.match(extension_pattern, new_extension)):
        raise ValueError(f'The specified extension {new_extension} is invalid')
    new_filename = re.sub(extension_pattern, new_extension, filename)
    return new_filename


def extract_substring(string: str, pattern: str) -> str:
    result = re.search(pattern, string)
    if not result:
        raise ValueError(f'The string {string} does not match the pattern {pattern}')
    return result.group()

```


---
## File: ./src/data_framework/modules/validation/core_quality_controls.py
```python
from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.validation.interface_quality_controls import InterfaceQualityControls, ControlsResponse
from data_framework.modules.config.model.flows import DatabaseTable
from data_framework.modules.storage.interface_storage import Layer
from typing import Any, Dict


class CoreQualityControls(object):

    @LazyClassProperty
    def _quality_controls(cls) -> InterfaceQualityControls:
        from data_framework.modules.validation.integrations.quality_controls import QualityControls
        return QualityControls()

    @classmethod
    def validate(
        cls,
        layer: Layer,
        table_config: DatabaseTable,
        df_data: Any = None,
        framework_controls: bool = False,
        **kwargs: Dict[str, Any]
    ) -> ControlsResponse:
        return cls._quality_controls.validate(
            layer=layer,
            table_config=table_config,
            df_data=df_data,
            framework_controls=framework_controls,
            **kwargs
        )

    @classmethod
    def set_parent(cls, parent: Any) -> None:
        return cls._quality_controls.set_parent(parent=parent)

```


---
## File: ./src/data_framework/modules/validation/integrations/quality_controls.py
```python
from data_framework.modules.validation.interface_quality_controls import (
    InterfaceQualityControls,
    ControlsResponse,
    ControlsTable,
    ControlRule,
    AlgorithmType,
    ControlLevel
)
from data_framework.modules.config.core import config
from data_framework.modules.utils.logger import logger
from data_framework.modules.data_process.core_data_process import CoreDataProcess
from data_framework.modules.config.model.flows import Technologies, DatabaseTable
from data_framework.modules.storage.interface_storage import Layer
from data_framework.modules.utils.debug import debug_code
from data_framework.modules.code.filter_kwargs import filter_function_kwargs
from data_framework.modules.exception.validation_exceptions import (
    QualityControlsError,
    FailedRulesError,
    ValidationFunctionNotFoundError,
    ParentNotConfiguredError,
    RuleComputeError,
    QualityRuleNotFoundError
)
from typing import Any, Dict
import pandas as pd
from importlib import import_module


class QualityControls(InterfaceQualityControls):

    def __init__(self):
        self.config = config()
        self.logger = logger
        self.data_process = CoreDataProcess()
        self.technology = self.config.current_process_config().processing_specifications.technology
        self.parent = None
        self.master_table = ControlsTable.master()
        self.dataset_table = ControlsTable.dataset()
        self.results_table = ControlsTable.results()
        self.df_data = None

    def set_parent(self, parent: Any) -> None:
        self.parent = parent

    def _set_df_data(self, df: Any) -> None:
        if df is not None:
            self.df_data = df

    def validate(
        self,
        layer: Layer,
        table_config: DatabaseTable,
        df_data: Any = None,
        framework_controls: bool = False,
        **kwargs: Dict[str, Any]
    ) -> ControlsResponse:
        try:
            self._set_df_data(df_data)
            if framework_controls:
                self.logger.info(f'Validating table {table_config.full_name} with default Data Framework controls')
            else:
                self.logger.info(f'Validating table {table_config.full_name}')
            df_rules = self._get_active_rules(layer, table_config, framework_controls)
            if self.data_process.is_empty(df_rules):
                self.logger.info(f'No rules defined for table {table_config.full_name}')
                response = ControlsResponse(
                    success=True,
                    error=None,
                    table=table_config,
                    data=self.df_data,
                    rules=None,
                    results=None,
                    overall_result=True
                )
            else:
                df_results = self._compute_rules(df_rules, **kwargs)
                if df_results is not None:
                    self._insert_results(df_results)
                    overall_result = self._get_overall_result(df_rules, df_results)
                    response = ControlsResponse(
                        success=True,
                        error=None,
                        table=table_config,
                        data=self.df_data,
                        rules=df_rules,
                        results=df_results,
                        overall_result=overall_result
                    )
                else:
                    self.logger.info('No control results to persist')
                    response = ControlsResponse(
                        success=True,
                        error=None,
                        table=table_config,
                        data=self.df_data,
                        rules=df_rules,
                        results=None,
                        overall_result=True
                    )
        except Exception as e:
            response = ControlsResponse(
                success=False,
                error=e,
                table=table_config,
                data=self.df_data,
                rules=None,
                results=None,
                overall_result=False
            )
            raise QualityControlsError(table_name=table_config.full_name)
        return response

    def _get_active_rules(self, layer: Layer, table_config: DatabaseTable, framework_controls: bool) -> Any:
        response_rules = self.data_process.read_table(
            self.dataset_table.table_config.database_relation,
            self.dataset_table.table_config.table,
            self.dataset_table.filter_rules(layer, table_config, framework_controls),
            columns=self.dataset_table.mandatory_columns
        )
        response_master = self.data_process.read_table(
            self.master_table.table_config.database_relation,
            self.master_table.table_config.table,
            columns=self.master_table.mandatory_columns
        )
        df_rules = response_rules.data
        df_master = response_master.data
        common_columns = list(set(df_rules.columns) & set(df_master.columns) - set(['control_master_id']))
        response = self.data_process.join(
            df_rules, df_master,
            how='left',
            left_on=['control_master_id'],
            left_suffix='_rules',
            right_suffix='_master'
        )
        df_rules = self.data_process.overwrite_columns(
            response.data, common_columns,
            custom_column_suffix='_rules',
            default_column_suffix='_master'
        ).data
        self.logger.info(
            f'Obtained {self.data_process.count_rows(df_rules)} rules ' +
            f'for layer {layer.value} and table {table_config.full_name}'
        )
        return df_rules

    def _get_overall_result(self, df_rules: Any, df_results: Any) -> bool:
        response = self.data_process.join(
            self.data_process.select_columns(
                df_rules,
                ['control_master_id', 'control_table_id', 'blocking_control_indicator', 'control_threshold_rag_max']
            ).data,
            self.data_process.select_columns(
                df_results,
                ['control_master_id', 'control_table_id', 'control_metric_value']
            ).data,
            how='inner',
            left_on=['control_master_id', 'control_table_id'],
        )
        # Check if there are blocker controls with KO result
        df = response.data
        if self.technology == Technologies.EMR:
            from pyspark.sql.functions import col, when
            # PySpark
            failed_controls = df.withColumn(
                'control_threshold_rag_max',
                when(col('control_threshold_rag_max').isNull(), 1.0).otherwise(col('control_threshold_rag_max'))
            ).filter(
                (col('blocking_control_indicator')) & (col('control_metric_value') < col('control_threshold_rag_max'))
            )
        else:
            # Pandas
            df['control_threshold_rag_max'] = df['control_threshold_rag_max'].fillna(1.0)
            failed_controls = df[
                (df['blocking_control_indicator']) &
                (df['control_metric_value'] < df['blocking_control_indicator'])
            ]
        if self.data_process.is_empty(failed_controls):
            return True
        else:
            return False

    def _insert_results(self, df_results: Any) -> None:
        self.data_process.insert_dataframe(df_results, self.results_table.table_config)
        self.logger.info(
            f'Successfully written control results in table {self.results_table.table_config.full_name}'
        )

    def _compute_rules(self, df_rules: Any, **kwargs: Dict[str, Any]) -> Any:
        if debug_code:
            self.logger.info('Controls definition:')
            self.data_process.show_dataframe(df_rules)
        if self.technology == Technologies.EMR:
            # Transform into Pandas dataframe
            df_rules = df_rules.toPandas()
        # Compute all rules
        df_results = df_rules.apply(self._compute_generic_rule, axis=1, **kwargs)
        if not df_results.empty:
            # Remove rules that raised an exception
            df_results = df_results[
                (df_results['control_master_id'].notna()) & (df_results['control_table_id'].notna())
            ]
        if len(df_rules) != len(df_results):
            raise FailedRulesError(n_failed_rules=(len(df_rules)-len(df_results)))
        if not df_results.empty:
            if self.technology == Technologies.EMR:
                # Transform into PySpark dataframe
                response = self.data_process.create_dataframe(df_results)
                df_results = response.data
            if debug_code:
                self.logger.info('Controls results:')
                self.data_process.show_dataframe(df_results)
            return df_results

    def _compute_generic_rule(self, rule_definition: pd.Series, **kwargs: Dict[str, Any]) -> pd.Series:
        try:
            # Parse rule
            rule = ControlRule.from_series(rule_definition)
            rule.result.set_data_date()
            if debug_code:
                self.logger.info(f'Parsed rule: {rule}')
            # Validate rule
            rule.validate()
            self.logger.info(f'Rule {rule.id} is valid')
            # Calculate rule
            self.logger.info(f'Computing rule {rule.id}')
            if rule.algorithm.algorithm_type == AlgorithmType.SQL.value:
                self._compute_sql_rule(rule, **kwargs)
            elif rule.algorithm.algorithm_type == AlgorithmType.PYTHON.value:
                self._compute_python_rule(rule, **kwargs)
            elif rule.algorithm.algorithm_type == AlgorithmType.REGEX.value:
                self._compute_regex_rule(rule)
            elif rule.algorithm.algorithm_type == AlgorithmType.DATA_FRAMEWORK.value:
                self._compute_data_framework_rule(rule, **kwargs)
            rule_result = rule.result.to_series()
            self.logger.info(f'Successfully computed rule {rule.id}')
            return rule_result
        except Exception:
            error = RuleComputeError(rule_id=rule.id, rule_type=rule.algorithm.algorithm_type)
            self.logger.error(error.format_exception())
            return pd.Series()

    def _compute_sql_rule(self, rule: ControlRule, **kwargs: Dict[str, Any]) -> None:
        query = rule.algorithm.algorithm_description.format(
            # TODO: usar kwargs
            file_date=self.config.parameters.file_date,
            file_name=self.config.parameters.file_name,
        )
        self.logger.info(f'Executing query {query}')
        # TODO: aplicar query sobre dataframe de datos en vez de BBDD
        response = self.data_process.query(query)
        result = rule.calculate_result(response.data)
        if result.invalid_identifiers:
            rule.result.add_detail(f"Invalid records: {', '.join(result.invalid_identifiers)}")

    def _compute_python_rule(self, rule: ControlRule, **kwargs: Dict[str, Any]) -> None:
        if not self.parent:
            raise ParentNotConfiguredError()
        try:
            function_name = rule.algorithm.algorithm_description
            validation_function = getattr(self.parent, function_name)
            args = filter_function_kwargs(kwargs=kwargs, function=validation_function)
            if rule.level == ControlLevel.DATA.value:
                validation_function(rule, self.df_data, **args)
            elif rule.level == ControlLevel.FILE.value:
                validation_function(rule, **args)
        except AttributeError:
            raise ValidationFunctionNotFoundError(function_name=function_name)

    def _compute_data_framework_rule(self, rule: ControlRule, **kwargs: Dict[str, Any]) -> None:
        try:
            rule_name = rule.algorithm.algorithm_description
            rule_namespace = f'data_framework.modules.validation.integrations.rules.{rule_name}'
            rule_module = import_module(rule_namespace)
            _class = getattr(rule_module, 'RuleCoordinator')
            _instance_class = _class(
                rule=rule,
                data_frame=self.df_data
            )
            args = filter_function_kwargs(kwargs=kwargs, function=_instance_class.process)
            _instance_class.process(**args)
            self._set_df_data(_instance_class.data_frame)
        except ModuleNotFoundError:
            raise QualityRuleNotFoundError(rule_name=rule_name)

    def _compute_regex_rule(self, rule: ControlRule) -> None:
        pattern = rule.algorithm.algorithm_description
        columns = rule.field_list
        target_columns = ['identifier', 'value']
        response = self.data_process.stack_columns(self.df_data, columns, target_columns)
        df_result = response.data
        if self.technology == Technologies.EMR:
            from pyspark.sql.functions import col, when, lit
            df_result = df_result \
                .withColumn('value', when(col('value').isNull(), lit('')).otherwise(col('value'))) \
                .withColumn('result', col('value').rlike(pattern))
        else:
            df_result['value'] = df_result['value'].fillna('')
            df_result['result'] = df_result['value'].str.match(pattern)
        df_result = self.data_process.select_columns(df_result, ['identifier', 'result']).data
        result = rule.calculate_result(df_result)
        if result.valid_identifiers:
            rule.result.add_detail(f"Valid columns: {', '.join(result.unique_valid_identifiers)}")
        if result.invalid_identifiers:
            rule.result.add_detail(f"Invalid columns: {', '.join(result.unique_invalid_identifiers)}")

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/check_json_status_code.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule
from pyspark.sql import DataFrame
from typing import Dict
import pandas as pd
from io import BytesIO
import json


class RuleCoordinator(InterfaceRule):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)

    def process(self, file_date: str, file_contents: Dict[str, BytesIO]) -> None:
        results = []
        identifiers = []
        for filename, file_data in file_contents.items():
            if file_data['validate']:
                file_data['content'].seek(0)
                json_content = json.load(file_data['content'])
                status_code = str(json_content['status_code'])
                valid_json = True if status_code == '200' else False
                identifier = f'{filename} (Status code: {status_code})'
                results.append(valid_json)
                identifiers.append(identifier)
        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': identifiers, 'result': results})
        ).data
        result = self.rule.calculate_result(df_result)
        if result.valid_identifiers:
            self.rule.result.add_detail(
                f"JSON files with valid status codes: {', '.join(result.valid_identifiers)}"
            )
        if result.invalid_identifiers:
            self.rule.result.add_detail(
                f"JSON files with invalid status codes: {', '.join(result.invalid_identifiers)}"
            )
        self.rule.result.set_data_date(file_date)

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/check_master_fields.py
```python
from data_framework.modules.validation.interface_quality_controls import (
    ControlRule
)

from pyspark.sql import DataFrame
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule

class RuleCoordinator(InterfaceRule):
    
    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)

    def process(self):
        self.require_primary_keys()

        self.logger.info(f'Validating {self.rule.database_table.full_name} with primary keys {self.rule.database_table.primary_keys}')

        source_columns = self.data_frame.columns
        master_fields = self.master_fields(
            source_fields=source_columns
        )

        sql = self.build_qa_query(
            master_fields=master_fields
        )

        df_result = self.data_process.query(
            sql=sql
        ).data

        # Calculate rule result
        result = self.rule.calculate_result(df_result)
        if result.invalid_identifiers:
            identifiers = '. '.join(result.invalid_identifiers)
            self.rule.result.add_detail(identifiers)

    def cast_column(self, column_name: str, column_type: str) -> str:
        if column_type in ('int', 'double', 'float', 'date', 'timestamp', 'boolean', 'bigint') or column_type.startswith('decimal'):
            query = f"""
                CASE
                    WHEN {column_name} IS NOT NULL THEN
                        COALESCE(
                            TRY_CAST({column_name} AS {column_type.upper()}),
                            CONCAT('_QA_KO_:', {column_name})
                        )
                    ELSE
                        {column_name}
                END AS {column_name}
            """
        elif 'struct<' in column_type or 'array<' in column_type:
            query = f"""
                CASE
                    WHEN {column_name} IS NOT NULL THEN
                        COALESCE(
                            FROM_JSON({column_name}, '{column_type}'),
                            CONCAT('_QA_KO_:', {column_name})
                        )
                    ELSE
                        {column_name}
                END AS {column_name}
            """
        else:
            query = f'{column_name}'
        return query

    def master_fields(self, source_fields: list[str]) -> dict:

        translate = str.maketrans('[]', '()')

        sql_source_fields = str(source_fields).translate(translate).lower()

        df_master_fields = self.data_process.query(
            sql=f"""
                SELECT
                    field,
                    type
                FROM
                    iceberg_catalog.rl_funds_common.master_fields
                WHERE
                    target = '{self.rule.database_table.table}'
                    AND LOWER(field) IN {sql_source_fields}
            """
        )

        rows = df_master_fields.collect()
        master_fields = {row['field']: row['type'] for row in rows}
        
        return master_fields
    
    def build_qa_query(self, master_fields: dict) -> str:
        sql_internal_qa_result = []
        sql_primary_keys = []
        sql_casted_columns = []

        for column_name, column_type in master_fields.items():
            sql_fix_column_name = f"`{column_name}`"
            qa_error_word = '_QA_KO_:'
            
            sql_cast = self.cast_column(
                column_name=sql_fix_column_name,
                column_type=column_type
            )
            
            sql_casted_columns.append(sql_cast)
            
            if column_name in self.rule.database_table.primary_keys:
                sql_primary_key = f"""CONCAT("{column_name}='", {sql_fix_column_name}, "'")"""
                sql_primary_keys.append(sql_primary_key)
            
            # Format: id='XXX', second_id='BBBB': campoX='VALUE' cannot cast 'X', campoZ='VALUEB' cannot cast 'B'
            
            if column_type != 'string':
                sql_qa_result = f"""
                    CASE
                        WHEN CONTAINS({sql_fix_column_name}, '{qa_error_word}') = true THEN
                            CONCAT(
                                '{column_name}',
                                "='",
                                REPLACE(
                                    {sql_fix_column_name},
                                    '{qa_error_word}'
                                ),
                                "' cannot cast ",
                                "'{column_type}', "
                            )
                        ELSE
                            ''
                    END
                """
                
                sql_internal_qa_result.append(sql_qa_result)

        casted_columns = [
            self.cast_columns(
                column_name=f"`{column_name}`",
                column_type=column_type
            )
            for column_name, column_type in master_fields.items()
        ]

        self.data_frame.createOrReplaceTempView('tmp_data_source')

        query = f"""
            SELECT
                CASE
                    WHEN LENGTH(__internal_result_qa) > 0 THEN
                        CONCAT(
                            {", ', ', ".join(sql_primary_keys)},
                            ': ',
                            SUBSTR(__internal_result_qa, 0, LENGTH(__internal_result_qa) - 2) 
                        )
                    ELSE
                        CONCAT(
                            {", ', ', ".join(sql_primary_keys)}
                        )
                END AS identifier,
                LENGTH(__internal_result_qa) > 0 AS result
            FROM (
                SELECT
                    CONCAT({', '.join(sql_internal_qa_result)}) AS __internal_result_qa
                FROM (
                    SELECT
                        {', '.join(casted_columns)}
                    FROM
                        tmp_data_source
                )
            )
        """

        return query


```


---
## File: ./src/data_framework/modules/validation/integrations/rules/check_primary_keys.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.drop_invalid_primary_keys import (
    RuleCoordinator as DropInvalidPrimaryKeys,
    DuplicatesStrategy
)
from pyspark.sql import DataFrame


class RuleCoordinator(DropInvalidPrimaryKeys):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)
        self.duplicates_strategy = DuplicatesStrategy.KEEP_ALL

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/drop_invalid_primary_keys.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule
from pyspark.sql.functions import col, count, greatest, concat_ws, when, concat, lit
from pyspark.sql import DataFrame
from enum import Enum


class DuplicatesStrategy(Enum):
    KEEP_ALL = "keep_all"
    DROP_ALL = "drop_all"
    KEEP_FIRST = "keep_first"


class RuleCoordinator(InterfaceRule):

    @property
    def duplicates_strategy(self) -> DuplicatesStrategy:
        return self._duplicates_strategy

    @duplicates_strategy.setter
    def duplicates_strategy(self, new_value: DuplicatesStrategy):
        self._duplicates_strategy = new_value

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)
        self._duplicates_strategy = DuplicatesStrategy.DROP_ALL

    def process(self) -> None:
        self.require_primary_keys()
        self.logger.info(
            f'Validating {self.rule.database_table.full_name} with primary keys {self.rule.database_table.primary_keys}'
        )
        # Generate identifier
        df_input = self.data_frame.withColumn(
            'identifier',
            concat_ws(', ', *[
                when(
                    col(column).isNull(), concat(lit(column), lit('=NULL'))
                ).otherwise(
                    concat(lit(column), lit('='), col(column))
                )
                for column in self.rule.database_table.primary_keys
            ])
        )
        # Count records with the same primary keys (duplicated records)
        df_result = df_input.groupBy(self.rule.database_table.primary_keys + ['identifier']) \
            .agg(count('*').alias('count')) \
            .withColumn(
                'has_duplicates',
                col('count') > 1
            )
        # Check if any of the primary keys is null
        df_result = df_result.withColumn(
            'has_null_pks',
            greatest(*[col(column).isNull() for column in self.rule.database_table.primary_keys])
        )
        # Join both results into one
        df_result = df_result.withColumn('result', ~col('has_duplicates') & ~col('has_null_pks'))
        # Select only needed columns
        df_result = df_result.select(*['identifier', 'result']).distinct()
        # Calculate rule result
        result = self.rule.calculate_result(df_result)
        if result.invalid_identifiers:
            identifiers = '), ('.join(result.invalid_identifiers)
            self.rule.result.add_detail(f'({identifiers})')

        if self.duplicates_strategy == DuplicatesStrategy.DROP_ALL:
            # Remove all records with null or duplicated PKs
            df_filtered = df_input.filter(~col('identifier').isin(result.invalid_identifiers))
            # Select only input columns
            df_filtered = df_filtered.select(self.data_frame.columns)
            self.data_frame = df_filtered

        elif self.duplicates_strategy == DuplicatesStrategy.KEEP_FIRST:
            # Remove records with duplicated PKs except the first one
            df_filtered = df_input.dropDuplicates(subset=self.rule.database_table.primary_keys)
            # Select only input columns
            df_filtered = df_filtered.select(self.data_frame.columns)
            self.data_frame = df_filtered

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/interface_rule.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.data_process.core_data_process import CoreDataProcess
from data_framework.modules.config.core import config
from pyspark.sql import DataFrame
from data_framework.modules.utils.logger import logger


class InterfaceRule:

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        self.rule = rule
        self.data_frame = data_frame
        self.data_process = CoreDataProcess()
        self.config = config()
        self.logger = logger

    def process(self):
        raise NotImplementedError('It is mandatory to implement process() function')

    def require_primary_keys(self):
        primary_keys = self.rule.database_table.primary_keys
        if not primary_keys:
            raise ValueError(
                f'Primary keys not defined for table {self.rule.database_table.full_name} in config file'
            )

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/unify_duplicated_primary_keys.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.drop_invalid_primary_keys import (
    RuleCoordinator as DropInvalidPrimaryKeys,
    DuplicatesStrategy
)
from pyspark.sql import DataFrame


class RuleCoordinator(DropInvalidPrimaryKeys):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)
        self.duplicates_strategy = DuplicatesStrategy.KEEP_FIRST

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/validate_csv_columns.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from pyspark.sql import DataFrame
from typing import Dict
import pandas as pd
import re
from io import BytesIO


class RuleCoordinator(InterfaceRule):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)
        self.catalogue = CoreCatalogue()

    def process(self, file_date: str, file_contents: Dict[str, BytesIO]) -> None:
        # Obtain expected columns from raw table
        response = self.catalogue.get_schema(
            self.config.current_process_config().output_file.database_relation,
            self.config.current_process_config().output_file.table
        )
        expected_columns = response.schema.get_column_names(partitioned=False)
        results = []
        filenames = []
        invalid_columns_info = []
        for filename, file_data in file_contents.items():
            if file_data['validate']:
                file_data['content'].seek(0)
                try:
                    df = pd.read_csv(
                        file_data['content'],
                        dtype=str,
                        delimiter=self.config.current_process_config().incoming_file.csv_specs.delimiter,
                        header=self.config.current_process_config().incoming_file.csv_specs.header_position,
                        encoding=self.config.current_process_config().incoming_file.csv_specs.encoding
                    )
                except Exception:
                    columns = []
                    valid_columns = False
                else:
                    columns = self._parse_columns(df)
                    valid_columns = columns == expected_columns
                results.append(valid_columns)
                filenames.append(filename)
                if not valid_columns:
                    extra_columns = list(set(columns) - set(expected_columns))
                    missing_columns = list(set(expected_columns) - set(columns))
                    invalid_columns_info.append(
                        f"{filename} (Extra columns: {', '.join(extra_columns) or None}, " +
                        f"Missing columns: {', '.join(missing_columns) or None})"
                    )
        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': filenames, 'result': results})
        ).data
        result = self.rule.calculate_result(df_result)
        if result.valid_identifiers:
            self.rule.result.add_detail(f"CSV files with valid columns: {', '.join(result.valid_identifiers)}")
        if result.invalid_identifiers:
            self.rule.result.add_detail(f"CSV files with invalid columns: {', '.join(invalid_columns_info)}")
        self.rule.result.set_data_date(file_date)

    def _parse_columns(self, df: pd.DataFrame) -> list:
        # TODO: definir un estándar y parametrizar en config
        # Replace whitespaces with _ and remove special characters
        columns = [
            re.sub(
                r'\s+', '_',
                re.sub(
                    r'[^A-Za-z0-9\s_]', '',
                    column.lower().strip().replace('/', ' ')
                )
            )
            for column in df.columns
        ]
        return columns

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/validate_csv_format.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule
from pyspark.sql import DataFrame
from typing import Dict
import pandas as pd
from io import BytesIO


class RuleCoordinator(InterfaceRule):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)
        self.incoming_file_config = self.config.current_process_config().incoming_file

    def process(self, file_date: str, file_contents: Dict[str, BytesIO]) -> None:
        delimiter = self.incoming_file_config.csv_specs.delimiter
        header = self.incoming_file_config.csv_specs.header_position
        encoding = self.incoming_file_config.csv_specs.encoding
        results = []
        filenames = []
        for filename, file_data in file_contents.items():
            if file_data['validate']:
                file_data['content'].seek(0)
                try:
                    df = pd.read_csv(
                        file_data['content'],
                        dtype=str,
                        delimiter=delimiter,
                        header=header,
                        encoding=encoding
                    )
                except Exception:
                    valid_csv = False
                else:
                    expected_n_columns = self._get_expected_number_of_columns(file_data['content'])
                    valid_csv = df.shape[1] == expected_n_columns
                results.append(valid_csv)
                filenames.append(filename)
        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': filenames, 'result': results})
        ).data
        result = self.rule.calculate_result(df_result)
        if result.valid_identifiers:
            self.rule.result.add_detail(f"CSV files with valid format: {', '.join(result.valid_identifiers)}")
        if result.invalid_identifiers:
            self.rule.result.add_detail(f"CSV files with invalid format: {', '.join(result.invalid_identifiers)}")
            self.rule.result.add_detail(f'Expected separator: {delimiter}')
            self.rule.result.add_detail(f'Expected header position: {header}')
            self.rule.result.add_detail(f'Expected encoding: {encoding}')
        self.rule.result.set_data_date(file_date)

    def _get_expected_number_of_columns(self, csv_content: BytesIO) -> int:
        csv_content.seek(0)
        header_position = self.incoming_file_config.csv_specs.header_position
        for i, line in enumerate(csv_content):
            if i == header_position:
                encoding = self.incoming_file_config.csv_specs.encoding
                delimiter = self.incoming_file_config.csv_specs.delimiter
                return len(line.decode(encoding).split(delimiter))

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/validate_filename_pattern.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule
from pyspark.sql import DataFrame
import pandas as pd
import re


class RuleCoordinator(InterfaceRule):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)

    def process(self, file_name: str, file_date: str) -> None:
        pattern = self.config.current_process_config().incoming_file.filename_pattern
        valid_filename = bool(re.match(pattern, file_name))
        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': [file_name], 'result': [valid_filename]})
        ).data
        result = self.rule.calculate_result(df_result)
        if result.valid_identifiers:
            self.rule.result.add_detail(f'Valid filenames: {file_name}')
        else:
            self.rule.result.add_detail(f'Invalid filenames: {file_name}. Expected pattern: {pattern}')
        self.rule.result.set_data_date(file_date)

```


---
## File: ./src/data_framework/modules/validation/integrations/rules/validate_unzipped_filename_pattern.py
```python
from data_framework.modules.validation.interface_quality_controls import ControlRule
from data_framework.modules.validation.integrations.rules.interface_rule import InterfaceRule
from pyspark.sql import DataFrame
from typing import Dict
import pandas as pd
import re
from io import BytesIO


class RuleCoordinator(InterfaceRule):

    def __init__(self, rule: ControlRule, data_frame: DataFrame):
        super().__init__(rule, data_frame)

    def process(self, file_date: str, file_contents: Dict[str, BytesIO]) -> None:
        pattern = self.config.current_process_config().incoming_file.filename_unzipped_pattern
        results = []
        filenames = []
        for filename, file_data in file_contents.items():
            if file_data['validate']:
                valid_filename = bool(re.match(pattern, filename))
                results.append(valid_filename)
                filenames.append(filename)
        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': filenames, 'result': results})
        ).data
        result = self.rule.calculate_result(df_result)
        if result.valid_identifiers:
            self.rule.result.add_detail(f"Valid filenames: {', '.join(result.valid_identifiers)}")
        if result.invalid_identifiers:
            self.rule.result.add_detail(f"Invalid filenames: {', '.join(result.invalid_identifiers)}")
            self.rule.result.add_detail(f'Expected pattern: {pattern}')
        self.rule.result.set_data_date(file_date)

```


---
## File: ./src/data_framework/modules/validation/interface_quality_controls.py
```python
from data_framework.modules.config.model.flows import DatabaseTable, Database, Technologies, Platform
from data_framework.modules.storage.interface_storage import Layer
from data_framework.modules.config.core import config
from data_framework.modules.exception.validation_exceptions import (
    InvalidThresholdError,
    InvalidDataFrameError,
    InvalidAlgorithmError,
    InvalidRuleError
)
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from enum import Enum
from typing import Any, Union, List, Dict
from datetime import datetime, date
import pandas as pd


@dataclass
class ControlsResponse:
    success: bool
    table: DatabaseTable
    overall_result: bool
    error: Any = None
    data: Any = None
    rules: Any = None
    results: Any = None


@dataclass
class ControlsTable:
    table_config: DatabaseTable
    mandatory_columns: List[str]

    @classmethod
    def master(cls) -> Any:
        # TODO: remove when migrating Infinity to Data Platform
        database = (
            Database.INFINITY_COMMON if config().platform == Platform.INFINITY
            else Database.DATA_QUALITY
        )
        return cls(
            table_config=DatabaseTable(
                database=database,
                table='controls_master',
                primary_keys=['control_master_id']
            ),
            mandatory_columns=[
                "control_master_id",
                "control_level",
                "control_threshold_min",
                "control_threshold_max",
                "control_threshold_type"
            ]
        )

    @classmethod
    def dataset(cls) -> Any:
        # TODO: remove when migrating Infinity to Data Platform
        database = (
            Database.INFINITY_COMMON if config().platform == Platform.INFINITY
            else Database.DATA_QUALITY
        )
        return cls(
            table_config=DatabaseTable(
                database=database,
                table='controls_dataset',
                primary_keys=['control_master_id', 'control_table_id']
            ),
            mandatory_columns=[
                "control_master_id",
                "control_table_id",
                "control_layer",
                "control_database",
                "control_table",
                "control_field",
                "control_dataset_details",
                "control_level",
                "control_description",
                "control_algorithm_type",
                "control_threshold_min",
                "control_threshold_max",
                "control_threshold_rag_min",
                "control_threshold_rag_max",
                "control_threshold_type",
                "blocking_control_indicator"
            ]
        )

    @classmethod
    def results(cls) -> Any:
        # TODO: remove when migrating Infinity to Data Platform
        database = (
            Database.INFINITY_COMMON if config().platform == Platform.INFINITY
            else Database.DATA_QUALITY
        )
        return cls(
            table_config=DatabaseTable(
                database=database,
                table='controls_results',
                primary_keys=[
                    'control_master_id', 'control_table_id',
                    'initial_date', 'end_date', 'data_date'
                ]
            ),
            mandatory_columns=[
                'control_master_id',
                'control_table_id',
                'control_detail',
                'control_outcome',
                'control_metric_value',
                'data_date',
                'initial_date',
                'end_date'
            ]
        )

    @staticmethod
    def filter_rules(layer: Layer, table_config: DatabaseTable, framework_controls: bool) -> str:
        if framework_controls:
            return f"""
                control_database = '{table_config.database.value}' AND
                control_table = '{table_config.table}' AND
                control_layer = '{layer.value}' AND
                control_algorithm_type = '{AlgorithmType.DATA_FRAMEWORK.value}' AND
                active_control_indicator
            """
        else:
            return f"""
                control_database = '{table_config.database.value}' AND
                control_table = '{table_config.table}' AND
                control_layer = '{layer.value}' AND
                control_algorithm_type <> '{AlgorithmType.DATA_FRAMEWORK.value}' AND
                active_control_indicator
            """


@dataclass
class ControlOutcome:
    total: float = 0.0
    value: float = 0.0

    @property
    def metric_value(self) -> Union[float, None]:
        if self.total is not None and self.value is not None:
            metric_value = 1.0 - (
                self.value / self.total
                if self.total != 0 else 0
            )
            return metric_value

    def to_string(self) -> str:
        return f'{{total: {self.total}, value: {self.value}}}'


@dataclass
class ThresholdResult:
    total_records: int
    invalid_records: int
    valid_identifiers: List[str]
    invalid_identifiers: List[str]

    @property
    def unique_valid_identifiers(self) -> List[str]:
        return list(set(self.valid_identifiers))

    @property
    def unique_invalid_identifiers(self) -> List[str]:
        return list(set(self.invalid_identifiers))


@dataclass
class ControlResult:
    master_id: str
    table_id: str
    outcome: ControlOutcome = field(default_factory=ControlOutcome)
    detail: str = ''
    initial_date: date = datetime.now()
    end_date: date = None
    data_date: date = date.today()

    def to_series(self) -> pd.Series:
        result = pd.Series({
            'control_master_id': self.master_id,
            'control_table_id': self.table_id,
            'control_outcome': self.outcome.to_string(),
            'control_metric_value': self.outcome.metric_value,
            'control_detail': self.detail,
            'initial_date': self.initial_date,
            'end_date': datetime.now(),
            'data_date': self.data_date,
        })
        return result

    def set_data_date(self, custom_data_date: str = None) -> None:
        if not custom_data_date:
            file_date = config().parameters.file_date
            if file_date is not None:
                self.data_date = datetime.strptime(file_date, '%Y-%m-%d')
        else:
            self.data_date = datetime.strptime(custom_data_date, '%Y-%m-%d')

    def add_detail(self, detail: str, separator: str = '. ') -> None:
        if not self.detail:
            self.detail = detail.strip()
        else:
            self.detail = self.detail + separator + detail.strip()

    def fill(self, threshold_result: ThresholdResult) -> None:
        self.outcome = ControlOutcome(
            total=threshold_result.total_records,
            value=threshold_result.invalid_records
        )


class ThresholdType(Enum):
    STANDARD = "Standard"
    ABSOLUTE = "Absolute"
    BINARY = "Binary"

    @classmethod
    def available_threshold_types(cls) -> List[str]:
        return [item.value for item in cls]


@dataclass
class ControlThreshold:
    threshold_type: str
    threshold_max: Union[float, None]
    threshold_min: Union[float, None]
    threshold_rag_min: Union[float, None]
    threshold_rag_max: Union[float, None]

    def validate(self) -> None:
        threshold_types = ThresholdType.available_threshold_types()
        if not self.threshold_type:
            raise InvalidThresholdError(error_message=(
                'Undefined threshold type. ' +
                f'Available threshold types: {", ".join(threshold_types)}'
            ))
        if self.threshold_type not in threshold_types:
            raise InvalidThresholdError(error_message=(
                f'Invalid threshold type: {self.threshold_type}. ' +
                f'Available threshold types: {", ".join(threshold_types)}'
            ))
        if (
            self.threshold_min is not None and
            self.threshold_max is not None and
            self.threshold_min > self.threshold_max
        ):
            raise InvalidThresholdError(error_message=(
                'Invalid threshold limits. Min threshold is greater than max threshold: ' +
                f'{self.threshold_min} > {self.threshold_max}'
            ))
        if (
            self.threshold_rag_min is not None and
            self.threshold_rag_max is not None and
            self.threshold_rag_min > self.threshold_rag_max
        ):
            raise InvalidThresholdError(error_message=(
                'Invalid threshold percentages. Min threshold is greater than max threshold: ' +
                f'{self.threshold_rag_min} > {self.threshold_rag_max}'
            ))
        if (
            self.threshold_rag_min is not None and not
            0.0 <= self.threshold_rag_min <= 1.0
        ):
            raise InvalidThresholdError(error_message=(
                'Invalid min threshold percentage. Must be expressed between 0.0 and 1.0: ' +
                f'{self.threshold_rag_min}'
            ))
        if (
            self.threshold_rag_max is not None and not
            0.0 <= self.threshold_rag_max <= 1.0
        ):
            raise InvalidThresholdError(error_message=(
                'Invalid max threshold percentage. Must be expressed between 0.0 and 1.0: ' +
                f'{self.threshold_rag_max}'
            ))
        if (
            self.threshold_min is not None and
            self.threshold_max is not None and
            self.threshold_type == ThresholdType.BINARY.value
        ):
            raise InvalidThresholdError(error_message=(
                f'Invalid threshold limits. {ThresholdType.BINARY.value} ' +
                'threshold does not need threshold limits'
            ))

    def apply_threshold(self, df_result: Any) -> ThresholdResult:
        if (
            df_result is None or
            'identifier' not in df_result.columns or
            'result' not in df_result.columns
        ):
            raise InvalidDataFrameError(error_message=(
                'The DataFrame with the results on which to apply the threshold ' +
                'must have the columns "identifier" and "result"'
            ))
        if self.threshold_type == ThresholdType.STANDARD.value:
            return self.calculate_standard_threshold(df_result)
        elif self.threshold_type == ThresholdType.ABSOLUTE.value:
            return self.calculate_absolute_threshold(df_result)
        elif self.threshold_type == ThresholdType.BINARY.value:
            return self.calculate_binary_threshold(df_result)

    def calculate_standard_threshold(self, df_result: Any) -> ThresholdResult:
        technology = config().current_process_config().processing_specifications.technology
        if technology == Technologies.EMR:
            from pyspark.sql.functions import col, udf
            from pyspark.sql.types import BooleanType
            # Apply threshold to each record
            udf_function = udf(self.apply_threshold_limits, BooleanType())
            df_result = df_result.withColumn('result_flag', udf_function(col('result')))
            # Records with True result
            valid_ids = df_result.filter(col('result_flag')).select('identifier').rdd.flatMap(lambda x: x).collect()
            # Records with False result
            invalid_ids = df_result.filter(~col('result_flag')).select('identifier').rdd.flatMap(lambda x: x).collect()
            # Calculate threshold
            total_records = df_result.count()
            invalid_records = len(invalid_ids)
        else:
            # Apply threshold to each record
            df_result['result_flag'] = df_result['result'].apply(self.apply_threshold_limits)
            # Records with True result
            valid_ids = df_result[df_result['result_flag']]['identifier'].tolist()
            # Records with False result
            invalid_ids = df_result[~df_result['result_flag']]['identifier'].tolist()
            # Calculate threshold
            total_records = len(df_result)
            invalid_records = len(invalid_ids)
        # Build response
        result = ThresholdResult(
            total_records=total_records,
            invalid_records=invalid_records,
            valid_identifiers=valid_ids,
            invalid_identifiers=invalid_ids
        )
        return result

    def calculate_absolute_threshold(self, df_result: Any) -> ThresholdResult:
        technology = config().current_process_config().processing_specifications.technology
        if technology == Technologies.EMR:
            from pyspark.sql.functions import col, udf, abs
            from pyspark.sql.types import BooleanType
            # Apply threshold and absolute value to each record
            udf_function = udf(self.apply_threshold_limits, BooleanType())
            df_result = df_result.withColumn('result_flag', udf_function(abs(col('result'))))
            # Records with True result
            valid_ids = df_result.filter(col('result_flag')).select('identifier').rdd.flatMap(lambda x: x).collect()
            # Records with False result
            invalid_ids = df_result.filter(~col('result_flag')).select('identifier').rdd.flatMap(lambda x: x).collect()
            # Calculate threshold
            total_records = df_result.count()
            invalid_records = len(invalid_ids)
        else:
            # Apply threshold and absolute value to each record
            df_result['result_flag'] = df_result['result'].abs().apply(self.apply_threshold_limits)
            # Records with True result
            valid_ids = df_result[df_result['result_flag']]['identifier'].tolist()
            # Records with False result
            invalid_ids = df_result[~df_result['result_flag']]['identifier'].tolist()
            # Calculate threshold
            total_records = len(df_result)
            invalid_records = len(invalid_ids)
        # Build response
        result = ThresholdResult(
            total_records=total_records,
            invalid_records=invalid_records,
            valid_identifiers=valid_ids,
            invalid_identifiers=invalid_ids
        )
        return result

    def calculate_binary_threshold(self, df_result: Any) -> ThresholdResult:
        technology = config().current_process_config().processing_specifications.technology
        if technology == Technologies.EMR:
            from pyspark.sql.functions import col
            # Records with True result
            valid_ids = df_result.filter(col('result')).select('identifier').rdd.flatMap(lambda x: x).collect()
            # Records with False result
            invalid_ids = df_result.filter(~col('result')).select('identifier').rdd.flatMap(lambda x: x).collect()
            # Calculate threshold
            total_records = df_result.count()
            invalid_records = len(invalid_ids)
        else:
            # Records with True result
            valid_ids = df_result[df_result['result']]['identifier'].tolist()
            # Records with False result
            invalid_ids = df_result[~df_result['result']]['identifier'].tolist()
            # Calculate threshold
            total_records = len(df_result)
            invalid_records = len(invalid_ids)
        # Build response
        result = ThresholdResult(
            total_records=total_records,
            invalid_records=invalid_records,
            valid_identifiers=valid_ids,
            invalid_identifiers=invalid_ids
        )
        return result

    def apply_threshold_limits(self, value: float) -> bool:
        if self.threshold_min is not None and self.threshold_max is not None:
            return (self.threshold_min <= value <= self.threshold_max)
        elif self.threshold_min is not None:
            return (self.threshold_min <= value)
        elif self.threshold_max is not None:
            return (value <= self.threshold_max)
        else:
            return True


class AlgorithmType(Enum):
    PYTHON = "Python"
    SQL = "SQL"
    REGEX = "Regex"
    DATA_FRAMEWORK = "Data Framework"

    @classmethod
    def available_algorithm_types(cls) -> List[str]:
        return [item.value for item in cls]


class ControlLevel(Enum):
    DATA = "Data"
    FILE = "File"

    @classmethod
    def available_control_levels(cls) -> List[str]:
        return [item.value for item in cls]


@dataclass
class ControlAlgorithm:
    algorithm_type: str
    algorithm_description: str

    def validate(self) -> None:
        algorithm_types = AlgorithmType.available_algorithm_types()
        if not self.algorithm_type:
            raise InvalidAlgorithmError(error_message=(
                'Undefined algorithm type. ' +
                f'Available algorithm types: {", ".join(algorithm_types)}'
            ))
        if self.algorithm_type not in algorithm_types:
            raise InvalidAlgorithmError(error_message=(
                f'Invalid algorithm type: {self.algorithm_type}. ' +
                f'Available algorithm types: {", ".join(algorithm_types)}'
            ))
        if not self.algorithm_description:
            raise InvalidAlgorithmError(error_message='Undefined algorithm description')


@dataclass
class ControlRule:
    master_id: str
    table_id: str
    layer: str
    database_table: DatabaseTable
    field: str
    rule_description: str
    level: str
    algorithm: ControlAlgorithm
    threshold: ControlThreshold
    is_blocker: bool
    result: ControlResult

    @property
    def id(self) -> str:
        return f"{self.master_id}:{self.table_id}"

    @property
    def field_list(self) -> List[str]:
        return [column.strip() for column in self.field.split(',')]

    @classmethod
    def from_series(cls, rule: pd.Series) -> Any:
        database_table = config().current_process_config().search_table_config(
            database=rule['control_database'],
            table=rule['control_table']
        )

        if database_table is None:
            database_table = DatabaseTable(
                database=rule['control_database'],
                table=rule['control_table']
            )

        return cls(
            master_id=rule['control_master_id'],
            table_id=rule['control_table_id'],
            layer=rule['control_layer'],
            database_table=database_table,
            field=rule['control_field'],
            rule_description=rule['control_dataset_details'],
            level=rule['control_level'],
            algorithm=ControlAlgorithm(
                algorithm_type=rule['control_algorithm_type'],
                algorithm_description=rule['control_description']
            ),
            threshold=ControlThreshold(
                threshold_type=rule['control_threshold_type'],
                threshold_max=(
                    None if pd.isna(rule['control_threshold_max'])
                    else rule['control_threshold_max']
                ),
                threshold_min=(
                    None if pd.isna(rule['control_threshold_min'])
                    else rule['control_threshold_min']
                ),
                threshold_rag_min=(
                    None if pd.isna(rule['control_threshold_rag_min'])
                    else rule['control_threshold_rag_min']
                ),
                threshold_rag_max=(
                    None if pd.isna(rule['control_threshold_rag_max'])
                    else rule['control_threshold_rag_max']
                )
            ),
            is_blocker=rule['blocking_control_indicator'],
            result=ControlResult(
                master_id=rule['control_master_id'],
                table_id=rule['control_table_id']
            )
        )

    def validate(self) -> None:
        # TODO: add more validations
        self.threshold.validate()
        self.algorithm.validate()
        control_levels = ControlLevel.available_control_levels()
        if not self.level:
            raise InvalidRuleError(error_message=(
                'Undefined control level. ' +
                f'Available control levels: {", ".join(control_levels)}'
            ))
        if self.level not in control_levels:
            raise InvalidRuleError(error_message=(
                f'Invalid control level: {self.level}. ' +
                f'Available control levels: {", ".join(control_levels)}'
            ))

    def calculate_result(self, df_result: Any) -> ThresholdResult:
        threshold_result = self.threshold.apply_threshold(df_result)
        self.result.fill(threshold_result)
        return threshold_result


class InterfaceQualityControls(ABC):

    @abstractmethod
    def validate(
        self,
        layer: Layer,
        table_config: DatabaseTable,
        df_data: Any = None,
        framework_controls: bool = False,
        **kwargs: Dict[str, Any]
    ) -> ControlsResponse:
        pass

    @abstractmethod
    def set_parent(self, parent: Any) -> None:
        pass

```


---
## File: ./src/data_framework/tests/test_utils.py
```python
import pytest
from data_framework.modules.utils.logger import Logger

def test_logger():
    logger = Logger()
    print("dsds")
    assert -1 == "Negative"

```


---
## File: ./src/launchers/emr_launcher.py
```python
from data_framework.modules.exception.generic_exceptions import DataFrameworkError
from importlib import import_module
import sys


class Launcher:

    def main(self, dataflow: str, process: str):

        common_module_name = f'transformation.dataflow.default.{process}'
        module_name = f'transformation.dataflow.{dataflow}.{process}'
        if process == 'landing_to_raw':
            module_name = 'data_framework.dataflow.landing'
        elif process == 'business_to_output':
            module_name = 'data_framework.dataflow.output'
        self._execute(module_name=module_name, default_module_name=common_module_name)

    def get_parameters(self) -> dict:
        parameters = {}
        for parameter_index in range(1, len(sys.argv), 2):
            key = sys.argv[parameter_index].replace('--', '').replace('-', '_')
            value = sys.argv[parameter_index+1]
            parameters[key] = value
        return parameters

    def _execute(self, module_name: str, default_module_name: str):

        class_name = 'ProcessingCoordinator'

        try:
            module = import_module(module_name)
        except ModuleNotFoundError:
            module = import_module(default_module_name)

        try:
            _class = getattr(module, class_name)
        except AttributeError:
            print(f'Class {class_name} not found in {module.__name__}')

        try:
            _instance_class = _class()
            _instance_class.process()
            _instance_class.save_monitorization()
            _instance_class.save_payload_response()
        except DataFrameworkError as e:
            print(e.format_exception())
            raise e
        except Exception as e:
            # Unhandled exception
            e = DataFrameworkError(str(e))
            print(e.format_exception())
            raise e


if __name__ == '__main__':

    launcher = Launcher()
    parameters = launcher.get_parameters()
    dataflow = parameters.get('dataflow')
    process = parameters.get('process')
    launcher.main(dataflow, process)

```


---
## File: ./tools/init.py
```python
import os

for root, dirs, files in os.walk('.\\data_framework\\src\\'):
    if '__init__.py' not in files:
        init_file = os.path.join(root, '__init__.py')
        open(init_file, 'a').close()
```
