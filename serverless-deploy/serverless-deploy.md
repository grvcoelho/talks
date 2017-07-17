theme: simple
footer: **grvcoelho** @ pagar.me 2017
slidenumbers: true

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# Serverless Microservice

## Como deployar um microserviço serverless

---

![profile](https://user-images.githubusercontent.com/7416751/28267091-c4065702-6ace-11e7-9789-5060ec60aa7a.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Pagar.me

![inline xxgatewayxx](https://user-images.githubusercontent.com/7416751/28253086-7c3229a0-6a75-11e7-88b3-b514eed51813.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Boleto

![inline xxboleto](https://user-images.githubusercontent.com/7416751/28253085-7c3155ca-6a75-11e7-99cc-2f29b34de167.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Boleto Registrado

![inline xxboleto-registrado](https://user-images.githubusercontent.com/7416751/28253084-7c2eaf8c-6a75-11e7-9d6e-5b924e630b30.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# :football:

## Superbowleto

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Superbowleto

![inline xxsuperbowleto](https://user-images.githubusercontent.com/7416751/28253087-7c4f36d0-6a75-11e7-95c1-2943a6b6c618.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Requisitos Superbowleto

- API Simples (CRUD)
- Salvar os dados em um banco de dados
- Fila para retentativa de registro de boleto
- Retorno via fila
- Ambientes live/sandbox

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Tecnologia

- Node.js
- Typescript** + Webpack
- Postgresql
- AWS SQS

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Como deployar uma API Node.js

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Serverless

- AWS Lambda: Function as a Service
- API Gateway: endpoints da API
- AWS SQS: serviço de filas

^ A gente meio que que já tinha brincado com Serverless
^ Achamos que seria mais fácil de subir esta API usando Serverless

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 1

## Como criar funções Lambda

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 1

## Serverless Framework

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Serverless Framework

- `serverless.yml`
- Gera configurações com CloudFormation
- Faz o deploy das Lambdas e API Gateway
- Cria outros recursos com CloudFormation

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## serverless.yml - schedule

```yaml
process-boleto-queue:
  handler: dist/boleto.process
  events:
    - schedule: rate(5 minutes)
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 2

## Criar outros recursos da aplicação

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Criar outros recursos da aplicação

- IAM: Roles, policies, permissions
- VPC: subnets, security\_groups, route\_tables
- SQS: queues
- RDS: database

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 2

## Terraform - Infrastructure as Code

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Terraform

- Infrastructure as Code
- Execution Plans
- HCL, Variables, Interpolation, Modules, :sparkling_heart:
- Toda infra da Pagar.me hoje é Terraform :muscle:

^ Falar que eu posso colocar tudo dentro de ./application e criar os ambientes live/sandbox

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 3

## API Gateway - Endpoints públicos

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## API Gateway - Endpoints públicos

**Opções:**
a) adicionar regras de autenticação
b) criar um "custom authorizer"
c) usar API Keys do API Gateway

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 4

## Serverless não suportava API Keys

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Serverless não suportava API Keys

- CloudFormation não tinha suporte a `Usage Plans`
- Criar `Usage Plan` no console da Amazon

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 4

## Usar Terraform para criar endpoints

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Usar Terraform para criar endpoints

- `rest_api`
- `api_resource`
- `api_method`
- `api_integration`
- `api_deployment`
- `lambda_permission`
- ...

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Usar Terraform para criar endpoints

```ruby
module "create_endpoint" {
  source = "./api-endpoint"
  method = "POST"
  lambda = "${var.lambda}"
  path   = "${var.resource_path}"
}
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 5

## Senha do banco de dados

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Senha do banco de dados

- Todo mundo pode ter acesso **read** na Amazon
- **Hard-coded**: senha plain-text no S3, no Cloudformation e na Lambda
- **Env Var**: senha plain-text na Lambda

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 5

## Salvar senha no Credstash

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Salvar senha no Credstash

Deploy:

```
$ credstash put sandbox/database/password pass123
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Salvar senha no Credstash

Aplicação:

```javascript
function init () {
  return Promise.resolve()
    .then(() => credstash.get('sandbox/database/password'))
    .then(initDatabase)
    .then(runFunction)
}
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 6

## Senha do banco de dados no `.tfstate`

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Senha do banco de dados no `.tfstate`

- `.tfstate` armazena informações do Terraform.
- Senha fica plain-text neste arquivo.
- Senha fica plain-text no arquivo `database.tf`

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 6

## Script para atualizar banco de dados

```ruby
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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 7

## Cross-referencing entre ferramentas

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Cross-referencing entre ferramentas

- Role (`terraform`) -> Lambda (`serverless`)
- Lambda (`serverless`) -> API Gateway (`terraform`)
- Database (`terraform`) -> Lambda Var (`serverless`)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 7

## Terraform everything!

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Terraform everything!

- Criar as Lambdas também com Terraform.
- Utilizar Serverless apenas para deployar as funções.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Problema 8

## Serverless não atualiza Lambdas já criadas

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 8

## Criar um deployer

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Como deployar uma função Lambda

- Zip source files
- Upload para o S3
- Atualiza o código das funções

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

![inline](https://user-images.githubusercontent.com/7416751/28267282-c397d77c-6acf-11e7-9d1e-b2629103b338.png)

## [github.com/pagarme/deployer](pagarme/deployer)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Deployer - Steps

- **SCM**: `git`
- **Build**: `rocker`
- **Deploy**: `nomad`, **`lambda`**

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Deployer - Na prática

Run:
- `deployer deploy --ref "master" --env "live" superbowleto.yml`

Chatbot:
- `sharon deploy superbowleto/master to production/live`

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 9

## Response time > 10s

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Response time > 10s

- Requests demorando constantemente 10, 11, 12 segundos
- Lambda esperar o Event Loop do Node se limpar
- Nós mantemos a conexão do banco de dados aberta

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 9

## Alterar o comportamento da Lambda

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Alterar o comportamento da Lambda

- Lambda recebe `event`, `context` e `callback`
- `context.callbackWaitsForEmptyEventLoop`

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Problema 10

## Migração de banco de dados

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

### Solução 10

## Lambda que roda migrations

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Futuro

- Monitoring
- Blue/Green Deployments
- code@pagar.me

---

## Superbowleto Opensource

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

![inline](https://user-images.githubusercontent.com/7416751/28267341-18c57402-6ad0-11e7-81b4-0f802d999efb.png)

## [github.com/pagarme/superbowleto](https://github.com/pagarme/superbowleto)
