# How to Create CrossAccount-Bucket-S3-Using-Two-Accounts

Sera utilizado 2 contas na AWS Contas A e Conta B

Passo 1 - CONTA-A-ID
Criar uma ROLE com a seguinte policy. (your-role-name-in-aws1)

IAM >> Roles >> <NOME DA ROLE> >> Permissions >> 

(iam_policy.json)
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Effect": "Allow",
          "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:PutObjectAcl"
          ],
          "Resource": "arn:aws:s3:::your-bucket-name/*"
      }
  ]
}

#########################################################################################################################

Passo 2 - CONTA-A-ID
Criar um Lambda com a role atachada (Change default execution role) Escolher a role ja criada!

lambda.js 

const AWS = require('aws-sdk');
AWS.config.update({
  region: 'us-east-1'
})

const s3 = new AWS.S3();

exports.handler = async () => {
  const fileContent = 'hellooooo, this is testing';
  const params = {
    Bucket: 'your-bucket-name',
    Key: 'data/test.txt',
    ACL: 'bucket-owner-full-control',
    Body: fileContent,
    ContentEncoding: 'utf8'
  }

  await s3.putObject(params).prom.then(() => {
    console.log('Successfully uploaded file to S3');
  }, (error) => {
    console.error('Error: ', error);
  });
}

OU

lambda python 3.6

import json
import boto3

def lambda_handler(event, context):
   
   #0 - VARIAVEIS
   local = "/tmp/arquivo.txt"
   nome_arquivo = "arquivoS3.txt"
   nome_do_s3 = "YOUR-BUCKET"
   
   #1 - Criar ARQUIVO
   arquivo = open(local, 'w')
   arquivo.write('linha 1 \n')
   arquivo.write('linha 2 \n')
   arquivo.write('linha 3 \n')
   arquivo.close()
   
   #2 - Realiza Upload
   s3 = boto3.client('s3')
   with open (local, "rb") as file:
       s3.upload_fileobj(file, nome_do_s3, nome_arquivo)

#########################################################################################################################

Passo 3 - CONTA-B-ID

Criar um bucket <your-bucket-name> 

Colocar a policy no bucket criado
Amazon S3 >> Buckets >> Bucket policy

bucket_policy.json

{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Effect": "Allow",
          "Principal": {
              "AWS": "arn:aws:iam::CONTA-A-ID:role/your-role-name-in-aws1"
          },
          "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:PutObjectAcl"
          ],
          "Resource": "arn:aws:s3:::your-bucket-name/*"
      }
  ]
}
