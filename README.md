# T02-Rede-de-Computadores
Repositório destinado ao Trabalho 2 de Fundamentos de Redes de Computadores

Primeiros passos para configuração da rede
Primeiramente foi instalado o Freebsd na máquina do LDS, abrir o rc.conf e foi setado o IP da máquina na rede com 

```
default_router="192.168.133.1"
if_config_em0="inet 192.168.133.6 netmask 255.255.255.0"
if_config_em1="inet 10.0.0.1 netmask 255.255.0.0"
gateway_enable="YES"

pf_enable="YES"
pflog_enable="YES"
```


Depois foi criado o arquivo pf.conf
Package Filter para verificar se os pacotes estão saindo da LAN e chegando na WAN. 

```
ext_if="em0"
int_if="em1"

internat_net="10.0.0.0/16"
external_net="192.168.133.2"

nat obn $ 
```


1. Configurar o rc.conf na pasta /etc
   Endereço do primeiro 192.168.133
   Configurar o número da máscara de rede;
   Default Router -> .1 IP
2. Instalar biblioteca do dhcp
     Rodar a lib e depois tentar o ping;
   
