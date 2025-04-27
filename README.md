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
