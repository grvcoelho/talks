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

---

## <diagrama pagar.me -> api -> gateway>

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

## Superbowleto

<diagrama boleto registrado com superbowleto>

---

## Requisitos Superbowleto

- API Simples (CRUD)
- Salvar os dados em um banco de dados
- Fila para retentativa de registro de boleto
- Retorno via fila

---

## <diagrama do Superbowleto>

---

## Tecnologia

- **Node.js**
- **Typescript** + **Webpack**
- **Postgresql**
- **AWS SQS**

---

> TODO: Esse slide e o debaixo dá para melhorar

## API Node.js

> No início, iríamos fazer uma api em Node.js, porém estávamos reestruturando a maneira como a gente faz deploy no Pagar.me e não tínhamos muito braço de DevOps para fazer o deploy de uma aplicação.

---

> TODO: Esse slide e o de cima dá para melhorar

## Requisitos API Node.js

- AutoScaling Group
- Load Balancing
- Monitoramento
- Service Discovery
- ...

> Quem Sou eu?
> Entrei no Pagar.me 2 anos atrás para fazer jQuery
> Há 4 meses estava fazendo coisas no core do Pagar.me
> Não tinha nem conta na Amazon muito menos conhecimento de Infra

---

## Serverless

> Aplicações que utilizam FaaS (Function) or BaaS (Backend)
> A gente meio que que já tinha brincado com Serverless
> Achamos que seria mais fácil de subir esta API usando Serverless
> Serverless resolveria o problema de provisionar máquinas para o deploy da aplicação

> FIXME: Talvez falar de como funciona AWS Lambda

---

## Serverless Framework

- serverless.yml
- Gera configurações com CloudFormation
- Faz o deploy das Lambdas e API Gateway
- É possível criar recursos com CloudFormation

---

## serverless.yml

```yaml
create-boleto:
  handler: dist/boleto.create
  events:
    - http:
        path: /boletos
        method: post
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
```

---

##  AWS SQS, Banco de Dados, IAM, etc

---

## Criando SQS - Cloudformation

```yaml
resources:
 Resources:
   SuperbowletoQueue:
     Type: AWS::S3::SQS
     Properties:
       QueueName: boletos-to-register
```

---

## Terraform

---

## Terraform

> Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently.

- Infrastructure as Code
- Execution Plans
- HCL vs JSON
- Modules

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

---

## API Gateway

- Implementar autorização na mão
- Custom Authorizers
- API Keys

---

## API Gateway

Issue about usage plans https://github.com/serverless/serverless/pull/3819

> Nós pensamos em utilziar Terraform para criar banco de dados, IAM, SQS, etc e usar o Serverless para criar as funções Lambda e o API Gateway

---

TODO

1. Decidimos criar o api gateway na mão.
1. Mostra como ficou - módulos terraform
1. Outro problema que apareceu: cross-referencing entre Serverless e Terraform
1. de infra, estavamos uusando Serverless apenas para criar as Lambdas
1. Decidimos criar tudo com Terraform :) inclusive as Lambdas

---

TODO

1. Usaríamos o Serverless apenas para deployar a aplicação
2. Serverless não atualiza as funções (apenas as que ele mesmo cria)
3. Dissecando o Serverless: faz o zip, sobe para o s3 atualiza as Lambdas
4. Criamos o pagarme/deployer

---

TODO: encaixar:
  - vpc
  - lambda e vpc
  - como salvar a senha do banco de dados
  - migração do banco de dados


## TODO

- Downsides: cross-referencing entre Serverless e Terraform
- API Gateway público, precisávamos de API keys e o Serverless não tinha suporte a Planos.

- Aonde salvar a senha do banco de dados? Consul -> Vault -> KMS com Credstash


