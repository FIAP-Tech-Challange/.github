# 🍔 Sistema de Autoatendimento - Lanchonete

## 📖 Visão Geral do Projeto

Este projeto representa a **Fase 3** do desenvolvimento de um sistema completo de autoatendimento para lanchonetes, implementando melhorias significativas em arquitetura, segurança e práticas de desenvolvimento. O sistema utiliza tecnologias modernas de nuvem (AWS) e segue as melhores práticas de DevOps.

### 🎯 Principais Melhorias da Fase 3

- **🚪 API Gateway + Serverless**: Implementação de autenticação via Lambda com validação por CPF
- **🏗️ Arquitetura**: Segregação de códigos em repositórios especializados
- **⚙️ CI/CD Avançado**: Deploy automatizado com proteção de branches e uso de Terraform
- **🗄️ Banco de Dados Melhorado**: PostgreSQL com modelagem otimizada e documentação completa
- **☁️ Infraestrutura Cloud**: Utilização de serviços serverless e cloud da AWS

## 🏛️ Arquitetura do Sistema

### Diagrama Geral da Arquitetura

```mermaid
graph TB
    subgraph "Frontend"
        WEB[Web/Mobile App]
        TOTEM[Totens de Autoatendimento]
    end

    subgraph "AWS Cloud"
        subgraph "API Layer"
            APIGW[API Gateway]
            LAMBDA[Lambda Authorizer]
        end

        subgraph "Compute"
            EKS[EKS Cluster]
            PODS[App Service Pods]
        end

        subgraph "Data Layer"
            RDS[(PostgreSQL RDS)]
            SSM[Systems Manager]
        end

        subgraph "CI/CD"
            ECR[Container Registry]
            S3[Terraform State]
        end
    end

    WEB --> APIGW
    APIGW --> LAMBDA
    LAMBDA --> RDS
    APIGW --> EKS
    TOTEM --> EKS
    EKS --> PODS
    PODS --> RDS
    PODS --> SSM

    style APIGW fill:#e1f5fe
    style LAMBDA fill:#fff3e0
    style EKS fill:#e8f5e8
    style RDS fill:#fce4ec
```

### Fluxo de Autenticação

```mermaid
sequenceDiagram
    participant U as Cliente (Web/Mobile)
    participant T as Totem
    participant G as API Gateway
    participant L as Lambda Authorizer
    participant A as App Service
    participant D as PostgreSQL

    Note over U,G: Fluxo de Usuários Externos
    U->>G: Request com CPF no header
    G->>L: Validação do token/CPF
    L->>D: Consulta dados do cliente
    D-->>L: Dados do cliente
    L-->>G: Token JWT válido
    G->>A: Request autorizado

    Note over T,A: Fluxo de Totens (Acesso Direto)
    T->>A: Request com token de totem
    A->>A: Validação do token interno
    A->>D: Operações no banco
    D-->>A: Resultado
    A-->>T: Response
    A-->>G: Response (usuários)
    G-->>U: Response final
```

## 📁 Estrutura de Repositórios

O projeto está organizado em **4 repositórios especializados**, cada um com responsabilidades específicas:

### 1. 🗄️ Infraestrutura do Banco de Dados

**Repositório**: `infra-db-terraform`

- **Função**: Provisionamento da infraestrutura de banco PostgreSQL RDS
- **Tecnologias**: Terraform, AWS RDS, Systems Manager
- **Responsabilidades**:
  - Configuração do PostgreSQL RDS Multi-AZ
  - Gerenciamento de credenciais via SSM
  - Backup automático e monitoramento
  - Configurações de segurança e rede

### 2. 🚀 Serviço Lambda

**Repositório**: `lambda-service`

- **Função**: Microserviço serverless para verificação de clientes
- **Tecnologias**: Node.js, TypeScript, Serverless Framework
- **Responsabilidades**:
  - Validação de CPF dos clientes
  - Consulta ao banco PostgreSQL
  - Integração com API Gateway

### 3. ☸️ Infraestrutura Kubernetes

**Repositório**: `infra-k8s-terraform`

- **Função**: Provisionamento do cluster EKS e API Gateway
- **Tecnologias**: Terraform, EKS, API Gateway, Lambda
- **Responsabilidades**:
  - Configuração do cluster EKS
  - Setup do API Gateway
  - Deploy do Lambda Authorizer
  - Configuração de rede e segurança

### 4. 🍔 Aplicação Principal

**Repositório**: `app-service`

- **Função**: API principal do sistema de autoatendimento
- **Tecnologias**: NestJS, TypeScript, TypeORM, Docker
- **Responsabilidades**:
  - Lógica de negócio do sistema
  - Gerenciamento de pedidos
  - Interface com totems
  - APIs RESTful

### Integração entre Repositórios

```mermaid
graph LR
    A[infra-db-terraform] --> B[RDS PostgreSQL]
    C[lambda-service] --> B
    D[infra-k8s-terraform] --> E[EKS + API Gateway]
    E --> C
    F[app-service] --> B
    E --> F

    style A fill:#e1f5fe
    style C fill:#fff3e0
    style D fill:#e8f5e8
    style F fill:#fce4ec
```

## ⚙️ CI/CD e Práticas DevOps

### Proteção de Branches

Todos os repositórios implementam proteção rigorosa da branch `main`:

