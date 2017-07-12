## Apresentação

1. Gui
1. Pagar.me
1. diagrama: api -> gateway -> providers
1. diagrama: boleto sem registro
1. diagrama: boleto registrado
1. necessidade: microserviço **SUPERBOWLETO**

## Definição do Superbowleto

1. Requisitos
  - Ambientes identicos: live e sandbox
  - API CRUD
  - Banco de Dados
  - Retentativa com Fila
  - Retorno via Fila

1. Como deployar uma aplicação Node.js
  - AutoScalingGroup
  - Load Balancing
  - Monitoring
  - Service Discovery
  - Clusters
  - ...

1. Serverless for the win
  - AWS Lambda -> Function as a Service
  - API Gateway -> endpoints da API

1. Serverless Framework
  - serverless.yml
  - gera configurações com CloudFormation
  - cria e faz deploy das Lambdas e API Gateway
  - É possível criar outros recursos com Cloudformation

1. Mostra exemplo de serverless

## Descobertas e caminho das pedras

1. Como criar banco de dados, filas, IAM, etc?
  Usando Serverless + CloudFormation:
    - serverless permite criar com cloudformation
    - json/yaml com as configurações

  Terraform:
    - Toda infra do Pagar.me hoje é descrita com Terraform
    - Infrastructure as Code (modules, variables, interpolation, etc)
    - Execution plans (terraform plan)
    - etc

2. API Gateway público
  - Rotas são públicas. Alternativas:
    a) criar um custom authorizer (com Lambda + API Gateway)
    b) criar lógica de autenticação no superbowleto
    c) usar api keys do API Gateway

  - Decidimos usar as api_keys, mas o Serverless não tinha suporte a isso
  - Decidimos criar as coisas do API Gateway com Terraform
  - O que eram 3 linhas no Serverless, agora eram 80 com Terraform (mas abstraímos com módulos)

3. Cross-referencing de coisas
  - Role é criado pelo Terraform e usado pelo Serverless
  - Lambda é criado pelo Serverless e usado pelo Terraform

4. Senha do banco de dados
  - Premissa: qualquer pessoa da empresa pode ter acesso Read na Amazon
  - Como fazemos quando deployamos em uma máquina (arquivo de configuração na máquina)
  - Lambda: o código fica no S3 e qualquer pessoa com acesso de Read veria o código
  - A mesma coisa com environment variables

  - Alternativas:
    a) passar via ENVVAR encriptada e decriptar em tempo de execução.
    b) usar o Credstash e decriptar em tempo de execução.

  - Senha do banco ficava no terraform `.tfstate`
  - local-exec: cria o banco, cria senha aleatória, altera o banco e manda para o credstash

5. Lambda Environment Variables
  - Queremos passar como ENV VAR o db_host e os endereços do SQS
  - Cross-referencing se torna ainda mais difícil
  - Decidimos criar as Lambdas com Terraform e só fazer o deploy com Serverless
  - Serverless não atualiza código de Lambdas que já existem

## Deploy

1. Vamos fazer nosso deployer :)
  - faz o zip
  - sobre para o s3
  - atualiza o código das lambdas

2. pagarme/deployer :)

3. Deploy em contas separadas na Amazon

4. bunker infrastructure
  - faz ssh na máquina e roda terraform apply
  - faz o deploy usando deployer: deployer deploy --ref="master" --env "sandbox"
  - chatbot slack: sharon deploy superbowleto/master to production/live

## Futuro

- Monitoring
- Blue/Green Deployments
- venhapara@pagar.me


## Estrutura Problema x Solução

P: Criar outros recursos
S: Usar terraform para isso

P: API Gateway público
S: Usar API Keys

P: Serverless não tem (tinha) suporte à API Keys
S: Usar Terraform para criar API Gateway

P: Senha do banco de dados
S: Usar credstash

P: Cross-referencing de coisas
S: Criar tudo com Terraform e Serverless para deploy

P: Serverless não altera funções já criadas
S: Usar pagarme/deployer
