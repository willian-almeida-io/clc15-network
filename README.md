# 🌐 AWS Resilient Multi-AZ Network with Terraform

[![Terraform](https://img.shields.io/badge/Terraform-1.5%2B-623CE4?logo=terraform&logoColor=white)](https://www.terraform.io/)
[![AWS Provider](https://img.shields.io/badge/AWS_Provider-6.42.0-FF9900?logo=amazon-aws&logoColor=white)](https://registry.terraform.io/providers/hashicorp/aws/latest)
[![Architecture](https://img.shields.io/badge/Architecture-Multi--AZ-007acc)](#-arquitetura-da-solucao)

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
