# Implementação do DNS Master

## Instalação
* Instalar o bind9 via apt-get, pois é a aplicação de DNS que roda no servidor.
```bash
$ sudo apt-get install bind9 dnsutils bind9-doc 
```

* Verificação o status do serviço:
```bash
$ sudo systemctl status bind9
```

* Caso, não estiver rodando:
```bash
$ sudo systemctl enable bind9
```

## Diretórios do bind
* Os arquivos do bind no diretório /etc/bind.
```bash
$ ls /etc/bind
```

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

### Zona reversa
* Se utiliza quando não se conhece o IP mas, sabe-se o nome do host.Assim, temos que criar a zona reversa a partir do arquivo /etc/bind/db.127
```bash
  $ sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.13.rev
```

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

## Verificação de sintaxe
* Executar o comando named-checkconf. Este scritp checa os arquivos /etc/bind/named.conf.local.
```bash
$ sudo named-checkconf
```

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

## Execute o BIND
```bash
$ sudo systemctl enable bind9
```

```bash
$ sudo systemctl restart bind9
```

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

## Testando o servidor DNS
```bash
$ systemd-resolve --status enp0s3
```

### Teste o serviço DNS para a máquina ns1.
```bash
$ dig ns1.grupo4.turma913.ifalarapiraca.local
```

### Teste o serviço DNS reverso para a máquina ns1.
```bash
$ dig -x 10.9.13.122
```

### Teste o serviço DNS reverso para a máquina ns2.
```bash
$ dig -x 10.9.13.118
```
