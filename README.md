# How to Create CrossAccount-Bucket-S3-Using-Two-Accounts

Sera utilizado 2 contas na AWS Contas A e Conta B

Passo 1 - CONTA-A-ID
Criar uma ROLE com a seguinte policy. (your-role-name-in-aws1)

IAM >> Roles >> <NOME DA ROLE> >> Permissions >> 

<iam_policy.json>


#########################################################################################################################

Passo 2 - CONTA-A-ID
Criar um Lambda com a role atachada (Change default execution role) Escolher a role ja criada!

<lambda.js>

OU

<lambda python 3.6>

#########################################################################################################################

Passo 3 - CONTA-B-ID

Criar um bucket <your-bucket-name> 

Colocar a policy no bucket criado
Amazon S3 >> Buckets >> Bucket policy

<bucket_policy.json>
