# 📚 RepoObsidian - Base de Conocimiento para Data Engineering

<div align="center">

![Data Engineering](https://img.shields.io/badge/Data-Engineering-blue?style=for-the-badge&logo=apache-spark&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Banking-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Obsidian](https://img.shields.io/badge/Obsidian-Vault-7C3AED?style=for-the-badge&logo=obsidian&logoColor=white)
![Español](https://img.shields.io/badge/Idioma-Español-red?style=for-the-badge)

**Biblioteca completa de conocimiento para Ingenieros de Datos, enfocada en el sector bancario.**

[Explorar Guías](#-contenido) • [Cómo Usar](#-cómo-usar) • [Contribuir](#-contribuir)

</div>

---

## 📋 Sobre este Repositorio

Este vault de [Obsidian](https://obsidian.md/) contiene **31 guías detalladas** en español, cubriendo desde los fundamentos de SQL hasta arquitecturas cloud en AWS para banca. Cada nota incluye:

- ✅ Ejemplos de código listos para copiar y usar
- ✅ Diagramas con [Mermaid](https://mermaid.js.org/)
- ✅ Enlaces bidireccionales (Graph View de Obsidian)
- ✅ Tips, warnings y buenas prácticas
- ✅ Contenido orientado al **sector financiero/bancario**

---

## 📂 Contenido

### 📚 Data Engineering (22 guías)

<details>
<summary><b>🔧 Fundamentos</b></summary>

| Guía | Temas |
| :--- | :--- |
| SQL Cheatsheet | JOINs, Window Functions, CTEs, optimización, DML |
| Python para Data Engineering | Pandas, PySpark, APIs, JDBC, validación de datos |
| Comandos Cloud y CLI | Linux/Bash, Docker/Podman, Git, AWS/GCP/Azure |

</details>

<details>
<summary><b>⚡ Tecnologías</b></summary>

| Guía | Temas |
| :--- | :--- |
| Apache Spark | SparkSession, transformaciones, Window, caché, Spark UI |
| Apache Flink | Streaming real-time, ventanas, PyFlink, checkpoints |
| Apache Kafka | Topics, productores/consumidores, Docker Compose |
| Snowflake | Warehouses, stages, Time Travel, Streams & Tasks |
| BigQuery | Partitioning, clustering, STRUCT/ARRAY, scripting |
| Apache Iceberg | Schema/partition evolution, Time Travel, MERGE |
| Databricks | Unity Catalog, Delta Lake, Auto Loader, workflows |

</details>

<details>
<summary><b>🏛️ Arquitectura y Diseño</b></summary>

| Guía | Temas |
| :--- | :--- |
| Data Modeling | Star Schema, Snowflake, Data Vault 2.0, SCD Tipo 2 |
| Arquitectura Medallion | Bronze / Silver / Gold con PySpark |
| Data Mesh | 4 principios, Data Products, gobernanza federada |
| Data Governance | Calidad, seguridad, linaje, GDPR, catálogo |

</details>

<details>
<summary><b>⚙️ Operaciones</b></summary>

| Guía | Temas |
| :--- | :--- |
| ETL y Pipelines | Airflow DAGs, dbt, Docker Compose |
| Airflow Avanzado | Dynamic DAGs, Sensors, XCom, Custom Operators |
| Terraform para Data | IaC para S3, Glue, BigQuery, modules |
| CI/CD para Pipelines | GitHub Actions, testing de datos, pre-commit |
| Observabilidad de Datos | Great Expectations, Soda Core, alertas |
| Troubleshooting | Errores comunes (Spark, SQL, Airflow, Docker), postmortems |

</details>

<details>
<summary><b>📈 Crecimiento Profesional</b></summary>

| Guía | Temas |
| :--- | :--- |
| Preguntas de Entrevista | SQL, Python, system design, escenarios prácticos |
| Glosario de Términos | Diccionario A-Z de Data Engineering |

</details>

---

### ☁️ AWS para Banca (7 guías)

| Guía | Temas |
| :--- | :--- |
| AWS S3 y Data Lake | Estructura bancaria, Athena, Lake Formation, lifecycle |
| AWS Glue | Crawlers, ETL PySpark, Data Quality, Bookmarks |
| AWS Redshift | COPY/UNLOAD, Spectrum, Distribution/Sort Keys, RLS |
| AWS Kinesis | Data Streams, Firehose, **detección de fraude real-time** |
| AWS Lambda y Serverless | Triggers S3, Step Functions, EventBridge |
| AWS IAM y Seguridad Bancaria | KMS, Secrets Manager, CloudTrail, **compliance SOC2/PCI** |
| Índice AWS | Arquitectura de referencia para banca |

---

### 📝 Obsidian Cheatsheets (2 guías)

| Guía | Temas |
| :--- | :--- |
| Markdown Cheatsheet | Sintaxis básica, tablas, código, enlaces |
| Advanced Markdown Guide | HTML, diagramas avanzados, footnotes |

---

## 🚀 Cómo Usar

### Opción 1: Con Obsidian (recomendado)
```bash
git clone https://github.com/plerzundidev/RepoObsidian.git
```
1. Abrir Obsidian
2. "Open folder as vault" → seleccionar la carpeta `Conocimiento/`
3. Navegar desde el **Índice Data Engineering** 
4. Activar **Graph View** para ver las conexiones entre notas

### Opción 2: Directo en GitHub
Navega las carpetas arriba — GitHub renderiza Markdown nativamente (los diagramas Mermaid también se ven).

---

## 🌿 Estrategia de Branches

| Branch | Propósito |
| :--- | :--- |
| `main` | Contenido estable y revisado |
| `antigravity` | Contenido generado/actualizado por IA — revisar antes de mergear |

**Flujo**: Contenido nuevo → commit en `antigravity` → Pull Request → Revisión → Merge a `main`

---

## 🤝 Contribuir

¿Quieres agregar una guía o mejorar una existente? 

1. Fork el repositorio
2. Crea un branch (`git checkout -b feature/nueva-guia`)
3. Escribe tu guía en Markdown siguiendo la estructura existente
4. Asegúrate de agregar enlaces bidireccionales (`[[Nota Relacionada]]`)
5. Abre un Pull Request

---

## 📊 Stats

| Métrica | Valor |
| :--- | :--- |
| Total de guías | 31 |
| Líneas de contenido | 9,300+ |
| Idioma | Español 🇪🇸 |
| Formato | Markdown / Obsidian |
| Enfoque | Data Engineering + Banca |

---

## 📜 Licencia

Este contenido es de uso educativo. Siéntete libre de usarlo, compartirlo y adaptarlo.

---

<div align="center">

**Hecho con 🧠 por [plerzundidev](https://github.com/plerzundidev) + [Antigravity AI](https://deepmind.google/)**

</div>
