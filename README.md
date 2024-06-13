# T02-Rede-de-Computadores

Repositório destinado ao Trabalho 2 de Fundamentos de Redes de Computadores

## Integrantes

| Nome                           | Matrícula |
| ------------------------------ | --------- |
| Adne Moreira Moretti           | 200013181 |
| Cícero Barrozo Fernandes Filho | 190085819 |
| Gabriel Oliveira               | 190045817 |
| Leonardo Vitoriano             | 201000379 |

## Informações iniciais

- Sistema operacional utilizado: Freebsd
- Aplicações utilizadas: isc-dhcp-server, vim, tmux, tcpdump;

## Vamos lá!

Primeiros passos para configuração da rede
Primeiramente foi instalado o Freebsd na máquina do LDS;

### Configuração do rc.conf

Abrir o rc.conf e foi realizada a configuração inicial da rede, segue como o arquivo ficou, vale lembrar que as interfaces de rede são: em0 para WAN e rl0 para LAN.

```bash
clear_tmp_enable="YES"
hostname="freebsd"
keymap="br.kdb"
ifconfig_em0="DHCP" # IP via DHCP para em0 (WAN)
dhcpd_enable="YES"
dhcpd_ifaces="rl0"  # Para o rl0 servir como o servidor do DHCP
sshd_enable="YES"
ntpd_enable="YES"
ntpd_sync_on_start="YES"
moused_nondefault_enable="NO"
dumpdev="NO"
zfs_enable="YES"
default_router="192.168.133.1" # Gateway
ifconfig_rl0="inet 10.0.0.1 netmask 255.255.0.0" # IP 10.0.0.1 para LAN
gateway_enable="YES"
pf_enable="YES"
pflog_enable="YES"
```

Após alterar esse arquivo, é necessário reiniciar as interfaces de redes, para isso, rode o seguinte comando:

```bash
service netif restart && service routing restart
```

### Configuração do pf.conf

Depois foi criado o arquivo pf.conf no caminho `/etc/pf.c
fn`:
O package Filter é um firewall e filtro de pacotes integrado ao FreeBSD, Nesse trabalho o Package Filter é usado para criar a tabela NAT.

```bash
ext_if="em0"
int_if="rl0"

nat on $exit_if from $int_if:network to any -> ($ext_if)

# Tráfego LAN

pass in on $int_if from $int_if:network to any

# Tráfego WAN

pass out on $ext_if from any to any

```

### Atualizar configurações do package filter

Após fazer alterações de configurações no package filter, reinicie o pf.conf com os seguintes comandos:

```bash
pfctl -f pf.conf
pfctl -e

```

### Instalação do servidor dhcp

Instalar o isc-dhcpd44, que é uma biblioteca de uma versão específica de um servidor dhcp para prover configurações de rede a dispositivos;

```bash
pkg install isc-dhcpd44
```

### Configuração dhcpd.conf

Após a instalação, rode o comando pra iniciar o servidor:

```bash
service isc-dhcpd start
```

No arquivo `dhcpd.conf`, que fica no caminho `/usr/local/etc/dhcpd.conf foi alterado:

```bash
authoritative;
ddns-update-style none;

subnet 10.0.0.0 netmask 255.255.0.0 {
   range 10.0.0.100 10.0.0.200;
   option routers 10.0.0.1;
   option domain-name-servers 192.168.133.1;
}

host client-machine {
   hardware ethernet C8:4B:D6:13:C7:BA;
   fixed-addresss 10.0.0.150;
}

```

Essas alterações foram feitas para que a máquina 10.0.0.1 funcionasse como um servidor DHCP para as máquinas que se conectam à rede local, por isso, no arquivo foi setada a máscara da subrede, que é 255.255.0.0/16, o range que seria aplicado para as máquinas que se conectassem a rede.

Além disso, também foi configurado um host, que seria a máquina de teste de um dos integrantes do grupo, para a máquina com o MAC address descrito em hardware ethernet, para essa máquina específica foi configuradp o IP 10.0.0.150;

### dhcpd.leases

O histórico de leases do servidor dhcp são salvos em um arquivo dentro do var/db/dhcpd, o arquivo dhcpd.leases

```
root@freedsd:/var/d/dhcpd # cat dhcpd.leases
```

```bash
# The format of this file is documented in the dhopd. leases(5) manual page. -
dhopd. leases. 1717766712 dhcpd.leases™
# This lease file was written by isc-dhop-4.4.3-P1
# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

lease 10.0.0.100 {
   starts 1 2024/06/10 11:50:10;
   ends 1 2024/05/10 12:00:10;
   tstp 1 2024/06/10 12:00:10;
   cltt 1 2024/06/10 11:50:18；
   binding state free;
   nardware eternet ocibelalicziazigas
   uid "\377VPM\230\0001002\000\000\253\021 Cm\235; \374\253\375\015":
}

lease 10.0.0.101 {
   starts 1 2024/06/10 12:36:49;
   ends 1 2024/05/10 12:40:25;
   tstp 1 2024/06/10 12:40:25;
   citt. 1 2024/06/10 12:36:49;
   binding state free;
   hardware ethernet c8:4:06:13:07:ba;
   uid "\000\001\000\001-\365\274U\000\010TIpx" ;
}

```

### Testes de conexão na rede

Para testes de conexão na rede local configurada, os seguintes comandos foram executados:

1. Da máquina de teste para a máquina do LDS:

```bash
ping 10.0.0.1

```

2. Da máquina do LDS para a máquina local:

```bash
ping 10.0.0.150

```

Que foi o IP configurado para a máquina de teste pelo DHCP;

3. Da máquina de teste para o roteador:

```bash
ping 192.168.133.1
```

4. Utilização do tcpdump para testar e analisar os logs do UDP com DHCP:

```bash
pkg install tcpdump
```

E para verificar os logs da captura de pacotes via UDP:

```bash
tcpdump -ni rl0 udp
```

Por meio disso, foi possível visualizar o host da máquina de teste ao conectar o cabo de que a máquina rede, onde o servidor dhcp identifica a entrada da máquina e coloca o ip 10.0.0.150 para a máquina. É um teste interessante, pois atualiza o log no momento em que a máquina entra e sai da rede local.

![tcpdump2](/fotos/tcpdump2.jpeg)

Além disso, é possível rodar alguns comandos que trarão informações sobre a rede:

- O ifconfig trará as configurações das interfaces de rede configuradas.

```bash
ifconfig
```

- O netstat trará as informações do monitoramento de conexões à rede. No caso, a flag "-a" mostra as conexões ativas.

```bash
netstat -a
```
