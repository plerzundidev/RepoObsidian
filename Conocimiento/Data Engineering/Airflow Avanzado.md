# 🎼 Airflow Avanzado - Ayuda Memoria para Ingenieros de Datos

## 1. 🧩 Dynamic DAGs (DAGs Dinámicos)

### DAG Factory (generar DAGs desde config)
```python
import yaml
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def crear_dag(config):
    dag = DAG(
        dag_id=config['dag_id'],
        schedule_interval=config['schedule'],
        start_date=datetime(2026, 1, 1),
        catchup=False,
        tags=config.get('tags', []),
    )
    
    with dag:
        for tarea in config['tasks']:
            PythonOperator(
                task_id=tarea['id'],
                python_callable=globals()[tarea['callable']],
                op_kwargs=tarea.get('params', {}),
            )
    return dag

# Leer configuraciones
with open('/opt/airflow/config/pipelines.yaml') as f:
    configs = yaml.safe_load(f)

for config in configs['pipelines']:
    globals()[config['dag_id']] = crear_dag(config)
```

### Generar tareas dinámicamente
```python
from airflow.decorators import dag, task

@dag(schedule_interval='@daily', start_date=datetime(2026, 1, 1), catchup=False)
def pipeline_multi_tabla():
    tablas = ['ventas', 'clientes', 'productos', 'inventario']
    
    @task
    def extraer(tabla: str):
        print(f"Extrayendo {tabla}")
    
    @task
    def transformar(tabla: str):
        print(f"Transformando {tabla}")
    
    @task
    def cargar(tabla: str):
        print(f"Cargando {tabla}")
    
    for tabla in tablas:
        extraer(tabla) >> transformar(tabla) >> cargar(tabla)

pipeline_multi_tabla()
```

---

## 2. 📡 XCom (Comunicación entre Tareas)

```python
from airflow.decorators import dag, task

@dag(schedule_interval='@daily', start_date=datetime(2026, 1, 1))
def pipeline_con_xcom():
    
    @task
    def extraer():
        datos = {'registros': 1500, 'fecha': '2026-02-12'}
        return datos  # Se guarda automáticamente en XCom
    
    @task
    def validar(datos: dict):
        assert datos['registros'] > 0, "Sin registros"
        return datos['registros']
    
    @task
    def reportar(total: int):
        print(f"Procesados {total} registros")
    
    datos = extraer()
    total = validar(datos)
    reportar(total)

pipeline_con_xcom()
```

> [!WARNING] Advertencia
> XCom almacena datos en la base de Airflow. No pases DataFrames ni datos grandes por XCom. Usa rutas de archivos o referencias.

---

## 3. 📡 Sensors (Esperar condiciones)

```python
from airflow.sensors.filesystem import FileSensor
from airflow.sensors.external_task import ExternalTaskSensor
from airflow.providers.http.sensors import HttpSensor
from airflow.sensors.sql import SqlSensor

# Esperar a que un archivo exista
esperar_archivo = FileSensor(
    task_id='esperar_archivo',
    filepath='/datos/ventas_{{ ds }}.csv',
    poke_interval=60,          # Revisar cada 60 segundos
    timeout=3600,              # Timeout: 1 hora
    mode='reschedule',         # Liberar worker mientras espera
)

# Esperar a que otro DAG termine
esperar_dag_externo = ExternalTaskSensor(
    task_id='esperar_ingesta',
    external_dag_id='ingesta_cruda',
    external_task_id='finalizar',
    timeout=7200,
    mode='reschedule',
)

# Esperar respuesta HTTP
esperar_api = HttpSensor(
    task_id='esperar_api',
    http_conn_id='mi_api',
    endpoint='/health',
    response_check=lambda response: response.json()['status'] == 'ready',
    poke_interval=30,
    timeout=600,
)

# Esperar condición SQL
esperar_datos = SqlSensor(
    task_id='esperar_datos',
    conn_id='mi_postgres',
    sql="SELECT COUNT(*) FROM staging WHERE fecha = '{{ ds }}'",
    success=lambda count: count[0][0] > 0,
    poke_interval=120,
    timeout=3600,
    mode='reschedule',
)
```

---

## 4. 🔌 Custom Operators

