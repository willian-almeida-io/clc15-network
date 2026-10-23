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


## ⚠️ Considerações Importantes

Antes de inicializar e provisionar a infraestrutura, verifique os seguintes requisitos:

- **Credenciais AWS:** configure as credenciais de acesso à AWS e confirme se a identidade utilizada possui as permissões necessárias para criar e gerenciar os recursos do projeto.

- **Backend remoto S3:** verifique se o bucket S3 e os parâmetros do backend remoto estão configurados corretamente antes de executar `terraform init`. Caso o backend ainda não exista, providencie sua criação conforme a configuração do projeto.

- **Versões do Terraform e dos Providers:** confirme a compatibilidade entre a versão do Terraform CLI, o AWS Provider e os arquivos de configuração utilizados.

- **Custos da infraestrutura:** recursos como NAT Gateways, endereços IP elásticos e outros componentes da AWS podem gerar cobranças enquanto estiverem provisionados. Consulte os preços vigentes antes da implantação.

- **Validação da infraestrutura:** confirme se os recursos, as configurações e os outputs documentados correspondem aos arquivos Terraform presentes no repositório.

### ✅ Boas práticas antes da implantação

Execute os comandos abaixo para verificar a formatação, validar a configuração e analisar as alterações planejadas antes de provisionar a infraestrutura:

```bash
terraform fmt -check
terraform validate
terraform plan
```

Revise cuidadosamente o plano gerado antes de executar:

```bash
terraform apply
```

### 🧹 Limpeza dos recursos

Após concluir os testes, avalie a necessidade de remover os recursos provisionados para evitar custos desnecessários:

```bash
terraform destroy
```

**Atenção:** antes de confirmar a destruição, revise os recursos que serão excluídos e certifique-se de que nenhum recurso ou dado importante será perdido.



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



## 📚 Documentação Técnica do Terraform

### Requisitos

| Componente | Versão |
|---|---|
| Terraform CLI | Compatível com o projeto |
| AWS Provider | 6.42.0 |

### Providers

| Provider | Versão |
|---|---|
| AWS (`aws`) | 6.42.0 |

### Recursos da infraestrutura

| Recurso | Descrição |
|---|---|
| `aws_vpc.terraform_vpc` | VPC com CIDR `10.0.0.0/16`. |
| `aws_subnet.subnet_public_1a` | Subnet pública na AZ 1a — `10.0.1.0/24`. |
| `aws_subnet.subnet_public_1b` | Subnet pública na AZ 1b — `10.0.2.0/24`. |
| `aws_subnet.subnet_private_1a` | Subnet privada na AZ 1a — `10.0.100.0/24`. |
| `aws_subnet.subnet_private_1b` | Subnet privada na AZ 1b — `10.0.200.0/24`. |
| `aws_internet_gateway.tf_igw` | Internet Gateway associado à VPC. |
| `aws_eip.tf_ip_nat_1a` | Elastic IP destinado à NAT Gateway da AZ 1a. |
| `aws_eip.tf_ip_nat_1b` | Elastic IP destinado à NAT Gateway da AZ 1b. |
| `aws_nat_gateway.tf_natgateway_1a` | NAT Gateway para acesso de saída à internet na AZ 1a. |
| `aws_nat_gateway.tf_natgateway_1b` | NAT Gateway para acesso de saída à internet na AZ 1b. |
| `aws_route_table.tf_public_rt` | Tabela de rotas públicas com rota padrão para o Internet Gateway. |
| `aws_route_table.tf_private_rt_1a` | Tabela de rotas privadas com saída pela NAT Gateway da AZ 1a. |
| `aws_route_table.tf_private_rt_1b` | Tabela de rotas privadas com saída pela NAT Gateway da AZ 1b. |
| `aws_route_table_association.public_1a_association` | Associação da subnet pública da AZ 1a à tabela de rotas. |
| `aws_route_table_association.public_1b_association` | Associação da subnet pública da AZ 1b à tabela de rotas. |
| `aws_route_table_association.private_1a_association` | Associação da subnet privada da AZ 1a à tabela de rotas. |
| `aws_route_table_association.private_1b_association` | Associação da subnet privada da AZ 1b à tabela de rotas. |
| `aws_flow_log.example` | Configuração de VPC Flow Logs com destino em um bucket S3. |
| `aws_default_security_group.default` | Gerenciamento das regras do Security Group padrão da VPC. |

### Outputs

| Output | Descrição |
|---|---|
| `vpc_id` | Identificador único da VPC provisionada na AWS. |

Para consultar o ID da VPC após o provisionamento, execute:

```bash
terraform output vpc_id
```

### Observações técnicas

- A infraestrutura utiliza subnets públicas e privadas para separar os recursos de rede.
- As NAT Gateways permitem acesso de saída à internet a partir das subnets privadas.
- As tabelas de rotas definem o encaminhamento do tráfego entre as redes e os gateways.
- O VPC Flow Logs permite monitorar informações sobre o tráfego de rede.
- A configuração efetiva depende dos arquivos Terraform presentes no repositório.


## 👨‍💻 Autor

**Willian Carlos de Almeida Silva**

- 📧 **E-mail:** [willian.carlosalmeidasilva@gmail.com](mailto:willian.carlosalmeidasilva@gmail.com)
- 🛠️ **Projeto:** CLC15 Network — Infraestrutura AWS com Terraform

Desenvolvido com foco em infraestrutura como código (IaC), automação e boas práticas de arquitetura de redes na AWS.

---








