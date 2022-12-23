
# Implementação do Samba 

## Objetivo
Implementar um serviço que forneça o compartilhamento de arquivos com o SAMBA.

### Observações iniciais
* Lembre-se de conectar-se na VM correta. Para isso, utilize o comando ```ssh```:
```bash
$ ssh administrador@10.9.13.116
```

* A máquina virtual utilizada deve ser nomeada como ```samba-srv```, ```smb-srv``` ou simplesmente ```smb```, de modo a tornar o ambiente mais organizado. 
Para isso, utilize o seguinte comando:
```bash
$ sudo hostnamectl set-hostname samba-srv
$ reboot
```

## Guia de implementação

### Passo 1
* Definição do IP (```addresses```) da rede interna para a máquina responsável por implementar o serviço Samba. 
> Comando
```bash
$ sudo nano /etc/netplan/00-installer-config.yaml
```

> Representação do arquivo
```bash
network:
    renderer: networkd
    ethernets:
        ens160:
            dhcp4: false 
            addresses: [10.9.13.116/24]
            gateway4: 10.9.13.1
            
        ens192:
            addresses: [192.168.13.58/28]
            #gateway4: 10.9.24.1
            #dhcp4: false 
    version: 2
```

* Aplique as alterações e verifique se as alterações foram salvas e aplicadas corretamente:
```bash
$ sudo netplan apply
$ ifconfig -a
```

![Captura de tela de 2022-12-23 10-32-16](https://user-images.githubusercontent.com/80183918/209344307-1acaf3e6-0469-4535-a76c-a020af3dfcc5.png)


### Passo 2
* Instalação do servidor samba para a VM de IP ```10.9.13.116```.
```bash
$ sudo apt update
```
![Captura de tela de 2022-12-23 10-33-33](https://user-images.githubusercontent.com/80183918/209344487-b0f87093-a184-455e-9524-9907b614588c.png)
```bash
$ sudo apt install samba
```
![Captura de tela de 2022-12-23 10-34-07](https://user-images.githubusercontent.com/80183918/209344553-22e0205a-ee3d-415e-a1b7-c225637155a3.png)

### Passo 3
* Verificação da plena execução do samba.
> O comando abaixo apresenta o caminho completo relativo ao samba.
```bash
$ whereis samba
```
![Captura de tela de 2022-12-23 10-35-59](https://user-images.githubusercontent.com/80183918/209344824-9e016791-da15-4c80-96b7-6612b819fdc8.png)
> Apresentação do status do samba no sistema:
```bash
$ sudo systemctl status smbd
```
![Captura de tela de 2022-12-23 10-36-57](https://user-images.githubusercontent.com/80183918/209344958-2c4490c9-94bb-4f7c-8af5-bf5bb493a185.png)
> Exibição das portas que estão sendo utilizadas: 
```bash
$ netstat -an | grep LISTEN
```
![Captura de tela de 2022-12-23 10-37-40](https://user-images.githubusercontent.com/80183918/209345030-210b1a87-a081-42f1-9418-9c175ec54273.png)

### Passo 4
* Realizar o ```backup``` da configuração do samba.
```bash
$ sudo cp /etc/samba/smb.conf{,.backup}
$ ls -la
$ sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf.backup | grep . > /etc/samba/smb.conf'
```
![Captura de tela de 2022-12-23 10-39-13](https://user-images.githubusercontent.com/80183918/209345846-fe628b5e-33fe-408d-9798-0748e25da1f5.png)

### Passo 5
* Editar o arquivo ```/etc/samba/smb.conf```, de modo a adicionar as interfaces de rede e alterar a seção ```public```.
> Lembre-se de separar o nome das interfaces por espaços. 
```bash
$ sudo nano /etc/samba/smb.conf
```
> O arquivo deve possuir essa cara:
![Captura de tela de 2022-12-23 10-41-18](https://user-images.githubusercontent.com/80183918/209345949-657cb017-e6cb-401b-a805-782e99db7325.png)

```
* Reinicie o servidor, para salvar as alterações.
```bash
$ sudo systemctl restart smbd
```

### Passo 6
* Criar um usuário para que ele possa utilizar o compartilhamento de arquivos via ```samba```.
> Recomendação de usuário e de senha:
> 
> ```usuário```: aluno
> 
> ```senha```: alunoifal
```bash
$ sudo adduser aluno
Adding user `aluno' ...
Adding new group `aluno' (1001) ...
Adding new user `aluno' (1001) with group `aluno' ...
Creating home directory `/home/aluno' ...
Copying files from `/etc/skel' ...
New password: 
Retype new password: 
passwd: password updated successfully
Changing the user information for aluno
Enter the new value, or press ENTER for the default
	Full Name []: Aluno de SRED no IFAL Arapiraca
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y
```
* Vincular o usuário criado ao serviço samba, de modo que ele possa ter acesso ao compartilhamento de arquivos. 
> Utilize a mesma senha daquela utilizada para a criação do usuário: ```alunoifal```. 
```bash
$ sudo smbpasswd -a aluno
New SMB password:
Retype new SMB password:
Added user aluno.

$ sudo usermod -aG sambashare aluno
```
![Captura de tela de 2022-12-23 10-46-15](https://user-images.githubusercontent.com/80183918/209346098-67ca39c9-e29e-4518-b677-49d956ff5b28.png)


### Passo 7
* Criar um diretório para compartilhar o samba em rede. 
```bash
$ mkdir sambashare
$ sudo mkdir -p /samba/public
```
![Captura de tela de 2022-12-23 10-47-41](https://user-images.githubusercontent.com/80183918/209346290-c93c455e-9fec-468c-b349-f93db266fce8.png)

* Habilitar permissões para que qualquer um utilize o compartilhamento de arquivos de maneira pública.
```bash
$ cd /samba
$ sudo chown -R nobody:nogroup /samba/public
$ ls -la
$ sudo chmod -R 0775 /samba/public
$ ls -la
$ sudo chgrp sambashare /samba/public
$ ls -la
```
![Captura de tela de 2022-12-23 10-48-09](https://user-images.githubusercontent.com/80183918/209346363-214026e5-13bd-4324-937c-5a907b963298.png)
![Captura de tela de 2022-12-23 10-48-48](https://user-images.githubusercontent.com/80183918/209346474-20e474e5-2103-4f8f-bdff-b472d3a20f1b.png)


## Verificação da efetividade dos passos realizados
* Digite o endereço IP da máquina responsável pelo servidor samba no ```Windows Explorer```, por exemplo, da seguinte forma:
```bash
\\10.9.13.116
```

