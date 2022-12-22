# Implemnetação do DNS Master

## Instalação
Instalar o bind9 via apt-get, pois é a aplicação de DNS que roda no servidor.
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



