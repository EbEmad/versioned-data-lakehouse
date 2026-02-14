Versioned Data Lakehouse and Atomic ETL Pipeline with Nessie, Iceberg, and Spark
===========================================================

![Nessie](https://img.shields.io/badge/Nessie-git--flow-blue) ![Spark](https://img.shields.io/badge/Spark-ETL-orange) ![Iceberg](https://img.shields.io/badge/Iceberg-table--format-green) ![MinIO](https://img.shields.io/badge/MinIO-object--storage-yellow) ![Jupyter](https://img.shields.io/badge/Jupyter-notebooks-red)


This project demonstrates how to build a versioned data lakehouse with an atomic version-controlled ETL pipeline using Project Nessie as the catalog provider, Apache Iceberg for table format, and Apache Spark for processing. It shows how to apply Git-like version control to your data engineering workflows, similar to how Git manages your code.

## Project Flow

```mermaid
graph TD
    subgraph Storage ["Object Storage"]
        MinIO[("MinIO (S3)")]
    end

    subgraph Catalog ["Catalog & Versioning"]
        Nessie{{"Project Nessie<br/>(Git-like Catalog)"}}
        subgraph Branches ["Branches"]
            B_Main[("main branch<br/>(Production)")]
            B_Raw[("raw branch<br/>(Ingestion)")]
            B_Dev[("dev branch<br/>(ETL / Validation)")]
        end
    end

    subgraph Compute ["Processing Engine"]
        Spark[["Apache Spark<br/>with Iceberg Extensions"]]
    end

    Data_CSV["IMDb Raw Data (CSV)"] --> Spark
    
    Spark -->|"1. Create 'raw' branch"| B_Raw
    Spark -->|"2. Ingest Data"| B_Raw
    B_Raw -->|"Metadata & Data"| MinIO

    B_Raw -->|"3. Branch 'dev' from 'raw'"| B_Dev
    Spark -->|"4. Transform & Clean"| B_Dev
    B_Dev -->|"Commit Updates"| Nessie
    B_Dev -->|"New Data Files"| MinIO

    B_Dev -->|"5. Quality Check & Merge"| B_Main
    B_Main -->|"6. Create Tag / Snapshot"| Nessie

    linkStyle 1,2,3,4,5,6,7 stroke:#f60,stroke-width:2px;
```
