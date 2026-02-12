# 🗺️ Índice AWS para Data Engineering Bancario

> [!NOTE] Mapa de Contenido (MOC)
> Guías específicas de AWS orientadas a Data Engineering en el sector bancario.

```mermaid
graph TD
    A["🗺️ AWS Index"] --> B["🪣 S3 / Data Lake"]
    A --> C["⚙️ Glue (ETL)"]
    A --> D["🏢 Redshift"]
    A --> E["📨 Kinesis"]
    A --> F["⚡ Lambda"]
    A --> G["🔐 IAM / Seguridad"]
    
    B --> C
    C --> D
    E --> F
    F --> C
    G -.-> B
    G -.-> D
```

---

## 📚 Servicios de Data

| # | Guía | Descripción |
| :--- | :--- | :--- |
| 1 | [[AWS S3 y Data Lake]] | Buckets, Athena, Lake Formation, lifecycle, estructura bancaria |
| 2 | [[AWS Glue]] | Crawlers, ETL Jobs PySpark, Data Quality, Workflows |
| 3 | [[AWS Redshift]] | DDL optimizado, COPY/UNLOAD, Spectrum, WLM, RLS |
| 4 | [[AWS Kinesis]] | Data Streams, Firehose, detección de fraude en tiempo real |
| 5 | [[AWS Lambda y Serverless]] | Triggers S3, Step Functions, EventBridge |
| 6 | [[AWS IAM y Seguridad Bancaria]] | KMS, Secrets Manager, CloudTrail, compliance SOC2/PCI |

---

## 🏦 Arquitectura de Referencia para Banca

```mermaid
graph TD
    subgraph "Fuentes"
        A1["Core Banking"]
        A2["Tarjetas"]
        A3["APIs Externas"]
    end
    
    subgraph "Ingesta"
        B1["Kinesis (Real-time)"]
        B2["S3 Landing (Batch)"]
    end
    
    subgraph "Procesamiento"
        C1["AWS Glue ETL"]
        C2["Lambda (Validación)"]
        C3["Step Functions"]
    end
    
    subgraph "Data Lake (S3)"
        D1["🥉 Bronze/Raw"]
        D2["🥈 Silver/Curated"]
        D3["🥇 Gold/Analytics"]
    end
    
    subgraph "Consumo"
        E1["Redshift"]
        E2["Athena"]
        E3["QuickSight"]
    end
    
    subgraph "Seguridad"
        F1["IAM + KMS"]
        F2["Lake Formation"]
        F3["CloudTrail"]
    end
    
    A1 --> B1
    A1 --> B2
    A2 --> B1
    A3 --> B2
    B1 --> C2
    B2 --> C2
    C2 --> D1
    D1 --> C1
    C1 --> D2
    C1 --> D3
    D3 --> E1
    D3 --> E2
    E1 --> E3
    E2 --> E3
    C3 -.-> C1
    F1 -.-> D1
    F2 -.-> D3
```

---

## 🏷️ Tags
#aws #s3 #glue #redshift #kinesis #lambda #iam #seguridad #banca #data-lake

---

## 🔗 Relacionado en el Vault
- [[Índice Data Engineering|📊 Índice General Data Engineering]]
- [[Terraform para Data|🏗️ Terraform para Data]]
- [[Comandos Cloud y CLI|☁️ CLI AWS]]
- [[Arquitectura Medallion|🥇 Medallion Architecture]]
