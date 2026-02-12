# ⚙️ ETL, Pipelines y Orquestación - Ayuda Memoria Maestra

## 1. 🔄 Conceptos Fundamentales

```mermaid
graph LR
    A["🗂️ Fuentes"] --> B["Extract"]
    B --> C["Transform"]
    C --> D["Load"]
    D --> E["📊 Destino"]
    style B fill:#f9a825
    style C fill:#43a047
    style D fill:#1e88e5
```

| Concepto | Descripción |
| :--- | :--- |
| **ETL** | Extract → Transform → Load (transformar antes de cargar) |
| **ELT** | Extract → Load → Transform (cargar crudo, transformar en destino) |
| **CDC** | Change Data Capture (capturar solo cambios incrementales) |
| **SCD** | Slowly Changing Dimensions (Tipo 1, 2, 3) |
| **Idempotencia** | Ejecutar N veces = mismo resultado. **Fundamental.** |
| **Backfill** | Reprocesar datos históricos |

---

## 2. 🏗️ Arquitectura de Datos Moderna

```mermaid
graph TD
    A["Fuentes de Datos"] --> B["Ingesta"]
    B --> C["Data Lake - Raw/Bronze"]
    C --> D["Limpieza - Silver"]
    D --> E["Curación - Gold"]
    E --> F["Data Warehouse"]
    F --> G["BI / Dashboards"]
    F --> H["ML / Analytics"]
```

| Capa | Descripción | Formato |
| :--- | :--- | :--- |
| **Bronze/Raw** | Datos crudos tal cual llegan | JSON, CSV, Avro |
| **Silver** | Datos limpios, tipados, deduplicados | Parquet, Delta |
| **Gold** | Datos agregados, listos para negocio | Parquet, Delta, Tablas |

---

## 3. 🎼 Apache Airflow (Orquestación)

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.operators.bash import BashOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data_team',
    'retries': 3,
    'retry_delay': timedelta(minutes=5),
    'email_on_failure': True,
    'email': ['alertas@empresa.com'],
}

with DAG(
    dag_id='pipeline_ventas_diario',
    default_args=default_args,
    description='Pipeline diario de ventas',
    schedule_interval='0 6 * * *',  # Todos los días a las 6 AM
    start_date=datetime(2026, 1, 1),
    catchup=False,
    tags=['ventas', 'produccion'],
) as dag:

    extraer = PythonOperator(
        task_id='extraer_datos',
        python_callable=extraer_ventas,
    )
    
    transformar = PythonOperator(
        task_id='transformar_datos',
        python_callable=transformar_ventas,
    )
    
    cargar = PythonOperator(
        task_id='cargar_datos',
        python_callable=cargar_a_warehouse,
    )
    
    notificar = BashOperator(
        task_id='notificar',
        bash_command='echo "Pipeline completado exitosamente"',
    )

    extraer >> transformar >> cargar >> notificar
```

### Cron Expressions Comunes

| Expresión | Significado |
| :--- | :--- |
| `0 6 * * *` | Todos los días a las 6:00 AM |
| `0 */2 * * *` | Cada 2 horas |
| `0 0 * * 1` | Cada lunes a medianoche |
| `0 0 1 * *` | Primer día de cada mes |
| `*/15 * * * *` | Cada 15 minutos |

---

## 4. 🐳 Docker Compose para Pipelines

```yaml
# docker-compose.yml
version: '3.8'
services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_DB: warehouse
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

  airflow:
    image: apache/airflow:2.8.0
    environment:
      AIRFLOW__CORE__EXECUTOR: LocalExecutor
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://admin:${DB_PASSWORD}@postgres/airflow
    volumes:
      - ./dags:/opt/airflow/dags
    ports:
      - "8080:8080"
    depends_on:
      - postgres

volumes:
  pgdata:
```

> [!TIP] Consejo
> Usa archivos `.env` para las contraseñas y **nunca** las pongas directamente en el `docker-compose.yml`.

---

## 5. 📐 dbt (Data Build Tool)

```sql
-- models/staging/stg_ventas.sql
WITH source AS (
    SELECT * FROM {{ source('raw', 'ventas') }}
),
renamed AS (
    SELECT
        id AS venta_id,
        CAST(fecha AS DATE) AS fecha_venta,
        CAST(monto AS DECIMAL(10,2)) AS monto,
        cliente_id,
        {{ dbt_utils.generate_surrogate_key(['id']) }} AS sk_venta
    FROM source
    WHERE monto > 0
)
SELECT * FROM renamed
```

```bash
# Comandos esenciales de dbt
dbt run                          # Ejecutar todos los modelos
dbt run --select staging         # Solo modelos de una carpeta
dbt test                         # Ejecutar tests
dbt build                        # run + test juntos
dbt docs generate && dbt docs serve  # Documentación
dbt snapshot                     # SCD Tipo 2
```

---

## 6. 📋 Checklist Diario del Ingeniero de Datos

- [ ] Revisar estado de pipelines nocturnos
- [ ] Verificar alertas de calidad de datos
- [ ] Revisar logs de errores en Airflow
- [ ] Validar conteos de registros vs fuente
- [ ] Monitorear uso de recursos (CPU, memoria, almacenamiento)
- [ ] Documentar cambios realizados

---

## 7. 🛡️ Buenas Prácticas

> [!IMPORTANT] Reglas de Oro
> 1. **Idempotencia**: Cada pipeline debe poder re-ejecutarse sin duplicar datos.
> 2. **Logging**: Registra todo lo relevante.
> 3. **Alertas**: Configura notificaciones para fallos.
> 4. **Particionado**: Particiona por fecha para cargas incrementales eficientes.
> 5. **Versionado**: Versiona tu código SQL y Python en Git.
> 6. **Tests**: Valida datos de entrada y salida.
> 7. **Documentación**: Si no está documentado, no existe.

---

## 🧭 Navegación

← Vuelve a [[Python para Data Engineering|Python para Data Engineering]]
Continúa con [[Comandos Cloud y CLI|Comandos Cloud y CLI]] →
Vuelve al [[Índice Data Engineering|Índice]]
