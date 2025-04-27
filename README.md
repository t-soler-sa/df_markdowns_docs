# Data Platform Framework

## Overview

The **Data Platform Framework** is a modular Python package designed to orchestrate end-to-end data ingestion, transformation, validation, monitoring, and notification workflows.  
It is built to operate in cloud-native environments (AWS) and supports both Spark (EMR) and Pandas-based (Lambda/local) processing engines.  
The framework abstracts infrastructure complexity, providing a robust architecture for managing typical data engineering pipelines efficiently.

---

## Project Structure

```
setup.py
tools/
  └── init.py
src/
  ├── __init__.py
  └── data_framework/
      ├── __init__.py
      ├── dataflow/
      │   ├── __init__.py
      │   ├── business.py
      │   ├── common.py
      │   ├── landing.py
      │   ├── output.py
      │   └── staging.py
      ├── modules/
      │   ├── __init__.py
      │   ├── catalogue/
      │   │   ├── __init__.py
      │   │   ├── core_catalogue.py
      │   │   ├── interface_catalogue.py
      │   │   └── integrations/
      │   │       ├── __init__.py
      │   │       └── aws_glue.py
      │   ├── code/
      │   │   ├── __init__.py
      │   │   └── lazy_class_property.py
      │   ├── config/
      │   │   ├── __init__.py
      │   │   ├── core.py
      │   │   └── model/
      │   │       ├── __init__.py
      │   │       └── flows.py
      │   ├── data_process/
      │   │   ├── __init__.py
      │   │   ├── core_data_process.py
      │   │   ├── helpers/
      │   │   │   ├── __init__.py
      │   │   │   ├── athena.py
      │   │   │   └── cast.py
      │   │   ├── integrations/
      │   │   │   ├── __init__.py
      │   │   │   ├── pandas/
      │   │   │   │   └── pandas_data_process.py
      │   │   │   └── spark/
      │   │   │       ├── __init__.py
      │   │   │       ├── dynamic_config.py
      │   │   │       ├── spark_data_process.py
      │   │   │       ├── utils.py
      │   │   │       └── transformations/
      │   │   │           ├── __init__.py
      │   │   │           └── parse_dates.py
      │   │   └── interface_data_process.py
      │   ├── dataflow/
      │   │   ├── __init__.py
      │   │   └── interface_dataflow.py
      │   ├── exception/
      │   │   ├── __init__.py
      │   │   ├── aws_exceptions.py
      │   │   ├── catalogue_exceptions.py
      │   │   ├── config_exceptions.py
      │   │   ├── data_process_exceptions.py
      │   │   ├── dataflow_exceptions.py
      │   │   ├── generic_exceptions.py
      │   │   ├── landing_exceptions.py
      │   │   ├── notification_exceptions.py
      │   │   ├── output_exceptions.py
      │   │   ├── storage_exceptions.py
      │   │   └── validation_exceptions.py
      │   ├── monitoring/
      │   │   ├── core_monitoring.py
      │   │   ├── interface_monitoring.py
      │   │   └── integrations/
      │   │       └── aws_cloudwatch/
      │   │           └── aws_cloudwatch_monitoring.py
      │   ├── notification/
      │   │   ├── __init__.py
      │   │   ├── core_notifications.py
      │   │   ├── interface_notifications.py
      │   │   └── notifications.py
      │   ├── storage/
      │   │   ├── __init__.py
      │   │   ├── core_storage.py
      │   │   ├── interface_storage.py
      │   │   └── integrations/
      │   │       ├── local_storage.py
      │   │       └── s3_storage.py
      │   ├── utils/
      │   │   ├── __init__.py
      │   │   ├── debug.py
      │   │   ├── logger.py
      │   │   └── regex.py
      │   ├── validation/
      │   │   ├── __init__.py
      │   │   ├── core_quality_controls.py
      │   │   ├── interface_quality_controls.py
      │   │   └── integrations/
      │   │       ├── __init__.py
      │   │       ├── file_validator.py
      │   │       └── quality_controls.py
      │   └── tests/
      │       ├── __init__.py
      │       ├── resources/
      │       │   ├── __init__.py
      │       │   └── configs/
      │       │       └── __init__.py
      │       └── test_utils.py
  └── launchers/
      └── emr_launcher.py
```

