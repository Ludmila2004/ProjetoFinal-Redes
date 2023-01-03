
# Implementação do Gateway Server

## Objetivo do Gateway 
Bom funcionamento da rede e para a proteção do usuário.

### Observações iniciais
* Vale lembrar que para iniciar todo o processo, é necessário estar conectado com sua VPN
* Para que isso aconteça use o comando:
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
![Captura de tela de 2022-12-23 10-57-06](https://user-images.githubusercontent.com/80183918/209347549-b906d254-4353-4331-ba38-841393b4ce80.png)

### Passo 2
* Encaminhamento de pacotes de WAN para LAN, editando os parâmetros do arquivo /etc/ufw/sysctl.conf, tirando o comentário(#) da linha net/ipv4/ip_forwarding=1
> Comando
```bash
$ sudo nano /etc/ufw/sysctl.conf
```
![Captura de tela de 2022-12-23 10-57-43](https://user-images.githubusercontent.com/80183918/209347633-97ef0f7b-b12d-4452-831f-dec606a54663.png)

* Para aplicar as alterações:
```bash
$ sudo netplan apply
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
$ sudo nano /etc/netplan/00-installer-config.yaml
```
```bash
$ sudo netplan apply
```
![Captura de tela de 2022-12-23 10-59-35](https://user-images.githubusercontent.com/80183918/209347925-50d38b20-45c9-4ca5-8762-1801054b5501.png)
* Deve estar dessa forma quando for executado o comando inicial:
![Captura de tela de 2022-12-29 10-34-44](https://user-images.githubusercontent.com/94541734/209961317-6b71cbcd-5964-46eb-9843-724a6a71aa4a.png)


### Passo 5
* Como um arquivo foi apagado, é necessário criá-lo novamente, assim:
```bash
$ sudo nano /etc/rc.local
```
Após criado, deve inserir algumas descrições, dessa forma, ficando da seguinte maneira:

![Captura de tela de 2023-01-03 14-27-42](https://user-images.githubusercontent.com/94541734/210409794-e0ef3d5a-00ee-4166-9f55-dc99be3b839d.png)



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
![Captura de tela de 2022-12-23 11-09-15](https://user-images.githubusercontent.com/80183918/209349236-38edf215-2f5b-430c-9c9d-9dc5e313365d.png)

### Passo 7
* Reinicializar a máquina 
> Comando
```bash
$ sudo reboot
```
### Passo 8
*  A fim de testar, execute os seguintes comandos
> Comando para testar conexão gw e ns1
```bash
$ ping 10.9.13.118
```
![Captura de tela de 2023-01-03 14-34-29](https://user-images.githubusercontent.com/94541734/210410755-0922d477-2dec-44d1-8194-9353da5618ea.png)



