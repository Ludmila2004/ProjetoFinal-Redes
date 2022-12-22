
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

### Passo 2
* Instalação do servidor samba para a VM de IP ```10.9.13.116```.
```bash
$ sudo apt update
$ sudo apt install samba
```

### Passo 3
* Verificação da plena execução do samba.
> O comando abaixo apresenta o caminho completo relativo ao samba.
```bash
$ whereis samba
```
> Apresentação do status do samba no sistema:
```bash
$ sudo systemctl status smbd
```

> Exibição das portas que estão sendo utilizadas: 
```bash
$ netstat -an | grep LISTEN
```
### Passo 4
* Realizar o ```backup``` da configuração do samba.
```bash
$ sudo cp /etc/samba/smb.conf{,.backup}
$ ls -la
-rw-r--r--  1 root root 8942 Mar 22 20:55 smb.conf
-rw-r--r--  1 root root 8942 Mar 23 01:42 smb.conf.backup
$ sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf.backup | grep . > /etc/samba/smb.conf'
```

### Passo 6
* Editar o arquivo ```/etc/samba/smb.conf```, de modo a adicionar as interfaces de rede e alterar a seção ```public```.
> Lembre-se de separar o nome das interfaces por espaços. 
```bash
$ sudo nano /etc/samba/smb.conf
```
> O arquivo deve possuir essa cara:
```bash
[global]
   workgroup = WORKGROUP
   netbios name = samba-srv
   security = user
   server string = %h server (Samba, Ubuntu)
   interfaces = 127.0.0.1/8 ens160 ens192
   bind interfaces only = yes
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d
   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes
   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .
   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes
[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700
[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
[homes]
   comment = Home Directories
   browseable = yes
   read only = no
   create mask = 0700
   directory mask = 0700
   valid users = %S
[public]
   comment = public anonymous access
   path = /samba/public
   browsable =yes
   create mask = 0660
   directory mask = 0771
   writable = yes
   guest ok = no
   valid users = @sambashare
   #guest only = yes
   #force user = nobody
   #force create mode = 0777
   #force directory mode = 0777
```
* Reinicie o servidor, para salvar as alterações.
```bash
$ sudo systemctl restart smbd
```

### Passo 7
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

### Passo 8
* Criar um diretório para compartilhar o samba em rede. 
```bash
$ mkdir sambashare
$ sudo mkdir -p /samba/public
```

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

## Verificação da efetividade dos passos realizados
* Digite o endereço IP da máquina responsável pelo servidor samba no ```Windows Explorer```, por exemplo, da seguinte forma:
```bash
\\10.9.13.116
```

