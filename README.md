# ⚙️ AWS Auto Scaling Lab — Escola da Nuvem

Este projeto foi desenvolvido como parte do laboratório **“Utilizando o Auto Scaling na AWS (Linux)”** da **Escola da Nuvem**, orientado pelo professor **Victor**.  
O objetivo foi criar uma infraestrutura **altamente disponível e escalável**, automatizando o provisionamento e o balanceamento de carga de instâncias **Amazon EC2**.

---

## 🧩 Arquitetura do Projeto

O laboratório foi dividido em etapas práticas, utilizando tanto o **AWS CLI** quanto o **Console da AWS**, resultando na arquitetura abaixo:

### 🏗️ Arquitetura Final
- **Elastic Load Balancer (ALB)**: Distribui o tráfego entre múltiplas instâncias EC2.
- **Auto Scaling Group (ASG)**: Escala automaticamente a quantidade de instâncias conforme a carga de CPU.
- **Launch Template**: Modelo base para criação automática de instâncias.
- **CloudWatch Alarms**: Monitora o uso de CPU e aciona o scale-out/scale-in.
- **Amazon Machine Image (AMI)**: Imagem personalizada criada a partir da instância base “WebServer”.
- **Security Group (HTTPAccess)**: Permite tráfego HTTP para a aplicação.
- **VPC com 2 Zonas de Disponibilidade**: Garante resiliência e alta disponibilidade.

---

## 🧠 Objetivos do Lab

✅ Criar e configurar uma instância EC2 via AWS CLI  
✅ Gerar uma nova AMI personalizada  
✅ Criar um Launch Template para Auto Scaling  
✅ Configurar um Auto Scaling Group (ASG)  
✅ Integrar o ASG ao Application Load Balancer  
✅ Simular alta carga de CPU e observar o scale-out automático  
✅ Validar o scale-in após normalização da carga

---

## 🖥️ Teste de Stress

Durante o teste com a aplicação **Load Test App**, ao clicar em **“Start Stress”**, a CPU da instância sobe para ~100%, acionando a política de **scale-out**.  
O **Auto Scaling Group** cria novas instâncias EC2 automaticamente para equilibrar a carga.  
Após o término do teste, o ASG reduz a capacidade (**scale-in**) até o valor desejado.

### 💡 Resultado:
- Scale-out validado: novas instâncias “WebApp” criadas automaticamente.  
- Scale-in validado: ASG reduziu para 2 instâncias após normalização da carga.

---

## 📸 Evidências do Lab

| Etapa | Descrição | Print |
|-------|------------|-------|
| 1️⃣ | **Application Load Balancer ativo em múltiplas AZs** | ![ALB](./images/ec2.jpeg) |
| 2️⃣ | **Instâncias EC2 ativas após o Auto Scaling** | ![Instances](./images/instances.jpeg)|
| 3️⃣ | **Aplicação de Teste – Load Test App** | ![Stress Test](./images/stress.jpeg) |
| 4️⃣ | **Instâncias reduzidas após scale-in** | ![After Load](./images/load.jpeg) |

---

## 🧾 Tecnologias e Serviços Utilizados

- **AWS EC2**  
- **AWS Auto Scaling**  
- **AWS Elastic Load Balancer (ALB)**  
- **AWS CloudWatch**  
- **AWS CLI**  
- **Amazon Machine Image (AMI)**  
- **VPC & Subnets**  
- **Security Groups**

---

## ⚙️ Principais Comandos AWS CLI

```bash
# Criar instância base
aws ec2 run-instances --key-name vockey \
  --instance-type t3.micro \
  --image-id ami-069f9cce803c015bc \
  --user-data file:///home/ec2-user/UserData.txt \
  --security-group-ids sg-062f5e57723d1ccbb \
  --subnet-id subnet-0f57c7a351036ad4e \
  --associate-public-ip-address \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=WebServer}]'

# Criar AMI personalizada
aws ec2 create-image --name WebServerAMI --instance-id NEW-INSTANCE-ID

# Esperar instância ficar ativa
aws ec2 wait instance-running --instance-ids NEW-INSTANCE-ID

# Obter o DNS público
aws ec2 describe-instances --instance-id NEW-INSTANCE-ID \
  --query 'Reservations[0].Instances[0].NetworkInterfaces[0].Association.PublicDnsName'
