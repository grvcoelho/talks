footer: **grvcoelho** @ pagar.me 2017
slidenumbers: true

# Serverless Microservice

## Como deployar um microserviço serverless

---

# Quem sou eu?

### :man: Guilherme Coelho
### :house_with_garden: Jundiaí
### :office: Pagar.me

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

> 5 meses atrás, eu não tinha conta na Amazon e eu não sabia o quer era uma máquina EC2.

> Quem Sou eu?
> Entrei no Pagar.me 2 anos atrás para fazer jQuery
> Há 4 meses estava fazendo coisas no core do Pagar.me
> Não tinha nem conta na Amazon muito menos conhecimento de Infra

---

## Serverless

- AWS Lambda: Function as a Service
- API Gateway: endpoints da API
- AWS SQS: serviço de filas

> A gente meio que que já tinha brincado com Serverless
> Achamos que seria mais fácil de subir esta API usando Serverless

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

## Outros recursos da aplicação

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

## Terraform - Infrastructure as Code

---

## Terraform

- Infrastructure as Code
- Execution Plans
- HCL, Variables, Interpolation, Modules, :sparkling_heart:
- Toda infra da Pagar.me hoje é Terraform :muscle:

<!-- TODO: Transformar isso em um slide para cada tópico -->
> Falar que eu posso colocar tudo dentro de ./application e criar os ambientes live/sandbox

---

## Criando SQS - Terraform

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

## Terraform && Serverless

- Serverless para criar e deployar as funções Lambda + API Gateway
- Terraform para todo o resto: Banco de Dados, Filas, Permissões, Network, etc.

Issue about usage plans https://github.com/serverless/serverless/pull/3819

> Nós pensamos em utilziar Terraform para criar banco de dados, IAM, SQS, etc e usar o Serverless para criar as funções Lambda e o API Gateway


## TODO

- Downsides: cross-referencing entre Serverless e Terraform
- API Gateway público, precisávamos de API keys e o Serverless não tinha suporte a Planos.

- Aonde salvar a senha do banco de dados? Consul -> Vault -> KMS com Credstash

- Deploy :)


