# Implementação do DNS Master

## Instalação
* Instalar o bind9 via apt-get, pois é a aplicação de DNS que roda no servidor.
```bash
$ sudo apt-get install bind9 dnsutils bind9-doc 
```
![Imagem do WhatsApp de 2022-12-29 à(s) 20 09 31](https://user-images.githubusercontent.com/103438153/210019745-96a9bfff-56aa-4e33-98e0-84132243491f.jpg)

* Verificação o status do serviço:
```bash
$ sudo systemctl status bind9
```
![WhatsApp Image 2022-12-23 at 11 37 38](https://user-images.githubusercontent.com/80183918/209551961-5e8ca2a1-4acf-44cb-bc1b-aa508575f856.jpeg)

* Caso, não estiver rodando:
```bash
$ sudo systemctl enable bind9
```


## Diretórios do bind
* Os arquivos do bind no diretório /etc/bind.
```bash
$ ls /etc/bind
```
![Imagem do WhatsApp de 2022-12-19 à(s) 17 15 31](https://user-images.githubusercontent.com/103438153/210019432-183bbbea-1a9e-4ffa-aff7-cd22adb53dc0.jpg)


## Zonas
* As zonas são especificadas em arquivos db. Sendo criado ppor um diretório para armazendar os arquivos de zonas, que sera o diretório /etc/bind/zones.
```bash
$ sudo mkdir /etc/bind/zones
```

### Zona direta 
* O arquivo db.labredes.ifalarapiraca.local cos nomes terá o domínio das máquinas  labredes.ifalarapiraca.local.Por sua vez, faremos uma cópia do arquivo /etc/bind/db.empt
```bash
$ sudo cp /etc/bind/db.empty /etc/bind/zones/db.labredes.ifalarapiraca.local 
```
![Imagem do WhatsApp de 2022-12-29 à(s) 20 20 55](https://user-images.githubusercontent.com/103438153/210020392-e6457bdd-e6b9-4f0f-b6d4-eb5aae18440c.jpg)


### Zona reversa
* Se utiliza quando não se conhece o IP mas, sabe-se o nome do host.Assim, temos que criar a zona reversa a partir do arquivo /etc/bind/db.127
```bash
  $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.13.rev
```
![Imagem do WhatsApp de 2022-12-29 à(s) 20 20 55](https://user-images.githubusercontent.com/103438153/210020423-9a7d5596-137d-4fc9-9a61-9bba0ac4b527.jpg)

## Editar arquivos db
### Zona direta: db.labredes.ifalarapiraca.local
* Editar o arquivo db.labredes.ifalarapiraca.local colocando informações do domínio
```bash
    $ sudo nano db.labredes.ifalarapiraca.local 
```
* Adicionando o domínio
```bash
 $ sudo nano grupo4.turma913.ifalarapiraca.local
```

## Configuração do named.conf.local
* Será ativado as zonas descritas nos arquivos db deve-se editar o arquivo de configuracão do bind. As zonas são adicionadas em /etc/bind/named.conf.local
```bash
$ sudo nano /etc/bind/named.conf.local
```

![Imagem do WhatsApp de 2022-12-20 à(s) 17 09 33](https://user-images.githubusercontent.com/103438153/210020533-e47bb9c0-2558-4596-94cf-05852c3de70b.jpg)

## Verificação de sintaxe
* Executar o comando named-checkconf. Este scritp checa os arquivos /etc/bind/named.conf.local.
```bash
$ sudo named-checkconf
```
![WhatsApp Image 2022-12-23 at 11 37 38 (1)](https://user-images.githubusercontent.com/80183918/209552084-a63f63dd-8a9f-4346-9f41-bbfeb1f9a041.jpeg)


## Configure para somente resolver endereços IPv4
```bash
$ sudo nano /etc/default/named
```

* Adicione a linha OPTIONS="-4 -u bind"
```bash
# run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-4 -u bind"
```
![WhatsApp Image 2022-12-23 at 11 37 38 (2)](https://user-images.githubusercontent.com/80183918/209552230-6d018b71-b9c9-43c9-880f-c74ea790c4bb.jpeg)

## Execute o BIND
```bash
$ sudo systemctl enable bind9
```

```bash
$ sudo systemctl restart bind9
```
![WhatsApp Image 2022-12-23 at 11 37 38 (3)](https://user-images.githubusercontent.com/80183918/209552316-84377764-3483-486b-a2d6-4e4fa961458f.jpeg)

## Configuração dos clientes
* Configure o dns no nas máquina ns1, ns2 

```bash
            nameservers: 
                addresses:
                - 10.9.13.118
                - 10.9.13.122
                search: [grupo4.turma913.ifalarapiraca.local]
```

```bash
$ sudo nano /etc/netplan/50-cloud-init.yaml 
```
![WhatsApp Image 2022-12-23 at 11 37 37](https://user-images.githubusercontent.com/80183918/209552361-3ea33ae7-5ea5-4a55-8dd3-eae2a4d78dc4.jpeg)

## Testando o servidor DNS
```bash
$ systemd-resolve --status enp0s3
```
![WhatsApp Image 2022-12-23 at 11 37 37 (1)](https://user-images.githubusercontent.com/80183918/209552420-c42c7b8d-ac21-48d1-8eef-8885f44ab918.jpeg)

### Teste o serviço DNS para a máquina ns1.
```bash
$ dig ns1.grupo4.turma913.ifalarapiraca.local
```
![WhatsApp Image 2022-12-23 at 11 37 36](https://user-images.githubusercontent.com/80183918/209552472-a5f90ac5-debb-4404-8a15-26508c47f525.jpeg)

### Teste o serviço DNS reverso para a máquina ns1.
```bash
$ dig -x 10.9.13.122
```
![WhatsApp Image 2022-12-23 at 11 37 36 (1)](https://user-images.githubusercontent.com/80183918/209552539-2ae493dc-bea7-4d80-8590-a9f97aabaa8d.jpeg)

### Teste o serviço DNS reverso para a máquina ns2.
```bash
$ dig -x 10.9.13.118
```
![WhatsApp Image 2022-12-23 at 11 37 36 (2)](https://user-images.githubusercontent.com/80183918/209552545-25d089cc-559f-415c-8f8e-9f84b71c6d20.jpeg)
