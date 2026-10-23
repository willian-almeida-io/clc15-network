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

## 1. Clonar o repositório

```bash
git clone https://github.com/willian-almeida-io/clc15-network.git
cd clc15-network
```



## 2. Inicializar o Terraform

```bash
terraform init
```



## 3. Validar a sintaxe do código

Verifique a formatação e valide os arquivos de configuração do Terraform:

```bash
terraform fmt -check
terraform validate
```

Para formatar automaticamente os arquivos, execute:

```bash
terraform fmt -recursive
```

## 4. Planejar o provisionamento

Gere um plano para visualizar os recursos que serão criados, modificados ou destruídos na AWS:

```bash
terraform plan
```

## 5. Aplicar a infraestrutura

Provisione os recursos definidos na configuração do Terraform:

```bash
terraform apply
```

Revise o plano e confirme a operação quando solicitado.

## 6. Destruir a infraestrutura

Após concluir os testes, remova os recursos gerenciados pelo Terraform para evitar custos desnecessários:

```bash
terraform destroy
```
**Atenção:** revise os recursos que serão excluídos antes de confirmar a operação.

📚 Documentação Técnica da Infraestrutura

Esta seção apresenta os requisitos técnicos, os provedores utilizados, os recursos provisionados e as saídas disponibilizadas pela infraestrutura definida com Terraform na AWS.

1. Requisitos (Requirements)
Componente	Versão
Terraform CLI	Compatível com a configuração do projeto
AWS Provider	6.42.0
2. Provedores (Providers)
Provedor	Versão
aws	6.42.0

O AWS Provider permite que o Terraform interaja com os serviços da Amazon Web Services (AWS) para provisionar e gerenciar os recursos de infraestrutura.

3. Recursos provisionados (Resources)

A infraestrutura contempla uma VPC com sub-redes públicas e privadas distribuídas entre duas zonas de disponibilidade, gateways de conectividade, tabelas de rotas, associações de rotas e recursos de monitoramento e segurança.

3.1. Rede e sub-redes
Recurso Terraform	Tipo	Descrição
aws_vpc.terraform_vpc	VPC	Rede virtual com bloco CIDR 10.0.0.0/16.
aws_subnet.subnet_public_1a	Subnet	Sub-rede pública na AZ 1a, com CIDR 10.0.1.0/24.
aws_subnet.subnet_public_1b	Subnet	Sub-rede pública na AZ 1b, com CIDR 10.0.2.0/24.
aws_subnet.subnet_private_1a	Subnet	Sub-rede privada na AZ 1a, com CIDR 10.0.100.0/24.
aws_subnet.subnet_private_1b	Subnet	Sub-rede privada na AZ 1b, com CIDR 10.0.200.0/24.
3.2. Conectividade com a internet
Recurso Terraform	Tipo	Descrição
aws_internet_gateway.tf_igw	Internet Gateway	Gateway responsável pela conectividade entre a VPC e a internet.
aws_eip.tf_ip_nat_1a	Elastic IP	Endereço IP público reservado para a NAT Gateway da AZ 1a.
aws_eip.tf_ip_nat_1b	Elastic IP	Endereço IP público reservado para a NAT Gateway da AZ 1b.
aws_nat_gateway.tf_natgateway_1a	NAT Gateway	Permite que recursos de sub-redes privadas acessem a internet por meio da AZ 1a.
aws_nat_gateway.tf_natgateway_1b	NAT Gateway	Permite que recursos de sub-redes privadas acessem a internet por meio da AZ 1b.
3.3. Tabelas e associações de rotas
Recurso Terraform	Tipo	Descrição
aws_route_table.tf_public_rt	Route Table	Tabela de rotas públicas com rota padrão 0.0.0.0/0 direcionada ao Internet Gateway.
aws_route_table.tf_private_rt_1a	Route Table	Tabela de rotas privadas com saída para a internet por meio da NAT Gateway da AZ 1a.
aws_route_table.tf_private_rt_1b	Route Table	Tabela de rotas privadas com saída para a internet por meio da NAT Gateway da AZ 1b.
aws_route_table_association.public_1a_association	Route Association	Associa a sub-rede pública da AZ 1a à tabela de rotas correspondente.
aws_route_table_association.public_1b_association	Route Association	Associa a sub-rede pública da AZ 1b à tabela de rotas correspondente.
aws_route_table_association.private_1a_association	Route Association	Associa a sub-rede privada da AZ 1a à tabela de rotas correspondente.
aws_route_table_association.private_1b_association	Route Association	Associa a sub-rede privada da AZ 1b à tabela de rotas correspondente.
3.4. Monitoramento e segurança
Recurso Terraform	Tipo	Descrição
aws_flow_log.example	VPC Flow Logs	Configura a captura de informações sobre o tráfego de rede da VPC, com destino em um bucket S3.
aws_default_security_group.default	Security Group	Gerencia as regras do grupo de segurança padrão da VPC, conforme a configuração definida no projeto.
4. Saídas da infraestrutura (Outputs)

