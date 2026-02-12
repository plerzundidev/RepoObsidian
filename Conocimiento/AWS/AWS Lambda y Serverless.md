# ⚡ AWS Lambda y Serverless - Ayuda Memoria para Data Engineers

## 1. 🧠 Cuándo Usar Lambda en Data Engineering

| Caso de Uso | Ejemplo Bancario |
| :--- | :--- |
| **Trigger por evento S3** | Archivo llega a landing → lanzar ETL |
| **Procesamiento ligero** | Validar formato, mover archivos, notificar |
| **Consumir Kinesis** | Detección de fraude en tiempo real |
| **API de datos** | Endpoint para consultar saldos |
| **Orquestación** | Iniciar Glue Jobs, Step Functions |

> [!NOTE] Límites importantes
> - **Timeout**: 15 minutos máximo
> - **Memoria**: 128 MB - 10 GB
> - **Payload**: 6 MB (síncrono), 256 KB (async)
> - Para ETL pesado (>15 min o >10GB), usa Glue o EMR

---

## 2. 🐍 Lambda Functions

### Trigger por S3 (archivo nuevo)
```python
import boto3
import json

glue = boto3.client('glue')
s3 = boto3.client('s3')

def lambda_handler(event, context):
    # Obtener info del archivo subido
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']
    size = event['Records'][0]['s3']['object']['size']
    
    print(f"📥 Nuevo archivo: s3://{bucket}/{key} ({size} bytes)")
    
    # Validar que es el tipo correcto
    if not key.endswith(('.csv', '.parquet', '.json')):
        print(f"⚠️ Formato no soportado: {key}")
        return {'status': 'skipped'}
    
    # Determinar qué pipeline lanzar según el path
    if 'transacciones' in key:
        job_name = 'etl-transacciones'
    elif 'clientes' in key:
        job_name = 'etl-clientes'
    else:
        job_name = 'etl-generico'
    
    # Lanzar Glue Job
    response = glue.start_job_run(
        JobName=job_name,
        Arguments={
            '--input_path': f's3://{bucket}/{key}',
            '--fecha': key.split('/')[-2] if '/' in key else 'unknown'
        }
    )
    
    return {
        'status': 'ok',
        'glue_job_run_id': response['JobRunId']
    }
```

### Procesar archivo CSV liviano
```python
import boto3
import csv
import io
import json

s3 = boto3.client('s3')
sns = boto3.client('sns')

def lambda_handler(event, context):
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']
    
    # Leer CSV desde S3
    response = s3.get_object(Bucket=bucket, Key=key)
    content = response['Body'].read().decode('utf-8')
    reader = csv.DictReader(io.StringIO(content))
    
    errores = []
    total = 0
    
    for row in reader:
        total += 1
        # Validaciones
        if not row.get('cliente_id'):
            errores.append(f"Fila {total}: cliente_id vacío")
        if float(row.get('monto', 0)) < 0:
            errores.append(f"Fila {total}: monto negativo")
    
    if errores:
        sns.publish(
            TopicArn='arn:aws:sns:us-east-1:123:alertas-calidad',
            Subject=f'⚠️ Errores en {key}',
            Message=json.dumps({
                'archivo': key,
                'total_registros': total,
                'errores': errores[:20]  # Primeros 20 errores
            })
        )
    
    return {'total': total, 'errores': len(errores)}
```

---

## 3. 🔄 Step Functions (Orquestación Serverless)

```json
{
  "Comment": "Pipeline Bancario Diario",
  "StartAt": "ValidarArchivo",
  "States": {
    "ValidarArchivo": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123:function:validar-archivo",
      "Next": "ArchivoValido",
      "Catch": [{"ErrorEquals": ["States.ALL"], "Next": "NotificarError"}]
    },
    "ArchivoValido": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.valido",
          "BooleanEquals": true,
          "Next": "EjecutarGlueETL"
        }
      ],
      "Default": "NotificarError"
    },
    "EjecutarGlueETL": {
      "Type": "Task",
      "Resource": "arn:aws:states:::glue:startJobRun.sync",
      "Parameters": {
        "JobName": "etl-transacciones",
        "Arguments": {"--fecha.$": "$.fecha"}
      },
      "Next": "ValidarCalidad"
    },
    "ValidarCalidad": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:us-east-1:123:function:validar-calidad",
      "Next": "NotificarExito"
    },
    "NotificarExito": {
      "Type": "Task",
      "Resource": "arn:aws:states:::sns:publish",
      "Parameters": {
        "TopicArn": "arn:aws:sns:us-east-1:123:pipeline-ok",
        "Message": "✅ Pipeline completado exitosamente"
      },
      "End": true
    },
    "NotificarError": {
      "Type": "Task",
      "Resource": "arn:aws:states:::sns:publish",
      "Parameters": {
        "TopicArn": "arn:aws:sns:us-east-1:123:pipeline-errores",
        "Message.$": "States.Format('❌ Error: {}', $.error)"
      },
      "End": true
    }
  }
}
```

---

## 4. 🧪 EventBridge (Programar y Reaccionar)

```bash
# Regla programada: ejecutar Step Function cada día a las 6 AM
aws events put-rule \
  --name "pipeline-diario-6am" \
  --schedule-expression "cron(0 6 * * ? *)" \
  --state ENABLED

# Target: Step Function
aws events put-targets \
  --rule "pipeline-diario-6am" \
  --targets '[{"Id": "1", "Arn": "arn:aws:states:us-east-1:123:stateMachine:pipeline-bancario", "RoleArn": "arn:aws:iam::123:role/EventBridgeRole", "Input": "{\"fecha\": \"today\"}"}]'

# Regla por evento: Glue Job terminó
aws events put-rule \
  --name "glue-job-terminado" \
  --event-pattern '{
    "source": ["aws.glue"],
    "detail-type": ["Glue Job State Change"],
    "detail": {"jobName": ["etl-transacciones"], "state": ["SUCCEEDED", "FAILED"]}
  }'
```

---

## 5. 📋 CLI Lambda

```bash
# Crear función
aws lambda create-function \
  --function-name "validar-archivo" \
  --runtime python3.12 \
  --role "arn:aws:iam::123:role/LambdaRole" \
  --handler "handler.lambda_handler" \
  --zip-file fileb://funcion.zip \
  --timeout 300 \
  --memory-size 512

# Invocar manualmente
aws lambda invoke \
  --function-name "validar-archivo" \
  --payload '{"bucket":"banco-datalake","key":"test.csv"}' \
  output.json

# Ver logs
aws logs tail /aws/lambda/validar-archivo --follow

# Actualizar código
aws lambda update-function-code \
  --function-name "validar-archivo" \
  --zip-file fileb://funcion_v2.zip
```

---

## 🧭 Navegación

Vuelve al [[Índice AWS|Índice AWS]]
Relacionado: [[AWS Glue|Glue]] | [[AWS Kinesis|Kinesis]] | [[AWS S3 y Data Lake|S3]]
