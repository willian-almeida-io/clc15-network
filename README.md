# AWS Resilient Multi-AZ Network with Terraform

Projeto de Infrastructure as Code (IaC) para provisionamento de uma arquitetura de rede corporativa altamente disponível, segura e resiliente na AWS, projetada para suportar workloads de missão crítica com isolamento de tráfego e conformidade de segurança.

---

## Principais Destaques Tecnicos

* **Alta Disponibilidade (Multi-AZ):** Distribuicao equilibrada de subnets publicas e privadas em multiplas zonas geograficas independentes (us-east-1a e us-east-1b).
* **Isolamento de Cargas Privadas:** Instancias nas redes privadas nao possuem IPs publicos e saem para a internet exclusivamente via NAT Gateways dedicados por AZ, eliminando pontos unicos de falha (SPOF).
* **Hardening de Seguranca:** Trancamento e controle do default security group para mitigar regras permissivas automaticas.
* **Auditoria e Observabilidade:** Configuracao de VPC Flow Logs com exportacao direta para bucket S3 para analise de trafego e conformidade.
* **Gerenciamento de Estado Seguro:** Armazenamento centralizado do arquivo de estado (terraform.tfstate) em backend remoto no Amazon S3.

---

## Como Executar

### Pre-requisitos
* Terraform instalado (versao >= 1.5)
* AWS CLI instalado e configurado (`aws configure`)
* Bucket S3 pre-existente para armazenamento do backend remoto (`clc15-willian-terraform`)

### Passo a Passo

1. Clone o repositorio:
```bash
git clone https://github.com/willian-almeida-io/clc15-network.git
cd clc15-network
