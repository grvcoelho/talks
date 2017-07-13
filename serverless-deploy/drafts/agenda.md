# Agenda

Encaixar:
  - Typescript

- Introdução
  1. Apresentação (Gui e Pagar.me)
  1. Fluxo do Boleto
  1. Fluxo do Boleto Registrado
  1. Microsserviço: Superbowleto

- Definição Superbowleto
  1. API simples (CRUD)
  1. Salva os dados no PostgreSQL
  1. Fila para retentar o registro de boletos
  1. Retorno via SQS

- História do Superbowleto
  1. Começou com uma API Node.js
  1. Não tinhamos DevOps.
  1. Deploy de aplicação: AutoScalingGroup, Load Balancing, Monitoramento, Service Discovery, etc.
  1. Serverless Framework :)

- Arquitetura Serverless
  1. serverless.yml
  1. Gera um arquivo CloudFormation
  1. Faz o deploy das Lambdas e API Gateway
  1. É possível provisionar outros recursos usando sintaxe CloudFormation

  1. Usar Serverless Framework para criar as funções Lambda
  1. Usar Serverless Framework para criar as API Gateway

- Problemas
  - Criar banco de dados com CloudFormation
  - Criar VPC com CloudFormation
  - API Gateway público
    - Alternativas: implementar autenticação, usar api_keys, implementar custom lambda authorizer.
  - Senha do banco de dados: KMS

- Deploy
  1. pagarme/deployer
  1. Arquivo de configuração

  ```yaml
  deploy:
    type: lambda
    region: us-east-1
    project_name: superbowleto
    s3_bucket: superbowleto-deploy-bucket
    package:
      - dist
      - node_modules
    functions:
      - create-boleto
      - index-boleto
      - show-boleto
      - update-boleto
      - register-boleto
      - process-boletos-to-register
      - migrate-database
  ```

  1. Build com Rockerfile
  1. Deploy lambda