```python
from airflow.models import BaseOperator
from airflow.utils.decorators import apply_defaults

class ValidarCalidadOperator(BaseOperator):
    
    template_fields = ['tabla', 'fecha']
    
    @apply_defaults
    def __init__(self, tabla, fecha, umbral_nulos=0.05, **kwargs):
        super().__init__(**kwargs)
        self.tabla = tabla
        self.fecha = fecha
        self.umbral_nulos = umbral_nulos
    
    def execute(self, context):
        from sqlalchemy import create_engine
        import pandas as pd
        
        engine = create_engine(self.get_connection('mi_db').get_uri())
        df = pd.read_sql(f"SELECT * FROM {self.tabla} WHERE fecha = '{self.fecha}'", engine)
        
        total = len(df)
        nulos = df.isnull().sum().sum()
        ratio = nulos / (total * len(df.columns))
        
        if ratio > self.umbral_nulos:
            raise ValueError(f"Ratio de nulos {ratio:.2%} excede umbral {self.umbral_nulos:.2%}")
        
        self.log.info(f"Validación OK: {total} filas, {ratio:.2%} nulos")
        return {'total': total, 'ratio_nulos': ratio}

# Uso en DAG
validar = ValidarCalidadOperator(
    task_id='validar_calidad',
    tabla='ventas',
    fecha='{{ ds }}',
    umbral_nulos=0.02,
)
```

---

## 5. 🔔 Alertas y Callbacks

```python
from airflow.providers.slack.operators.slack_webhook import SlackWebhookOperator

def alerta_fallo(context):
    """Callback cuando una tarea falla."""
    dag_id = context['dag'].dag_id
    task_id = context['task'].task_id
    exec_date = context['execution_date']
    log_url = context['task_instance'].log_url
    
    SlackWebhookOperator(
        task_id='alerta_slack',
        slack_webhook_conn_id='slack_alertas',
        message=f"❌ *Fallo en Pipeline*\nDAG: `{dag_id}`\nTarea: `{task_id}`\nFecha: {exec_date}\n<{log_url}|Ver logs>",
    ).execute(context)

def alerta_exito(context):
    """Callback cuando el DAG completa exitosamente."""
    print(f"✅ DAG {context['dag'].dag_id} completado")

# Aplicar callbacks
default_args = {
    'on_failure_callback': alerta_fallo,
    'on_success_callback': alerta_exito,
    'retries': 3,
    'retry_delay': timedelta(minutes=5),
    'retry_exponential_backoff': True,
}
```

---

## 6. 📊 Jinja Templating

```python
# Variables de fecha disponibles en templates
"""
{{ ds }}              → 2026-02-12 (fecha de ejecución)
{{ ds_nodash }}       → 20260212
{{ prev_ds }}         → 2026-02-11 (día anterior)
{{ next_ds }}         → 2026-02-13
{{ execution_date }}  → DateTime completo
{{ data_interval_start }}
{{ data_interval_end }}
{{ macros.ds_add(ds, 7) }}  → Sumar 7 días
{{ macros.ds_format(ds, '%Y-%m-%d', '%d/%m/%Y') }}
{{ params.mi_variable }}
{{ var.value.mi_variable_airflow }}
{{ conn.mi_conexion.host }}
"""

# Ejemplo en BashOperator
from airflow.operators.bash import BashOperator

procesar = BashOperator(
    task_id='procesar',
    bash_command="""
        python pipeline.py \
            --fecha {{ ds }} \
            --tabla {{ params.tabla }} \
            --output s3://bucket/output/{{ ds_nodash }}/
    """,
    params={'tabla': 'ventas'},
)
```

---

## 7. 🏗️ Buenas Prácticas

> [!TIP] Reglas de Oro de Airflow
> 1. DAGs deben ser **idempotentes** y **determinísticos**
> 2. No hagas trabajo pesado en el DAG file (solo definición)
> 3. Usa `mode='reschedule'` en Sensors para liberar workers
> 4. Nunca pases datos grandes por XCom
> 5. Usa `catchup=False` a menos que necesites backfill
> 6. Tareas atómicas: una tarea = una acción
> 7. Naming: `dag_id` descriptivo, `task_id` verbo_sustantivo

---

## 🧭 Navegación

Vuelve al [[Índice Data Engineering|Índice]]
Relacionado: [[ETL y Pipelines|ETL Básico]] | [[CI-CD para Pipelines|CI/CD]]
