### Instituto Federal de Alagoas - Campus Arapiraca<br>Disciplina: Infraestrutura e Serviços de Rede<br>Turma: 913 - Informática<br>Grupo 4: Giovanna Stephany Souza da Silva, Júlia Vitória Marques de Góis, José Cristiano Costa Sobrinho, Ludmila Barbosa da Silva

<p align="center">Projeto final - Ambiente de rede contendo 6 máquinas virtuais com o S.O Ubuntu Server<p>

* Tabela 1: Configurações de hardware utilizada nas máquinas virtuais
```
-------------------------------------------------------------------------------------------------------
|          QTDE. DE MEMÓRIA        |         Nº DE PROCESSADORES         |       ESPAÇO EM DISCO      |   
-------------------------------------------------------------------------------------------------------
|              512 MB              |                  1                  |          10,00 GB          |   
-------------------------------------------------------------------------------------------------------
```

* Tabela 2: Definições dos IPs das MVs com a máscara de rede /28 (255.255.255.240).
```
-----------------------------------------------------------------------------------------------
|          SUBREDES         |         REDE         |       BROADCAST      |      GATEWAY      |
-----------------------------------------------------------------------------------------------
|   192.168.13.[48-63]/28   |   192.168.13.48/28   |   192.168.13.63/28   |   192.168.13.49   |
-----------------------------------------------------------------------------------------------
```
## Considerações finais
Por meio da execução e da elaboração desse projeto, 
pôde-se assimilar e compreender a importância dos serviços 
implementados nesse último projeto da disciplina Infraestrutura e 
Serviços de Redes, bem como a indispensabilidade desses 
serviços em uma rede, visto que otimiza o compartilhamento 
de arquivos e configura uma organização mais aprimorada aos clientes.

## Partições:
* [Parte 1 - Implementação do SAMBA](https://github.com/Ludmila2004/ProjetoFinal-Redes/blob/main/Implementa%C3%A7%C3%A3o%20do%20SAMBA.md)
* [Parte 2 - Implementação do Gateway Server](https://github.com/Ludmila2004/ProjetoFinal-Redes/blob/main/Implementa%C3%A7%C3%A3o%20do%20gateway%20server.md)
* [Parte 3 - Implementação do DNS Master](https://github.com/Ludmila2004/ProjetoFinal-Redes/blob/main/Implementa%C3%A7%C3%A3o%20do%20DNS%20Master.md)
* [Parte 4 - Implementação do DNS Slaver](https://github.com/Ludmila2004/ProjetoFinal-Redes/blob/main/Implementa%C3%A7%C3%A3o%20do%20DNS%20Slave.md)
