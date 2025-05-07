# Dataplatform Framework Scripts


## Table of Contents

* Setup
    * [setup.py](#setuppy)
* Source Code
  * The Data Framework
    * [Dataflows](#the-data-framework-dataflows)
      * [business.py](#srcdata_frameworkdataflowbusinesspy)
      * [common.py](#srcdata_frameworkdataflowcommonpy)
      * [landing.py](#srcdata_frameworkdataflowlandingpy)
      * [output.py](#srcdata_frameworkdataflowoutputpy)
      * [staging.py](#srcdata_frameworkdataflowstagingpy)
    * modules
      * catalogue
        * [core_catalogue.py](#srcdata_frameworkmodulescataloguecore_cataloguepy)
        * integrations
          * [aws_glue.py](#srcdata_frameworkmodulescatalogueintegrationsaws_gluepy)
        * [interface_catalogue.py](#srcdata_frameworkmodulescatalogueinterface_cataloguepy)
      * code
        * [lazy_class_property.py](#srcdata_frameworkmodulescodelazy_class_propertypy)
      * config
        * [core.py](#srcdata_frameworkmodulesconfigcorepy)
        * model
          * [flows.py](#srcdata_frameworkmodulesconfigmodelflowspy)
      * data_process
        * [core_data_process.py](#srcdata_frameworkmodulesdata_processcore_data_processpy)
        * helpers
          * [athena.py](#srcdata_frameworkmodulesdata_processhelpersathenapy)
          * [cast.py](#srcdata_frameworkmodulesdata_processhelperscastpy)
        * integrations
          * pandas
            * [pandas_data_process.py](#srcdata_frameworkmodulesdata_processintegrationspandaspandas_data_processpy)
          * spark
            * [dynamic_config.py](#srcdata_frameworkmodulesdata_processintegrationssparkdynamic_configpy)
            * [spark_data_process.py](#srcdata_frameworkmodulesdata_processintegrationssparkspark_data_processpy)
            * transformations
              * [parse_dates.py](#srcdata_frameworkmodulesdata_processintegrationssparktransformationsparse_datespy)
            * [utils.py](#srcdata_frameworkmodulesdata_processintegrationssparkutilspy)
        * [interface_data_process.py](#srcdata_frameworkmodulesdata_processinterface_data_processpy)
      * dataflow
        * [interface_dataflow.py](#srcdata_frameworkmodulesdataflowinterface_dataflowpy)
      * exception
        * [aws_exceptions.py](#srcdata_frameworkmodulesexceptionaws_exceptionspy)
        * [catalogue_exceptions.py](#srcdata_frameworkmodulesexceptioncatalogue_exceptionspy)
        * [config_exceptions.py](#srcdata_frameworkmodulesexceptionconfig_exceptionspy)
        * [data_process_exceptions.py](#srcdata_frameworkmodulesexceptiondata_process_exceptionspy)
        * [dataflow_exceptions.py](#srcdata_frameworkmodulesexceptiondataflow_exceptionspy)
        * [generic_exceptions.py](#srcdata_frameworkmodulesexceptiongeneric_exceptionspy)
        * [landing_exceptions.py](#srcdata_frameworkmodulesexceptionlanding_exceptionspy)
        * [notification_exceptions.py](#srcdata_frameworkmodulesexceptionnotification_exceptionspy)
        * [output_exceptions.py](#srcdata_frameworkmodulesexceptionoutput_exceptionspy)
        * [storage_exceptions.py](#srcdata_frameworkmodulesexceptionstorage_exceptionspy)
        * [validation_exceptions.py](#srcdata_frameworkmodulesexceptionvalidation_exceptionspy)
      * monitoring
        * [core_monitoring.py](#srcdata_frameworkmodulesmonitoringcore_monitoringpy)
        * integrations
          * aws_cloudwatch
            * [aws_cloudwatch_monitoring.py](#srcdata_frameworkmodulesmonitoringintegrationsaws_cloudwatchaws_cloudwatch_monitoringpy)
        * [interface_monitoring.py](#srcdata_frameworkmodulesmonitoringinterface_monitoringpy)
      * notification
        * [core_notifications.py](#srcdata_frameworkmodulesnotificationcore_notificationspy)
        * [interface_notifications.py](#srcdata_frameworkmodulesnotificationinterface_notificationspy)
        * [notifications.py](#srcdata_frameworkmodulesnotificationnotificationspy)
      * storage
        * [core_storage.py](#srcdata_frameworkmodulesstoragecore_storagepy)
        * integrations
          * [local_storage.py](#srcdata_frameworkmodulesstorageintegrationslocal_storagepy)
          * [s3_storage.py](#srcdata_frameworkmodulesstorageintegrationss3_storagepy)
        * [interface_storage.py](#srcdata_frameworkmodulesstorageinterface_storagepy)
      * utils
        * [debug.py](#srcdata_frameworkmodulesutilsdebugpy)
        * [logger.py](#srcdata_frameworkmodulesutilsloggerpy)
        * [regex.py](#srcdata_frameworkmodulesutilsregexpy)
      * validation
        * [core_quality_controls.py](#srcdata_frameworkmodulesvalidationcore_quality_controlspy)
        * integrations
          * [file_validator.py](#srcdata_frameworkmodulesvalidationintegrationsfile_validatorpy)
          * [quality_controls.py](#srcdata_frameworkmodulesvalidationintegrationsquality_controlspy)
        * [interface_quality_controls.py](#srcdata_frameworkmodulesvalidationinterface_quality_controlspy)
      * tests
        * resources
          * configs
        * [test_utils.py](#srcdata_frameworkteststest_utilspy)
  * launchers
    * [emr_launcher.py](#srclaunchersemr_launcherpy)
* [tools/init.py](#toolsinitpy)


---

# Set up
## setup.py
```python
"""
Setup script for the Data Platform Framework Python package.

This script uses setuptools to package the data_framework module.
It specifies metadata such as name, version, description, author, and licensing.
It also defines package discovery rules and Python version requirements.
"""

from setuptools import setup, find_packages

# Configure the setup using setuptools
setup(
    name='data_framework',  # Package name
    version='1',  # Initial version
    description='Sample',  # Short description of the package
    license="MIT",  # License type
    author='WMI',  # Author name
    author_email='sample@sample',  # Author email address
    python_requires='>=3.9',  # Minimum Python version requirement
    packages=find_packages(
        where='src',  # Look for packages inside the 'src' directory
        exclude=['tests', 'tests.*']  # Exclude test packages from the build
    ),
    package_dir={'': 'src'},  # Map packages so 'src' is treated as root
    include_package_data=True  # Include non-code files specified in MANIFEST.in
)

```

---

# The Data Framework Source Code

## The Data Framework: Dataflows.
#### Summary
This component provides the concrete implementations for various standard dataflow processes within the Data Framework. It includes specific logic for handling data movement and transformation between different layers: Landing to Raw (`landing.py`), Raw to Staging (`staging.py`), Staging to Common (`common.py`), Staging/Common to Business (`business.py`), and Business to Output (`output.py`). Each implementation builds upon the common structure and utilities defined by `DataFlowInterface`.

#### What the "dataflow" component does (big picture)
- **Goal:** To define the specific, executable logic for each distinct stage of data processing within the framework's defined architecture (e.g., ingestion, staging, business logic application, output generation).
- **Why it matters:** It encapsulates the unique tasks required at each data layer (like file validation in Landing, transformation logic in Staging/Business, formatting in Output), making the overall data pipeline modular and maintainable.
- **How:** Each script defines one or more classes (e.g., `ProcessingCoordinator`, `StagingToBusiness`, `RawToStaging`) that inherit from `DataFlowInterface` (located in ``). These classes leverage the inherited properties (access to config, logger, core modules) and helper methods. Key scripts like `landing.py` and `output.py` implement detailed logic within their `process()` methods, covering tasks like file handling, validation, deduplication, data retrieval, format conversion (XML/Excel/JSON to Parquet/JSON Lines), writing data via storage modules, and sending notifications. Other scripts (`business.py`, `common.py`, `staging.py`) currently define the class structure inheriting from the interface, implying their specific process logic would be added later.

#### dataflow Component High-Level: Script by Script

| Script                                | What it is        | What it does |
| :------------------------------------ | :---------------- | :----------- |
| `business.py`                         | Dataflow Implementation | Defines classes (`StagingToBusiness`, `CommonToBusiness`) representing dataflows targeting the Business layer. Currently inherits structure from `DataFlowInterface`. |
| `common.py`                           | Dataflow Implementation | Defines a class (`StagingToCommon`) representing the dataflow from Staging to the Common layer. Currently inherits structure from `DataFlowInterface`. |
| `landing.py`                          | Dataflow Implementation | Implements `ProcessingCoordinator` for the Landing-to-Raw process. Handles file reading (incl. archives), validation, date extraction, deduplication, partitioning, format normalization (XML/Excel/JSON), and writing to the Raw layer. |
| `output.py`                           | Dataflow Implementation | Implements `ProcessingCoordinator` for generating output files. Reads data based on configurations, formats (CSV/JSON), applies filters/aliases, writes to specified paths, handles errors per output, and sends notifications. |
| `staging.py`                          | Dataflow Implementation | Defines a class (`RawToStaging`) representing the dataflow from the Raw layer to the Staging layer. Currently inherits structure from `DataFlowInterface`. |


### Dataflows: landing
#### src/data_framework/dataflow/landing.py
#### `landing.py`
**Purpose:**
This script implements the core logic for ingesting data from the Landing layer into the Raw layer. It acts as the entry point for new data, performing crucial steps like reading files, validating their structure and content, checking for duplicates against previous runs, handling different file formats and compressions, and finally writing the validated, potentially normalized data to the Raw storage layer with appropriate partitioning.

**Key parts:**
- **`ProcessingCoordinator(DataFlowInterface)` class:** Orchestrates the entire landing process.
    - **`__init__(self)`:** Initializes base class and helpers like `Storage`, `CoreCatalogue`.
    - **`process(self)`:** Main entry point. Handles different execution modes (`DELTA` for single file, `ON_DEMAND` iterates through files matching a pattern under a prefix). Calls `process_file` for each valid file.
    - **`process_file(self)`:** Orchestrates the processing of a single file: reads, validates, checks for duplicates, creates partitions, and writes. Manages payload response state (`success`, `next_stage`). Sends `file_arrival` notification. Uses `FileValidator` and `CoreQualityControls`.
    - **`read_data(self)`:** Reads file content from Landing storage using `Storage.read`. Handles plain files and decompression for ZIP (`ZipFile`) and TAR (`tarfile`) archives, returning a dictionary of file contents.
    - **`obtain_file_date(self)`:** Extracts a date string from the filename using regex patterns defined in the configuration (`incoming_file.specifications`). Handles named or unnamed regex groups. Raises errors for pattern mismatches or missing groups. `DateLocated.COLUMN` is noted as not implemented.
    - **`compare_with_previous_file(self, file_contents: dict)`:** Checks if the current file is identical to the last successfully processed file with the same base name by comparing MD5 hashes (`get_file_hash`). Uses `get_last_processed_file_key` to find the previous file. Returns `False` if identical (to skip processing), `True` otherwise.
    - **`get_last_processed_file_key(self, ...)`:** Finds the S3 key of the most recent previously processed file matching the incoming filename by parsing date/time from the key path.
    - **`get_file_hash(self, file_content: BytesIO, ...)`:** Calculates the MD5 hash of a file's content.
    - **`create_partitions(self, file_date: str)`:** Creates partitions in the Glue Data Catalog for the Raw table using `CoreCatalogue.create_partition` based on the extracted `file_date`.
    - **`write_data(self, file_contents: dict, ...)`:** Writes the validated and potentially normalized file content(s) to the Raw layer using `Storage.write`, applying the created partitions. Calls `normalize_file_content`.
    - **`normalize_file_content(self, ...)`:** Converts input file content if necessary (XML/Excel to Parquet, JSON to JSON Lines) before writing. Calls specific conversion methods.
    - **`convert_xml_to_parquet(self, ...)`:** Converts XML content to a Parquet file in memory using `pandas.read_xml` and `df.to_parquet`.
    - **`convert_excel_to_parquet(self, ...)`:** Converts Excel content to a Parquet file in memory using `pandas.read_excel` and `df.to_parquet`.
    - **`convert_json_to_json_lines(self, ...)`:** Converts standard JSON (list or dict) into JSON Lines format, ensuring the partition field is added to each line.

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


### Dataflows: Staging
#### src/data_framework/dataflow/staging.py
#### `staging.py`
**Purpose:**
This script defines the dataflow class responsible for processing data from the Raw layer into the Staging layer of the framework. It provides a placeholder for logic that typically involves cleaning, standardizing, and possibly enriching the raw data before it moves further into the Common or Business layers.

**Key parts:**
- **`RawToStaging(DataFlowInterface)` class:** Represents the process of transforming data from the Raw layer to the Staging layer. Inherits from `DataFlowInterface`. Currently only calls the parent `__init__`. The specific `process()` logic (likely involving reading from Raw, applying transformations/validations, and writing to Staging using `CoreDataProcess`) is expected to be implemented here.
```python
# staging.py
"""
Defines the dataflow process from the Raw layer to the Staging layer
within the Data Platform Framework.

This module implements a ProcessingCoordinator class responsible
for coordinating the movement and preparation of data at the staging level.
"""

from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface


class RawToStaging(DataFlowInterface):
    """
    Coordinates the dataflow from the Raw layer to the Staging layer.

    Inherits shared functionality from DataFlowInterface.
    """

    def __init__(self):
        """
        Initialize RawToStaging by invoking the base class constructor.
        """
        super().__init__()  # Initialize the parent DataFlowInterface

```

---


### Dataflows: common
#### src/data_framework/dataflow/common.py
#### `common.py`
**Purpose:**
This script defines the dataflow class responsible for processing data into the Common layer of the framework. It provides a placeholder for logic that transforms data from the Staging layer into a standardized, common format.

**Key parts:**
- **`StagingToCommon(DataFlowInterface)` class:** Represents the process of transforming data from the Staging layer to the Common layer. Inherits from `DataFlowInterface`. Currently only calls the parent `__init__`. The specific `process()` logic is expected to be implemented here.

```python

# common.py
"""
Defines the common-level dataflow process for the Data Platform Framework.

This module implements a ProcessingCoordinator class responsible
for handling data transitions from staging to common layers.
"""

from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface


class StagingToCommon(DataFlowInterface):
    """
    Coordinates the dataflow from the Staging layer to the Common layer.

    Inherits shared functionality from DataFlowInterface.
    """

    def __init__(self):
        """
        Initialize StagingToCommon by invoking the base class constructor.
        """
        super().__init__()  # Initialize the parent DataFlowInterface


```


### Dataflows: business
#### src/data_framework/dataflow/business.py
#### `business.py`
**Purpose:**
This script defines the dataflow classes responsible for processing data into the Business layer of the framework. It provides placeholders for logic that transforms data from Staging or Common layers into business-specific views or aggregates.

**Key parts:**
- **`StagingToBusiness(DataFlowInterface)` class:** Represents the process of transforming data from the Staging layer to the Business layer. Inherits from `DataFlowInterface`. Currently only calls the parent `__init__`. The specific `process()` logic is expected to be implemented here.
- **`CommonToBusiness(DataFlowInterface)` class:** Represents the process of transforming data from the Common layer to the Business layer. Inherits from `DataFlowInterface`. Currently only calls the parent `__init__`. The specific `process()` logic is expected to be implemented here.

```python

# business.py
"""
Defines the business-level dataflow processes for the Data Platform Framework.

This module implements specific ProcessingCoordinator classes responsible
for handling data transitions from staging to business and common to business layers.
"""

from data_framework.modules.dataflow.interface_dataflow import DataFlowInterface


class StagingToBusiness(DataFlowInterface):
    """
    Coordinates the dataflow from the Staging layer to the Business layer.

    Inherits shared functionality from DataFlowInterface.
    """

    def __init__(self):
        """
        Initialize StagingToBusiness by invoking the base class constructor.
        """
        super().__init__()  # Initialize the parent DataFlowInterface


class CommonToBusiness(DataFlowInterface):
    """
    Coordinates the dataflow from the Common layer to the Business layer.

    Inherits shared functionality from DataFlowInterface.
    """

    def __init__(self):
        """
        Initialize CommonToBusiness by invoking the base class constructor.
        """
        super().__init__()  # Initialize the parent DataFlowInterface


```

### Dataflows: output
#### src/data_framework/dataflow/output.py
#### `output.py`
**Purpose:**
This script implements the logic for generating final output files based on data residing typically in the Business layer. It reads data according to defined report configurations, formats it (CSV or JSON), applies transformations or filters, writes the formatted data to the Output storage layer, and handles notifications and error reporting for each generated output.

**Key parts:**
- **`ProcessingCoordinator(DataFlowInterface)` class:** Orchestrates the output generation process.
    - **`__init__(self)`:** Initializes base class and `Storage`.
    - **`process(self)`:** Main entry point. Iterates through all `OutputReport` configurations defined in `self.output_reports`. Calls `generate_output_file` for each, captures individual successes/failures in `self.payload_response.outputs`, and raises an `OutputError` if any output fails.
    - **`generate_output_file(self, config_output: OutputReport)`:** Handles the generation of a single output file. Calls `retrieve_data` and `write_data_to_file`. Raises `NoOutputDataError` if retrieved data is empty.
    - **`retrieve_data(self, config_output: OutputReport)`:** Reads data for the output using `CoreDataProcess.read_table`. Constructs column list with aliases if provided (`config_output.columns_alias`). Applies filters (`config_output.where`) after formatting them using `format_string`.
    - **`write_data_to_file(self, df: DataFrame, config_output: OutputReport)`:** Writes the DataFrame (`df`) to the specified output path.
        - Constructs the filename using `format_string`.
        - Normalizes the output folder name using `parse_output_folder`.
        - Determines the full S3 path.
        - Formats data to CSV (`df.toPandas().to_csv`) or JSON (`df.write.json` to temp location, read back, apply replaces, write final) based on `config_output` specs.
        - Writes the final content using `Storage.write_to_path`.
        - Sends `output_generated` notification on success.
    - **`parse_output_folder(output_folder: str)` (staticmethod):** Cleans and normalizes a string to be used as a folder name (lowercase, underscore spaces, remove special chars).
    - **`format_string(self, string_to_format: str, ...)`:** Replaces placeholders like `{file_date}`, `{file_name}`, `{current_date}` in strings (e.g., filenames, filters) with actual runtime values.

```python

# output.py
"""
Handles the generation and storage of output files in the Data Platform Framework.

This module implements the ProcessingCoordinator class responsible for:
- Reading data from source tables
- Formatting outputs (CSV, JSON)
- Writing outputs to storage
- Managing notifications on output generation
- Handling errors during output creation
"""

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
    """
    Coordinates the creation of output reports by reading data and writing formatted files.
    
    Inherits from DataFlowInterface to ensure standard pipeline execution.
    """

    def __init__(self):
        """
        Initialize ProcessingCoordinator and its components.
        """
        super().__init__()
        self.storage = Storage()

    def process(self) -> dict:
        """
        Main execution method.

        Processes all configured outputs:
        - Generates output files
        - Captures errors per output
        - Sends notifications if needed
        """
        self.payload_response.success = True

        # Iterate over each output report configuration
        for config_output in self.output_reports:
            try:
                self.generate_output_file(config_output)
            except Exception as e:
                # Capture individual output failure
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
                # Successful output generation
                output = OutputResponse(
                    name=config_output.name,
                    success=True
                )
                self.payload_response.outputs.append(output)

        # If any output failed, raise a grouped error
        if not self.payload_response.success:
            failed_outputs = self.payload_response.get_failed_outputs()
            raise OutputError(failed_outputs=failed_outputs)

    def generate_output_file(self, config_output: OutputReport) -> None:
        """
        Generate and save a single output file based on the provided configuration.
        """
        self.logger.info(f'Generating output {config_output.name}')
        
        # Retrieve data for the output
        df = self.retrieve_data(config_output)

        # Write output to storage if data is available
        if df and not df.isEmpty():
            self.write_data_to_file(df, config_output)
            self.logger.info(f'Output {config_output.name} generated successfully')
        else:
            raise NoOutputDataError(output_name=config_output.name)

    def retrieve_data(self, config_output: OutputReport) -> DataFrame:
        """
        Retrieve the data for the output report, applying filters and column aliases if specified.
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
            config_output.source_table.database_relation,
            config_output.source_table.table,
            _filter,
            columns
        )
        return response.data

    def write_data_to_file(self, df: DataFrame, config_output: OutputReport) -> None:
        """
        Write the output DataFrame to storage in the configured format (CSV or JSON).
        """
        filename = self.format_string(config_output.filename_pattern, config_output.filename_date_format)
        output_folder = self.parse_output_folder(config_output.name)
        file_path = f"{self.config.project_id}/{output_folder}/inbound/{filename}"
        self.logger.info(f"Saving output '{config_output.name}' in {file_path}")

        file_to_save = BytesIO()

        # Handle CSV output format
        if config_output.csv_specs:
            pdf = df.toPandas()
            pdf.to_csv(
                file_to_save,
                sep=config_output.csv_specs.delimiter,
                header=config_output.csv_specs.header,
                index=config_output.csv_specs.index,
                encoding=config_output.csv_specs.encoding
            )

        # Handle JSON output format
        if config_output.json_specs:
            # Write as JSON to temporary location
            response = Storage.base_layer_path(layer=Layer.OUTPUT)
            tmp_write_path = f"{response.base_path}/{self.config.project_id}/{output_folder}/tmp/"
            df.coalesce(1).write.mode("overwrite").json(path=tmp_write_path)

            # Read the JSON file back into memory
            tmp_read_path = f"{self.config.project_id}/{output_folder}/tmp/"
            response = Storage.list_files(layer=Layer.OUTPUT, prefix=tmp_read_path)
            path_output_file = next((path for path in response.result if ".json" in path), "")
            response = Storage.read(layer=Layer.OUTPUT, key_path=path_output_file)

            file = response.data

            # Apply any specified replacements inside the JSON content
            if config_output.replaces:
                file = file.decode('utf-8')
                for replace_dict in config_output.replaces:
                    for replace, new_value in replace_dict.items():
                        file = file.replace(replace, new_value)
                file = file.encode('utf-8')

            file_to_save = BytesIO(file)

        # Upload the generated file
        response = self.storage.write_to_path(Layer.OUTPUT, file_path, file_to_save.getvalue())
        if response.success:
            # Send notification of successful output generation
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
        """
        Normalize the output folder name by:
        - Lowercasing
        - Replacing spaces with underscores
        - Removing non-alphabetical characters
        """
        return re.sub(
            r'\s+', '_',
            re.sub(
                r'[^a-z\s]', '',
                output_folder.lower().strip()
            )
        )

    def format_string(self, string_to_format: str, date_format: str = '%Y-%m-%d') -> str:
        """
        Format a string by replacing placeholders with runtime variables such as
        file date, file name, or current date.
        """
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

## The Data Framework: Modules.

In this framework, `modules` is a **library of reusable, pluggable building blocks**.  
Each module provides logic for one specific responsibility in a data pipeline — like reading from storage, interacting with AWS Glue, transforming data with Spark, validating outputs, or sending monitoring metrics.

They are grouped by domain (catalogue, data_process, storage, monitoring, etc.) and implement patterns like:

- Interfaces (to enforce consistent APIs)

- Core logic (to execute functionality)

- Integrations (to connect to real systems like AWS or Spark)

### DF Modules: Catalogue.
#### Summary

The catalogue module is responsible for managing metadata: reading table schemas and creating partitions dynamically. It hides the complexity of AWS Glue behind clean interfaces and easy-to-use static methods.

#### What the "catalogue" module does (big picture)

- **Goal:** The `catalogue` module abstracts the interaction with the metadata store — specifically AWS Glue.
- **Why it matters:** Data pipelines need to know the schema of tables (what columns exist, types, partition keys) and register new partitions when new data arrives.
- **How:** 
  - The interface defines what operations a catalogue must have.
  - The core code provides a standard way to access catalogue functionality.
  - The AWS Glue integration implements how to actually talk to AWS Glue.

The catalogue module acts as the brain that knows how datasets are organized.

#### Catalogue Module High-Level: Script by Script

| Script | What it is | What it does |
|:---|:---|:---|
| `interface_catalogue.py` | Interface and models | Defines the expected behaviors for any catalogue system (create partition, get schema) and provides data models like `Column`, `Schema`, and response wrappers. |
| `core_catalogue.py` | Core access point | Provides a simple, static API (`create_partition`, `get_schema`) that hides the complexity of which backend (AWS Glue) is being used. |
| `integrations/aws_glue.py` | AWS Glue implementation | Implements how to interact with AWS Glue: <br> - `create_partition`: Adds new partition values to tables. <br> - `get_schema`: Reads table schemas (columns, types, partition info) from Glue. |

---

#### src/data_framework/modules/catalogue/core_catalogue.py
#### `core_catalogue.py`

**Purpose:**  
Provide a standardized, lazy-loading API to the catalogue functionality.

**Key parts:**
- `LazyClassProperty`: defers the actual heavy loading of the AWS Glue connection until you need it.
- `_catalogue`: internally holds the AWS Glue client.
- `create_partition(database, table, partition_field, value)`: 
  - Calls the backend to create a new partition if it does not exist.
- `get_schema(database, table)`:
  - Fetches and returns the table’s schema (columns, types, etc.).

The rest of the code does not have to know it is AWS Glue — it just uses `CoreCatalogue`.

```python
# core_catalogue.py
"""
Core access point to the metadata catalogue functionality for the Data Platform Framework.

This module provides static methods for:
- Creating partitions dynamically in the metadata store
- Retrieving table schemas

It uses lazy-loading to initialize the underlying catalogue integration (e.g., AWS Glue).
"""

from data_framework.modules.code.lazy_class_property import LazyClassProperty
from data_framework.modules.catalogue.interface_catalogue import (
    CatalogueInterface,
    SchemaResponse,
    GenericResponse
)
from typing import Union


class CoreCatalogue:
    """
    Provides a simple static API for interacting with the metadata catalogue.

    Internally, uses the AWS Glue integration but hides the implementation
    from the external users.
    """

    @LazyClassProperty
    def _catalogue(cls) -> CatalogueInterface:
        """
        Lazy-load the backend catalogue integration.

        Returns:
            CatalogueInterface: An instance of the configured catalogue backend.
        """
        from data_framework.modules.catalogue.integrations.aws_glue import CatalogueAWSGlue
        return CatalogueAWSGlue()

    @staticmethod
    def create_partition(
        database: str,
        table: str,
        partition_field: str,
        value: Union[str, int]
    ) -> GenericResponse:
        """
        Create a new partition for a table in the metadata store.

        Args:
            database (str): Database name.
            table (str): Table name.
            partition_field (str): Name of the partition field.
            value (Union[str, int]): Value for the partition.

        Returns:
            GenericResponse: Success or failure of the operation.
        """
        return CoreCatalogue._catalogue.create_partition(
            database,
            table,
            partition_field,
            value
        )

    @staticmethod
    def get_schema(database: str, table: str) -> SchemaResponse:
        """
        Fetch the schema of a table from the metadata store.

        Args:
            database (str): Database name.
            table (str): Table name.

        Returns:
            SchemaResponse: Table columns and types.
        """
        return CoreCatalogue._catalogue.get_schema(
            database,
            table
        )


```

#### src/data_framework/modules/catalogue/integrations/aws_glue.py
#### `integrations/aws_glue.py`

**Purpose:**  
Concrete implementation of `CatalogueInterface` using AWS Glue.

**Key parts:**
- `__init__`: prepares a boto3 client, logger, config, and a cache.
- `create_partition(database, table, partition_field, value)`:
  - Checks if a partition already exists.
  - If not, builds a description and creates it in AWS Glue.
- `get_schema(database, table)`:
  - Gets the table's columns and partition keys from Glue.
  - Caches the result for performance.
- `_get_glue_table`: Helper method to call AWS Glue's `get_table` API.

**Error handling:**  
Uses custom exceptions like `GlueError`, `CreatePartitionError`, and `SchemaError` to provide clear, specific errors when something goes wrong.

```python
# aws_glue.py
"""
AWS Glue integration for the metadata catalogue in the Data Platform Framework.

This module implements how to:
- Create partitions dynamically in AWS Glue tables.
- Retrieve table schemas from AWS Glue.
- Handle errors related to AWS Glue interactions.
"""

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
    """
    AWS Glue implementation of the CatalogueInterface.
    """

    def __init__(self):
        """
        Initialize the AWS Glue client, logger, config, and internal cache.
        """
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
        Create a new partition in an AWS Glue table.

        Args:
            database (str): Database name.
            table (str): Table name.
            partition_field (str): Name of partition field.
            partition_value (Union[str, int]): Value to insert.

        Returns:
            GenericResponse: Success or failure response.
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
                    # Create a new partition description
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
        Fetch the table schema (columns and partitions) from AWS Glue.

        Args:
            database (str): Database name.
            table (str): Table name.

        Returns:
            SchemaResponse: Schema object containing columns metadata.
        """
        try:
            cache_key = f'schema.{database}.{table}'
            if cache_key in self.cache:
                return self.cache.get(cache_key)

            table_gl = self._get_glue_table(database, table)

            # Read normal columns
            l_columns = table_gl['Table']['StorageDescriptor']['Columns']
            l_names = [column['Name'] for column in l_columns]
            l_types = [column['Type'] for column in l_columns]
            l_ispartitioned = [False for _ in l_columns]

            # Read partition columns
            if table_gl['Table'].get('PartitionKeys'):
                l_partition_keys = table_gl['Table']['PartitionKeys']
                l_partition_keys_names = [column['Name'] for column in l_partition_keys]
                l_partition_keys_types = [column['Type'] for column in l_partition_keys]
                l_partition_keys_ispartitioned = [True for _ in l_partition_keys]

                l_names.extend(l_partition_keys_names)
                l_types.extend(l_partition_keys_types)
                l_ispartitioned.extend(l_partition_keys_ispartitioned)

            n_cols = len(l_names)
            l_order = [number + 1 for number in range(n_cols)]
            l_schema_zip = list(zip(l_names, l_types, l_order, l_ispartitioned))
            l_schema = [Column(elem[0], elem[1], elem[2], elem[3]) for elem in l_schema_zip]

            schema = Schema(columns=l_schema)
            response = SchemaResponse(success=True, error=None, schema=schema)
            self.cache[cache_key] = response
            return response
        except Exception:
            raise SchemaError(database=database, table=table)

    def _get_glue_table(self, database: str, table: str) -> Any:
        """
        Internal helper to retrieve table metadata from AWS Glue.

        Args:
            database (str): Database name.
            table (str): Table name.

        Returns:
            dict: Glue API response containing the table definition.
        """
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
#### src/data_framework/modules/catalogue/interface_catalogue.py
#### `interface_catalogue.py`

**Purpose:**  
Define the blueprint for catalogue systems and basic data structures.

**Key parts:**
- `Column`: describes one column (name, type, order, if partitioned).
- `Schema`: holds a list of `Column` objects and provides helpers to extract information.
- `SchemaResponse` and `GenericResponse`: simple success/error wrappers.
- `CatalogueInterface`: abstract base class that says:
  - A catalogue must know how to `create_partition`.
  - A catalogue must know how to `get_schema`.

```python
# interface_catalogue.py
"""
Defines the abstract base interface and basic models for catalogue systems.

This module specifies:
- The expected API for any catalogue (e.g., AWS Glue, other future catalogues).
- Data models to represent schema and column metadata.
"""

from abc import ABC, abstractmethod
from typing import Union, Optional, List, Dict
from dataclasses import dataclass


@dataclass
class Column:
    """
    Model representing a single column in a table schema.
    """
    name: str
    type: str
    order: int
    ispartitioned: bool


@dataclass
class Schema:
    """
    Model representing a full table schema with columns and partitions.
    """
    columns: Column

    def get_column_names(self, partitioned: bool = False) -> List[str]:
        """
        Get a list of column names, optionally excluding partitions.
        """
        if partitioned:
            return [column.name for column in self.columns]
        else:
            return [column.name for column in self.columns if not column.ispartitioned]

    def get_type_columns(self, partitioned: bool = False) -> List[str]:
        """
        Get a list of column types, optionally excluding partitions.
        """
        if partitioned:
            return [column.type for column in self.columns]
        else:
            return [column.type for column in self.columns if not column.ispartitioned]

    def get_column_type_mapping(self, partitioned: bool = False) -> Dict[str, str]:
        """
        Get a mapping of column name to type, optionally excluding partitions.
        """
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
    """
    Standard response for schema retrieval operations.
    """
    success: bool
    error: Optional[str] = None
    schema: Optional[Schema] = None


@dataclass
class GenericResponse:
    """
    Standard generic response for catalogue operations.
    """
    success: bool
    error: Optional[str] = None


class CatalogueInterface(ABC):
    """
    Abstract base class for a metadata catalogue.

    Defines required methods like creating partitions and fetching schemas.
    """

    @abstractmethod
    def create_partition(
        self,
        database: str,
        table: str,
        partition_field: str,
        partition_value: Union[str, int]
    ) -> GenericResponse:
        pass

    @abstractmethod
    def get_schema(self, database: str, table: str) -> SchemaResponse:
        pass

```
---

### DF Modules: Code
#### src/data_framework/modules/code/lazy_class_property.py
```python
# lazy_class_property.py
"""
Provides a decorator for defining lazy-loaded class-level properties.

This utility allows expensive or late-initialized class attributes
(e.g., SparkSession, boto3 clients) to be loaded only once upon first access,
improving performance and startup times in the Data Platform Framework.
"""

class LazyClassProperty:
    """
    Descriptor that implements a lazy-loaded, cached class property.

    When accessed for the first time, the value is computed and stored
    as a private class attribute. Subsequent accesses return the cached value.
    """

    def __init__(self, func):
        """
        Initialize the LazyClassProperty with the original function.

        Args:
            func (callable): Function to compute the property value.
        """
        self.func = func
        self.attr_name = f"_{func.__name__}"  # Name to cache the computed value

    def __get__(self, instance, owner):
        """
        Retrieve the lazy property value, computing and caching it if needed.

        Args:
            instance (Any): The instance of the class (ignored for class properties).
            owner (Type): The class itself.

        Returns:
            Any: The computed or cached value of the property.
        """
        if not hasattr(owner, self.attr_name):
            # Compute and store the value the first time it's accessed
            setattr(owner, self.attr_name, self.func(owner))
        return getattr(owner, self.attr_name)

```
---

### DF Modules: Config.
#### Summary

The config module is responsible for loading, parsing, and validating all configuration files that define how data processes run. It handles environment-specific settings, platform-specific overrides, and dynamic reading from AWS services like STS and S3. It provides a consistent, typed, and validated `Config` object that is used throughout the framework.

#### What the "config" module does (big picture)

- **Goal:** The `config` module abstracts all interactions related to reading and managing configuration, ensuring a consistent runtime environment for the framework.
- **Why it matters:** Data pipelines depend heavily on dynamic parameters, environment-specific variables, and structured process flows that need to be safely loaded and interpreted.
- **How:**
  - Provides a singleton `Config` object that holds all configuration.
  - Reads configuration from local files or zipped packages.
  - Dynamically merges configurations based on the environment and platform.
  - Handles AWS account identification for environment-specific setups.

The config module ensures that the right settings are always available for the correct environment, platform, and dataflow.

#### Config Module High-Level: Script by Script

| Script | What it is | What it does |
|:---|:---|:---|
| `core.py` | Config loader and manager | Instantiates and manages the `Config` singleton. Reads configuration files, interprets CLI parameters, and merges environment-specific settings. |
| `model/flows.py` | Config data models and enums | Defines all dataclasses (e.g., `Processes`, `Parameters`, `DatabaseTable`) and enums (e.g., `Environment`, `Technologies`) that describe the structure of a full dataflow configuration. |

---

#### src/data_framework/modules/config/core.py
#### `core.py`

**Purpose:**  
Provide a thread-safe singleton for loading and managing all configuration required by the framework at runtime.

**Key parts:**
- `config()`: Function to retrieve the singleton `Config` object.
- `ConfigSetup`: Core class that initializes configuration based on CLI parameters or environment settings.
- `read_data_framework_config()`: Reads AWS account-specific configuration from `data_framework_config.json`.
- `read_dataflow_config()`: Reads specific dataflow configurations from transformation files.
- `parse_to_model()`: Recursively parses JSON data into strongly typed dataclasses.

The core.py script ensures that the entire platform has access to a clean, validated, and environment-aware configuration.

```python

# core.py
"""
Manages configuration loading and parsing for the Data Platform Framework.

This module provides a thread-safe singleton (`ConfigSetup`) that loads
runtime configuration, reads environment-specific settings, and dynamically
merges platform overrides. It ensures consistent, validated access to all
configuration settings across the framework.
"""

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
    Transformation
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
import json
import zipfile
import boto3
from enum import Enum
from pathlib import Path

T = TypeVar('T')


def config(parameters: dict = None, reset: bool = False) -> Config:
    """
    Retrieve the singleton Config object.

    Args:
        parameters (dict, optional): CLI parameters or overrides.
        reset (bool): If True, reinitialize the Config.

    Returns:
        Config: The loaded configuration.
    """
    if ConfigSetup._instancia is None or reset:
        return ConfigSetup(parameters)._instancia.config
    else:
        return ConfigSetup._instancia.config


class ConfigSetup:
    """
    Thread-safe singleton class responsible for loading and parsing configuration.

    Handles:
    - Reading framework-level config (e.g., environment, platform)
    - Reading dataflow-specific config
    - Merging dynamic overrides
    - Parsing into dataclass models
    """

    _instancia = None
    _lock = threading.Lock()
    _environment: None

    _models = (
        Processes, LandingToRaw, GenericProcess, ToOutput, CSVSpecs, XMLSpecs, JSONSpecs, IncomingFileLandingToRaw,
        DateLocatedFilename, DatabaseTable, ProcessingSpecifications,
        Hardware, SparkConfiguration,
        OutputReport, CSVSpecsReport, JSONSpecsReport,
        VolumetricExpectation, Notification, Casting, Transformation,
        DataFrameworkNotifications, NotificationsParameters
    )

    def __new__(cls, *args, **kwargs):
        if cls._instancia is None:
            with cls._lock:
                if cls._instancia is None:
                    cls._instancia = super(ConfigSetup, cls).__new__(cls)
        return cls._instancia

    def __init__(self, parameters: dict = None):
        """
        Initialize and load the complete configuration model.
        """
        try:
            # If parameters not passed explicitly, attempt to parse CLI arguments
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

            # Handle special case for INFINITY platform
            if platform == Platform.INFINITY.value and not parameters.get('local_file'):
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
                        json_file=parameters,
                        environment=data_framework_config['environment']
                    ),
                    project_id=Platform.INFINITY.value
                )
            else:
                # Normal Data Platform path
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
        """
        Load data_framework_config.json based on the current AWS account ID.

        Returns:
            dict: Config settings mapped to account.
        """
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

        config_json = cls.read_config_file(
            absolute_path='data_framework/modules/config/data_framework_config.json',
            relative_path='data_framework_config.json'
        )
        current_config = config_json.get(account_id)

        if not current_config:
            raise AccountNotFoundError(
                account_id=account_id, available_ids=list(config_json.keys())
            )
        else:
            return current_config

    @classmethod
    def read_notifications_config(cls) -> dict:
        """
        Read notification configuration (data_framework_notifications.json).
        """
        notifications_config = cls.read_config_file(
            absolute_path='data_framework/modules/notification/data_framework_notifications.json',
            relative_path='../notification/data_framework_notifications.json'
        )
        return notifications_config

    @classmethod
    def read_config_file(cls, absolute_path: str, relative_path: str) -> dict:
        """
        Read a JSON configuration file either from zip or local filesystem.

        Args:
            absolute_path (str): Path inside zip.
            relative_path (str): Path on filesystem.

        Returns:
            dict: Loaded configuration.
        """
        try:
            current_path = Path(__file__).resolve()
            if 'data_framework.zip' in current_path.parts:
                # If executing from inside a zip archive
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
        """
        Read dataflow-specific configuration from transformation files.
        """
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

        # Inject dynamic values
        current_flow_json['environment'] = environment
        current_flow_json['platform'] = platform
        current_flow_json['project_id'] = config_json.get('project_id')
        current_flow_json['data_framework_notifications'] = cls.read_notifications_config()

        return current_flow_json

    @classmethod
    def merged_current_dataflow_with_default(cls, current_dataflow: dict, default: dict) -> dict:
        """
        Recursively merge current dataflow config with defaults.
        """
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
        """
        Recursively parse a JSON configuration dictionary into a typed dataclass model.

        Handles nested models, enums, lists, optionals.
        """
        fieldtypes = get_type_hints(model)
        kwargs = {}
        model_instantiated = None

        try:
            for field, field_type in fieldtypes.items():
                default_value = None
                # Handling complex nested fields
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
                            ) if field_model == Transformation else cls.parse_to_model(
                                model=field_model,
                                json_file=field_item,
                                environment=environment
                            )
                            for field_item in json_file.get(field)
                        ]
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
#### src/data_framework/modules/config/model/flows.py
#### `model/flows.py`

**Purpose:**  
Define the complete structure of a configuration file, including all possible process types, parameters, environment options, and processing specifications.

**Key parts:**
- Enum definitions: Define controlled vocabularies for technologies, platforms, file formats, execution modes, etc.
- Dataclass models: `Processes`, `LandingToRaw`, `GenericProcess`, `ToOutput`, `DatabaseTable`, and many others represent each part of the configuration.
- Nested structures: Support for complex relationships like tables inside processes, transformations inside casting, notifications inside processes.
- Helpers: Properties like `volumetric_expectation`, `sql_where`, `database_relation` provide dynamic behavior depending on the runtime environment.

The model/flows.py script defines the expected schema and structure for all configuration files consumed by the framework.

```python

# flows.py
"""
Defines the full configuration models and enums for the Data Platform Framework.

This module structures all supported configuration options, including:
- Process flows
- Table definitions
- Landing file specs
- Technology/platform settings
- Spark dynamic tuning
- Notification wiring
"""

from data_framework.modules.storage.interface_storage import Database
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
from data_framework.modules.exception.data_process_exceptions import TransformationNotImplementedError
from dataclasses import dataclass, field, fields
from enum import Enum
from typing import Optional, List, Tuple, Union
import os


# --- Enumerations for controlled config vocabularies ---

class Environment(Enum):
    """
    Supported deployment environments.
    """
    LOCAL = "local"
    DEVELOP = "develop"
    PREPRODUCTION = "preproduction"
    PRODUCTION = "production"


class Platform(Enum):
    """
    Supported platform identifiers.
    """
    DATA_PLATFORM = "data_platform"
    INFINITY = "infinity"


class DateLocated(Enum):
    """
    Where dates are found in incoming files.
    """
    FILENAME = "filename"
    COLUMN = "column"


class Technologies(Enum):
    """
    Supported processing engines.
    """
    LAMBDA = "lambda"
    EMR = "emr"


class LandingFileFormat(Enum):
    """
    Accepted landing file formats.
    """
    CSV = "csv"
    JSON = "json"
    JSON_LINES = "json_lines"  # TODO: JSON Lines implementation
    EXCEL = "xls"
    XML = "xml"


class OutputFileFormat(Enum):
    """
    Supported output file formats.
    """
    CSV = "csv"
    JSON = "json"


class ExecutionMode(Enum):
    """
    How the pipeline executes: incremental (delta) or full.
    """
    DELTA = "delta"
    FULL = "full"

    @property
    def is_delta(self) -> bool:
        return self.value == 'delta'


class JSONSpectFormat(Enum):
    """
    Structure of exported JSON outputs.
    """
    LINES = "lines"
    COLUMNS = "columns"


class JSONFormat(Enum):
    """
    Structure of imported JSON inputs.
    """
    DICTIONARY = "dictionary"
    ARRAY = "array"


class CastingStrategy(Enum):
    """
    Strategies for casting data types during processing.
    """
    ONE_BY_ONE = "one_by_one"
    DYNAMIC = "dynamic"


class TransformationType(Enum):
    """
    Supported types of transformations on data.
    """
    PARSE_DATES = "parse_dates"


# --- Configuration models ---

@dataclass
class Hardware:
    """
    Hardware specifications for EMR/Spark jobs.
    """
    ram: int = 4
    cores: int = 2
    disk: int = 20


@dataclass
class VolumetricExpectation:
    """
    Expected data volume characteristics.
    """
    data_size_gb: float = 0.1
    avg_file_size_mb: int = 100


@dataclass
class SparkConfiguration:
    """
    Spark tuning configurations based on volume or custom settings.
    """
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
        """
        Get final Spark configuration either from custom settings or recommended.
        """
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
    """
    Specifies which technology and tuning apply to a process.
    """
    technology: Technologies = Technologies.EMR
    spark_configuration: SparkConfiguration = field(default_factory=SparkConfiguration)


@dataclass
class DateLocatedFilename:
    """
    Regex to extract dates from filenames.
    """
    regex: str


class InterfaceSpecs:
    """
    Base class for input file specification models.
    """

    @property
    def read_config(self) -> dict:
        raise NotImplementedError('It is mandatory to implement read_config property')


@dataclass
class XMLSpecs(InterfaceSpecs):
    """
    Specifications for reading XML input files.
    """
    encoding: str
    xpath: str
    date_located: DateLocated
    date_located_filename: DateLocatedFilename

    @property
    def read_config(self) -> dict:
        return {}


@dataclass
class JSONSpecs(InterfaceSpecs):
    """
    Specifications for reading JSON input files.
    """
    encoding: str
    date_located: DateLocated
    date_located_filename: DateLocatedFilename
    source_level: Optional[str]
    source_level_format: JSONFormat = JSONFormat.ARRAY

    @property
    def read_config(self) -> dict:
        return {
            'encoding': self.encoding
        }

    @property
    def levels(self) -> List[str]:
        """
        JSON navigation path if hierarchical.
        """
        if self.source_level:
            return self.source_level.split('.')
        else:
            return []


@dataclass
class CSVSpecs(InterfaceSpecs):
    """
    Specifications for reading CSV input files.
    """
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
# flows.py (continued)

@dataclass
class CSVSpecsReport:
    """
    Specifications for output CSV reports.
    """
    header: bool
    index: bool
    encoding: str
    delimiter: str


@dataclass
class JSONSpecsReport:
    """
    Specifications for output JSON reports.
    """
    format: JSONSpectFormat = JSONSpectFormat.LINES


@dataclass
class Parameters:
    """
    Dynamic runtime parameters for the current process execution.
    """
    dataflow: str
    process: str
    table: str
    source_file_path: str
    bucket_prefix: str
    file_name: Optional[str]
    file_date: Optional[str]
    execution_mode: ExecutionMode = ExecutionMode.DELTA

    @property
    def region(self) -> str:
        """
        AWS region retrieved from environment variables.
        """
        return os.environ["AWS_REGION"]


@dataclass
class IncomingFileLandingToRaw:
    """
    Configuration for incoming files at the Landing layer.
    """
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
        """
        Return the file-specific reading configuration.
        """
        if self.file_format == LandingFileFormat.XML:
            return self.xml_specs
        elif self.file_format == LandingFileFormat.JSON:
            return self.json_specs
        else:
            return self.csv_specs


@dataclass
class Transformation:
    """
    Base class for a data transformation.
    """
    type: TransformationType

    @classmethod
    def get_subclass_from_dict(cls, transformation: dict):
        """
        Factory to dynamically instantiate the correct transformation subclass.
        """
        transformation_type = transformation.get('type')
        transformation_mapping = {
            TransformationType.PARSE_DATES.value: ParseDatesTransformation
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
    """
    Transformation that parses string dates into structured formats.
    """
    columns: List[str]
    source_format: List[str]
    target_format: str = "yyyy-MM-dd"


@dataclass
class Casting:
    """
    Casting specifications for type transformations on data.
    """
    strategy: CastingStrategy = CastingStrategy.ONE_BY_ONE
    fix_incompatible_characters: bool = True
    master_table: Optional[str] = None
    transformations: List[Transformation] = field(default_factory=list)


@dataclass
class DatabaseTable:
    """
    Logical and physical metadata for a data table.
    """
    database: Database
    table: str
    primary_keys: Optional[list] = field(default_factory=list)
    casting: Casting = field(default_factory=Casting)
    partition_field: str = "data_date"

    @property
    def database_relation(self) -> str:
        """
        Full database name relation, considering platform.
        """
        from data_framework.modules.config.core import config
        if config().platform == Platform.INFINITY:
            return self.database.value
        else:
            return f'rl_{self.database.value}'

    @property
    def full_name(self) -> str:
        """
        Full qualified table name (database.table).
        """
        return f'{self.database_relation}.{self.table}'

    @property
    def sql_where(self) -> str:
        """
        Auto-generated SQL WHERE filter based on partition field.
        """
        from data_framework.modules.config.core import config
        if config().parameters.execution_mode == ExecutionMode.DELTA and self.partition_field:
            return f"{self.partition_field} = '{config().parameters.file_date}'"
        return ""


@dataclass
class TableDict:
    """
    Wrapper for multiple DatabaseTable objects.
    """
    tables: Tuple[str, DatabaseTable]

    def table(self, table_key: str) -> Union[DatabaseTable, None]:
        """
        Retrieve a table configuration by its key.
        """
        table_info = self.tables.get(table_key)
        if not table_info:
            raise TableKeyError(
                table_key=table_key,
                available_table_keys=list(self.tables.keys())
            )
        return table_info

    def table_key(self, database: str, table: str) -> Union[str, None]:
        """
        Retrieve a key by database and table names.
        """
        for table_key, database_table in self.tables.items():
            if database_table.database.value == database and database_table.table == table:
                return table_key
        raise TableConfigNotFoundError(database=database, table=table)


@dataclass
class LandingToRaw:
    """
    Full configuration for a landing-to-raw pipeline process.
    """
    incoming_file: IncomingFileLandingToRaw
    output_file: DatabaseTable
    processing_specifications: ProcessingSpecifications = field(default_factory=ProcessingSpecifications)
    notifications: NotificationDict = field(default_factory=NotificationDict)


@dataclass
class ProcessVars:
    """
    Dynamic variables injected into processes.
    """
    _variables: dict = field(default_factory=dict)

    def get_variable(self, name: str):
        """
        Retrieve a variable by name.
        """
        return self._variables.get(name)


@dataclass
class GenericProcess:
    """
    Generic ETL pipeline process connecting source tables to target tables.
    """
    source_tables: TableDict
    target_tables: TableDict
    processing_specifications: ProcessingSpecifications = field(default_factory=ProcessingSpecifications)
    notifications: NotificationDict = field(default_factory=NotificationDict)
    vars: Optional[ProcessVars] = field(default_factory=ProcessVars)


@dataclass
class OutputReport:
    """
    Single output report configuration (e.g., for exports).
    """
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
    """
    Group of output reports generated at the end of a pipeline.
    """
    output_reports: List[OutputReport]
    processing_specifications: ProcessingSpecifications
    notifications: NotificationDict = field(default_factory=NotificationDict)


@dataclass
class Processes:
    """
    Container of all configured processes for a dataflow.
    """
    landing_to_raw: Optional[LandingToRaw]
    raw_to_staging: Optional[GenericProcess] = None
    staging_to_common: Optional[GenericProcess] = None
    staging_to_business: Optional[GenericProcess] = None
    common_to_business: Optional[GenericProcess] = None
    common_to_output: Optional[ToOutput] = None
    business_to_output: Optional[ToOutput] = None


@dataclass
class Config:
    """
    Master configuration object used by the entire Data Platform Framework.
    """
    processes: Processes
    environment: Environment
    platform: Platform
    parameters: Parameters
    project_id: str
    data_framework_notifications: DataFrameworkNotifications

    @property
    def has_next_process(self) -> bool:
        """
        Check if there are any remaining processes after the current one.
        """
        processes = [field.name for field in fields(Processes)]
        current_process_index = processes.index(self.parameters.process)
        posible_processes_begind_index = current_process_index + 1
        possible_processes = processes[posible_processes_begind_index:]
        next_processes = [process for process in possible_processes if getattr(self.processes, process) is not None]
        return not next_processes

    @property
    def is_first_process(self) -> bool:
        """
        Check if the current process is the first in the sequence.
        """
        processes = [field.name for field in fields(Processes)]
        next_processes = [process for process in processes if getattr(self.processes, process) is not None]
        current_process_index = next_processes.index(self.parameters.process)
        return current_process_index == 0

    def current_process_config(self) -> Union[LandingToRaw, GenericProcess, ToOutput]:
        """
        Retrieve the full configuration object for the current process.
        """
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


```



### DF Modules: Data Process
#### Summary
This module provides a standardized abstraction layer for data processing operations within the framework. It defines a common interface (`DataProcessInterface`) for tasks like reading, writing (merge, insert, overwrite), transforming, casting, and querying data. It features a core facade (`CoreDataProcess`) that dynamically delegates these operations to technology-specific implementations based on configuration, primarily supporting Apache Spark with Iceberg/Glue integration (`SparkDataProcess`) and a more basic Pandas integration leveraging AWS Athena (`PandasDataProcess`). The module includes helpers for Athena interaction (`AthenaClient`), SQL casting (`Cast`), Spark dynamic configuration (`DynamicConfig`), and various utility functions for Spark transformations and data handling (`integrations/spark/utils.py`, `integrations/spark/transformations/`).

#### What the "data_process" module does (big picture)
- **Goal:** To offer a consistent, high-level API for diverse data processing needs across different execution environments (EMR/Spark, Lambda/Pandas) and data formats/stores (Iceberg, S3 files via Athena).
- **Why it matters:** It decouples the core data pipeline logic from the specifics of the underlying data processing engine (Spark vs. Pandas) and storage format (Iceberg vs. others). This promotes code reusability, maintainability, and adaptability to different technological choices or environments.
- **How:** It employs an interface (`DataProcessInterface`) and facade pattern (`CoreDataProcess`). `CoreDataProcess` acts as the single entry point, dynamically instantiating and using either `SparkDataProcess` or `PandasDataProcess` based on runtime configuration. `SparkDataProcess` provides a rich implementation leveraging Spark SQL, Iceberg capabilities (via Glue Catalog), dynamic configuration generation (`DynamicConfig`), custom transformations (e.g., `parse_dates`), helper utilities (`integrations/spark/utils.py`), monitoring integration, and retry logic for Iceberg operations. `PandasDataProcess` offers a more limited implementation, primarily using Pandas DataFrames and an `AthenaClient` helper for data interaction via SQL queries. The `Cast` helper assists both implementations with SQL-based type casting.

#### data_process Module High-Level: Script by Script

| Script                                                               | What it is        | What it does |
| :------------------------------------------------------------------- | :---------------- | :----------- |
| `core_data_process.py`                                               | Core Logic/Facade | Acts as the main entry point, dynamically choosing and delegating data processing tasks to the appropriate technology-specific implementation based on configuration. |
| `helpers/athena.py`                                                  | AWS Integration Helper | Provides a client (`AthenaClient`) to execute SQL queries against AWS Athena, wait for completion, and retrieve results as Pandas DataFrames. |
| `helpers/cast.py`                                                    | Data Type Helper  | Contains logic (`Cast` class) for generating SQL `CAST` expressions and formatting data for insertion, particularly for compatibility between different data types and systems. |
| `integrations/pandas/pandas_data_process.py`                         | Technology Integration (Pandas) | Implements the `DataProcessInterface` using Pandas DataFrames, leveraging the `AthenaClient` to interact with data stored in AWS (via Athena) for read/query/insert operations. Some operations are not implemented. |
| `integrations/spark/dynamic_config.py`                               | Spark Configuration Helper | Generates recommended Spark configuration settings dynamically based on dataset size, job type, and optimization goals, especially tailored for EMR Serverless constraints. |
| `integrations/spark/spark_data_process.py`                           | Technology Integration (Spark) | Implements the `DataProcessInterface` using Apache Spark, specifically interacting with Apache Iceberg tables via AWS Glue Catalog. Handles Spark session creation, data reading/writing (merge, insert overwrite, append), casting, joins, and other transformations. Includes retry logic and monitoring. |
| `integrations/spark/transformations/parse_dates.py`                  | Spark Transformation | Provides a specific Spark transformation function (`parse_dates`) to convert date/timestamp columns between different string formats using Spark SQL functions. |
| `integrations/spark/utils.py`                                        | Spark Utilities   | Contains helper functions for the Spark integration, including schema conversion (to all strings), mapping types, dynamically applying transformations, fixing incompatible characters in column names/JSON strings, and parsing nested JSON data from files. |
| `interface_data_process.py`                                          | Interface Definition | Defines the abstract base class (`DataProcessInterface`) and standard data structures (`ReadResponse`, `WriteResponse`) for all data processing operations, ensuring consistency across different implementations (Spark, Pandas). |
| `interface_dataflow.py`                                | Interface Definition | Defines the abstract base class (`DataFlowInterface`) and common structures/utilities for all specific dataflow implementations within the framework. |
---

#### src/data_framework/modules/data_process/core_data_process.py
#### `core_data_process.py`
**Purpose:**
This script serves as the primary interface for data processing operations within the framework. It abstracts the underlying technology (Pandas, Spark) by dynamically loading the correct implementation based on the project's configuration. It exposes a set of common data manipulation methods.

**Key parts:**
- **`CoreDataProcess` class:** The main class acting as a facade.
- **`_data_process` (LazyClassProperty):** Lazily initializes and returns the appropriate `DataProcessInterface` implementation (e.g., `SparkDataProcess`, `PandasDataProcess`) based on `config().current_process_config().processing_specifications.technology`.
- **Class Methods (`merge`, `insert_overwrite`, `datacast`, `read_table`, `delete_from_table`, `insert_dataframe`, `join`, `create_dataframe`, `query`, `overwrite_columns`, `unfold_string_values`, `add_dynamic_column`, `stack_columns`, `is_empty`, `count_rows`, `select_columns`, `show_dataframe`):** These methods mirror the `DataProcessInterface` and simply delegate the call to the instantiated `_data_process` object.

```python
"""
core_data_process.py

This module acts as the central access point for executing data processing tasks
within the data framework. It uses the Facade pattern to abstract the underlying
technology (e.g., Pandas or Spark) and exposes a unified interface for performing
common data operations such as reading, writing, joining, transforming, and querying data.

Main Class:
    - CoreDataProcess: Provides class-level methods that delegate data processing
      tasks to a backend implementation based on runtime configuration.

Key Features:
    - Technology-agnostic data processing
    - Lazy initialization of the data processing backend
    - Simplified interface for ETL workflows
"""

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
    """
    Facade class that delegates data processing operations to the correct
    backend implementation (Pandas or Spark) depending on the configuration.

    Usage:
        CoreDataProcess.read_table(...)
        CoreDataProcess.merge(...)
    """

    @LazyClassProperty
    def _data_process(cls) -> DataProcessInterface:
        """
        Lazily loads and returns the appropriate data processing backend.

        The backend is chosen based on the `technology` specified in the
        configuration (e.g., EMR for Spark, LAMBDA for Pandas).

        Returns:
            DataProcessInterface: A concrete implementation such as SparkDataProcess or PandasDataProcess.
        """
        technology = config().current_process_config().processing_specifications.technology

        if technology == Technologies.EMR:
            # Import and return Spark implementation
            from data_framework.modules.data_process.integrations.spark.spark_data_process import SparkDataProcess
            return SparkDataProcess()

        elif technology == Technologies.LAMBDA:
            # Import and return Pandas implementation
            from data_framework.modules.data_process.integrations.pandas.pandas_data_process import PandasDataProcess
            return PandasDataProcess()

    
    
    """
    @classmethod is a method that is bound to the class, not the instance. It receives the class as its first argument, conventionally named cls.

    When to Use @classmethod? Use @classmethod when:

    - The method needs access to class attributes (like _data_process) or factory methods.
    - You want the method to work at the class level instead of instance level.
    
    """
    
    @classmethod
    def merge(cls, dataframe: Any, table_config: DatabaseTable, custom_strategy: str = None) -> WriteResponse:
        """
        Merges a dataframe into the target table using a custom strategy if provided.
        """
        return cls._data_process.merge(dataframe=dataframe, table_config=table_config, custom_strategy=custom_strategy)

    @classmethod
    def insert_overwrite(cls, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        """
        Inserts the dataframe into the target table, replacing existing data.
        """
        return cls._data_process.insert_overwrite(dataframe=dataframe, table_config=table_config)

    @classmethod
    def datacast(cls, table_source: DatabaseTable, table_target: DatabaseTable) -> ReadResponse:
        """
        Casts the schema of the source table to match the target table.
        """
        return cls._data_process.datacast(table_source=table_source, table_target=table_target)

    @classmethod
    def read_table(cls, database: str, table: str, filter: str = None, columns: List[str] = None) -> ReadResponse:
        """
        Reads data from a database table with optional filtering and column selection.
        """
        return cls._data_process.read_table(database=database, table=table, filter=filter, columns=columns)

    @classmethod
    def delete_from_table(cls, table_config: DatabaseTable, _filter: str) -> WriteResponse:
        """
        Deletes rows from a table based on a given filter condition.
        """
        return cls._data_process.delete_from_table(table_config=table_config, _filter=_filter)

    @classmethod
    def insert_dataframe(cls, dataframe: Any, table_config: DatabaseTable) -> WriteResponse:
        """
        Inserts a dataframe into a target table without replacing existing data.
        """
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
        """
        Joins two dataframes using the specified join strategy and join keys.

        Parameters:
            how (str): Type of join: 'inner', 'left', 'right', 'outer', etc.
        """
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
        """
        Creates a new dataframe from raw data, optionally applying a schema.
        """
        return cls._data_process.create_dataframe(data=data, schema=schema)

    @classmethod
    def query(cls, sql: str) -> ReadResponse:
        """
        Executes a SQL query and returns the result as a dataframe.
        """
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
        """
        Replaces columns in the dataframe using suffix logic to avoid collisions.

        Parameters:
            drop_columns (bool): Whether to drop the original columns after overwriting.
        """
        return cls._data_process.overwrite_columns(
            dataframe=dataframe,
            columns=columns,
            custom_column_suffix=custom_column_suffix,
            default_column_suffix=default_column_suffix,
            drop_columns=drop_columns
        )

    @classmethod
    def unfold_string_values(cls, dataframe: Any, column_name: str, separator: str) -> ReadResponse:
        """
        Splits string values in a column by a separator and expands them into rows.
        """
        return cls._data_process.unfold_string_values(dataframe=dataframe, column_name=column_name, separator=separator)

    @classmethod
    def add_dynamic_column(
        cls,
        dataframe: Any,
        new_column: str,
        reference_column: str,
        available_columns: List[str],
        default_value: Any = None
    ) -> ReadResponse:
        """
        Adds a new column based on logic involving another column and a set of candidates.

        Typically used for dynamic transformations such as fallback lookups.
        """
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
        """
        Combines values from multiple columns into repeated rows with a consistent schema.
        """
        return cls._data_process.stack_columns(
            dataframe=dataframe,
            source_columns=source_columns,
            target_columns=target_columns
        )

    @classmethod
    def is_empty(cls, dataframe: Any) -> bool:
        """
        Returns True if the dataframe has no rows; otherwise False.
        """
        return cls._data_process.is_empty(dataframe=dataframe)

    @classmethod
    def count_rows(cls, dataframe: Any) -> int:
        """
        Returns the number of rows in the dataframe.
        """
        return cls._data_process.count_rows(dataframe=dataframe)

    @classmethod
    def select_columns(cls, dataframe: Any, columns: List[str]) -> ReadResponse:
        """
        Selects a subset of columns from the dataframe.
        """
        return cls._data_process.select_columns(dataframe=dataframe, columns=columns)

    @classmethod
    def show_dataframe(cls, dataframe: Any) -> WriteResponse:
        """
        Displays the dataframe, useful for debugging or validation during development.
        """
        return cls._data_process.show_dataframe(dataframe=dataframe)


```

#### src/data_framework/modules/data_process/helpers/athena.py
#### `athena.py`
**Purpose:**
This script provides a dedicated client for interacting with AWS Athena. It encapsulates the logic required to execute SQL queries, monitor their execution status, and retrieve the results, typically as Pandas DataFrames.

**Key parts:**
- **`AthenaClient` class:** Manages interactions with AWS Athena.
- **`__init__(self)`:** Initializes the boto3 Athena client, storage access (`Storage`), and determines the S3 output path for query results based on configuration.
- **`execute_query(self, query: str, read_output: bool = True)`:** Submits a query to Athena, waits for it to complete using `wait_for_query_to_complete`, and optionally reads the results using `get_query_results`. Handles potential errors.
- **`wait_for_query_to_complete(self, query_execution_id: str)`:** Polls Athena to check the status of a running query execution. Returns the S3 output location upon success or raises an `AthenaError` on failure/cancellation.
- **`get_query_results(self, output_location: str)`:** Reads the query result CSV file from the specified S3 location using the `Storage` module and parses it into a Pandas DataFrame.
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

#### src/data_framework/modules/data_process/helpers/cast.py
#### `cast.py`
**Purpose:**
This script offers utility functions specifically designed for data type casting and formatting, primarily when preparing data for SQL-based operations or ensuring type compatibility between source and target systems.

**Key parts:**
- **`Cast` class:** Contains methods for casting logic.
- **`__init__(self)`:** Initializes logger and `CoreCatalogue` for fetching table schemas.
- **`cast_columns(self, column_name: str, column_type: str)`:** Generates a SQL `CAST` or `FROM_JSON` expression string for a given column name and target type, handling various SQL types including complex ones like STRUCT/ARRAY and BOOLEAN variations.
- **`build_datacast_query(self, source_columns: List[str], table_target: DatabaseTable, view_name: str = 'data_to_cast')`:** Constructs a full SQL `SELECT` query that applies the `cast_columns` logic to each column in a list, based on the target table's schema obtained from the `CoreCatalogue`. Used for type casting entire datasets. Raises `CastQueryError` on failure.
- **`get_query_to_insert_dataframe(self, dataframe: DataFrame, table_config: DatabaseTable)`:** Generates a SQL `INSERT INTO ... VALUES ...` query string from a Pandas DataFrame. It aligns DataFrame columns with the target table schema, formats each value correctly using `cast_df_row`, and constructs the multi-row `VALUES` clause. Raises `CastQueryError` on failure.
- **`cast_df_row(self, row: Series, column_types: Dict[str, str])`:** Formats a single row (Pandas Series) into a SQL `VALUES` tuple string (e.g., `('value1', 123, NULL)`), applying `cast_df_value` to each cell based on the target column type.
- **`cast_df_value(self, value: Any, _type: str)`:** Formats an individual Python/Pandas value into its SQL string representation (e.g., handling NULLs, quoting strings, formatting dates/timestamps).

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
        if column_type in ('int', 'double', 'float', 'date', 'timestamp') or column_type.startswith('decimal'):
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
        view_name: str = 'data_to_cast'
    ) -> str:
        try:
            # Obtain the target schema with the type of each column
            schema_target = self.catalogue.get_schema(table_target.database_relation, table_target.table)
            target_types = schema_target.schema.get_column_type_mapping(partitioned=True)
            # Cast each column to its target type
            casted_columns = [
                self.cast_columns(
                    column_name=f"`{column}`",
                    column_type=target_types.get(column.lower(), 'string')
                )
                for column in source_columns
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


#### src/data_framework/modules/data_process/integrations/pandas/pandas_data_process.py
#### `pandas_data_process.py`
**Purpose:**
This script provides the concrete implementation of the `DataProcessInterface` using the Pandas library. It primarily interacts with data via AWS Athena for reading and querying, performing transformations directly using Pandas functions where possible.

**Key parts:**
- **`PandasDataProcess` class:** Implements the `DataProcessInterface`.
- **`__init__(self)`:** Initializes the `AthenaClient` helper.
- **`read_table(self, database: str, table: str, ...)`:** Constructs and executes a `SELECT` query via `AthenaClient` to read data into a Pandas DataFrame.
- **`insert_dataframe(self, dataframe: DataFrame, ...)`:** Uses the `Cast` helper to generate an `INSERT` SQL query from the Pandas DataFrame and executes it via `AthenaClient`.
- **`join(self, df_1: DataFrame, df_2: DataFrame, ...)`:** Performs DataFrame joins using the native `pandas.merge` function. Includes validation for join type and column matching.
- **`create_dataframe(self, data: Any, ...)`:** Creates a Pandas DataFrame from input data (e.g., a list of dictionaries) using `pd.DataFrame`. Schema support is noted as not implemented.
- **`query(self, sql: str)`:** Executes an arbitrary SQL query using `AthenaClient` and returns the result as a Pandas DataFrame.
- **`overwrite_columns(self, dataframe: DataFrame, ...)`:** Implements column overwriting logic using Pandas DataFrame operations (`fillna`, `drop`).
- **`stack_columns(self, dataframe: DataFrame, ...)`:** Implements column stacking (unpivoting) using `pandas.melt`. Includes validation for target column count.
- **`is_empty(self, dataframe: DataFrame)`:** Checks if a DataFrame is empty using `dataframe.empty`.
- **`count_rows(self, dataframe: DataFrame)`:** Counts rows using `len(dataframe)`.
- **`select_columns(self, dataframe: DataFrame, ...)`:** Selects specific columns using Pandas DataFrame indexing.
- **`show_dataframe(self, dataframe: DataFrame)`:** Prints the DataFrame content to the log using `logger.info` and Pandas display options.
- **`NotImplementedError`:** Raised for methods (`merge`, `datacast`, `delete_from_table`, `unfold_string_values`, `add_dynamic_column`) that are not currently supported by this Pandas/Athena implementation.
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


#### src/data_framework/modules/data_process/integrations/spark/dynamic_config.py
#### `src/data_framework/modules/data_process/integrations/spark/dynamic_config.py`
**Purpose:**
This script provides helper methods to dynamically generate recommended Apache Spark configuration settings. It aims to optimize Spark jobs based on input parameters like dataset size, job type, and optimization goals (cost vs. throughput), with specific adjustments for AWS EMR Serverless resource constraints.

**Key parts:**
- **`DynamicConfig` class:** Container for static methods related to configuration generation.
- **`determine_cores(cls, dataset_size_gb: float)`:** Suggests the number of executor cores based on dataset size tiers.
- **`determine_driver_settings(cls, dataset_size_gb: float, ...)`:** Suggests driver memory and cores based on dataset size and maximum executor memory.
- **`determine_disk_size(cls, dataset_size_gb: float)`:** Recommends EMR Serverless disk size based on dataset size, clamped between 20GB and 200GB.
- **`determine_memory_overhead_factor(cls, dataset_size_gb: float)`:** Suggests a memory overhead factor based on dataset size tiers.
- **`adjust_resources_to_emr_serverless_constraints(cls, cpu: int, memory_gb: int)`:** Adjusts requested CPU and memory values to align with the specific valid combinations allowed by EMR Serverless (e.g., CPU/memory ratios and increments).
- **`recommend_spark_config(cls, dataset_size_gb: float, ...)`:** The main method that orchestrates the others. It takes dataset size, job type, optimization goal, average file size, maximum executors/memory, and optional EMR application ID/instance count to generate a dictionary of recommended Spark configuration properties (e.g., `spark.executor.memory`, `spark.driver.cores`, `spark.dynamicAllocation.maxExecutors`, `spark.emr-serverless.executor.disk`). It includes commented-out sections for potential future enhancements like Iceberg-specific tuning or direct EMR Serverless API integration.

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

#### src/data_framework/modules/data_process/integrations/spark/spark_data_process.py
**Key parts:**
- **`SparkDataProcess` class:** Implements the `DataProcessInterface` for Spark/Iceberg.
- **`__init__(self)`:**
    - Initializes SparkConf with base settings (AppName, S3 connections, KryoSerializer, Iceberg extensions, Glue Catalog integration, Hive support, dynamic partition overwrite).
    - Loads additional Spark configurations from the framework's config module (`json_config.spark_configuration.config`).
    - Creates the `SparkSession` using the combined configuration.
    - Initializes `CoreCatalogue`, `Storage`, and `CoreMonitoring`.
    - Raises `SparkConfigurationError` if setup fails.
- **`_build_complete_table_name(self, database: str, table: str)`:** Helper to construct the full Iceberg table identifier using the `iceberg_catalog`.
- **`_track_table_metric(self, table_config: DatabaseTable, data_frame: DataFrame = None)`:** Sends table metrics (read record count or Iceberg snapshot summary metrics like added/deleted records/size) to `CoreMonitoring`. Uses a mapping (`__iceberg_snapshot_metrics_map`) for Iceberg metrics.
- **`_execute_query(self, query: str)`:** Executes a Spark SQL query with a built-in retry mechanism (up to 3 attempts with random sleep) specifically for handling common Iceberg commit exceptions (`ConcurrentModificationException`, etc.).
- **`merge(self, dataframe: DataFrame, ...)`:** Implements Iceberg `MERGE INTO` operation. Selects relevant columns, creates a temp view, builds and executes the `MERGE` SQL based on primary keys and an optional custom strategy. Tracks metrics post-operation. Sets Spark job group for monitoring.
- **`insert_overwrite(self, dataframe: Any, ...)`:** Implements an overwrite operation using `dataframe.write.format("iceberg").mode('overwrite').save(...)`. Selects columns and tracks metrics. Sets Spark job group.
- **`datacast(self, table_source: DatabaseTable, table_target: DatabaseTable)`:** Handles reading data from a source (often raw layer), applying transformations, and casting data types according to the `table_target` configuration. Supports different `CastingStrategy` (ONE_BY_ONE, DYNAMIC) and file formats (JSON, CSV, Parquet). Uses `_read_raw_file` or `_read_raw_json_file`, applies transformations via `utils.apply_transformations`, optionally fixes incompatible characters, and uses `Cast().build_datacast_query` for ONE_BY_ONE casting. Tracks read metrics.
- **`_read_raw_file(self, ...)`:** Reads raw data (CSV/Parquet) using `spark.read`, applying configurations from the framework config and handling schema inference or application. Supports delta vs full execution modes.
- **`_read_raw_json_file(self, ...)`:** Reads and parses JSON files (potentially multiple) from storage, creates a Spark DataFrame, optionally converting all fields to string based on `CastingStrategy`.
- **`read_table(self, database: str, table: str, ...)`:** Reads an Iceberg table using a `SELECT` query constructed via `_build_complete_table_name` and executed by `_execute_query`.
- **`delete_from_table(self, table_config: DatabaseTable, _filter: str)`:** Executes an Iceberg `DELETE FROM` query via `_execute_query`.
- **`insert_dataframe(self, dataframe: DataFrame, ...)`:** Appends a DataFrame to an Iceberg table using `dataframe.writeTo(table_name).append()`. Includes retry logic for Iceberg exceptions. Selects columns first using `_select_table_columns`.
- **`_select_table_columns(self, dataframe: DataFrame, ...)`:** Selects and de-duplicates columns from a DataFrame based on the target table's schema obtained from `CoreCatalogue`.
- **`join(self, df_1: DataFrame, df_2: DataFrame, ...)`:** Performs Spark DataFrame joins. Handles aliasing, renaming conflicting columns (except join keys), and supports different join keys (`left_on`, `right_on`). Includes validation.
- **`create_dataframe(self, data: Any, schema: str = None)`:** Creates a Spark DataFrame using `spark.createDataFrame`.
- **`query(self, sql: str)`:** Executes an arbitrary SQL query using `_execute_query`.
- **`overwrite_columns(self, dataframe: DataFrame, ...)`:** Overwrites specified columns based on values from corresponding custom/default suffixed columns using Spark's `withColumn` and `when/otherwise`.
- **`unfold_string_values(self, dataframe: DataFrame, ...)`:** Extracts unique values from a string column containing delimited values using Spark SQL functions (`explode`, `split`).
- **`add_dynamic_column(self, dataframe: DataFrame, ...)`:** Adds a new column whose value is dynamically determined based on the value in a `reference_column` and mapped against `available_columns` using Spark's `when/otherwise`.
- **`stack_columns(self, dataframe: DataFrame, ...)`:** Unpivots specified `source_columns` into two `target_columns` using Spark SQL's `stack` expression. Includes validation.
- **`is_empty(self, dataframe: DataFrame)`:** Checks if a Spark DataFrame is empty using `dataframe.isEmpty()`.
- **`count_rows(self, dataframe: DataFrame)`:** Counts rows using `dataframe.count()`.
- **`select_columns(self, dataframe: DataFrame, ...)`:** Selects columns using `dataframe.select()`.
- **`show_dataframe(self, dataframe: DataFrame)`:** Displays DataFrame content using `dataframe.show(truncate=False)`.

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
            self._track_table_metric(table_config=table_config)
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
            self._track_table_metric(table_config=table_config)

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
                    partition_path=read_path.relative_path,
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
            df_raw = utils.apply_transformations(df_raw, table_target.casting.transformations)

            if table_target.casting.strategy == CastingStrategy.ONE_BY_ONE:
                if debug_code:
                    logger.info('Schema before casting:')
                    df_raw.printSchema()
                if table_target.casting.fix_incompatible_characters:
                    df_raw = utils.fix_incompatible_characters(
                        df_origin=df_raw,
                        table_target=table_target
                    )
                # After reading the data as strings, each field is converted to its corresponding data type
                view_name = 'data_to_cast'
                df_raw.createOrReplaceTempView(view_name)
                query = Cast().build_datacast_query(
                    source_columns=df_raw.columns,
                    table_target=table_target,
                    view_name=view_name
                )
                df_raw = self._execute_query(query)
                if debug_code:
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

    def _read_raw_json_file(self, table_path: str, partition_path: str, casting_strategy: CastingStrategy) -> DataFrame:
        # Read JSON files from S3
        if config().parameters.execution_mode == ExecutionMode.DELTA:
            file_path = partition_path + config().parameters.file_name
            response = self.storage.read(layer=Layer.RAW, key_path=file_path)
            files = [BytesIO(response.data)]
        else:
            files = [
                BytesIO(self.storage.read(layer=Layer.RAW, key_path=file_key).data)
                for file_key in self.storage.list_files(layer=Layer.RAW, prefix=table_path).result
            ]
        # Parse into Python dictionaries
        data = utils.parse_json(files)
        # Transform into a DataFrame
        if casting_strategy == CastingStrategy.ONE_BY_ONE:
            # All fields are converted into strings
            columns = max(data, key=len).keys()
            schema = utils.convert_schema_to_strings(columns=columns)
            df = self.create_dataframe(data=data, schema=schema).data
            return df
        elif casting_strategy == CastingStrategy.DYNAMIC:
            # Each field type is inferred by Spark
            df = self.create_dataframe(data=data).data
            return df

    def _execute_query(self, query: str) -> DataFrame:
        max_retries = 3
        for attempt in range(max_retries):
            try:
                df_result = self.spark.sql(query)
                break
            except Exception as exception:
                if any(word in str(exception) for word in iceberg_exceptions) and attempt < max_retries - 1:
                    logger.warning(exception)
                    time.sleep(random.randint(1, 20))
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
            max_retries = 3
            for attempt in range(max_retries):
                try:
                    dataframe.writeTo(table_name).append()
                    return WriteResponse(success=True, error=None)
                except Exception as exception:
                    if any(word in str(exception) for word in iceberg_exceptions) and attempt < max_retries - 1:
                        logger.warning(exception)
                        time.sleep(random.randint(1, 20))
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
        dataframe = dataframe.select(*table_columns).distinct()
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


#### src/data_framework/modules/data_process/integrations/spark/transformations/parse_dates.py
#### `src/data_framework/modules/data_process/integrations/spark/transformations/parse_dates.py`
**Purpose:**
This script provides a reusable transformation function specifically for parsing date or timestamp strings within a Spark DataFrame. It attempts to convert specified columns from various potential source formats into a single target format.

**Key parts:**
- **`parse_dates(df: DataFrame, transformation: Transformation)` function:**
    - Takes a Spark DataFrame and a `Transformation` configuration object as input.
    - Iterates through the columns specified in `transformation.columns`.
    - For each column, iterates through potential `transformation.source_format` strings.
    - Uses nested Spark SQL `when` conditions with `to_date` and `date_format` functions. It attempts to parse the column using a `source_format`; if successful (`isNotNull`), it formats the result to the `transformation.target_format`. If parsing fails for a format, it keeps the original column value and tries the next format.
    - Returns the modified DataFrame.
    - Raises `ValueError` if a specified column is not found in the DataFrame.

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

#### src/data_framework/modules/data_process/integrations/spark/utils.py
#### `src/data_framework/modules/data_process/integrations/spark/utils.py`
**Purpose:**
This script contains various utility functions specifically designed to support the Spark integration (`SparkDataProcess`). These helpers handle tasks like schema manipulation, type mapping, dynamic transformation execution, string cleaning for compatibility, and parsing complex JSON structures.

**Key parts:**
- **`convert_schema_to_strings(columns: List[str])`:** Creates a Spark `StructType` where all specified columns are defined as `StringType`. Useful for reading raw data before explicit casting.
- **`map_to_spark_type(db_type: str)`:** Maps common database type strings (like 'varchar', 'int', 'timestamp') to their corresponding PySpark `DataType` objects (e.g., `StringType()`, `IntegerType()`, `TimestampType()`). Defaults to `StringType`.
- **`apply_transformations(df: DataFrame, transformations: List[Transformation], ...)`:** Dynamically imports and applies transformation functions based on the `transformation.type` enum value. It looks for corresponding modules within the `...spark.transformations` package. Raises specific errors for missing or failed transformations.
- **`fix_column_incompatible_characters(json)`:** A helper function (intended for use within a UDF) that uses regular expressions to clean up potentially problematic characters or formatting in JSON strings, especially within keys, to make them compatible with Spark processing (e.g., converting `key=value` to `"key": "value"`, removing `@`, ensuring double quotes).
- **`fix_incompatible_characters(df_origin: DataFrame, table_target: DatabaseTable)`:** Applies the `fix_column_incompatible_characters` logic via a Spark UDF to specified struct columns in a DataFrame. It also renames columns by removing non-alphanumeric characters (`\W`) to ensure compatibility, based on the target schema definition.
- **`parse_json(files: List[BytesIO])`:** Reads JSON data line-by-line from a list of file-like objects (BytesIO). It navigates nested structures based on `json_specs.levels`, handles dictionary-formatted data (`JSONFormat.DICTIONARY`), extracts a date field for partitioning, and returns a flattened list of dictionaries ready for DataFrame creation.

```python

from data_framework.modules.config.model.flows import (
    Transformation,
    DatabaseTable,
    JSONFormat
)
from data_framework.modules.config.core import config
from data_framework.modules.exception.data_process_exceptions import (
    TransformationNotImplementedError,
    TransformationError
)
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from importlib import import_module
from typing import List, Dict, Any
from io import BytesIO
import pyspark.sql.functions as f
from pyspark.sql import DataFrame
from pyspark.sql.types import (
    StructType, StructField, StringType,
    IntegerType, FloatType, DoubleType,
    BooleanType, DateType, TimestampType
)
import re
import json


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
            df = transformation_function(df, transformation, **kwargs)
        except (ModuleNotFoundError, AttributeError):
            raise TransformationNotImplementedError(transformation=function_name)
        except Exception:
            raise TransformationError(transformation=function_name)
    return df


def fix_column_incompatible_characters(json):

    def repl(match):
        key = match.group(1)
        new_key = re.sub(r'\W', '', key).lower()
        return f'"{new_key}"'

    # key=value => "key": "value"
    json = re.sub(r'([^{},=\s]+)=([^,}]+)', r'"\1": "\2"', json)
    # '@key' => "key" or "@key" => "key"
    json = re.sub(r"[\"']([^\"']+)[\"'](?=\s*:)", repl, json)
    # 'key': => "key":
    json = re.sub(r"'([^']+)'(\s*:\s*)", r'"\1"\2', json)
    # :'value' => :"value"
    json = re.sub(r"(:\s*)'([^']+)'", r'\1"\2"', json)

    return json


def fix_incompatible_characters(df_origin: DataFrame, table_target: DatabaseTable) -> DataFrame:
    catalogue = CoreCatalogue()
    schema_target = catalogue.get_schema(table_target.database_relation, table_target.table).schema
    target_columns = schema_target.get_column_type_mapping(partitioned=True)

    df_modified = df_origin
    udf_fix_column_incompatible_characters = f.udf(fix_column_incompatible_characters, StringType())

    for field in df_origin.schema.fields:
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


def parse_json(files: List[BytesIO]) -> List[dict]:
    # Obtain JSON specifications
    json_specs = config().processes.landing_to_raw.incoming_file.json_specs
    partition_field = config().processes.landing_to_raw.output_file.partition_field
    data = []
    for file in files:
        for line in file.readlines():
            json_data = json.loads(line)
            data_date = json_data.get(partition_field, config().parameters.file_date)
            # Obtain the data to be parsed into a DataFrame based on the specified json path
            for key_level in json_specs.levels:
                try:
                    json_data = json_data[key_level]
                except KeyError:
                    raise KeyError(f'Path {json_specs.source_level} not found in JSON file')
            if json_specs.source_level_format == JSONFormat.DICTIONARY:
                json_data = list(json_data.values())
            [item.update({partition_field: data_date}) for item in json_data]
            data += json_data
    return data


```

#### src/data_framework/modules/data_process/interface_data_process.py
#### `src/data_framework/modules/data_process/interface_data_process.py`
**Purpose:**
This script defines the core contract for all data processing implementations within the framework. It establishes a standard set of methods that any data processing engine (like Spark or Pandas) must implement, ensuring consistency and interchangeability. It also defines standard response structures.

**Key parts:**
- **`ReadResponse` dataclass:** Standard structure for operations returning data. Contains `success` (bool), `error` (str), and `data` (Any, typically a DataFrame).
- **`WriteResponse` dataclass:** Standard structure for operations performing writes or actions. Contains `success` (bool) and `error` (str).
- **`DataProcessInterface(ABC)` class:**
    - Abstract Base Class defining the required methods for data processing.
    - **Abstract Methods:** Defines the signature for all common operations like `merge`, `insert_overwrite`, `datacast`, `read_table`, `delete_from_table`, `insert_dataframe`, `join`, `create_dataframe`, `query`, `overwrite_columns`, `unfold_string_values`, `add_dynamic_column`, `stack_columns`, `is_empty`, `count_rows`, `select_columns`, `show_dataframe`. Each method specifies expected parameters (often including DataFrames represented as `Any` and `DatabaseTable` configuration objects) and the expected return type (`ReadResponse` or `WriteResponse`). Implementations must provide concrete logic for these methods.

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


#### src/data_framework/modules/dataflow/interface_dataflow.py
#### `interface_dataflow.py`
**Purpose:**
This script defines the abstract base class `DataFlowInterface` that serves as the blueprint for all specific dataflow process implementations (e.g., LandingDataFlow, StagingDataFlow). It ensures a consistent structure and provides shared initialization logic, access to core framework components, helper methods for common tasks, and standardized ways to report results and metrics.

**Key parts:**
- **Dataclasses (`DataQualityTable`, `DataQuality`, `OutputResponse`, `PayloadResponse`):** Define standard structures for reporting data quality check targets, individual output results, and the overall payload summarizing the process execution outcome (success, next stage trigger, file info, DQ info, output results, notifications). `PayloadResponse` includes a method `get_failed_outputs`.
- **`DataFlowInterface(ABC)` class:**
    - **Properties (`config`, `logger`, `data_process`, `quality_controls`, `notifications`, `source_tables`, `target_tables`, `payload_response`, etc.):** Provide convenient access to the framework's configuration and core service modules, initialized in the constructor.
    - **`__init__(self)`:**
        - Initializes core framework components (`config`, `logger`, `CoreDataProcess`, `CoreQualityControls`, `CoreNotifications`, `CoreMonitoring`).
        - Initializes the `PayloadResponse` object.
        - Initializes an SSM client (`boto3.client('ssm')`).
        - Tracks the start time (`time.time()`) and logs a `DATAFLOW_START_EVENT` metric if it's the first process in the dataflow.
        - Handles potential initialization errors (`DataflowInitializationError`).
    - **`process(self)` (Abstract Method):** Placeholder for the main logic of a specific dataflow implementation. Must be overridden by subclasses.
    - **`vars(self, name: str)`:** Retrieves a variable from the current process configuration's `vars` section.
    - **`read_table_with_casting(self, ...)`:** Helper method to read data from a source table and apply casting logic using `CoreDataProcess.datacast`, simplifying a common pattern. Logs read information based on `ExecutionMode`.
    - **`read_table(self, ...)`:** Helper method to read data from a source table using `CoreDataProcess.read_table`, applying filters based on `ExecutionMode`. Logs read information.
    - **`write(self, df: Any, output_table_key: str)`:** Helper method to write a DataFrame to a target table using `CoreDataProcess.merge`. Logs success message. *(Note: Hardcoded use of merge, potential TODO noted in code regarding primary key retrieval).*
    - **`save_monitorization(self)`:** Calculates process duration and tracks standard end-of-process metrics (`PROCESS_END_EVENT`, `PROCESS_DURATION`) using `CoreMonitoring`. Includes commented-out logic for a potential `DATAFLOW_END_EVENT`.
    - **`save_payload_response(self)`:** Compiles the final `PayloadResponse` (including identified data quality tables and notifications to send), converts it to JSON, and attempts to save it to AWS SSM Parameter Store under a structured name (`/dataflow/{project_id}/{dataflow}-{process}/result`). Handles potential SSM or payload generation errors (`SSMError`, `PayloadResponseError`).
```python
"""
interface_dataflow.py

This module defines the abstract base class `DataFlowInterface` used as the foundation
for all specific dataflow implementations in the data processing framework. 
It centralizes logic for reading/writing data, applying data quality controls, tracking metrics, 
and saving state/results to AWS SSM. Each specific dataflow implementation 
(e.g., landing to raw, raw to staging) should inherit from this interface.

Purpose:
    - Standardizes initialization and structure across all dataflow classes.
    - Provides access to core services (configuration, logging, data processing, validation, notifications, monitoring).
    - Defines helper methods to simplify repetitive ETL operations like reading, writing, casting, and tracking.
    - Collects and stores process execution metadata, metrics, and quality results.

Key Components:
    - Data classes for reporting and payloads: `DataQualityTable`, `DataQuality`, `OutputResponse`, `PayloadResponse`
    - Abstract base class `DataFlowInterface` with mandatory and reusable behaviors
"""
@dataclass
class DataQualityTable:
    """
    Represents a single table involved in a data quality check.
    
    Attributes:
        database (str): The database name of the table.
        table (str): The table name.
    """
    database: str
    table: str


@dataclass
class DataQuality:
    """
    Collection of DataQualityTable items for grouping quality control targets.
    
    Attributes:
        tables (List[DataQualityTable]): A list of tables checked for quality.
    """
    tables: List[DataQualityTable] = field(default_factory=list)


@dataclass
class OutputResponse:
    """
    Encapsulates the result of an output write operation.
    
    Attributes:
        name (str): The identifier or name of the output.
        success (bool): Flag indicating whether the operation succeeded.
        error (Any): Error details in case of failure.
    """
    name: str
    success: bool = False
    error: Any = None


@dataclass
class PayloadResponse:
    """
    Represents the final output of a dataflow process including its status, file metadata,
    data quality details, outputs, and notifications.
    
    Attributes:
        success (bool): Flag indicating the overall success of the process.
        next_stage (bool): Indicates whether to trigger the next stage.
        file_name (str): Name of the incoming file (if applicable).
        file_date (str): Date of the file.
        data_quality (DataQuality): Data quality tables metadata.
        outputs (List[OutputResponse]): List of output responses.
        notifications (Optional[List[NotificationToSend]]): Notifications to be sent.
    """
    success: bool = False
    next_stage: bool = False
    file_name: str = None
    file_date: str = None
    data_quality: DataQuality = field(default_factory=DataQuality)
    outputs: List[OutputResponse] = field(default_factory=list)
    notifications: Optional[List[NotificationToSend]] = field(default_factory=list)

    def get_failed_outputs(self) -> List[str]:
        """
        Returns a list of output names that failed.
        
        Returns:
            List[str]: Names of outputs where success is False.
        """
        failed_outputs = [
            output.name
            for output in self.outputs
            if not output.success
        ]
        return failed_outputs

aws_ssm_exceptions = ['TooManyUpdates']


class DataFlowInterface(ABC):
    """
    Abstract base class for all specific dataflow processes. Provides shared infrastructure,
    such as config loading, table reading, writing, monitoring, and payload reporting.

    Subclasses must implement the `process()` method.
    """

    @property
    def config(self) -> Config:
        """Returns the loaded configuration for the current dataflow process."""
        return self.__config

    @property
    def logger(self):
        """Returns the logger instance for logging messages."""
        return self.__logger

    @property
    def data_process(self) -> CoreDataProcess:
        """Returns the data process handler (Spark/Pandas abstraction)."""
        return self.__data_process

    @property
    def quality_controls(self) -> CoreQualityControls:
        """Returns the quality controls module for data validation."""
        return self.__quality_controls

    @property
    def notifications(self) -> CoreNotifications:
        """Returns the notifications module for handling alerts."""
        return self.__notifications

    @property
    def source_tables(self) -> TableDict:
        """Returns the dictionary of configured source tables."""
        return self.__current_process_config.source_tables

    @property
    def target_tables(self) -> TableDict:
        """Returns the dictionary of configured target tables."""
        return self.__current_process_config.target_tables

    @property
    def payload_response(self) -> PayloadResponse:
        """Returns the current payload response instance to be populated and saved."""
        return self.__payload_response

    @property
    def incoming_file(self) -> DatabaseTable:
        """Returns the configured incoming file metadata."""
        return self.__current_process_config.incoming_file

    @property
    def output_file(self) -> DatabaseTable:
        """Returns the configured output file metadata."""
        return self.__current_process_config.output_file

    @property
    def output_reports(self) -> List[OutputReport]:
        """Returns the list of configured output reports."""
        return self.__current_process_config.output_reports

    def __init__(self):
        """
        Initializes all shared modules and handles first-time monitoring setup. 
        Raises a DataflowInitializationError on failure.
        """
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
        """
        Abstract method to be implemented by subclasses with the main dataflow logic.
        """
        raise NotImplementedError('It is mandatory to implement process() function')

    def vars(self, name: str):
        """
        Retrieve a named variable from the process configuration.

        Args:
            name (str): Variable name.

        Returns:
            Any: Value associated with the variable.
        """
        return self.__current_process_config.vars.get_variable(name=name)

    def read_table_with_casting(
        self,
        name_of_raw_table: str,
        name_of_staging_table_to_casting: str = None,
        apply_quality_controls: bool = True
    ) -> Any:
        """
        Reads a raw table and applies casting and optional quality controls.

        Args:
            name_of_raw_table (str): Key for the raw input table.
            name_of_staging_table_to_casting (str, optional): Key for the casting config table.
            apply_quality_controls (bool): Whether to validate the data.

        Returns:
            Any: DataFrame after casting and optional quality checks.
        """
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
        """
        Reads a table using current config and applies optional quality controls.

        Args:
            name_of_table (str): Key of the table to read.
            apply_quality_controls (bool): Whether to validate the data.

        Returns:
            Any: DataFrame after reading and optional quality checks.
        """
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
        """
        Writes a DataFrame to a configured target table using merge operation.

        Args:
            df (Any): The data to write.
            output_table_key (str): Key of the target table.
        """
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
        """
        Tracks end-of-process metrics including duration and successful completion.
        """
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

    def save_payload_response(self):
        """
        Assembles and saves the process result payload to AWS SSM.
        Includes output data quality metadata and pending notifications.
        """
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
            self.payload_response.notifications = self.__notifications.get_notifications_to_send()
            payload_json = json.dumps(asdict(self.payload_response), ensure_ascii=False, indent=2)
            self._update_ssm_key(value=payload_json)
        except Exception:
            raise PayloadResponseError()
        
    def _update_ssm_key(self, value: str):
        """
        Stores a string value in AWS SSM Parameter Store with retry logic on specific failures.

        Args:
            value (str): The stringified payload to save.

        Raises:
            SSMError: If the save fails after all retry attempts.
        """
        max_retries = 6
        secure_random = secrets.SystemRandom()

        ssm_name = (
            f'/dataflow/{self.config.project_id}/' +
            f'{self.config.parameters.dataflow}-{self.config.parameters.process}/result'
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


### DF Modules: exception
#### Summary
This module defines a comprehensive set of custom exception classes used throughout the Data Framework. It establishes a clear error hierarchy, starting with a base `DataFrameworkError`, and provides specific exceptions for errors related to configuration, AWS services (S3, Glue, Athena, etc.), data catalogue operations, data processing tasks, dataflow execution, storage operations, validation rules, notifications, and specific processing steps like landing and output generation. This structured approach facilitates specific error handling and provides informative error messages for debugging.

#### What the "exception" module does (big picture)
- **Goal:** To create a standardized and informative system for error handling and reporting within the Data Framework.
- **Why it matters:** Using custom exceptions instead of generic ones allows for more precise error identification and handling (e.g., distinguishing a configuration error from a data processing error). It leads to clearer logs, easier debugging, and more robust application flow control by allowing different types of errors to be caught and managed specifically.
- **How:** It defines a base exception class, `DataFrameworkError` (in `generic_exceptions.py`), which includes a helpful `format_exception` method to generate detailed error reports including tracebacks. Numerous specific exception classes are then defined across multiple files, each inheriting directly or indirectly from `DataFrameworkError`. These specific exceptions correspond to different framework modules (e.g., `config_exceptions.py`, `storage_exceptions.py`) or specific error conditions (e.g., `FileNotFoundError`, `ReadDataError`, `RuleComputeError`). The `__init__` methods of these exceptions typically accept context-specific arguments (like filenames, table names, error details) to generate tailored and informative error messages.

#### exception Module High-Level: Script by Script

| Script                        | What it is              | What it does |
| :---------------------------- | :---------------------- | :----------- |
| `aws_exceptions.py`           | Exception Definitions   | Defines exceptions specific to interactions with AWS services (STS, Glue, Athena, SSM, S3). |
| `catalogue_exceptions.py`     | Exception Definitions   | Defines exceptions related to data catalogue operations (creating partitions, getting schemas). |
| `config_exceptions.py`        | Exception Definitions   | Defines exceptions related to loading, parsing, and accessing framework configuration. |
| `data_process_exceptions.py`  | Exception Definitions   | Defines exceptions related to data processing tasks (transformations, casting, reading/writing data, Spark issues). |
| `dataflow_exceptions.py`      | Exception Definitions   | Defines exceptions related to the initialization and execution of dataflow processes. |
| `generic_exceptions.py`       | Base Exception Definition | Defines the base `DataFrameworkError` class for all framework exceptions and includes a generic logger error. |
| `landing_exceptions.py`       | Exception Definitions   | Defines exceptions specific to the landing dataflow process (file processing, reading, date regex issues, validation failures). |
| `notification_exceptions.py`  | Exception Definitions   | Defines exceptions related to sending and configuring notifications (not found, duplication, limits exceeded, send errors). |
| `output_exceptions.py`        | Exception Definitions   | Defines exceptions specific to the output generation process (general errors, specific generation failures, no data found). |
| `storage_exceptions.py`       | Exception Definitions   | Defines exceptions related to data storage operations (generic errors, read failures, write failures). |
| `validation_exceptions.py`    | Exception Definitions   | Defines exceptions related to data quality and validation rules (general errors, failed rules, computation errors, configuration issues). |

---
#### src/data_framework/modules/exception/aws_exceptions.py
#### `src/data_framework/modules/exception/aws_exceptions.py`
**Purpose:**
This script defines custom exception classes for errors encountered during interactions with various AWS services used by the framework.

**Key parts:**
- **`STSError(DataFrameworkError)`:** Error related to AWS Security Token Service (STS).
- **`GlueError(DataFrameworkError)`:** Error related to AWS Glue (e.g., catalog operations).
- **`AthenaError(DataFrameworkError)`:** Error related to AWS Athena (e.g., query execution).
- **`SSMError(DataFrameworkError)`:** Error related to AWS Systems Manager Parameter Store (SSM).
- **`S3Error(DataFrameworkError)`:** Error related to AWS Simple Storage Service (S3).

---

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

#### src/data_framework/modules/exception/catalogue_exceptions.py
#### `src/data_framework/modules/exception/catalogue_exceptions.py`
**Purpose:**
This script defines custom exceptions specifically related to operations within the data catalogue module, such as interacting with table schemas and partitions (likely in AWS Glue).

**Key parts:**
- **`CreatePartitionError(DataFrameworkError)`:** Raised when creating a partition in a table fails. Includes database, table, partition field, and value in the message.
- **`InvalidPartitionFieldError(DataFrameworkError)`:** Raised when a specified partition field does not exist in the target table's schema. Includes database, table, and partition field.
- **`SchemaError(DataFrameworkError)`:** Raised when fetching or processing a table's schema fails. Includes database and table name.

---

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

#### src/data_framework/modules/exception/config_exceptions.py
#### `src/data_framework/modules/exception/config_exceptions.py`
**Purpose:**
This script defines a suite of custom exceptions related to errors encountered during the loading, parsing, or accessing of configuration files and parameters for the framework and specific dataflows.

**Key parts:**
- **`ConfigError(DataFrameworkError)`:** A base error for configuration issues, typically related to initialization.
- **`ConfigFileNotFoundError(DataFrameworkError)`:** Raised when the main configuration file cannot be found at the specified path.
- **`AccountNotFoundError(DataFrameworkError)`:** Raised when the target AWS Account ID is not defined in the configuration. Lists available IDs.
- **`ParameterParseError(DataFrameworkError)`:** Raised when input command-line arguments cannot be parsed correctly.
- **`ConfigParseError(DataFrameworkError)`:** Raised during config file parsing if a field cannot be converted to its expected type. Includes field name and expected type.
- **`EmptyProcessConfigError(DataFrameworkError)`:** Raised if the configuration section for a specific process is unexpectedly empty.
- **`ProcessNotFoundError(DataFrameworkError)`:** Raised when a requested process name isn't found in the configuration. Lists available processes.
- **`TableKeyError(DataFrameworkError)`:** Raised when a specific table key (used for referencing table configurations) is not found. Lists available keys.
- **`TableConfigNotFoundError(DataFrameworkError)`:** Raised when configuration details for a specific database and table combination cannot be located using table keys.

---

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

#### src/data_framework/modules/exception/data_process_exceptions.py
#### `src/data_framework/modules/exception/data_process_exceptions.py`
**Purpose:**
This script defines custom exceptions related to errors occurring during data processing operations, such as applying transformations, casting data types, reading from or writing to tables, and Spark-specific issues.

**Key parts:**
- **`TransformationNotImplementedError(DataFrameworkError)`:** Raised if a specified transformation type is not recognized or implemented. Can list available transformations.
- **`TransformationError(DataFrameworkError)`:** Raised when an implemented transformation fails during execution.
- **`CastQueryError(DataFrameworkError)`:** Raised specifically when generating the SQL query for data casting fails.
- **`CastDataError(DataFrameworkError)`:** Raised when the overall data casting operation between source and target tables fails. Includes table names and casting strategy.
- **`ReadDataError(DataFrameworkError)`:** Raised when reading data (e.g., via a SQL query) fails. Includes the attempted query.
- **`WriteDataError(DataFrameworkError)`:** Raised when writing data to a target table fails. Includes database and table name.
- **`DeleteDataError(DataFrameworkError)`:** Raised when deleting data from a table fails. Includes database and table name.
- **`DataProcessError(DataFrameworkError)`:** A more generic error for data transformation failures, allowing a custom message.
- **`SparkConfigurationError(DataFrameworkError)`:** Raised specifically when setting up or configuring the Spark session fails.

---

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


```

#### src/data_framework/modules/exception/dataflow_exceptions.py
#### `src/data_framework/modules/exception/dataflow_exceptions.py`
**Purpose:**
This script defines custom exceptions related to the overall execution and initialization of dataflow processes defined by `DataFlowInterface`.

**Key parts:**
- **`DataflowInitializationError(DataFrameworkError)`:** Raised if the initialization (`__init__`) of a `DataFlowInterface` subclass fails.
- **`PayloadResponseError(DataFrameworkError)`:** Raised if generating the final JSON payload response for the dataflow process fails.

---

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

#### src/data_framework/modules/exception/generic_exceptions.py
#### `src/data_framework/modules/exception/generic_exceptions.py`
**Purpose:**
This script defines the foundational base exception class for the entire Data Framework and a generic exception for logger issues.

**Key parts:**
- **`DataFrameworkError(Exception)`:**
    - The root base class for all custom exceptions defined within the framework. Inherits from Python's built-in `Exception`.
    - **`format_exception(self) -> str`:** A method to provide a formatted string representation of the exception, including its type, message, and the full traceback. This is useful for detailed logging.
- **`LoggerInitializationError(DataFrameworkError)`:** Raised specifically if the framework's logger fails to initialize correctly.

---

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

#### src/data_framework/modules/exception/landing_exceptions.py
#### `src/data_framework/modules/exception/landing_exceptions.py`
**Purpose:**
This script defines custom exceptions specifically tailored to errors that can occur during the landing dataflow process (ingesting files into the Raw layer).

**Key parts:**
- **`FileProcessError(DataFrameworkError)`:** A general error raised when processing a specific file fails. Includes the file path.
- **`FileReadError(DataFrameworkError)`:** Raised when reading the content of a file from storage fails. Includes the file path.
- **`InvalidDateRegexError(DataFrameworkError)`:** Raised if the date pattern extracted from a filename does not match the configured regex. Includes filename and pattern.
- **`InvalidRegexGroupError(DataFrameworkError)`:** Raised if the named groups (`year`, `month`, `day`) within the date regex are missing or incorrect. Includes the pattern.
- **`InvalidFileError(DataFrameworkError)`:** Raised if a file fails the defined quality control checks during the landing process. Includes the file path.

---

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

#### src/data_framework/modules/exception/notification_exceptions.py
#### `src/data_framework/modules/exception/notification_exceptions.py`
**Purpose:**
This script defines custom exceptions related to the configuration and sending of notifications within the framework.

**Key parts:**
- **`NotificationNotFoundError(DataFrameworkError)`:** Raised when attempting to use or send a notification key that is not defined in the configuration. Can list available keys.
- **`DuplicatedNotificationError(DataFrameworkError)`:** Raised if the user configuration defines a notification with a name that conflicts with a pre-defined framework notification. Lists the duplicated names.
- **`NotificationError(DataFrameworkError)`:** A general error raised when sending a specific notification fails. Includes the notification name.
- **`SubjectLimitExceededError(DataFrameworkError)`:** Raised if the generated subject line for a notification exceeds the maximum allowed length. Includes notification name and max length.
- **`BodyLimitExceededError(DataFrameworkError)`:** Raised if the generated body content for a notification exceeds the maximum allowed length. Includes notification name and max length.
- **`NotificationLimitExceededError(DataFrameworkError)`:** Raised if the total number of notifications queued to be sent exceeds a predefined limit. Includes the limit number.

---

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

#### src/data_framework/modules/exception/output_exceptions.py
#### `src/data_framework/modules/exception/output_exceptions.py`
**Purpose:**
This script defines custom exceptions specifically for errors occurring during the output generation dataflow process.

**Key parts:**
- **`OutputError(DataFrameworkError)`:** A general error raised when one or more output files fail to generate. Lists the names of the failed outputs.
- **`OutputGenerationError(DataFrameworkError)`:** Raised when a specific output file generation fails. Includes the output name and the underlying error message.
- **`NoOutputDataError(DataFrameworkError)`:** Raised when the data retrieval step for a specific output yields no data, preventing file generation. Includes the output name.

---

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

#### src/data_framework/modules/exception/storage_exceptions.py
#### `src/data_framework/modules/exception/storage_exceptions.py`
**Purpose:**
This script defines custom exceptions related to interactions with the underlying storage systems (like local storage or S3 via the Storage module).

**Key parts:**
- **`StorageError(DataFrameworkError)`:** A generic error for storage operations, taking a custom error message.
- **`StorageReadError(DataFrameworkError)`:** Raised specifically when a read operation from storage fails. Includes the path being read.
- **`StorageWriteError(DataFrameworkError)`:** Raised specifically when a write operation to storage fails. Includes the path being written to.

---

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

#### src/data_framework/modules/exception/validation_exceptions.py
#### `src/data_framework/modules/exception/validation_exceptions.py`
**Purpose:**
This script defines custom exceptions related to the data validation and quality control processes within the framework.

**Key parts:**
- **`QualityControlsError(DataFrameworkError)`:** A general error raised when data validation for a specific table fails. Includes the table name.
- **`FailedRulesError(DataFrameworkError)`:** Raised when one or more quality rules could not be executed correctly (distinct from rules failing their checks). Includes the count of failed rules.
- **`RuleComputeError(DataFrameworkError)`:** Raised when the computation logic for a specific rule fails during execution. Includes rule ID and type.
- **`ValidationFunctionNotFoundError(DataFrameworkError)`:** Raised if a custom validation function specified in the configuration cannot be found in the designated parent module/class.
- **`ParentNotConfiguredError(DataFrameworkError)`:** Raised if custom validation functions are attempted without first setting the parent module/class using `QualityControls().set_parent()`.
- **`InvalidThresholdError(DataFrameworkError)`:** Raised if the threshold value defined for a validation rule is invalid (e.g., wrong type, outside expected range). Takes a specific error message.
- **`InvalidAlgorithmError(DataFrameworkError)`:** Raised if the algorithm specified for a rule is not valid or recognized. Takes a specific error message.
- **`InvalidRuleError(DataFrameworkError)`:** Raised if the definition or configuration of a validation rule itself is invalid. Takes a specific error message.
- **`InvalidDataFrameError(DataFrameworkError)`:** Raised if the DataFrame provided for validation is invalid (e.g., missing expected columns). Takes a specific error message.

```python

"""Definition of exceptions for validation module"""

from data_framework.modules.exception.generic_exceptions import DataFrameworkError


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


### DF Modules: monitoring
#### Summary
This module provides the framework's monitoring capabilities. It defines a standard interface (`MonitoringInterface`) and data structures (`Metric`, `MetricNames`, `MetricUnits`) for tracking operational metrics. A core facade (`CoreMonitoring`) dynamically loads and delegates to specific implementations, with the initial implementation targeting AWS CloudWatch (`AWSCloudWatch`). This allows tracking of metrics related to dataflow processes, table operations (reads/writes), and computational resources, sending them to CloudWatch with standardized dimensions.

#### What the "monitoring" module does (big picture)
- **Goal:** To provide a consistent mechanism for capturing and reporting key operational metrics from Data Framework processes to a central monitoring system (initially AWS CloudWatch).
- **Why it matters:** Enables observability into the performance, health, and resource usage of data pipelines built with the framework. Standardized metrics facilitate dashboard creation, alerting, and operational analysis.
- **How:**
    - It defines an abstract base class `MonitoringInterface` with methods (`track_metric`, `track_table_metric`, `track_process_metric`, `track_computation_metric`) that concrete monitoring backends must implement.
    - It standardizes metric definitions using the `Metric` dataclass, the `MetricNames` enum (which also logically maps names to `MetricUnits`), and the `MetricUnits` enum.
    - A facade class `CoreMonitoring` uses lazy loading (`LazyClassProperty`) to instantiate the appropriate `MonitoringInterface` implementation (currently hardcoded to `AWSCloudWatch`). Other framework components interact with `CoreMonitoring`.
    - The `AWSCloudWatch` implementation formats the standard `Metric` objects into the structure required by the AWS CloudWatch `put_metric_data` API call. It defines a standard CloudWatch namespace (`DataFramework/Product`) and constructs dimensions based on framework configuration (`ProjectId`, `DataFlow`, `Process`) and environment variables (`EMR_SERVERLESS_JOB_ID`, `EMR_SERVERLESS_JOB_RUN_ID`). It uses `boto3` to interact with CloudWatch. *(Note: The final call to `put_metric_data` is currently commented out in the provided code).*

#### monitoring Module High-Level: Script by Script

| Script                                                             | What it is             | What it does |
| :----------------------------------------------------------------- | :--------------------- | :----------- |
| `core_monitoring.py`                                               | Core Logic/Facade      | Provides the main entry point for monitoring actions, dynamically loading and delegating metric tracking calls to the configured monitoring implementation. |
| `integrations/aws_cloudwatch/aws_cloudwatch_monitoring.py`         | Integration (CloudWatch) | Implements the `MonitoringInterface` to send formatted metrics to AWS CloudWatch using boto3, defining specific namespaces and dimensions. |
| `interface_monitoring.py`                                          | Interface Definition   | Defines the abstract base class (`MonitoringInterface`), metric data structures (`Metric`, `MetricNames`, `MetricUnits`), and standard metric names/units used across the framework. |

---
#### src/data_framework/modules/monitoring/core_monitoring.py
#### `src/data_framework/modules/monitoring/core_monitoring.py`
**Purpose:**
This script acts as the central facade for the monitoring module. It provides class methods that other parts of the framework can use to track metrics without needing to know the specifics of the underlying monitoring system implementation. It dynamically loads the configured monitoring backend.

**Key parts:**
- **`CoreMonitoring` class:** The main facade class.
- **`_monitoring` (LazyClassProperty):** Lazily initializes and returns an instance of the concrete `MonitoringInterface` implementation. Currently hardcoded to load `AWSCloudWatch`.
- **`track_metric(cls, metric: Metric)`:** Delegates the tracking of a generic `Metric` object to the loaded monitoring implementation.
- **`track_table_metric(cls, name: MetricNames, ...)`:** Convenience method to track metrics specifically related to table operations (e.g., reads, writes). Delegates to the implementation.
- **`track_process_metric(cls, name: MetricNames, ...)`:** Convenience method to track metrics related to dataflow process execution (e.g., start/end events, duration). Delegates to the implementation.
- **`track_computation_metric(cls, name: MetricNames, ...)`:** Convenience method to track metrics related to computational aspects (likely within Spark). Delegates to the implementation.

---

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

#### src/data_framework/modules/monitoring/integrations/aws_cloudwatch/aws_cloudwatch_monitoring.py
#### `src/data_framework/modules/monitoring/integrations/aws_cloudwatch/aws_cloudwatch_monitoring.py`
**Purpose:**
This script implements the `MonitoringInterface` to send metrics specifically to AWS CloudWatch. It handles the initialization of the CloudWatch client, defines the namespace and dimensions for the metrics, and formats the metric data according to CloudWatch API requirements.

**Key parts:**
- **`InternalMetric(Metric)` dataclass:** An internal extension of the base `Metric` dataclass with a `parse` property to format the metric data into the dictionary structure required by CloudWatch's `put_metric_data`.
- **`AWSCloudWatch(MonitoringInterface)` class:** Implements the monitoring interface for CloudWatch.
    - **`__namespace` (static attribute):** Defines the CloudWatch namespace (`DataFramework/Product`) where metrics will be sent.
    - **Dimension Attributes (`__computation_dimensions`, `__process_dimensions`, `__table_dimensions`):** Define lists of standard dimensions (like `ExecutionId`, `ProjectId`, `DataFlow`, `Process`, `JobId`) to be included with different types of metrics. Values are pulled from `config()` and environment variables.
    - **`__init__(self)`:** Initializes the `boto3` CloudWatch client, configuring the region and endpoint URL.
    - **`track_metric(self, metric: InternalMetric)`:** The core method that takes a formatted `InternalMetric` and sends it to CloudWatch using `self._client.put_metric_data`. *(Note: This API call is commented out in the provided code).*
    - **`track_table_metric(self, name: MetricNames, ...)`:** Constructs an `InternalMetric` object for table-related metrics, adding `Database` and `Table` dimensions to the standard `__table_dimensions`, and calls `track_metric`.
    - **`track_process_metric(self, name: MetricNames, ...)`:** Constructs an `InternalMetric` for process-related metrics, adding a `Success` dimension if provided, and calls `track_metric`.
    - **`track_computation_metric(self, name: MetricNames, ...)`:** Constructs an `InternalMetric` for computation-related metrics using `__computation_dimensions` and calls `track_metric`.

---

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

#### src/data_framework/modules/monitoring/interface_monitoring.py
#### `src/data_framework/modules/monitoring/interface_monitoring.py`
**Purpose:**
This script defines the abstract interface and common data structures for monitoring within the framework. It ensures that all monitoring implementations adhere to a consistent contract and use standardized metric definitions.

**Key parts:**
- **`MetricUnits(Enum)`:** Defines standard units for metrics (Seconds, Megabytes, Count, Percent, None).
- **`MetricNames(Enum)`:** Defines standard names for metrics used across the framework (e.g., `TABLE_READ_RECORDS`, `PROCESS_DURATION`, `DATAFLOW_START_EVENT`).
    - **`unit` property:** Contains logic to automatically determine the correct `MetricUnits` based on the `MetricNames` enum value.
- **`Metric` dataclass:** Defines the standard structure for a metric, including its creation timestamp (`_created_at`), `name` (MetricNames), `value` (float), and `dimensions` (List of Dicts).
- **`MonitoringInterface(ABC)` class:**
    - Abstract Base Class defining the methods required for any monitoring implementation.
    - **Abstract Methods:** `track_metric`, `track_table_metric`, `track_process_metric`, `track_computation_metric`. These methods define the signatures for tracking different categories of metrics, ensuring implementing classes (like `AWSCloudWatch`) provide the necessary functionality.

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

### DF Modules: Notifications
#### Summary
The Notifications module manages the creation, configuration, validation, and sending of notifications (specifically emails) during data workflows. It supports dynamic notification generation, validation against configurable limits, and adapts message formatting based on the environment.

#### What the "Notifications" module does (big picture)
- **Goal:** Provide a standardized system for preparing and dispatching notifications within data processes.
- **Why it matters:** Ensures stakeholders are informed of process statuses, errors, or key events with configurable, environment-aware messaging.
- **How:** Defines notification interfaces, core logic for managing and validating notifications, and implements notification dispatching through dynamic configurations and a queuing system.

#### Notifications Module High-Level: Script by Script
| Script             | What it is        | What it does |
| :----------------- | :---------------- | :----------- |
| `[core_notifications.py]` | Core Logic | Acts as a facade to send and retrieve notifications, utilizing lazy initialization. |
| `[interface_notifications.py]` | Interface | Defines abstract structures and data models for notifications. |
| `[notifications.py]` | Core Implementation | Manages combining configurations, formatting, validating, and queuing notifications for dispatch. |


#### src/data_framework/modules/notification/core_notifications.py
#### `[core_notifications.py]`
**Purpose:**
Provides a simplified access point to manage notifications by wrapping the `Notifications` class with lazy loading and class methods.

**Key parts:**
- `CoreNotifications` class: Core wrapper exposing `send_notification` and `get_notifications_to_send` methods.
- `LazyClassProperty` usage: Ensures the underlying `Notifications` object is loaded only when needed.

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

#### src/data_framework/modules/notification/interface_notifications.py
#### `[interface_notifications.py]`
**Purpose:**
Defines the data structures, types, and abstract methods necessary for a notifications system to ensure consistency and loose coupling.

**Key parts:**
- `NotificationType` and `Topic` Enums: Standardizes notification types and topics.
- `Notification`, `NotificationToSend`, `NotificationDict`, `NotificationsParameters`, `DataFrameworkNotifications` dataclasses: Define the structure for notifications and related parameters.
- `InterfaceNotifications` ABC: Specifies required methods (`send_notification`, `get_notifications_to_send`) for notification handlers.
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

#### src/data_framework/modules/notification/notifications.py
#### `[notifications.py]`
**Purpose:**
Implements the notification handling logic including combining default and custom notifications, validating content length, formatting messages, and queuing notifications for sending.

**Key parts:**
- `Notifications` class: Implements `InterfaceNotifications`.
- `_combine_notifications`: Merges default and custom notifications safely.
- `send_notification`: Dispatches a notification after formatting and validation.
- `_send_email_notification`, `_format_subject`, `_format_body`: Helper methods for constructing and formatting messages.
- `_validate_subject_length`, `_validate_body_length`: Enforce configured limits on notification size.
- `_add_notification`: Queues a notification, respecting maximum queue size.
- `get_notifications_to_send`: Returns all queued notifications ready to be sent.
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

### DF Modules: storage
#### Summary
This module provides an abstraction layer for interacting with different storage systems. It defines a common interface (`CoreStorageInterface`) for file and object operations like reading, writing, and listing files within predefined logical layers (Landing, Raw, Staging, etc.). It includes concrete implementations for interacting with the local filesystem (`LocalStorage`) and AWS S3 (`S3Storage`).

#### What the "storage" module does (big picture)
- **Goal:** To offer a standardized way to perform read, write, and list operations on data files/objects, irrespective of the underlying storage technology (local disk or cloud storage like S3).
- **Why it matters:** It decouples the rest of the framework from the specific storage implementation details. This allows dataflows and processing logic to remain agnostic to where the data physically resides, making the framework more flexible and adaptable (e.g., switching between local development and cloud deployment). It also enforces consistent path structures using predefined `Layer` and `Database` concepts.
- **How:**
    - It defines an abstract base class `CoreStorageInterface` specifying the essential methods (`read`, `write`, `write_to_path`, `list_files`, `raw_layer_path`, `base_layer_path`) and standard response dataclasses (`ReadResponse`, `WriteResponse`, `ListResponse`, `PathResponse`).
    - It defines enums `Layer` and `Database` to represent logical data zones and schemas, used for constructing paths.
    - Concrete classes `LocalStorage` and `S3Storage` implement the `CoreStorageInterface`.
    - `LocalStorage` uses Python's standard `os` module functions (`os.path.join`, `os.makedirs`, `open`, `os.scandir`) to interact with the local filesystem. It constructs paths based on the configured `bucket_prefix` and layer name.
    - `S3Storage` uses the `boto3` library to interact with AWS S3 (`s3.get_object`, `s3.put_object`, `s3.get_paginator('list_objects_v2')`). It constructs S3 bucket names and object keys based on configuration and the logical layer/database/table/partition structure.

#### storage Module High-Level: Script by Script

| Script                                                        | What it is             | What it does |
| :------------------------------------------------------------ | :--------------------- | :----------- |
| `integrations/local_storage.py`                               | Storage Implementation | Implements the `CoreStorageInterface` for interacting with the local filesystem using standard Python `os` functions. |
| `integrations/s3_storage.py`                                  | Storage Implementation | Implements the `CoreStorageInterface` for interacting with AWS S3 using the `boto3` library. |
| `interface_storage.py`                                        | Interface Definition   | Defines the abstract base class (`CoreStorageInterface`), standard response types, and enums (`Layer`, `Database`) for storage operations. |

---
#### src/data_framework/modules/storage/core_storage.py
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

#### src/data_framework/modules/storage/integrations/local_storage.py
#### `src/data_framework/modules/storage/integrations/local_storage.py`
**Purpose:**
This script provides a concrete implementation of the `CoreStorageInterface` that interacts with the local filesystem. It allows the framework to read, write, and list files on the machine where the code is running, simulating a layered storage structure using local directories.

**Key parts:**
- **`LocalStorage(CoreStorageInterface)` class:** Implements the storage interface.
    - **`__init__(self)`:** Initializes the logger.
    - **`read(self, layer: Layer, key_path: str, ...)`:** Reads a file from the local filesystem. Constructs the full path using `_build_folder_name` and `os.path.join`. Returns `ReadResponse` with file content as bytes if found, or an error response if not.
    - **`_build_folder_name(self, layer: Layer)`:** Creates the base folder name for a layer (e.g., `prefix-landing`, `prefix-raw`) based on configuration.
    - **`_build_file_path(self, database: Database, ...)`:** Constructs the relative file path within a layer's folder, including database, table, optional partition structure (`key=value/`), and filename.
    - **`write(self, layer: Layer, database: Database, ...)`:** Writes data (bytes) to a file in the local filesystem. Uses `_build_folder_name` and `_build_file_path` to determine the full path, creates necessary parent directories (`os.makedirs`), and writes the file. Returns `WriteResponse`.
    - **`write_to_path(self, layer: Layer, key_path: str, ...)`:** Writes data (bytes) directly to a specified relative `key_path` within the appropriate layer folder. Creates parent directories and writes the file. Returns `WriteResponse`.
    - **`list_files(self, layer: Layer, prefix: str)`:** Lists files within a given prefix (directory path) in the corresponding layer folder using `os.scandir`. Returns `ListResponse` containing a list of full file paths.
    - **`raw_layer_path(self, database: Database, ...)`:** Constructs and returns path information (`PathResponse`) for the Raw layer, primarily providing the local `base_path`.
    - **`base_layer_path(self, layer: Layer)`:** Marked as not implemented for local storage. Raises `NotImplementedError`.

---

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

#### src/data_framework/modules/storage/integrations/s3_storage.py
#### `src/data_framework/modules/storage/integrations/s3_storage.py`
**Purpose:**
This script provides a concrete implementation of the `CoreStorageInterface` that interacts with AWS Simple Storage Service (S3). It allows the framework to read, write, and list objects in S3 buckets structured according to the defined layers and database/table/partition conventions.

**Key parts:**
- **`BuildPathResponse(NamedTuple)`:** Internal helper tuple for returning base and data paths/keys.
- **`S3Storage(CoreStorageInterface)` class:** Implements the storage interface for S3.
    - **`__init__(self)`:** Initializes the `boto3` S3 client using the region from the configuration.
    - **`read(self, layer: Layer, key_path: str, ...)`:** Reads an object from S3. Determines the bucket using `_build_s3_bucket_name`, calls `s3.get_object`, and returns the object's body content as bytes in a `ReadResponse`. Handles S3-specific errors.
    - **`_build_s3_bucket_name(self, layer: Layer)`:** Constructs the S3 bucket name based on the configured `bucket_prefix` and the layer name (e.g., `prefix-landing`, `prefix-raw`).
    - **`_build_s3_key_path(self, database: Database, ...)`:** Constructs the S3 object key (path within the bucket), including database, table, optional partition structure (`key=value/`), and filename. Returns a `BuildPathResponse`.
    - **`write(self, layer: Layer, database: Database, ...)`:** Writes data (bytes) to an S3 object. Determines bucket and key using helper methods and calls `s3.put_object`. Returns `WriteResponse`. Handles S3-specific errors.
    - **`write_to_path(self, layer: Layer, key_path: str, ...)`:** Writes data (bytes) directly to a specified `key_path` within the appropriate S3 bucket. Calls `s3.put_object`. Returns `WriteResponse`.
    - **`list_files(self, layer: Layer, prefix: str)`:** Lists object keys within a given S3 bucket and prefix using `s3.get_paginator('list_objects_v2')` to handle potential pagination. Returns `ListResponse` containing a list of object keys.
    - **`raw_layer_path(self, database: Database, ...)`:** Constructs and returns path information (`PathResponse`) for accessing data in the Raw layer on S3, considering execution mode for partitioning. Provides S3 URIs (`s3://...`) and relative paths.
    - **`base_layer_path(self, layer: Layer)`:** Constructs and returns path information (`PathResponse`) representing the base S3 URI for a given layer (e.g., `s3://prefix-landing`).

---

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
            partitions[config().processes.landing_to_raw.output_file.partition_field] = config().parameters.file_date

        s3_key = self._build_s3_key_path(database=database, table=table_name, partitions=partitions)

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

#### src/data_framework/modules/storage/interface_storage.py
#### `src/data_framework/modules/storage/interface_storage.py`s
**Purpose:**
This script defines the abstract interface and common data structures for storage operations within the framework. It establishes a contract that all concrete storage implementations (like `LocalStorage` or `S3Storage`) must follow, ensuring consistency. It also defines standard logical groupings for data (Layers and Databases).

**Key parts:**
- **`Layer(Enum)`:** Defines standard logical data layers (LANDING, RAW, STAGING, COMMON, BUSINESS, OUTPUT, ATHENA, TEMP). Values correspond to path components.
- **`Database(Enum)`:** Defines standard logical database names used within layers (e.g., FUNDS_RAW, FUNDS_STAGING, DATA_QUALITY). Values correspond to path components. Includes some potentially legacy/specific database names.
- **`ReadResponse` dataclass:** Standard structure for returning data from read operations (`success`, `error`, `data` as bytes).
- **`WriteResponse` dataclass:** Standard structure for reporting the outcome of write operations (`success`, `error`).
- **`ListResponse` dataclass:** Standard structure for returning results from list operations (`success`, `error`, `result` as list).
- **`PathResponse` dataclass:** Standard structure for returning path-related information (`success`, `error`, `bucket`, various path representations).
- **`CoreStorageInterface(ABC)` class:**
    - Abstract Base Class defining the required methods for storage interactions.
    - **Abstract Methods:** `read`, `write`, `write_to_path`, `list_files`, `raw_layer_path`, `base_layer_path`. These methods define the expected signatures, ensuring implementing classes provide the necessary functionality for interacting with storage in a standardized way.

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


### DF Modules: utils
#### Summary
This module provides a collection of general-purpose utilities and shared components used throughout the Data Framework. It includes a simple mechanism for conditional debugging based on environment configuration, a standardized singleton logger setup for consistent logging across the framework, and helper functions leveraging regular expressions for common string manipulations.

#### What the "utils" module does (big picture)
- **Goal:** To centralize common, reusable functionalities like logging, debugging checks, and string operations, supporting various other modules within the framework.
- **Why it matters:** Consolidating these utilities prevents code duplication, ensures consistent logging behavior and formatting, provides a simple way to manage debug-only code paths, and offers reliable string manipulation helpers.
- **How:**
    - `debug.py` defines a lambda function `debug_code` that checks the global framework configuration (`config().environment`) to return `True` if the environment is *not* `PRODUCTION`, offering a simple flag for conditional logic.
    - `logger.py` implements a thread-safe singleton `Logger` class to configure and provide a globally accessible standard Python logger instance (`logger`). It sets a predefined format (`LOG_FORMAT`, `DATE_FORMAT`) and configures a console handler (`StreamHandler`) at the `INFO` level.
    - `regex.py` contains utility functions built using Python's `re` module. Currently, it includes `change_file_extension` for safely modifying filename extensions.

#### utils Module High-Level: Script by Script

| Script      | What it is             | What it does |
| :---------- | :--------------------- | :----------- |
| `debug.py`  | Debug Flag Utility     | Provides a simple flag (`debug_code`) that returns true if the configured environment is not Production. |
| `logger.py` | Logging Configuration  | Sets up and provides a thread-safe, globally accessible singleton logger instance with standard formatting and console output. |
| `regex.py`  | Regex Utilities        | Contains helper functions that use regular expressions for string manipulation, such as changing file extensions. |

---
#### src/data_framework/modules/utils/debug.py
#### `src/data_framework/modules/utils/debug.py`
**Purpose:**
This script provides a straightforward way to check if the framework is running in a non-production environment, typically used to enable or disable debugging-specific code execution (e.g., extra logging, showing dataframes).

**Key parts:**
- **`debug_code` (lambda function):** Checks `config().environment` from the core configuration module. Returns `True` if the environment is not equal to `Environment.PRODUCTION`, `False` otherwise.

---

```python

from data_framework.modules.config.core import config
from data_framework.modules.config.model.flows import Environment

debug_code = lambda: config().environment != Environment.PRODUCTION


```

#### src/data_framework/modules/utils/logger.py
#### `src/data_framework/modules/utils/logger.py`
**Purpose:**
This script configures and provides a standardized, globally accessible logger instance for use throughout the Data Framework. It ensures consistent log formatting and output handling using a singleton pattern.

**Key parts:**
- **`LOG_FORMAT` (constant):** Defines the standard format string for log messages, including timestamp, level, filename, function name, line number, and message.
- **`DATE_FORMAT` (constant):** Defines the format for timestamps within log messages.
- **`Logger` class (Singleton):**
    - Uses a thread-safe lock (`_lock`) and class variable (`_instance`) to ensure only one instance is created.
    - In its `__new__` method, it configures the root logger (named "DF"):
        - Sets the logging level to `INFO`.
        - Creates a `StreamHandler` to output logs to `sys.stdout`.
        - Creates a `Formatter` using `LOG_FORMAT` and `DATE_FORMAT`.
        - Adds the configured handler to the logger.
        - Stores the logger instance in `cls._instance.logger`.
    - Raises `LoggerInitializationError` if any part of the setup fails.
- **`logger` (global instance):** A globally accessible variable holding the configured logger instance obtained from the `Logger` singleton. Other modules can import and use this `logger` directly.

---

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

#### src/data_framework/modules/utils/regex.py
#### `src/data_framework/modules/utils/regex.py`
**Purpose:**
This script provides reusable utility functions that leverage regular expressions for common string manipulation tasks, particularly useful for handling filenames and potentially other text processing needs within the framework.

**Key parts:**
- **`change_file_extension(filename: str, new_extension: str) -> str` function:**
    - Takes a filename string and a desired new extension string.
    - Validates that the `new_extension` looks like a valid extension (starts with '.' followed by word characters). Prepends '.' if missing. Raises `ValueError` if invalid.
    - Uses `re.sub` with a pattern (`r'\.\w+$'`) to find and replace the existing extension (a dot followed by one or more word characters at the end of the string) with the `new_extension`.
    - Returns the modified filename string.

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


```

### DF Modules: Validations
#### src/data_framework/modules/validation/core_quality_controls.py
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
        **kwargs: Dict[str, Any]
    ) -> ControlsResponse:
        return cls._quality_controls.validate(
            layer=layer,
            table_config=table_config,
            df_data=df_data,
            **kwargs
        )

    @classmethod
    def set_parent(cls, parent: Any) -> None:
        return cls._quality_controls.set_parent(parent=parent)


```

#### src/data_framework/modules/validation/integrations/file_validator.py
```python

from data_framework.modules.config.core import config
from data_framework.modules.utils.logger import logger
from data_framework.modules.catalogue.core_catalogue import CoreCatalogue
from data_framework.modules.validation.core_quality_controls import CoreQualityControls
from data_framework.modules.data_process.core_data_process import CoreDataProcess
from data_framework.modules.validation.interface_quality_controls import ControlRule
import re
from io import BytesIO
from pathlib import Path
import pandas as pd


class FileValidator:

    def __init__(self, file_date: str, file_contents: dict, source_file_path: str):
        self.config = config()
        self.current_process_config = self.config.current_process_config()
        self.logger = logger
        self.catalogue = CoreCatalogue()
        self.quality_controls = CoreQualityControls()
        self.data_process = CoreDataProcess()
        self.incoming_file_config = self.current_process_config.incoming_file
        self.output_file_config = self.current_process_config.output_file
        self.file_contents = file_contents
        self.file_name = Path(source_file_path).name
        self.file_date = file_date

    def validate_filename_pattern(self, rule: ControlRule) -> None:
        pattern = self.incoming_file_config.filename_pattern
        valid_filename = bool(re.match(pattern, self.file_name))
        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': [self.file_name], 'result': [valid_filename]})
        ).data
        result = rule.calculate_result(df_result)
        if result.valid_identifiers:
            rule.result.add_detail(f'Valid filenames: {self.file_name}')
        else:
            rule.result.add_detail(f'Invalid filenames: {self.file_name}. Expected pattern: {pattern}')
        rule.result.set_data_date(self.file_date)

    def validate_unzipped_filename_pattern(self, rule: ControlRule) -> None:
        pattern = self.incoming_file_config.filename_unzipped_pattern
        results = []
        filenames = []
        for filename, file_data in self.file_contents.items():
            if file_data['validate']:
                valid_filename = bool(re.match(pattern, filename))
                results.append(valid_filename)
                filenames.append(filename)

        df_result = self.data_process.create_dataframe(
            pd.DataFrame({'identifier': filenames, 'result': results})
        ).data
        result = rule.calculate_result(df_result)
        if result.valid_identifiers:
            rule.result.add_detail(f"Valid filenames: {', '.join(result.valid_identifiers)}")
        if result.invalid_identifiers:
            rule.result.add_detail(f"Invalid filenames: {', '.join(result.invalid_identifiers)}")
            rule.result.add_detail(f'Expected pattern: {pattern}')
        rule.result.set_data_date(self.file_date)

    def validate_csv_format(self, rule: ControlRule) -> None:
        delimiter = self.incoming_file_config.csv_specs.delimiter
        header = self.incoming_file_config.csv_specs.header_position
        encoding = self.incoming_file_config.csv_specs.encoding
        results = []
        filenames = []
        for filename, file_data in self.file_contents.items():
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
        result = rule.calculate_result(df_result)
        if result.valid_identifiers:
            rule.result.add_detail(f"CSV files with valid format: {', '.join(result.valid_identifiers)}")
        if result.invalid_identifiers:
            rule.result.add_detail(f"CSV files with invalid format: {', '.join(result.invalid_identifiers)}")
            rule.result.add_detail(f'Expected separator: {delimiter}')
            rule.result.add_detail(f'Expected header position: {header}')
            rule.result.add_detail(f'Expected encoding: {encoding}')
        rule.result.set_data_date(self.file_date)

    def validate_csv_columns(self, rule: ControlRule) -> None:
        # Obtain expected columns from raw table
        response = self.catalogue.get_schema(
            self.output_file_config.database_relation,
            self.output_file_config.table
        )
        expected_columns = response.schema.get_column_names(partitioned=False)
        results = []
        filenames = []
        invalid_columns_info = []
        for filename, file_data in self.file_contents.items():
            if file_data['validate']:
                file_data['content'].seek(0)
                try:
                    df = pd.read_csv(
                        file_data['content'],
                        dtype=str,
                        delimiter=self.incoming_file_config.csv_specs.delimiter,
                        header=self.incoming_file_config.csv_specs.header_position,
                        encoding=self.incoming_file_config.csv_specs.encoding
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
        result = rule.calculate_result(df_result)
        if result.valid_identifiers:
            rule.result.add_detail(f"CSV files with valid columns: {', '.join(result.valid_identifiers)}")
        if result.invalid_identifiers:
            rule.result.add_detail(f"CSV files with invalid columns: {', '.join(invalid_columns_info)}")
        rule.result.set_data_date(self.file_date)

    def _get_expected_number_of_columns(self, csv_content: BytesIO) -> int:
        csv_content.seek(0)
        header_position = self.incoming_file_config.csv_specs.header_position
        for i, line in enumerate(csv_content):
            if i == header_position:
                encoding = self.incoming_file_config.csv_specs.encoding
                delimiter = self.incoming_file_config.csv_specs.delimiter
                return len(line.decode(encoding).split(delimiter))

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

#### src/data_framework/modules/validation/integrations/quality_controls.py
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
from data_framework.modules.exception.validation_exceptions import (
    QualityControlsError,
    FailedRulesError,
    ValidationFunctionNotFoundError,
    ParentNotConfiguredError,
    RuleComputeError
)
from typing import Any, Dict
import pandas as pd


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

    def set_parent(self, parent: Any) -> None:
        self.parent = parent

    def validate(
        self,
        layer: Layer,
        table_config: DatabaseTable,
        df_data: Any = None,
        **kwargs: Dict[str, Any]
    ) -> ControlsResponse:
        try:
            self.logger.info(f'Validating table {table_config.full_name}')
            df_rules = self._get_active_rules(layer, table_config)
            if self.data_process.is_empty(df_rules):
                self.logger.info(f'No rules defined for table {table_config.full_name}')
                response = ControlsResponse(
                    success=True,
                    error=None,
                    table=table_config,
                    data=df_data,
                    rules=None,
                    results=None,
                    overall_result=True
                )
            else:
                df_results = self._compute_rules(df_data, df_rules, **kwargs)
                if df_results is not None:
                    self._insert_results(df_results)
                    overall_result = self._get_overall_result(df_rules, df_results)
                    response = ControlsResponse(
                        success=True,
                        error=None,
                        table=table_config,
                        data=df_data,
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
                        data=df_data,
                        rules=df_rules,
                        results=None,
                        overall_result=True
                    )
        except Exception as e:
            response = ControlsResponse(
                success=False,
                error=e,
                table=table_config,
                data=df_data,
                rules=None,
                results=None,
                overall_result=False
            )
            raise QualityControlsError(table_name=table_config.full_name)
        return response

    def _get_active_rules(self, layer: Layer, table_config: DatabaseTable) -> Any:
        response_rules = self.data_process.read_table(
            self.dataset_table.table_config.database_relation,
            self.dataset_table.table_config.table,
            self.dataset_table.filter_rules(layer, table_config),
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

    def _compute_rules(self, df_data: Any, df_rules: Any, **kwargs: Dict[str, Any]) -> Any:
        if debug_code:
            self.logger.info('Controls definition:')
            self.data_process.show_dataframe(df_rules)
        if self.technology == Technologies.EMR:
            # Transform into Pandas dataframe
            df_rules = df_rules.toPandas()
        # Compute all rules
        df_results = df_rules.apply(self._compute_generic_rule, axis=1, args=(df_data,), **kwargs)
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

    def _compute_generic_rule(self, rule_definition: pd.Series, df_data: Any, **kwargs: Dict[str, Any]) -> pd.Series:
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
                self._compute_python_rule(rule, df_data, **kwargs)
            elif rule.algorithm.algorithm_type == AlgorithmType.REGEX.value:
                self._compute_regex_rule(rule, df_data)
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

    def _compute_python_rule(self, rule: ControlRule, df_data: Any, **kwargs: Dict[str, Any]) -> None:
        if not self.parent:
            raise ParentNotConfiguredError()
        try:
            function_name = rule.algorithm.algorithm_description
            validation_function = getattr(self.parent, function_name)
            if rule.level == ControlLevel.DATA.value:
                validation_function(rule, df_data, **kwargs)
            elif rule.level == ControlLevel.FILE.value:
                validation_function(rule, **kwargs)
        except AttributeError:
            raise ValidationFunctionNotFoundError(function_name=function_name)

    def _compute_regex_rule(self, rule: ControlRule, df_data: Any) -> None:
        pattern = rule.algorithm.algorithm_description
        columns = rule.field_list
        target_columns = ['identifier', 'value']
        response = self.data_process.stack_columns(df_data, columns, target_columns)
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

#### src/data_framework/modules/validation/interface_quality_controls.py
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
    def filter_rules(layer: Layer, table_config: DatabaseTable) -> str:
        return f"""
            control_database = '{table_config.database.value}' AND
            control_table = '{table_config.table}' AND
            control_layer = '{layer.value}' AND
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

    def set_data_date(self, custom_data_date: datetime = None) -> None:
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
        return cls(
            master_id=rule['control_master_id'],
            table_id=rule['control_table_id'],
            layer=rule['control_layer'],
            database_table=DatabaseTable(
                database=rule['control_database'],
                table=rule['control_table']
            ),
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
        **kwargs: Dict[str, Any]
    ) -> ControlsResponse:
        pass

    @abstractmethod
    def set_parent(self, parent: Any) -> None:
        pass


```

## The Data Framework: Tests.
#### src/data_framework/tests/test_utils.py
```python

import pytest
from data_framework.modules.utils.logger import Logger

def test_logger():
    logger = Logger()
    print("dsds")
    assert -1 == "Negative"


```

## Launcher
#### src/launchers/emr_launcher.py
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

## tools/init.py
```python

for root, dirs, files in os.walk('.\\data_framework\\src\\'):
    if '__init__.py' not in files:
        init_file = os.path.join(root, '__init__.py')
        open(init_file, 'a').close()

```