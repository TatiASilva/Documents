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

### Resumo dos Passos:

1. Identificar o IP correto do nó manager (não utilizar IPs de interfaces *bridge*).
2. Remover o manager do Swarm existente (`docker swarm leave --force`).
3. Iniciar o Swarm novamente no manager com o IP correto (`docker swarm init --advertise-addr <IP_CORRETO>`).
4. Conectar o worker ao Swarm usando o IP correto e o novo token (`docker swarm join --token <token_gerado> <IP_CORRETO>:xxxx`).
5. Verificar conectividade de rede entre os nós (`ping`).