Os outputs permitem consultar informações relevantes geradas após o provisionamento dos recursos.

Output	Descrição
vpc_id	Identificador único da VPC provisionada na AWS.

Para consultar o identificador da VPC após a implantação, execute:

terraform output vpc_id


Para listar todas as saídas disponíveis:

terraform output

5. Considerações técnicas
A arquitetura separa os recursos em sub-redes públicas e privadas, permitindo definir diferentes políticas de conectividade.
As NAT Gateways permitem o acesso de saída à internet para recursos em sub-redes privadas, sem exigir endereços IP públicos nesses recursos.
As tabelas de rotas determinam o encaminhamento do tráfego de rede de acordo com a configuração definida.
O VPC Flow Logs auxilia na análise e no monitoramento do tráfego de rede.
A configuração efetiva de segurança depende das regras definidas nos arquivos Terraform.

Nota: esta documentação descreve os recursos informados para o projeto. Os nomes, as versões e as configurações devem ser conferidos com os arquivos Terraform e com o estado efetivamente provisionado na AWS.

⚠️ Considerações importantes

Antes de inicializar e provisionar a infraestrutura, verifique os seguintes requisitos:

Credenciais AWS: configure as credenciais de acesso à AWS e certifique-se de que a identidade utilizada possui as permissões necessárias para criar e gerenciar os recursos definidos no projeto.
Backend remoto S3: confirme se o bucket S3 e os demais parâmetros do backend remoto estão configurados corretamente antes de executar terraform init. Caso o backend ainda não exista, providencie sua criação conforme a configuração do projeto.
Versão do Terraform e dos Providers: verifique a compatibilidade entre a versão do Terraform, o AWS Provider e os arquivos de configuração utilizados.
Custos dos recursos: recursos como NAT Gateways, endereços IP elásticos e outros componentes da infraestrutura AWS podem gerar cobranças enquanto estiverem provisionados. Consulte os preços vigentes da AWS antes de iniciar a implantação.
Validação da infraestrutura: confira se os recursos, as configurações e os outputs documentados correspondem aos arquivos Terraform presentes no repositório.

Recomendação: execute terraform fmt -check, terraform validate e terraform plan antes de aplicar qualquer alteração. Revise cuidadosamente o plano gerado antes de executar terraform apply.

Após a conclusão dos testes, avalie a necessidade de executar terraform destroy para remover os recursos provisionados e evitar custos desnecessários. Verifique os recursos que serão excluídos antes de confirmar a operação.


---

## 👨‍💻 Autor

**Willian Carlos de Almeida Silva**

- 📧 **E-mail:** [willian.carlosalmeidasilva@gmail.com](mailto:willian.carlosalmeidasilva@gmail.com)
- 🛠️ **Projeto:** CLC15 Network — Infraestrutura AWS com Terraform

Desenvolvido com foco em infraestrutura como código (IaC), automação e boas práticas de arquitetura de redes na AWS.

---








