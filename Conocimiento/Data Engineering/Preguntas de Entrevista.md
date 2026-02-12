# 🎯 Preguntas de Entrevista - Data Engineering

## 1. 📋 SQL (Nivel Técnico)

### Básico
**P: ¿Cuál es la diferencia entre WHERE y HAVING?**
> WHERE filtra filas **antes** de la agregación. HAVING filtra **después** del GROUP BY.

**P: ¿Diferencia entre DELETE, TRUNCATE y DROP?**
> - `DELETE`: Borra filas, se puede filtrar, genera logs, se puede rollback
> - `TRUNCATE`: Borra todas las filas, más rápido, no genera logs por fila
> - `DROP`: Elimina la tabla completamente (estructura + datos)

### Intermedio
**P: Explica las Window Functions con un ejemplo.**
> Las Window Functions calculan un valor por fila basado en un grupo de filas relacionadas, sin colapsar el resultado como GROUP BY.
> ```sql
> SELECT nombre, departamento, salario,
>        RANK() OVER (PARTITION BY departamento ORDER BY salario DESC) AS ranking
> FROM empleados;
> ```
> Esto calcula el ranking salarial dentro de cada departamento sin eliminar filas.

**P: ¿Qué es un CTE recursivo? ¿Cuándo lo usarías?**
> Un CTE que se referencia a sí mismo. Útil para jerarquías (organigramas, categorías anidadas, grafos).

### Avanzado
**P: ¿Cómo optimizarías una query que tarda 30 minutos?**
> 1. `EXPLAIN ANALYZE` para ver el plan de ejecución
> 2. Verificar si hay full table scan → crear índices
> 3. Revisar JOINs innecesarios o mal ordenados
> 4. Eliminar `SELECT *`, solo columnas necesarias
> 5. Particionar la tabla si es muy grande
> 6. Considerar vistas materializadas para queries recurrentes

---

## 2. 🐍 Python y Spark

**P: ¿Diferencia entre Pandas y PySpark?**
> | Pandas | PySpark |
> | :--- | :--- |
> | Single machine | Distribuido (cluster) |
> | Datos en memoria | Lazy evaluation |
> | MB a pocos GB | GB a TB+ |
> | Ejecución inmediata | Necesita acción para ejecutar |

**P: ¿Qué es lazy evaluation en Spark?**
> Las transformaciones (`filter`, `select`, `withColumn`) no se ejecutan inmediatamente. Spark construye un DAG y solo ejecuta cuando se llama una acción (`count`, `show`, `write`). Esto permite optimizar el plan de ejecución.

**P: ¿Cómo manejas data skew en Spark?**
> 1. **Salting**: Agregar sufijo aleatorio al key, reparticionar, luego re-agregar
> 2. **Broadcast join**: Si una tabla es pequeña, usar `broadcast()`
> 3. **AQE (Adaptive Query Execution)**: Spark lo maneja automáticamente
> 4. **Repartition**: Redistribuir datos explícitamente

---

## 3. 🏗️ Arquitectura y Diseño de Sistemas

**P: Diseña un pipeline de datos para una empresa de e-commerce.**
> ```mermaid
> graph TD
>     A["API Orders"] --> B["Kafka"]
>     C["Base Productos"] --> D["CDC (Debezium)"]
>     D --> B
>     B --> E["Spark Streaming"]
>     E --> F["Bronze (S3/Delta)"]
>     F --> G["Silver (Limpieza)"]
>     G --> H["Gold (Star Schema)"]
>     H --> I["Snowflake/BigQuery"]
>     I --> J["Dashboards BI"]
>     I --> K["ML Models"]
> ```

**P: ¿Qué es ETL vs ELT? ¿Cuándo usar cada uno?**
> - **ETL**: Transformar antes de cargar. Cuando el destino tiene cómputo limitado (on-prem)
> - **ELT**: Cargar crudo, transformar en destino. Cuando tienes un warehouse potente (Snowflake, BigQuery) y quieres mantener datos crudos

**P: Explica la arquitectura Medallion.**
> - **Bronze**: Datos crudos, append-only, copia fiel de la fuente
> - **Silver**: Datos limpios, tipados, deduplicados, validados
> - **Gold**: Datos agregados, modelos dimensionales, listos para negocio

**P: ¿Qué es idempotencia y por qué es importante?**
> Un pipeline idempotente produce el mismo resultado sin importar cuántas veces se ejecute. Es fundamental para:
> - Re-ejecución segura en caso de fallos
> - Backfill de datos históricos
> - Evitar duplicados en la carga

---

## 4. 📨 Streaming

**P: ¿At-most-once vs At-least-once vs Exactly-once?**
> | Semántica | Pérdida | Duplicados | Uso |
> | :--- | :--- | :--- | :--- |
> | At-most-once | Posible | No | Métricas no críticas |
> | At-least-once | No | Posible | La mayoría de casos + idempotencia |
> | Exactly-once | No | No | Transacciones financieras |

**P: ¿Cómo garantizas exactly-once en Kafka?**
> 1. Productor: `enable.idempotence=true`, `acks=all`
> 2. Consumidor: Commit manual después de procesar
> 3. Transacciones: `transactional.id` para atomic writes

---

## 5. 🧩 Escenarios Prácticos

**P: Tienes un pipeline que carga 1 billón de filas diarias. ¿Cómo lo diseñas?**
> 1. **Carga incremental**: Solo procesar datos nuevos o cambiados (CDC)
> 2. **Particionado**: Por fecha para limitar el scan
> 3. **Formato columnar**: Parquet o ORC
> 4. **Procesamiento distribuido**: Spark con cluster autoscalable
> 5. **Compresión**: Snappy o Zstandard
> 6. **Monitoreo**: Alertas de volumen, frescura y calidad

**P: Un dashboard muestra datos incorrectos. ¿Cómo investigas?**
> 1. ¿Cuándo empezó? → Revisar historial de cambios
> 2. ¿Es el query del dashboard o los datos? → Consultar tabla directamente
> 3. Trazar el linaje hacia atrás: Gold → Silver → Bronze → Fuente
> 4. ¿Cambió el schema? ¿Llegaron datos duplicados?
> 5. Comparar conteos entre capas
> 6. Revisar logs del pipeline de esa fecha

---

## 6. 💡 Preguntas de Comportamiento

**P: Cuéntame sobre un pipeline complejo que diseñaste.**
> Estructura tu respuesta con **STAR**: Situación, Tarea, Acción, Resultado.

**P: ¿Cómo manejas prioridades conflictivas?**
> Evaluar impacto al negocio, comunicar con stakeholders, documentar trade-offs.

**P: ¿Cómo te mantienes actualizado en el campo?**
> Blogs (Data Engineering Weekly), conferencias, podcasts, proyectos personales, comunidades.

---

## 🧭 Navegación

Vuelve al [[Índice Data Engineering|Índice]]
Relacionado: [[SQL Cheatsheet|SQL]] | [[Apache Spark|Spark]] | [[Apache Kafka|Kafka]]
