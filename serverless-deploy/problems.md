# 1
P: Como criar as funções Lambda/API Gateway?
R: Serverless

# 2
P: Como criar outros recursos (database, sqs, iam, etc)
R: Cloudformation -> Terraform

# 3
P: API Gateway - Endpoints públicos
R: Usar API Keys do API Gateway

# 4
P: Serverless não suportava API Keys
R: Criar endpoints com Terraform também

# 5
P: Senha do banco de dados
R: Credstash

# 6
P: Terraform - Senha do banco de dados
R: Script para alterar senha do banco

# 6
P: Cross-reference
R: Criar tudo com Terraform

# 7
P: Serverless não atualiza funções existentes
R: Deployar sem o Serverless

---

### Problema 3

## API Gateway - Endpoints públicos

---

## API Gateway - Endpoints públicos

**Opções:**
a) adicionar regras de autenticação
b) criar um "custom authorizer"
c) usar API Keys do API Gateway

---

### Problema 4

## Serverless não suportava API Keys

---

## Serverless não suportava API Keys

- CloudFormation não tinha suporte a `Usage Plans`
- Criar `Usage Plan` no console da Amazon

---

### Solução 4

## Usar Terraform para criar endpoints

TODO: Descorrer aqui sobre o que aprendemos
  - 6 recursos precisam ser criados
  - Terraform: 80 linhas, mas dá para abstrair em módulos
  - etc

---

### Problema 5

## Senha do banco de dados

---

## Senha do banco de dados

- Todo mundo pode ter acesso **read** na Amazon
- **Hard-coded**: senha plain-text no S3, no Cloudformation e na Lambda
- **Env Var**: senha plain-text na Lambda

---

### Solução 5

## Salvar senha no Credstash

---

## Salvar senha no Credstash

TODO: diagrama: pega senha no banco de dados -> conecta -> executa função

---

### Problema 6

## Senha do banco de dados no `.tfstate`

---

## Senha do banco de dados no `.tfstate`

- `.tfstate` armazena informações do Terraform.
- Senha fica plain-text neste arquivo.
- Senha fica plain-text no arquivo `database.tf`

---

### Solução 6

## Script para atualizar banco de dados

```
resource "aws_db_instance" "database" {
  name = "superbowleto"
  password = "123456"
  ...

  provisioner "local-exec" {
    command = "sh update-database-password-and-send-to-credstash.sh"
  }
}
```

---

### Problema 7

## Cross-referencing entre ferramentas

---

## Cross-referencing entre ferramentas

- Role (`terraform`) -> Lambda (`serverless`)
- Lambda (`serverless`) -> API Gateway (`terraform`)
- Database (`terraform`) -> Lambda Var (`serverless`)

---

### Solução 7

## Terraform everything!

---

## Terraform everything!

- Criar as Lambdas também com Terraform.
- Utilizar Serverless apenas para deployar as funções.

---

## Problema 8

## Serverless não atualiza Lambdas já criadas

---

### Solução 8

## Criar um deployer

---

### Como deployar uma função Lambda

- Zip source files
- Upload para o S3
- Atualiza o código das funções

---

## Deployer

pagarme/deployer

---

## Deployer - Steps

- **SCM**: `git`
- **Build**: `rocker`
- **Deploy**: `nomad`, **`lambda`**

---

## Deployer - Lambda

```yml
scm:
  type: git
  repository: git@github.com:pagarme/superbowleto.git

build:
  type: rocker

deploy:
  type: lambda
  s3_bucket: deploy-bucket
  region: us-east-1
  package:
    - node_modules
    - handler.js

environment:
  live:
    functions:
      - create-boleto
      - update-boleto
```

---

## Deployer - Na prática

Run:
- `deployer deploy --ref "master" --env "live" superbowleto.yml`

Chatbot:
- `sharon deploy superbowleto/master to production/live`

---

## Futuro

- Monitoring
- Blue/Green Deployments
- code@pagar.me
