# Laporan Praktikum JARKOM Kelompok B07 #

| Nama                      | NRP           |
| ------------------------- | ------------- |
| Danial Farros Maulana     | 5025201004    |
| Rendi Dwi Francisko       | 5025201056    |
| Ahmad Ibnu Malik Rahman   | 5025201232    |


## Pembahasan soal Modul ##

### Nomer 1 ###
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server
**Jawab :**
![GambarTopologi](image/topologi.png)

#### (DNS Server) Wise ####
network configuration

```
auto eth0
iface eth0 inet static
	address 192.176.2.2
	netmask 255.255.255.0
	gateway 192.176.2.1
```
Bashrc

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```

#### (DHCP Server) Westalis ####
network configuration

```
auto eth0
iface eth0 inet static
	address 192.176.2.4
	netmask 255.255.255.0
	gateway 192.176.2.1
```
Bashrc

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
```


#### (Proxy Server) Berlint ####
network configuration

```
auto eth0
iface eth0 inet static
	address 192.176.2.3
	netmask 255.255.255.0
	gateway 192.176.2.1
```
Bashrc

```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install libapache2-mod-php7.0 -y
apt-get install squid -y
```

### Nomer 2 ###
dan Ostania sebagai DHCP Relay 
**Jawab :**
#### (DHCP relay) Ostania ####
network configuration

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.176.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.176.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.176.3.1
	netmask 255.255.255.0
```
Bashrc

```
apt-get update
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.176.0.0/16
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay restart
```
isi SERVERS="192.176.2.4" dan INTERFACES="eth1 eth3 eth2"

### Nomer 3-4 ###
Ada beberapa kriteria yang ingin dibuat oleh Loid dan Franky, yaitu:
Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155 (3)
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85 (4)

**Jawab :**

#### (Clietn) SSS,Garden,KemonoPark,NewstonCastle ####
network configuration

```
auto eth0
iface eth0 inet dhcp
```
#### (Clietn) Eden ####
network configuration

```
auto eth0
iface eth0 inet dhcp
hwaddress ether 12:41:bf:96:4f:d3
```
Westalis
kemudian tambahkan `INTERFACES=\"eth0\"` padan `/etc/default/isc-dhcp-server`
lalu tambahkan juga
```
subnet 192.176.1.0 netmask 255.255.255.0 {
    range  192.176.1.50 192.176.1.88;
    range  192.176.1.120 192.176.1.155;
    option routers 192.176.1.1;
    option broadcast-address 192.176.1.255;
    option domain-name-servers 192.176.2.2;
}
subnet 192.176.3.0 netmask 255.255.255.0 {
    range  192.176.3.10 192.176.3.30;
    range  192.176.3.60 192.176.3.85;
    option routers 192.176.3.1;
    option broadcast-address 192.176.3.255;
    option domain-name-servers 192.176.2.2;
}
``` 
pada `/etc/dhcp/dhcpd.conf` dan jangan lupa restart `service isc-dhcp-server restart`
lalu cek dengan command `ip a` pada client

#### switch1 ####
![GambarTopologi](image/3.1.png)
#### switch2 ####
![GambarTopologi](image/3.2.png)

