# Table of Contents

* [data\_framework.dataflow.business](#data_framework.dataflow.business)
  * [DataFlowInterface](#data_framework.dataflow.business.DataFlowInterface)
  * [StagingToBusiness](#data_framework.dataflow.business.StagingToBusiness)
    * [\_\_init\_\_](#data_framework.dataflow.business.StagingToBusiness.__init__)
  * [CommonToBusiness](#data_framework.dataflow.business.CommonToBusiness)
    * [\_\_init\_\_](#data_framework.dataflow.business.CommonToBusiness.__init__)
* [data\_framework.dataflow.common](#data_framework.dataflow.common)
  * [DataFlowInterface](#data_framework.dataflow.common.DataFlowInterface)
  * [StagingToCommon](#data_framework.dataflow.common.StagingToCommon)
    * [\_\_init\_\_](#data_framework.dataflow.common.StagingToCommon.__init__)
* [data\_framework.dataflow.landing](#data_framework.dataflow.landing)
  * [DataFlowInterface](#data_framework.dataflow.landing.DataFlowInterface)
  * [ExecutionMode](#data_framework.dataflow.landing.ExecutionMode)
  * [DateLocated](#data_framework.dataflow.landing.DateLocated)
  * [LandingFileFormat](#data_framework.dataflow.landing.LandingFileFormat)
  * [Storage](#data_framework.dataflow.landing.Storage)
  * [CoreCatalogue](#data_framework.dataflow.landing.CoreCatalogue)
  * [Layer](#data_framework.dataflow.landing.Layer)
  * [FileValidator](#data_framework.dataflow.landing.FileValidator)
  * [FileProcessError](#data_framework.dataflow.landing.FileProcessError)
  * [FileReadError](#data_framework.dataflow.landing.FileReadError)
  * [InvalidDateRegexError](#data_framework.dataflow.landing.InvalidDateRegexError)
  * [InvalidRegexGroupError](#data_framework.dataflow.landing.InvalidRegexGroupError)
  * [InvalidFileError](#data_framework.dataflow.landing.InvalidFileError)
  * [regex\_utils](#data_framework.dataflow.landing.regex_utils)
  * [re](#data_framework.dataflow.landing.re)
  * [hashlib](#data_framework.dataflow.landing.hashlib)
  * [json](#data_framework.dataflow.landing.json)
  * [Tuple](#data_framework.dataflow.landing.Tuple)
  * [datetime](#data_framework.dataflow.landing.datetime)
  * [BytesIO](#data_framework.dataflow.landing.BytesIO)
  * [Path](#data_framework.dataflow.landing.Path)
  * [ZipFile](#data_framework.dataflow.landing.ZipFile)
  * [tarfile](#data_framework.dataflow.landing.tarfile)
  * [read\_xml](#data_framework.dataflow.landing.read_xml)
  * [read\_excel](#data_framework.dataflow.landing.read_excel)
  * [ProcessingCoordinator](#data_framework.dataflow.landing.ProcessingCoordinator)
    * [\_\_init\_\_](#data_framework.dataflow.landing.ProcessingCoordinator.__init__)
    * [process](#data_framework.dataflow.landing.ProcessingCoordinator.process)
    * [process\_file](#data_framework.dataflow.landing.ProcessingCoordinator.process_file)
    * [read\_data](#data_framework.dataflow.landing.ProcessingCoordinator.read_data)
    * [obtain\_file\_date](#data_framework.dataflow.landing.ProcessingCoordinator.obtain_file_date)
    * [compare\_with\_previous\_file](#data_framework.dataflow.landing.ProcessingCoordinator.compare_with_previous_file)
    * [get\_last\_processed\_file\_key](#data_framework.dataflow.landing.ProcessingCoordinator.get_last_processed_file_key)
    * [get\_file\_hash](#data_framework.dataflow.landing.ProcessingCoordinator.get_file_hash)
    * [create\_partitions](#data_framework.dataflow.landing.ProcessingCoordinator.create_partitions)
    * [write\_data](#data_framework.dataflow.landing.ProcessingCoordinator.write_data)
    * [normalize\_file\_content](#data_framework.dataflow.landing.ProcessingCoordinator.normalize_file_content)
    * [convert\_xml\_to\_parquet](#data_framework.dataflow.landing.ProcessingCoordinator.convert_xml_to_parquet)
    * [convert\_excel\_to\_parquet](#data_framework.dataflow.landing.ProcessingCoordinator.convert_excel_to_parquet)
    * [convert\_json\_to\_json\_lines](#data_framework.dataflow.landing.ProcessingCoordinator.convert_json_to_json_lines)
* [data\_framework.dataflow.output](#data_framework.dataflow.output)
  * [DataFlowInterface](#data_framework.dataflow.output.DataFlowInterface)
  * [OutputResponse](#data_framework.dataflow.output.OutputResponse)
  * [Storage](#data_framework.dataflow.output.Storage)
  * [Layer](#data_framework.dataflow.output.Layer)
  * [OutputReport](#data_framework.dataflow.output.OutputReport)
  * [OutputError](#data_framework.dataflow.output.OutputError)
  * [OutputGenerationError](#data_framework.dataflow.output.OutputGenerationError)
  * [NoOutputDataError](#data_framework.dataflow.output.NoOutputDataError)
  * [DataFrame](#data_framework.dataflow.output.DataFrame)
  * [datetime](#data_framework.dataflow.output.datetime)
  * [ZoneInfo](#data_framework.dataflow.output.ZoneInfo)
  * [BytesIO](#data_framework.dataflow.output.BytesIO)
  * [re](#data_framework.dataflow.output.re)
  * [TIME\_ZONE](#data_framework.dataflow.output.TIME_ZONE)
  * [ProcessingCoordinator](#data_framework.dataflow.output.ProcessingCoordinator)
    * [\_\_init\_\_](#data_framework.dataflow.output.ProcessingCoordinator.__init__)
    * [process](#data_framework.dataflow.output.ProcessingCoordinator.process)
    * [generate\_output\_file](#data_framework.dataflow.output.ProcessingCoordinator.generate_output_file)
    * [retrieve\_data](#data_framework.dataflow.output.ProcessingCoordinator.retrieve_data)
    * [write\_data\_to\_file](#data_framework.dataflow.output.ProcessingCoordinator.write_data_to_file)
    * [parse\_output\_folder](#data_framework.dataflow.output.ProcessingCoordinator.parse_output_folder)
    * [format\_string](#data_framework.dataflow.output.ProcessingCoordinator.format_string)
* [data\_framework.dataflow.staging](#data_framework.dataflow.staging)
  * [DataFlowInterface](#data_framework.dataflow.staging.DataFlowInterface)
  * [RawToStaging](#data_framework.dataflow.staging.RawToStaging)
    * [\_\_init\_\_](#data_framework.dataflow.staging.RawToStaging.__init__)
* [data\_framework.modules.catalogue.core\_catalogue](#data_framework.modules.catalogue.core_catalogue)
  * [LazyClassProperty](#data_framework.modules.catalogue.core_catalogue.LazyClassProperty)
  * [CatalogueInterface](#data_framework.modules.catalogue.core_catalogue.CatalogueInterface)
  * [SchemaResponse](#data_framework.modules.catalogue.core_catalogue.SchemaResponse)
  * [GenericResponse](#data_framework.modules.catalogue.core_catalogue.GenericResponse)
  * [Union](#data_framework.modules.catalogue.core_catalogue.Union)
  * [CoreCatalogue](#data_framework.modules.catalogue.core_catalogue.CoreCatalogue)
    * [\_catalogue](#data_framework.modules.catalogue.core_catalogue.CoreCatalogue._catalogue)
    * [create\_partition](#data_framework.modules.catalogue.core_catalogue.CoreCatalogue.create_partition)
    * [get\_schema](#data_framework.modules.catalogue.core_catalogue.CoreCatalogue.get_schema)
* [data\_framework.modules.catalogue.integrations.aws\_glue](#data_framework.modules.catalogue.integrations.aws_glue)
  * [CatalogueInterface](#data_framework.modules.catalogue.integrations.aws_glue.CatalogueInterface)
  * [SchemaResponse](#data_framework.modules.catalogue.integrations.aws_glue.SchemaResponse)
  * [GenericResponse](#data_framework.modules.catalogue.integrations.aws_glue.GenericResponse)
  * [Column](#data_framework.modules.catalogue.integrations.aws_glue.Column)
  * [Schema](#data_framework.modules.catalogue.integrations.aws_glue.Schema)
  * [GlueError](#data_framework.modules.catalogue.integrations.aws_glue.GlueError)
  * [CreatePartitionError](#data_framework.modules.catalogue.integrations.aws_glue.CreatePartitionError)
  * [InvalidPartitionFieldError](#data_framework.modules.catalogue.integrations.aws_glue.InvalidPartitionFieldError)
  * [SchemaError](#data_framework.modules.catalogue.integrations.aws_glue.SchemaError)
  * [logger](#data_framework.modules.catalogue.integrations.aws_glue.logger)
  * [config](#data_framework.modules.catalogue.integrations.aws_glue.config)
  * [Union](#data_framework.modules.catalogue.integrations.aws_glue.Union)
  * [Any](#data_framework.modules.catalogue.integrations.aws_glue.Any)
  * [boto3](#data_framework.modules.catalogue.integrations.aws_glue.boto3)
  * [CatalogueAWSGlue](#data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue)
    * [\_\_init\_\_](#data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue.__init__)
    * [create\_partition](#data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue.create_partition)
    * [get\_schema](#data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue.get_schema)
    * [\_get\_glue\_table](#data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue._get_glue_table)
* [data\_framework.modules.catalogue.interface\_catalogue](#data_framework.modules.catalogue.interface_catalogue)
  * [ABC](#data_framework.modules.catalogue.interface_catalogue.ABC)
  * [abstractmethod](#data_framework.modules.catalogue.interface_catalogue.abstractmethod)
  * [Union](#data_framework.modules.catalogue.interface_catalogue.Union)
  * [Optional](#data_framework.modules.catalogue.interface_catalogue.Optional)
  * [List](#data_framework.modules.catalogue.interface_catalogue.List)
  * [Dict](#data_framework.modules.catalogue.interface_catalogue.Dict)
  * [dataclass](#data_framework.modules.catalogue.interface_catalogue.dataclass)
  * [Column](#data_framework.modules.catalogue.interface_catalogue.Column)
    * [name](#data_framework.modules.catalogue.interface_catalogue.Column.name)
    * [type](#data_framework.modules.catalogue.interface_catalogue.Column.type)
    * [order](#data_framework.modules.catalogue.interface_catalogue.Column.order)
    * [ispartitioned](#data_framework.modules.catalogue.interface_catalogue.Column.ispartitioned)
  * [Schema](#data_framework.modules.catalogue.interface_catalogue.Schema)
    * [columns](#data_framework.modules.catalogue.interface_catalogue.Schema.columns)
    * [get\_column\_names](#data_framework.modules.catalogue.interface_catalogue.Schema.get_column_names)
    * [get\_type\_columns](#data_framework.modules.catalogue.interface_catalogue.Schema.get_type_columns)
    * [get\_column\_type\_mapping](#data_framework.modules.catalogue.interface_catalogue.Schema.get_column_type_mapping)
  * [SchemaResponse](#data_framework.modules.catalogue.interface_catalogue.SchemaResponse)
    * [success](#data_framework.modules.catalogue.interface_catalogue.SchemaResponse.success)
    * [error](#data_framework.modules.catalogue.interface_catalogue.SchemaResponse.error)
    * [schema](#data_framework.modules.catalogue.interface_catalogue.SchemaResponse.schema)
  * [GenericResponse](#data_framework.modules.catalogue.interface_catalogue.GenericResponse)
    * [success](#data_framework.modules.catalogue.interface_catalogue.GenericResponse.success)
    * [error](#data_framework.modules.catalogue.interface_catalogue.GenericResponse.error)
  * [CatalogueInterface](#data_framework.modules.catalogue.interface_catalogue.CatalogueInterface)
    * [create\_partition](#data_framework.modules.catalogue.interface_catalogue.CatalogueInterface.create_partition)
    * [get\_schema](#data_framework.modules.catalogue.interface_catalogue.CatalogueInterface.get_schema)
* [data\_framework.modules.code.lazy\_class\_property](#data_framework.modules.code.lazy_class_property)
  * [LazyClassProperty](#data_framework.modules.code.lazy_class_property.LazyClassProperty)
    * [\_\_init\_\_](#data_framework.modules.code.lazy_class_property.LazyClassProperty.__init__)
    * [\_\_get\_\_](#data_framework.modules.code.lazy_class_property.LazyClassProperty.__get__)
* [data\_framework.modules.config.core](#data_framework.modules.config.core)
  * [Type](#data_framework.modules.config.core.Type)
  * [TypeVar](#data_framework.modules.config.core.TypeVar)
  * [Union](#data_framework.modules.config.core.Union)
  * [get\_type\_hints](#data_framework.modules.config.core.get_type_hints)
  * [get\_origin](#data_framework.modules.config.core.get_origin)
  * [get\_args](#data_framework.modules.config.core.get_args)
  * [Processes](#data_framework.modules.config.core.Processes)
  * [LandingToRaw](#data_framework.modules.config.core.LandingToRaw)
  * [ToOutput](#data_framework.modules.config.core.ToOutput)
  * [IncomingFileLandingToRaw](#data_framework.modules.config.core.IncomingFileLandingToRaw)
  * [DateLocatedFilename](#data_framework.modules.config.core.DateLocatedFilename)
  * [Parameters](#data_framework.modules.config.core.Parameters)
  * [CSVSpecs](#data_framework.modules.config.core.CSVSpecs)
  * [XMLSpecs](#data_framework.modules.config.core.XMLSpecs)
  * [JSONSpecs](#data_framework.modules.config.core.JSONSpecs)
  * [Config](#data_framework.modules.config.core.Config)
  * [DatabaseTable](#data_framework.modules.config.core.DatabaseTable)
  * [ProcessingSpecifications](#data_framework.modules.config.core.ProcessingSpecifications)
  * [Hardware](#data_framework.modules.config.core.Hardware)
  * [SparkConfiguration](#data_framework.modules.config.core.SparkConfiguration)
  * [OutputReport](#data_framework.modules.config.core.OutputReport)
  * [GenericProcess](#data_framework.modules.config.core.GenericProcess)
  * [TableDict](#data_framework.modules.config.core.TableDict)
  * [CSVSpecsReport](#data_framework.modules.config.core.CSVSpecsReport)
  * [JSONSpecsReport](#data_framework.modules.config.core.JSONSpecsReport)
  * [VolumetricExpectation](#data_framework.modules.config.core.VolumetricExpectation)
  * [Platform](#data_framework.modules.config.core.Platform)
  * [Technologies](#data_framework.modules.config.core.Technologies)
  * [Environment](#data_framework.modules.config.core.Environment)
  * [ProcessVars](#data_framework.modules.config.core.ProcessVars)
  * [Casting](#data_framework.modules.config.core.Casting)
  * [Transformation](#data_framework.modules.config.core.Transformation)
  * [NotificationDict](#data_framework.modules.config.core.NotificationDict)
  * [DataFrameworkNotifications](#data_framework.modules.config.core.DataFrameworkNotifications)
  * [Notification](#data_framework.modules.config.core.Notification)
  * [NotificationsParameters](#data_framework.modules.config.core.NotificationsParameters)
  * [ConfigError](#data_framework.modules.config.core.ConfigError)
  * [ConfigFileNotFoundError](#data_framework.modules.config.core.ConfigFileNotFoundError)
  * [ConfigParseError](#data_framework.modules.config.core.ConfigParseError)
  * [AccountNotFoundError](#data_framework.modules.config.core.AccountNotFoundError)
  * [ParameterParseError](#data_framework.modules.config.core.ParameterParseError)
  * [STSError](#data_framework.modules.config.core.STSError)
  * [threading](#data_framework.modules.config.core.threading)
  * [os](#data_framework.modules.config.core.os)
  * [sys](#data_framework.modules.config.core.sys)
  * [Enum](#data_framework.modules.config.core.Enum)
  * [json](#data_framework.modules.config.core.json)
  * [Path](#data_framework.modules.config.core.Path)
  * [zipfile](#data_framework.modules.config.core.zipfile)
  * [boto3](#data_framework.modules.config.core.boto3)
  * [T](#data_framework.modules.config.core.T)
  * [config](#data_framework.modules.config.core.config)
  * [ConfigSetup](#data_framework.modules.config.core.ConfigSetup)
    * [\_instancia](#data_framework.modules.config.core.ConfigSetup._instancia)
    * [\_lock](#data_framework.modules.config.core.ConfigSetup._lock)
    * [\_environment](#data_framework.modules.config.core.ConfigSetup._environment)
    * [\_models](#data_framework.modules.config.core.ConfigSetup._models)
    * [\_\_new\_\_](#data_framework.modules.config.core.ConfigSetup.__new__)
    * [\_\_init\_\_](#data_framework.modules.config.core.ConfigSetup.__init__)
    * [read\_data\_framework\_config](#data_framework.modules.config.core.ConfigSetup.read_data_framework_config)
    * [read\_notifications\_config](#data_framework.modules.config.core.ConfigSetup.read_notifications_config)
    * [read\_config\_file](#data_framework.modules.config.core.ConfigSetup.read_config_file)
    * [read\_dataflow\_config](#data_framework.modules.config.core.ConfigSetup.read_dataflow_config)
    * [merged\_current\_dataflow\_with\_default](#data_framework.modules.config.core.ConfigSetup.merged_current_dataflow_with_default)
    * [parse\_to\_model](#data_framework.modules.config.core.ConfigSetup.parse_to_model)
* [data\_framework.modules.config.model.flows](#data_framework.modules.config.model.flows)
  * [Database](#data_framework.modules.config.model.flows.Database)
  * [NotificationDict](#data_framework.modules.config.model.flows.NotificationDict)
  * [DataFrameworkNotifications](#data_framework.modules.config.model.flows.DataFrameworkNotifications)
  * [EmptyProcessConfigError](#data_framework.modules.config.model.flows.EmptyProcessConfigError)
  * [ProcessNotFoundError](#data_framework.modules.config.model.flows.ProcessNotFoundError)
  * [TableKeyError](#data_framework.modules.config.model.flows.TableKeyError)
  * [TableConfigNotFoundError](#data_framework.modules.config.model.flows.TableConfigNotFoundError)
  * [TransformationNotImplementedError](#data_framework.modules.config.model.flows.TransformationNotImplementedError)
  * [dataclass](#data_framework.modules.config.model.flows.dataclass)
  * [field](#data_framework.modules.config.model.flows.field)
  * [fields](#data_framework.modules.config.model.flows.fields)
  * [Enum](#data_framework.modules.config.model.flows.Enum)
  * [Optional](#data_framework.modules.config.model.flows.Optional)
  * [List](#data_framework.modules.config.model.flows.List)
  * [Tuple](#data_framework.modules.config.model.flows.Tuple)
  * [Union](#data_framework.modules.config.model.flows.Union)
  * [os](#data_framework.modules.config.model.flows.os)
  * [Environment](#data_framework.modules.config.model.flows.Environment)
    * [LOCAL](#data_framework.modules.config.model.flows.Environment.LOCAL)
    * [DEVELOP](#data_framework.modules.config.model.flows.Environment.DEVELOP)
    * [PREPRODUCTION](#data_framework.modules.config.model.flows.Environment.PREPRODUCTION)
    * [PRODUCTION](#data_framework.modules.config.model.flows.Environment.PRODUCTION)
  * [Platform](#data_framework.modules.config.model.flows.Platform)
    * [DATA\_PLATFORM](#data_framework.modules.config.model.flows.Platform.DATA_PLATFORM)
    * [INFINITY](#data_framework.modules.config.model.flows.Platform.INFINITY)
  * [DateLocated](#data_framework.modules.config.model.flows.DateLocated)
    * [FILENAME](#data_framework.modules.config.model.flows.DateLocated.FILENAME)
    * [COLUMN](#data_framework.modules.config.model.flows.DateLocated.COLUMN)
  * [Technologies](#data_framework.modules.config.model.flows.Technologies)
    * [LAMBDA](#data_framework.modules.config.model.flows.Technologies.LAMBDA)
    * [EMR](#data_framework.modules.config.model.flows.Technologies.EMR)
  * [LandingFileFormat](#data_framework.modules.config.model.flows.LandingFileFormat)
    * [CSV](#data_framework.modules.config.model.flows.LandingFileFormat.CSV)
    * [JSON](#data_framework.modules.config.model.flows.LandingFileFormat.JSON)
    * [JSON\_LINES](#data_framework.modules.config.model.flows.LandingFileFormat.JSON_LINES)
    * [EXCEL](#data_framework.modules.config.model.flows.LandingFileFormat.EXCEL)
    * [XML](#data_framework.modules.config.model.flows.LandingFileFormat.XML)
  * [OutputFileFormat](#data_framework.modules.config.model.flows.OutputFileFormat)
    * [CSV](#data_framework.modules.config.model.flows.OutputFileFormat.CSV)
    * [JSON](#data_framework.modules.config.model.flows.OutputFileFormat.JSON)
  * [ExecutionMode](#data_framework.modules.config.model.flows.ExecutionMode)
    * [DELTA](#data_framework.modules.config.model.flows.ExecutionMode.DELTA)
    * [FULL](#data_framework.modules.config.model.flows.ExecutionMode.FULL)
    * [is\_delta](#data_framework.modules.config.model.flows.ExecutionMode.is_delta)
  * [JSONSpectFormat](#data_framework.modules.config.model.flows.JSONSpectFormat)
    * [LINES](#data_framework.modules.config.model.flows.JSONSpectFormat.LINES)
    * [COLUMNS](#data_framework.modules.config.model.flows.JSONSpectFormat.COLUMNS)
  * [JSONFormat](#data_framework.modules.config.model.flows.JSONFormat)
    * [DICTIONARY](#data_framework.modules.config.model.flows.JSONFormat.DICTIONARY)
    * [ARRAY](#data_framework.modules.config.model.flows.JSONFormat.ARRAY)
  * [CastingStrategy](#data_framework.modules.config.model.flows.CastingStrategy)
    * [ONE\_BY\_ONE](#data_framework.modules.config.model.flows.CastingStrategy.ONE_BY_ONE)
    * [DYNAMIC](#data_framework.modules.config.model.flows.CastingStrategy.DYNAMIC)
  * [TransformationType](#data_framework.modules.config.model.flows.TransformationType)
    * [PARSE\_DATES](#data_framework.modules.config.model.flows.TransformationType.PARSE_DATES)
  * [Hardware](#data_framework.modules.config.model.flows.Hardware)
    * [ram](#data_framework.modules.config.model.flows.Hardware.ram)
    * [cores](#data_framework.modules.config.model.flows.Hardware.cores)
    * [disk](#data_framework.modules.config.model.flows.Hardware.disk)
  * [VolumetricExpectation](#data_framework.modules.config.model.flows.VolumetricExpectation)
    * [data\_size\_gb](#data_framework.modules.config.model.flows.VolumetricExpectation.data_size_gb)
    * [avg\_file\_size\_mb](#data_framework.modules.config.model.flows.VolumetricExpectation.avg_file_size_mb)
  * [SparkConfiguration](#data_framework.modules.config.model.flows.SparkConfiguration)
    * [full\_volumetric\_expectation](#data_framework.modules.config.model.flows.SparkConfiguration.full_volumetric_expectation)
    * [delta\_volumetric\_expectation](#data_framework.modules.config.model.flows.SparkConfiguration.delta_volumetric_expectation)
    * [delta\_custom](#data_framework.modules.config.model.flows.SparkConfiguration.delta_custom)
    * [full\_custom](#data_framework.modules.config.model.flows.SparkConfiguration.full_custom)
    * [volumetric\_expectation](#data_framework.modules.config.model.flows.SparkConfiguration.volumetric_expectation)
    * [custom\_config](#data_framework.modules.config.model.flows.SparkConfiguration.custom_config)
    * [config](#data_framework.modules.config.model.flows.SparkConfiguration.config)
  * [ProcessingSpecifications](#data_framework.modules.config.model.flows.ProcessingSpecifications)
    * [technology](#data_framework.modules.config.model.flows.ProcessingSpecifications.technology)
    * [spark\_configuration](#data_framework.modules.config.model.flows.ProcessingSpecifications.spark_configuration)
  * [DateLocatedFilename](#data_framework.modules.config.model.flows.DateLocatedFilename)
    * [regex](#data_framework.modules.config.model.flows.DateLocatedFilename.regex)
  * [InterfaceSpecs](#data_framework.modules.config.model.flows.InterfaceSpecs)
    * [read\_config](#data_framework.modules.config.model.flows.InterfaceSpecs.read_config)
  * [XMLSpecs](#data_framework.modules.config.model.flows.XMLSpecs)
    * [encoding](#data_framework.modules.config.model.flows.XMLSpecs.encoding)
    * [xpath](#data_framework.modules.config.model.flows.XMLSpecs.xpath)
    * [date\_located](#data_framework.modules.config.model.flows.XMLSpecs.date_located)
    * [date\_located\_filename](#data_framework.modules.config.model.flows.XMLSpecs.date_located_filename)
    * [read\_config](#data_framework.modules.config.model.flows.XMLSpecs.read_config)
  * [JSONSpecs](#data_framework.modules.config.model.flows.JSONSpecs)
    * [encoding](#data_framework.modules.config.model.flows.JSONSpecs.encoding)
    * [date\_located](#data_framework.modules.config.model.flows.JSONSpecs.date_located)
    * [date\_located\_filename](#data_framework.modules.config.model.flows.JSONSpecs.date_located_filename)
    * [source\_level](#data_framework.modules.config.model.flows.JSONSpecs.source_level)
    * [source\_level\_format](#data_framework.modules.config.model.flows.JSONSpecs.source_level_format)
    * [read\_config](#data_framework.modules.config.model.flows.JSONSpecs.read_config)
    * [levels](#data_framework.modules.config.model.flows.JSONSpecs.levels)
  * [CSVSpecs](#data_framework.modules.config.model.flows.CSVSpecs)
    * [header\_position](#data_framework.modules.config.model.flows.CSVSpecs.header_position)
    * [header](#data_framework.modules.config.model.flows.CSVSpecs.header)
    * [encoding](#data_framework.modules.config.model.flows.CSVSpecs.encoding)
    * [delimiter](#data_framework.modules.config.model.flows.CSVSpecs.delimiter)
    * [date\_located](#data_framework.modules.config.model.flows.CSVSpecs.date_located)
    * [date\_located\_filename](#data_framework.modules.config.model.flows.CSVSpecs.date_located_filename)
    * [escape](#data_framework.modules.config.model.flows.CSVSpecs.escape)
    * [comment](#data_framework.modules.config.model.flows.CSVSpecs.comment)
    * [null\_value](#data_framework.modules.config.model.flows.CSVSpecs.null_value)
    * [nan\_value](#data_framework.modules.config.model.flows.CSVSpecs.nan_value)
    * [special\_character](#data_framework.modules.config.model.flows.CSVSpecs.special_character)
    * [multiline](#data_framework.modules.config.model.flows.CSVSpecs.multiline)
    * [read\_config](#data_framework.modules.config.model.flows.CSVSpecs.read_config)
  * [CSVSpecsReport](#data_framework.modules.config.model.flows.CSVSpecsReport)
    * [header](#data_framework.modules.config.model.flows.CSVSpecsReport.header)
    * [index](#data_framework.modules.config.model.flows.CSVSpecsReport.index)
    * [encoding](#data_framework.modules.config.model.flows.CSVSpecsReport.encoding)
    * [delimiter](#data_framework.modules.config.model.flows.CSVSpecsReport.delimiter)
  * [JSONSpecsReport](#data_framework.modules.config.model.flows.JSONSpecsReport)
    * [format](#data_framework.modules.config.model.flows.JSONSpecsReport.format)
  * [Parameters](#data_framework.modules.config.model.flows.Parameters)
    * [dataflow](#data_framework.modules.config.model.flows.Parameters.dataflow)
    * [process](#data_framework.modules.config.model.flows.Parameters.process)
    * [table](#data_framework.modules.config.model.flows.Parameters.table)
    * [source\_file\_path](#data_framework.modules.config.model.flows.Parameters.source_file_path)
    * [bucket\_prefix](#data_framework.modules.config.model.flows.Parameters.bucket_prefix)
    * [file\_name](#data_framework.modules.config.model.flows.Parameters.file_name)
    * [file\_date](#data_framework.modules.config.model.flows.Parameters.file_date)
    * [execution\_mode](#data_framework.modules.config.model.flows.Parameters.execution_mode)
    * [region](#data_framework.modules.config.model.flows.Parameters.region)
  * [IncomingFileLandingToRaw](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw)
    * [zipped](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.zipped)
    * [file\_format](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.file_format)
    * [filename\_pattern](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.filename_pattern)
    * [filename\_unzipped\_pattern](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.filename_unzipped_pattern)
    * [csv\_specs](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.csv_specs)
    * [xml\_specs](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.xml_specs)
    * [json\_specs](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.json_specs)
    * [compare\_with\_previous\_file](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.compare_with_previous_file)
    * [specifications](#data_framework.modules.config.model.flows.IncomingFileLandingToRaw.specifications)
  * [Transformation](#data_framework.modules.config.model.flows.Transformation)
    * [type](#data_framework.modules.config.model.flows.Transformation.type)
    * [get\_subclass\_from\_dict](#data_framework.modules.config.model.flows.Transformation.get_subclass_from_dict)
  * [ParseDatesTransformation](#data_framework.modules.config.model.flows.ParseDatesTransformation)
    * [columns](#data_framework.modules.config.model.flows.ParseDatesTransformation.columns)
    * [source\_format](#data_framework.modules.config.model.flows.ParseDatesTransformation.source_format)
    * [target\_format](#data_framework.modules.config.model.flows.ParseDatesTransformation.target_format)
  * [Casting](#data_framework.modules.config.model.flows.Casting)
    * [strategy](#data_framework.modules.config.model.flows.Casting.strategy)
    * [fix\_incompatible\_characters](#data_framework.modules.config.model.flows.Casting.fix_incompatible_characters)
    * [master\_table](#data_framework.modules.config.model.flows.Casting.master_table)
    * [transformations](#data_framework.modules.config.model.flows.Casting.transformations)
  * [DatabaseTable](#data_framework.modules.config.model.flows.DatabaseTable)
    * [database](#data_framework.modules.config.model.flows.DatabaseTable.database)
    * [table](#data_framework.modules.config.model.flows.DatabaseTable.table)
    * [primary\_keys](#data_framework.modules.config.model.flows.DatabaseTable.primary_keys)
    * [casting](#data_framework.modules.config.model.flows.DatabaseTable.casting)
    * [partition\_field](#data_framework.modules.config.model.flows.DatabaseTable.partition_field)
    * [database\_relation](#data_framework.modules.config.model.flows.DatabaseTable.database_relation)
    * [full\_name](#data_framework.modules.config.model.flows.DatabaseTable.full_name)
    * [sql\_where](#data_framework.modules.config.model.flows.DatabaseTable.sql_where)
  * [TableDict](#data_framework.modules.config.model.flows.TableDict)
    * [tables](#data_framework.modules.config.model.flows.TableDict.tables)
    * [table](#data_framework.modules.config.model.flows.TableDict.table)
    * [table\_key](#data_framework.modules.config.model.flows.TableDict.table_key)
  * [LandingToRaw](#data_framework.modules.config.model.flows.LandingToRaw)
    * [incoming\_file](#data_framework.modules.config.model.flows.LandingToRaw.incoming_file)
    * [output\_file](#data_framework.modules.config.model.flows.LandingToRaw.output_file)
    * [processing\_specifications](#data_framework.modules.config.model.flows.LandingToRaw.processing_specifications)
    * [notifications](#data_framework.modules.config.model.flows.LandingToRaw.notifications)
  * [ProcessVars](#data_framework.modules.config.model.flows.ProcessVars)
    * [\_variables](#data_framework.modules.config.model.flows.ProcessVars._variables)
    * [get\_variable](#data_framework.modules.config.model.flows.ProcessVars.get_variable)
  * [GenericProcess](#data_framework.modules.config.model.flows.GenericProcess)
    * [source\_tables](#data_framework.modules.config.model.flows.GenericProcess.source_tables)
    * [target\_tables](#data_framework.modules.config.model.flows.GenericProcess.target_tables)
    * [processing\_specifications](#data_framework.modules.config.model.flows.GenericProcess.processing_specifications)
    * [notifications](#data_framework.modules.config.model.flows.GenericProcess.notifications)
    * [vars](#data_framework.modules.config.model.flows.GenericProcess.vars)
  * [OutputReport](#data_framework.modules.config.model.flows.OutputReport)
    * [name](#data_framework.modules.config.model.flows.OutputReport.name)
    * [source\_table](#data_framework.modules.config.model.flows.OutputReport.source_table)
    * [columns](#data_framework.modules.config.model.flows.OutputReport.columns)
    * [file\_format](#data_framework.modules.config.model.flows.OutputReport.file_format)
    * [filename\_pattern](#data_framework.modules.config.model.flows.OutputReport.filename_pattern)
    * [csv\_specs](#data_framework.modules.config.model.flows.OutputReport.csv_specs)
    * [json\_specs](#data_framework.modules.config.model.flows.OutputReport.json_specs)
    * [replaces](#data_framework.modules.config.model.flows.OutputReport.replaces)
    * [description](#data_framework.modules.config.model.flows.OutputReport.description)
    * [where](#data_framework.modules.config.model.flows.OutputReport.where)
    * [columns\_alias](#data_framework.modules.config.model.flows.OutputReport.columns_alias)
    * [filename\_date\_format](#data_framework.modules.config.model.flows.OutputReport.filename_date_format)
  * [ToOutput](#data_framework.modules.config.model.flows.ToOutput)
    * [output\_reports](#data_framework.modules.config.model.flows.ToOutput.output_reports)
    * [processing\_specifications](#data_framework.modules.config.model.flows.ToOutput.processing_specifications)
    * [notifications](#data_framework.modules.config.model.flows.ToOutput.notifications)
  * [Processes](#data_framework.modules.config.model.flows.Processes)
    * [landing\_to\_raw](#data_framework.modules.config.model.flows.Processes.landing_to_raw)
    * [raw\_to\_staging](#data_framework.modules.config.model.flows.Processes.raw_to_staging)
    * [staging\_to\_common](#data_framework.modules.config.model.flows.Processes.staging_to_common)
    * [staging\_to\_business](#data_framework.modules.config.model.flows.Processes.staging_to_business)
    * [common\_to\_business](#data_framework.modules.config.model.flows.Processes.common_to_business)
    * [common\_to\_output](#data_framework.modules.config.model.flows.Processes.common_to_output)
    * [business\_to\_output](#data_framework.modules.config.model.flows.Processes.business_to_output)
  * [Config](#data_framework.modules.config.model.flows.Config)
    * [processes](#data_framework.modules.config.model.flows.Config.processes)
    * [environment](#data_framework.modules.config.model.flows.Config.environment)
    * [platform](#data_framework.modules.config.model.flows.Config.platform)
    * [parameters](#data_framework.modules.config.model.flows.Config.parameters)
    * [project\_id](#data_framework.modules.config.model.flows.Config.project_id)
    * [data\_framework\_notifications](#data_framework.modules.config.model.flows.Config.data_framework_notifications)
    * [has\_next\_process](#data_framework.modules.config.model.flows.Config.has_next_process)
    * [is\_first\_process](#data_framework.modules.config.model.flows.Config.is_first_process)
    * [current\_process\_config](#data_framework.modules.config.model.flows.Config.current_process_config)
* [data\_framework.modules.dataflow.interface\_dataflow](#data_framework.modules.dataflow.interface_dataflow)
  * [ABC](#data_framework.modules.dataflow.interface_dataflow.ABC)
  * [config](#data_framework.modules.dataflow.interface_dataflow.config)
  * [Config](#data_framework.modules.dataflow.interface_dataflow.Config)
  * [TableDict](#data_framework.modules.dataflow.interface_dataflow.TableDict)
  * [DatabaseTable](#data_framework.modules.dataflow.interface_dataflow.DatabaseTable)
  * [OutputReport](#data_framework.modules.dataflow.interface_dataflow.OutputReport)
  * [ExecutionMode](#data_framework.modules.dataflow.interface_dataflow.ExecutionMode)
  * [logger](#data_framework.modules.dataflow.interface_dataflow.logger)
  * [CoreDataProcess](#data_framework.modules.dataflow.interface_dataflow.CoreDataProcess)
  * [CoreQualityControls](#data_framework.modules.dataflow.interface_dataflow.CoreQualityControls)
  * [CoreNotifications](#data_framework.modules.dataflow.interface_dataflow.CoreNotifications)
  * [NotificationToSend](#data_framework.modules.dataflow.interface_dataflow.NotificationToSend)
  * [CoreMonitoring](#data_framework.modules.dataflow.interface_dataflow.CoreMonitoring)
  * [Metric](#data_framework.modules.dataflow.interface_dataflow.Metric)
  * [MetricNames](#data_framework.modules.dataflow.interface_dataflow.MetricNames)
  * [DataflowInitializationError](#data_framework.modules.dataflow.interface_dataflow.DataflowInitializationError)
  * [PayloadResponseError](#data_framework.modules.dataflow.interface_dataflow.PayloadResponseError)
  * [SSMError](#data_framework.modules.dataflow.interface_dataflow.SSMError)
  * [dataclass](#data_framework.modules.dataflow.interface_dataflow.dataclass)
  * [asdict](#data_framework.modules.dataflow.interface_dataflow.asdict)
  * [field](#data_framework.modules.dataflow.interface_dataflow.field)
  * [Any](#data_framework.modules.dataflow.interface_dataflow.Any)
  * [List](#data_framework.modules.dataflow.interface_dataflow.List)
  * [Optional](#data_framework.modules.dataflow.interface_dataflow.Optional)
  * [boto3](#data_framework.modules.dataflow.interface_dataflow.boto3)
  * [json](#data_framework.modules.dataflow.interface_dataflow.json)
  * [time](#data_framework.modules.dataflow.interface_dataflow.time)
  * [DataQualityTable](#data_framework.modules.dataflow.interface_dataflow.DataQualityTable)
    * [database](#data_framework.modules.dataflow.interface_dataflow.DataQualityTable.database)
    * [table](#data_framework.modules.dataflow.interface_dataflow.DataQualityTable.table)
  * [DataQuality](#data_framework.modules.dataflow.interface_dataflow.DataQuality)
    * [tables](#data_framework.modules.dataflow.interface_dataflow.DataQuality.tables)
  * [OutputResponse](#data_framework.modules.dataflow.interface_dataflow.OutputResponse)
    * [name](#data_framework.modules.dataflow.interface_dataflow.OutputResponse.name)
    * [success](#data_framework.modules.dataflow.interface_dataflow.OutputResponse.success)
    * [error](#data_framework.modules.dataflow.interface_dataflow.OutputResponse.error)
  * [PayloadResponse](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse)
    * [success](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.success)
    * [next\_stage](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.next_stage)
    * [file\_name](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.file_name)
    * [file\_date](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.file_date)
    * [data\_quality](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.data_quality)
    * [outputs](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.outputs)
    * [notifications](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.notifications)
    * [get\_failed\_outputs](#data_framework.modules.dataflow.interface_dataflow.PayloadResponse.get_failed_outputs)
  * [DataFlowInterface](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface)
    * [config](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.config)
    * [logger](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.logger)
    * [data\_process](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.data_process)
    * [quality\_controls](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.quality_controls)
    * [notifications](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.notifications)
    * [source\_tables](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.source_tables)
    * [target\_tables](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.target_tables)
    * [payload\_response](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.payload_response)
    * [incoming\_file](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.incoming_file)
    * [output\_file](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.output_file)
    * [output\_reports](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.output_reports)
    * [\_\_init\_\_](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.__init__)
    * [process](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.process)
    * [vars](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.vars)
    * [read\_table\_with\_casting](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.read_table_with_casting)
    * [read\_table](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.read_table)
    * [write](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.write)
    * [save\_monitorization](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.save_monitorization)
    * [save\_payload\_response](#data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.save_payload_response)
* [data\_framework.modules.data\_process.core\_data\_process](#data_framework.modules.data_process.core_data_process)
  * [LazyClassProperty](#data_framework.modules.data_process.core_data_process.LazyClassProperty)
  * [DataProcessInterface](#data_framework.modules.data_process.core_data_process.DataProcessInterface)
  * [ReadResponse](#data_framework.modules.data_process.core_data_process.ReadResponse)
  * [WriteResponse](#data_framework.modules.data_process.core_data_process.WriteResponse)
  * [config](#data_framework.modules.data_process.core_data_process.config)
  * [Technologies](#data_framework.modules.data_process.core_data_process.Technologies)
  * [DatabaseTable](#data_framework.modules.data_process.core_data_process.DatabaseTable)
  * [List](#data_framework.modules.data_process.core_data_process.List)
  * [Any](#data_framework.modules.data_process.core_data_process.Any)
  * [CoreDataProcess](#data_framework.modules.data_process.core_data_process.CoreDataProcess)
    * [\_data\_process](#data_framework.modules.data_process.core_data_process.CoreDataProcess._data_process)
    * [merge](#data_framework.modules.data_process.core_data_process.CoreDataProcess.merge)
    * [insert\_overwrite](#data_framework.modules.data_process.core_data_process.CoreDataProcess.insert_overwrite)
    * [datacast](#data_framework.modules.data_process.core_data_process.CoreDataProcess.datacast)
    * [read\_table](#data_framework.modules.data_process.core_data_process.CoreDataProcess.read_table)
    * [delete\_from\_table](#data_framework.modules.data_process.core_data_process.CoreDataProcess.delete_from_table)
    * [insert\_dataframe](#data_framework.modules.data_process.core_data_process.CoreDataProcess.insert_dataframe)
    * [join](#data_framework.modules.data_process.core_data_process.CoreDataProcess.join)
    * [create\_dataframe](#data_framework.modules.data_process.core_data_process.CoreDataProcess.create_dataframe)
    * [query](#data_framework.modules.data_process.core_data_process.CoreDataProcess.query)
    * [overwrite\_columns](#data_framework.modules.data_process.core_data_process.CoreDataProcess.overwrite_columns)
    * [unfold\_string\_values](#data_framework.modules.data_process.core_data_process.CoreDataProcess.unfold_string_values)
    * [add\_dynamic\_column](#data_framework.modules.data_process.core_data_process.CoreDataProcess.add_dynamic_column)
    * [stack\_columns](#data_framework.modules.data_process.core_data_process.CoreDataProcess.stack_columns)
    * [is\_empty](#data_framework.modules.data_process.core_data_process.CoreDataProcess.is_empty)
    * [count\_rows](#data_framework.modules.data_process.core_data_process.CoreDataProcess.count_rows)
    * [select\_columns](#data_framework.modules.data_process.core_data_process.CoreDataProcess.select_columns)
    * [show\_dataframe](#data_framework.modules.data_process.core_data_process.CoreDataProcess.show_dataframe)
* [data\_framework.modules.data\_process.helpers.athena](#data_framework.modules.data_process.helpers.athena)
  * [config](#data_framework.modules.data_process.helpers.athena.config)
  * [logger](#data_framework.modules.data_process.helpers.athena.logger)
  * [Platform](#data_framework.modules.data_process.helpers.athena.Platform)
  * [Layer](#data_framework.modules.data_process.helpers.athena.Layer)
  * [Storage](#data_framework.modules.data_process.helpers.athena.Storage)
  * [AthenaError](#data_framework.modules.data_process.helpers.athena.AthenaError)
  * [boto3](#data_framework.modules.data_process.helpers.athena.boto3)
  * [read\_csv](#data_framework.modules.data_process.helpers.athena.read_csv)
  * [DataFrame](#data_framework.modules.data_process.helpers.athena.DataFrame)
  * [sleep](#data_framework.modules.data_process.helpers.athena.sleep)
  * [BytesIO](#data_framework.modules.data_process.helpers.athena.BytesIO)
  * [Union](#data_framework.modules.data_process.helpers.athena.Union)
  * [AthenaClient](#data_framework.modules.data_process.helpers.athena.AthenaClient)
    * [\_\_init\_\_](#data_framework.modules.data_process.helpers.athena.AthenaClient.__init__)
    * [execute\_query](#data_framework.modules.data_process.helpers.athena.AthenaClient.execute_query)
    * [wait\_for\_query\_to\_complete](#data_framework.modules.data_process.helpers.athena.AthenaClient.wait_for_query_to_complete)
    * [get\_query\_results](#data_framework.modules.data_process.helpers.athena.AthenaClient.get_query_results)
* [data\_framework.modules.data\_process.helpers.cast](#data_framework.modules.data_process.helpers.cast)
  * [logger](#data_framework.modules.data_process.helpers.cast.logger)
  * [CoreCatalogue](#data_framework.modules.data_process.helpers.cast.CoreCatalogue)
  * [DatabaseTable](#data_framework.modules.data_process.helpers.cast.DatabaseTable)
  * [CastQueryError](#data_framework.modules.data_process.helpers.cast.CastQueryError)
  * [Any](#data_framework.modules.data_process.helpers.cast.Any)
  * [Dict](#data_framework.modules.data_process.helpers.cast.Dict)
  * [List](#data_framework.modules.data_process.helpers.cast.List)
  * [pd](#data_framework.modules.data_process.helpers.cast.pd)
  * [DataFrame](#data_framework.modules.data_process.helpers.cast.DataFrame)
  * [Series](#data_framework.modules.data_process.helpers.cast.Series)
  * [Cast](#data_framework.modules.data_process.helpers.cast.Cast)
    * [\_\_init\_\_](#data_framework.modules.data_process.helpers.cast.Cast.__init__)
    * [cast\_columns](#data_framework.modules.data_process.helpers.cast.Cast.cast_columns)
    * [build\_datacast\_query](#data_framework.modules.data_process.helpers.cast.Cast.build_datacast_query)
    * [get\_query\_to\_insert\_dataframe](#data_framework.modules.data_process.helpers.cast.Cast.get_query_to_insert_dataframe)
    * [cast\_df\_row](#data_framework.modules.data_process.helpers.cast.Cast.cast_df_row)
    * [cast\_df\_value](#data_framework.modules.data_process.helpers.cast.Cast.cast_df_value)
* [data\_framework.modules.data\_process.integrations.pandas.pandas\_data\_process](#data_framework.modules.data_process.integrations.pandas.pandas_data_process)
  * [DataProcessInterface](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.DataProcessInterface)
  * [ReadResponse](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.ReadResponse)
  * [WriteResponse](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.WriteResponse)
  * [AthenaClient](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.AthenaClient)
  * [Cast](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.Cast)
  * [logger](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.logger)
  * [DatabaseTable](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.DatabaseTable)
  * [ReadDataError](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.ReadDataError)
  * [WriteDataError](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.WriteDataError)
  * [DataProcessError](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.DataProcessError)
  * [List](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.List)
  * [Any](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.Any)
  * [pd](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.pd)
  * [DataFrame](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.DataFrame)
  * [PandasDataProcess](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess)
    * [\_\_init\_\_](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.__init__)
    * [merge](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.merge)
    * [datacast](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.datacast)
    * [read\_table](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.read_table)
    * [delete\_from\_table](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.delete_from_table)
    * [insert\_dataframe](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.insert_dataframe)
    * [join](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.join)
    * [create\_dataframe](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.create_dataframe)
    * [query](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.query)
    * [overwrite\_columns](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.overwrite_columns)
    * [unfold\_string\_values](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.unfold_string_values)
    * [add\_dynamic\_column](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.add_dynamic_column)
    * [stack\_columns](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.stack_columns)
    * [is\_empty](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.is_empty)
    * [count\_rows](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.count_rows)
    * [select\_columns](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.select_columns)
    * [show\_dataframe](#data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.show_dataframe)
* [data\_framework.modules.data\_process.integrations.spark.dynamic\_config](#data_framework.modules.data_process.integrations.spark.dynamic_config)
  * [json](#data_framework.modules.data_process.integrations.spark.dynamic_config.json)
  * [DynamicConfig](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig)
    * [determine\_cores](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_cores)
    * [determine\_driver\_settings](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_driver_settings)
    * [determine\_disk\_size](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_disk_size)
    * [determine\_memory\_overhead\_factor](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_memory_overhead_factor)
    * [adjust\_resources\_to\_emr\_serverless\_constraints](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.adjust_resources_to_emr_serverless_constraints)
    * [recommend\_spark\_config](#data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.recommend_spark_config)
* [data\_framework.modules.data\_process.integrations.spark.spark\_data\_process](#data_framework.modules.data_process.integrations.spark.spark_data_process)
  * [DataProcessInterface](#data_framework.modules.data_process.integrations.spark.spark_data_process.DataProcessInterface)
  * [ReadResponse](#data_framework.modules.data_process.integrations.spark.spark_data_process.ReadResponse)
  * [WriteResponse](#data_framework.modules.data_process.integrations.spark.spark_data_process.WriteResponse)
  * [utils](#data_framework.modules.data_process.integrations.spark.spark_data_process.utils)
  * [Storage](#data_framework.modules.data_process.integrations.spark.spark_data_process.Storage)
  * [Layer](#data_framework.modules.data_process.integrations.spark.spark_data_process.Layer)
  * [config](#data_framework.modules.data_process.integrations.spark.spark_data_process.config)
  * [logger](#data_framework.modules.data_process.integrations.spark.spark_data_process.logger)
  * [Cast](#data_framework.modules.data_process.integrations.spark.spark_data_process.Cast)
  * [CoreCatalogue](#data_framework.modules.data_process.integrations.spark.spark_data_process.CoreCatalogue)
  * [CoreMonitoring](#data_framework.modules.data_process.integrations.spark.spark_data_process.CoreMonitoring)
  * [MetricNames](#data_framework.modules.data_process.integrations.spark.spark_data_process.MetricNames)
  * [DatabaseTable](#data_framework.modules.data_process.integrations.spark.spark_data_process.DatabaseTable)
  * [ExecutionMode](#data_framework.modules.data_process.integrations.spark.spark_data_process.ExecutionMode)
  * [CastingStrategy](#data_framework.modules.data_process.integrations.spark.spark_data_process.CastingStrategy)
  * [LandingFileFormat](#data_framework.modules.data_process.integrations.spark.spark_data_process.LandingFileFormat)
  * [debug\_code](#data_framework.modules.data_process.integrations.spark.spark_data_process.debug_code)
  * [DynamicConfig](#data_framework.modules.data_process.integrations.spark.spark_data_process.DynamicConfig)
  * [ReadDataError](#data_framework.modules.data_process.integrations.spark.spark_data_process.ReadDataError)
  * [WriteDataError](#data_framework.modules.data_process.integrations.spark.spark_data_process.WriteDataError)
  * [DataProcessError](#data_framework.modules.data_process.integrations.spark.spark_data_process.DataProcessError)
  * [CastDataError](#data_framework.modules.data_process.integrations.spark.spark_data_process.CastDataError)
  * [DeleteDataError](#data_framework.modules.data_process.integrations.spark.spark_data_process.DeleteDataError)
  * [SparkConfigurationError](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkConfigurationError)
  * [List](#data_framework.modules.data_process.integrations.spark.spark_data_process.List)
  * [Any](#data_framework.modules.data_process.integrations.spark.spark_data_process.Any)
  * [Union](#data_framework.modules.data_process.integrations.spark.spark_data_process.Union)
  * [BytesIO](#data_framework.modules.data_process.integrations.spark.spark_data_process.BytesIO)
  * [SparkConf](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkConf)
  * [SparkSession](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkSession)
  * [DataFrame](#data_framework.modules.data_process.integrations.spark.spark_data_process.DataFrame)
  * [when](#data_framework.modules.data_process.integrations.spark.spark_data_process.when)
  * [StructType](#data_framework.modules.data_process.integrations.spark.spark_data_process.StructType)
  * [f](#data_framework.modules.data_process.integrations.spark.spark_data_process.f)
  * [time](#data_framework.modules.data_process.integrations.spark.spark_data_process.time)
  * [random](#data_framework.modules.data_process.integrations.spark.spark_data_process.random)
  * [inspect](#data_framework.modules.data_process.integrations.spark.spark_data_process.inspect)
  * [iceberg\_exceptions](#data_framework.modules.data_process.integrations.spark.spark_data_process.iceberg_exceptions)
  * [SparkDataProcess](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess)
    * [\_\_iceberg\_snapshot\_metrics\_map](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.__iceberg_snapshot_metrics_map)
    * [\_\_init\_\_](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.__init__)
    * [\_build\_complete\_table\_name](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._build_complete_table_name)
    * [\_track\_table\_metric](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._track_table_metric)
    * [merge](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.merge)
    * [insert\_overwrite](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.insert_overwrite)
    * [datacast](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.datacast)
    * [\_read\_raw\_file](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._read_raw_file)
    * [\_read\_raw\_json\_file](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._read_raw_json_file)
    * [\_execute\_query](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._execute_query)
    * [read\_table](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.read_table)
    * [delete\_from\_table](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.delete_from_table)
    * [insert\_dataframe](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.insert_dataframe)
    * [\_select\_table\_columns](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._select_table_columns)
    * [join](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.join)
    * [create\_dataframe](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.create_dataframe)
    * [query](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.query)
    * [overwrite\_columns](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.overwrite_columns)
    * [unfold\_string\_values](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.unfold_string_values)
    * [add\_dynamic\_column](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.add_dynamic_column)
    * [stack\_columns](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.stack_columns)
    * [is\_empty](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.is_empty)
    * [count\_rows](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.count_rows)
    * [select\_columns](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.select_columns)
    * [show\_dataframe](#data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.show_dataframe)
* [data\_framework.modules.data\_process.integrations.spark.transformations.parse\_dates](#data_framework.modules.data_process.integrations.spark.transformations.parse_dates)
  * [Transformation](#data_framework.modules.data_process.integrations.spark.transformations.parse_dates.Transformation)
  * [DataFrame](#data_framework.modules.data_process.integrations.spark.transformations.parse_dates.DataFrame)
  * [f](#data_framework.modules.data_process.integrations.spark.transformations.parse_dates.f)
  * [parse\_dates](#data_framework.modules.data_process.integrations.spark.transformations.parse_dates.parse_dates)
* [data\_framework.modules.data\_process.integrations.spark.utils](#data_framework.modules.data_process.integrations.spark.utils)
  * [Transformation](#data_framework.modules.data_process.integrations.spark.utils.Transformation)
  * [DatabaseTable](#data_framework.modules.data_process.integrations.spark.utils.DatabaseTable)
  * [JSONFormat](#data_framework.modules.data_process.integrations.spark.utils.JSONFormat)
  * [config](#data_framework.modules.data_process.integrations.spark.utils.config)
  * [TransformationNotImplementedError](#data_framework.modules.data_process.integrations.spark.utils.TransformationNotImplementedError)
  * [TransformationError](#data_framework.modules.data_process.integrations.spark.utils.TransformationError)
  * [CoreCatalogue](#data_framework.modules.data_process.integrations.spark.utils.CoreCatalogue)
  * [import\_module](#data_framework.modules.data_process.integrations.spark.utils.import_module)
  * [List](#data_framework.modules.data_process.integrations.spark.utils.List)
  * [Dict](#data_framework.modules.data_process.integrations.spark.utils.Dict)
  * [Any](#data_framework.modules.data_process.integrations.spark.utils.Any)
  * [BytesIO](#data_framework.modules.data_process.integrations.spark.utils.BytesIO)
  * [f](#data_framework.modules.data_process.integrations.spark.utils.f)
  * [DataFrame](#data_framework.modules.data_process.integrations.spark.utils.DataFrame)
  * [StructType](#data_framework.modules.data_process.integrations.spark.utils.StructType)
  * [StructField](#data_framework.modules.data_process.integrations.spark.utils.StructField)
  * [StringType](#data_framework.modules.data_process.integrations.spark.utils.StringType)
  * [IntegerType](#data_framework.modules.data_process.integrations.spark.utils.IntegerType)
  * [FloatType](#data_framework.modules.data_process.integrations.spark.utils.FloatType)
  * [DoubleType](#data_framework.modules.data_process.integrations.spark.utils.DoubleType)
  * [BooleanType](#data_framework.modules.data_process.integrations.spark.utils.BooleanType)
  * [DateType](#data_framework.modules.data_process.integrations.spark.utils.DateType)
  * [TimestampType](#data_framework.modules.data_process.integrations.spark.utils.TimestampType)
  * [re](#data_framework.modules.data_process.integrations.spark.utils.re)
  * [json](#data_framework.modules.data_process.integrations.spark.utils.json)
  * [convert\_schema\_to\_strings](#data_framework.modules.data_process.integrations.spark.utils.convert_schema_to_strings)
  * [map\_to\_spark\_type](#data_framework.modules.data_process.integrations.spark.utils.map_to_spark_type)
  * [apply\_transformations](#data_framework.modules.data_process.integrations.spark.utils.apply_transformations)
  * [fix\_column\_incompatible\_characters](#data_framework.modules.data_process.integrations.spark.utils.fix_column_incompatible_characters)
  * [fix\_incompatible\_characters](#data_framework.modules.data_process.integrations.spark.utils.fix_incompatible_characters)
  * [parse\_json](#data_framework.modules.data_process.integrations.spark.utils.parse_json)
* [data\_framework.modules.data\_process.interface\_data\_process](#data_framework.modules.data_process.interface_data_process)
  * [ABC](#data_framework.modules.data_process.interface_data_process.ABC)
  * [abstractmethod](#data_framework.modules.data_process.interface_data_process.abstractmethod)
  * [dataclass](#data_framework.modules.data_process.interface_data_process.dataclass)
  * [List](#data_framework.modules.data_process.interface_data_process.List)
  * [Any](#data_framework.modules.data_process.interface_data_process.Any)
  * [DatabaseTable](#data_framework.modules.data_process.interface_data_process.DatabaseTable)
  * [ReadResponse](#data_framework.modules.data_process.interface_data_process.ReadResponse)
    * [success](#data_framework.modules.data_process.interface_data_process.ReadResponse.success)
    * [error](#data_framework.modules.data_process.interface_data_process.ReadResponse.error)
    * [data](#data_framework.modules.data_process.interface_data_process.ReadResponse.data)
  * [WriteResponse](#data_framework.modules.data_process.interface_data_process.WriteResponse)
    * [success](#data_framework.modules.data_process.interface_data_process.WriteResponse.success)
    * [error](#data_framework.modules.data_process.interface_data_process.WriteResponse.error)
  * [DataProcessInterface](#data_framework.modules.data_process.interface_data_process.DataProcessInterface)
    * [merge](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.merge)
    * [insert\_overwrite](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.insert_overwrite)
    * [datacast](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.datacast)
    * [read\_table](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.read_table)
    * [delete\_from\_table](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.delete_from_table)
    * [insert\_dataframe](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.insert_dataframe)
    * [join](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.join)
    * [create\_dataframe](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.create_dataframe)
    * [query](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.query)
    * [overwrite\_columns](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.overwrite_columns)
    * [unfold\_string\_values](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.unfold_string_values)
    * [add\_dynamic\_column](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.add_dynamic_column)
    * [stack\_columns](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.stack_columns)
    * [is\_empty](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.is_empty)
    * [count\_rows](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.count_rows)
    * [select\_columns](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.select_columns)
    * [show\_dataframe](#data_framework.modules.data_process.interface_data_process.DataProcessInterface.show_dataframe)
* [data\_framework.modules.exception.aws\_exceptions](#data_framework.modules.exception.aws_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.aws_exceptions.DataFrameworkError)
  * [STSError](#data_framework.modules.exception.aws_exceptions.STSError)
    * [\_\_init\_\_](#data_framework.modules.exception.aws_exceptions.STSError.__init__)
  * [GlueError](#data_framework.modules.exception.aws_exceptions.GlueError)
    * [\_\_init\_\_](#data_framework.modules.exception.aws_exceptions.GlueError.__init__)
  * [AthenaError](#data_framework.modules.exception.aws_exceptions.AthenaError)
    * [\_\_init\_\_](#data_framework.modules.exception.aws_exceptions.AthenaError.__init__)
  * [SSMError](#data_framework.modules.exception.aws_exceptions.SSMError)
    * [\_\_init\_\_](#data_framework.modules.exception.aws_exceptions.SSMError.__init__)
  * [S3Error](#data_framework.modules.exception.aws_exceptions.S3Error)
    * [\_\_init\_\_](#data_framework.modules.exception.aws_exceptions.S3Error.__init__)
* [data\_framework.modules.exception.catalogue\_exceptions](#data_framework.modules.exception.catalogue_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.catalogue_exceptions.DataFrameworkError)
  * [CreatePartitionError](#data_framework.modules.exception.catalogue_exceptions.CreatePartitionError)
    * [\_\_init\_\_](#data_framework.modules.exception.catalogue_exceptions.CreatePartitionError.__init__)
  * [InvalidPartitionFieldError](#data_framework.modules.exception.catalogue_exceptions.InvalidPartitionFieldError)
    * [\_\_init\_\_](#data_framework.modules.exception.catalogue_exceptions.InvalidPartitionFieldError.__init__)
  * [SchemaError](#data_framework.modules.exception.catalogue_exceptions.SchemaError)
    * [\_\_init\_\_](#data_framework.modules.exception.catalogue_exceptions.SchemaError.__init__)
* [data\_framework.modules.exception.config\_exceptions](#data_framework.modules.exception.config_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.config_exceptions.DataFrameworkError)
  * [List](#data_framework.modules.exception.config_exceptions.List)
  * [ConfigError](#data_framework.modules.exception.config_exceptions.ConfigError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.ConfigError.__init__)
  * [ConfigFileNotFoundError](#data_framework.modules.exception.config_exceptions.ConfigFileNotFoundError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.ConfigFileNotFoundError.__init__)
  * [AccountNotFoundError](#data_framework.modules.exception.config_exceptions.AccountNotFoundError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.AccountNotFoundError.__init__)
  * [ParameterParseError](#data_framework.modules.exception.config_exceptions.ParameterParseError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.ParameterParseError.__init__)
  * [ConfigParseError](#data_framework.modules.exception.config_exceptions.ConfigParseError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.ConfigParseError.__init__)
  * [EmptyProcessConfigError](#data_framework.modules.exception.config_exceptions.EmptyProcessConfigError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.EmptyProcessConfigError.__init__)
  * [ProcessNotFoundError](#data_framework.modules.exception.config_exceptions.ProcessNotFoundError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.ProcessNotFoundError.__init__)
  * [TableKeyError](#data_framework.modules.exception.config_exceptions.TableKeyError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.TableKeyError.__init__)
  * [TableConfigNotFoundError](#data_framework.modules.exception.config_exceptions.TableConfigNotFoundError)
    * [\_\_init\_\_](#data_framework.modules.exception.config_exceptions.TableConfigNotFoundError.__init__)
* [data\_framework.modules.exception.dataflow\_exceptions](#data_framework.modules.exception.dataflow_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.dataflow_exceptions.DataFrameworkError)
  * [DataflowInitializationError](#data_framework.modules.exception.dataflow_exceptions.DataflowInitializationError)
    * [\_\_init\_\_](#data_framework.modules.exception.dataflow_exceptions.DataflowInitializationError.__init__)
  * [PayloadResponseError](#data_framework.modules.exception.dataflow_exceptions.PayloadResponseError)
    * [\_\_init\_\_](#data_framework.modules.exception.dataflow_exceptions.PayloadResponseError.__init__)
* [data\_framework.modules.exception.data\_process\_exceptions](#data_framework.modules.exception.data_process_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.data_process_exceptions.DataFrameworkError)
  * [List](#data_framework.modules.exception.data_process_exceptions.List)
  * [TransformationNotImplementedError](#data_framework.modules.exception.data_process_exceptions.TransformationNotImplementedError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.TransformationNotImplementedError.__init__)
  * [TransformationError](#data_framework.modules.exception.data_process_exceptions.TransformationError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.TransformationError.__init__)
  * [CastQueryError](#data_framework.modules.exception.data_process_exceptions.CastQueryError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.CastQueryError.__init__)
  * [CastDataError](#data_framework.modules.exception.data_process_exceptions.CastDataError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.CastDataError.__init__)
  * [ReadDataError](#data_framework.modules.exception.data_process_exceptions.ReadDataError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.ReadDataError.__init__)
  * [WriteDataError](#data_framework.modules.exception.data_process_exceptions.WriteDataError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.WriteDataError.__init__)
  * [DeleteDataError](#data_framework.modules.exception.data_process_exceptions.DeleteDataError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.DeleteDataError.__init__)
  * [DataProcessError](#data_framework.modules.exception.data_process_exceptions.DataProcessError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.DataProcessError.__init__)
  * [SparkConfigurationError](#data_framework.modules.exception.data_process_exceptions.SparkConfigurationError)
    * [\_\_init\_\_](#data_framework.modules.exception.data_process_exceptions.SparkConfigurationError.__init__)
* [data\_framework.modules.exception.generic\_exceptions](#data_framework.modules.exception.generic_exceptions)
  * [traceback](#data_framework.modules.exception.generic_exceptions.traceback)
  * [DataFrameworkError](#data_framework.modules.exception.generic_exceptions.DataFrameworkError)
    * [format\_exception](#data_framework.modules.exception.generic_exceptions.DataFrameworkError.format_exception)
  * [LoggerInitializationError](#data_framework.modules.exception.generic_exceptions.LoggerInitializationError)
    * [\_\_init\_\_](#data_framework.modules.exception.generic_exceptions.LoggerInitializationError.__init__)
* [data\_framework.modules.exception.landing\_exceptions](#data_framework.modules.exception.landing_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.landing_exceptions.DataFrameworkError)
  * [FileProcessError](#data_framework.modules.exception.landing_exceptions.FileProcessError)
    * [\_\_init\_\_](#data_framework.modules.exception.landing_exceptions.FileProcessError.__init__)
  * [FileReadError](#data_framework.modules.exception.landing_exceptions.FileReadError)
    * [\_\_init\_\_](#data_framework.modules.exception.landing_exceptions.FileReadError.__init__)
  * [InvalidDateRegexError](#data_framework.modules.exception.landing_exceptions.InvalidDateRegexError)
    * [\_\_init\_\_](#data_framework.modules.exception.landing_exceptions.InvalidDateRegexError.__init__)
  * [InvalidRegexGroupError](#data_framework.modules.exception.landing_exceptions.InvalidRegexGroupError)
    * [\_\_init\_\_](#data_framework.modules.exception.landing_exceptions.InvalidRegexGroupError.__init__)
  * [InvalidFileError](#data_framework.modules.exception.landing_exceptions.InvalidFileError)
    * [\_\_init\_\_](#data_framework.modules.exception.landing_exceptions.InvalidFileError.__init__)
* [data\_framework.modules.exception.notification\_exceptions](#data_framework.modules.exception.notification_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.notification_exceptions.DataFrameworkError)
  * [List](#data_framework.modules.exception.notification_exceptions.List)
  * [NotificationNotFoundError](#data_framework.modules.exception.notification_exceptions.NotificationNotFoundError)
    * [\_\_init\_\_](#data_framework.modules.exception.notification_exceptions.NotificationNotFoundError.__init__)
  * [DuplicatedNotificationError](#data_framework.modules.exception.notification_exceptions.DuplicatedNotificationError)
    * [\_\_init\_\_](#data_framework.modules.exception.notification_exceptions.DuplicatedNotificationError.__init__)
  * [NotificationError](#data_framework.modules.exception.notification_exceptions.NotificationError)
    * [\_\_init\_\_](#data_framework.modules.exception.notification_exceptions.NotificationError.__init__)
  * [SubjectLimitExceededError](#data_framework.modules.exception.notification_exceptions.SubjectLimitExceededError)
    * [\_\_init\_\_](#data_framework.modules.exception.notification_exceptions.SubjectLimitExceededError.__init__)
  * [BodyLimitExceededError](#data_framework.modules.exception.notification_exceptions.BodyLimitExceededError)
    * [\_\_init\_\_](#data_framework.modules.exception.notification_exceptions.BodyLimitExceededError.__init__)
  * [NotificationLimitExceededError](#data_framework.modules.exception.notification_exceptions.NotificationLimitExceededError)
    * [\_\_init\_\_](#data_framework.modules.exception.notification_exceptions.NotificationLimitExceededError.__init__)
* [data\_framework.modules.exception.output\_exceptions](#data_framework.modules.exception.output_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.output_exceptions.DataFrameworkError)
  * [List](#data_framework.modules.exception.output_exceptions.List)
  * [OutputError](#data_framework.modules.exception.output_exceptions.OutputError)
    * [\_\_init\_\_](#data_framework.modules.exception.output_exceptions.OutputError.__init__)
  * [OutputGenerationError](#data_framework.modules.exception.output_exceptions.OutputGenerationError)
    * [\_\_init\_\_](#data_framework.modules.exception.output_exceptions.OutputGenerationError.__init__)
  * [NoOutputDataError](#data_framework.modules.exception.output_exceptions.NoOutputDataError)
    * [\_\_init\_\_](#data_framework.modules.exception.output_exceptions.NoOutputDataError.__init__)
* [data\_framework.modules.exception.storage\_exceptions](#data_framework.modules.exception.storage_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.storage_exceptions.DataFrameworkError)
  * [StorageError](#data_framework.modules.exception.storage_exceptions.StorageError)
    * [\_\_init\_\_](#data_framework.modules.exception.storage_exceptions.StorageError.__init__)
  * [StorageReadError](#data_framework.modules.exception.storage_exceptions.StorageReadError)
    * [\_\_init\_\_](#data_framework.modules.exception.storage_exceptions.StorageReadError.__init__)
  * [StorageWriteError](#data_framework.modules.exception.storage_exceptions.StorageWriteError)
    * [\_\_init\_\_](#data_framework.modules.exception.storage_exceptions.StorageWriteError.__init__)
* [data\_framework.modules.exception.validation\_exceptions](#data_framework.modules.exception.validation_exceptions)
  * [DataFrameworkError](#data_framework.modules.exception.validation_exceptions.DataFrameworkError)
  * [QualityControlsError](#data_framework.modules.exception.validation_exceptions.QualityControlsError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.QualityControlsError.__init__)
  * [FailedRulesError](#data_framework.modules.exception.validation_exceptions.FailedRulesError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.FailedRulesError.__init__)
  * [RuleComputeError](#data_framework.modules.exception.validation_exceptions.RuleComputeError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.RuleComputeError.__init__)
  * [ValidationFunctionNotFoundError](#data_framework.modules.exception.validation_exceptions.ValidationFunctionNotFoundError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.ValidationFunctionNotFoundError.__init__)
  * [ParentNotConfiguredError](#data_framework.modules.exception.validation_exceptions.ParentNotConfiguredError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.ParentNotConfiguredError.__init__)
  * [InvalidThresholdError](#data_framework.modules.exception.validation_exceptions.InvalidThresholdError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.InvalidThresholdError.__init__)
  * [InvalidAlgorithmError](#data_framework.modules.exception.validation_exceptions.InvalidAlgorithmError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.InvalidAlgorithmError.__init__)
  * [InvalidRuleError](#data_framework.modules.exception.validation_exceptions.InvalidRuleError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.InvalidRuleError.__init__)
  * [InvalidDataFrameError](#data_framework.modules.exception.validation_exceptions.InvalidDataFrameError)
    * [\_\_init\_\_](#data_framework.modules.exception.validation_exceptions.InvalidDataFrameError.__init__)
* [data\_framework.modules.monitoring.core\_monitoring](#data_framework.modules.monitoring.core_monitoring)
  * [LazyClassProperty](#data_framework.modules.monitoring.core_monitoring.LazyClassProperty)
  * [MonitoringInterface](#data_framework.modules.monitoring.core_monitoring.MonitoringInterface)
  * [Metric](#data_framework.modules.monitoring.core_monitoring.Metric)
  * [MetricNames](#data_framework.modules.monitoring.core_monitoring.MetricNames)
  * [Union](#data_framework.modules.monitoring.core_monitoring.Union)
  * [CoreMonitoring](#data_framework.modules.monitoring.core_monitoring.CoreMonitoring)
    * [\_monitoring](#data_framework.modules.monitoring.core_monitoring.CoreMonitoring._monitoring)
    * [track\_metric](#data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_metric)
    * [track\_table\_metric](#data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_table_metric)
    * [track\_process\_metric](#data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_process_metric)
    * [track\_computation\_metric](#data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_computation_metric)
* [data\_framework.modules.monitoring.integrations.aws\_cloudwatch.aws\_cloudwatch\_monitoring](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring)
  * [MonitoringInterface](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.MonitoringInterface)
  * [Metric](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Metric)
  * [MetricNames](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.MetricNames)
  * [MetricUnits](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.MetricUnits)
  * [Union](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Union)
  * [Optional](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Optional)
  * [List](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.List)
  * [Dict](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Dict)
  * [Any](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Any)
  * [dataclass](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.dataclass)
  * [config](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.config)
  * [logger](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.logger)
  * [datetime](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.datetime)
  * [boto3](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.boto3)
  * [os](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.os)
  * [cloudwatch](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.cloudwatch)
  * [BaseClient](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.BaseClient)
  * [InternalMetric](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.InternalMetric)
    * [parse](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.InternalMetric.parse)
  * [AWSCloudWatch](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch)
    * [\_\_namespace](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__namespace)
    * [\_\_computation\_dimensions](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__computation_dimensions)
    * [\_\_process\_dimensions](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__process_dimensions)
    * [\_\_table\_dimensions](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__table_dimensions)
    * [namespace](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.namespace)
    * [client](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.client)
    * [\_\_init\_\_](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__init__)
    * [track\_metric](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_metric)
    * [track\_table\_metric](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_table_metric)
    * [track\_process\_metric](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_process_metric)
    * [track\_computation\_metric](#data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_computation_metric)
* [data\_framework.modules.monitoring.interface\_monitoring](#data_framework.modules.monitoring.interface_monitoring)
  * [ABC](#data_framework.modules.monitoring.interface_monitoring.ABC)
  * [abstractmethod](#data_framework.modules.monitoring.interface_monitoring.abstractmethod)
  * [Union](#data_framework.modules.monitoring.interface_monitoring.Union)
  * [Optional](#data_framework.modules.monitoring.interface_monitoring.Optional)
  * [List](#data_framework.modules.monitoring.interface_monitoring.List)
  * [Dict](#data_framework.modules.monitoring.interface_monitoring.Dict)
  * [Any](#data_framework.modules.monitoring.interface_monitoring.Any)
  * [dataclass](#data_framework.modules.monitoring.interface_monitoring.dataclass)
  * [field](#data_framework.modules.monitoring.interface_monitoring.field)
  * [fields](#data_framework.modules.monitoring.interface_monitoring.fields)
  * [Enum](#data_framework.modules.monitoring.interface_monitoring.Enum)
  * [time](#data_framework.modules.monitoring.interface_monitoring.time)
  * [config](#data_framework.modules.monitoring.interface_monitoring.config)
  * [os](#data_framework.modules.monitoring.interface_monitoring.os)
  * [MetricUnits](#data_framework.modules.monitoring.interface_monitoring.MetricUnits)
    * [SECONDS](#data_framework.modules.monitoring.interface_monitoring.MetricUnits.SECONDS)
    * [MEGABYTES](#data_framework.modules.monitoring.interface_monitoring.MetricUnits.MEGABYTES)
    * [COUNT](#data_framework.modules.monitoring.interface_monitoring.MetricUnits.COUNT)
    * [PERCENT](#data_framework.modules.monitoring.interface_monitoring.MetricUnits.PERCENT)
    * [NONE](#data_framework.modules.monitoring.interface_monitoring.MetricUnits.NONE)
  * [MetricNames](#data_framework.modules.monitoring.interface_monitoring.MetricNames)
    * [UNKNOWN](#data_framework.modules.monitoring.interface_monitoring.MetricNames.UNKNOWN)
    * [TABLE\_READ\_RECORDS](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_READ_RECORDS)
    * [TABLE\_READ\_SIZE](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_READ_SIZE)
    * [TABLE\_WRITE\_ADDED\_RECORDS](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_ADDED_RECORDS)
    * [TABLE\_WRITE\_ADDED\_SIZE](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_ADDED_SIZE)
    * [TABLE\_WRITE\_DELETED\_RECORDS](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_DELETED_RECORDS)
    * [TABLE\_WRITE\_DELETED\_SIZE](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_DELETED_SIZE)
    * [TABLE\_WRITE\_TOTAL\_RECORDS](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_TOTAL_RECORDS)
    * [TABLE\_WRITE\_TOTAL\_SIZE](#data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_TOTAL_SIZE)
    * [DATAFLOW\_START\_EVENT](#data_framework.modules.monitoring.interface_monitoring.MetricNames.DATAFLOW_START_EVENT)
    * [DATAFLOW\_END\_EVENT](#data_framework.modules.monitoring.interface_monitoring.MetricNames.DATAFLOW_END_EVENT)
    * [PROCESS\_START\_EVENT](#data_framework.modules.monitoring.interface_monitoring.MetricNames.PROCESS_START_EVENT)
    * [PROCESS\_END\_EVENT](#data_framework.modules.monitoring.interface_monitoring.MetricNames.PROCESS_END_EVENT)
    * [PROCESS\_DURATION](#data_framework.modules.monitoring.interface_monitoring.MetricNames.PROCESS_DURATION)
    * [unit](#data_framework.modules.monitoring.interface_monitoring.MetricNames.unit)
  * [Metric](#data_framework.modules.monitoring.interface_monitoring.Metric)
    * [\_created\_at](#data_framework.modules.monitoring.interface_monitoring.Metric._created_at)
    * [name](#data_framework.modules.monitoring.interface_monitoring.Metric.name)
    * [value](#data_framework.modules.monitoring.interface_monitoring.Metric.value)
    * [dimensions](#data_framework.modules.monitoring.interface_monitoring.Metric.dimensions)
  * [MonitoringInterface](#data_framework.modules.monitoring.interface_monitoring.MonitoringInterface)
    * [track\_metric](#data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_metric)
    * [track\_table\_metric](#data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_table_metric)
    * [track\_process\_metric](#data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_process_metric)
    * [track\_computation\_metric](#data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_computation_metric)
* [data\_framework.modules.notification.core\_notifications](#data_framework.modules.notification.core_notifications)
  * [LazyClassProperty](#data_framework.modules.notification.core_notifications.LazyClassProperty)
  * [InterfaceNotifications](#data_framework.modules.notification.core_notifications.InterfaceNotifications)
  * [Notification](#data_framework.modules.notification.core_notifications.Notification)
  * [Any](#data_framework.modules.notification.core_notifications.Any)
  * [Dict](#data_framework.modules.notification.core_notifications.Dict)
  * [List](#data_framework.modules.notification.core_notifications.List)
  * [CoreNotifications](#data_framework.modules.notification.core_notifications.CoreNotifications)
    * [\_notifications](#data_framework.modules.notification.core_notifications.CoreNotifications._notifications)
    * [send\_notification](#data_framework.modules.notification.core_notifications.CoreNotifications.send_notification)
    * [get\_notifications\_to\_send](#data_framework.modules.notification.core_notifications.CoreNotifications.get_notifications_to_send)
* [data\_framework.modules.notification.interface\_notifications](#data_framework.modules.notification.interface_notifications)
  * [NotificationNotFoundError](#data_framework.modules.notification.interface_notifications.NotificationNotFoundError)
  * [ABC](#data_framework.modules.notification.interface_notifications.ABC)
  * [abstractmethod](#data_framework.modules.notification.interface_notifications.abstractmethod)
  * [Any](#data_framework.modules.notification.interface_notifications.Any)
  * [Dict](#data_framework.modules.notification.interface_notifications.Dict)
  * [List](#data_framework.modules.notification.interface_notifications.List)
  * [Union](#data_framework.modules.notification.interface_notifications.Union)
  * [dataclass](#data_framework.modules.notification.interface_notifications.dataclass)
  * [field](#data_framework.modules.notification.interface_notifications.field)
  * [Enum](#data_framework.modules.notification.interface_notifications.Enum)
  * [NotificationType](#data_framework.modules.notification.interface_notifications.NotificationType)
    * [EMAIL](#data_framework.modules.notification.interface_notifications.NotificationType.EMAIL)
  * [Topic](#data_framework.modules.notification.interface_notifications.Topic)
    * [INTERNAL](#data_framework.modules.notification.interface_notifications.Topic.INTERNAL)
    * [EXTERNAL](#data_framework.modules.notification.interface_notifications.Topic.EXTERNAL)
  * [Notification](#data_framework.modules.notification.interface_notifications.Notification)
    * [type](#data_framework.modules.notification.interface_notifications.Notification.type)
    * [topics](#data_framework.modules.notification.interface_notifications.Notification.topics)
    * [subject](#data_framework.modules.notification.interface_notifications.Notification.subject)
    * [body](#data_framework.modules.notification.interface_notifications.Notification.body)
    * [active](#data_framework.modules.notification.interface_notifications.Notification.active)
  * [NotificationToSend](#data_framework.modules.notification.interface_notifications.NotificationToSend)
    * [type](#data_framework.modules.notification.interface_notifications.NotificationToSend.type)
    * [topics](#data_framework.modules.notification.interface_notifications.NotificationToSend.topics)
    * [subject](#data_framework.modules.notification.interface_notifications.NotificationToSend.subject)
    * [body](#data_framework.modules.notification.interface_notifications.NotificationToSend.body)
  * [NotificationDict](#data_framework.modules.notification.interface_notifications.NotificationDict)
    * [notifications](#data_framework.modules.notification.interface_notifications.NotificationDict.notifications)
    * [get\_notification](#data_framework.modules.notification.interface_notifications.NotificationDict.get_notification)
  * [NotificationsParameters](#data_framework.modules.notification.interface_notifications.NotificationsParameters)
    * [max\_subject\_length](#data_framework.modules.notification.interface_notifications.NotificationsParameters.max_subject_length)
    * [max\_body\_length](#data_framework.modules.notification.interface_notifications.NotificationsParameters.max_body_length)
    * [max\_number\_of\_notifications](#data_framework.modules.notification.interface_notifications.NotificationsParameters.max_number_of_notifications)
    * [signature](#data_framework.modules.notification.interface_notifications.NotificationsParameters.signature)
  * [DataFrameworkNotifications](#data_framework.modules.notification.interface_notifications.DataFrameworkNotifications)
    * [notifications](#data_framework.modules.notification.interface_notifications.DataFrameworkNotifications.notifications)
    * [parameters](#data_framework.modules.notification.interface_notifications.DataFrameworkNotifications.parameters)
  * [InterfaceNotifications](#data_framework.modules.notification.interface_notifications.InterfaceNotifications)
    * [send\_notification](#data_framework.modules.notification.interface_notifications.InterfaceNotifications.send_notification)
    * [get\_notifications\_to\_send](#data_framework.modules.notification.interface_notifications.InterfaceNotifications.get_notifications_to_send)
* [data\_framework.modules.notification.notifications](#data_framework.modules.notification.notifications)
  * [config](#data_framework.modules.notification.notifications.config)
  * [logger](#data_framework.modules.notification.notifications.logger)
  * [Environment](#data_framework.modules.notification.notifications.Environment)
  * [ExecutionMode](#data_framework.modules.notification.notifications.ExecutionMode)
  * [InterfaceNotifications](#data_framework.modules.notification.notifications.InterfaceNotifications)
  * [NotificationDict](#data_framework.modules.notification.notifications.NotificationDict)
  * [NotificationType](#data_framework.modules.notification.notifications.NotificationType)
  * [NotificationToSend](#data_framework.modules.notification.notifications.NotificationToSend)
  * [Notification](#data_framework.modules.notification.notifications.Notification)
  * [DuplicatedNotificationError](#data_framework.modules.notification.notifications.DuplicatedNotificationError)
  * [NotificationError](#data_framework.modules.notification.notifications.NotificationError)
  * [NotificationLimitExceededError](#data_framework.modules.notification.notifications.NotificationLimitExceededError)
  * [SubjectLimitExceededError](#data_framework.modules.notification.notifications.SubjectLimitExceededError)
  * [BodyLimitExceededError](#data_framework.modules.notification.notifications.BodyLimitExceededError)
  * [Dict](#data_framework.modules.notification.notifications.Dict)
  * [Any](#data_framework.modules.notification.notifications.Any)
  * [List](#data_framework.modules.notification.notifications.List)
  * [Notifications](#data_framework.modules.notification.notifications.Notifications)
    * [\_\_init\_\_](#data_framework.modules.notification.notifications.Notifications.__init__)
    * [\_combine\_notifications](#data_framework.modules.notification.notifications.Notifications._combine_notifications)
    * [send\_notification](#data_framework.modules.notification.notifications.Notifications.send_notification)
    * [\_send\_email\_notification](#data_framework.modules.notification.notifications.Notifications._send_email_notification)
    * [\_format\_subject](#data_framework.modules.notification.notifications.Notifications._format_subject)
    * [\_format\_body](#data_framework.modules.notification.notifications.Notifications._format_body)
    * [\_validate\_subject\_length](#data_framework.modules.notification.notifications.Notifications._validate_subject_length)
    * [\_validate\_body\_length](#data_framework.modules.notification.notifications.Notifications._validate_body_length)
    * [\_add\_notification](#data_framework.modules.notification.notifications.Notifications._add_notification)
    * [get\_notifications\_to\_send](#data_framework.modules.notification.notifications.Notifications.get_notifications_to_send)
* [data\_framework.modules.storage.core\_storage](#data_framework.modules.storage.core_storage)
  * [config](#data_framework.modules.storage.core_storage.config)
  * [Environment](#data_framework.modules.storage.core_storage.Environment)
  * [LazyClassProperty](#data_framework.modules.storage.core_storage.LazyClassProperty)
  * [CoreStorageInterface](#data_framework.modules.storage.core_storage.CoreStorageInterface)
  * [Database](#data_framework.modules.storage.core_storage.Database)
  * [Layer](#data_framework.modules.storage.core_storage.Layer)
  * [ReadResponse](#data_framework.modules.storage.core_storage.ReadResponse)
  * [WriteResponse](#data_framework.modules.storage.core_storage.WriteResponse)
  * [ListResponse](#data_framework.modules.storage.core_storage.ListResponse)
  * [PathResponse](#data_framework.modules.storage.core_storage.PathResponse)
  * [Storage](#data_framework.modules.storage.core_storage.Storage)
    * [\_storage](#data_framework.modules.storage.core_storage.Storage._storage)
    * [read](#data_framework.modules.storage.core_storage.Storage.read)
    * [write](#data_framework.modules.storage.core_storage.Storage.write)
    * [write\_to\_path](#data_framework.modules.storage.core_storage.Storage.write_to_path)
    * [list\_files](#data_framework.modules.storage.core_storage.Storage.list_files)
    * [raw\_layer\_path](#data_framework.modules.storage.core_storage.Storage.raw_layer_path)
    * [base\_layer\_path](#data_framework.modules.storage.core_storage.Storage.base_layer_path)
* [data\_framework.modules.storage.integrations.local\_storage](#data_framework.modules.storage.integrations.local_storage)
  * [logger](#data_framework.modules.storage.integrations.local_storage.logger)
  * [config](#data_framework.modules.storage.integrations.local_storage.config)
  * [CoreStorageInterface](#data_framework.modules.storage.integrations.local_storage.CoreStorageInterface)
  * [Database](#data_framework.modules.storage.integrations.local_storage.Database)
  * [Layer](#data_framework.modules.storage.integrations.local_storage.Layer)
  * [ReadResponse](#data_framework.modules.storage.integrations.local_storage.ReadResponse)
  * [WriteResponse](#data_framework.modules.storage.integrations.local_storage.WriteResponse)
  * [ListResponse](#data_framework.modules.storage.integrations.local_storage.ListResponse)
  * [PathResponse](#data_framework.modules.storage.integrations.local_storage.PathResponse)
  * [StorageError](#data_framework.modules.storage.integrations.local_storage.StorageError)
  * [StorageReadError](#data_framework.modules.storage.integrations.local_storage.StorageReadError)
  * [StorageWriteError](#data_framework.modules.storage.integrations.local_storage.StorageWriteError)
  * [os](#data_framework.modules.storage.integrations.local_storage.os)
  * [LocalStorage](#data_framework.modules.storage.integrations.local_storage.LocalStorage)
    * [\_\_init\_\_](#data_framework.modules.storage.integrations.local_storage.LocalStorage.__init__)
    * [read](#data_framework.modules.storage.integrations.local_storage.LocalStorage.read)
    * [\_build\_folder\_name](#data_framework.modules.storage.integrations.local_storage.LocalStorage._build_folder_name)
    * [\_build\_file\_path](#data_framework.modules.storage.integrations.local_storage.LocalStorage._build_file_path)
    * [write](#data_framework.modules.storage.integrations.local_storage.LocalStorage.write)
    * [write\_to\_path](#data_framework.modules.storage.integrations.local_storage.LocalStorage.write_to_path)
    * [list\_files](#data_framework.modules.storage.integrations.local_storage.LocalStorage.list_files)
    * [raw\_layer\_path](#data_framework.modules.storage.integrations.local_storage.LocalStorage.raw_layer_path)
    * [base\_layer\_path](#data_framework.modules.storage.integrations.local_storage.LocalStorage.base_layer_path)
* [data\_framework.modules.storage.integrations.s3\_storage](#data_framework.modules.storage.integrations.s3_storage)
  * [boto3](#data_framework.modules.storage.integrations.s3_storage.boto3)
  * [logger](#data_framework.modules.storage.integrations.s3_storage.logger)
  * [config](#data_framework.modules.storage.integrations.s3_storage.config)
  * [CoreStorageInterface](#data_framework.modules.storage.integrations.s3_storage.CoreStorageInterface)
  * [Database](#data_framework.modules.storage.integrations.s3_storage.Database)
  * [Layer](#data_framework.modules.storage.integrations.s3_storage.Layer)
  * [ReadResponse](#data_framework.modules.storage.integrations.s3_storage.ReadResponse)
  * [WriteResponse](#data_framework.modules.storage.integrations.s3_storage.WriteResponse)
  * [ListResponse](#data_framework.modules.storage.integrations.s3_storage.ListResponse)
  * [PathResponse](#data_framework.modules.storage.integrations.s3_storage.PathResponse)
  * [S3Error](#data_framework.modules.storage.integrations.s3_storage.S3Error)
  * [StorageError](#data_framework.modules.storage.integrations.s3_storage.StorageError)
  * [StorageReadError](#data_framework.modules.storage.integrations.s3_storage.StorageReadError)
  * [StorageWriteError](#data_framework.modules.storage.integrations.s3_storage.StorageWriteError)
  * [ExecutionMode](#data_framework.modules.storage.integrations.s3_storage.ExecutionMode)
  * [NamedTuple](#data_framework.modules.storage.integrations.s3_storage.NamedTuple)
  * [BuildPathResponse](#data_framework.modules.storage.integrations.s3_storage.BuildPathResponse)
    * [base\_path](#data_framework.modules.storage.integrations.s3_storage.BuildPathResponse.base_path)
    * [data\_path](#data_framework.modules.storage.integrations.s3_storage.BuildPathResponse.data_path)
  * [S3Storage](#data_framework.modules.storage.integrations.s3_storage.S3Storage)
    * [\_\_init\_\_](#data_framework.modules.storage.integrations.s3_storage.S3Storage.__init__)
    * [read](#data_framework.modules.storage.integrations.s3_storage.S3Storage.read)
    * [\_build\_s3\_bucket\_name](#data_framework.modules.storage.integrations.s3_storage.S3Storage._build_s3_bucket_name)
    * [\_build\_s3\_key\_path](#data_framework.modules.storage.integrations.s3_storage.S3Storage._build_s3_key_path)
    * [write](#data_framework.modules.storage.integrations.s3_storage.S3Storage.write)
    * [write\_to\_path](#data_framework.modules.storage.integrations.s3_storage.S3Storage.write_to_path)
    * [list\_files](#data_framework.modules.storage.integrations.s3_storage.S3Storage.list_files)
    * [raw\_layer\_path](#data_framework.modules.storage.integrations.s3_storage.S3Storage.raw_layer_path)
    * [base\_layer\_path](#data_framework.modules.storage.integrations.s3_storage.S3Storage.base_layer_path)
* [data\_framework.modules.storage.interface\_storage](#data_framework.modules.storage.interface_storage)
  * [ABC](#data_framework.modules.storage.interface_storage.ABC)
  * [abstractmethod](#data_framework.modules.storage.interface_storage.abstractmethod)
  * [Enum](#data_framework.modules.storage.interface_storage.Enum)
  * [dataclass](#data_framework.modules.storage.interface_storage.dataclass)
  * [Layer](#data_framework.modules.storage.interface_storage.Layer)
    * [LANDING](#data_framework.modules.storage.interface_storage.Layer.LANDING)
    * [RAW](#data_framework.modules.storage.interface_storage.Layer.RAW)
    * [STAGING](#data_framework.modules.storage.interface_storage.Layer.STAGING)
    * [COMMON](#data_framework.modules.storage.interface_storage.Layer.COMMON)
    * [BUSINESS](#data_framework.modules.storage.interface_storage.Layer.BUSINESS)
    * [OUTPUT](#data_framework.modules.storage.interface_storage.Layer.OUTPUT)
    * [ATHENA](#data_framework.modules.storage.interface_storage.Layer.ATHENA)
    * [TEMP](#data_framework.modules.storage.interface_storage.Layer.TEMP)
  * [Database](#data_framework.modules.storage.interface_storage.Database)
    * [FUNDS\_RAW](#data_framework.modules.storage.interface_storage.Database.FUNDS_RAW)
    * [FUNDS\_STAGING](#data_framework.modules.storage.interface_storage.Database.FUNDS_STAGING)
    * [FUNDS\_COMMON](#data_framework.modules.storage.interface_storage.Database.FUNDS_COMMON)
    * [FUNDS\_BUSINESS](#data_framework.modules.storage.interface_storage.Database.FUNDS_BUSINESS)
    * [DATA\_QUALITY](#data_framework.modules.storage.interface_storage.Database.DATA_QUALITY)
    * [CONFIG\_SCHEMAS](#data_framework.modules.storage.interface_storage.Database.CONFIG_SCHEMAS)
    * [INFINITY\_STAGING](#data_framework.modules.storage.interface_storage.Database.INFINITY_STAGING)
    * [INFINITY\_COMMON](#data_framework.modules.storage.interface_storage.Database.INFINITY_COMMON)
    * [INFINITY\_BUSINESS](#data_framework.modules.storage.interface_storage.Database.INFINITY_BUSINESS)
    * [SAM\_STG](#data_framework.modules.storage.interface_storage.Database.SAM_STG)
    * [SAM\_DWH](#data_framework.modules.storage.interface_storage.Database.SAM_DWH)
  * [ReadResponse](#data_framework.modules.storage.interface_storage.ReadResponse)
    * [success](#data_framework.modules.storage.interface_storage.ReadResponse.success)
    * [error](#data_framework.modules.storage.interface_storage.ReadResponse.error)
    * [data](#data_framework.modules.storage.interface_storage.ReadResponse.data)
  * [WriteResponse](#data_framework.modules.storage.interface_storage.WriteResponse)
    * [success](#data_framework.modules.storage.interface_storage.WriteResponse.success)
    * [error](#data_framework.modules.storage.interface_storage.WriteResponse.error)
  * [ListResponse](#data_framework.modules.storage.interface_storage.ListResponse)
    * [success](#data_framework.modules.storage.interface_storage.ListResponse.success)
    * [error](#data_framework.modules.storage.interface_storage.ListResponse.error)
    * [result](#data_framework.modules.storage.interface_storage.ListResponse.result)
  * [PathResponse](#data_framework.modules.storage.interface_storage.PathResponse)
    * [success](#data_framework.modules.storage.interface_storage.PathResponse.success)
    * [error](#data_framework.modules.storage.interface_storage.PathResponse.error)
    * [bucket](#data_framework.modules.storage.interface_storage.PathResponse.bucket)
    * [path](#data_framework.modules.storage.interface_storage.PathResponse.path)
    * [base\_path](#data_framework.modules.storage.interface_storage.PathResponse.base_path)
    * [relative\_path](#data_framework.modules.storage.interface_storage.PathResponse.relative_path)
    * [relative\_base\_path](#data_framework.modules.storage.interface_storage.PathResponse.relative_base_path)
  * [CoreStorageInterface](#data_framework.modules.storage.interface_storage.CoreStorageInterface)
    * [read](#data_framework.modules.storage.interface_storage.CoreStorageInterface.read)
    * [write](#data_framework.modules.storage.interface_storage.CoreStorageInterface.write)
    * [write\_to\_path](#data_framework.modules.storage.interface_storage.CoreStorageInterface.write_to_path)
    * [list\_files](#data_framework.modules.storage.interface_storage.CoreStorageInterface.list_files)
    * [raw\_layer\_path](#data_framework.modules.storage.interface_storage.CoreStorageInterface.raw_layer_path)
    * [base\_layer\_path](#data_framework.modules.storage.interface_storage.CoreStorageInterface.base_layer_path)
* [data\_framework.modules.utils.debug](#data_framework.modules.utils.debug)
  * [config](#data_framework.modules.utils.debug.config)
  * [Environment](#data_framework.modules.utils.debug.Environment)
  * [debug\_code](#data_framework.modules.utils.debug.debug_code)
* [data\_framework.modules.utils.logger](#data_framework.modules.utils.logger)
  * [LoggerInitializationError](#data_framework.modules.utils.logger.LoggerInitializationError)
  * [logging](#data_framework.modules.utils.logger.logging)
  * [threading](#data_framework.modules.utils.logger.threading)
  * [sys](#data_framework.modules.utils.logger.sys)
  * [LOG\_FORMAT](#data_framework.modules.utils.logger.LOG_FORMAT)
  * [DATE\_FORMAT](#data_framework.modules.utils.logger.DATE_FORMAT)
  * [Logger](#data_framework.modules.utils.logger.Logger)
    * [\_instance](#data_framework.modules.utils.logger.Logger._instance)
    * [\_lock](#data_framework.modules.utils.logger.Logger._lock)
    * [\_\_new\_\_](#data_framework.modules.utils.logger.Logger.__new__)
  * [logger](#data_framework.modules.utils.logger.logger)
* [data\_framework.modules.utils.regex](#data_framework.modules.utils.regex)
  * [re](#data_framework.modules.utils.regex.re)
  * [change\_file\_extension](#data_framework.modules.utils.regex.change_file_extension)
* [data\_framework.modules.validation.core\_quality\_controls](#data_framework.modules.validation.core_quality_controls)
  * [LazyClassProperty](#data_framework.modules.validation.core_quality_controls.LazyClassProperty)
  * [InterfaceQualityControls](#data_framework.modules.validation.core_quality_controls.InterfaceQualityControls)
  * [ControlsResponse](#data_framework.modules.validation.core_quality_controls.ControlsResponse)
  * [DatabaseTable](#data_framework.modules.validation.core_quality_controls.DatabaseTable)
  * [Layer](#data_framework.modules.validation.core_quality_controls.Layer)
  * [Any](#data_framework.modules.validation.core_quality_controls.Any)
  * [Dict](#data_framework.modules.validation.core_quality_controls.Dict)
  * [CoreQualityControls](#data_framework.modules.validation.core_quality_controls.CoreQualityControls)
    * [\_quality\_controls](#data_framework.modules.validation.core_quality_controls.CoreQualityControls._quality_controls)
    * [validate](#data_framework.modules.validation.core_quality_controls.CoreQualityControls.validate)
    * [set\_parent](#data_framework.modules.validation.core_quality_controls.CoreQualityControls.set_parent)
* [data\_framework.modules.validation.integrations.file\_validator](#data_framework.modules.validation.integrations.file_validator)
  * [config](#data_framework.modules.validation.integrations.file_validator.config)
  * [logger](#data_framework.modules.validation.integrations.file_validator.logger)
  * [CoreCatalogue](#data_framework.modules.validation.integrations.file_validator.CoreCatalogue)
  * [CoreQualityControls](#data_framework.modules.validation.integrations.file_validator.CoreQualityControls)
  * [CoreDataProcess](#data_framework.modules.validation.integrations.file_validator.CoreDataProcess)
  * [ControlRule](#data_framework.modules.validation.integrations.file_validator.ControlRule)
  * [re](#data_framework.modules.validation.integrations.file_validator.re)
  * [BytesIO](#data_framework.modules.validation.integrations.file_validator.BytesIO)
  * [Path](#data_framework.modules.validation.integrations.file_validator.Path)
  * [pd](#data_framework.modules.validation.integrations.file_validator.pd)
  * [FileValidator](#data_framework.modules.validation.integrations.file_validator.FileValidator)
    * [\_\_init\_\_](#data_framework.modules.validation.integrations.file_validator.FileValidator.__init__)
    * [validate\_filename\_pattern](#data_framework.modules.validation.integrations.file_validator.FileValidator.validate_filename_pattern)
    * [validate\_unzipped\_filename\_pattern](#data_framework.modules.validation.integrations.file_validator.FileValidator.validate_unzipped_filename_pattern)
    * [validate\_csv\_format](#data_framework.modules.validation.integrations.file_validator.FileValidator.validate_csv_format)
    * [validate\_csv\_columns](#data_framework.modules.validation.integrations.file_validator.FileValidator.validate_csv_columns)
    * [\_get\_expected\_number\_of\_columns](#data_framework.modules.validation.integrations.file_validator.FileValidator._get_expected_number_of_columns)
    * [\_parse\_columns](#data_framework.modules.validation.integrations.file_validator.FileValidator._parse_columns)
* [data\_framework.modules.validation.integrations.quality\_controls](#data_framework.modules.validation.integrations.quality_controls)
  * [InterfaceQualityControls](#data_framework.modules.validation.integrations.quality_controls.InterfaceQualityControls)
  * [ControlsResponse](#data_framework.modules.validation.integrations.quality_controls.ControlsResponse)
  * [ControlsTable](#data_framework.modules.validation.integrations.quality_controls.ControlsTable)
  * [ControlRule](#data_framework.modules.validation.integrations.quality_controls.ControlRule)
  * [AlgorithmType](#data_framework.modules.validation.integrations.quality_controls.AlgorithmType)
  * [ControlLevel](#data_framework.modules.validation.integrations.quality_controls.ControlLevel)
  * [config](#data_framework.modules.validation.integrations.quality_controls.config)
  * [logger](#data_framework.modules.validation.integrations.quality_controls.logger)
  * [CoreDataProcess](#data_framework.modules.validation.integrations.quality_controls.CoreDataProcess)
  * [Technologies](#data_framework.modules.validation.integrations.quality_controls.Technologies)
  * [DatabaseTable](#data_framework.modules.validation.integrations.quality_controls.DatabaseTable)
  * [Layer](#data_framework.modules.validation.integrations.quality_controls.Layer)
  * [debug\_code](#data_framework.modules.validation.integrations.quality_controls.debug_code)
  * [QualityControlsError](#data_framework.modules.validation.integrations.quality_controls.QualityControlsError)
  * [FailedRulesError](#data_framework.modules.validation.integrations.quality_controls.FailedRulesError)
  * [ValidationFunctionNotFoundError](#data_framework.modules.validation.integrations.quality_controls.ValidationFunctionNotFoundError)
  * [ParentNotConfiguredError](#data_framework.modules.validation.integrations.quality_controls.ParentNotConfiguredError)
  * [RuleComputeError](#data_framework.modules.validation.integrations.quality_controls.RuleComputeError)
  * [Any](#data_framework.modules.validation.integrations.quality_controls.Any)
  * [Dict](#data_framework.modules.validation.integrations.quality_controls.Dict)
  * [pd](#data_framework.modules.validation.integrations.quality_controls.pd)
  * [QualityControls](#data_framework.modules.validation.integrations.quality_controls.QualityControls)
    * [\_\_init\_\_](#data_framework.modules.validation.integrations.quality_controls.QualityControls.__init__)
    * [set\_parent](#data_framework.modules.validation.integrations.quality_controls.QualityControls.set_parent)
    * [validate](#data_framework.modules.validation.integrations.quality_controls.QualityControls.validate)
    * [\_get\_active\_rules](#data_framework.modules.validation.integrations.quality_controls.QualityControls._get_active_rules)
    * [\_get\_overall\_result](#data_framework.modules.validation.integrations.quality_controls.QualityControls._get_overall_result)
    * [\_insert\_results](#data_framework.modules.validation.integrations.quality_controls.QualityControls._insert_results)
    * [\_compute\_rules](#data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_rules)
    * [\_compute\_generic\_rule](#data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_generic_rule)
    * [\_compute\_sql\_rule](#data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_sql_rule)
    * [\_compute\_python\_rule](#data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_python_rule)
    * [\_compute\_regex\_rule](#data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_regex_rule)
* [data\_framework.modules.validation.interface\_quality\_controls](#data_framework.modules.validation.interface_quality_controls)
  * [DatabaseTable](#data_framework.modules.validation.interface_quality_controls.DatabaseTable)
  * [Database](#data_framework.modules.validation.interface_quality_controls.Database)
  * [Technologies](#data_framework.modules.validation.interface_quality_controls.Technologies)
  * [Platform](#data_framework.modules.validation.interface_quality_controls.Platform)
  * [Layer](#data_framework.modules.validation.interface_quality_controls.Layer)
  * [config](#data_framework.modules.validation.interface_quality_controls.config)
  * [InvalidThresholdError](#data_framework.modules.validation.interface_quality_controls.InvalidThresholdError)
  * [InvalidDataFrameError](#data_framework.modules.validation.interface_quality_controls.InvalidDataFrameError)
  * [InvalidAlgorithmError](#data_framework.modules.validation.interface_quality_controls.InvalidAlgorithmError)
  * [InvalidRuleError](#data_framework.modules.validation.interface_quality_controls.InvalidRuleError)
  * [ABC](#data_framework.modules.validation.interface_quality_controls.ABC)
  * [abstractmethod](#data_framework.modules.validation.interface_quality_controls.abstractmethod)
  * [dataclass](#data_framework.modules.validation.interface_quality_controls.dataclass)
  * [field](#data_framework.modules.validation.interface_quality_controls.field)
  * [Enum](#data_framework.modules.validation.interface_quality_controls.Enum)
  * [Any](#data_framework.modules.validation.interface_quality_controls.Any)
  * [Union](#data_framework.modules.validation.interface_quality_controls.Union)
  * [List](#data_framework.modules.validation.interface_quality_controls.List)
  * [Dict](#data_framework.modules.validation.interface_quality_controls.Dict)
  * [datetime](#data_framework.modules.validation.interface_quality_controls.datetime)
  * [date](#data_framework.modules.validation.interface_quality_controls.date)
  * [pd](#data_framework.modules.validation.interface_quality_controls.pd)
  * [ControlsResponse](#data_framework.modules.validation.interface_quality_controls.ControlsResponse)
    * [success](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.success)
    * [table](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.table)
    * [overall\_result](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.overall_result)
    * [error](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.error)
    * [data](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.data)
    * [rules](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.rules)
    * [results](#data_framework.modules.validation.interface_quality_controls.ControlsResponse.results)
  * [ControlsTable](#data_framework.modules.validation.interface_quality_controls.ControlsTable)
    * [table\_config](#data_framework.modules.validation.interface_quality_controls.ControlsTable.table_config)
    * [mandatory\_columns](#data_framework.modules.validation.interface_quality_controls.ControlsTable.mandatory_columns)
    * [master](#data_framework.modules.validation.interface_quality_controls.ControlsTable.master)
    * [dataset](#data_framework.modules.validation.interface_quality_controls.ControlsTable.dataset)
    * [results](#data_framework.modules.validation.interface_quality_controls.ControlsTable.results)
    * [filter\_rules](#data_framework.modules.validation.interface_quality_controls.ControlsTable.filter_rules)
  * [ControlOutcome](#data_framework.modules.validation.interface_quality_controls.ControlOutcome)
    * [total](#data_framework.modules.validation.interface_quality_controls.ControlOutcome.total)
    * [value](#data_framework.modules.validation.interface_quality_controls.ControlOutcome.value)
    * [metric\_value](#data_framework.modules.validation.interface_quality_controls.ControlOutcome.metric_value)
    * [to\_string](#data_framework.modules.validation.interface_quality_controls.ControlOutcome.to_string)
  * [ThresholdResult](#data_framework.modules.validation.interface_quality_controls.ThresholdResult)
    * [total\_records](#data_framework.modules.validation.interface_quality_controls.ThresholdResult.total_records)
    * [invalid\_records](#data_framework.modules.validation.interface_quality_controls.ThresholdResult.invalid_records)
    * [valid\_identifiers](#data_framework.modules.validation.interface_quality_controls.ThresholdResult.valid_identifiers)
    * [invalid\_identifiers](#data_framework.modules.validation.interface_quality_controls.ThresholdResult.invalid_identifiers)
    * [unique\_valid\_identifiers](#data_framework.modules.validation.interface_quality_controls.ThresholdResult.unique_valid_identifiers)
    * [unique\_invalid\_identifiers](#data_framework.modules.validation.interface_quality_controls.ThresholdResult.unique_invalid_identifiers)
  * [ControlResult](#data_framework.modules.validation.interface_quality_controls.ControlResult)
    * [master\_id](#data_framework.modules.validation.interface_quality_controls.ControlResult.master_id)
    * [table\_id](#data_framework.modules.validation.interface_quality_controls.ControlResult.table_id)
    * [outcome](#data_framework.modules.validation.interface_quality_controls.ControlResult.outcome)
    * [detail](#data_framework.modules.validation.interface_quality_controls.ControlResult.detail)
    * [initial\_date](#data_framework.modules.validation.interface_quality_controls.ControlResult.initial_date)
    * [end\_date](#data_framework.modules.validation.interface_quality_controls.ControlResult.end_date)
    * [data\_date](#data_framework.modules.validation.interface_quality_controls.ControlResult.data_date)
    * [to\_series](#data_framework.modules.validation.interface_quality_controls.ControlResult.to_series)
    * [set\_data\_date](#data_framework.modules.validation.interface_quality_controls.ControlResult.set_data_date)
    * [add\_detail](#data_framework.modules.validation.interface_quality_controls.ControlResult.add_detail)
    * [fill](#data_framework.modules.validation.interface_quality_controls.ControlResult.fill)
  * [ThresholdType](#data_framework.modules.validation.interface_quality_controls.ThresholdType)
    * [STANDARD](#data_framework.modules.validation.interface_quality_controls.ThresholdType.STANDARD)
    * [ABSOLUTE](#data_framework.modules.validation.interface_quality_controls.ThresholdType.ABSOLUTE)
    * [BINARY](#data_framework.modules.validation.interface_quality_controls.ThresholdType.BINARY)
    * [available\_threshold\_types](#data_framework.modules.validation.interface_quality_controls.ThresholdType.available_threshold_types)
  * [ControlThreshold](#data_framework.modules.validation.interface_quality_controls.ControlThreshold)
    * [threshold\_type](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_type)
    * [threshold\_max](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_max)
    * [threshold\_min](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_min)
    * [threshold\_rag\_min](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_rag_min)
    * [threshold\_rag\_max](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_rag_max)
    * [validate](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.validate)
    * [apply\_threshold](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.apply_threshold)
    * [calculate\_standard\_threshold](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.calculate_standard_threshold)
    * [calculate\_absolute\_threshold](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.calculate_absolute_threshold)
    * [calculate\_binary\_threshold](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.calculate_binary_threshold)
    * [apply\_threshold\_limits](#data_framework.modules.validation.interface_quality_controls.ControlThreshold.apply_threshold_limits)
  * [AlgorithmType](#data_framework.modules.validation.interface_quality_controls.AlgorithmType)
    * [PYTHON](#data_framework.modules.validation.interface_quality_controls.AlgorithmType.PYTHON)
    * [SQL](#data_framework.modules.validation.interface_quality_controls.AlgorithmType.SQL)
    * [REGEX](#data_framework.modules.validation.interface_quality_controls.AlgorithmType.REGEX)
    * [available\_algorithm\_types](#data_framework.modules.validation.interface_quality_controls.AlgorithmType.available_algorithm_types)
  * [ControlLevel](#data_framework.modules.validation.interface_quality_controls.ControlLevel)
    * [DATA](#data_framework.modules.validation.interface_quality_controls.ControlLevel.DATA)
    * [FILE](#data_framework.modules.validation.interface_quality_controls.ControlLevel.FILE)
    * [available\_control\_levels](#data_framework.modules.validation.interface_quality_controls.ControlLevel.available_control_levels)
  * [ControlAlgorithm](#data_framework.modules.validation.interface_quality_controls.ControlAlgorithm)
    * [algorithm\_type](#data_framework.modules.validation.interface_quality_controls.ControlAlgorithm.algorithm_type)
    * [algorithm\_description](#data_framework.modules.validation.interface_quality_controls.ControlAlgorithm.algorithm_description)
    * [validate](#data_framework.modules.validation.interface_quality_controls.ControlAlgorithm.validate)
  * [ControlRule](#data_framework.modules.validation.interface_quality_controls.ControlRule)
    * [master\_id](#data_framework.modules.validation.interface_quality_controls.ControlRule.master_id)
    * [table\_id](#data_framework.modules.validation.interface_quality_controls.ControlRule.table_id)
    * [layer](#data_framework.modules.validation.interface_quality_controls.ControlRule.layer)
    * [database\_table](#data_framework.modules.validation.interface_quality_controls.ControlRule.database_table)
    * [field](#data_framework.modules.validation.interface_quality_controls.ControlRule.field)
    * [rule\_description](#data_framework.modules.validation.interface_quality_controls.ControlRule.rule_description)
    * [level](#data_framework.modules.validation.interface_quality_controls.ControlRule.level)
    * [algorithm](#data_framework.modules.validation.interface_quality_controls.ControlRule.algorithm)
    * [threshold](#data_framework.modules.validation.interface_quality_controls.ControlRule.threshold)
    * [is\_blocker](#data_framework.modules.validation.interface_quality_controls.ControlRule.is_blocker)
    * [result](#data_framework.modules.validation.interface_quality_controls.ControlRule.result)
    * [id](#data_framework.modules.validation.interface_quality_controls.ControlRule.id)
    * [field\_list](#data_framework.modules.validation.interface_quality_controls.ControlRule.field_list)
    * [from\_series](#data_framework.modules.validation.interface_quality_controls.ControlRule.from_series)
    * [validate](#data_framework.modules.validation.interface_quality_controls.ControlRule.validate)
    * [calculate\_result](#data_framework.modules.validation.interface_quality_controls.ControlRule.calculate_result)
  * [InterfaceQualityControls](#data_framework.modules.validation.interface_quality_controls.InterfaceQualityControls)
    * [validate](#data_framework.modules.validation.interface_quality_controls.InterfaceQualityControls.validate)
    * [set\_parent](#data_framework.modules.validation.interface_quality_controls.InterfaceQualityControls.set_parent)
* [data\_framework.tests.test\_utils](#data_framework.tests.test_utils)
  * [pytest](#data_framework.tests.test_utils.pytest)
  * [Logger](#data_framework.tests.test_utils.Logger)
  * [test\_logger](#data_framework.tests.test_utils.test_logger)

<a id="data_framework.dataflow.business"></a>

# data\_framework.dataflow.business

<a id="data_framework.dataflow.business.DataFlowInterface"></a>

## DataFlowInterface

<a id="data_framework.dataflow.business.StagingToBusiness"></a>

## StagingToBusiness Objects

```python
class StagingToBusiness(DataFlowInterface)
```

<a id="data_framework.dataflow.business.StagingToBusiness.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.dataflow.business.CommonToBusiness"></a>

## CommonToBusiness Objects

```python
class CommonToBusiness(DataFlowInterface)
```

<a id="data_framework.dataflow.business.CommonToBusiness.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.dataflow.common"></a>

# data\_framework.dataflow.common

<a id="data_framework.dataflow.common.DataFlowInterface"></a>

## DataFlowInterface

<a id="data_framework.dataflow.common.StagingToCommon"></a>

## StagingToCommon Objects

```python
class StagingToCommon(DataFlowInterface)
```

<a id="data_framework.dataflow.common.StagingToCommon.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.dataflow.landing"></a>

# data\_framework.dataflow.landing

<a id="data_framework.dataflow.landing.DataFlowInterface"></a>

## DataFlowInterface

<a id="data_framework.dataflow.landing.ExecutionMode"></a>

## ExecutionMode

<a id="data_framework.dataflow.landing.DateLocated"></a>

## DateLocated

<a id="data_framework.dataflow.landing.LandingFileFormat"></a>

## LandingFileFormat

<a id="data_framework.dataflow.landing.Storage"></a>

## Storage

<a id="data_framework.dataflow.landing.CoreCatalogue"></a>

## CoreCatalogue

<a id="data_framework.dataflow.landing.Layer"></a>

## Layer

<a id="data_framework.dataflow.landing.FileValidator"></a>

## FileValidator

<a id="data_framework.dataflow.landing.FileProcessError"></a>

## FileProcessError

<a id="data_framework.dataflow.landing.FileReadError"></a>

## FileReadError

<a id="data_framework.dataflow.landing.InvalidDateRegexError"></a>

## InvalidDateRegexError

<a id="data_framework.dataflow.landing.InvalidRegexGroupError"></a>

## InvalidRegexGroupError

<a id="data_framework.dataflow.landing.InvalidFileError"></a>

## InvalidFileError

<a id="data_framework.dataflow.landing.regex_utils"></a>

## regex\_utils

<a id="data_framework.dataflow.landing.re"></a>

## re

<a id="data_framework.dataflow.landing.hashlib"></a>

## hashlib

<a id="data_framework.dataflow.landing.json"></a>

## json

<a id="data_framework.dataflow.landing.Tuple"></a>

## Tuple

<a id="data_framework.dataflow.landing.datetime"></a>

## datetime

<a id="data_framework.dataflow.landing.BytesIO"></a>

## BytesIO

<a id="data_framework.dataflow.landing.Path"></a>

## Path

<a id="data_framework.dataflow.landing.ZipFile"></a>

## ZipFile

<a id="data_framework.dataflow.landing.tarfile"></a>

## tarfile

<a id="data_framework.dataflow.landing.read_xml"></a>

## read\_xml

<a id="data_framework.dataflow.landing.read_excel"></a>

## read\_excel

<a id="data_framework.dataflow.landing.ProcessingCoordinator"></a>

## ProcessingCoordinator Objects

```python
class ProcessingCoordinator(DataFlowInterface)
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.process"></a>

#### process

```python
def process()
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.process_file"></a>

#### process\_file

```python
def process_file()
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.read_data"></a>

#### read\_data

```python
def read_data() -> dict
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.obtain_file_date"></a>

#### obtain\_file\_date

```python
def obtain_file_date() -> str
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.compare_with_previous_file"></a>

#### compare\_with\_previous\_file

```python
def compare_with_previous_file(file_contents: dict) -> bool
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.get_last_processed_file_key"></a>

#### get\_last\_processed\_file\_key

```python
def get_last_processed_file_key(incoming_filename: str, file_keys: str) -> str
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.get_file_hash"></a>

#### get\_file\_hash

```python
def get_file_hash(file_content: BytesIO, chunk_size: int = 8000)
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.create_partitions"></a>

#### create\_partitions

```python
def create_partitions(file_date: str) -> dict
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.write_data"></a>

#### write\_data

```python
def write_data(file_contents: dict, partitions: dict, file_date: str) -> None
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.normalize_file_content"></a>

#### normalize\_file\_content

```python
def normalize_file_content(filename: str, file_content: BytesIO,
                           file_date: str) -> Tuple[str, BytesIO]
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.convert_xml_to_parquet"></a>

#### convert\_xml\_to\_parquet

```python
def convert_xml_to_parquet(filename: str,
                           file_content: BytesIO) -> Tuple[str, BytesIO]
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.convert_excel_to_parquet"></a>

#### convert\_excel\_to\_parquet

```python
def convert_excel_to_parquet(filename: str,
                             file_content: BytesIO) -> Tuple[str, BytesIO]
```

<a id="data_framework.dataflow.landing.ProcessingCoordinator.convert_json_to_json_lines"></a>

#### convert\_json\_to\_json\_lines

```python
def convert_json_to_json_lines(file_content: BytesIO,
                               file_date: str) -> BytesIO
```

<a id="data_framework.dataflow.output"></a>

# data\_framework.dataflow.output

<a id="data_framework.dataflow.output.DataFlowInterface"></a>

## DataFlowInterface

<a id="data_framework.dataflow.output.OutputResponse"></a>

## OutputResponse

<a id="data_framework.dataflow.output.Storage"></a>

## Storage

<a id="data_framework.dataflow.output.Layer"></a>

## Layer

<a id="data_framework.dataflow.output.OutputReport"></a>

## OutputReport

<a id="data_framework.dataflow.output.OutputError"></a>

## OutputError

<a id="data_framework.dataflow.output.OutputGenerationError"></a>

## OutputGenerationError

<a id="data_framework.dataflow.output.NoOutputDataError"></a>

## NoOutputDataError

<a id="data_framework.dataflow.output.DataFrame"></a>

## DataFrame

<a id="data_framework.dataflow.output.datetime"></a>

## datetime

<a id="data_framework.dataflow.output.ZoneInfo"></a>

## ZoneInfo

<a id="data_framework.dataflow.output.BytesIO"></a>

## BytesIO

<a id="data_framework.dataflow.output.re"></a>

## re

<a id="data_framework.dataflow.output.TIME_ZONE"></a>

#### TIME\_ZONE

<a id="data_framework.dataflow.output.ProcessingCoordinator"></a>

## ProcessingCoordinator Objects

```python
class ProcessingCoordinator(DataFlowInterface)
```

<a id="data_framework.dataflow.output.ProcessingCoordinator.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.dataflow.output.ProcessingCoordinator.process"></a>

#### process

```python
def process() -> dict
```

<a id="data_framework.dataflow.output.ProcessingCoordinator.generate_output_file"></a>

#### generate\_output\_file

```python
def generate_output_file(config_output: OutputReport) -> None
```

<a id="data_framework.dataflow.output.ProcessingCoordinator.retrieve_data"></a>

#### retrieve\_data

```python
def retrieve_data(config_output: OutputReport) -> DataFrame
```

Function to build sql a retrieve the dataframe with the data

<a id="data_framework.dataflow.output.ProcessingCoordinator.write_data_to_file"></a>

#### write\_data\_to\_file

```python
def write_data_to_file(df: DataFrame, config_output: OutputReport) -> None
```

Function to write the dataframe with the data in storage

<a id="data_framework.dataflow.output.ProcessingCoordinator.parse_output_folder"></a>

#### parse\_output\_folder

```python
@staticmethod
def parse_output_folder(output_folder: str) -> str
```

<a id="data_framework.dataflow.output.ProcessingCoordinator.format_string"></a>

#### format\_string

```python
def format_string(string_to_format: str, date_format: str = '%Y-%m-%d') -> str
```

<a id="data_framework.dataflow.staging"></a>

# data\_framework.dataflow.staging

<a id="data_framework.dataflow.staging.DataFlowInterface"></a>

## DataFlowInterface

<a id="data_framework.dataflow.staging.RawToStaging"></a>

## RawToStaging Objects

```python
class RawToStaging(DataFlowInterface)
```

<a id="data_framework.dataflow.staging.RawToStaging.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.catalogue.core_catalogue"></a>

# data\_framework.modules.catalogue.core\_catalogue

<a id="data_framework.modules.catalogue.core_catalogue.LazyClassProperty"></a>

## LazyClassProperty

<a id="data_framework.modules.catalogue.core_catalogue.CatalogueInterface"></a>

## CatalogueInterface

<a id="data_framework.modules.catalogue.core_catalogue.SchemaResponse"></a>

## SchemaResponse

<a id="data_framework.modules.catalogue.core_catalogue.GenericResponse"></a>

## GenericResponse

<a id="data_framework.modules.catalogue.core_catalogue.Union"></a>

## Union

<a id="data_framework.modules.catalogue.core_catalogue.CoreCatalogue"></a>

## CoreCatalogue Objects

```python
class CoreCatalogue()
```

<a id="data_framework.modules.catalogue.core_catalogue.CoreCatalogue._catalogue"></a>

#### \_catalogue

```python
@LazyClassProperty
def _catalogue(cls) -> CatalogueInterface
```

<a id="data_framework.modules.catalogue.core_catalogue.CoreCatalogue.create_partition"></a>

#### create\_partition

```python
@staticmethod
def create_partition(database: str, table: str, partition_field: str,
                     value: Union[str, int]) -> GenericResponse
```

<a id="data_framework.modules.catalogue.core_catalogue.CoreCatalogue.get_schema"></a>

#### get\_schema

```python
@staticmethod
def get_schema(database: str, table: str) -> SchemaResponse
```

<a id="data_framework.modules.catalogue.integrations.aws_glue"></a>

# data\_framework.modules.catalogue.integrations.aws\_glue

<a id="data_framework.modules.catalogue.integrations.aws_glue.CatalogueInterface"></a>

## CatalogueInterface

<a id="data_framework.modules.catalogue.integrations.aws_glue.SchemaResponse"></a>

## SchemaResponse

<a id="data_framework.modules.catalogue.integrations.aws_glue.GenericResponse"></a>

## GenericResponse

<a id="data_framework.modules.catalogue.integrations.aws_glue.Column"></a>

## Column

<a id="data_framework.modules.catalogue.integrations.aws_glue.Schema"></a>

## Schema

<a id="data_framework.modules.catalogue.integrations.aws_glue.GlueError"></a>

## GlueError

<a id="data_framework.modules.catalogue.integrations.aws_glue.CreatePartitionError"></a>

## CreatePartitionError

<a id="data_framework.modules.catalogue.integrations.aws_glue.InvalidPartitionFieldError"></a>

## InvalidPartitionFieldError

<a id="data_framework.modules.catalogue.integrations.aws_glue.SchemaError"></a>

## SchemaError

<a id="data_framework.modules.catalogue.integrations.aws_glue.logger"></a>

## logger

<a id="data_framework.modules.catalogue.integrations.aws_glue.config"></a>

## config

<a id="data_framework.modules.catalogue.integrations.aws_glue.Union"></a>

## Union

<a id="data_framework.modules.catalogue.integrations.aws_glue.Any"></a>

## Any

<a id="data_framework.modules.catalogue.integrations.aws_glue.boto3"></a>

## boto3

<a id="data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue"></a>

## CatalogueAWSGlue Objects

```python
class CatalogueAWSGlue(CatalogueInterface)
```

<a id="data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue.create_partition"></a>

#### create\_partition

```python
def create_partition(database: str, table: str, partition_field: str,
                     partition_value: Union[str, int]) -> GenericResponse
```

Function to create new partition in a table

**Arguments**:

- `database`: name of database of the table
- `table`: name of the table
- `partition_field`: name of the partition
- `partition_value`: value od the partition

**Returns**:

GenericResponse

<a id="data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue.get_schema"></a>

#### get\_schema

```python
def get_schema(database: str, table: str) -> SchemaResponse
```

Function to get schema(list of columns(name and type) of a table

**Arguments**:

- `database`: name of database of the table
- `table`: name of the table

**Returns**:

SchemaResponse

<a id="data_framework.modules.catalogue.integrations.aws_glue.CatalogueAWSGlue._get_glue_table"></a>

#### \_get\_glue\_table

```python
def _get_glue_table(database: str, table: str) -> Any
```

<a id="data_framework.modules.catalogue.interface_catalogue"></a>

# data\_framework.modules.catalogue.interface\_catalogue

<a id="data_framework.modules.catalogue.interface_catalogue.ABC"></a>

## ABC

<a id="data_framework.modules.catalogue.interface_catalogue.abstractmethod"></a>

## abstractmethod

<a id="data_framework.modules.catalogue.interface_catalogue.Union"></a>

## Union

<a id="data_framework.modules.catalogue.interface_catalogue.Optional"></a>

## Optional

<a id="data_framework.modules.catalogue.interface_catalogue.List"></a>

## List

<a id="data_framework.modules.catalogue.interface_catalogue.Dict"></a>

## Dict

<a id="data_framework.modules.catalogue.interface_catalogue.dataclass"></a>

## dataclass

<a id="data_framework.modules.catalogue.interface_catalogue.Column"></a>

## Column Objects

```python
@dataclass
class Column()
```

<a id="data_framework.modules.catalogue.interface_catalogue.Column.name"></a>

#### name

<a id="data_framework.modules.catalogue.interface_catalogue.Column.type"></a>

#### type

<a id="data_framework.modules.catalogue.interface_catalogue.Column.order"></a>

#### order

<a id="data_framework.modules.catalogue.interface_catalogue.Column.ispartitioned"></a>

#### ispartitioned

<a id="data_framework.modules.catalogue.interface_catalogue.Schema"></a>

## Schema Objects

```python
@dataclass
class Schema()
```

<a id="data_framework.modules.catalogue.interface_catalogue.Schema.columns"></a>

#### columns

<a id="data_framework.modules.catalogue.interface_catalogue.Schema.get_column_names"></a>

#### get\_column\_names

```python
def get_column_names(partitioned: bool = False) -> List[str]
```

<a id="data_framework.modules.catalogue.interface_catalogue.Schema.get_type_columns"></a>

#### get\_type\_columns

```python
def get_type_columns(partitioned: bool = False) -> List[str]
```

<a id="data_framework.modules.catalogue.interface_catalogue.Schema.get_column_type_mapping"></a>

#### get\_column\_type\_mapping

```python
def get_column_type_mapping(partitioned: bool = False) -> Dict[str, str]
```

<a id="data_framework.modules.catalogue.interface_catalogue.SchemaResponse"></a>

## SchemaResponse Objects

```python
@dataclass
class SchemaResponse()
```

<a id="data_framework.modules.catalogue.interface_catalogue.SchemaResponse.success"></a>

#### success

<a id="data_framework.modules.catalogue.interface_catalogue.SchemaResponse.error"></a>

#### error

<a id="data_framework.modules.catalogue.interface_catalogue.SchemaResponse.schema"></a>

#### schema

<a id="data_framework.modules.catalogue.interface_catalogue.GenericResponse"></a>

## GenericResponse Objects

```python
@dataclass
class GenericResponse()
```

<a id="data_framework.modules.catalogue.interface_catalogue.GenericResponse.success"></a>

#### success

<a id="data_framework.modules.catalogue.interface_catalogue.GenericResponse.error"></a>

#### error

<a id="data_framework.modules.catalogue.interface_catalogue.CatalogueInterface"></a>

## CatalogueInterface Objects

```python
class CatalogueInterface(ABC)
```

<a id="data_framework.modules.catalogue.interface_catalogue.CatalogueInterface.create_partition"></a>

#### create\_partition

```python
@abstractmethod
def create_partition(database: str, table: str, artition_field: str,
                     partition_value: Union[str, int]) -> GenericResponse
```

<a id="data_framework.modules.catalogue.interface_catalogue.CatalogueInterface.get_schema"></a>

#### get\_schema

```python
@abstractmethod
def get_schema(database: str, table: str) -> SchemaResponse
```

<a id="data_framework.modules.code.lazy_class_property"></a>

# data\_framework.modules.code.lazy\_class\_property

<a id="data_framework.modules.code.lazy_class_property.LazyClassProperty"></a>

## LazyClassProperty Objects

```python
class LazyClassProperty()
```

<a id="data_framework.modules.code.lazy_class_property.LazyClassProperty.__init__"></a>

#### \_\_init\_\_

```python
def __init__(func)
```

<a id="data_framework.modules.code.lazy_class_property.LazyClassProperty.__get__"></a>

#### \_\_get\_\_

```python
def __get__(instance, owner)
```

<a id="data_framework.modules.config.core"></a>

# data\_framework.modules.config.core

<a id="data_framework.modules.config.core.Type"></a>

## Type

<a id="data_framework.modules.config.core.TypeVar"></a>

## TypeVar

<a id="data_framework.modules.config.core.Union"></a>

## Union

<a id="data_framework.modules.config.core.get_type_hints"></a>

## get\_type\_hints

<a id="data_framework.modules.config.core.get_origin"></a>

## get\_origin

<a id="data_framework.modules.config.core.get_args"></a>

## get\_args

<a id="data_framework.modules.config.core.Processes"></a>

## Processes

<a id="data_framework.modules.config.core.LandingToRaw"></a>

## LandingToRaw

<a id="data_framework.modules.config.core.ToOutput"></a>

## ToOutput

<a id="data_framework.modules.config.core.IncomingFileLandingToRaw"></a>

## IncomingFileLandingToRaw

<a id="data_framework.modules.config.core.DateLocatedFilename"></a>

## DateLocatedFilename

<a id="data_framework.modules.config.core.Parameters"></a>

## Parameters

<a id="data_framework.modules.config.core.CSVSpecs"></a>

## CSVSpecs

<a id="data_framework.modules.config.core.XMLSpecs"></a>

## XMLSpecs

<a id="data_framework.modules.config.core.JSONSpecs"></a>

## JSONSpecs

<a id="data_framework.modules.config.core.Config"></a>

## Config

<a id="data_framework.modules.config.core.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.config.core.ProcessingSpecifications"></a>

## ProcessingSpecifications

<a id="data_framework.modules.config.core.Hardware"></a>

## Hardware

<a id="data_framework.modules.config.core.SparkConfiguration"></a>

## SparkConfiguration

<a id="data_framework.modules.config.core.OutputReport"></a>

## OutputReport

<a id="data_framework.modules.config.core.GenericProcess"></a>

## GenericProcess

<a id="data_framework.modules.config.core.TableDict"></a>

## TableDict

<a id="data_framework.modules.config.core.CSVSpecsReport"></a>

## CSVSpecsReport

<a id="data_framework.modules.config.core.JSONSpecsReport"></a>

## JSONSpecsReport

<a id="data_framework.modules.config.core.VolumetricExpectation"></a>

## VolumetricExpectation

<a id="data_framework.modules.config.core.Platform"></a>

## Platform

<a id="data_framework.modules.config.core.Technologies"></a>

## Technologies

<a id="data_framework.modules.config.core.Environment"></a>

## Environment

<a id="data_framework.modules.config.core.ProcessVars"></a>

## ProcessVars

<a id="data_framework.modules.config.core.Casting"></a>

## Casting

<a id="data_framework.modules.config.core.Transformation"></a>

## Transformation

<a id="data_framework.modules.config.core.NotificationDict"></a>

## NotificationDict

<a id="data_framework.modules.config.core.DataFrameworkNotifications"></a>

## DataFrameworkNotifications

<a id="data_framework.modules.config.core.Notification"></a>

## Notification

<a id="data_framework.modules.config.core.NotificationsParameters"></a>

## NotificationsParameters

<a id="data_framework.modules.config.core.ConfigError"></a>

## ConfigError

<a id="data_framework.modules.config.core.ConfigFileNotFoundError"></a>

## ConfigFileNotFoundError

<a id="data_framework.modules.config.core.ConfigParseError"></a>

## ConfigParseError

<a id="data_framework.modules.config.core.AccountNotFoundError"></a>

## AccountNotFoundError

<a id="data_framework.modules.config.core.ParameterParseError"></a>

## ParameterParseError

<a id="data_framework.modules.config.core.STSError"></a>

## STSError

<a id="data_framework.modules.config.core.threading"></a>

## threading

<a id="data_framework.modules.config.core.os"></a>

## os

<a id="data_framework.modules.config.core.sys"></a>

## sys

<a id="data_framework.modules.config.core.Enum"></a>

## Enum

<a id="data_framework.modules.config.core.json"></a>

## json

<a id="data_framework.modules.config.core.Path"></a>

## Path

<a id="data_framework.modules.config.core.zipfile"></a>

## zipfile

<a id="data_framework.modules.config.core.boto3"></a>

## boto3

<a id="data_framework.modules.config.core.T"></a>

#### T

<a id="data_framework.modules.config.core.config"></a>

#### config

```python
def config(parameters: dict = None, reset: bool = False) -> Config
```

<a id="data_framework.modules.config.core.ConfigSetup"></a>

## ConfigSetup Objects

```python
class ConfigSetup()
```

<a id="data_framework.modules.config.core.ConfigSetup._instancia"></a>

#### \_instancia

<a id="data_framework.modules.config.core.ConfigSetup._lock"></a>

#### \_lock

<a id="data_framework.modules.config.core.ConfigSetup._environment"></a>

#### \_environment

<a id="data_framework.modules.config.core.ConfigSetup._models"></a>

#### \_models

<a id="data_framework.modules.config.core.ConfigSetup.__new__"></a>

#### \_\_new\_\_

```python
def __new__(cls, *args, **kwargs)
```

<a id="data_framework.modules.config.core.ConfigSetup.__init__"></a>

#### \_\_init\_\_

```python
def __init__(parameters: dict = None)
```

<a id="data_framework.modules.config.core.ConfigSetup.read_data_framework_config"></a>

#### read\_data\_framework\_config

```python
@classmethod
def read_data_framework_config(cls) -> dict
```

<a id="data_framework.modules.config.core.ConfigSetup.read_notifications_config"></a>

#### read\_notifications\_config

```python
@classmethod
def read_notifications_config(cls) -> dict
```

<a id="data_framework.modules.config.core.ConfigSetup.read_config_file"></a>

#### read\_config\_file

```python
@classmethod
def read_config_file(cls, absolute_path: str, relative_path: str) -> dict
```

<a id="data_framework.modules.config.core.ConfigSetup.read_dataflow_config"></a>

#### read\_dataflow\_config

```python
@classmethod
def read_dataflow_config(cls, dataflow: str, local_file: str, environment: str,
                         platform: str) -> dict
```

<a id="data_framework.modules.config.core.ConfigSetup.merged_current_dataflow_with_default"></a>

#### merged\_current\_dataflow\_with\_default

```python
@classmethod
def merged_current_dataflow_with_default(cls, current_dataflow: dict,
                                         default: dict) -> dict
```

<a id="data_framework.modules.config.core.ConfigSetup.parse_to_model"></a>

#### parse\_to\_model

```python
@classmethod
def parse_to_model(cls,
                   model: Type[T],
                   json_file: dict,
                   environment: str,
                   parameters: dict = None) -> T
```

<a id="data_framework.modules.config.model.flows"></a>

# data\_framework.modules.config.model.flows

<a id="data_framework.modules.config.model.flows.Database"></a>

## Database

<a id="data_framework.modules.config.model.flows.NotificationDict"></a>

## NotificationDict

<a id="data_framework.modules.config.model.flows.DataFrameworkNotifications"></a>

## DataFrameworkNotifications

<a id="data_framework.modules.config.model.flows.EmptyProcessConfigError"></a>

## EmptyProcessConfigError

<a id="data_framework.modules.config.model.flows.ProcessNotFoundError"></a>

## ProcessNotFoundError

<a id="data_framework.modules.config.model.flows.TableKeyError"></a>

## TableKeyError

<a id="data_framework.modules.config.model.flows.TableConfigNotFoundError"></a>

## TableConfigNotFoundError

<a id="data_framework.modules.config.model.flows.TransformationNotImplementedError"></a>

## TransformationNotImplementedError

<a id="data_framework.modules.config.model.flows.dataclass"></a>

## dataclass

<a id="data_framework.modules.config.model.flows.field"></a>

## field

<a id="data_framework.modules.config.model.flows.fields"></a>

## fields

<a id="data_framework.modules.config.model.flows.Enum"></a>

## Enum

<a id="data_framework.modules.config.model.flows.Optional"></a>

## Optional

<a id="data_framework.modules.config.model.flows.List"></a>

## List

<a id="data_framework.modules.config.model.flows.Tuple"></a>

## Tuple

<a id="data_framework.modules.config.model.flows.Union"></a>

## Union

<a id="data_framework.modules.config.model.flows.os"></a>

## os

<a id="data_framework.modules.config.model.flows.Environment"></a>

## Environment Objects

```python
class Environment(Enum)
```

<a id="data_framework.modules.config.model.flows.Environment.LOCAL"></a>

#### LOCAL

<a id="data_framework.modules.config.model.flows.Environment.DEVELOP"></a>

#### DEVELOP

<a id="data_framework.modules.config.model.flows.Environment.PREPRODUCTION"></a>

#### PREPRODUCTION

<a id="data_framework.modules.config.model.flows.Environment.PRODUCTION"></a>

#### PRODUCTION

<a id="data_framework.modules.config.model.flows.Platform"></a>

## Platform Objects

```python
class Platform(Enum)
```

<a id="data_framework.modules.config.model.flows.Platform.DATA_PLATFORM"></a>

#### DATA\_PLATFORM

<a id="data_framework.modules.config.model.flows.Platform.INFINITY"></a>

#### INFINITY

<a id="data_framework.modules.config.model.flows.DateLocated"></a>

## DateLocated Objects

```python
class DateLocated(Enum)
```

<a id="data_framework.modules.config.model.flows.DateLocated.FILENAME"></a>

#### FILENAME

<a id="data_framework.modules.config.model.flows.DateLocated.COLUMN"></a>

#### COLUMN

<a id="data_framework.modules.config.model.flows.Technologies"></a>

## Technologies Objects

```python
class Technologies(Enum)
```

<a id="data_framework.modules.config.model.flows.Technologies.LAMBDA"></a>

#### LAMBDA

<a id="data_framework.modules.config.model.flows.Technologies.EMR"></a>

#### EMR

<a id="data_framework.modules.config.model.flows.LandingFileFormat"></a>

## LandingFileFormat Objects

```python
class LandingFileFormat(Enum)
```

<a id="data_framework.modules.config.model.flows.LandingFileFormat.CSV"></a>

#### CSV

<a id="data_framework.modules.config.model.flows.LandingFileFormat.JSON"></a>

#### JSON

<a id="data_framework.modules.config.model.flows.LandingFileFormat.JSON_LINES"></a>

#### JSON\_LINES

<a id="data_framework.modules.config.model.flows.LandingFileFormat.EXCEL"></a>

#### EXCEL

<a id="data_framework.modules.config.model.flows.LandingFileFormat.XML"></a>

#### XML

<a id="data_framework.modules.config.model.flows.OutputFileFormat"></a>

## OutputFileFormat Objects

```python
class OutputFileFormat(Enum)
```

<a id="data_framework.modules.config.model.flows.OutputFileFormat.CSV"></a>

#### CSV

<a id="data_framework.modules.config.model.flows.OutputFileFormat.JSON"></a>

#### JSON

<a id="data_framework.modules.config.model.flows.ExecutionMode"></a>

## ExecutionMode Objects

```python
class ExecutionMode(Enum)
```

<a id="data_framework.modules.config.model.flows.ExecutionMode.DELTA"></a>

#### DELTA

<a id="data_framework.modules.config.model.flows.ExecutionMode.FULL"></a>

#### FULL

<a id="data_framework.modules.config.model.flows.ExecutionMode.is_delta"></a>

#### is\_delta

```python
@property
def is_delta() -> bool
```

<a id="data_framework.modules.config.model.flows.JSONSpectFormat"></a>

## JSONSpectFormat Objects

```python
class JSONSpectFormat(Enum)
```

<a id="data_framework.modules.config.model.flows.JSONSpectFormat.LINES"></a>

#### LINES

<a id="data_framework.modules.config.model.flows.JSONSpectFormat.COLUMNS"></a>

#### COLUMNS

<a id="data_framework.modules.config.model.flows.JSONFormat"></a>

## JSONFormat Objects

```python
class JSONFormat(Enum)
```

<a id="data_framework.modules.config.model.flows.JSONFormat.DICTIONARY"></a>

#### DICTIONARY

<a id="data_framework.modules.config.model.flows.JSONFormat.ARRAY"></a>

#### ARRAY

<a id="data_framework.modules.config.model.flows.CastingStrategy"></a>

## CastingStrategy Objects

```python
class CastingStrategy(Enum)
```

<a id="data_framework.modules.config.model.flows.CastingStrategy.ONE_BY_ONE"></a>

#### ONE\_BY\_ONE

<a id="data_framework.modules.config.model.flows.CastingStrategy.DYNAMIC"></a>

#### DYNAMIC

<a id="data_framework.modules.config.model.flows.TransformationType"></a>

## TransformationType Objects

```python
class TransformationType(Enum)
```

<a id="data_framework.modules.config.model.flows.TransformationType.PARSE_DATES"></a>

#### PARSE\_DATES

<a id="data_framework.modules.config.model.flows.Hardware"></a>

## Hardware Objects

```python
@dataclass
class Hardware()
```

<a id="data_framework.modules.config.model.flows.Hardware.ram"></a>

#### ram

<a id="data_framework.modules.config.model.flows.Hardware.cores"></a>

#### cores

<a id="data_framework.modules.config.model.flows.Hardware.disk"></a>

#### disk

<a id="data_framework.modules.config.model.flows.VolumetricExpectation"></a>

## VolumetricExpectation Objects

```python
@dataclass
class VolumetricExpectation()
```

<a id="data_framework.modules.config.model.flows.VolumetricExpectation.data_size_gb"></a>

#### data\_size\_gb

<a id="data_framework.modules.config.model.flows.VolumetricExpectation.avg_file_size_mb"></a>

#### avg\_file\_size\_mb

<a id="data_framework.modules.config.model.flows.SparkConfiguration"></a>

## SparkConfiguration Objects

```python
@dataclass
class SparkConfiguration()
```

<a id="data_framework.modules.config.model.flows.SparkConfiguration.full_volumetric_expectation"></a>

#### full\_volumetric\_expectation

<a id="data_framework.modules.config.model.flows.SparkConfiguration.delta_volumetric_expectation"></a>

#### delta\_volumetric\_expectation

<a id="data_framework.modules.config.model.flows.SparkConfiguration.delta_custom"></a>

#### delta\_custom

<a id="data_framework.modules.config.model.flows.SparkConfiguration.full_custom"></a>

#### full\_custom

<a id="data_framework.modules.config.model.flows.SparkConfiguration.volumetric_expectation"></a>

#### volumetric\_expectation

```python
@property
def volumetric_expectation() -> VolumetricExpectation
```

<a id="data_framework.modules.config.model.flows.SparkConfiguration.custom_config"></a>

#### custom\_config

```python
@property
def custom_config() -> dict
```

<a id="data_framework.modules.config.model.flows.SparkConfiguration.config"></a>

#### config

```python
@property
def config() -> dict
```

<a id="data_framework.modules.config.model.flows.ProcessingSpecifications"></a>

## ProcessingSpecifications Objects

```python
@dataclass
class ProcessingSpecifications()
```

<a id="data_framework.modules.config.model.flows.ProcessingSpecifications.technology"></a>

#### technology

<a id="data_framework.modules.config.model.flows.ProcessingSpecifications.spark_configuration"></a>

#### spark\_configuration

<a id="data_framework.modules.config.model.flows.DateLocatedFilename"></a>

## DateLocatedFilename Objects

```python
@dataclass
class DateLocatedFilename()
```

<a id="data_framework.modules.config.model.flows.DateLocatedFilename.regex"></a>

#### regex

<a id="data_framework.modules.config.model.flows.InterfaceSpecs"></a>

## InterfaceSpecs Objects

```python
class InterfaceSpecs()
```

<a id="data_framework.modules.config.model.flows.InterfaceSpecs.read_config"></a>

#### read\_config

```python
@property
def read_config() -> dict
```

<a id="data_framework.modules.config.model.flows.XMLSpecs"></a>

## XMLSpecs Objects

```python
@dataclass
class XMLSpecs(InterfaceSpecs)
```

<a id="data_framework.modules.config.model.flows.XMLSpecs.encoding"></a>

#### encoding

<a id="data_framework.modules.config.model.flows.XMLSpecs.xpath"></a>

#### xpath

<a id="data_framework.modules.config.model.flows.XMLSpecs.date_located"></a>

#### date\_located

<a id="data_framework.modules.config.model.flows.XMLSpecs.date_located_filename"></a>

#### date\_located\_filename

<a id="data_framework.modules.config.model.flows.XMLSpecs.read_config"></a>

#### read\_config

```python
@property
def read_config() -> dict
```

<a id="data_framework.modules.config.model.flows.JSONSpecs"></a>

## JSONSpecs Objects

```python
@dataclass
class JSONSpecs(InterfaceSpecs)
```

<a id="data_framework.modules.config.model.flows.JSONSpecs.encoding"></a>

#### encoding

<a id="data_framework.modules.config.model.flows.JSONSpecs.date_located"></a>

#### date\_located

<a id="data_framework.modules.config.model.flows.JSONSpecs.date_located_filename"></a>

#### date\_located\_filename

<a id="data_framework.modules.config.model.flows.JSONSpecs.source_level"></a>

#### source\_level

<a id="data_framework.modules.config.model.flows.JSONSpecs.source_level_format"></a>

#### source\_level\_format

<a id="data_framework.modules.config.model.flows.JSONSpecs.read_config"></a>

#### read\_config

```python
@property
def read_config() -> dict
```

<a id="data_framework.modules.config.model.flows.JSONSpecs.levels"></a>

#### levels

```python
@property
def levels() -> List[str]
```

<a id="data_framework.modules.config.model.flows.CSVSpecs"></a>

## CSVSpecs Objects

```python
@dataclass
class CSVSpecs(InterfaceSpecs)
```

<a id="data_framework.modules.config.model.flows.CSVSpecs.header_position"></a>

#### header\_position

<a id="data_framework.modules.config.model.flows.CSVSpecs.header"></a>

#### header

<a id="data_framework.modules.config.model.flows.CSVSpecs.encoding"></a>

#### encoding

<a id="data_framework.modules.config.model.flows.CSVSpecs.delimiter"></a>

#### delimiter

<a id="data_framework.modules.config.model.flows.CSVSpecs.date_located"></a>

#### date\_located

<a id="data_framework.modules.config.model.flows.CSVSpecs.date_located_filename"></a>

#### date\_located\_filename

<a id="data_framework.modules.config.model.flows.CSVSpecs.escape"></a>

#### escape

<a id="data_framework.modules.config.model.flows.CSVSpecs.comment"></a>

#### comment

<a id="data_framework.modules.config.model.flows.CSVSpecs.null_value"></a>

#### null\_value

<a id="data_framework.modules.config.model.flows.CSVSpecs.nan_value"></a>

#### nan\_value

<a id="data_framework.modules.config.model.flows.CSVSpecs.special_character"></a>

#### special\_character

<a id="data_framework.modules.config.model.flows.CSVSpecs.multiline"></a>

#### multiline

<a id="data_framework.modules.config.model.flows.CSVSpecs.read_config"></a>

#### read\_config

```python
@property
def read_config() -> dict
```

<a id="data_framework.modules.config.model.flows.CSVSpecsReport"></a>

## CSVSpecsReport Objects

```python
@dataclass
class CSVSpecsReport()
```

<a id="data_framework.modules.config.model.flows.CSVSpecsReport.header"></a>

#### header

<a id="data_framework.modules.config.model.flows.CSVSpecsReport.index"></a>

#### index

<a id="data_framework.modules.config.model.flows.CSVSpecsReport.encoding"></a>

#### encoding

<a id="data_framework.modules.config.model.flows.CSVSpecsReport.delimiter"></a>

#### delimiter

<a id="data_framework.modules.config.model.flows.JSONSpecsReport"></a>

## JSONSpecsReport Objects

```python
@dataclass
class JSONSpecsReport()
```

<a id="data_framework.modules.config.model.flows.JSONSpecsReport.format"></a>

#### format

<a id="data_framework.modules.config.model.flows.Parameters"></a>

## Parameters Objects

```python
@dataclass
class Parameters()
```

<a id="data_framework.modules.config.model.flows.Parameters.dataflow"></a>

#### dataflow

<a id="data_framework.modules.config.model.flows.Parameters.process"></a>

#### process

<a id="data_framework.modules.config.model.flows.Parameters.table"></a>

#### table

<a id="data_framework.modules.config.model.flows.Parameters.source_file_path"></a>

#### source\_file\_path

<a id="data_framework.modules.config.model.flows.Parameters.bucket_prefix"></a>

#### bucket\_prefix

<a id="data_framework.modules.config.model.flows.Parameters.file_name"></a>

#### file\_name

<a id="data_framework.modules.config.model.flows.Parameters.file_date"></a>

#### file\_date

<a id="data_framework.modules.config.model.flows.Parameters.execution_mode"></a>

#### execution\_mode

<a id="data_framework.modules.config.model.flows.Parameters.region"></a>

#### region

```python
@property
def region() -> str
```

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw"></a>

## IncomingFileLandingToRaw Objects

```python
@dataclass
class IncomingFileLandingToRaw()
```

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.zipped"></a>

#### zipped

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.file_format"></a>

#### file\_format

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.filename_pattern"></a>

#### filename\_pattern

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.filename_unzipped_pattern"></a>

#### filename\_unzipped\_pattern

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.csv_specs"></a>

#### csv\_specs

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.xml_specs"></a>

#### xml\_specs

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.json_specs"></a>

#### json\_specs

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.compare_with_previous_file"></a>

#### compare\_with\_previous\_file

<a id="data_framework.modules.config.model.flows.IncomingFileLandingToRaw.specifications"></a>

#### specifications

```python
@property
def specifications() -> Union[CSVSpecs, XMLSpecs, JSONSpecs]
```

<a id="data_framework.modules.config.model.flows.Transformation"></a>

## Transformation Objects

```python
@dataclass
class Transformation()
```

<a id="data_framework.modules.config.model.flows.Transformation.type"></a>

#### type

<a id="data_framework.modules.config.model.flows.Transformation.get_subclass_from_dict"></a>

#### get\_subclass\_from\_dict

```python
@classmethod
def get_subclass_from_dict(cls, transformation: dict)
```

<a id="data_framework.modules.config.model.flows.ParseDatesTransformation"></a>

## ParseDatesTransformation Objects

```python
@dataclass
class ParseDatesTransformation(Transformation)
```

<a id="data_framework.modules.config.model.flows.ParseDatesTransformation.columns"></a>

#### columns

<a id="data_framework.modules.config.model.flows.ParseDatesTransformation.source_format"></a>

#### source\_format

<a id="data_framework.modules.config.model.flows.ParseDatesTransformation.target_format"></a>

#### target\_format

<a id="data_framework.modules.config.model.flows.Casting"></a>

## Casting Objects

```python
@dataclass
class Casting()
```

<a id="data_framework.modules.config.model.flows.Casting.strategy"></a>

#### strategy

<a id="data_framework.modules.config.model.flows.Casting.fix_incompatible_characters"></a>

#### fix\_incompatible\_characters

<a id="data_framework.modules.config.model.flows.Casting.master_table"></a>

#### master\_table

<a id="data_framework.modules.config.model.flows.Casting.transformations"></a>

#### transformations

<a id="data_framework.modules.config.model.flows.DatabaseTable"></a>

## DatabaseTable Objects

```python
@dataclass
class DatabaseTable()
```

<a id="data_framework.modules.config.model.flows.DatabaseTable.database"></a>

#### database

<a id="data_framework.modules.config.model.flows.DatabaseTable.table"></a>

#### table

<a id="data_framework.modules.config.model.flows.DatabaseTable.primary_keys"></a>

#### primary\_keys

<a id="data_framework.modules.config.model.flows.DatabaseTable.casting"></a>

#### casting

<a id="data_framework.modules.config.model.flows.DatabaseTable.partition_field"></a>

#### partition\_field

<a id="data_framework.modules.config.model.flows.DatabaseTable.database_relation"></a>

#### database\_relation

```python
@property
def database_relation() -> str
```

<a id="data_framework.modules.config.model.flows.DatabaseTable.full_name"></a>

#### full\_name

```python
@property
def full_name() -> str
```

<a id="data_framework.modules.config.model.flows.DatabaseTable.sql_where"></a>

#### sql\_where

```python
@property
def sql_where() -> str
```

<a id="data_framework.modules.config.model.flows.TableDict"></a>

## TableDict Objects

```python
@dataclass
class TableDict()
```

<a id="data_framework.modules.config.model.flows.TableDict.tables"></a>

#### tables

<a id="data_framework.modules.config.model.flows.TableDict.table"></a>

#### table

```python
def table(table_key: str) -> Union[DatabaseTable, None]
```

<a id="data_framework.modules.config.model.flows.TableDict.table_key"></a>

#### table\_key

```python
def table_key(database: str, table: str) -> Union[str, None]
```

<a id="data_framework.modules.config.model.flows.LandingToRaw"></a>

## LandingToRaw Objects

```python
@dataclass
class LandingToRaw()
```

<a id="data_framework.modules.config.model.flows.LandingToRaw.incoming_file"></a>

#### incoming\_file

<a id="data_framework.modules.config.model.flows.LandingToRaw.output_file"></a>

#### output\_file

<a id="data_framework.modules.config.model.flows.LandingToRaw.processing_specifications"></a>

#### processing\_specifications

<a id="data_framework.modules.config.model.flows.LandingToRaw.notifications"></a>

#### notifications

<a id="data_framework.modules.config.model.flows.ProcessVars"></a>

## ProcessVars Objects

```python
@dataclass
class ProcessVars()
```

<a id="data_framework.modules.config.model.flows.ProcessVars._variables"></a>

#### \_variables

<a id="data_framework.modules.config.model.flows.ProcessVars.get_variable"></a>

#### get\_variable

```python
def get_variable(name: str)
```

<a id="data_framework.modules.config.model.flows.GenericProcess"></a>

## GenericProcess Objects

```python
@dataclass
class GenericProcess()
```

<a id="data_framework.modules.config.model.flows.GenericProcess.source_tables"></a>

#### source\_tables

<a id="data_framework.modules.config.model.flows.GenericProcess.target_tables"></a>

#### target\_tables

<a id="data_framework.modules.config.model.flows.GenericProcess.processing_specifications"></a>

#### processing\_specifications

<a id="data_framework.modules.config.model.flows.GenericProcess.notifications"></a>

#### notifications

<a id="data_framework.modules.config.model.flows.GenericProcess.vars"></a>

#### vars

<a id="data_framework.modules.config.model.flows.OutputReport"></a>

## OutputReport Objects

```python
@dataclass
class OutputReport()
```

<a id="data_framework.modules.config.model.flows.OutputReport.name"></a>

#### name

<a id="data_framework.modules.config.model.flows.OutputReport.source_table"></a>

#### source\_table

<a id="data_framework.modules.config.model.flows.OutputReport.columns"></a>

#### columns

<a id="data_framework.modules.config.model.flows.OutputReport.file_format"></a>

#### file\_format

<a id="data_framework.modules.config.model.flows.OutputReport.filename_pattern"></a>

#### filename\_pattern

<a id="data_framework.modules.config.model.flows.OutputReport.csv_specs"></a>

#### csv\_specs

<a id="data_framework.modules.config.model.flows.OutputReport.json_specs"></a>

#### json\_specs

<a id="data_framework.modules.config.model.flows.OutputReport.replaces"></a>

#### replaces

<a id="data_framework.modules.config.model.flows.OutputReport.description"></a>

#### description

<a id="data_framework.modules.config.model.flows.OutputReport.where"></a>

#### where

<a id="data_framework.modules.config.model.flows.OutputReport.columns_alias"></a>

#### columns\_alias

<a id="data_framework.modules.config.model.flows.OutputReport.filename_date_format"></a>

#### filename\_date\_format

<a id="data_framework.modules.config.model.flows.ToOutput"></a>

## ToOutput Objects

```python
@dataclass
class ToOutput()
```

<a id="data_framework.modules.config.model.flows.ToOutput.output_reports"></a>

#### output\_reports

<a id="data_framework.modules.config.model.flows.ToOutput.processing_specifications"></a>

#### processing\_specifications

<a id="data_framework.modules.config.model.flows.ToOutput.notifications"></a>

#### notifications

<a id="data_framework.modules.config.model.flows.Processes"></a>

## Processes Objects

```python
@dataclass
class Processes()
```

<a id="data_framework.modules.config.model.flows.Processes.landing_to_raw"></a>

#### landing\_to\_raw

<a id="data_framework.modules.config.model.flows.Processes.raw_to_staging"></a>

#### raw\_to\_staging

<a id="data_framework.modules.config.model.flows.Processes.staging_to_common"></a>

#### staging\_to\_common

<a id="data_framework.modules.config.model.flows.Processes.staging_to_business"></a>

#### staging\_to\_business

<a id="data_framework.modules.config.model.flows.Processes.common_to_business"></a>

#### common\_to\_business

<a id="data_framework.modules.config.model.flows.Processes.common_to_output"></a>

#### common\_to\_output

<a id="data_framework.modules.config.model.flows.Processes.business_to_output"></a>

#### business\_to\_output

<a id="data_framework.modules.config.model.flows.Config"></a>

## Config Objects

```python
@dataclass
class Config()
```

<a id="data_framework.modules.config.model.flows.Config.processes"></a>

#### processes

<a id="data_framework.modules.config.model.flows.Config.environment"></a>

#### environment

<a id="data_framework.modules.config.model.flows.Config.platform"></a>

#### platform

<a id="data_framework.modules.config.model.flows.Config.parameters"></a>

#### parameters

<a id="data_framework.modules.config.model.flows.Config.project_id"></a>

#### project\_id

<a id="data_framework.modules.config.model.flows.Config.data_framework_notifications"></a>

#### data\_framework\_notifications

<a id="data_framework.modules.config.model.flows.Config.has_next_process"></a>

#### has\_next\_process

```python
@property
def has_next_process() -> bool
```

<a id="data_framework.modules.config.model.flows.Config.is_first_process"></a>

#### is\_first\_process

```python
@property
def is_first_process() -> bool
```

<a id="data_framework.modules.config.model.flows.Config.current_process_config"></a>

#### current\_process\_config

```python
def current_process_config() -> Union[LandingToRaw, GenericProcess, ToOutput]
```

<a id="data_framework.modules.dataflow.interface_dataflow"></a>

# data\_framework.modules.dataflow.interface\_dataflow

<a id="data_framework.modules.dataflow.interface_dataflow.ABC"></a>

## ABC

<a id="data_framework.modules.dataflow.interface_dataflow.config"></a>

## config

<a id="data_framework.modules.dataflow.interface_dataflow.Config"></a>

## Config

<a id="data_framework.modules.dataflow.interface_dataflow.TableDict"></a>

## TableDict

<a id="data_framework.modules.dataflow.interface_dataflow.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.dataflow.interface_dataflow.OutputReport"></a>

## OutputReport

<a id="data_framework.modules.dataflow.interface_dataflow.ExecutionMode"></a>

## ExecutionMode

<a id="data_framework.modules.dataflow.interface_dataflow.logger"></a>

## logger

<a id="data_framework.modules.dataflow.interface_dataflow.CoreDataProcess"></a>

## CoreDataProcess

<a id="data_framework.modules.dataflow.interface_dataflow.CoreQualityControls"></a>

## CoreQualityControls

<a id="data_framework.modules.dataflow.interface_dataflow.CoreNotifications"></a>

## CoreNotifications

<a id="data_framework.modules.dataflow.interface_dataflow.NotificationToSend"></a>

## NotificationToSend

<a id="data_framework.modules.dataflow.interface_dataflow.CoreMonitoring"></a>

## CoreMonitoring

<a id="data_framework.modules.dataflow.interface_dataflow.Metric"></a>

## Metric

<a id="data_framework.modules.dataflow.interface_dataflow.MetricNames"></a>

## MetricNames

<a id="data_framework.modules.dataflow.interface_dataflow.DataflowInitializationError"></a>

## DataflowInitializationError

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponseError"></a>

## PayloadResponseError

<a id="data_framework.modules.dataflow.interface_dataflow.SSMError"></a>

## SSMError

<a id="data_framework.modules.dataflow.interface_dataflow.dataclass"></a>

## dataclass

<a id="data_framework.modules.dataflow.interface_dataflow.asdict"></a>

## asdict

<a id="data_framework.modules.dataflow.interface_dataflow.field"></a>

## field

<a id="data_framework.modules.dataflow.interface_dataflow.Any"></a>

## Any

<a id="data_framework.modules.dataflow.interface_dataflow.List"></a>

## List

<a id="data_framework.modules.dataflow.interface_dataflow.Optional"></a>

## Optional

<a id="data_framework.modules.dataflow.interface_dataflow.boto3"></a>

## boto3

<a id="data_framework.modules.dataflow.interface_dataflow.json"></a>

## json

<a id="data_framework.modules.dataflow.interface_dataflow.time"></a>

## time

<a id="data_framework.modules.dataflow.interface_dataflow.DataQualityTable"></a>

## DataQualityTable Objects

```python
@dataclass
class DataQualityTable()
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataQualityTable.database"></a>

#### database

<a id="data_framework.modules.dataflow.interface_dataflow.DataQualityTable.table"></a>

#### table

<a id="data_framework.modules.dataflow.interface_dataflow.DataQuality"></a>

## DataQuality Objects

```python
@dataclass
class DataQuality()
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataQuality.tables"></a>

#### tables

<a id="data_framework.modules.dataflow.interface_dataflow.OutputResponse"></a>

## OutputResponse Objects

```python
@dataclass
class OutputResponse()
```

<a id="data_framework.modules.dataflow.interface_dataflow.OutputResponse.name"></a>

#### name

<a id="data_framework.modules.dataflow.interface_dataflow.OutputResponse.success"></a>

#### success

<a id="data_framework.modules.dataflow.interface_dataflow.OutputResponse.error"></a>

#### error

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse"></a>

## PayloadResponse Objects

```python
@dataclass
class PayloadResponse()
```

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.success"></a>

#### success

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.next_stage"></a>

#### next\_stage

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.file_name"></a>

#### file\_name

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.file_date"></a>

#### file\_date

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.data_quality"></a>

#### data\_quality

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.outputs"></a>

#### outputs

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.notifications"></a>

#### notifications

<a id="data_framework.modules.dataflow.interface_dataflow.PayloadResponse.get_failed_outputs"></a>

#### get\_failed\_outputs

```python
def get_failed_outputs() -> List[str]
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface"></a>

## DataFlowInterface Objects

```python
class DataFlowInterface(ABC)
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.config"></a>

#### config

```python
@property
def config() -> Config
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.logger"></a>

#### logger

```python
@property
def logger()
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.data_process"></a>

#### data\_process

```python
@property
def data_process() -> CoreDataProcess
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.quality_controls"></a>

#### quality\_controls

```python
@property
def quality_controls() -> CoreQualityControls
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.notifications"></a>

#### notifications

```python
@property
def notifications() -> CoreNotifications
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.source_tables"></a>

#### source\_tables

```python
@property
def source_tables() -> TableDict
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.target_tables"></a>

#### target\_tables

```python
@property
def target_tables() -> TableDict
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.payload_response"></a>

#### payload\_response

```python
@property
def payload_response() -> PayloadResponse
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.incoming_file"></a>

#### incoming\_file

```python
@property
def incoming_file() -> DatabaseTable
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.output_file"></a>

#### output\_file

```python
@property
def output_file() -> DatabaseTable
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.output_reports"></a>

#### output\_reports

```python
@property
def output_reports() -> List[OutputReport]
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.process"></a>

#### process

```python
def process()
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.vars"></a>

#### vars

```python
def vars(name: str)
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.read_table_with_casting"></a>

#### read\_table\_with\_casting

```python
def read_table_with_casting(
        name_of_raw_table: str,
        name_of_staging_table_to_casting: str = None) -> Any
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.read_table"></a>

#### read\_table

```python
def read_table(name_of_table: str) -> Any
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.write"></a>

#### write

```python
def write(df: Any, output_table_key: str) -> None
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.save_monitorization"></a>

#### save\_monitorization

```python
def save_monitorization()
```

<a id="data_framework.modules.dataflow.interface_dataflow.DataFlowInterface.save_payload_response"></a>

#### save\_payload\_response

```python
def save_payload_response()
```

<a id="data_framework.modules.data_process.core_data_process"></a>

# data\_framework.modules.data\_process.core\_data\_process

<a id="data_framework.modules.data_process.core_data_process.LazyClassProperty"></a>

## LazyClassProperty

<a id="data_framework.modules.data_process.core_data_process.DataProcessInterface"></a>

## DataProcessInterface

<a id="data_framework.modules.data_process.core_data_process.ReadResponse"></a>

## ReadResponse

<a id="data_framework.modules.data_process.core_data_process.WriteResponse"></a>

## WriteResponse

<a id="data_framework.modules.data_process.core_data_process.config"></a>

## config

<a id="data_framework.modules.data_process.core_data_process.Technologies"></a>

## Technologies

<a id="data_framework.modules.data_process.core_data_process.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.data_process.core_data_process.List"></a>

## List

<a id="data_framework.modules.data_process.core_data_process.Any"></a>

## Any

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess"></a>

## CoreDataProcess Objects

```python
class CoreDataProcess(object)
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess._data_process"></a>

#### \_data\_process

```python
@LazyClassProperty
def _data_process(cls) -> DataProcessInterface
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.merge"></a>

#### merge

```python
@classmethod
def merge(cls,
          dataframe: Any,
          table_config: DatabaseTable,
          custom_strategy: str = None) -> WriteResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.insert_overwrite"></a>

#### insert\_overwrite

```python
@classmethod
def insert_overwrite(cls, dataframe: Any,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.datacast"></a>

#### datacast

```python
@classmethod
def datacast(cls, table_source: DatabaseTable,
             table_target: DatabaseTable) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.read_table"></a>

#### read\_table

```python
@classmethod
def read_table(cls,
               database: str,
               table: str,
               filter: str = None,
               columns: List[str] = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.delete_from_table"></a>

#### delete\_from\_table

```python
@classmethod
def delete_from_table(cls, table_config: DatabaseTable,
                      _filter: str) -> WriteResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.insert_dataframe"></a>

#### insert\_dataframe

```python
@classmethod
def insert_dataframe(cls, dataframe: Any,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.join"></a>

#### join

```python
@classmethod
def join(cls,
         df_1: Any,
         df_2: Any,
         how: str,
         left_on: List[str],
         right_on: List[str] = None,
         left_suffix: str = '_df_1',
         right_suffix: str = '_df_2') -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.create_dataframe"></a>

#### create\_dataframe

```python
@classmethod
def create_dataframe(cls, data: Any, schema: str = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.query"></a>

#### query

```python
@classmethod
def query(cls, sql: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.overwrite_columns"></a>

#### overwrite\_columns

```python
@classmethod
def overwrite_columns(cls,
                      dataframe: Any,
                      columns: List[str],
                      custom_column_suffix: str,
                      default_column_suffix: str,
                      drop_columns: bool = True) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.unfold_string_values"></a>

#### unfold\_string\_values

```python
@classmethod
def unfold_string_values(cls, dataframe: Any, column_name: str,
                         separator: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.add_dynamic_column"></a>

#### add\_dynamic\_column

```python
@classmethod
def add_dynamic_column(cls,
                       dataframe: Any,
                       new_column: str,
                       reference_column: str,
                       available_columns: List[str],
                       default_value: Any = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.stack_columns"></a>

#### stack\_columns

```python
@classmethod
def stack_columns(cls, dataframe: Any, source_columns: List[str],
                  target_columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.is_empty"></a>

#### is\_empty

```python
@classmethod
def is_empty(cls, dataframe: Any) -> bool
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.count_rows"></a>

#### count\_rows

```python
@classmethod
def count_rows(cls, dataframe: Any) -> int
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.select_columns"></a>

#### select\_columns

```python
@classmethod
def select_columns(cls, dataframe: Any, columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.core_data_process.CoreDataProcess.show_dataframe"></a>

#### show\_dataframe

```python
@classmethod
def show_dataframe(cls, dataframe: Any) -> WriteResponse
```

<a id="data_framework.modules.data_process.helpers.athena"></a>

# data\_framework.modules.data\_process.helpers.athena

<a id="data_framework.modules.data_process.helpers.athena.config"></a>

## config

<a id="data_framework.modules.data_process.helpers.athena.logger"></a>

## logger

<a id="data_framework.modules.data_process.helpers.athena.Platform"></a>

## Platform

<a id="data_framework.modules.data_process.helpers.athena.Layer"></a>

## Layer

<a id="data_framework.modules.data_process.helpers.athena.Storage"></a>

## Storage

<a id="data_framework.modules.data_process.helpers.athena.AthenaError"></a>

## AthenaError

<a id="data_framework.modules.data_process.helpers.athena.boto3"></a>

## boto3

<a id="data_framework.modules.data_process.helpers.athena.read_csv"></a>

## read\_csv

<a id="data_framework.modules.data_process.helpers.athena.DataFrame"></a>

## DataFrame

<a id="data_framework.modules.data_process.helpers.athena.sleep"></a>

## sleep

<a id="data_framework.modules.data_process.helpers.athena.BytesIO"></a>

## BytesIO

<a id="data_framework.modules.data_process.helpers.athena.Union"></a>

## Union

<a id="data_framework.modules.data_process.helpers.athena.AthenaClient"></a>

## AthenaClient Objects

```python
class AthenaClient()
```

<a id="data_framework.modules.data_process.helpers.athena.AthenaClient.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.data_process.helpers.athena.AthenaClient.execute_query"></a>

#### execute\_query

```python
def execute_query(query: str,
                  read_output: bool = True) -> Union[DataFrame, None]
```

<a id="data_framework.modules.data_process.helpers.athena.AthenaClient.wait_for_query_to_complete"></a>

#### wait\_for\_query\_to\_complete

```python
def wait_for_query_to_complete(query_execution_id: str) -> str
```

<a id="data_framework.modules.data_process.helpers.athena.AthenaClient.get_query_results"></a>

#### get\_query\_results

```python
def get_query_results(output_location: str) -> DataFrame
```

<a id="data_framework.modules.data_process.helpers.cast"></a>

# data\_framework.modules.data\_process.helpers.cast



<a id="data_framework.modules.data_process.helpers.cast.logger"></a>

## logger

<a id="data_framework.modules.data_process.helpers.cast.CoreCatalogue"></a>

## CoreCatalogue

<a id="data_framework.modules.data_process.helpers.cast.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.data_process.helpers.cast.CastQueryError"></a>

## CastQueryError

<a id="data_framework.modules.data_process.helpers.cast.Any"></a>

## Any

<a id="data_framework.modules.data_process.helpers.cast.Dict"></a>

## Dict

<a id="data_framework.modules.data_process.helpers.cast.List"></a>

## List

<a id="data_framework.modules.data_process.helpers.cast.pd"></a>

## pd

<a id="data_framework.modules.data_process.helpers.cast.DataFrame"></a>

## DataFrame

<a id="data_framework.modules.data_process.helpers.cast.Series"></a>

## Series

<a id="data_framework.modules.data_process.helpers.cast.Cast"></a>

## Cast Objects

```python
class Cast()
```

<a id="data_framework.modules.data_process.helpers.cast.Cast.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.data_process.helpers.cast.Cast.cast_columns"></a>

#### cast\_columns

```python
def cast_columns(column_name: str, column_type: str) -> str
```

<a id="data_framework.modules.data_process.helpers.cast.Cast.build_datacast_query"></a>

#### build\_datacast\_query

```python
def build_datacast_query(source_columns: List[str],
                         table_target: DatabaseTable,
                         view_name: str = 'data_to_cast') -> str
```

<a id="data_framework.modules.data_process.helpers.cast.Cast.get_query_to_insert_dataframe"></a>

#### get\_query\_to\_insert\_dataframe

```python
def get_query_to_insert_dataframe(dataframe: DataFrame,
                                  table_config: DatabaseTable) -> str
```

<a id="data_framework.modules.data_process.helpers.cast.Cast.cast_df_row"></a>

#### cast\_df\_row

```python
def cast_df_row(row: Series, column_types: Dict[str, str]) -> str
```

<a id="data_framework.modules.data_process.helpers.cast.Cast.cast_df_value"></a>

#### cast\_df\_value

```python
def cast_df_value(value: Any, _type: str) -> str
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process"></a>

# data\_framework.modules.data\_process.integrations.pandas.pandas\_data\_process

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.DataProcessInterface"></a>

## DataProcessInterface

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.ReadResponse"></a>

## ReadResponse

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.WriteResponse"></a>

## WriteResponse

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.AthenaClient"></a>

## AthenaClient

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.Cast"></a>

## Cast

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.logger"></a>

## logger

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.ReadDataError"></a>

## ReadDataError

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.WriteDataError"></a>

## WriteDataError

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.DataProcessError"></a>

## DataProcessError

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.List"></a>

## List

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.Any"></a>

## Any

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.pd"></a>

## pd

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.DataFrame"></a>

## DataFrame

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess"></a>

## PandasDataProcess Objects

```python
class PandasDataProcess(DataProcessInterface)
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.merge"></a>

#### merge

```python
def merge(dataframe: DataFrame,
          table_config: DatabaseTable,
          custom_strategy: str = None) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.datacast"></a>

#### datacast

```python
def datacast(table_source: DatabaseTable,
             table_target: DatabaseTable) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.read_table"></a>

#### read\_table

```python
def read_table(database: str,
               table: str,
               filter: str = None,
               columns: List[str] = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.delete_from_table"></a>

#### delete\_from\_table

```python
def delete_from_table(table_config: DatabaseTable,
                      _filter: str) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.insert_dataframe"></a>

#### insert\_dataframe

```python
def insert_dataframe(dataframe: DataFrame,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.join"></a>

#### join

```python
def join(df_1: DataFrame,
         df_2: DataFrame,
         how: str,
         left_on: List[str],
         right_on: List[str] = None,
         left_suffix: str = '_df_1',
         right_suffix: str = '_df_2') -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.create_dataframe"></a>

#### create\_dataframe

```python
def create_dataframe(data: Any, schema: str = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.query"></a>

#### query

```python
def query(sql: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.overwrite_columns"></a>

#### overwrite\_columns

```python
def overwrite_columns(dataframe: DataFrame,
                      columns: List[str],
                      custom_column_suffix: str,
                      default_column_suffix: str,
                      drop_columns: bool = True) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.unfold_string_values"></a>

#### unfold\_string\_values

```python
def unfold_string_values(dataframe: DataFrame, column_name: str,
                         separator: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.add_dynamic_column"></a>

#### add\_dynamic\_column

```python
def add_dynamic_column(dataframe: DataFrame,
                       new_column: str,
                       reference_column: str,
                       available_columns: List[str],
                       default_value: Any = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.stack_columns"></a>

#### stack\_columns

```python
def stack_columns(dataframe: DataFrame, source_columns: List[str],
                  target_columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.is_empty"></a>

#### is\_empty

```python
def is_empty(dataframe: DataFrame) -> bool
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.count_rows"></a>

#### count\_rows

```python
def count_rows(dataframe: DataFrame) -> int
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.select_columns"></a>

#### select\_columns

```python
def select_columns(dataframe: DataFrame, columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.pandas.pandas_data_process.PandasDataProcess.show_dataframe"></a>

#### show\_dataframe

```python
def show_dataframe(dataframe: DataFrame) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config"></a>

# data\_framework.modules.data\_process.integrations.spark.dynamic\_config

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.json"></a>

## json

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig"></a>

## DynamicConfig Objects

```python
class DynamicConfig()
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_cores"></a>

#### determine\_cores

```python
@classmethod
def determine_cores(cls, dataset_size_gb: float) -> int
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_driver_settings"></a>

#### determine\_driver\_settings

```python
@classmethod
def determine_driver_settings(cls, dataset_size_gb: float,
                              max_memory_per_executor_gb: int) -> (int, int)
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_disk_size"></a>

#### determine\_disk\_size

```python
@classmethod
def determine_disk_size(cls, dataset_size_gb: float) -> int
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.determine_memory_overhead_factor"></a>

#### determine\_memory\_overhead\_factor

```python
@classmethod
def determine_memory_overhead_factor(cls, dataset_size_gb: float) -> float
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.adjust_resources_to_emr_serverless_constraints"></a>

#### adjust\_resources\_to\_emr\_serverless\_constraints

```python
@classmethod
def adjust_resources_to_emr_serverless_constraints(
        cls, cpu: int, memory_gb: int) -> (int, int)
```

<a id="data_framework.modules.data_process.integrations.spark.dynamic_config.DynamicConfig.recommend_spark_config"></a>

#### recommend\_spark\_config

```python
@classmethod
def recommend_spark_config(cls,
                           dataset_size_gb: float = 2,
                           job_type: str = "batch",
                           optimization_goal: str = "cost",
                           avg_file_size_mb: int = 500,
                           max_executors: int = 20,
                           max_memory_per_executor_gb: int = 120,
                           emr_application_id: str = None,
                           num_instances: int = 20)
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process"></a>

# data\_framework.modules.data\_process.integrations.spark.spark\_data\_process

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.DataProcessInterface"></a>

## DataProcessInterface

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.ReadResponse"></a>

## ReadResponse

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.WriteResponse"></a>

## WriteResponse

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.utils"></a>

## utils

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.Storage"></a>

## Storage

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.Layer"></a>

## Layer

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.config"></a>

## config

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.logger"></a>

## logger

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.Cast"></a>

## Cast

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.CoreCatalogue"></a>

## CoreCatalogue

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.CoreMonitoring"></a>

## CoreMonitoring

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.MetricNames"></a>

## MetricNames

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.ExecutionMode"></a>

## ExecutionMode

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.CastingStrategy"></a>

## CastingStrategy

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.LandingFileFormat"></a>

## LandingFileFormat

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.debug_code"></a>

## debug\_code

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.DynamicConfig"></a>

## DynamicConfig

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.ReadDataError"></a>

## ReadDataError

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.WriteDataError"></a>

## WriteDataError

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.DataProcessError"></a>

## DataProcessError

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.CastDataError"></a>

## CastDataError

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.DeleteDataError"></a>

## DeleteDataError

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkConfigurationError"></a>

## SparkConfigurationError

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.List"></a>

## List

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.Any"></a>

## Any

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.Union"></a>

## Union

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.BytesIO"></a>

## BytesIO

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkConf"></a>

## SparkConf

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkSession"></a>

## SparkSession

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.DataFrame"></a>

## DataFrame

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.when"></a>

## when

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.StructType"></a>

## StructType

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.f"></a>

## f

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.time"></a>

## time

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.random"></a>

## random

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.inspect"></a>

## inspect

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.iceberg_exceptions"></a>

#### iceberg\_exceptions

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess"></a>

## SparkDataProcess Objects

```python
class SparkDataProcess(DataProcessInterface)
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.__iceberg_snapshot_metrics_map"></a>

#### \_\_iceberg\_snapshot\_metrics\_map

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._build_complete_table_name"></a>

#### \_build\_complete\_table\_name

```python
def _build_complete_table_name(database: str, table: str) -> str
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._track_table_metric"></a>

#### \_track\_table\_metric

```python
def _track_table_metric(table_config: DatabaseTable,
                        data_frame: DataFrame = None)
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.merge"></a>

#### merge

```python
def merge(dataframe: DataFrame,
          table_config: DatabaseTable,
          custom_strategy: str = None) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.insert_overwrite"></a>

#### insert\_overwrite

```python
def insert_overwrite(dataframe: Any,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.datacast"></a>

#### datacast

```python
def datacast(table_source: DatabaseTable,
             table_target: DatabaseTable) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._read_raw_file"></a>

#### \_read\_raw\_file

```python
def _read_raw_file(base_path: str,
                   data_path: str,
                   schema: Union[StructType, None] = None) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._read_raw_json_file"></a>

#### \_read\_raw\_json\_file

```python
def _read_raw_json_file(table_path: str, partition_path: str,
                        casting_strategy: CastingStrategy) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._execute_query"></a>

#### \_execute\_query

```python
def _execute_query(query: str) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.read_table"></a>

#### read\_table

```python
def read_table(database: str,
               table: str,
               filter: str = None,
               columns: List[str] = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.delete_from_table"></a>

#### delete\_from\_table

```python
def delete_from_table(table_config: DatabaseTable,
                      _filter: str) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.insert_dataframe"></a>

#### insert\_dataframe

```python
def insert_dataframe(dataframe: DataFrame,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess._select_table_columns"></a>

#### \_select\_table\_columns

```python
def _select_table_columns(dataframe: DataFrame,
                          table_config: DatabaseTable) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.join"></a>

#### join

```python
def join(df_1: DataFrame,
         df_2: DataFrame,
         how: str,
         left_on: List[str],
         right_on: List[str] = None,
         left_suffix: str = '_df_1',
         right_suffix: str = '_df_2') -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.create_dataframe"></a>

#### create\_dataframe

```python
def create_dataframe(data: Any, schema: str = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.query"></a>

#### query

```python
def query(sql: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.overwrite_columns"></a>

#### overwrite\_columns

```python
def overwrite_columns(dataframe: DataFrame,
                      columns: List[str],
                      custom_column_suffix: str,
                      default_column_suffix: str,
                      drop_columns: bool = True) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.unfold_string_values"></a>

#### unfold\_string\_values

```python
def unfold_string_values(dataframe: DataFrame, column_name: str,
                         separator: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.add_dynamic_column"></a>

#### add\_dynamic\_column

```python
def add_dynamic_column(dataframe: DataFrame,
                       new_column: str,
                       reference_column: str,
                       available_columns: List[str],
                       default_value: Any = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.stack_columns"></a>

#### stack\_columns

```python
def stack_columns(dataframe: DataFrame, source_columns: List[str],
                  target_columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.is_empty"></a>

#### is\_empty

```python
def is_empty(dataframe: DataFrame) -> bool
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.count_rows"></a>

#### count\_rows

```python
def count_rows(dataframe: DataFrame) -> int
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.select_columns"></a>

#### select\_columns

```python
def select_columns(dataframe: DataFrame, columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.integrations.spark.spark_data_process.SparkDataProcess.show_dataframe"></a>

#### show\_dataframe

```python
def show_dataframe(dataframe: DataFrame) -> WriteResponse
```

<a id="data_framework.modules.data_process.integrations.spark.transformations.parse_dates"></a>

# data\_framework.modules.data\_process.integrations.spark.transformations.parse\_dates

<a id="data_framework.modules.data_process.integrations.spark.transformations.parse_dates.Transformation"></a>

## Transformation

<a id="data_framework.modules.data_process.integrations.spark.transformations.parse_dates.DataFrame"></a>

## DataFrame

<a id="data_framework.modules.data_process.integrations.spark.transformations.parse_dates.f"></a>

## f

<a id="data_framework.modules.data_process.integrations.spark.transformations.parse_dates.parse_dates"></a>

#### parse\_dates

```python
def parse_dates(df: DataFrame, transformation: Transformation) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.utils"></a>

# data\_framework.modules.data\_process.integrations.spark.utils

<a id="data_framework.modules.data_process.integrations.spark.utils.Transformation"></a>

## Transformation

<a id="data_framework.modules.data_process.integrations.spark.utils.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.data_process.integrations.spark.utils.JSONFormat"></a>

## JSONFormat

<a id="data_framework.modules.data_process.integrations.spark.utils.config"></a>

## config

<a id="data_framework.modules.data_process.integrations.spark.utils.TransformationNotImplementedError"></a>

## TransformationNotImplementedError

<a id="data_framework.modules.data_process.integrations.spark.utils.TransformationError"></a>

## TransformationError

<a id="data_framework.modules.data_process.integrations.spark.utils.CoreCatalogue"></a>

## CoreCatalogue

<a id="data_framework.modules.data_process.integrations.spark.utils.import_module"></a>

## import\_module

<a id="data_framework.modules.data_process.integrations.spark.utils.List"></a>

## List

<a id="data_framework.modules.data_process.integrations.spark.utils.Dict"></a>

## Dict

<a id="data_framework.modules.data_process.integrations.spark.utils.Any"></a>

## Any

<a id="data_framework.modules.data_process.integrations.spark.utils.BytesIO"></a>

## BytesIO

<a id="data_framework.modules.data_process.integrations.spark.utils.f"></a>

## f

<a id="data_framework.modules.data_process.integrations.spark.utils.DataFrame"></a>

## DataFrame

<a id="data_framework.modules.data_process.integrations.spark.utils.StructType"></a>

## StructType

<a id="data_framework.modules.data_process.integrations.spark.utils.StructField"></a>

## StructField

<a id="data_framework.modules.data_process.integrations.spark.utils.StringType"></a>

## StringType

<a id="data_framework.modules.data_process.integrations.spark.utils.IntegerType"></a>

## IntegerType

<a id="data_framework.modules.data_process.integrations.spark.utils.FloatType"></a>

## FloatType

<a id="data_framework.modules.data_process.integrations.spark.utils.DoubleType"></a>

## DoubleType

<a id="data_framework.modules.data_process.integrations.spark.utils.BooleanType"></a>

## BooleanType

<a id="data_framework.modules.data_process.integrations.spark.utils.DateType"></a>

## DateType

<a id="data_framework.modules.data_process.integrations.spark.utils.TimestampType"></a>

## TimestampType

<a id="data_framework.modules.data_process.integrations.spark.utils.re"></a>

## re

<a id="data_framework.modules.data_process.integrations.spark.utils.json"></a>

## json

<a id="data_framework.modules.data_process.integrations.spark.utils.convert_schema_to_strings"></a>

#### convert\_schema\_to\_strings

```python
def convert_schema_to_strings(columns: List[str]) -> StructType
```

<a id="data_framework.modules.data_process.integrations.spark.utils.map_to_spark_type"></a>

#### map\_to\_spark\_type

```python
def map_to_spark_type(db_type: str)
```

<a id="data_framework.modules.data_process.integrations.spark.utils.apply_transformations"></a>

#### apply\_transformations

```python
def apply_transformations(df: DataFrame, transformations: List[Transformation],
                          **kwargs: Dict[str, Any]) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.utils.fix_column_incompatible_characters"></a>

#### fix\_column\_incompatible\_characters

```python
def fix_column_incompatible_characters(json)
```

<a id="data_framework.modules.data_process.integrations.spark.utils.fix_incompatible_characters"></a>

#### fix\_incompatible\_characters

```python
def fix_incompatible_characters(df_origin: DataFrame,
                                table_target: DatabaseTable) -> DataFrame
```

<a id="data_framework.modules.data_process.integrations.spark.utils.parse_json"></a>

#### parse\_json

```python
def parse_json(files: List[BytesIO]) -> List[dict]
```

<a id="data_framework.modules.data_process.interface_data_process"></a>

# data\_framework.modules.data\_process.interface\_data\_process

<a id="data_framework.modules.data_process.interface_data_process.ABC"></a>

## ABC

<a id="data_framework.modules.data_process.interface_data_process.abstractmethod"></a>

## abstractmethod

<a id="data_framework.modules.data_process.interface_data_process.dataclass"></a>

## dataclass

<a id="data_framework.modules.data_process.interface_data_process.List"></a>

## List

<a id="data_framework.modules.data_process.interface_data_process.Any"></a>

## Any

<a id="data_framework.modules.data_process.interface_data_process.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.data_process.interface_data_process.ReadResponse"></a>

## ReadResponse Objects

```python
@dataclass
class ReadResponse()
```

<a id="data_framework.modules.data_process.interface_data_process.ReadResponse.success"></a>

#### success

<a id="data_framework.modules.data_process.interface_data_process.ReadResponse.error"></a>

#### error

<a id="data_framework.modules.data_process.interface_data_process.ReadResponse.data"></a>

#### data

<a id="data_framework.modules.data_process.interface_data_process.WriteResponse"></a>

## WriteResponse Objects

```python
@dataclass
class WriteResponse()
```

<a id="data_framework.modules.data_process.interface_data_process.WriteResponse.success"></a>

#### success

<a id="data_framework.modules.data_process.interface_data_process.WriteResponse.error"></a>

#### error

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface"></a>

## DataProcessInterface Objects

```python
class DataProcessInterface(ABC)
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.merge"></a>

#### merge

```python
@abstractmethod
def merge(dataframe: Any,
          table_config: DatabaseTable,
          custom_strategy: str = None) -> WriteResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.insert_overwrite"></a>

#### insert\_overwrite

```python
@abstractmethod
def insert_overwrite(dataframe: Any,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.datacast"></a>

#### datacast

```python
@abstractmethod
def datacast(table_source: DatabaseTable,
             table_target: DatabaseTable) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.read_table"></a>

#### read\_table

```python
@abstractmethod
def read_table(database: str, table: str, filter: str,
               columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.delete_from_table"></a>

#### delete\_from\_table

```python
@abstractmethod
def delete_from_table(table_config: DatabaseTable,
                      _filter: str) -> WriteResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.insert_dataframe"></a>

#### insert\_dataframe

```python
@abstractmethod
def insert_dataframe(dataframe: Any,
                     table_config: DatabaseTable) -> WriteResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.join"></a>

#### join

```python
@abstractmethod
def join(df_1: Any,
         df_2: Any,
         how: str,
         left_on: List[str],
         right_on: List[str] = None,
         left_suffix: str = '_df_1',
         right_suffix: str = '_df_2') -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.create_dataframe"></a>

#### create\_dataframe

```python
@abstractmethod
def create_dataframe(data: Any, schema: str = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.query"></a>

#### query

```python
@abstractmethod
def query(sql: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.overwrite_columns"></a>

#### overwrite\_columns

```python
@abstractmethod
def overwrite_columns(dataframe: Any,
                      columns: List[str],
                      custom_column_suffix: str,
                      default_column_suffix: str,
                      drop_columns: bool = True) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.unfold_string_values"></a>

#### unfold\_string\_values

```python
@abstractmethod
def unfold_string_values(dataframe: Any, column_name: str,
                         separator: str) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.add_dynamic_column"></a>

#### add\_dynamic\_column

```python
@abstractmethod
def add_dynamic_column(dataframe: Any,
                       new_column: str,
                       reference_column: str,
                       available_columns: List[str],
                       default_value: Any = None) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.stack_columns"></a>

#### stack\_columns

```python
@abstractmethod
def stack_columns(dataframe: Any, source_columns: List[str],
                  target_columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.is_empty"></a>

#### is\_empty

```python
@abstractmethod
def is_empty(dataframe: Any) -> bool
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.count_rows"></a>

#### count\_rows

```python
@abstractmethod
def count_rows(dataframe: Any) -> int
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.select_columns"></a>

#### select\_columns

```python
@abstractmethod
def select_columns(dataframe: Any, columns: List[str]) -> ReadResponse
```

<a id="data_framework.modules.data_process.interface_data_process.DataProcessInterface.show_dataframe"></a>

#### show\_dataframe

```python
@abstractmethod
def show_dataframe(dataframe: Any) -> WriteResponse
```

<a id="data_framework.modules.exception.aws_exceptions"></a>

# data\_framework.modules.exception.aws\_exceptions

Definition of exceptions for AWS services

<a id="data_framework.modules.exception.aws_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.aws_exceptions.STSError"></a>

## STSError Objects

```python
class STSError(DataFrameworkError)
```

Error related to STS

<a id="data_framework.modules.exception.aws_exceptions.STSError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.aws_exceptions.GlueError"></a>

## GlueError Objects

```python
class GlueError(DataFrameworkError)
```

Error related to Glue

<a id="data_framework.modules.exception.aws_exceptions.GlueError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.aws_exceptions.AthenaError"></a>

## AthenaError Objects

```python
class AthenaError(DataFrameworkError)
```

Error related to Athena

<a id="data_framework.modules.exception.aws_exceptions.AthenaError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.aws_exceptions.SSMError"></a>

## SSMError Objects

```python
class SSMError(DataFrameworkError)
```

Error related to SSM

<a id="data_framework.modules.exception.aws_exceptions.SSMError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.aws_exceptions.S3Error"></a>

## S3Error Objects

```python
class S3Error(DataFrameworkError)
```

Error related to S3

<a id="data_framework.modules.exception.aws_exceptions.S3Error.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.catalogue_exceptions"></a>

# data\_framework.modules.exception.catalogue\_exceptions

Definition of exceptions for catalogue module

<a id="data_framework.modules.exception.catalogue_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.catalogue_exceptions.CreatePartitionError"></a>

## CreatePartitionError Objects

```python
class CreatePartitionError(DataFrameworkError)
```

Error raised when a partition could not be created in a table

<a id="data_framework.modules.exception.catalogue_exceptions.CreatePartitionError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(database: str, table: str, partition_field: str,
             partition_value: str)
```

<a id="data_framework.modules.exception.catalogue_exceptions.InvalidPartitionFieldError"></a>

## InvalidPartitionFieldError Objects

```python
class InvalidPartitionFieldError(DataFrameworkError)
```

Error raised when the specified partition field does not exist in a table

<a id="data_framework.modules.exception.catalogue_exceptions.InvalidPartitionFieldError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(database: str, table: str, partition_field: str)
```

<a id="data_framework.modules.exception.catalogue_exceptions.SchemaError"></a>

## SchemaError Objects

```python
class SchemaError(DataFrameworkError)
```

Error raised when a table schema could not be obtained correctly

<a id="data_framework.modules.exception.catalogue_exceptions.SchemaError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(database: str, table: str)
```

<a id="data_framework.modules.exception.config_exceptions"></a>

# data\_framework.modules.exception.config\_exceptions

Definition of exceptions for config module

<a id="data_framework.modules.exception.config_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.config_exceptions.List"></a>

## List

<a id="data_framework.modules.exception.config_exceptions.ConfigError"></a>

## ConfigError Objects

```python
class ConfigError(DataFrameworkError)
```

Base class for all config module exceptions

<a id="data_framework.modules.exception.config_exceptions.ConfigError.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.exception.config_exceptions.ConfigFileNotFoundError"></a>

## ConfigFileNotFoundError Objects

```python
class ConfigFileNotFoundError(DataFrameworkError)
```

Error raised when a config file is not found in the specified path

<a id="data_framework.modules.exception.config_exceptions.ConfigFileNotFoundError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(config_file_path: str)
```

<a id="data_framework.modules.exception.config_exceptions.AccountNotFoundError"></a>

## AccountNotFoundError Objects

```python
class AccountNotFoundError(DataFrameworkError)
```

Error raised when a specific AWS accound ID is not found in the Data Framework config file

<a id="data_framework.modules.exception.config_exceptions.AccountNotFoundError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(account_id: str, available_ids: List[str])
```

<a id="data_framework.modules.exception.config_exceptions.ParameterParseError"></a>

## ParameterParseError Objects

```python
class ParameterParseError(DataFrameworkError)
```

Error raised when the input parameters could not be parsed correctly

<a id="data_framework.modules.exception.config_exceptions.ParameterParseError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(arguments: List[str])
```

<a id="data_framework.modules.exception.config_exceptions.ConfigParseError"></a>

## ConfigParseError Objects

```python
class ConfigParseError(DataFrameworkError)
```

Error raised when the dataflow config could not be parsed correctly

<a id="data_framework.modules.exception.config_exceptions.ConfigParseError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(field: str, field_type: str)
```

<a id="data_framework.modules.exception.config_exceptions.EmptyProcessConfigError"></a>

## EmptyProcessConfigError Objects

```python
class EmptyProcessConfigError(DataFrameworkError)
```

Error raised when the configuration of a specific process is empty

<a id="data_framework.modules.exception.config_exceptions.EmptyProcessConfigError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(process: str)
```

<a id="data_framework.modules.exception.config_exceptions.ProcessNotFoundError"></a>

## ProcessNotFoundError Objects

```python
class ProcessNotFoundError(DataFrameworkError)
```

Error raised when a specific process is not found in the dataflow config file

<a id="data_framework.modules.exception.config_exceptions.ProcessNotFoundError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(process: str, available_processes: List[str])
```

<a id="data_framework.modules.exception.config_exceptions.TableKeyError"></a>

## TableKeyError Objects

```python
class TableKeyError(DataFrameworkError)
```

Error raised when a specific table key is not found in the dataflow config file

<a id="data_framework.modules.exception.config_exceptions.TableKeyError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(table_key: str, available_table_keys: List[str])
```

<a id="data_framework.modules.exception.config_exceptions.TableConfigNotFoundError"></a>

## TableConfigNotFoundError Objects

```python
class TableConfigNotFoundError(DataFrameworkError)
```

Error raised when a specific database and table are not found in the dataflow config file

<a id="data_framework.modules.exception.config_exceptions.TableConfigNotFoundError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(database: str, table: str)
```

<a id="data_framework.modules.exception.dataflow_exceptions"></a>

# data\_framework.modules.exception.dataflow\_exceptions

Definition of exceptions for dataflow module

<a id="data_framework.modules.exception.dataflow_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.dataflow_exceptions.DataflowInitializationError"></a>

## DataflowInitializationError Objects

```python
class DataflowInitializationError(DataFrameworkError)
```

Error raised when dataflow class could not be initialized correctly

<a id="data_framework.modules.exception.dataflow_exceptions.DataflowInitializationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.exception.dataflow_exceptions.PayloadResponseError"></a>

## PayloadResponseError Objects

```python
class PayloadResponseError(DataFrameworkError)
```

Error raised when dataflow response could not be generated correctly

<a id="data_framework.modules.exception.dataflow_exceptions.PayloadResponseError.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.exception.data_process_exceptions"></a>

# data\_framework.modules.exception.data\_process\_exceptions

Definition of exceptions for data process module

<a id="data_framework.modules.exception.data_process_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.data_process_exceptions.List"></a>

## List

<a id="data_framework.modules.exception.data_process_exceptions.TransformationNotImplementedError"></a>

## TransformationNotImplementedError Objects

```python
class TransformationNotImplementedError(DataFrameworkError)
```

Error raised when a transformation specified in the config file is not implemented yet

<a id="data_framework.modules.exception.data_process_exceptions.TransformationNotImplementedError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(transformation: str, available_types: List[str] = None)
```

<a id="data_framework.modules.exception.data_process_exceptions.TransformationError"></a>

## TransformationError Objects

```python
class TransformationError(DataFrameworkError)
```

Error raised when a pre-defined transformation fails

<a id="data_framework.modules.exception.data_process_exceptions.TransformationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(transformation: str)
```

<a id="data_framework.modules.exception.data_process_exceptions.CastQueryError"></a>

## CastQueryError Objects

```python
class CastQueryError(DataFrameworkError)
```

Error raised when the generation of a query to cast data fails

<a id="data_framework.modules.exception.data_process_exceptions.CastQueryError.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.exception.data_process_exceptions.CastDataError"></a>

## CastDataError Objects

```python
class CastDataError(DataFrameworkError)
```

Error raised when the casting of a table fails

<a id="data_framework.modules.exception.data_process_exceptions.CastDataError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(source_database: str, source_table: str, target_database: str,
             target_table: str, casting_strategy: str)
```

<a id="data_framework.modules.exception.data_process_exceptions.ReadDataError"></a>

## ReadDataError Objects

```python
class ReadDataError(DataFrameworkError)
```

Error raised when a query to read data fails

<a id="data_framework.modules.exception.data_process_exceptions.ReadDataError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(query: str)
```

<a id="data_framework.modules.exception.data_process_exceptions.WriteDataError"></a>

## WriteDataError Objects

```python
class WriteDataError(DataFrameworkError)
```

Error raised when data could not be written successfully in a table

<a id="data_framework.modules.exception.data_process_exceptions.WriteDataError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(database: str, table: str)
```

<a id="data_framework.modules.exception.data_process_exceptions.DeleteDataError"></a>

## DeleteDataError Objects

```python
class DeleteDataError(DataFrameworkError)
```

Error raised when data could not be deleted successfully from a table

<a id="data_framework.modules.exception.data_process_exceptions.DeleteDataError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(database: str, table: str)
```

<a id="data_framework.modules.exception.data_process_exceptions.DataProcessError"></a>

## DataProcessError Objects

```python
class DataProcessError(DataFrameworkError)
```

Error raised when a data transformation fails

<a id="data_framework.modules.exception.data_process_exceptions.DataProcessError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str = 'Error performing data transformation')
```

<a id="data_framework.modules.exception.data_process_exceptions.SparkConfigurationError"></a>

## SparkConfigurationError Objects

```python
class SparkConfigurationError(DataFrameworkError)
```

Error raised when Spark configuration fails

<a id="data_framework.modules.exception.data_process_exceptions.SparkConfigurationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str = 'Error configuring Spark session')
```

<a id="data_framework.modules.exception.generic_exceptions"></a>

# data\_framework.modules.exception.generic\_exceptions

Definition of generic data framework exceptions

<a id="data_framework.modules.exception.generic_exceptions.traceback"></a>

## traceback

<a id="data_framework.modules.exception.generic_exceptions.DataFrameworkError"></a>

## DataFrameworkError Objects

```python
class DataFrameworkError(Exception)
```

Base class for all Data Framework exceptions

<a id="data_framework.modules.exception.generic_exceptions.DataFrameworkError.format_exception"></a>

#### format\_exception

```python
def format_exception() -> str
```

<a id="data_framework.modules.exception.generic_exceptions.LoggerInitializationError"></a>

## LoggerInitializationError Objects

```python
class LoggerInitializationError(DataFrameworkError)
```

Error raised when logger could not be configured correctly

<a id="data_framework.modules.exception.generic_exceptions.LoggerInitializationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.exception.landing_exceptions"></a>

# data\_framework.modules.exception.landing\_exceptions

Definition of exceptions for landing step

<a id="data_framework.modules.exception.landing_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.landing_exceptions.FileProcessError"></a>

## FileProcessError Objects

```python
class FileProcessError(DataFrameworkError)
```

Error raised when a file could not be processed correctly

<a id="data_framework.modules.exception.landing_exceptions.FileProcessError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(file_path: str)
```

<a id="data_framework.modules.exception.landing_exceptions.FileReadError"></a>

## FileReadError Objects

```python
class FileReadError(DataFrameworkError)
```

Error raised when a file could not be read correctly

<a id="data_framework.modules.exception.landing_exceptions.FileReadError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(file_path: str)
```

<a id="data_framework.modules.exception.landing_exceptions.InvalidDateRegexError"></a>

## InvalidDateRegexError Objects

```python
class InvalidDateRegexError(DataFrameworkError)
```

Error raised when the date in a filename does not match the specified regex

<a id="data_framework.modules.exception.landing_exceptions.InvalidDateRegexError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(filename: str, pattern: str)
```

<a id="data_framework.modules.exception.landing_exceptions.InvalidRegexGroupError"></a>

## InvalidRegexGroupError Objects

```python
class InvalidRegexGroupError(DataFrameworkError)
```

Error raised when the groups in the filename date regex are invalid

<a id="data_framework.modules.exception.landing_exceptions.InvalidRegexGroupError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(pattern: str)
```

<a id="data_framework.modules.exception.landing_exceptions.InvalidFileError"></a>

## InvalidFileError Objects

```python
class InvalidFileError(DataFrameworkError)
```

Error raised when a file does not meet the defined quality controls

<a id="data_framework.modules.exception.landing_exceptions.InvalidFileError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(file_path: str)
```

<a id="data_framework.modules.exception.notification_exceptions"></a>

# data\_framework.modules.exception.notification\_exceptions

Definition of exceptions for notification module

<a id="data_framework.modules.exception.notification_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.notification_exceptions.List"></a>

## List

<a id="data_framework.modules.exception.notification_exceptions.NotificationNotFoundError"></a>

## NotificationNotFoundError Objects

```python
class NotificationNotFoundError(DataFrameworkError)
```

Error raised when the system tries to send a notification that is not defined

<a id="data_framework.modules.exception.notification_exceptions.NotificationNotFoundError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(notification: str, available_notifications: List[str] = None)
```

<a id="data_framework.modules.exception.notification_exceptions.DuplicatedNotificationError"></a>

## DuplicatedNotificationError Objects

```python
class DuplicatedNotificationError(DataFrameworkError)
```

Error raised when the user defines a notification with
the same name as one of the pre-defined notifications

<a id="data_framework.modules.exception.notification_exceptions.DuplicatedNotificationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(duplicated_notifications: List[str])
```

<a id="data_framework.modules.exception.notification_exceptions.NotificationError"></a>

## NotificationError Objects

```python
class NotificationError(DataFrameworkError)
```

Error raised when a notification could not be sent successfully

<a id="data_framework.modules.exception.notification_exceptions.NotificationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(notification: str)
```

<a id="data_framework.modules.exception.notification_exceptions.SubjectLimitExceededError"></a>

## SubjectLimitExceededError Objects

```python
class SubjectLimitExceededError(DataFrameworkError)
```

Error raised when the maximum length of a notification subject is exceeded

<a id="data_framework.modules.exception.notification_exceptions.SubjectLimitExceededError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(notification: str, max_length: int)
```

<a id="data_framework.modules.exception.notification_exceptions.BodyLimitExceededError"></a>

## BodyLimitExceededError Objects

```python
class BodyLimitExceededError(DataFrameworkError)
```

Error raised when the maximum length of a notification body is exceeded

<a id="data_framework.modules.exception.notification_exceptions.BodyLimitExceededError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(notification: str, max_length: int)
```

<a id="data_framework.modules.exception.notification_exceptions.NotificationLimitExceededError"></a>

## NotificationLimitExceededError Objects

```python
class NotificationLimitExceededError(DataFrameworkError)
```

Error raised when the maximum number of notifications is exceeded

<a id="data_framework.modules.exception.notification_exceptions.NotificationLimitExceededError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(max_notifications: int)
```

<a id="data_framework.modules.exception.output_exceptions"></a>

# data\_framework.modules.exception.output\_exceptions

Definition of exceptions for output step

<a id="data_framework.modules.exception.output_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.output_exceptions.List"></a>

## List

<a id="data_framework.modules.exception.output_exceptions.OutputError"></a>

## OutputError Objects

```python
class OutputError(DataFrameworkError)
```

Error raised when one or more output files could not be generated correctly

<a id="data_framework.modules.exception.output_exceptions.OutputError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(failed_outputs: List[str])
```

<a id="data_framework.modules.exception.output_exceptions.OutputGenerationError"></a>

## OutputGenerationError Objects

```python
class OutputGenerationError(DataFrameworkError)
```

Error raised when a specific output file could not be generated correctly

<a id="data_framework.modules.exception.output_exceptions.OutputGenerationError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(output_name: str, error_message: str)
```

<a id="data_framework.modules.exception.output_exceptions.NoOutputDataError"></a>

## NoOutputDataError Objects

```python
class NoOutputDataError(DataFrameworkError)
```

Error raised when there is no data to include in a specific output file

<a id="data_framework.modules.exception.output_exceptions.NoOutputDataError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(output_name: str)
```

<a id="data_framework.modules.exception.storage_exceptions"></a>

# data\_framework.modules.exception.storage\_exceptions

Definition of exceptions for storage module

<a id="data_framework.modules.exception.storage_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.storage_exceptions.StorageError"></a>

## StorageError Objects

```python
class StorageError(DataFrameworkError)
```

Error raised when a storage generic operation fails

<a id="data_framework.modules.exception.storage_exceptions.StorageError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.storage_exceptions.StorageReadError"></a>

## StorageReadError Objects

```python
class StorageReadError(DataFrameworkError)
```

Error raised when a storage read operation fails

<a id="data_framework.modules.exception.storage_exceptions.StorageReadError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(path: str)
```

<a id="data_framework.modules.exception.storage_exceptions.StorageWriteError"></a>

## StorageWriteError Objects

```python
class StorageWriteError(DataFrameworkError)
```

Error raised when a storage write operation fails

<a id="data_framework.modules.exception.storage_exceptions.StorageWriteError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(path: str)
```

<a id="data_framework.modules.exception.validation_exceptions"></a>

# data\_framework.modules.exception.validation\_exceptions

Definition of exceptions for validation module

<a id="data_framework.modules.exception.validation_exceptions.DataFrameworkError"></a>

## DataFrameworkError

<a id="data_framework.modules.exception.validation_exceptions.QualityControlsError"></a>

## QualityControlsError Objects

```python
class QualityControlsError(DataFrameworkError)
```

Error raised when data validation fails

<a id="data_framework.modules.exception.validation_exceptions.QualityControlsError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(table_name: str)
```

<a id="data_framework.modules.exception.validation_exceptions.FailedRulesError"></a>

## FailedRulesError Objects

```python
class FailedRulesError(DataFrameworkError)
```

Error raised when one or more quality rules fail

<a id="data_framework.modules.exception.validation_exceptions.FailedRulesError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(n_failed_rules: int)
```

<a id="data_framework.modules.exception.validation_exceptions.RuleComputeError"></a>

## RuleComputeError Objects

```python
class RuleComputeError(DataFrameworkError)
```

Error raised when a rule computation fails

<a id="data_framework.modules.exception.validation_exceptions.RuleComputeError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(rule_id: str, rule_type: str)
```

<a id="data_framework.modules.exception.validation_exceptions.ValidationFunctionNotFoundError"></a>

## ValidationFunctionNotFoundError Objects

```python
class ValidationFunctionNotFoundError(DataFrameworkError)
```

Error raised when the Python function defined for a validation is not found

<a id="data_framework.modules.exception.validation_exceptions.ValidationFunctionNotFoundError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(function_name: str)
```

<a id="data_framework.modules.exception.validation_exceptions.ParentNotConfiguredError"></a>

## ParentNotConfiguredError Objects

```python
class ParentNotConfiguredError(DataFrameworkError)
```

Error raised when the parent with validation functions is not configured

<a id="data_framework.modules.exception.validation_exceptions.ParentNotConfiguredError.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.exception.validation_exceptions.InvalidThresholdError"></a>

## InvalidThresholdError Objects

```python
class InvalidThresholdError(DataFrameworkError)
```

Error raised when the threshold defined for a rule is not valid

<a id="data_framework.modules.exception.validation_exceptions.InvalidThresholdError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.validation_exceptions.InvalidAlgorithmError"></a>

## InvalidAlgorithmError Objects

```python
class InvalidAlgorithmError(DataFrameworkError)
```

Error raised when the algorithm defined for a rule is not valid

<a id="data_framework.modules.exception.validation_exceptions.InvalidAlgorithmError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.validation_exceptions.InvalidRuleError"></a>

## InvalidRuleError Objects

```python
class InvalidRuleError(DataFrameworkError)
```

Error raised when the definition of a rule is not valid

<a id="data_framework.modules.exception.validation_exceptions.InvalidRuleError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.exception.validation_exceptions.InvalidDataFrameError"></a>

## InvalidDataFrameError Objects

```python
class InvalidDataFrameError(DataFrameworkError)
```

Error raised when the threshold defined for a rule is not valid

<a id="data_framework.modules.exception.validation_exceptions.InvalidDataFrameError.__init__"></a>

#### \_\_init\_\_

```python
def __init__(error_message: str)
```

<a id="data_framework.modules.monitoring.core_monitoring"></a>

# data\_framework.modules.monitoring.core\_monitoring

<a id="data_framework.modules.monitoring.core_monitoring.LazyClassProperty"></a>

## LazyClassProperty

<a id="data_framework.modules.monitoring.core_monitoring.MonitoringInterface"></a>

## MonitoringInterface

<a id="data_framework.modules.monitoring.core_monitoring.Metric"></a>

## Metric

<a id="data_framework.modules.monitoring.core_monitoring.MetricNames"></a>

## MetricNames

<a id="data_framework.modules.monitoring.core_monitoring.Union"></a>

## Union

<a id="data_framework.modules.monitoring.core_monitoring.CoreMonitoring"></a>

## CoreMonitoring Objects

```python
class CoreMonitoring()
```

<a id="data_framework.modules.monitoring.core_monitoring.CoreMonitoring._monitoring"></a>

#### \_monitoring

```python
@LazyClassProperty
def _monitoring(cls) -> MonitoringInterface
```

<a id="data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_metric"></a>

#### track\_metric

```python
@classmethod
def track_metric(cls, metric: Metric)
```

<a id="data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_table_metric"></a>

#### track\_table\_metric

```python
@classmethod
def track_table_metric(cls, name: MetricNames, database: str, table: str,
                       value: float)
```

<a id="data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_process_metric"></a>

#### track\_process\_metric

```python
@classmethod
def track_process_metric(cls,
                         name: MetricNames,
                         value: float,
                         success: bool = None)
```

<a id="data_framework.modules.monitoring.core_monitoring.CoreMonitoring.track_computation_metric"></a>

#### track\_computation\_metric

```python
@classmethod
def track_computation_metric(cls, name: MetricNames, value: float)
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring"></a>

# data\_framework.modules.monitoring.integrations.aws\_cloudwatch.aws\_cloudwatch\_monitoring

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.MonitoringInterface"></a>

## MonitoringInterface

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Metric"></a>

## Metric

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.MetricNames"></a>

## MetricNames

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.MetricUnits"></a>

## MetricUnits

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Union"></a>

## Union

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Optional"></a>

## Optional

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.List"></a>

## List

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Dict"></a>

## Dict

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.Any"></a>

## Any

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.dataclass"></a>

## dataclass

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.config"></a>

## config

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.logger"></a>

## logger

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.datetime"></a>

## datetime

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.boto3"></a>

## boto3

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.os"></a>

## os

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.cloudwatch"></a>

## cloudwatch

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.BaseClient"></a>

## BaseClient

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.InternalMetric"></a>

## InternalMetric Objects

```python
@dataclass
class InternalMetric(Metric)
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.InternalMetric.parse"></a>

#### parse

```python
@property
def parse() -> Dict
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch"></a>

## AWSCloudWatch Objects

```python
class AWSCloudWatch(MonitoringInterface)
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__namespace"></a>

#### \_\_namespace

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__computation_dimensions"></a>

#### \_\_computation\_dimensions

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__process_dimensions"></a>

#### \_\_process\_dimensions

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__table_dimensions"></a>

#### \_\_table\_dimensions

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.namespace"></a>

#### namespace

```python
@property
def namespace() -> str
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.client"></a>

#### client

```python
@property
def client() -> BaseClient
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_metric"></a>

#### track\_metric

```python
def track_metric(metric: InternalMetric)
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_table_metric"></a>

#### track\_table\_metric

```python
def track_table_metric(name: MetricNames, database: str, table: str,
                       value: float)
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_process_metric"></a>

#### track\_process\_metric

```python
def track_process_metric(name: MetricNames,
                         value: float,
                         success: bool = None)
```

<a id="data_framework.modules.monitoring.integrations.aws_cloudwatch.aws_cloudwatch_monitoring.AWSCloudWatch.track_computation_metric"></a>

#### track\_computation\_metric

```python
def track_computation_metric(name: MetricNames, value: float)
```

<a id="data_framework.modules.monitoring.interface_monitoring"></a>

# data\_framework.modules.monitoring.interface\_monitoring

<a id="data_framework.modules.monitoring.interface_monitoring.ABC"></a>

## ABC

<a id="data_framework.modules.monitoring.interface_monitoring.abstractmethod"></a>

## abstractmethod

<a id="data_framework.modules.monitoring.interface_monitoring.Union"></a>

## Union

<a id="data_framework.modules.monitoring.interface_monitoring.Optional"></a>

## Optional

<a id="data_framework.modules.monitoring.interface_monitoring.List"></a>

## List

<a id="data_framework.modules.monitoring.interface_monitoring.Dict"></a>

## Dict

<a id="data_framework.modules.monitoring.interface_monitoring.Any"></a>

## Any

<a id="data_framework.modules.monitoring.interface_monitoring.dataclass"></a>

## dataclass

<a id="data_framework.modules.monitoring.interface_monitoring.field"></a>

## field

<a id="data_framework.modules.monitoring.interface_monitoring.fields"></a>

## fields

<a id="data_framework.modules.monitoring.interface_monitoring.Enum"></a>

## Enum

<a id="data_framework.modules.monitoring.interface_monitoring.time"></a>

## time

<a id="data_framework.modules.monitoring.interface_monitoring.config"></a>

## config

<a id="data_framework.modules.monitoring.interface_monitoring.os"></a>

## os

<a id="data_framework.modules.monitoring.interface_monitoring.MetricUnits"></a>

## MetricUnits Objects

```python
class MetricUnits(Enum)
```

<a id="data_framework.modules.monitoring.interface_monitoring.MetricUnits.SECONDS"></a>

#### SECONDS

<a id="data_framework.modules.monitoring.interface_monitoring.MetricUnits.MEGABYTES"></a>

#### MEGABYTES

<a id="data_framework.modules.monitoring.interface_monitoring.MetricUnits.COUNT"></a>

#### COUNT

<a id="data_framework.modules.monitoring.interface_monitoring.MetricUnits.PERCENT"></a>

#### PERCENT

<a id="data_framework.modules.monitoring.interface_monitoring.MetricUnits.NONE"></a>

#### NONE

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames"></a>

## MetricNames Objects

```python
class MetricNames(Enum)
```

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.UNKNOWN"></a>

#### UNKNOWN

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_READ_RECORDS"></a>

#### TABLE\_READ\_RECORDS

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_READ_SIZE"></a>

#### TABLE\_READ\_SIZE

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_ADDED_RECORDS"></a>

#### TABLE\_WRITE\_ADDED\_RECORDS

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_ADDED_SIZE"></a>

#### TABLE\_WRITE\_ADDED\_SIZE

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_DELETED_RECORDS"></a>

#### TABLE\_WRITE\_DELETED\_RECORDS

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_DELETED_SIZE"></a>

#### TABLE\_WRITE\_DELETED\_SIZE

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_TOTAL_RECORDS"></a>

#### TABLE\_WRITE\_TOTAL\_RECORDS

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.TABLE_WRITE_TOTAL_SIZE"></a>

#### TABLE\_WRITE\_TOTAL\_SIZE

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.DATAFLOW_START_EVENT"></a>

#### DATAFLOW\_START\_EVENT

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.DATAFLOW_END_EVENT"></a>

#### DATAFLOW\_END\_EVENT

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.PROCESS_START_EVENT"></a>

#### PROCESS\_START\_EVENT

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.PROCESS_END_EVENT"></a>

#### PROCESS\_END\_EVENT

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.PROCESS_DURATION"></a>

#### PROCESS\_DURATION

<a id="data_framework.modules.monitoring.interface_monitoring.MetricNames.unit"></a>

#### unit

```python
@property
def unit() -> MetricUnits
```

<a id="data_framework.modules.monitoring.interface_monitoring.Metric"></a>

## Metric Objects

```python
@dataclass
class Metric()
```

<a id="data_framework.modules.monitoring.interface_monitoring.Metric._created_at"></a>

#### \_created\_at

<a id="data_framework.modules.monitoring.interface_monitoring.Metric.name"></a>

#### name

<a id="data_framework.modules.monitoring.interface_monitoring.Metric.value"></a>

#### value

<a id="data_framework.modules.monitoring.interface_monitoring.Metric.dimensions"></a>

#### dimensions

<a id="data_framework.modules.monitoring.interface_monitoring.MonitoringInterface"></a>

## MonitoringInterface Objects

```python
class MonitoringInterface(ABC)
```

<a id="data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_metric"></a>

#### track\_metric

```python
@abstractmethod
def track_metric(metric: Metric)
```

<a id="data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_table_metric"></a>

#### track\_table\_metric

```python
@abstractmethod
def track_table_metric(name: MetricNames, database: str, table: str,
                       value: float)
```

<a id="data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_process_metric"></a>

#### track\_process\_metric

```python
@abstractmethod
def track_process_metric(name: MetricNames,
                         value: float,
                         success: bool = None)
```

<a id="data_framework.modules.monitoring.interface_monitoring.MonitoringInterface.track_computation_metric"></a>

#### track\_computation\_metric

```python
@abstractmethod
def track_computation_metric(name: MetricNames, value: float)
```

<a id="data_framework.modules.notification.core_notifications"></a>

# data\_framework.modules.notification.core\_notifications

<a id="data_framework.modules.notification.core_notifications.LazyClassProperty"></a>

## LazyClassProperty

<a id="data_framework.modules.notification.core_notifications.InterfaceNotifications"></a>

## InterfaceNotifications

<a id="data_framework.modules.notification.core_notifications.Notification"></a>

## Notification

<a id="data_framework.modules.notification.core_notifications.Any"></a>

## Any

<a id="data_framework.modules.notification.core_notifications.Dict"></a>

## Dict

<a id="data_framework.modules.notification.core_notifications.List"></a>

## List

<a id="data_framework.modules.notification.core_notifications.CoreNotifications"></a>

## CoreNotifications Objects

```python
class CoreNotifications(object)
```

<a id="data_framework.modules.notification.core_notifications.CoreNotifications._notifications"></a>

#### \_notifications

```python
@LazyClassProperty
def _notifications(cls) -> InterfaceNotifications
```

<a id="data_framework.modules.notification.core_notifications.CoreNotifications.send_notification"></a>

#### send\_notification

```python
@classmethod
def send_notification(cls, notification_name: str,
                      arguments: Dict[str, Any]) -> None
```

<a id="data_framework.modules.notification.core_notifications.CoreNotifications.get_notifications_to_send"></a>

#### get\_notifications\_to\_send

```python
@classmethod
def get_notifications_to_send(cls) -> List[Notification]
```

<a id="data_framework.modules.notification.interface_notifications"></a>

# data\_framework.modules.notification.interface\_notifications

<a id="data_framework.modules.notification.interface_notifications.NotificationNotFoundError"></a>

## NotificationNotFoundError

<a id="data_framework.modules.notification.interface_notifications.ABC"></a>

## ABC

<a id="data_framework.modules.notification.interface_notifications.abstractmethod"></a>

## abstractmethod

<a id="data_framework.modules.notification.interface_notifications.Any"></a>

## Any

<a id="data_framework.modules.notification.interface_notifications.Dict"></a>

## Dict

<a id="data_framework.modules.notification.interface_notifications.List"></a>

## List

<a id="data_framework.modules.notification.interface_notifications.Union"></a>

## Union

<a id="data_framework.modules.notification.interface_notifications.dataclass"></a>

## dataclass

<a id="data_framework.modules.notification.interface_notifications.field"></a>

## field

<a id="data_framework.modules.notification.interface_notifications.Enum"></a>

## Enum

<a id="data_framework.modules.notification.interface_notifications.NotificationType"></a>

## NotificationType Objects

```python
class NotificationType(Enum)
```

<a id="data_framework.modules.notification.interface_notifications.NotificationType.EMAIL"></a>

#### EMAIL

<a id="data_framework.modules.notification.interface_notifications.Topic"></a>

## Topic Objects

```python
class Topic(Enum)
```

<a id="data_framework.modules.notification.interface_notifications.Topic.INTERNAL"></a>

#### INTERNAL

<a id="data_framework.modules.notification.interface_notifications.Topic.EXTERNAL"></a>

#### EXTERNAL

<a id="data_framework.modules.notification.interface_notifications.Notification"></a>

## Notification Objects

```python
@dataclass
class Notification()
```

<a id="data_framework.modules.notification.interface_notifications.Notification.type"></a>

#### type

<a id="data_framework.modules.notification.interface_notifications.Notification.topics"></a>

#### topics

<a id="data_framework.modules.notification.interface_notifications.Notification.subject"></a>

#### subject

<a id="data_framework.modules.notification.interface_notifications.Notification.body"></a>

#### body

<a id="data_framework.modules.notification.interface_notifications.Notification.active"></a>

#### active

<a id="data_framework.modules.notification.interface_notifications.NotificationToSend"></a>

## NotificationToSend Objects

```python
@dataclass
class NotificationToSend()
```

<a id="data_framework.modules.notification.interface_notifications.NotificationToSend.type"></a>

#### type

<a id="data_framework.modules.notification.interface_notifications.NotificationToSend.topics"></a>

#### topics

<a id="data_framework.modules.notification.interface_notifications.NotificationToSend.subject"></a>

#### subject

<a id="data_framework.modules.notification.interface_notifications.NotificationToSend.body"></a>

#### body

<a id="data_framework.modules.notification.interface_notifications.NotificationDict"></a>

## NotificationDict Objects

```python
@dataclass
class NotificationDict()
```

<a id="data_framework.modules.notification.interface_notifications.NotificationDict.notifications"></a>

#### notifications

<a id="data_framework.modules.notification.interface_notifications.NotificationDict.get_notification"></a>

#### get\_notification

```python
def get_notification(notification_name: str) -> Union[Notification, None]
```

<a id="data_framework.modules.notification.interface_notifications.NotificationsParameters"></a>

## NotificationsParameters Objects

```python
@dataclass
class NotificationsParameters()
```

<a id="data_framework.modules.notification.interface_notifications.NotificationsParameters.max_subject_length"></a>

#### max\_subject\_length

<a id="data_framework.modules.notification.interface_notifications.NotificationsParameters.max_body_length"></a>

#### max\_body\_length

<a id="data_framework.modules.notification.interface_notifications.NotificationsParameters.max_number_of_notifications"></a>

#### max\_number\_of\_notifications

<a id="data_framework.modules.notification.interface_notifications.NotificationsParameters.signature"></a>

#### signature

<a id="data_framework.modules.notification.interface_notifications.DataFrameworkNotifications"></a>

## DataFrameworkNotifications Objects

```python
@dataclass
class DataFrameworkNotifications()
```

<a id="data_framework.modules.notification.interface_notifications.DataFrameworkNotifications.notifications"></a>

#### notifications

<a id="data_framework.modules.notification.interface_notifications.DataFrameworkNotifications.parameters"></a>

#### parameters

<a id="data_framework.modules.notification.interface_notifications.InterfaceNotifications"></a>

## InterfaceNotifications Objects

```python
class InterfaceNotifications(ABC)
```

<a id="data_framework.modules.notification.interface_notifications.InterfaceNotifications.send_notification"></a>

#### send\_notification

```python
@abstractmethod
def send_notification(notification_name: str, arguments: Dict[str,
                                                              Any]) -> None
```

<a id="data_framework.modules.notification.interface_notifications.InterfaceNotifications.get_notifications_to_send"></a>

#### get\_notifications\_to\_send

```python
@abstractmethod
def get_notifications_to_send() -> List[Notification]
```

<a id="data_framework.modules.notification.notifications"></a>

# data\_framework.modules.notification.notifications

<a id="data_framework.modules.notification.notifications.config"></a>

## config

<a id="data_framework.modules.notification.notifications.logger"></a>

## logger

<a id="data_framework.modules.notification.notifications.Environment"></a>

## Environment

<a id="data_framework.modules.notification.notifications.ExecutionMode"></a>

## ExecutionMode

<a id="data_framework.modules.notification.notifications.InterfaceNotifications"></a>

## InterfaceNotifications

<a id="data_framework.modules.notification.notifications.NotificationDict"></a>

## NotificationDict

<a id="data_framework.modules.notification.notifications.NotificationType"></a>

## NotificationType

<a id="data_framework.modules.notification.notifications.NotificationToSend"></a>

## NotificationToSend

<a id="data_framework.modules.notification.notifications.Notification"></a>

## Notification

<a id="data_framework.modules.notification.notifications.DuplicatedNotificationError"></a>

## DuplicatedNotificationError

<a id="data_framework.modules.notification.notifications.NotificationError"></a>

## NotificationError

<a id="data_framework.modules.notification.notifications.NotificationLimitExceededError"></a>

## NotificationLimitExceededError

<a id="data_framework.modules.notification.notifications.SubjectLimitExceededError"></a>

## SubjectLimitExceededError

<a id="data_framework.modules.notification.notifications.BodyLimitExceededError"></a>

## BodyLimitExceededError

<a id="data_framework.modules.notification.notifications.Dict"></a>

## Dict

<a id="data_framework.modules.notification.notifications.Any"></a>

## Any

<a id="data_framework.modules.notification.notifications.List"></a>

## List

<a id="data_framework.modules.notification.notifications.Notifications"></a>

## Notifications Objects

```python
class Notifications(InterfaceNotifications)
```

<a id="data_framework.modules.notification.notifications.Notifications.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.notification.notifications.Notifications._combine_notifications"></a>

#### \_combine\_notifications

```python
def _combine_notifications() -> NotificationDict
```

<a id="data_framework.modules.notification.notifications.Notifications.send_notification"></a>

#### send\_notification

```python
def send_notification(notification_name: str, arguments: Dict[str,
                                                              Any]) -> None
```

<a id="data_framework.modules.notification.notifications.Notifications._send_email_notification"></a>

#### \_send\_email\_notification

```python
def _send_email_notification(notification: Notification,
                             notification_name: str,
                             arguments: Dict[str, Any]) -> None
```

<a id="data_framework.modules.notification.notifications.Notifications._format_subject"></a>

#### \_format\_subject

```python
def _format_subject(subject: str, arguments: Dict[str, Any]) -> str
```

<a id="data_framework.modules.notification.notifications.Notifications._format_body"></a>

#### \_format\_body

```python
def _format_body(body: str, arguments: dict) -> str
```

<a id="data_framework.modules.notification.notifications.Notifications._validate_subject_length"></a>

#### \_validate\_subject\_length

```python
def _validate_subject_length(subject: str, notification_name: str) -> None
```

<a id="data_framework.modules.notification.notifications.Notifications._validate_body_length"></a>

#### \_validate\_body\_length

```python
def _validate_body_length(body: str, notification_name: str) -> None
```

<a id="data_framework.modules.notification.notifications.Notifications._add_notification"></a>

#### \_add\_notification

```python
def _add_notification(notification_to_send: NotificationToSend) -> None
```

<a id="data_framework.modules.notification.notifications.Notifications.get_notifications_to_send"></a>

#### get\_notifications\_to\_send

```python
def get_notifications_to_send() -> List[NotificationToSend]
```

<a id="data_framework.modules.storage.core_storage"></a>

# data\_framework.modules.storage.core\_storage

<a id="data_framework.modules.storage.core_storage.config"></a>

## config

<a id="data_framework.modules.storage.core_storage.Environment"></a>

## Environment

<a id="data_framework.modules.storage.core_storage.LazyClassProperty"></a>

## LazyClassProperty

<a id="data_framework.modules.storage.core_storage.CoreStorageInterface"></a>

## CoreStorageInterface

<a id="data_framework.modules.storage.core_storage.Database"></a>

## Database

<a id="data_framework.modules.storage.core_storage.Layer"></a>

## Layer

<a id="data_framework.modules.storage.core_storage.ReadResponse"></a>

## ReadResponse

<a id="data_framework.modules.storage.core_storage.WriteResponse"></a>

## WriteResponse

<a id="data_framework.modules.storage.core_storage.ListResponse"></a>

## ListResponse

<a id="data_framework.modules.storage.core_storage.PathResponse"></a>

## PathResponse

<a id="data_framework.modules.storage.core_storage.Storage"></a>

## Storage Objects

```python
class Storage()
```

<a id="data_framework.modules.storage.core_storage.Storage._storage"></a>

#### \_storage

```python
@LazyClassProperty
def _storage(cls) -> CoreStorageInterface
```

<a id="data_framework.modules.storage.core_storage.Storage.read"></a>

#### read

```python
@classmethod
def read(cls, layer: Layer, key_path: str, bucket: str = None) -> ReadResponse
```

<a id="data_framework.modules.storage.core_storage.Storage.write"></a>

#### write

```python
@classmethod
def write(cls, layer: Layer, database: Database, table: str, data: bytes,
          partitions: str, filename: str) -> WriteResponse
```

<a id="data_framework.modules.storage.core_storage.Storage.write_to_path"></a>

#### write\_to\_path

```python
@classmethod
def write_to_path(cls, layer: Layer, key_path: str,
                  data: bytes) -> WriteResponse
```

<a id="data_framework.modules.storage.core_storage.Storage.list_files"></a>

#### list\_files

```python
@classmethod
def list_files(cls, layer: Layer, prefix: str) -> ListResponse
```

<a id="data_framework.modules.storage.core_storage.Storage.raw_layer_path"></a>

#### raw\_layer\_path

```python
@classmethod
def raw_layer_path(cls, database: Database, table_name: str) -> PathResponse
```

<a id="data_framework.modules.storage.core_storage.Storage.base_layer_path"></a>

#### base\_layer\_path

```python
@classmethod
def base_layer_path(cls, layer: Layer) -> PathResponse
```

<a id="data_framework.modules.storage.integrations.local_storage"></a>

# data\_framework.modules.storage.integrations.local\_storage

<a id="data_framework.modules.storage.integrations.local_storage.logger"></a>

## logger

<a id="data_framework.modules.storage.integrations.local_storage.config"></a>

## config

<a id="data_framework.modules.storage.integrations.local_storage.CoreStorageInterface"></a>

## CoreStorageInterface

<a id="data_framework.modules.storage.integrations.local_storage.Database"></a>

## Database

<a id="data_framework.modules.storage.integrations.local_storage.Layer"></a>

## Layer

<a id="data_framework.modules.storage.integrations.local_storage.ReadResponse"></a>

## ReadResponse

<a id="data_framework.modules.storage.integrations.local_storage.WriteResponse"></a>

## WriteResponse

<a id="data_framework.modules.storage.integrations.local_storage.ListResponse"></a>

## ListResponse

<a id="data_framework.modules.storage.integrations.local_storage.PathResponse"></a>

## PathResponse

<a id="data_framework.modules.storage.integrations.local_storage.StorageError"></a>

## StorageError

<a id="data_framework.modules.storage.integrations.local_storage.StorageReadError"></a>

## StorageReadError

<a id="data_framework.modules.storage.integrations.local_storage.StorageWriteError"></a>

## StorageWriteError

<a id="data_framework.modules.storage.integrations.local_storage.os"></a>

## os

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage"></a>

## LocalStorage Objects

```python
class LocalStorage(CoreStorageInterface)
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.read"></a>

#### read

```python
def read(layer: Layer, key_path: str, bucket: str = None) -> ReadResponse
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage._build_folder_name"></a>

#### \_build\_folder\_name

```python
def _build_folder_name(layer: Layer) -> str
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage._build_file_path"></a>

#### \_build\_file\_path

```python
def _build_file_path(database: Database,
                     table: str,
                     partitions: dict = {},
                     filename: str = '') -> str
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.write"></a>

#### write

```python
def write(layer: Layer,
          database: Database,
          table: str,
          data: bytes,
          filename: str,
          partitions: dict = None) -> WriteResponse
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.write_to_path"></a>

#### write\_to\_path

```python
def write_to_path(layer: Layer, key_path: str, data: bytes) -> WriteResponse
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.list_files"></a>

#### list\_files

```python
def list_files(layer: Layer, prefix: str) -> ListResponse
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.raw_layer_path"></a>

#### raw\_layer\_path

```python
def raw_layer_path(database: Database, table_name: str,
                   data_date: str) -> PathResponse
```

<a id="data_framework.modules.storage.integrations.local_storage.LocalStorage.base_layer_path"></a>

#### base\_layer\_path

```python
def base_layer_path(layer: Layer) -> PathResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage"></a>

# data\_framework.modules.storage.integrations.s3\_storage

<a id="data_framework.modules.storage.integrations.s3_storage.boto3"></a>

## boto3

<a id="data_framework.modules.storage.integrations.s3_storage.logger"></a>

## logger

<a id="data_framework.modules.storage.integrations.s3_storage.config"></a>

## config

<a id="data_framework.modules.storage.integrations.s3_storage.CoreStorageInterface"></a>

## CoreStorageInterface

<a id="data_framework.modules.storage.integrations.s3_storage.Database"></a>

## Database

<a id="data_framework.modules.storage.integrations.s3_storage.Layer"></a>

## Layer

<a id="data_framework.modules.storage.integrations.s3_storage.ReadResponse"></a>

## ReadResponse

<a id="data_framework.modules.storage.integrations.s3_storage.WriteResponse"></a>

## WriteResponse

<a id="data_framework.modules.storage.integrations.s3_storage.ListResponse"></a>

## ListResponse

<a id="data_framework.modules.storage.integrations.s3_storage.PathResponse"></a>

## PathResponse

<a id="data_framework.modules.storage.integrations.s3_storage.S3Error"></a>

## S3Error

<a id="data_framework.modules.storage.integrations.s3_storage.StorageError"></a>

## StorageError

<a id="data_framework.modules.storage.integrations.s3_storage.StorageReadError"></a>

## StorageReadError

<a id="data_framework.modules.storage.integrations.s3_storage.StorageWriteError"></a>

## StorageWriteError

<a id="data_framework.modules.storage.integrations.s3_storage.ExecutionMode"></a>

## ExecutionMode

<a id="data_framework.modules.storage.integrations.s3_storage.NamedTuple"></a>

## NamedTuple

<a id="data_framework.modules.storage.integrations.s3_storage.BuildPathResponse"></a>

## BuildPathResponse Objects

```python
class BuildPathResponse(NamedTuple)
```

<a id="data_framework.modules.storage.integrations.s3_storage.BuildPathResponse.base_path"></a>

#### base\_path

<a id="data_framework.modules.storage.integrations.s3_storage.BuildPathResponse.data_path"></a>

#### data\_path

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage"></a>

## S3Storage Objects

```python
class S3Storage(CoreStorageInterface)
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.read"></a>

#### read

```python
def read(layer: Layer, key_path: str, bucket: str = None) -> ReadResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage._build_s3_bucket_name"></a>

#### \_build\_s3\_bucket\_name

```python
def _build_s3_bucket_name(layer: Layer) -> str
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage._build_s3_key_path"></a>

#### \_build\_s3\_key\_path

```python
def _build_s3_key_path(database: Database,
                       table: str,
                       partitions: dict = {},
                       filename: str = '') -> BuildPathResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.write"></a>

#### write

```python
def write(layer: Layer,
          database: Database,
          table: str,
          data: bytes,
          filename: str,
          partitions: dict = None) -> WriteResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.write_to_path"></a>

#### write\_to\_path

```python
def write_to_path(layer: Layer, key_path: str, data: bytes) -> WriteResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.list_files"></a>

#### list\_files

```python
def list_files(layer: Layer, prefix: str) -> ListResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.raw_layer_path"></a>

#### raw\_layer\_path

```python
def raw_layer_path(database: Database, table_name: str) -> PathResponse
```

<a id="data_framework.modules.storage.integrations.s3_storage.S3Storage.base_layer_path"></a>

#### base\_layer\_path

```python
def base_layer_path(layer: Layer) -> PathResponse
```

<a id="data_framework.modules.storage.interface_storage"></a>

# data\_framework.modules.storage.interface\_storage

<a id="data_framework.modules.storage.interface_storage.ABC"></a>

## ABC

<a id="data_framework.modules.storage.interface_storage.abstractmethod"></a>

## abstractmethod

<a id="data_framework.modules.storage.interface_storage.Enum"></a>

## Enum

<a id="data_framework.modules.storage.interface_storage.dataclass"></a>

## dataclass

<a id="data_framework.modules.storage.interface_storage.Layer"></a>

## Layer Objects

```python
class Layer(Enum)
```

<a id="data_framework.modules.storage.interface_storage.Layer.LANDING"></a>

#### LANDING

<a id="data_framework.modules.storage.interface_storage.Layer.RAW"></a>

#### RAW

<a id="data_framework.modules.storage.interface_storage.Layer.STAGING"></a>

#### STAGING

<a id="data_framework.modules.storage.interface_storage.Layer.COMMON"></a>

#### COMMON

<a id="data_framework.modules.storage.interface_storage.Layer.BUSINESS"></a>

#### BUSINESS

<a id="data_framework.modules.storage.interface_storage.Layer.OUTPUT"></a>

#### OUTPUT

<a id="data_framework.modules.storage.interface_storage.Layer.ATHENA"></a>

#### ATHENA

<a id="data_framework.modules.storage.interface_storage.Layer.TEMP"></a>

#### TEMP

<a id="data_framework.modules.storage.interface_storage.Database"></a>

## Database Objects

```python
class Database(Enum)
```

<a id="data_framework.modules.storage.interface_storage.Database.FUNDS_RAW"></a>

#### FUNDS\_RAW

<a id="data_framework.modules.storage.interface_storage.Database.FUNDS_STAGING"></a>

#### FUNDS\_STAGING

<a id="data_framework.modules.storage.interface_storage.Database.FUNDS_COMMON"></a>

#### FUNDS\_COMMON

<a id="data_framework.modules.storage.interface_storage.Database.FUNDS_BUSINESS"></a>

#### FUNDS\_BUSINESS

<a id="data_framework.modules.storage.interface_storage.Database.DATA_QUALITY"></a>

#### DATA\_QUALITY

<a id="data_framework.modules.storage.interface_storage.Database.CONFIG_SCHEMAS"></a>

#### CONFIG\_SCHEMAS

<a id="data_framework.modules.storage.interface_storage.Database.INFINITY_STAGING"></a>

#### INFINITY\_STAGING

<a id="data_framework.modules.storage.interface_storage.Database.INFINITY_COMMON"></a>

#### INFINITY\_COMMON

<a id="data_framework.modules.storage.interface_storage.Database.INFINITY_BUSINESS"></a>

#### INFINITY\_BUSINESS

<a id="data_framework.modules.storage.interface_storage.Database.SAM_STG"></a>

#### SAM\_STG

<a id="data_framework.modules.storage.interface_storage.Database.SAM_DWH"></a>

#### SAM\_DWH

<a id="data_framework.modules.storage.interface_storage.ReadResponse"></a>

## ReadResponse Objects

```python
@dataclass
class ReadResponse()
```

<a id="data_framework.modules.storage.interface_storage.ReadResponse.success"></a>

#### success

<a id="data_framework.modules.storage.interface_storage.ReadResponse.error"></a>

#### error

<a id="data_framework.modules.storage.interface_storage.ReadResponse.data"></a>

#### data

<a id="data_framework.modules.storage.interface_storage.WriteResponse"></a>

## WriteResponse Objects

```python
@dataclass
class WriteResponse()
```

<a id="data_framework.modules.storage.interface_storage.WriteResponse.success"></a>

#### success

<a id="data_framework.modules.storage.interface_storage.WriteResponse.error"></a>

#### error

<a id="data_framework.modules.storage.interface_storage.ListResponse"></a>

## ListResponse Objects

```python
@dataclass
class ListResponse()
```

<a id="data_framework.modules.storage.interface_storage.ListResponse.success"></a>

#### success

<a id="data_framework.modules.storage.interface_storage.ListResponse.error"></a>

#### error

<a id="data_framework.modules.storage.interface_storage.ListResponse.result"></a>

#### result

<a id="data_framework.modules.storage.interface_storage.PathResponse"></a>

## PathResponse Objects

```python
@dataclass
class PathResponse()
```

<a id="data_framework.modules.storage.interface_storage.PathResponse.success"></a>

#### success

<a id="data_framework.modules.storage.interface_storage.PathResponse.error"></a>

#### error

<a id="data_framework.modules.storage.interface_storage.PathResponse.bucket"></a>

#### bucket

<a id="data_framework.modules.storage.interface_storage.PathResponse.path"></a>

#### path

<a id="data_framework.modules.storage.interface_storage.PathResponse.base_path"></a>

#### base\_path

<a id="data_framework.modules.storage.interface_storage.PathResponse.relative_path"></a>

#### relative\_path

<a id="data_framework.modules.storage.interface_storage.PathResponse.relative_base_path"></a>

#### relative\_base\_path

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface"></a>

## CoreStorageInterface Objects

```python
class CoreStorageInterface(ABC)
```

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface.read"></a>

#### read

```python
@abstractmethod
def read(layer: Layer, key_path: str, bucket: str = None) -> ReadResponse
```

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface.write"></a>

#### write

```python
@abstractmethod
def write(layer: Layer, database: Database, table: str, data: bytes,
          partitions: str, filename: str) -> WriteResponse
```

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface.write_to_path"></a>

#### write\_to\_path

```python
@abstractmethod
def write_to_path(layer: Layer, key_path: str, data: bytes) -> WriteResponse
```

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface.list_files"></a>

#### list\_files

```python
@abstractmethod
def list_files(layer: Layer, prefix: str) -> ListResponse
```

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface.raw_layer_path"></a>

#### raw\_layer\_path

```python
@abstractmethod
def raw_layer_path(database: Database, table_name: str) -> PathResponse
```

<a id="data_framework.modules.storage.interface_storage.CoreStorageInterface.base_layer_path"></a>

#### base\_layer\_path

```python
@abstractmethod
def base_layer_path(layer: Layer) -> PathResponse
```

<a id="data_framework.modules.utils.debug"></a>

# data\_framework.modules.utils.debug

<a id="data_framework.modules.utils.debug.config"></a>

## config

<a id="data_framework.modules.utils.debug.Environment"></a>

## Environment

<a id="data_framework.modules.utils.debug.debug_code"></a>

#### debug\_code

<a id="data_framework.modules.utils.logger"></a>

# data\_framework.modules.utils.logger

<a id="data_framework.modules.utils.logger.LoggerInitializationError"></a>

## LoggerInitializationError

<a id="data_framework.modules.utils.logger.logging"></a>

## logging

<a id="data_framework.modules.utils.logger.threading"></a>

## threading

<a id="data_framework.modules.utils.logger.sys"></a>

## sys

<a id="data_framework.modules.utils.logger.LOG_FORMAT"></a>

#### LOG\_FORMAT

<a id="data_framework.modules.utils.logger.DATE_FORMAT"></a>

#### DATE\_FORMAT

<a id="data_framework.modules.utils.logger.Logger"></a>

## Logger Objects

```python
class Logger()
```

<a id="data_framework.modules.utils.logger.Logger._instance"></a>

#### \_instance

<a id="data_framework.modules.utils.logger.Logger._lock"></a>

#### \_lock

<a id="data_framework.modules.utils.logger.Logger.__new__"></a>

#### \_\_new\_\_

```python
def __new__(cls)
```

<a id="data_framework.modules.utils.logger.logger"></a>

#### logger

<a id="data_framework.modules.utils.regex"></a>

# data\_framework.modules.utils.regex

<a id="data_framework.modules.utils.regex.re"></a>

## re

<a id="data_framework.modules.utils.regex.change_file_extension"></a>

#### change\_file\_extension

```python
def change_file_extension(filename: str, new_extension: str) -> str
```

<a id="data_framework.modules.validation.core_quality_controls"></a>

# data\_framework.modules.validation.core\_quality\_controls

<a id="data_framework.modules.validation.core_quality_controls.LazyClassProperty"></a>

## LazyClassProperty

<a id="data_framework.modules.validation.core_quality_controls.InterfaceQualityControls"></a>

## InterfaceQualityControls

<a id="data_framework.modules.validation.core_quality_controls.ControlsResponse"></a>

## ControlsResponse

<a id="data_framework.modules.validation.core_quality_controls.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.validation.core_quality_controls.Layer"></a>

## Layer

<a id="data_framework.modules.validation.core_quality_controls.Any"></a>

## Any

<a id="data_framework.modules.validation.core_quality_controls.Dict"></a>

## Dict

<a id="data_framework.modules.validation.core_quality_controls.CoreQualityControls"></a>

## CoreQualityControls Objects

```python
class CoreQualityControls(object)
```

<a id="data_framework.modules.validation.core_quality_controls.CoreQualityControls._quality_controls"></a>

#### \_quality\_controls

```python
@LazyClassProperty
def _quality_controls(cls) -> InterfaceQualityControls
```

<a id="data_framework.modules.validation.core_quality_controls.CoreQualityControls.validate"></a>

#### validate

```python
@classmethod
def validate(cls,
             layer: Layer,
             table_config: DatabaseTable,
             df_data: Any = None,
             **kwargs: Dict[str, Any]) -> ControlsResponse
```

<a id="data_framework.modules.validation.core_quality_controls.CoreQualityControls.set_parent"></a>

#### set\_parent

```python
@classmethod
def set_parent(cls, parent: Any) -> None
```

<a id="data_framework.modules.validation.integrations.file_validator"></a>

# data\_framework.modules.validation.integrations.file\_validator

<a id="data_framework.modules.validation.integrations.file_validator.config"></a>

## config

<a id="data_framework.modules.validation.integrations.file_validator.logger"></a>

## logger

<a id="data_framework.modules.validation.integrations.file_validator.CoreCatalogue"></a>

## CoreCatalogue

<a id="data_framework.modules.validation.integrations.file_validator.CoreQualityControls"></a>

## CoreQualityControls

<a id="data_framework.modules.validation.integrations.file_validator.CoreDataProcess"></a>

## CoreDataProcess

<a id="data_framework.modules.validation.integrations.file_validator.ControlRule"></a>

## ControlRule

<a id="data_framework.modules.validation.integrations.file_validator.re"></a>

## re

<a id="data_framework.modules.validation.integrations.file_validator.BytesIO"></a>

## BytesIO

<a id="data_framework.modules.validation.integrations.file_validator.Path"></a>

## Path

<a id="data_framework.modules.validation.integrations.file_validator.pd"></a>

## pd

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator"></a>

## FileValidator Objects

```python
class FileValidator()
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator.__init__"></a>

#### \_\_init\_\_

```python
def __init__(file_date: str, file_contents: dict, source_file_path: str)
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator.validate_filename_pattern"></a>

#### validate\_filename\_pattern

```python
def validate_filename_pattern(rule: ControlRule) -> None
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator.validate_unzipped_filename_pattern"></a>

#### validate\_unzipped\_filename\_pattern

```python
def validate_unzipped_filename_pattern(rule: ControlRule) -> None
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator.validate_csv_format"></a>

#### validate\_csv\_format

```python
def validate_csv_format(rule: ControlRule) -> None
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator.validate_csv_columns"></a>

#### validate\_csv\_columns

```python
def validate_csv_columns(rule: ControlRule) -> None
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator._get_expected_number_of_columns"></a>

#### \_get\_expected\_number\_of\_columns

```python
def _get_expected_number_of_columns(csv_content: BytesIO) -> int
```

<a id="data_framework.modules.validation.integrations.file_validator.FileValidator._parse_columns"></a>

#### \_parse\_columns

```python
def _parse_columns(df: pd.DataFrame) -> list
```

<a id="data_framework.modules.validation.integrations.quality_controls"></a>

# data\_framework.modules.validation.integrations.quality\_controls

<a id="data_framework.modules.validation.integrations.quality_controls.InterfaceQualityControls"></a>

## InterfaceQualityControls

<a id="data_framework.modules.validation.integrations.quality_controls.ControlsResponse"></a>

## ControlsResponse

<a id="data_framework.modules.validation.integrations.quality_controls.ControlsTable"></a>

## ControlsTable

<a id="data_framework.modules.validation.integrations.quality_controls.ControlRule"></a>

## ControlRule

<a id="data_framework.modules.validation.integrations.quality_controls.AlgorithmType"></a>

## AlgorithmType

<a id="data_framework.modules.validation.integrations.quality_controls.ControlLevel"></a>

## ControlLevel

<a id="data_framework.modules.validation.integrations.quality_controls.config"></a>

## config

<a id="data_framework.modules.validation.integrations.quality_controls.logger"></a>

## logger

<a id="data_framework.modules.validation.integrations.quality_controls.CoreDataProcess"></a>

## CoreDataProcess

<a id="data_framework.modules.validation.integrations.quality_controls.Technologies"></a>

## Technologies

<a id="data_framework.modules.validation.integrations.quality_controls.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.validation.integrations.quality_controls.Layer"></a>

## Layer

<a id="data_framework.modules.validation.integrations.quality_controls.debug_code"></a>

## debug\_code

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControlsError"></a>

## QualityControlsError

<a id="data_framework.modules.validation.integrations.quality_controls.FailedRulesError"></a>

## FailedRulesError

<a id="data_framework.modules.validation.integrations.quality_controls.ValidationFunctionNotFoundError"></a>

## ValidationFunctionNotFoundError

<a id="data_framework.modules.validation.integrations.quality_controls.ParentNotConfiguredError"></a>

## ParentNotConfiguredError

<a id="data_framework.modules.validation.integrations.quality_controls.RuleComputeError"></a>

## RuleComputeError

<a id="data_framework.modules.validation.integrations.quality_controls.Any"></a>

## Any

<a id="data_framework.modules.validation.integrations.quality_controls.Dict"></a>

## Dict

<a id="data_framework.modules.validation.integrations.quality_controls.pd"></a>

## pd

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls"></a>

## QualityControls Objects

```python
class QualityControls(InterfaceQualityControls)
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls.__init__"></a>

#### \_\_init\_\_

```python
def __init__()
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls.set_parent"></a>

#### set\_parent

```python
def set_parent(parent: Any) -> None
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls.validate"></a>

#### validate

```python
def validate(layer: Layer,
             table_config: DatabaseTable,
             df_data: Any = None,
             **kwargs: Dict[str, Any]) -> ControlsResponse
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._get_active_rules"></a>

#### \_get\_active\_rules

```python
def _get_active_rules(layer: Layer, table_config: DatabaseTable) -> Any
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._get_overall_result"></a>

#### \_get\_overall\_result

```python
def _get_overall_result(df_rules: Any, df_results: Any) -> bool
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._insert_results"></a>

#### \_insert\_results

```python
def _insert_results(df_results: Any) -> None
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_rules"></a>

#### \_compute\_rules

```python
def _compute_rules(df_data: Any, df_rules: Any, **kwargs: Dict[str,
                                                               Any]) -> Any
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_generic_rule"></a>

#### \_compute\_generic\_rule

```python
def _compute_generic_rule(rule_definition: pd.Series, df_data: Any,
                          **kwargs: Dict[str, Any]) -> pd.Series
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_sql_rule"></a>

#### \_compute\_sql\_rule

```python
def _compute_sql_rule(rule: ControlRule, **kwargs: Dict[str, Any]) -> None
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_python_rule"></a>

#### \_compute\_python\_rule

```python
def _compute_python_rule(rule: ControlRule, df_data: Any,
                         **kwargs: Dict[str, Any]) -> None
```

<a id="data_framework.modules.validation.integrations.quality_controls.QualityControls._compute_regex_rule"></a>

#### \_compute\_regex\_rule

```python
def _compute_regex_rule(rule: ControlRule, df_data: Any) -> None
```

<a id="data_framework.modules.validation.interface_quality_controls"></a>

# data\_framework.modules.validation.interface\_quality\_controls

<a id="data_framework.modules.validation.interface_quality_controls.DatabaseTable"></a>

## DatabaseTable

<a id="data_framework.modules.validation.interface_quality_controls.Database"></a>

## Database

<a id="data_framework.modules.validation.interface_quality_controls.Technologies"></a>

## Technologies

<a id="data_framework.modules.validation.interface_quality_controls.Platform"></a>

## Platform

<a id="data_framework.modules.validation.interface_quality_controls.Layer"></a>

## Layer

<a id="data_framework.modules.validation.interface_quality_controls.config"></a>

## config

<a id="data_framework.modules.validation.interface_quality_controls.InvalidThresholdError"></a>

## InvalidThresholdError

<a id="data_framework.modules.validation.interface_quality_controls.InvalidDataFrameError"></a>

## InvalidDataFrameError

<a id="data_framework.modules.validation.interface_quality_controls.InvalidAlgorithmError"></a>

## InvalidAlgorithmError

<a id="data_framework.modules.validation.interface_quality_controls.InvalidRuleError"></a>

## InvalidRuleError

<a id="data_framework.modules.validation.interface_quality_controls.ABC"></a>

## ABC

<a id="data_framework.modules.validation.interface_quality_controls.abstractmethod"></a>

## abstractmethod

<a id="data_framework.modules.validation.interface_quality_controls.dataclass"></a>

## dataclass

<a id="data_framework.modules.validation.interface_quality_controls.field"></a>

## field

<a id="data_framework.modules.validation.interface_quality_controls.Enum"></a>

## Enum

<a id="data_framework.modules.validation.interface_quality_controls.Any"></a>

## Any

<a id="data_framework.modules.validation.interface_quality_controls.Union"></a>

## Union

<a id="data_framework.modules.validation.interface_quality_controls.List"></a>

## List

<a id="data_framework.modules.validation.interface_quality_controls.Dict"></a>

## Dict

<a id="data_framework.modules.validation.interface_quality_controls.datetime"></a>

## datetime

<a id="data_framework.modules.validation.interface_quality_controls.date"></a>

## date

<a id="data_framework.modules.validation.interface_quality_controls.pd"></a>

## pd

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse"></a>

## ControlsResponse Objects

```python
@dataclass
class ControlsResponse()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.success"></a>

#### success

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.table"></a>

#### table

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.overall_result"></a>

#### overall\_result

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.error"></a>

#### error

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.data"></a>

#### data

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.rules"></a>

#### rules

<a id="data_framework.modules.validation.interface_quality_controls.ControlsResponse.results"></a>

#### results

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable"></a>

## ControlsTable Objects

```python
@dataclass
class ControlsTable()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable.table_config"></a>

#### table\_config

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable.mandatory_columns"></a>

#### mandatory\_columns

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable.master"></a>

#### master

```python
@classmethod
def master(cls) -> Any
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable.dataset"></a>

#### dataset

```python
@classmethod
def dataset(cls) -> Any
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable.results"></a>

#### results

```python
@classmethod
def results(cls) -> Any
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlsTable.filter_rules"></a>

#### filter\_rules

```python
@staticmethod
def filter_rules(layer: Layer, table_config: DatabaseTable) -> str
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlOutcome"></a>

## ControlOutcome Objects

```python
@dataclass
class ControlOutcome()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlOutcome.total"></a>

#### total

<a id="data_framework.modules.validation.interface_quality_controls.ControlOutcome.value"></a>

#### value

<a id="data_framework.modules.validation.interface_quality_controls.ControlOutcome.metric_value"></a>

#### metric\_value

```python
@property
def metric_value() -> Union[float, None]
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlOutcome.to_string"></a>

#### to\_string

```python
def to_string() -> str
```

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult"></a>

## ThresholdResult Objects

```python
@dataclass
class ThresholdResult()
```

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult.total_records"></a>

#### total\_records

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult.invalid_records"></a>

#### invalid\_records

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult.valid_identifiers"></a>

#### valid\_identifiers

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult.invalid_identifiers"></a>

#### invalid\_identifiers

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult.unique_valid_identifiers"></a>

#### unique\_valid\_identifiers

```python
@property
def unique_valid_identifiers() -> List[str]
```

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdResult.unique_invalid_identifiers"></a>

#### unique\_invalid\_identifiers

```python
@property
def unique_invalid_identifiers() -> List[str]
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult"></a>

## ControlResult Objects

```python
@dataclass
class ControlResult()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.master_id"></a>

#### master\_id

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.table_id"></a>

#### table\_id

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.outcome"></a>

#### outcome

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.detail"></a>

#### detail

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.initial_date"></a>

#### initial\_date

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.end_date"></a>

#### end\_date

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.data_date"></a>

#### data\_date

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.to_series"></a>

#### to\_series

```python
def to_series() -> pd.Series
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.set_data_date"></a>

#### set\_data\_date

```python
def set_data_date(custom_data_date: datetime = None) -> None
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.add_detail"></a>

#### add\_detail

```python
def add_detail(detail: str, separator: str = '. ') -> None
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlResult.fill"></a>

#### fill

```python
def fill(threshold_result: ThresholdResult) -> None
```

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdType"></a>

## ThresholdType Objects

```python
class ThresholdType(Enum)
```

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdType.STANDARD"></a>

#### STANDARD

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdType.ABSOLUTE"></a>

#### ABSOLUTE

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdType.BINARY"></a>

#### BINARY

<a id="data_framework.modules.validation.interface_quality_controls.ThresholdType.available_threshold_types"></a>

#### available\_threshold\_types

```python
@classmethod
def available_threshold_types(cls) -> List[str]
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold"></a>

## ControlThreshold Objects

```python
@dataclass
class ControlThreshold()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_type"></a>

#### threshold\_type

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_max"></a>

#### threshold\_max

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_min"></a>

#### threshold\_min

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_rag_min"></a>

#### threshold\_rag\_min

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.threshold_rag_max"></a>

#### threshold\_rag\_max

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.validate"></a>

#### validate

```python
def validate() -> None
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.apply_threshold"></a>

#### apply\_threshold

```python
def apply_threshold(df_result: Any) -> ThresholdResult
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.calculate_standard_threshold"></a>

#### calculate\_standard\_threshold

```python
def calculate_standard_threshold(df_result: Any) -> ThresholdResult
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.calculate_absolute_threshold"></a>

#### calculate\_absolute\_threshold

```python
def calculate_absolute_threshold(df_result: Any) -> ThresholdResult
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.calculate_binary_threshold"></a>

#### calculate\_binary\_threshold

```python
def calculate_binary_threshold(df_result: Any) -> ThresholdResult
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlThreshold.apply_threshold_limits"></a>

#### apply\_threshold\_limits

```python
def apply_threshold_limits(value: float) -> bool
```

<a id="data_framework.modules.validation.interface_quality_controls.AlgorithmType"></a>

## AlgorithmType Objects

```python
class AlgorithmType(Enum)
```

<a id="data_framework.modules.validation.interface_quality_controls.AlgorithmType.PYTHON"></a>

#### PYTHON

<a id="data_framework.modules.validation.interface_quality_controls.AlgorithmType.SQL"></a>

#### SQL

<a id="data_framework.modules.validation.interface_quality_controls.AlgorithmType.REGEX"></a>

#### REGEX

<a id="data_framework.modules.validation.interface_quality_controls.AlgorithmType.available_algorithm_types"></a>

#### available\_algorithm\_types

```python
@classmethod
def available_algorithm_types(cls) -> List[str]
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlLevel"></a>

## ControlLevel Objects

```python
class ControlLevel(Enum)
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlLevel.DATA"></a>

#### DATA

<a id="data_framework.modules.validation.interface_quality_controls.ControlLevel.FILE"></a>

#### FILE

<a id="data_framework.modules.validation.interface_quality_controls.ControlLevel.available_control_levels"></a>

#### available\_control\_levels

```python
@classmethod
def available_control_levels(cls) -> List[str]
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlAlgorithm"></a>

## ControlAlgorithm Objects

```python
@dataclass
class ControlAlgorithm()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlAlgorithm.algorithm_type"></a>

#### algorithm\_type

<a id="data_framework.modules.validation.interface_quality_controls.ControlAlgorithm.algorithm_description"></a>

#### algorithm\_description

<a id="data_framework.modules.validation.interface_quality_controls.ControlAlgorithm.validate"></a>

#### validate

```python
def validate() -> None
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule"></a>

## ControlRule Objects

```python
@dataclass
class ControlRule()
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.master_id"></a>

#### master\_id

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.table_id"></a>

#### table\_id

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.layer"></a>

#### layer

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.database_table"></a>

#### database\_table

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.field"></a>

#### field

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.rule_description"></a>

#### rule\_description

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.level"></a>

#### level

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.algorithm"></a>

#### algorithm

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.threshold"></a>

#### threshold

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.is_blocker"></a>

#### is\_blocker

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.result"></a>

#### result

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.id"></a>

#### id

```python
@property
def id() -> str
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.field_list"></a>

#### field\_list

```python
@property
def field_list() -> List[str]
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.from_series"></a>

#### from\_series

```python
@classmethod
def from_series(cls, rule: pd.Series) -> Any
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.validate"></a>

#### validate

```python
def validate() -> None
```

<a id="data_framework.modules.validation.interface_quality_controls.ControlRule.calculate_result"></a>

#### calculate\_result

```python
def calculate_result(df_result: Any) -> ThresholdResult
```

<a id="data_framework.modules.validation.interface_quality_controls.InterfaceQualityControls"></a>

## InterfaceQualityControls Objects

```python
class InterfaceQualityControls(ABC)
```

<a id="data_framework.modules.validation.interface_quality_controls.InterfaceQualityControls.validate"></a>

#### validate

```python
@abstractmethod
def validate(layer: Layer,
             table_config: DatabaseTable,
             df_data: Any = None,
             **kwargs: Dict[str, Any]) -> ControlsResponse
```

<a id="data_framework.modules.validation.interface_quality_controls.InterfaceQualityControls.set_parent"></a>

#### set\_parent

```python
@abstractmethod
def set_parent(parent: Any) -> None
```

<a id="data_framework.tests.test_utils"></a>

# data\_framework.tests.test\_utils

<a id="data_framework.tests.test_utils.pytest"></a>

## pytest

<a id="data_framework.tests.test_utils.Logger"></a>

## Logger

<a id="data_framework.tests.test_utils.test_logger"></a>

#### test\_logger

```python
def test_logger()
```

