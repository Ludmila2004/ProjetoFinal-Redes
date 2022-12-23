
# Implementação do Gateway Server

## Objetivo do Gateway 
Bom funcionamento da rede e para a proteção do usuário.

### Observações iniciais
*Vale lembrar que para iniciar todo o processo, é necessário estar conectado com sua VPN
*Para que isso aconteça use o comando:
```bash
$ openvpn3 session-start --config CONFIG_NAME
```

* Lembre-se de conectar-se na VM correta. Para isso, utilize o comando ```ssh```:
```bash
$ ssh administrador@10.9.13.111
```

## Guia de implementação

### Passo 1
* Primeiramente faz-se necessário habilitar o firewall, somente assim é possivel configurar o gateway
> Comando
```bash
 $ sudo ufw enable
```
* Após fazer isso, é necessário permitir o acesso ssh 
> Comando
```bash
 $ sudo ufw allow ssh
```
* Para ver se realmente os comandos foram executados é possível ver o status
> Comando
```bash
 $ sudo ufw status
```

### Passo 2
* Encaminhamento de pacotes de WAN para LAN, editando os parâmetros do arquivo /etc/ufw/sysctl.conf, tirando o comentário(#) da linha net/ipv4/ip_forwarding=1
> Comando
```bash
$ sudo nano /etc/ufw/sysctl.conf
```

### Passo 3
* Ver se os nomes das interfaces WAN- enp0s3  e LAN- enp0s8 estão corretos
> Comando
```bash
$ ifconfig -a
```

### Passo 4
* Configurar as interfaces de rede, analisar se estão todas as informações, caso estejam dar um "apply"
> Comando
```bash
$ sudo nano /etc/netplan/50-cloud-init.yaml 
```
```bash
$ sudo netplan apply
```
* Deve estar dessa forma quando for executado o comando inicial:
```bash
network:
    ethernets:
        enp0s3:
            dhcp4: true
        enp0s8:
            addresses: [10.0.0.1/24]
            dhcp4: false              
    version: 2
```

### Passo 5
* Como um arquivo foi apagado, é necessário criá-lo novamente, assim:
```bash
$ sudo nano /etc/rc.local
```
* Após criado, deve inserir as seguintes descrições
```bash
#!/bin/bash

# /etc/rc.local

# Default policy to drop all incoming packets.
# Politica padrão para bloquear (drop) todos os pacotes de entrada
iptables -P INPUT DROP
iptables -P FORWARD DROP

# Accept incoming packets from localhost and the LAN interface.
# Aceita pacotes de entrada a partir das interfaces localhost e the LAN.
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i enp0s8 -j ACCEPT

# Accept incoming packets from the WAN if the router initiated the connection.
# Aceita pacotes de entrada a partir da WAN se o roteador iniciou a conexao
iptables -A INPUT -i enp0s3 -m conntrack \
--ctstate ESTABLISHED,RELATED -j ACCEPT

# Forward LAN packets to the WAN.
# Encaminha os pacotes da LAN para a WAN
iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT

# Forward WAN packets to the LAN if the LAN initiated the connection.
# Encaminha os pacotes WAN para a LAN se a LAN inicar a conexao.
iptables -A FORWARD -i enp0s3 -o enp0s8 -m conntrack \
--ctstate ESTABLISHED,RELATED -j ACCEPT

# NAT traffic going out the WAN interface.
# Trafego NAT sai pela interface WAN
iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE

# rc.local needs to exit with 0
# rc.local precisa sair com 0
exit 0
```

### Passo 6
* Após tudo, o usuário deve converter o arquivo acima para poder executá-lo e ser possível inicializar no boot
```bash
$ sudo chmod 755 /etc/rc.local
```

* Para ver se realmente os comandos foram executados é possível ver o status
> Comando
```bash
$ sudo ufw status
```
### Passo 6
* Reinicializar a máquina 
> Comando
```bash
$ sudo reboot
```
### Passo 7
* A partir daqui, a finalidade é permitir que o serviço de compartilhamento de arquivos esteja disponível externamente
* Após ter reinicializado, entre novamente na sua vpn(DO GATEWAY)e ultilize o comando abaixo para entrar no seu diretório
> Comando
```bash
$ cd /etc/netplan/
$ ls -la
```
* Depois de executar esses comandos, como a configuração de rede passou a ser feita através da ferramenta Netplan, que mantém as configurações da rede em um arquivo do tipo YAML. O arquivo de configuração de rede nessas versões fica no seguinte endereço:
```bash
$ cat /etc/netplan/00-installer-config.yaml
```
### Passo 8 
* Após o usuário entrar nas outras vpn's com os passos anteriormente ensinados, ultilie o comando:
```bash
$ sudo vi /etc/netplan/00-installer-config.yaml
```
* Irá abrir uma interface e o usuário deve ativar o gateway no SAMBA, NS1 e NS2
* Após isso, você deve comentar(#) o primeiro gateway e tirar o comentário do segundo, como tá representado abaixo.
* Lembre-se de alterar o gateway para o IP do seu próprio gateway
----- IMAGEM------

```bash
$ sudo netplan apply
```

### Passo 9
* Para que o compartilhamento de arquivos esteja disponível externamente, adicione as informações do IPTABLES sobre portas
```bash
$ sudo vi /etc/rc.local
```
* Adicione os seguintes textos
```bash
#Recebe pacotes na porta 445 da interface externa do gw e encaminha para o servidor interno na porta 445
iptables -A PREROUTING -t nat -i enp0s3 -p tcp --dport 445 -j DNAT --to 10.0.0.100:445
iptables -A FORWARD -p tcp -d 10.0.0.100 --dport 445 -j ACCEPT

#Recebe pacotes na porta 139 da interface externa do gw e encaminha para o servidor interno na porta 139
iptables -A PREROUTING -t nat -i enp0s3 -p tcp --dport 139 -j DNAT --to 10.0.0.100:139
iptables -A FORWARD -p tcp -d 10.0.0.100 --dport 139 -j ACCEPT
```
