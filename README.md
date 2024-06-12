# T02-Rede-de-Computadores

Repositório destinado ao Trabalho 2 de Fundamentos de Redes de Computadores

## Integrantes

|              Foto              | Matrícula |  Github  |
| :----------------------------: | :-------: | :------: |
|      Adne Moreira Moretti      |           |          |
| Cícero Barrozo Fernandes Filho | 190085819 | ciceroff |
|        Gabriel Oliveira        |           |          |
|       Leonardo Vitoriano       |           |          |

Primeiros passos para configuração da rede
Primeiramente foi instalado o Freebsd na máquina do LDS;

### rc.conf

Abrir o rc.conf e foi realizada a configuração inicial da rede, segue como o arquivo ficou, vale lembrar que as interfaces de rede são: em0 para WAN e rl0 para LAN.

```
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

```
service netif restart && service routing restart
```

### pf.conf

Depois foi criado o arquivo pf.conf no caminho `/etc/pf.c
fn`:
O package Filter é um firewall e filtro de pacotes integrado ao FreeBSD, Nesse trabalho o Package Filter é usado para criar a tabela NAT.

```
ext_if="em0"
int_if="rl0"

nat on $exit_if from $int_if:network to any -> ($ext_if)

# Tráfego LAN

pass in on $int_if from $int_if:network to any

# Tráfego WAN

pass out on $ext_if from any to any

```

### Atualizar configurações do package filter

esfazereiniciar o Após fazer alterações de configurações no package filter, precpf.conf com os seguintes comandos:

```
pfctl -f pf.conisamos ro### Instalar o isc-dhcpd44



### Instalar o dhcpd.conf feita a instalação do pacote isc-dhcpd44, que é uma biblioteca de uma versão específica de um servidor dhcp para prover configurações de rede a dispositivos;


pkg install isc-dhcpd44

Instalação do dhcpd.conf


``

### dhcpd.conf

No arquivo `dhcpd.conf`, que fica no caminho `/usr/local/etc/dhcpd.conf foi alterado:

```

subnet 10.0.0.0 netmask 255.255.0.0 (
range 10.0.0.100 10.0.0.200;
option routers 10.0.0.1;
option domain
name-servers 192.168.133.1;
)

host client-machine (
hardware ethernet C8:4B:D6:13:C7:BA;
fixed-addresss 10.0.0.150;
)

```

Essas alterações foram feitas para que a máquina 10.0.0.1 funcionasse como um servidor DHCP para as máquinas que se conectam à rede local, por isso, no arquivo foi setada a máscara da subrede, que é 255.255.0.0/16, o range que seria aplicado para as máquinas que se conectassem a rede.

Além disso, também foi setado um host, que seria a máquina de teste de um dos integrantes do grupo, para a máquina com o MAC address descrito em hardware ethernet, para essa máquina específica foi setado o IP 10.0.0.150;

```

# dhcpd.leases

# Testes de conexão na rede

```

```

```

```
