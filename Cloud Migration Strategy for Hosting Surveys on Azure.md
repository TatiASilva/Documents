## Introduction  
This document outlines a cost-effective and scalable solution for migrating the existing survey application infrastructure to Microsoft Azure. The proposed solution leverages Azure Virtual Machines (VMs) to replicate the current setup while addressing challenges such as high server load, limited scalability, and reliability.  

---

## Client’s Current Challenges  
The client currently operates a web-based survey application using two Linux machines hosted on-premises: one for the database and another for the application container. This setup is underperforming due to heavy user traffic, resulting in 100% resource utilization and incomplete survey submissions.  

With millions of users accessing the application globally, the current infrastructure lacks the scalability and reliability needed to meet demand. Recognizing the growing trend of cloud adoption, the client has decided to migrate to Microsoft Azure to enhance performance, ensure availability, and reduce operational challenges.  

---

## Proposed Azure Infrastructure  

### 1. Overview of the Solution  
To maintain a structure like the existing physical setup, the solution involves:  
- Deploying one Azure VM for the database.  
- Deploying one Azure VM for the application.  
- Configuring scalability to handle traffic spikes dynamically.  

This setup ensures improved performance, reliability, and cost efficiency while maintaining operational simplicity.  

---

### 2. Components and Configuration  

#### 2.1 Azure VM for the Database  
- **Purpose**: Host the database to manage survey data.  
- **Configuration**:  
  - Start with a VM size optimized for database workloads (e.g., D-series or E-series).  
  - Attach **Premium SSD** or **Ultra Disk** for fast read/write operations.  
  - Configure **Azure Backup** to schedule regular snapshots for data protection.  
- **Scaling Options**:  
  - Vertical Scaling: Increase VM size as needed.  
  - Horizontal Scaling (future): Transition to a managed database service for seamless scaling.  

#### 2.2 Azure VM for the Application  
- **Purpose**: Host the application container and serve users globally.  
- **Configuration**:  
  - Deploy a general-purpose VM (e.g., B-series or D-series) with Docker installed.  
  - Use **Azure Virtual Machine Scale Sets** for dynamic scaling during high traffic.  
  - Optimize the VM's OS and container runtime for performance.  
- **Networking**:  
  - Deploy an **Azure Load Balancer** for distributing incoming traffic.  
  - Configure **Azure DNS** to map `topsurvey.contoso.com` to the public IP.  

---

### 3. Networking and Security  
- Create a secure **Virtual Network (VNet)** to allow communication between the application and database VMs.  
- Implement **Network Security Groups (NSGs)** to restrict traffic:  
  - Allow only the application VM to access the database VM.  
  - Open ports for HTTP/HTTPS traffic to the application.  
- Add **Azure Bastion** for secure VM management without exposing them to the public internet.  
- Enable **DDoS Protection** to safeguard against traffic spikes caused by malicious attacks.  

---

### 4. Cost Optimization  
- Use **Pay-as-you-go** pricing for flexibility.  
- Explore **Reserved Instances** for predictable workloads to save up to 72% on VM costs.  
- Enable **Auto-Shutdown** during off-peak hours to reduce expenses.  
- Monitor resource usage with **Azure Cost Management** to identify and control unnecessary expenses.  

---

### 5. High Availability and Reliability  
- Deploy VMs in an **Availability Set** or across **Availability Zones** to protect against hardware failures.  
- Use **Azure Monitor** for proactive tracking of performance and resource utilization.  
- Configure a disaster recovery plan using **Azure Recovery Services Vault**.  

---

## Benefits of the Proposed Solution  
1. **Scalability**: Automatically adjusts resources to handle peak traffic.  
2. **Cost Efficiency**: Pay only for the resources used, with options for reserved pricing.  
3. **High Availability**: Redundancy ensures minimal downtime and reliable service.  
4. **Improved Security**: Managed firewalls, secure access, and DDoS protection enhance security.  
5. **Ease of Maintenance**: Azure services automate backups, updates, and scaling.  

---

## Next Steps  
1. Approval of the proposed migration plan.  
2. Selection of Azure VM sizes based on current usage metrics.  
3. Configuration and testing of the infrastructure on Azure.  
4. Migration of data and application to the new environment.  
5. Ongoing monitoring and optimization after deployment.

----------------------------------------------------------------------------

# Estratégia de Migração para a Nuvem para Hospedar Pesquisas no Azure

## Introdução  
Este documento apresenta uma solução escalável e econômica para migrar a infraestrutura existente do aplicativo de pesquisa para o Microsoft Azure. A solução proposta utiliza Máquinas Virtuais (VMs) no Azure para replicar a configuração atual, abordando problemas como alta carga nos servidores, escalabilidade limitada e falta de confiabilidade.  

---

## Desafios Atuais do Cliente  
O cliente atualmente opera um aplicativo de pesquisas baseado na web utilizando duas máquinas Linux hospedadas localmente: uma para o banco de dados e outra para o contêiner da aplicação. Esta configuração está apresentando baixo desempenho devido ao tráfego intenso de usuários, resultando em 100% de utilização dos recursos e pesquisas incompletas.  

