# Agent pool creation in Azure DevOps.md

The Azure DevOps agent enables the server to perform Continuous Integration (CI) and Continuous Delivery (CD) tasks.

### Note:
- Do not execute `./run.sh`, as the agent will go offline after the session ends. It is recommended to use the `svc.sh` command to install and start the agent as a service.
- You need to create a PAT (Personal Access Token) in Azure DevOps for the installation.

---

### 1. Access Azure DevOps
- Log in to Azure DevOps and navigate to the project where you want to create the *agent pool*.

---

### 2. Navigate to Project Settings
- On the left-hand menu, click **Project settings**.

---

### 3. Access Agent Pools
- Under **Pipelines**, click **Agent pools**.

---

### 4. Create a New Agent Pool
- Click **Add pool**.
- Provide a name for your *agent pool* and, if necessary, a description.
- Click **Create**.

---

### 5. Install an Agent
To add an agent to the *agent pool*, follow these steps:

1. **Select operating system**: Choose the operating system for the agent (Windows, Linux, or macOS) based on your requirements.  
2. **Download the agent**: Download the corresponding agent package for the chosen operating system.  
3. **Configure the agent**:  
   - Extract the agent package:  
     ```bash
     cd /var/www/; mkdir myagent; cd myagent
     tar zxvf vsts-agent-linux-x64-3.246.0.tar.gz
     ./config.sh` (Cannot be executed as root user)
     ```
   - During the configuration, provide the following information:
     - **Server URL**: The Azure DevOps URL, such as `https://dev.azure.com/YourOrganization`.
     - **Authentication type**: Use the "PAT" (Personal Access Token) option.
     - **Personal Access Token (PAT)**: Enter the previously generated token.
     - **Agent Pool**: The name of the *agent pool* created earlier.
     - **Agent Name**: The name of the machine where the agent will run.
     - **Work Folder**: Leave the default option.
     - **Run as Service**: Leave the default setting.
     - **Configure Autologin and Run**: Leave the default setting.

Once all information is entered, the agent will attempt to connect to Azure DevOps and complete the configuration.

---

### 6. Start the Agent
Run the following commands to install and start the agent as a service:

```bash
sudo ./svc.sh install root
sudo ./svc.sh start
sudo ./svc.sh status
```

---

### 7. Verify in Azure DevOps
- Go back to the *agent pool* in Azure DevOps and check if the new agent is active and available for use.


**************************************************************************************************************************************************************************************************************************************

# Criação do agent pool no Azure DevOps

O agente do Azure DevOps permite que o servidor execute tarefas de integração contínua (CI) e entrega contínua (CD).

### Observação:
- Não execute `./run.sh`, pois o agente ficará offline após a sessão ser encerrada. É preferível usar o comando `svc.sh` para instalar e iniciar o serviço do agente.
- Necessário criar um PAT (Personal Access Token) no Azure DevOps para a instalação.

---

### 1. Acesse o Azure DevOps
- Entre no Azure DevOps e acesse o projeto onde deseja criar o *agent pool*.

---

### 2. Navegue até as Configurações do Projeto
- No menu do lado esquerdo, clique em **Project settings** (Configurações do projeto).

---

### 3. Acesse Agent Pools
- Em **Pipelines**, clique em **Agent pools**.

---

### 4. Criar um Novo Agent Pool
- Clique em **Add pool** (Adicionar pool).
- Dê um nome ao seu *agent pool* e, se necessário, forneça uma descrição.
- Clique em **Create**.

---

### 5. Instalar um Agente
Para adicionar um agente ao *agent pool*, siga os passos abaixo:

1. **Selecionar sistema operacional**: Escolha o sistema operacional do agente (Windows, Linux ou macOS) conforme suas necessidades.
2. **Baixar o agente**: Faça o download do pacote correspondente ao sistema operacional escolhido.
3. **Configurar o agente**:
   - Extraia o pacote do agente:
     ```bash
     cd /var/www/; mkdir myagent; cd myagent
     tar zxvf vsts-agent-linux-x64-3.246.0.tar.gz
     ./config.sh` (Não pode ser executado com o user root)
     ```
   - Durante a configuração, forneça as seguintes informações:
     - **Server URL**: A URL do Azure DevOps, como `https://dev.azure.com/SuaOrganizacao`.
     - **Authentication type**: Use a opção "PAT" (Personal Access Token).
     - **Personal Access Token (PAT)**: Insira o token gerado previamente.
     - **Agent Pool**: O nome do *agent pool* criado anteriormente.
     - **Agent Name**: Nome da máquina onde o agente será executado.
     - **Work Folder**: Deixe a opção padrão.
     - **Run as Service**: Deixe na configuração padrão.
     - **Configure Autologin and Run**: Deixe na configuração padrão.

Após preencher todas as informações, o agente tentará se conectar ao Azure DevOps e será configurado.

---

### 6. Iniciar o Agente
Execute os seguintes comandos para instalar e iniciar o agente como um serviço:

```bash
sudo ./svc.sh install root
sudo ./svc.sh start
sudo ./svc.sh status
```

---

### 7. Verificar no Azure DevOps
- Retorne ao *agent pool* no Azure DevOps e verifique se o novo agente está ativo e disponível para uso.
