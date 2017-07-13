theme: simple
footer: **grvcoelho** @ pagar.me 2017
slidenumbers: true

# Serverless Microservice

## Como deployar um microserviço serverless

---

# Quem sou eu?

### :man: Guilherme Coelho
### :house_with_garden: Jundiaí
### :office: Stone.co

TODO: github.com/grvcoelho

---

## Pagar.me

<diagrama api-gateway-providers>

---

## Boleto

<diagrama boleto>

---

## Boleto Registrado

<diagrama boleto registrado>

---

# :football:

## Superbowleto

---

## Requisitos Superbowleto

- API Simples (CRUD)
- Salvar os dados em um banco de dados
- Fila para retentativa de registro de boleto
- Retorno via fila
- Ambientes live/sandbox

---

## <diagrama do Superbowleto>

---

## Tecnologia

- **Node.js**
- **Typescript** + **Webpack**
- **Postgresql**
- **AWS SQS**

---

## Como deployar uma API Node.js

---

## Como deployar uma API Node.js

- AutoScaling Group
- Load Balancing
- Monitoramento
- Service Discovery
- Clusters
- ...

^ 5 meses atrás, eu não tinha conta na Amazon e eu não sabia o quer era uma máquina EC2.

^ Quem Sou eu?
^ Entrei no Pagar.me 2 anos atrás para fazer jQuery
^ Há 4 meses estava fazendo coisas no core do Pagar.me
^ Não tinha nem conta na Amazon muito menos conhecimento de Infra

---

## Serverless

- AWS Lambda: Function as a Service
- API Gateway: endpoints da API
- AWS SQS: serviço de filas

^ A gente meio que que já tinha brincado com Serverless
^ Achamos que seria mais fácil de subir esta API usando Serverless

---

### Problema 1

## Como criar funções Lambda

---

### Solução 1

## Serverless Framework

---

## Serverless Framework

- `serverless.yml`
- Gera configurações com CloudFormation
- Faz o deploy das Lambdas e API Gateway
- Cria outros recursos com CloudFormation

---

## serverless.yml - http

```yaml
create-boleto:
  handler: dist/boleto.create
  events:
    - http:
        path: /boletos
        method: post
```

---

## serverless.yml - schedule

```yaml
process-boleto-queue:
  handler: dist/boleto.process
  events:
    - schedule: rate(5 minutes)
```

---

## serverless.yml - resultado

```yaml
service: superbowleto
stage: live
region: us-east-1

endpoints:
  POST - https://mc4evvg26b.execute-api.us-east-1.amazonaws.com/live/boletos
functions:
  create-boleto: live-superbowleto-create-boleto
  process-boleto-queue: live-superbowleto-process-boleto-queue
```

---

### Problema 2

## Criar outros recursos da aplicação

---

## Criar outros recursos da aplicação

- IAM: Roles, policies, permissions
- VPC: subnets, security_groups, route_tables
- SQS: queues
- RDS: database

---

## Serverless + CloudFormation

```yaml
resources:
 Resources:
   SuperbowletoQueue:
     Type: AWS::S3::SQS
     Properties:
       QueueName: boletos-to-register
```

---

### Solução 2

## Terraform - Infrastructure as Code

---

## Terraform

- Infrastructure as Code
- Execution Plans
- HCL, Variables, Interpolation, Modules, :sparkling_heart:
- Toda infra da Pagar.me hoje é Terraform :muscle:

^ Falar que eu posso colocar tudo dentro de ./application e criar os ambientes live/sandbox

---

## Terraform - Exemplo

```tcl
variable "delay" {
  type = "string"
}

resource "aws_queue" "superbowleto_queue" {
  queue_name = "boletos-to-register"
  delay_seconds = "${var.delay}"
}

output "queue_arn" {
  value = "${aws_queue.superbowleto_queue.arn}"
}
```

---

## Terraform - Módulos

```tcl
module "sandbox" {
  source  = "./application"
  network = "10.0.0.0/16"
  ...
}

module "live" {
  source  = "./application"
  network = "172.168.0.0/16"
  ...
}
```

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

---

## Usar Terraform para criar endpoints

- `rest_api`
- `api_resource`
- `api_method`
- `api_integration`
- `api_deployment`
- `lambda_permission`
- ...

---

## Usar Terraform para criar endpoints

```
module "create_endpoint" {
  source = "./api-endpoint"
  method = "POST"
  lambda = "${var.lambda}"
  path   = "${var.resource_path}"
}
```

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

^ TODO: colocar github do deployer aqui

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

### Problema 9

## Response time > 10s

---

## Response time > 10s

- Requests demorando constantemente 10, 11, 12 segundos
- Lambda esperar o Event Loop do Node se limpar
- Nós mantemos a conexão do banco de dados aberta

---

### Solução 9

## Alterar o comportamento da Lambda

---

## Alterar o comportamento da Lambda

- Lambda recebe `event`, `context` e `callback`
- `context.callbackWaitsForEmptyEventLoop`

---

### Problema 10

## Migração de banco de dados

---

### Solução 10

## Lambda que roda migrations

---

## Futuro

- Monitoring
- Blue/Green Deployments
- code@pagar.me
