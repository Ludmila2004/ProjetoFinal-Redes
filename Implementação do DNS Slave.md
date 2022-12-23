# Implementação do DNS Slave

## Objetivo do DNS Slave
Transferência de dados do servidor master, nas próprias definições das zonas do domínio.

### Observações iniciais
Vale lembrar que para iniciar todo o processo, é necessário estar conectado com sua VPN
Para que isso aconteça use o comando:
```bash
$ openvpn3 session-start --config CONFIG_NAME
```

* Lembre-se de conectar-se na VM correta. Para isso, utilize o comando ```ssh```:
```bash
$ ssh administrador@10.9.13.122
```

## Guia de implementação

### Passo 1: Configuração e instalação do servidos DNS secundário (slave)
``` bash
$ sudo apt-get install bind9 dnsutils bind9-doc -y
``` 
![Captura de tela de 2022-12-23 08-39-32](https://user-images.githubusercontent.com/80183918/209334779-bf326c64-1950-4726-88e9-14a83092c6f4.png)

* Realizar a verificação do status do serviço:
``` bash
$ sudo systemctl status bind9
``` 
![Captura de tela de 2022-12-23 08-40-18](https://user-images.githubusercontent.com/80183918/209334990-31fe7dd2-59a0-47b2-9f0e-265396960c4c.png)

### Passo 2: Configurar zonas
``` bash
$ sudo nano /etc/bind/named.conf.local
``` 
![Captura de tela de 2022-12-23 08-41-19](https://user-images.githubusercontent.com/80183918/209335279-cc03815f-58ff-48c2-ab4d-b7276f921ca7.png)
![Captura de tela de 2022-12-23 08-51-17](https://user-images.githubusercontent.com/80183918/209335463-cf53e50b-6838-4f1f-8ff8-d586881bc71f.png)

### Passo 3: Checar a sintaxe
``` bash
$ sudo named-checkconf
``` 
![Captura de tela de 2022-12-23 08-52-09](https://user-images.githubusercontent.com/80183918/209335737-9191c4fa-8f3a-4ab4-97a8-cfcc9663eeed.png)

### Passo 4: Realizar o teste com o dig
``` bash
$ dig @10.9.13.118 ns1.grupo4.turma913.ifalara.local
``` 
![Captura de tela de 2022-12-23 08-56-36](https://user-images.githubusercontent.com/80183918/209336029-0a2987a7-750c-4afb-a84c-29ee3d6e1691.png)