Com milhões de usuários acessando o aplicativo globalmente, a infraestrutura atual carece da escalabilidade e confiabilidade necessárias para atender à demanda. Reconhecendo a tendência crescente de adoção da nuvem, o cliente decidiu migrar para o Microsoft Azure para melhorar o desempenho, garantir disponibilidade e reduzir desafios operacionais.  

---

## Infraestrutura Proposta no Azure  

### 1. Visão Geral da Solução  
Para manter uma estrutura semelhante à configuração física atual, a solução envolve:  
- Implantar uma VM no Azure para o banco de dados.  
- Implantar uma VM no Azure para o aplicativo.  
- Configurar escalabilidade para lidar dinamicamente com picos de tráfego.  

Essa configuração garante melhor desempenho, confiabilidade e eficiência de custos, mantendo a simplicidade operacional.  

---

### 2. Componentes e Configuração  

#### 2.1 VM do Azure para o Banco de Dados  
- **Finalidade**: Hospedar o banco de dados para gerenciar os dados das pesquisas.  
- **Configuração**:  
  - Iniciar com um tamanho de VM otimizado para cargas de trabalho de banco de dados (por exemplo, séries D ou E).  
  - Anexar **Premium SSD** ou **Ultra Disk** para operações de leitura/gravação rápidas.  
  - Configurar **Azure Backup** para agendar snapshots regulares como proteção de dados.  
- **Opções de Escalabilidade**:  
  - Escalabilidade Vertical: Aumentar o tamanho da VM conforme necessário.  
  - Escalabilidade Horizontal (futuro): Migrar para um serviço de banco de dados gerenciado para escalabilidade mais simples.  

#### 2.2 VM do Azure para o Aplicativo  
- **Finalidade**: Hospedar o contêiner da aplicação e atender usuários globalmente.  
- **Configuração**:  
  - Implantar uma VM de uso geral (por exemplo, séries B ou D) com Docker instalado.  
  - Utilizar **Azure Virtual Machine Scale Sets** para escalabilidade dinâmica durante altos picos de tráfego.  
  - Otimizar o sistema operacional da VM e o runtime do contêiner para maior desempenho.  
- **Rede**:  
  - Implantar um **Azure Load Balancer** para distribuir o tráfego de entrada.  
  - Configurar o **Azure DNS** para mapear `topsurvey.contoso.com` ao IP público.  

---

### 3. Rede e Segurança  
- Criar uma **Rede Virtual (VNet)** segura para permitir a comunicação entre as VMs do aplicativo e do banco de dados.  
- Implementar **Grupos de Segurança de Rede (NSGs)** para restringir o tráfego:  
  - Permitir apenas que a VM do aplicativo acesse a VM do banco de dados.  
  - Abrir portas para tráfego HTTP/HTTPS no aplicativo.  
- Adicionar **Azure Bastion** para gerenciamento seguro das VMs sem expô-las à internet pública.  
- Ativar **Proteção DDoS** para proteger contra picos de tráfego causados por ataques maliciosos.  

---

### 4. Otimização de Custos  
- Usar preços baseados em **Pay-as-you-go** para flexibilidade.  
- Explorar **Instâncias Reservadas** para cargas de trabalho previsíveis e economizar até 72% nos custos das VMs.  
- Habilitar **Desligamento Automático** durante horários de baixa utilização para reduzir despesas.  
- Monitorar o uso de recursos com o **Azure Cost Management** para identificar e controlar despesas desnecessárias.  

---

### 5. Alta Disponibilidade e Confiabilidade  
- Implantar VMs em um **Conjunto de Disponibilidade** ou em **Zonas de Disponibilidade** para proteger contra falhas de hardware.  
- Utilizar o **Azure Monitor** para acompanhamento proativo do desempenho e uso de recursos.  
- Configurar um plano de recuperação de desastres usando o **Azure Recovery Services Vault**.  

---

## Benefícios da Solução Proposta  
1. **Escalabilidade**: Ajusta automaticamente os recursos para lidar com picos de tráfego.  
2. **Eficiência de Custos**: Pague apenas pelos recursos utilizados, com opções de preços reservados.  
3. **Alta Disponibilidade**: Redundância garante tempo de inatividade mínimo e serviço confiável.  
4. **Melhoria na Segurança**: Firewalls gerenciados, acesso seguro e proteção DDoS aumentam a segurança.  
5. **Facilidade de Manutenção**: Os serviços do Azure automatizam backups, atualizações e escalabilidade.  

---

## Próximos Passos  
1. Aprovação do plano de migração proposto.  
2. Seleção dos tamanhos de VM no Azure com base nas métricas de uso atuais.  
3. Configuração e testes da infraestrutura no Azure.  
4. Migração dos dados e do aplicativo para o novo ambiente.  
5. Monitoramento e otimização contínuos após a implantação.  

