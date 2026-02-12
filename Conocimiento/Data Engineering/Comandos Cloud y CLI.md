# ☁️ Comandos Cloud, CLI y DevOps - Ayuda Memoria

## 1. 🐧 Linux / Bash Esenciales

```bash
# Buscar archivos
find /datos -name "*.csv" -mtime -7       # CSV modificados últimos 7 días
find /logs -size +100M -type f            # Archivos mayores a 100MB

# Procesar texto
grep -r "ERROR" /var/log/                 # Buscar errores recursivamente
grep -c "ERROR" archivo.log              # Contar ocurrencias
awk -F',' '{print $1, $3}' datos.csv     # Extraer columnas 1 y 3
sed 's/viejo/nuevo/g' archivo.txt        # Reemplazar texto
sort datos.csv | uniq -c | sort -rn      # Contar y ordenar duplicados

# Monitoreo del sistema
htop                                      # Monitor interactivo
df -h                                     # Espacio en disco
du -sh /datos/*                           # Tamaño de directorios
free -h                                   # Uso de memoria RAM

# Transferencia de archivos
scp archivo.csv user@server:/destino/     # Copiar a servidor remoto
rsync -avz /datos/ user@server:/backup/   # Sincronizar directorios

# Comprimir/Descomprimir
tar -czf backup.tar.gz /datos/            # Comprimir
tar -xzf backup.tar.gz                    # Descomprimir
gzip archivo.csv                          # Comprimir individual
zcat archivo.csv.gz | head -100           # Ver comprimido sin extraer
```

---

## 2. 🐳 Docker & Podman

```bash
# Imágenes
podman build -t mi_pipeline:v1 .          # Construir imagen
podman images                             # Listar imágenes
podman pull postgres:16                   # Descargar imagen

# Contenedores
podman run -d --name postgres -p 5432:5432 postgres:16
podman ps                                 # Contenedores activos
podman ps -a                              # Todos (incluidos parados)
podman logs -f mi_contenedor              # Ver logs en tiempo real
podman exec -it mi_contenedor bash        # Entrar al contenedor
podman stop mi_contenedor                 # Detener
podman rm mi_contenedor                   # Eliminar

# Compose
podman-compose up -d                      # Levantar en segundo plano
podman-compose down                       # Detener y eliminar
podman-compose logs -f                    # Ver todos los logs
podman-compose ps                         # Estado de servicios

# Limpieza
podman system prune -a                    # Eliminar todo lo no usado
podman volume prune                       # Eliminar volúmenes huérfanos
```

---

## 3. 🔀 Git Esencial

```bash
# Flujo diario
git status                                # Ver estado
git add .                                 # Agregar todos los cambios
git commit -m "feat: agregar pipeline ventas"
git pull origin main --rebase             # Actualizar (limpio)
git push origin feature/mi-rama

# Ramas
git checkout -b feature/nueva-feature     # Crear y cambiar de rama
git branch -d feature/vieja               # Eliminar rama local
git branch -a                             # Listar todas las ramas

# Deshacer cambios
git stash                                 # Guardar cambios temporalmente
git stash pop                             # Recuperar cambios guardados
git reset --soft HEAD~1                   # Deshacer último commit (mantener cambios)
git checkout -- archivo.py                # Descartar cambios de un archivo

# Historial
git log --oneline -20                     # Últimos 20 commits resumidos
git log --graph --oneline --all           # Gráfico de ramas
git diff main..feature/mi-rama            # Diferencias entre ramas
```

### Conventional Commits (Estándar)

| Prefijo | Uso |
| :--- | :--- |
| `feat:` | Nueva funcionalidad |
| `fix:` | Corrección de bug |
| `docs:` | Solo documentación |
| `refactor:` | Reestructurar sin cambiar funcionalidad |
| `chore:` | Tareas de mantenimiento |
| `test:` | Agregar o modificar tests |

---

## 4. ☁️ AWS CLI (Referencia Rápida)

```bash
# S3
aws s3 ls s3://mi-bucket/                # Listar contenido
aws s3 cp archivo.csv s3://mi-bucket/    # Subir archivo
aws s3 sync ./datos/ s3://mi-bucket/datos/  # Sincronizar directorio
aws s3 rm s3://mi-bucket/viejo.csv       # Eliminar archivo
aws s3 mb s3://nuevo-bucket              # Crear bucket

# Glue (ETL)
aws glue start-job-run --job-name mi_job
aws glue get-job-runs --job-name mi_job

# Athena (SQL sobre S3)
aws athena start-query-execution \
  --query-string "SELECT * FROM tabla LIMIT 10" \
  --result-configuration "OutputLocation=s3://resultados/"
```

---

## 5. ☁️ GCP CLI (gcloud)

```bash
# BigQuery
bq query --use_legacy_sql=false 'SELECT * FROM dataset.tabla LIMIT 10'
bq load --source_format=CSV dataset.tabla gs://bucket/datos.csv
bq ls dataset                             # Listar tablas
bq show dataset.tabla                     # Info de tabla

# Cloud Storage
gsutil ls gs://mi-bucket/                 # Listar
gsutil cp archivo.csv gs://mi-bucket/     # Subir
gsutil rsync -r ./datos/ gs://mi-bucket/datos/  # Sincronizar
```

---

## 6. ☁️ Azure CLI

```bash
# Blob Storage
az storage blob list --container-name datos --account-name cuenta
az storage blob upload --file datos.csv --container-name datos --name datos.csv
az storage blob download --container-name datos --name datos.csv -f local.csv

# Synapse / SQL
az synapse spark job submit --workspace ws --name mi_job --main-definition-file job.py
```

---

## 🧭 Navegación

← Vuelve a [[ETL y Pipelines|ETL y Pipelines]]
Vuelve al [[Índice Data Engineering|Índice]]
