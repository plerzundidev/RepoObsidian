# 📖 Glosario de Términos - Data Engineering

> [!NOTE] Cómo usar este glosario
> Usa `Ctrl+F` (o `Cmd+F`) para buscar un término rápidamente.

---

## A

| Término | Definición |
| :--- | :--- |
| **ACID** | Atomicity, Consistency, Isolation, Durability. Propiedades de transacciones confiables |
| **Aggregation** | Resumir datos: SUM, AVG, COUNT, etc. |
| **Airflow** | Orquestador de workflows de Apache. Programa y monitorea pipelines |
| **AVRO** | Formato de serialización binario con schema embebido |

## B

| Término | Definición |
| :--- | :--- |
| **Backfill** | Reprocesar datos históricos que faltan o erraron |
| **Batch Processing** | Procesar datos en grupos discretos (no en tiempo real) |
| **BigQuery** | Data Warehouse serverless de Google Cloud |
| **Bronze** | Capa de datos crudos en arquitectura Medallion |

## C

| Término | Definición |
| :--- | :--- |
| **Catalog** | Registro central de metadatos de tablas y datasets |
| **CDC** | Change Data Capture. Capturar solo los cambios incrementales de una fuente |
| **Clustering** | Ordenar físicamente los datos por columnas específicas para optimizar queries |
| **Columnar Format** | Formato que almacena datos por columna (Parquet, ORC). Ideal para analytics |
| **CTE** | Common Table Expression. Subconsulta nombrada reutilizable |

## D

| Término | Definición |
| :--- | :--- |
| **DAG** | Directed Acyclic Graph. Grafo de dependencias sin ciclos (Airflow, Spark) |
| **Data Lake** | Almacenamiento centralizado de datos crudos en cualquier formato |
| **Data Lakehouse** | Combinación de Data Lake + Data Warehouse (Delta Lake, Iceberg) |
| **Data Mesh** | Paradigma descentralizado donde dominios de negocio son dueños de sus datos |
| **Data Warehouse** | Base de datos optimizada para consultas analíticas (OLAP) |
| **Data Vault** | Metodología de modelado para data warehouses empresariales (Hubs, Links, Satellites) |
| **dbt** | Data Build Tool. Framework de transformación SQL con testing y documentación |
| **Delta Lake** | Formato de tabla abierto de Databricks con ACID sobre Data Lake |
| **Denormalization** | Agregar redundancia para mejorar performance de lectura |
| **Dimension** | Tabla descriptiva en modelo dimensional (quién, qué, dónde, cuándo) |

## E

| Término | Definición |
| :--- | :--- |
| **ELT** | Extract-Load-Transform. Cargar crudo y transformar en destino |
| **ETL** | Extract-Transform-Load. Transformar antes de cargar |
| **Event Streaming** | Flujo continuo de eventos (Kafka, Kinesis, Pub/Sub) |
| **Executor** | Proceso que ejecuta tareas en un nodo de Spark |
| **Exactly-once** | Semántica de entrega donde cada mensaje se procesa una sola vez |

## F

| Término | Definición |
| :--- | :--- |
| **Fact Table** | Tabla de hechos/métricas en modelo dimensional (lo que mides) |
| **Flink** | Framework de procesamiento de streams en tiempo real de Apache |
| **Freshness** | Qué tan actualizados están los datos |

## G

| Término | Definición |
| :--- | :--- |
| **GDPR** | Regulación europea de protección de datos personales |
| **Glue** | Servicio de ETL gestionado de AWS |
| **Gold** | Capa de datos curados y listos para negocio en arquitectura Medallion |

## H

| Término | Definición |
| :--- | :--- |
| **HDFS** | Hadoop Distributed File System. Sistema de archivos distribuido |
| **Hive** | Infraestructura SQL sobre Hadoop/Data Lake |
| **Hub** | Tabla central en Data Vault que almacena business keys |

## I

| Término | Definición |
| :--- | :--- |
| **Iceberg** | Formato de tabla abierto con ACID, time travel, schema evolution |
| **Idempotency** | Ejecutar N veces = mismo resultado. Fundamental en pipelines |
| **Incremental Load** | Cargar solo datos nuevos o cambiados |

## J-K

| Término | Definición |
| :--- | :--- |
| **JOIN** | Combinar filas de dos tablas basándose en una condición |
| **Kafka** | Plataforma de streaming de eventos distribuida de Apache |
| **Kinesis** | Servicio de streaming en tiempo real de AWS |

## L

| Término | Definición |
| :--- | :--- |
| **Lakehouse** | Arquitectura que combina Data Lake + Warehouse |
| **Lineage** | Linaje. Rastreo del origen y transformaciones de los datos |
| **Lazy Evaluation** | Las operaciones se registran pero no se ejecutan hasta una acción |

## M-N

| Término | Definición |
| :--- | :--- |
| **Medallion** | Arquitectura Bronze/Silver/Gold para organizar Data Lakes |
| **Metadata** | Datos sobre los datos (schema, owner, frecuencia, etc.) |
| **MERGE** | Operación que combina INSERT, UPDATE, DELETE en una sola sentencia |
| **Normalization** | Eliminar redundancia organizando datos en tablas relacionadas |

## O

| Término | Definición |
| :--- | :--- |
| **OLAP** | Online Analytical Processing. Optimizado para queries analíticas |
| **OLTP** | Online Transaction Processing. Optimizado para transacciones |
| **Orchestration** | Coordinar y programar la ejecución de múltiples tareas/pipelines |

## P

| Término | Definición |
| :--- | :--- |
| **Parquet** | Formato columnar binario, comprimido, estándar de la industria |
| **Partitioning** | Dividir datos en segmentos para optimizar queries |
| **PII** | Personally Identifiable Information. Datos personales protegidos |
| **Pipeline** | Flujo automatizado de extracción, transformación y carga de datos |

## R-S

| Término | Definición |
| :--- | :--- |
| **RBAC** | Role-Based Access Control. Permisos basados en roles |
| **SCD** | Slowly Changing Dimension. Tipos 0, 1, 2, 3 para manejar cambios históricos |
| **Schema** | Estructura de una tabla/dataset (columnas, tipos, constraints) |
| **Schema Evolution** | Capacidad de modificar el schema sin romper datos existentes |
| **Shuffle** | Redistribución de datos entre nodos en Spark (operación costosa) |
| **Silver** | Capa de datos limpios en arquitectura Medallion |
| **SLA** | Service Level Agreement. Compromisos de disponibilidad y frescura |
| **Snapshot** | Estado inmutable de los datos en un punto del tiempo |
| **Snowflake** | Data Warehouse cloud con separación compute/storage |
| **Spark** | Framework de procesamiento distribuido de Apache |
| **Star Schema** | Modelo dimensional con tabla de hechos central y dimensiones |
| **Surrogate Key** | Clave artificial generada (vs clave natural del negocio) |

## T-Z

| Término | Definición |
| :--- | :--- |
| **Terraform** | Herramienta de Infraestructura como Código (IaC) |
| **Time Travel** | Consultar datos como estaban en un punto anterior |
| **Topic** | Canal lógico en Kafka donde se publican mensajes |
| **Watermark** | En streaming, marca temporal que indica completitud de eventos |
| **Window Function** | Función SQL que calcula sobre un grupo de filas sin colapsar resultados |
| **ZORDER** | Técnica de co-localización de datos en Delta Lake para optimizar queries |

---

## 🧭 Navegación

Vuelve al [[Índice Data Engineering|Índice]]
