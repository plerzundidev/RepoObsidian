# 🐍 Python para Data Engineering - Ayuda Memoria Maestra

## 1. 📦 Pandas (Manipulación de Datos)

```python
import pandas as pd

# Leer datos
df = pd.read_csv('datos.csv')
df = pd.read_parquet('datos.parquet')
df = pd.read_json('datos.json')
df = pd.read_sql('SELECT * FROM tabla', connection)

# Vista rápida del DataFrame
df.head(10)          # Primeras 10 filas
df.shape             # (filas, columnas)
df.info()            # Tipos de datos y nulos
df.describe()        # Estadísticas descriptivas
df.dtypes            # Tipos de cada columna
df.isnull().sum()    # Conteo de nulos por columna
```

### Filtrado y Selección
```python
# Filtrar filas
df[df['edad'] > 30]
df[(df['edad'] > 30) & (df['ciudad'] == 'Madrid')]
df.query("edad > 30 and ciudad == 'Madrid'")  # Alternativa más legible

# Seleccionar columnas
df[['nombre', 'edad']]
df.loc[0:5, 'nombre':'ciudad']   # Por etiqueta
df.iloc[0:5, 0:3]                # Por posición
```

### Transformaciones
```python
# Crear/modificar columna
df['nombre_upper'] = df['nombre'].str.upper()
df['anio'] = pd.to_datetime(df['fecha']).dt.year

# Apply personalizado
df['categoria'] = df['monto'].apply(lambda x: 'alto' if x > 1000 else 'bajo')

# Reemplazar valores
df['estado'].replace({'activo': 1, 'inactivo': 0}, inplace=True)

# Eliminar duplicados
df.drop_duplicates(subset=['email'], keep='last')

# Manejar nulos
df.fillna({'edad': 0, 'nombre': 'Desconocido'})
df.dropna(subset=['email'])
```

### Agregación y Agrupación
```python
# Group By
df.groupby('departamento').agg(
    total_empleados=('id', 'count'),
    salario_promedio=('salario', 'mean'),
    salario_max=('salario', 'max')
).reset_index()

# Pivot Table
pd.pivot_table(df, values='ventas', index='region', columns='mes', aggfunc='sum')

# Merge (equivalente a JOIN)
resultado = pd.merge(clientes, ordenes, left_on='id', right_on='cliente_id', how='left')
```

### Exportar
```python
df.to_csv('resultado.csv', index=False)
df.to_parquet('resultado.parquet', compression='snappy')
df.to_json('resultado.json', orient='records')
```

---

## 2. 🔥 PySpark (Big Data)

```python
from pyspark.sql import SparkSession
from pyspark.sql import functions as F
from pyspark.sql.window import Window

# Crear sesión
spark = SparkSession.builder \
    .appName("MiApp") \
    .config("spark.sql.shuffle.partitions", 200) \
    .getOrCreate()

# Leer datos
df = spark.read.parquet("s3://bucket/datos/")
df = spark.read.csv("datos.csv", header=True, inferSchema=True)
df = spark.read.json("datos.json")
```

### Transformaciones en Spark
```python
# Filtrar
df.filter(F.col("edad") > 30)
df.where("edad > 30 AND ciudad = 'Madrid'")

# Seleccionar y renombrar
df.select("nombre", F.col("edad").alias("years"))

# Crear columnas
df.withColumn("anio", F.year("fecha"))
df.withColumn("categoria", F.when(F.col("monto") > 1000, "alto").otherwise("bajo"))

# Agregación
df.groupBy("departamento").agg(
    F.count("*").alias("total"),
    F.avg("salario").alias("promedio"),
    F.sum("salario").alias("nomina")
)

# Window Functions
ventana = Window.partitionBy("departamento").orderBy(F.desc("salario"))
df.withColumn("ranking", F.row_number().over(ventana))

# JOIN
resultado = df1.join(df2, df1.id == df2.cliente_id, "left")

# Eliminar duplicados
df.dropDuplicates(["email"])

# Caché (para reutilizar DataFrames)
df.cache()
df.persist()
```

### Escritura
```python
# Escribir particionado
df.write.mode("overwrite") \
    .partitionBy("anio", "mes") \
    .parquet("s3://bucket/output/")

# Delta Lake
df.write.format("delta").mode("merge").save("/delta/tabla")
```

---

## 3. 🌐 APIs y Requests

```python
import requests
import json

# GET simple
response = requests.get("https://api.ejemplo.com/datos", 
                         headers={"Authorization": "Bearer TOKEN"})
data = response.json()

# POST
payload = {"nombre": "test", "valor": 42}
response = requests.post("https://api.ejemplo.com/enviar", json=payload)

# Paginación automática
def obtener_todos(url):
    resultados = []
    while url:
        resp = requests.get(url).json()
        resultados.extend(resp['data'])
        url = resp.get('next_page')
    return resultados

# Reintentos con backoff
from tenacity import retry, wait_exponential, stop_after_attempt

@retry(wait=wait_exponential(min=1, max=60), stop=stop_after_attempt(5))
def llamar_api(url):
    resp = requests.get(url, timeout=30)
    resp.raise_for_status()
    return resp.json()
```

---

## 4. 📁 Manejo de Archivos

```python
import os
import glob
import shutil

# Listar archivos por patrón
archivos = glob.glob("/datos/**/*.csv", recursive=True)

# Leer/escribir JSON
with open('config.json', 'r') as f:
    config = json.load(f)

with open('output.json', 'w') as f:
    json.dump(data, f, indent=2, ensure_ascii=False)

# Variables de entorno
DB_HOST = os.getenv("DB_HOST", "localhost")
DB_PASS = os.getenv("DB_PASSWORD")  # Nunca hardcodear passwords
```

---

## 5. 🗃️ Conexiones a Bases de Datos

```python
# SQLAlchemy (universal)
from sqlalchemy import create_engine

engine = create_engine("postgresql://user:pass@host:5432/db")
df = pd.read_sql("SELECT * FROM tabla", engine)
df.to_sql("tabla_destino", engine, if_exists='replace', index=False)

# psycopg2 (PostgreSQL directo)
import psycopg2

conn = psycopg2.connect(host="localhost", database="mi_db", 
                         user="usuario", password="secreto")
cur = conn.cursor()
cur.execute("SELECT * FROM tabla")
rows = cur.fetchall()
conn.close()
```

---

## 6. ✅ Testing y Validación de Datos

```python
# Great Expectations (validación de calidad)
import great_expectations as gx

context = gx.get_context()

# Validaciones comunes
expectation_suite = context.add_expectation_suite("validaciones")
# - Columna no nula
# - Valores únicos
# - Rango de valores
# - Formato de fecha

# Assert manual rápido
assert df.shape[0] > 0, "DataFrame vacío"
assert df['email'].duplicated().sum() == 0, "Emails duplicados"
assert df['monto'].min() >= 0, "Montos negativos encontrados"
```

---

## 🧭 Navegación

← Vuelve a [[SQL Cheatsheet|SQL Cheatsheet]]
Continúa con [[ETL y Pipelines|ETL y Pipelines]] →
Vuelve al [[Índice Data Engineering|Índice]]
