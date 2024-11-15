# **Documentation - Network Configuration in Azure with Virtual Machines**

## **Objective**

Create an environment with two virtual machines (VMs) in different networks, where one (`jump-server-vm01`) has both a public and private IP, and the other (`poc-server-vm01`) only has a private IP. Additionally, a third machine (`dev-server-vm01`) will be added, which will also be accessible only through `jump-server-vm01`.

---

## **Requirements**

1. **SSH access** to `poc-server-vm01` and `dev-server-vm01` exclusively via `jump-server-vm01`.
2. Proper network configuration to avoid IP conflicts.
3. VNet Peering to connect different networks.
4. Network Security Group (NSG) rules to restrict access.

---

## **Initial Questions Raised**

### 1. **Private IP Address Conflict**

   - **Question**: "Why do the VMs have the same private IP, even though they are in different networks?"  
   - **Answer**: Both VMs were in the same VNet, which shared the same address space (`10.0.0.0/16`). This caused private IPs to be assigned from the same range.

   **Solution**: Create different VNets with distinct address spaces to ensure that the VMs have unique private IPs.

---

### 2. **Recreating `poc-server-vm01` in a New VNet**

   - **Task**: Recreate `poc-server-vm01` in a new VNet with the address space `10.1.0.0/16` and the subnet `10.1.1.0/24`, without a public IP.

   **Steps**:
   1. **Create the new VNet**:  
      - Address space: `10.1.0.0/16`  
      - Subnet: `10.1.1.0/24`
   2. **Create the `poc-server-vm01` VM**:  
      - Associate it with the new VNet and subnet, without a public IP.

---

### 3. **Restricting Access to `poc-server-vm01` via `jump-server-vm01`**

   - **Task**: Configure the NSG for `poc-server-vm01` to allow SSH access only from `jump-server-vm01`.
   - **Question**: "How can the NSG be configured to ensure that `poc-server-vm01` is only accessible from `jump-server-vm01`?"

   **Solution**:
   1. Access the **NSG** for `poc-server-vm01`.
   2. Edit or add a new **Inbound Security Rule**:
      - **Source**: `IP Addresses`
      - **Source IP address range**: `10.0.0.4` (private IP of `jump-server-vm01`)
      - **Destination**: `Any`
      - **Destination port ranges**: `22`
      - **Protocol**: `TCP`
      - **Action**: `Allow`
      - **Priority**: Set a low priority, such as `100`.

   **Explanation**: Peering between the networks enables traffic to flow between different VNets. The NSG allows controlling access to the VM based on the source IP. In this case, access is restricted to `jump-server-vm01` connecting to `poc-server-vm01`.

---

### 4. **Unable to Connect to `poc-server-vm01` via SSH**

   - **Problem**: SSH connection to `poc-server-vm01` from `jump-server-vm01` failed.
   - **Solution**: The issue was due to the absence of **VNet Peering** between the VNets. Peering enables communication between different networks.

   **Steps to Configure Peering**:
   1. Go to the **VNet for `jump-server-vm01`**.
   2. Select the **Peering** option and add a peering to the VNet for `poc-server-vm01`.
   3. Repeat the process for the **VNet for `poc-server-vm01`**, adding a peering back to the VNet for `jump-server-vm01`.

---

### 5. **Creating a New VM (`dev-server-vm01`)**

   - **Task**: Create the `dev-server-vm01` VM with a private IP and no public IP, ensuring it is accessible only via `jump-server-vm01`.

   **Solution**:
   1. Create a new VNet for `dev-server-vm01`:  
      - Address space: `10.2.0.0/16`  
      - Subnet: `10.2.0.0/24`
   2. Create the `dev-server-vm01` VM and associate it with this VNet.
   3. Configure **VNet Peering** between the VNet for `jump-server-vm01` and the VNet for `dev-server-vm01`.
   4. Configure the **NSG for `dev-server-vm01`** to allow only SSH access from `jump-server-vm01`.

---

## **Summary of Technical Solutions**

1. **Network Addressing (VNets)**:
   - `jump-server-vm01` VNet: `10.0.0.0/16`
   - `poc-server-vm01` VNet: `10.1.0.0/16`
   - `dev-server-vm01` VNet: `10.2.0.0/16`

2. **VNet Peering**:
   - Peering between all VNets to enable communication between VMs in different networks.

3. **Security Rules (NSG)**:
   - NSG configurations to allow SSH traffic only from `jump-server-vm01`.

4. **Restricted Access**:
   - Both `poc-server-vm01` and `dev-server-vm01` can only be accessed via SSH from `jump-server-vm01`, based on the configured NSG rules and VNet Peering.

---

This document details the process and adjustments made to configure virtual machines in different networks, with SSH access control using Azure's networking tools like VNets, Peering, and NSGs.


******************************************************************************************************************************************************************************************************************************

# **Documentação - Configuração de Rede no Azure com Máquinas Virtuais**

## **Objetivo**

Criar um ambiente com duas máquinas virtuais (VMs) que estão em redes diferentes, onde uma (`jump-server-vm01`) tem um IP público e privado, e a outra (`poc-server-vm01`) tem apenas IP privado. Além disso, uma terceira máquina (`dev-server-vm01`) será adicionada, acessível também apenas pela `jump-server-vm01`.

