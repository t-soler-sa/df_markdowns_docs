## The Data Framework: Modules.
### DF Modules: Catalogue.
#### Summary

The catalogue module is responsible for managing metadata: reading table schemas and creating partitions dynamically. It hides the complexity of AWS Glue behind clean interfaces and easy-to-use static methods.

### What the "catalogue" module does (big picture)

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
