# 🌐 AWS Resilient Multi-AZ Network with Terraform

[![Terraform](https://img.shields.io/badge/Terraform-1.5%2B-623CE4?logo=terraform&logoColor=white)](https://www.terraform.io/)
[![AWS Provider](https://img.shields.io/badge/AWS_Provider-6.42.0-FF9900?logo=amazon-aws&logoColor=white)](https://registry.terraform.io/providers/hashicorp/aws/latest)
[![Architecture](https://img.shields.io/badge/Architecture-Multi--AZ-007acc)](#-arquitetura)

Projeto de Infrastructure as Code (IaC) para provisionamento de uma arquitetura de rede corporativa altamente disponível, segura e resiliente na AWS, projetada para suportar workloads de missão crítica com isolamento de tráfego e conformidade de segurança.

---

## 🏛️ Arquitetura da Solução

O projeto provisiona uma Virtual Private Cloud (VPC) distribuída em duas Zonas de Disponibilidade (`us-east-1a` e `us-east-1b`):

```text
               +-------------------------------------------------------------+
               |                  AWS Region (us-east-1)                     |
               |                                                             |
               |                   Internet Gateway (IGW)                    |
               |                             |                               |
               |     +-----------------------+-------------------------+     |
               |     |                                                 |     |
               |  [AZ 1a]                                           [AZ 1b]  |
               |  +---------------------------+   +-----------------------+  |
Public Subnet  |  | Subnet Pública (10.0.1.0) |   | Subnet Pública        |  |
               |  | [NAT Gateway 1a]          |   | (10.0.2.0)            |  |
               |  +-------------|-------------+   | [NAT Gateway 1b]      |  |
               |                |                 +-----------|-----------+  |
               |                |                             |              |
Private Subnet |  +-------------v-------------+   +-----------v-----------+  |
               |  | Subnet Privada            |   | Subnet Privada        |  |
               |  | (10.0.100.0)              |   | (10.0.200.0)          |  |
               |  +---------------------------+   +-----------------------+  |
               +-------------------------------------------------------------+
                                      |
                     [VPC Flow Logs -> Amazon S3 Bucket]

```



## 📌 Principais Destaques Técnicos

* **Alta Disponibilidade (Multi-AZ):** Distribuição equilibrada de subnets públicas e privadas em múltiplas zonas geográficas independentes (`us-east-1a` e `us-east-1b`)[cite: 7].
* **Isolamento de Cargas Privadas:** Instâncias nas redes privadas não possuem IPs públicos e saem para a internet exclusivamente via **NAT Gateways dedicados por AZ**, eliminando pontos únicos de falha (Single Point of Failure - SPOF)[cite: 7].
* **Hardening de Segurança:** Trancamento e controle do `default security group` para mitigar regras permissivas automáticas[cite: 7].
* **Auditoria e Observabilidade:** Configuração de **VPC Flow Logs** com exportação direta para bucket S3 para análise de tráfego e conformidade[cite: 7].
* **Gerenciamento de Estado Seguro:** Armazenamento centralizado do arquivo de estado (`terraform.tfstate`) em backend remoto no Amazon S3[cite: 1, 7].

---

## 🚀 Como Executar o Projeto Passo a Passo

### Pré-requisitos
* Ter o **Terraform** instalado (versão >= 1.5)[cite: 7].
* Ter a **AWS CLI** instalada e autenticada com suas credenciais (`aws configure`)[cite: 7].
* Ter o bucket S3 pré-criado para o backend remoto (`clc15-willian-terraform`)[cite: 1, 7].

---

### Passo 1: Clonar o repositório
Clone o projeto em seu ambiente local e acesse o diretório[cite: 7]:

```bash
git clone [https://github.com/willian-almeida-io/clc15-network.git](https://github.com/willian-almeida-io/clc15-network.git)
cd clc15-network
```[cite: 7]

---

### Passo 2: Inicializar o Terraform
Baixe os provedores necessários e configure a conexão com o backend remoto no S3[cite: 7]:

```bash
terraform init
```[cite: 7]

---

### Passo 3: Validar a sintaxe do código
Verifique a formatação e a integridade da sintaxe dos arquivos de configuração[cite: 7]:

```bash
terraform fmt -check
terraform validate
```[cite: 7]

---

### Passo 4: Planejar o provisionamento
Gere um preview detalhado de todos os recursos que serão criados na AWS[cite: 7]:

```bash
terraform plan
```[cite: 7]

---

### Passo 5: Aplicar a infraestrutura
Provisione a infraestrutura completa na sua conta AWS[cite: 7]:

```bash
terraform apply
```[cite: 7]

---

### Passo 6: Descomissionamento (Cleanup)
Para destruir os recursos e evitar custos na conta AWS após os testes[cite: 7]:

```bash
terraform destroy
```[cite: 7]

---

## 📋 Documentação Técnica (Terraform Docs)

### Requirements

| Name | Version |
| --- | --- |
| aws | 6.42.0 |[cite: 7]

### Providers

| Name | Version |
| --- | --- |
| aws | 6.42.0 |[cite: 7]

### Resources Criados

| Recurso | Tipo | Descrição |
| --- | --- | --- |
| aws_vpc.terraform_vpc | resource | VPC com bloco CIDR 10.0.0.0/16[cite: 7] |
| aws_subnet.subnet_public_1a | resource | Subnet pública na AZ 1a (10.0.1.0/24)[cite: 7] |
| aws_subnet.subnet_public_1b | resource | Subnet pública na AZ 1b (10.0.2.0/24)[cite: 7] |
| aws_subnet.subnet_private_1a | resource | Subnet privada na AZ 1a (10.0.100.0/24)[cite: 7] |
| aws_subnet.subnet_private_1b | resource | Subnet privada na AZ 1b (10.0.200.0/24)[cite: 7] |
| aws_internet_gateway.tf_igw | resource | Internet Gateway conectado à VPC[cite: 7] |
| aws_eip.tf_ip_nat_1a | resource | IP Elástico alocado para a NAT Gateway da AZ 1a[cite: 7] |
| aws_eip.tf_ip_nat_1b | resource | IP Elástico alocado para a NAT Gateway da AZ 1b[cite: 7] |
| aws_nat_gateway.tf_natgateway_1a | resource | NAT Gateway para saída de internet da AZ 1a[cite: 7] |
| aws_nat_gateway.tf_natgateway_1b | resource | NAT Gateway para saída de internet da AZ 1b[cite: 7] |
| aws_route_table.tf_public_rt | resource | Tabela de rotas com saída via IGW (0.0.0.0/0)[cite: 7] |
| aws_route_table.tf_private_rt_1a | resource | Tabela de rotas privada com saída via NAT 1a[cite: 7] |
| aws_route_table.tf_private_rt_1b | resource | Tabela de rotas privada com saída via NAT 1b[cite: 7] |
| aws_route_table_association.public_1a_association | resource | Associação de rota da subnet pública 1a[cite: 7] |
| aws_route_table_association.public_1b_association | resource | Associação de rota da subnet pública 1b[cite: 2, 7] |
| aws_route_table_association.private_1a_association | resource | Associação de rota da subnet privada 1a[cite: 2, 7] |
| aws_route_table_association.private_1b_association | resource | Associação de rota da subnet privada 1b[cite: 2, 7] |
| aws_flow_log.example | resource | Ativação de VPC Flow Logs para bucket S3[cite: 2, 7] |
| aws_default_security_group.default | resource | Hardening do Security Group padrão[cite: 2, 7] |

### Outputs

| Name | Description |
| --- | --- |
| vpc_id | Identificador único da VPC provisionada |[cite: 3]