- 🚫 **Push direto bloqueado**: Impossibilita commits diretos na main
- ✅ **Pull Request obrigatório**: Todas as mudanças passam por PR
- 🔍 **Code Review obrigatório**: Aprovação de pelo menos 1 revisor
- 🧪 **Testes obrigatórios**: CI/CD deve passar com sucesso
- 📋 **Status checks**: Validação de qualidade e segurança

### Pipeline de Deploy

```mermaid
graph LR
    A[Feature Branch] --> B[Pull Request]
    B --> C[CI Tests]
    C --> D[Code Review]
    D --> E[Merge to Main]
    E --> F[Deploy Automático]
    F --> G[Terraform Apply]
    G --> H[Infrastructure Updated]

    style C fill:#e8f5e8
    style F fill:#fff3e0
    style G fill:#e1f5fe
```

### Tecnologias CI/CD

- **GitHub Actions**: Automação de workflows
- **Terraform**: Infrastructure as Code
- **Docker**: Containerização
- **AWS ECR**: Registry de containers
- **AWS S3 + DynamoDB**: State management do Terraform
- **Secrets Manager**: Gerenciamento seguro de credenciais

## 🛠️ Justificativas Técnicas

### Por que PostgreSQL?

#### 🎯 Robustez Técnica

- **ACID Compliant**: Garante consistência em transações críticas
- **Performance Superior**: Índices avançados e otimização automática
- **JSON Nativo**: Perfeito para APIs REST e dados semi-estruturados

#### ☁️ Integração AWS

- **RDS Gerenciado**: Backups automáticos, Multi-AZ e monitoramento
- **Escalabilidade**: Read replicas e particionamento
- **Segurança**: Criptografia integrada e controle granular

#### 🎓 Benefícios para Aprendizado

- **Open Source**: Sem custos de licença
- **Padrão da Indústria**: Amplamente usado em empresas de tecnologia
- **Microserviços-friendly**: Schemas múltiplos e isolamento por domínio

### Por que AWS?

- **🚀 Serverless**: Lambda para escalabilidade automática
- **🔐 Segurança**: IAM, Secrets Manager, VPC
- **📊 Monitoramento**: CloudWatch integrado
- **💰 Custo-benefício**: Pay-per-use para recursos serverless
- **🌍 Disponibilidade**: Multi-AZ e alta disponibilidade

### Por que Kubernetes (EKS)?

- **📈 Escalabilidade**: Auto-scaling baseado em métricas
- **🔄 Orquestração**: Gerenciamento automático de containers
- **🛡️ Isolamento**: Namespaces e network policies
- **🔧 Flexibilidade**: Deploy de aplicações complexas
- **📊 Observabilidade**: Integração com ferramentas de monitoramento

## 🗄️ Modelagem de Dados

### Esquema do Banco de Dados

Acesse o esquema completo do banco de dados através do link:
**[Esquema do Banco - BR Modelo](https://app.brmodeloweb.com/#!/publicview/68213be2acb39fc7c317bb53)**

### Estrutura Principal

O banco PostgreSQL foi modelado seguindo as melhores práticas de normalização e inclui:

- **Tabela de Clientes**: Informações dos usuários com validação de CPF
- **Tabela de Produtos**: Catálogo com categorias e preços
- **Tabela de Pedidos**: Controle de status e histórico
- **Tabela de Itens do Pedido**: Relacionamento many-to-many otimizado
- **Tabela de Totens**: Controle de dispositivos autorizados

### Características da Modelagem

- **🔐 Segurança**: Campos sensíveis criptografados
- **📊 Performance**: Índices otimizados para consultas frequentes
- **🔄 Integridade**: Constraints e foreign keys bem definidas
- **📈 Escalabilidade**: Estrutura preparada para crescimento

## 🎥 Demonstração

### Vídeo da Arquitetura

Assista à demonstração completa da arquitetura desenvolvida e execução das pipelines CI/CD:

**[youtube.com](youtube.com)**

O vídeo apresenta:

- 🏗️ **Arquitetura na AWS**: Visão completa dos serviços provisionados
- ⚙️ **Pipelines CI/CD**: Demonstração dos workflows automatizados
- 🔧 **Infraestrutura como Código**: Terraform em ação
- 🚀 **Deploy Automatizado**: Processo completo de deployment
- 🛡️ **Segurança**: Implementação de boas práticas

## 🚀 Como Executar

### Pré-requisitos

- AWS CLI configurado
- Terraform >= 1.0
- Docker
- kubectl
- Node.js 18+

### Ordem de Deploy

1. **Infraestrutura do Banco**: Deploy do `infra-db-terraform`
2. **Lambda Service**: Deploy do `lambda-service`
3. **Infraestrutura K8s**: Deploy do `infra-k8s-terraform`
4. **App Service**: Deploy do `app-service`

### Comandos Básicos

```bash
# 1. Clonar os repositórios
git clone <repositório-infra-db>
git clone <repositório-infra-k8s>
git clone <repositório-lambda>
git clone <repositório-app>

# 2. Deploy dos serviços
cd ../lambda-service
serverless deploy


# 3. Deploy da infraestrutura
cd infra-db-terraform
terraform init && terraform apply

cd ../infra-k8s-terraform
terraform init && terraform apply

# 4.Deploy da aplicação
cd ../app-service
docker build -t app-service .
kubectl apply -f k8s/
```
