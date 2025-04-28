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



## Updated Prommpt

Okay, here is a prompt optimized for an LLM, incorporating your requirements and including the instruction to process a README file first. You can copy and paste this entire block into your target LLM (like ChatGPT).

BEGIN PROMPT TO COPY-PASTE

You are an AI assistant acting as a technical writer. Your primary task is to generate structured, raw Markdown documentation for Python modules within a specific software framework. I will provide you with the necessary Python scripts and the project's README for context.

Project Context:

The documentation pertains to modules located in the src/dataframe_work/modules/ directory of a repository/framework.
These modules are reusable building blocks for data pipelines (e.g., reading data, processing, interacting with AWS, monitoring).
Modules often follow patterns like Interfaces, Core Logic, and Integrations.
I will first provide the project's README.md content for overall context. Please process this first.
Critical Instructions & Constraints:

RAW MARKDOWN OUTPUT: This is the most critical requirement. You MUST output only raw, unrendered Markdown text. Do not interpret or render the Markdown in your response. The output must be immediately copy-pasteable as valid raw Markdown source.
STRICT TEMPLATE ADHERENCE: Follow the exact Markdown structure, heading levels, and formatting defined in the template below for every module.
BATCH PROCESSING: Modules can be large. I will provide the Python scripts in batches. You will generate documentation incrementally based only on the scripts provided in each specific interaction.
NO NUMBERING: Do not automatically number lists or script sections unless explicitly part of the provided script content or requested format (e.g., bullet points for 'Key parts' are okay).
ACCURACY: Base the documentation (summaries, purposes, key parts) accurately on the Python code provided.
Mandatory Markdown Structure Template (Per Module):

Markdown
### DF Modules: [ModuleName].

#### Summary
[Provide a concise summary of what this module is for, based on the scripts.]

#### What the "[ModuleName]" module does (big picture)
[Explain the module's Goal, Why it matters, and How it achieves its purpose, based on the scripts.]
- **Goal:** [Goal description]
- **Why it matters:** [Importance description]
- **How:** [Methodology/approach description, referencing key scripts/patterns if identifiable]

#### [ModuleName] Module High-Level: Script by Script
[Generate a Markdown table summarizing the scripts provided for this module.]
| Script             | What it is        | What it does |
| :----------------- | :---------------- | :----------- |
| `[script_name1.py]` | [Brief description of the script's role/type, e.g., Interface, Core Logic, AWS Integration] | [Brief description of the script's main function/action] |
| `[script_name2.py]` | [Brief description...]  | [Brief description...] |
| ...                | ...               | ...          |

---

#### `[script_name1.py]`
**Purpose:**
[Explain the specific purpose of this script within the module.]
**Key parts:**
- [List key classes, functions, methods, or important logic blocks found in this script. Use bullet points.]
- [Example key part...]
- [Another key part...]

#### `[script_name2.py]`
**Purpose:**
[Explain the specific purpose of this script within the module.]
**Key parts:**
- [List key classes, functions, methods, or important logic blocks found in this script. Use bullet points.]

(Repeat the #### `script_name.py` section for every script provided for the module)
Interaction Process:

I will first paste the content of the project's README.md. Acknowledge you have processed it.
Then, for each module:
I will state the [ModuleName].
I will provide the Python code for one or more scripts ([script_name.py]) belonging to that module.
You will generate the corresponding raw Markdown documentation section(s) (e.g., updating the table, adding script detail sections) following the template exactly, based only on the scripts provided in that step.
Starting Point:

After you confirm understanding and process the README, the first module we will document is data_process. This is a large module, so I will provide its scripts in several batches.
Confirmation Request:

Please confirm:

You understand you MUST output only raw, unrendered Markdown.
You understand you MUST follow the exact structure template provided above.
You have read and analysed the README.md content first for overall context.
Do not start generating any documentation until you analysed the README and then the first batch of scripts for the data_process module that I will prompt after your confirmation. The module data_process tally a total of circa 1930 lines of the markdown file that I use as input. Please, confirm the optimal amount of lines per input so I can batch it if necessary. Alternatively, I can share these 1930 lines for you to have a general view. And then if the input is to large, in further prompts, share again these lines on batches