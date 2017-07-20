theme: simple
build-lists: false

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# Serverless Microservice

## Como deployar um microserviço serverless

^ Sobre um projeto em que estive trabalhando nos últimos meses

^ Falar sobre os problemas e aprendizados que tivemos no decorrer do projeto

^ Disclaimer 1: a ordem que as coisas aparecem não são arbitrárias, é a ordem cronológica do projeto.

^ Disclaimer 2: quando enviei a talk 2 meses atrás, não tinha a menor ideia de como deployar um microsserviço em serverless.

---

![profile](https://user-images.githubusercontent.com/7416751/28267091-c4065702-6ace-11e7-9789-5060ec60aa7a.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![stone-pagarme inline 100%](https://user-images.githubusercontent.com/7416751/28395390-099c83e8-6cca-11e7-9a7a-dc9dfc9aadbb.png)

^ Pagar.me resolve problemas de pagamento, especialmente no meio digital.

^ Infraestrutura para receber pagamentos e gestão financeira.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Pagar.me

^ Antes de falar do microsserviço, explicar o fluxo de uma transação no Pagar.me

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Pagar.me

![inline xxgatewayxx](https://user-images.githubusercontent.com/7416751/28253086-7c3229a0-6a75-11e7-88b3-b514eed51813.png)

^ Fluxo generalista: cartão de crédito

^ Nosso cliente nos envia uma request para a nossa API

^ Cliente vê API, mas internamente temos API e Gateway. API handleia requests e controla o fluxo. Gateway entre outras coisas, se comunica com o restante da cadeia financeira.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Boleto

^ O fluxo que importa para gente hoje, é o fluxo de uma transação de boleto.

^ Até recentemente, tinhamos o chamado Boleto não-registrado.

^ Boleto é simplesmente um número (código de barras) que contém informações de pagamento.

^ Banco só tem conhecimento do boleto quando o mesmo é pago.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Boleto

![inline xxboleto](https://user-images.githubusercontent.com/7416751/28253085-7c3155ca-6a75-11e7-99cc-2f29b34de167.png)

^ O fluxo que importa para gente hoje, é o fluxo de uma transação de boleto.

^ Até recentemente, tinhamos o chamado Boleto não-registrado.

^ Boleto é simplesmente um número (código de barras) que contém informações de pagamento.

^ Banco só tem conhecimento do boleto quando o mesmo é pago.

^ Então o gateway só precisa criar o boleto dentro do banco de dados.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Boleto Registrado

^ Porém com regulamentações que estão acontecendo recentemente, surgiu o Boleto Registrado

^ Além de gerar o boleto, é preciso registrá-lo na CIP (via um banco)

^ CIP: Camara Interbancaria de Pagamentos

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Fluxo Transação - Boleto Registrado

![inline xxboleto-registrado](https://user-images.githubusercontent.com/7416751/28253084-7c2eaf8c-6a75-11e7-9d6e-5b924e630b30.png)

^ Porém com regulamentações que estão acontecendo recentemente, surgiu o Boleto Registrado

^ Além de gerar o boleto, é preciso registrá-lo na CIP (via um banco)

^ Precisamos se comunicar com o banco para registrar o boleto

^ Poderiamos implementar isso no Gateway, mas decidimos aproveitar a oportunidade para extrair tudo que é de boleto e criar um microsserviço que cuide apenas disso.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# :football:

## Superbowleto

^ Superbowleto é um microsserviço que a gente criou

^ Cuja responsabilidade é criar, registrar e conciliar boletos.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Superbowleto

![inline xxsuperbowleto](https://user-images.githubusercontent.com/7416751/28253087-7c4f36d0-6a75-11e7-95c1-2943a6b6c618.png)

^ Chega a request na API, vai para o Gateway.

^ O Gateway manda uma request para o Superbowleto como se fosse um provider da cadeia financeira.

^ Superbowleto se comunica com a cadeia financeira para registrar os boletos.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :cd: Requisitos Superbowleto

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Requisitos Superbowleto

- API Simples (CRUD)
- Salvar os dados em um banco de dados
- Fila para retentativa de registro de boleto
- Retorno via fila
- Ambientes idênticos live/sandbox

^ Retorno via fila: retorna boletos para uma fila SQS do Gateway
^ Tudo no Pagar.me é duplicado em live e sandbox

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :microscope: Tecnologia

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![nodejs-logo inline 100%](https://user-images.githubusercontent.com/7416751/28395686-2552161e-6ccc-11e7-85ab-62d6e4a0227a.png)

^ Node.js como tecnologia para o microsserviço.

^ Muito porque era a nossa maior expertise naquele momento.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![typescript inline 100%](https://user-images.githubusercontent.com/7416751/28395718-72a7aed8-6ccc-11e7-80fb-c63caa311b30.png)

^ Typescript para escrever Javascript mais seguro com Tipos

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![webpack inline 35%](https://user-images.githubusercontent.com/7416751/28395747-ab7590cc-6ccc-11e7-9a4e-c1a7de8bf060.png)

^ Webpack para buildar o nosso app: compilar typescript, gerenciar assets, etc

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![postgresql-logo inline 80%](https://user-images.githubusercontent.com/7416751/28395774-d7d788b4-6ccc-11e7-9ede-3476ef968ddd.png)

^ Postgresql: como banco de dados.

^ Também nossa maior expertise

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![sqs inline 35%](https://user-images.githubusercontent.com/7416751/28395807-1b61da26-6ccd-11e7-9d79-e6b6b36f48f6.png)

## AWS SQS

^ SQS: Como sistema de filas, por ser simples de usar e barato

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :rocket: Deployando uma API Node.js

^ A gente começou criando uma API com Node.js + Express

^ Mas esbarramos na dificuldade de deployar isso

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Deployando uma API Node.js

- AutoScaling Group
- Load Balancing
- Clusters
- Service Discovery
- Monitoramento
- ...

^ Equipe reduzida: só eu

^ Eu não tinha nenhum conhecimento de DevOps ou Infraestrutura (não tinha nem conta na Amazon na época)

^ Aí alguém deu a a ideia de fazer algo com...... Serverless

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :zap: Serverless Architecture

^ Tema que estava sendo bem discutido na época

^ Tinha bastante coisa (tooling) sendo desenvolvida

^ Parecia simplificar nossos problemas com infraestrutura

^ A gente meio que que já tinha brincado com Serverless em produção (Integração Plataforma) (vtext)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![sqs inline 35%](https://user-images.githubusercontent.com/7416751/28395807-1b61da26-6ccd-11e7-9d79-e6b6b36f48f6.png)

## AWS SQS - Filas

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![api-gateway inline 28%](http://kinlane-productions.s3.amazonaws.com/api-evangelist-site/blog/aws-api-gateway-icon.png)

## API Gateway - Endpoints

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![api-gateway inline 20%](http://2ndwatch.com/wp-content/uploads/2016/05/AWS-Lambda.png)

## AWS Lambda - Functions

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Como criar funções Lambda

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## ![serverless-framwork inline 80%](https://user-images.githubusercontent.com/7416751/28384097-c6fd1ae6-6c99-11e7-9b2c-dda86989e73b.png)

^ Framework que foi criado para facilitar a criação de aplicações serverless
^ Tinham acabado de lançar a v1
^ Estavam implementando muitas features muito rápido

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## serverless deploy

Deploy:

```
$ serverless deploy
```

Resultado:

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

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :computer: Criar outros recursos da aplicação

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Criar outros recursos da aplicação

- IAM: Roles, policies, permissions
- VPC: subnets, security\_groups, route\_tables
- SQS: queues
- RDS: database

^ A role que a lambda precisa para executar código com todas as policies e permissões

^ VPC para gerenciar subredes e regras de segurança na amazon

^ RDS para a gente criar o banco de dados Postgres

^ SQS para criarmos as filas

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Serverless + CloudFormation

```yaml
resources:
 Resources:
   SuperbowletoQueue:
     Type: AWS::SQS::Queue
     Properties:
       QueueName: boletos-to-register
```

^ Por baixo dos panos o Serverless gera arquivos CloudFormation

^ Te permite criar outros recursos usando CloudFormation (json ou yaml)

^ Mas a gente tinha uma alternativa melhor

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

![terraform inline 100%](https://user-images.githubusercontent.com/7416751/28382292-085d1cee-6c94-11e7-8e14-ffd8371be2bd.png)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Terraform

- Infrastructure as Code :pager:
- Execution Plans + `.tfstate` :clipboard:
- HCL, Variables, Interpolation, Modules, :sparkling_heart:
- Toda infra da Pagar.me hoje é Terraform :muscle:

^ Terraform é uma ferramenta da Hashicorp que permite criar sua infraestrutura, descrevendo-a como código

^ Execution Plans permitem ver as mudanças na sua infra, antes de aplica-las

^ HCL > abismo > json ou yaml

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Terraform - Exemplo

```ruby
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

## Terraform - Apply

```sh
# Mostra tudo que vai mudar
$ terraform plan

# Aplica as mudanças
$ terraform apply
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

^ O melhor de tudo: podemos abstrair em módulos (pastas) toda a nossa aplicação e duplicar para ambientes de live e sandbox

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :globe_with_meridians: API Gateway - Endpoints públicos

^ Não tinhamos implementado autenticação no microsserviço
^ Endpoints do API Gateways são sempre públicos

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## API Gateway - Opções

- :x: Adicionar regras de autenticação
- :x: Criar um "custom authorizer"
- :white_check_mark: Usar API Keys do API Gateway

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Serverless não suportava API Keys

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Serverless não suportava API Keys

- CloudFormation não tinha suporte a `Usage Plans` [^1]
- Criar `Usage Plan` no console da Amazon

[^1]: Problema corrigido 29 dias atrás: https://github.com/serverless/serverless/pull/3819

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

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

^ O que eram três linhas com Serverless agora eram 80 com terraform para um endpoint

^ Diversos recursos que precisam ser criados eram abstraidos pelo Serverless

^ Felizmente, podemos usar novamente os módulos do Terraform

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

^ Diversos recursos que precisam ser criados eram abstraidos pelo Serverless

^ Felizmente, podemos usar novamente os módulos do Terraform

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :key: Senha do banco de dados

^ Quase tudo na Amazon é controlado via permissões.

^ SQS: tem que dar permissão para a Lambda enviar mensagens para uma fila

^ RDS é diferente: tem usuário e senha. Problema é: aonde salvar esta senha?

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Senha do banco de dados

- :globe_with_meridians: **Premissa**: Todo mundo pode ter acesso **read** na Amazon
- :x: **Hard-coded no código**: senha plain-text no S3, no Cloudformation e na Lambda
- :x: **Env Var**: senha plain-text na Lambda

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :closed_lock_with_key: Salvar senha no Credstash

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Credstash

- Banco de credenciais
- Key-Value store
- KMS + DynamoDB

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Salvar senha no Credstash

Deploy:

```
$ credstash put sandbox/database/password pass123
```

^ Basicamente, quando criarmos um banco de dados, a gente salva a senha no Credstash que depois será usada pela aplicaç˜åo

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

^ Na aplicação, antes de se conectar com o banco de dados e rodar e executar a Lambda, nós pegamos a senha do credstash

^ Deixamos a senha armazenada em cache para não precisar fazer isso toda hora.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :key: Senha do banco de dados no `.tfstate`

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## `.tfstate`

- Representação da sua infraestrutura :clipboard:
- Sem criptografia :cry:
- Disponível no S3 (state\_lock) :globe_with_meridians:

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Script para atualizar banco de dados

```go
// Gera uma senha aleatória
password = generatePassword()

// Atualiza a instância do banco de dados
aws.updateDatabasePassword(password)

// Salva a senha no credstash
credstash.put('database/password', password)
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Terraform - Local Provisioner

```ruby
resource "aws_db_instance" "database" {
  engine   = "postgres"
  username = "superbowleto"
  password = "touchdown1!"
  # ...

  provisioner "local-exec" {
    command = "${path.module}/update-db-password.sh"
  }
}
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :rotating_light: Cross-referencing entre ferramentas

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Cross-referencing entre ferramentas

- Role (`terraform`) -> Lambda (`serverless`)
- Lambda (`serverless`) -> API Gateway (`terraform`)
- Database (`terraform`) -> Lambda Var (`serverless`)

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :zap: Terraform everything!

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Terraform everything!

- Criar as Lambdas também com Terraform.
- Utilizar Serverless apenas para deployar as funções.

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :x: Serverless não atualiza Lambdas já criadas

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :pager:
## Fazer o nosso próprio deployer de Lambdas

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

^ A gente já tinha um deployer que usávamos para deploar aplicações com Nomad

^ Implementamos o deploy de Lambda no deployer

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Deployer - Steps

- **SCM**: `git`
- **Build**: `rocker`
- **Deploy**: `nomad`, **`lambda`**

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## deploy.yml

```yml
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

## Deployer deploy

Deploy:

```ruby
deployer deploy --ref "master" --env "live" superbowleto.yml
````

Chatbot:

```ruby
sharon deploy superbowleto/master to production/live
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :turtle: Response time > 10s

^ Uma vez que tinhamos deployado, fomos testar e estava tudo dando timeout

^ Aumentamos o timeout da lambda de 10 para 30 segundos

^ Percebemos que todas as requests estavam demorando ~10-11segundos

^ Lamba tem o tempo de warm-up, mas não parceia ser isso

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Response time > 10s

- Requests demorando constantemente 10, 11, 12 segundos
- Lambda espera o Event Loop do Node estar limpo
- Nós mantemos a conexão do banco de dados aberta (by design)

^ Nós mantemos a conexão do banco de dados aberta para não precisarmos nos conectar a cada vez que a lambda é executada

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :rotating_light: Alterar o comportamento da Lambda

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Alterar o comportamento da Lambda

- `context.callbackWaitsForEmptyEventLoop`

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Alterar o comportamento da Lambda

```javascript
export const handler = (event, context, callback) => {
  context.callbackWaitsForEmptyEventLoop = false

  // ...
  callback(null, 'Success!')
}
```

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Migração de banco de dados

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :rocket: Lambda que roda migrations

^ Criamos uma Lambda que roda migrations

^ Rodamos as migrations após o deploy

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## :crystal_ball: Futuro

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

## Futuro

- :telescope: Monitoramento
- :book: Otimizações
- :roller_coaster: Escalabilidade

^ Projeto está rodando a pouco tempo, temos muito ainda a aprender

^ Por fim: gostaria de falar sobre algo bem legal que está acontecendo na nossa empresa a Stone.co

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![stone-co inline 90%](https://user-images.githubusercontent.com/7416751/28396257-a37fc8e8-6cd0-11e7-9561-3063a0370df5.png)

## stone.co + Open Source = :green_heart:

^ Nós queremos melhorar o desenvolvimento no Brasil e ajudar o desenvolvedor a se melhorar. E a gente acredita que a gente pode fazer isso impulsionando a comunidade Open Source

^ Empresas normalmente lançam "tooling" opensource como o nosso deployer. É difícil ver algum produto core ser open source.

^ Nós vamos fazer algo um pouco diferente hoje:

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

![inline](https://user-images.githubusercontent.com/7416751/28267341-18c57402-6ad0-11e7-81b4-0f802d999efb.png)

## [github.com/pagarme/superbowleto](https://github.com/pagarme/superbowleto)

^ Nós estamos lançando opensource esse nosso microsserviço de boletos que está rodando em produção.

^ Nós acreditamos que open source não é sobre código aberto, mas sobre PESSOAS

^ Eu gostaria de convidar todo mundo a dar uma olhada no projeto, analisar as decisões que a gente tomou e dialogar com a gente.

^ Vamos mudar o desenvolvimento no Brasil!!!

---

![background](https://user-images.githubusercontent.com/7416751/28267089-c3fe87fc-6ace-11e7-9ad7-70f25b05d5a6.png)

# ![inline 50%](https://avatars1.githubusercontent.com/u/7416751?v=4&s=460)

## Obrigado :green_heart:

### [github.com/grvcoelho](github.com/grvcoelho)