---

## High-Level Architecture

- **`src/data_framework/dataflow/`**: Defines business processes ("Landing to Raw", "Raw to Staging", etc.)
- **`src/data_framework/modules/`**: Implements reusable modules like:
  - Catalogue integration (e.g., AWS Glue)
  - Data Processing (Spark, Pandas)
  - Configuration Management
  - Storage (S3, Local)
  - Monitoring (CloudWatch)
  - Notifications (Email)
  - Validation (Quality Controls)
- **`src/launchers/`**: Provides dynamic runtime for launching processes.
- **`tools/`**: Utilities (like adding missing `__init__.py` files).

---

## Main Components

### 1. Dataflow (`data_framework/dataflow`)
- **`ProcessingCoordinator`** classes implement the business logic for different pipeline stages:
  - Landing → Raw
  - Raw → Staging
  - Staging → Common
  - Business → Output
- Base class: `DataFlowInterface` provides shared methods (e.g., `read_table`, `write`, `save_monitorization`).

### 2. Modules (`data_framework/modules`)
#### Config
- Loads JSON-based configuration files.
- Parses CLI arguments or defaults.
- Supports dynamic environment and platform handling.

#### Catalogue
- Abstracts Glue Data Catalog interactions.
- Creates partitions and fetches table schemas.

#### Data Process
- Integrates Pandas (Lambda) and Spark (EMR) engines.
- Supports table reads, transformations, data casting, merging, and joins.

#### Storage
- Abstracts S3 and Local filesystem operations for reading/writing data.

#### Validation
- Provides automatic Data Quality Controls based on a metadata-driven rules engine.

#### Monitoring
- Sends metrics to AWS CloudWatch at table and process levels.

#### Notifications
- Manages email notifications for file arrivals, errors, and output generations.

### 3. Launchers (`launchers/emr_launcher.py`)
- Dynamically imports and executes the correct `ProcessingCoordinator` class based on CLI parameters.

---

## Patterns and Interfaces

- **Abstract Base Classes**: Defined for key interfaces like `DataProcessInterface`, `MonitoringInterface`, `InterfaceQualityControls`, ensuring loose coupling.
- **Lazy Initialization**: `LazyClassProperty` delays heavy imports (e.g., boto3, SparkSession) until needed.
- **Separation of Concerns**: Each module addresses one responsibility (e.g., storage, validation, notifications).
- **Exception Handling**: Custom, descriptive error classes for each subdomain (e.g., `StorageReadError`, `ConfigParseError`).

---

## Common Entry Points: How to Launch a Process

Typical execution flow:

```bash
python src/launchers/emr_launcher.py --dataflow your_dataflow_name --process landing_to_raw
```

The launcher will dynamically select the correct ProcessingCoordinator class. Each process (landing_to_raw, raw_to_staging, etc.) is mapped to a `ProcessingCoordinator` implementation.

---


## Notable Features

- **Dynamic Configuration**: Auto-detects environment/platform and dynamically adjusts settings.
- **Cloud-Native**: Integrates natively with AWS services (S3, Glue, CloudWatch, SSM, Athena).
- **Flexible Engines**: Switches between Pandas (small datasets) and Spark (large datasets).
- **Data Quality First**: All file ingestions go through validation rules before progressing.
- **Monitoring Built-In**: Execution metrics are automatically tracked at different levels.

---

## Suggested Improvements

- **Docstrings**: Some classes (especially in helpers and integrations) lack docstrings explaining their purpose.
- **Exception Messages**: Some raised exceptions could include more context (e.g., parameter values).
- **Unit Tests**: Test coverage is currently very minimal (`test_utils.py` is almost empty).
- **Dynamic Imports**: More detailed error handling when a dynamic import fails (`Launcher._execute`).