---

## **Requisitos**
1. **Acesso SSH** à `poc-server-vm01` e `dev-server-vm01` somente pela `jump-server-vm01`.
2. Configurações de rede adequadas, evitando conflitos de IPs.
3. VNet Peering para conectar redes diferentes.
4. Regras de segurança de rede (NSG) para restringir acessos.

---

## **Questões Inicialmente Levantadas**

### 1. **Problema de IPs Privados Iguais**
   - **Pergunta**: "Por que as VMs possuem o mesmo IP privado, mesmo estando em redes diferentes?"
   - **Resposta**: As duas VMs estavam na mesma VNet, que tinha o mesmo espaço de endereçamento (`10.0.0.0/16`). Isso fez com que os IPs privados fossem atribuídos dentro do mesmo intervalo.
   
   **Solução**: Criar VNets diferentes com espaços de endereçamento distintos para garantir que as VMs tenham IPs privados únicos.

### 2. **Recriação da `poc-server-vm01` em uma Nova VNet**
   - **Tarefa**: Recriar a `poc-server-vm01` em uma nova VNet com o espaço de endereçamento `10.1.0.0/16` e a subnet `10.1.1.0/24`, sem IP público.

   **Passos**:
   1. **Criar a nova VNet**: 
      - Espaço de endereçamento: `10.1.0.0/16`
      - Subnet: `10.1.1.0/24`
   2. **Criar a VM `poc-server-vm01`**:
      - Associar à nova VNet e subnet, sem IP público.

### 3. **Restrição de Acesso à `poc-server-vm01` pela `jump-server-vm01`**
   - **Tarefa**: Configurar o NSG da `poc-server-vm01` para permitir acesso SSH apenas a partir da `jump-server-vm01`.
   - **Pergunta**: "Como configurar o NSG para permitir que a `poc-server-vm01` seja acessada apenas pela `jump-server-vm01`?"

   **Solução**:
   1. Acesse o **NSG** da `poc-server-vm01`.
   2. Edite ou adicione uma nova **Inbound Security Rule**:
      - **Source**: `IP Addresses`
      - **Source IP address range**: `10.0.0.4` (IP privado da `jump-server-vm01`)
      - **Destination**: `Any`
      - **Destination port ranges**: `22`
      - **Protocol**: `TCP`
      - **Action**: `Allow`
      - **Priority**: Atribuir prioridade baixa, como `100`.

   **Explicação**: O peering entre as redes permite que o tráfego flua entre VNets diferentes. A NSG permite controlar quem pode acessar a VM com base no IP de origem. Neste caso, permitimos acesso apenas da máquina `jump-server-vm01` para `poc-server-vm01`.

### 4. **Falha ao Conectar via SSH à `poc-server-vm01`**
   - **Problema**: Não foi possível se conectar à `poc-server-vm01` via SSH a partir da `jump-server-vm01`.
   - **Solução**: A causa foi a falta de configuração do **VNet Peering** entre as VNets. O peering permite que redes diferentes se comuniquem.

   **Passos para Configurar o Peering**:
   1. Vá até a **VNet da `jump-server-vm01`**.
   2. Selecione a opção **Peering** e adicione um peering para a VNet da `poc-server-vm01`.
   3. Faça o mesmo para a **VNet da `poc-server-vm01`**, adicionando um peering de volta para a VNet da `jump-server-vm01`.

### 5. **Criação de Nova VM (`dev-server-vm01`)**
   - **Tarefa**: Criar a VM `dev-server-vm01` com IP privado e sem IP público, e garantir que ela seja acessada apenas pela `jump-server-vm01`.
   
   **Solução**:
   1. Criar uma nova VNet para `dev-server-vm01`:
      - Espaço de endereçamento: `10.2.0.0/16`
      - Subnet: `10.2.0.0/24`
   2. Criar a VM `dev-server-vm01` e associá-la a essa VNet.
   3. Configurar o **VNet Peering** entre a VNet da `jump-server-vm01` e a VNet da `dev-server-vm01`.
   4. Configurar o **NSG da `dev-server-vm01`** para permitir apenas o acesso SSH a partir da `jump-server-vm01`.

---

## **Resumo das Soluções Técnicas**

1. **Endereçamento de Redes (VNets)**:
   - `jump-server-vm01` VNet: `10.0.0.0/16`
   - `poc-server-vm01` VNet: `10.1.0.0/16`
   - `dev-server-vm01` VNet: `10.2.0.0/16`

2. **VNet Peering**:
   - Peering entre todas as VNets para permitir comunicação entre VMs em redes diferentes.

3. **Regras de Segurança (NSG)**:
   - Configurações NSG para permitir tráfego SSH apenas a partir da `jump-server-vm01`.

4. **Acesso Restrito**:
   - `poc-server-vm01` e `dev-server-vm01` só podem ser acessadas via SSH a partir da `jump-server-vm01` com base nas regras NSG e peering configurados.

---

Essa documentação detalha o processo e os ajustes feitos para configurar as máquinas virtuais em redes diferentes, com controle de acesso via SSH, usando as ferramentas de rede do Azure como VNets, Peering e NSG.
