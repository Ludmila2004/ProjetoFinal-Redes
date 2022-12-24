
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
![Captura de tela de 2022-12-23 10-57-06](https://user-images.githubusercontent.com/80183918/209347549-b906d254-4353-4331-ba38-841393b4ce80.png)

### Passo 2
* Encaminhamento de pacotes de WAN para LAN, editando os parâmetros do arquivo /etc/ufw/sysctl.conf, tirando o comentário(#) da linha net/ipv4/ip_forwarding=1
> Comando
```bash
$ sudo nano /etc/ufw/sysctl.conf
```
![Captura de tela de 2022-12-23 10-57-43](https://user-images.githubusercontent.com/80183918/209347633-97ef0f7b-b12d-4452-831f-dec606a54663.png)

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
![Captura de tela de 2022-12-23 10-59-35](https://user-images.githubusercontent.com/80183918/209347925-50d38b20-45c9-4ca5-8762-1801054b5501.png)
* Deve estar dessa forma quando for executado o comando inicial:
![Captura de tela de 2022-12-23 10-59-52](https://user-images.githubusercontent.com/80183918/209347932-6ff4a29f-4357-45b6-b9de-048a35cf9705.png)

### Passo 5
* Como um arquivo foi apagado, é necessário criá-lo novamente, assim:
```bash
$ sudo nano /etc/rc.local
```
Após criado, deve inserir algumas descrições, dessa forma, ficando da seguinte maneira:

![Captura de tela de 2022-12-23 11-00-55](https://user-images.githubusercontent.com/80183918/209348299-4ef5cecf-ee81-4a27-ae94-73a8f28b370a.png)
![Captura de tela de 2022-12-23 11-02-37](https://user-images.githubusercontent.com/80183918/209348260-16a14715-c4f4-47da-8a97-12241d2ee39b.png)


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
![Captura de tela de 2022-12-23 11-07-51](https://user-images.githubusercontent.com/80183918/209349042-b84e20c2-9121-4265-ad6d-b9f7ec5fa001.png)

### Passo 8 
* Após o usuário entrar nas outras vpn's com os passos anteriormente ensinados, ultilie o comando:
```bash
$ sudo vi /etc/netplan/00-installer-config.yaml
```
* Irá abrir uma interface e o usuário deve ativar o gateway no SAMBA, NS1 e NS2
* Após isso, você deve comentar(#) o primeiro gateway e tirar o comentário do segundo, como tá representado abaixo.
* Lembre-se de alterar o gateway para o IP do seu próprio gateway
```bash
$ sudo netplan apply
```
![Captura de tela de 2022-12-23 11-11-08](https://user-images.githubusercontent.com/80183918/209349478-562f5fbe-95d7-43aa-9a7b-fe2b431c1351.png)

### Passo 9
* Para que o compartilhamento de arquivos esteja disponível externamente, adicione as informações do IPTABLES sobre portas
```bash
$ sudo vi /etc/rc.local
```
* Adicione  alguns textos, resultando em:
![Captura de tela de 2022-12-23 11-12-16](https://user-images.githubusercontent.com/80183918/209349623-0befec9f-b753-4131-bb15-8fbd14e63eff.png)

* Além disso, lembre-se de trocar algumas informações ajustando para a sua vpn, como IP e crie uma rota para as portas
