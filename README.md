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

* **Alta Disponibilidade (Multi-AZ):** Distribuição equilibrada de subnets públicas e privadas em múltiplas zonas geográficas independentes (`us-east-1a` e `us-east-1b`).
* **Isolamento de Cargas Privadas:** Instâncias nas redes privadas não possuem IPs públicos e saem para a internet exclusivamente via **NAT Gateways dedicados por AZ**, eliminando pontos únicos de falha (Single Point of Failure - SPOF).
* **Hardening de Segurança:** Trancamento e controle do `default security group` para mitigar regras permissivas automáticas.
* **Auditoria e Observabilidade:** Configuração de **VPC Flow Logs** com exportação direta para bucket S3 para análise de tráfego e conformidade.
* **Gerenciamento de Estado Seguro:** Armazenamento centralizado do arquivo de estado (`terraform.tfstate`) em backend remoto no Amazon S3.

---

## 🚀 Como Executar o Projeto Passo a Passo

### Pré-requisitos
* Ter o **Terraform** instalado (versão >= 1.5).
* Ter a **AWS CLI** instalada e autenticada com suas credenciais (`aws configure`).
* Ter o bucket S3 pré-criado para o backend remoto (`clc15-willian-terraform`).

---

1. Clonar o repositório

Clone o repositório GitHub e acesse o diretório do projeto:

git clone https://github.com/willian-almeida-io/clc15-network.git

cd clc15-network

2. Inicializar o Terraform

Baixe os provedores necessários e inicialize o backend remoto no Amazon S3, caso esteja configurado no projeto.

terraform init

3. Validar a sintaxe do código

Verifique a formatação e valide os arquivos de configuração do Terraform:

terraform fmt -check
terraform validate


Para formatar automaticamente os arquivos, execute:

terraform fmt -recursive

4. Planejar o provisionamento

Gere um plano para visualizar os recursos que serão criados, modificados ou destruídos na AWS:

terraform plan

5. Aplicar a infraestrutura

Provisione os recursos definidos na configuração do Terraform:

terraform apply


Revise o plano e confirme a operação quando solicitado.

6. Destruir a infraestrutura

Após concluir os testes, remova os recursos gerenciados pelo Terraform para evitar custos desnecessários:

terraform destroy


Atenção: esse comando pode excluir recursos e dados importantes. Revise o plano antes de confirmar.

Documentação técnica do Terraform

Requirements
Nome	Versão
Terraform	Compatível com a configuração do projeto
AWS Provider	6.42.0
Providers
Nome	Versão
aws	6.42.0
Recursos criados
Recurso	Tipo	Descrição
aws_vpc.terraform_vpc	Resource	VPC com bloco CIDR 10.0.0.0/16.
aws_subnet.subnet_public_1a	Resource	Subnet pública na AZ 1a, com CIDR 10.0.1.0/24.
aws_subnet.subnet_public_1b	Resource	Subnet pública na AZ 1b, com CIDR 10.0.2.0/24.
aws_subnet.subnet_private_1a	Resource	Subnet privada na AZ 1a, com CIDR 10.0.100.0/24.
aws_subnet.subnet_private_1b	Resource	Subnet privada na AZ 1b, com CIDR 10.0.200.0/24.
aws_internet_gateway.tf_igw	Resource	Internet Gateway conectado à VPC.
aws_eip.tf_ip_nat_1a	Resource	IP Elástico para a NAT Gateway da AZ 1a.
aws_eip.tf_ip_nat_1b	Resource	IP Elástico para a NAT Gateway da AZ 1b.
aws_nat_gateway.tf_natgateway_1a	Resource	NAT Gateway para saída à internet na AZ 1a.
aws_nat_gateway.tf_natgateway_1b	Resource	NAT Gateway para saída à internet na AZ 1b.
aws_route_table.tf_public_rt	Resource	Tabela de rotas públicas com saída via Internet Gateway (0.0.0.0/0).
aws_route_table.tf_private_rt_1a	Resource	Tabela de rotas privada com saída via NAT Gateway da AZ 1a.
aws_route_table.tf_private_rt_1b	Resource	Tabela de rotas privada com saída via NAT Gateway da AZ 1b.
aws_route_table_association.public_1a_association	Resource	Associação da subnet pública 1a à tabela de rotas.
aws_route_table_association.public_1b_association	Resource	Associação da subnet pública 1b à tabela de rotas.
aws_route_table_association.private_1a_association	Resource	Associação da subnet privada 1a à tabela de rotas.
aws_route_table_association.private_1b_association	Resource	Associação da subnet privada 1b à tabela de rotas.
aws_flow_log.example	Resource	Configuração de VPC Flow Logs com destino em um bucket S3.
aws_default_security_group.default	Resource	Configuração de segurança do Security Group padrão.
Outputs
Nome	Descrição
vpc_id	Identificador único da VPC provisionada.

Observações importantes
Configure as credenciais da AWS antes de executar o provisionamento.
Verifique se o backend remoto S3 está configurado corretamente antes de executar terraform init.
Confirme se a versão do AWS Provider é compatível com os arquivos do projeto.
As NAT Gateways, os endereços IP elásticos e outros recursos podem gerar custos enquanto estiverem ativos.
Os recursos descritos nesta documentação devem ser conferidos com os arquivos Terraform existentes no repositório.












