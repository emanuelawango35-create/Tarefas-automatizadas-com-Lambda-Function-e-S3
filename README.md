# Tarefas-automatizadas-com-Lambda-Function-e-S3
🚀 Automatizando Tarefas com AWS Lambda e S3
📌 O que é AWS Lambda?
O AWS Lambda é um serviço de computação serverless que permite executar código em resposta a eventos, sem precisar gerenciar servidores. Ele é ideal para automações simples e escaláveis.
📦 O que é o Amazon S3?
O Amazon S3 (Simple Storage Service) é um serviço de armazenamento de objetos. Ele é usado para guardar arquivos como imagens, documentos, logs, backups, etc.

🔄 Como Lambda e S3 trabalham juntos?
Você pode configurar o S3 para disparar uma função Lambda automaticamente quando um evento ocorre, como:

Upload de um novo arquivo
Exclusão de um objeto
Alteração de metadados

🧪 Exemplo 1: Redimensionar Imagens Automaticamente
Cenário:
Você tem um bucket S3 onde usuários fazem upload de imagens. Sempre que uma imagem é enviada, você quer gerar uma versão redimensionada (thumbnail).
Fluxo:

Usuário envia imagem para o bucket uploads/
Evento dispara a função Lambda
Lambda redimensiona a imagem
Salva a nova imagem em thumbnails/

Código básico (Python):

import boto3
from PIL import Image
import io

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    response = s3.get_object(Bucket=bucket, Key=key)
    image = Image.open(response['Body'])
    image.thumbnail((100, 100))

    buffer = io.BytesIO()
    image.save(buffer, 'JPEG')
    buffer.seek(0)

    s3.put_object(Bucket=bucket, Key='thumbnails/' + key, Body=buffer, ContentType='image/jpeg')
🧪 Exemplo 2: Validação de Arquivos CSV
Cenário:
Você recebe arquivos CSV com dados de clientes. Sempre que um novo arquivo é enviado, você quer validar se ele tem as colunas obrigatórias.
Fluxo:

Upload no bucket csv-input/
Lambda verifica se o CSV tem colunas: name, email, phone
Se estiver válido, move para csv-valid/; senão, para csv-error/

Código básico:


import boto3
import csv
import io

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    response = s3.get_object(Bucket=b content = response['Body'].read().decode('utf-8')
    reader = csv.DictReader(io.StringIO(content))

    required_columns = {'name', 'email', 'phone'}
    if required_columns.issubset(reader.fieldnames):
        destination = 'csv-valid/' + key
    else:
        destination = 'csv-error/' + key

    s3.copy_object(Bucket=bucket, CopySource={'Bucket': bucket, 'Key': key}, Key=destination)
    s3.delete_object(Bucket=bucket, Key=key)

🧠 Boas Práticas

Use IAM Roles com permissões mínimas para Lambda acessar S3.
Configure timeout e memória adequados para sua função.
Use CloudWatch Logs para monitorar e depurar.
Teste localmente com ferramentas como SAM CLI ou Lambda Powertools.

📈 Casos de Uso Comuns

Processamento de logs
Geração de relatórios
Conversão de arquivos
Notificações por e-mail/SNS
Integração com outros serviços (DynamoDB, SQS, etc.)


SAIBA MAIS: https://docs.aws.amazon.com/pt_br/AmazonS3/latest/userguide/olap-using-cfn-template.html
    

    