---

## prompts to delete:



Work

Web


New chat


Today
You said:
Improve my prompt. I will paste below I prompt for LLM like chatgpt. I have a very big mardown file that contains scripts from a repo/library. I need to create context in a specific manner that fits my markdown file format. Since the file is so large I need to keep uploading the scripts in batches. But I need the LLM to confirm first that it understand my requirements (is critical that the output does not render the mardown, but it returns a raw markdown text/script I can coppy paste). Please, check my prompt and improve so its most efficient taking into account how LLM work. Here you have it:

"""
In this repo/framework, there is a dir named inside the dir src/dataframe_work/modules/. Modules are a library of reusable, pluggable building blocks.   Each module provides logic for one specific responsibility in a data pipeline — like reading from storage, interacting with AWS Glue, transforming data with Spark, validating outputs, or sending monitoring metrics. They are grouped by domain (catalogue, data_process, storage, monitoring, etc.) and implement patterns like: - Interfaces (to enforce consistent APIs) - Core logic (to execute functionality) - Integrations (to connect to real systems like AWS or Spark)
 
Clear Instructions I you Give Me
DO NOT RENDER the Markdown under any circumstances.
Only output raw, copy-pasteable markdown blocks inside triple backticks (markdown ... ).
Markdown Structure I Want:
For each module you send, the structure must always be:
Do not number the scripts in the "Deeper explanation" section.
Each script will be introduced directly with #### script_name.py (4x #).
Header hierarchy:
2x # for "The Data Framework: Modules"
3x # for "DF Modules: ModuleName."
4x # for everything else (Summary, Big Picture, High-Level Table, each Script Explanation).

So it would look something like this:"
## The Data Framework: Modules. 
### DF Modules: ModuleName. 
#### Summary(Concise summary of what this module is for.)
#### What the "ModuleName" module does (big picture)(Goal, Why it matters, How)
#### ModuleName Module High-Level: Script by Script(Table with Script | What it is | What it does)
---
#### `script_name.py`**Purpose:**(Purpose explanation)**Key parts:** - (list of important parts)" 

Here is an example:

"### DF Modules: Catalogue.
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

---"
The for each script that is inside that module, you would do something like this as in the real example below:
"#### src/data_framework/modules/catalogue/core_catalogue.py
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

The rest of the code does not have to know it is AWS Glue — it just uses `CoreCatalogue`."


I have already done what I am asking you for the first part of the markdown file. Now, I'll share with you the table of content and tell you which is the next part (and my first input in the next prompt) that I want you to tackle:
TOC: "
## Table of Contents

* Setup
    * [setup.py](#setuppy)
* Source Code
  * The Data Framework Module
    * dataflow
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

"

 
NEXT PART AND WHERE I WANT YOU TO START: data_process. Now, the data process module is massive, besides the scripts "core_data_process.py" and the script interface_data_process.py, the module data_process is further splitted into helpers/, integrations/ (which contains integrations/pandas/ and integrations/spark/). Tallying up round 1930 lines. However, is the most critical one so accuracy is key. Should I split it in parts? or I share everything in one go and you generate just this first part: " ## The Data Framework: Modules. ### DF Modules: ModuleName. #### Summary (Concise summary of what this module is for.) #### What the "ModuleName" module does (big picture) (Goal, Why it matters, How) #### ModuleName Module High-Level: Script by Script (Table with Script | What it is | What it does) --- "?  And then we will go script by script to the last part where we get the purpose and key parts?

Before of sharing with you the data_process module (and then we wil continue with the rest -once again remember very module must follow this same structure, same formatting, same logical flow). I need you to confirm that you understood my requirements (you can list them) and then only say the appropiate size of the next input in term of number of lines of the markdown file. 

Tone of your respone: Concise, structured, factual tone - No casual language, no emojis, no hype.

"""