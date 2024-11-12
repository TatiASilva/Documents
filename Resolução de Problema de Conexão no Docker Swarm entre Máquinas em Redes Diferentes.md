# Use Case: Resolving Docker Swarm Connection Issue Between Machines on Different Networks

### Context

While setting up a Docker Swarm cluster, an error occurred when trying to connect a worker node to a manager node. The nodes were on different networks, and Docker was using IPs from virtual network interfaces (*bridge*), which caused a connection error.

### Error Encountered

When attempting to run the `docker swarm join` command on the worker node to connect to the manager node, the following error was returned:
*Error response from daemon: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing dial tcp 172.xx.xx.xx:xxxx: connect: connection refused"*

### Cause of the Error

The error occurred because Docker was using the IP address of the `docker0` interface (a virtual network interface created by Docker), which is used for internal container communication and is not suitable for communication between machines on different networks. The IP address used was not accessible by the worker node.

### Solution

#### Step 1: Identify the Correct IP of the Manager Node

When connecting to the manager node, use the IP address of the network interface accessible by other nodes on the network.

Find the IP of the network interface connected to your local network, likely something like 192.168.x.x or 10.x.x.x.

On the manager node, it was found that Docker was using the *bridge* interface IP, which was not accessible by the worker. Therefore, it was necessary to find the correct network interface IP (connected to the public or private network between the VMs).

Instead of using the docker0 or br-120efe9d55c0 interfaces, you should find the actual IP of the VMs, which is usually on the main network interface, such as eth0 or ens0.

    Command Used: On the manager node, the `ifconfig` (or `ip a` in some cases) command was used to identify the primary network interface IP: *ifconfig*

The correct IP of the public network interface was identified as `10.xx.x.x`.

#### Step 2: Leave the Existing Swarm on the Manager Node

Since the manager node was already part of a Swarm using the wrong IP, it needed to be removed from the current Swarm.

    Command Used: *docker swarm leave --force*

This command forces the node to leave the Swarm cluster.

#### Step 3: Initialize Docker Swarm with the Correct IP

After leaving the Swarm, the manager node was reconfigured to advertise the correct IP (`10.xxx.x.x`) when initializing the cluster.

    Command Used: *docker swarm init --advertise-addr 10.xxx.x.x*

This command initialized the Swarm on the manager with the correct IP, which would be accessible by the worker node.

#### Step 4: Connect the Worker Node to the Swarm Cluster

On the worker node, the `docker swarm join` command was used again, this time with the new token and correct IP of the manager.

    Command Used on Worker: *docker swarm join --token <generated_token> 10.xxx.x.x:xxxx*

#### Network Connectivity Check

To ensure the worker could communicate with the manager, the `ping` command was used on the worker to verify connectivity with the manager's IP: *ping 10.xxx.x.x*

### Result

After these steps, the worker node successfully connected to the manager node, and the Docker Swarm cluster was correctly set up.

### Summary of Steps

1. Identify the correct IP of the manager node (avoid using *bridge* interface IPs).
2. Remove the manager from the existing Swarm (`docker swarm leave --force`).
3. Re-initialize the Swarm on the manager with the correct IP (`docker swarm init --advertise-addr <CORRECT_IP>`).
4. Connect the worker to the Swarm using the correct IP and new token (`docker swarm join --token <generated_token> <CORRECT_IP>:xxxx`).
5. Check network connectivity between the nodes (`ping`).


**************************************************************************************************************

# Caso de Uso: Resolução de Problema de Conexão no Docker Swarm entre Máquinas em Redes Diferentes

### Contexto

Durante a configuração de um cluster Docker Swarm, houve um erro ao tentar conectar um nó worker a um nó manager. As máquinas (nós) estavam em redes diferentes e o Docker estava usando os IPs de interfaces de rede virtuais (*bridge*), o que gerou um erro de conexão.

### Erro Encontrado

Ao tentar executar o comando `docker swarm join` no nó worker para conectar ao nó manager, foi retornado o seguinte erro:
*Error response from daemon: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing dial tcp 172.xx.xx.xx:xxxx: connect: connection refused"*

### Causa do Erro

O erro ocorreu porque o Docker estava utilizando o endereço IP da interface `docker0` (uma interface de rede virtual criada pelo Docker), que é usada para a comunicação interna dos contêineres, não sendo apropriada para a comunicação entre máquinas em redes diferentes. O endereço IP utilizado não era acessível pelo nó worker.

### Solução

#### Passo 1: Identificar o IP Correto do Nó Manager

Ao tentar se conectar ao nó manager, você deve usar o endereço IP da interface de rede que está acessível pelos outros nós na rede.

Procure o IP da interface de rede que está conectada à sua rede local, provavelmente será algo como 192.168.x.x ou 10.x.x.x.

No nó manager, foi verificado que o Docker estava utilizando o IP da interface *bridge*, mas esse IP não era acessível pelo worker. Portanto, era necessário encontrar o IP da interface de rede correta (aquela conectada à rede pública ou privada entre as VMs).

Em vez de usar as interfaces docker0 ou br-120efe9d55c0, você deve encontrar o IP real das VMs. Isso geralmente está na interface de rede principal, como eth0 ou ens0.

    Comando Utilizado: No nó manager, foi utilizado o comando `ifconfig` (ou `ip a` em alguns casos) para identificar o IP da interface de rede principal: *ifconfig*

Foi identificado que o IP correto da interface de rede pública era `10.xx.x.x`.

#### Passo 2: Sair do Swarm Existente no Nó Manager

Como o nó manager já fazia parte de um Swarm utilizando o IP errado, foi necessário removê-lo do Swarm atual.

    Comando Utilizado: *docker swarm leave --force*

Esse comando força o nó a sair do cluster Swarm.

#### Passo 3: Inicializar o Docker Swarm com o IP Correto

Após sair do Swarm, o nó manager foi reconfigurado para anunciar o IP correto (`10.xxx.x.x`) ao inicializar o cluster.

    Comando Utilizado: *docker swarm init --advertise-addr 10.xxx.x.x*

Esse comando inicializou o Swarm no manager com o IP correto, que será acessível pelo nó worker.

#### Passo 4: Conectar o Nó Worker ao Cluster Swarm

No nó worker, o comando `docker swarm join` foi utilizado novamente, desta vez com o novo token gerado e o IP correto do manager.

    Comando Utilizado no Worker: *docker swarm join --token <token_gerado> 10.xxx.x.x:xxxx*

Verificação de Conectividade
Para garantir que o worker conseguia se comunicar com o manager, foi utilizado o comando `ping` no worker para verificar a conectividade com o IP do manager: *ping 10.xxx.x.x*

### Resultado

Após esses passos, o nó worker foi conectado com sucesso ao nó manager e o cluster Docker Swarm foi configurado corretamente.

### Resumo dos Passos

1. Identificar o IP correto do nó manager (não utilizar IPs de interfaces *bridge*).
2. Remover o manager do Swarm existente (`docker swarm leave --force`).
3. Iniciar o Swarm novamente no manager com o IP correto (`docker swarm init --advertise-addr <IP_CORRETO>`).
4. Conectar o worker ao Swarm usando o IP correto e o novo token (`docker swarm join --token <token_gerado> <IP_CORRETO>:xxxx`).
5. Verificar conectividade de rede entre os nós (`ping`).
