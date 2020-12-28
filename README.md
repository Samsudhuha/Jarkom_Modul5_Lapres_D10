# Jarkom_Modul5_Lapres_D10

### 05111840000129 Ryukazu Andara Saviestyan
### 05111840000155 Muhamat Samsu Dhuha
### Kelompok D10

## Soal

```
Setelah kalian mempelajari semua modul yang telah diberikan, Bibah ingin meminta bantuan untuk
terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Bibah.
(A) Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan
Bibah seperti dibawah ini :
```

<br>

<center>
  
![img](/img/topologi.png)

</center>

<br>

```
Keterangan : 
SURABAYA diberikan IP TUNTAP
MALANG merupakan DNS Server diberikan IP DMZ
MOJOKERTO merupakan DHCP Server diberikan IP DMZ
MADIUN dan PROBOLINGGO merupakan WEB Server
Setiap Server diberikan memory sebesar 128M
Client dan Router diberikan memori sebesar 96M
Jumlah host pada subnet SIDOARJO 200 Host
Jumlah host pada subnet GRESIK 210 Host

(B) karena kalian telah mempelajari Subnetting dan Routing, Bibah meminta kalian untuk membuat
topologi tersebut menggunakan teknik CIDR atau VLSM. Setelah melakukan subnetting, 
(C)kalian juga diharuskan melakukan routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
(D) Tugas berikutnya adalah memberikan ip pada subnet SIDOARJO dan GRESIK secara dinamis
menggunakan bantuan DHCP SERVER (Selain subnet tersebut menggunakan ip static). Kemudian
kalian mengingat bahwa kalian harus setting DHCP RELAY pada router yang menghubungkannya,
seperti yang kalian telah pelajari di masa lalu.
(1) Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi
SURABAYA menggunakan iptables, namun Bibah tidak ingin kalian menggunakan
MASQUERADE.
(2) Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML) Kalian pada server
yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.
(3) Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta kalian untuk membatasi DHCP
dan DNS server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari
mana saja menggunakan iptables pada masing masing server, selebihnya akan di DROP.
kemudian kalian diminta untuk membatasi akses ke MALANG yang berasal dari SUBNET
SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:
(4) Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin
sampai Jumat.
(5) Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap
harinya. 
Selain itu paket akan di REJECT.

Karena kita memiliki 2 buah WEB Server, (6) Bibah ingin SURABAYA disetting sehingga setiap
request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada
PROBOLINGGO port 80 dan MADIUN port 80.
(7) Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap
UML yang memiliki aturan drop.
Bibah berterima kasih kepada kalian karena telah mau membantunya. Bibah juga mengingatkan agar
semua aturan iptables harus disimpan pada sistem atau paling tidak kalian menyediakan script sebagai
backup.
```

<br>


## Jawab

A. 
``` 
Membuat topologi dan interface pada setiap uml
```
<center>
  
![img](/img/topologi-sh.png)

![img](/img/surabaya.png)

![img](/img/malang.png)

![img](/img/kediri.png)

![img](/img/madiun.png)

![img](/img/probolinggo.png)

![img](/img/gresik.png)

![img](/img/batu.png)

![img](/img/mojokerto.png)

</center>

<br>

``` 
Maaf gabisa ss mojokerto karena tetiba UML gabisa dibuka
```

B. 
```
Subnetting dan Routing
```
<center>
  
![img](/img/vlsm.png)

![img](/img/ip.png)

![img](/img/pohon.png)

![img](/img/routing.png)

</center>

<br>

C.
``` 
Setelah mengetahui hasil routing, maka implementasikan
```

1. Surabaya

<center>
  
![img](/img/route-surabaya.png)

</center>

2. Batu
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.5
```

3. Kediri
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.168.0.1
```

D.
```
DHCP-SERVER & DHCP-RELAY
```

1. DHCP SERVER
- Konfigurasi DHCP server MOJOKERTO :
  - Install dhcp-server : apt-get install isc-dhcp-server
  - Konfigurasi interfaces : nano /etc/default/isc-dhcp-server dengan INTERFACESv4="eth0"
  - Konfigurasi pada dhcp-server : nano /etc/dhcp/dhcpd.conf 
  - Restart : service isc-dhcp-server restart

2. DHCP RELAY
- Lakukan pada router KEDIRI, BATU, SURABAYA
  - Install dhcp-relay  : apt-get install isc-dhcp-relay
  - Konfigurasi interface dhcp-relay : nano /etc/default/isc-dhcp-relay 
  - Atur agar server mengarah ke MOJOKERTO dengan SERVERS="10.151.79.91" dan INTERFACESv4
  - Lalu Restart : service isc-dhcp-relay restart



## Iptables

1. 
```
Agar topologi yang kalian buat dapat mengakses keluar, 
kalian diminta untuk mengkonfigurasi SURABAYA menggunakan iptables, 
namun Bibah tidak ingin kalian menggunakan MASQUERADE.
```
- Jawab

### SURABAYA
iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.78.46

2. 
```
Kalian diminta untuk mendrop semua akses SSH dari luar Topologi (UML)
Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER) pada SURABAYA demi menjaga keamanan.
```
- Jawab

### SURABAYA
iptables -A FORWARD -d 10.151.79.88/29 -i eth0 -p tcp --dport 22 -j DROP

3.
```
Karena tim kalian maksimal terdiri dari 3 orang, 
Bibah meminta kalian untuk membatasi DHCP dan DNS server hanya boleh 
menerima maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja 
menggunakan iptables pada masing masing server, selebihnya akan di DROP.
```
- Jawab

### DI MALANG, MOJOKERTO
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 -j DROP
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP

4.
```
kemudian kalian diminta untuk membatasi akses ke MALANG yang berasal dari SUBNET SIDOARJO dan SUBNET GRESIK dengan peraturan sebagai berikut:
Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 - 17.00 pada hari Senin sampai Jumat.
```
- Jawab

### DI MALANG
ip sidoarjo
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.2.0/24 -j REJECT

5.
```
Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00 hingga pukul 07.00 setiap harinya.
```
- Jawab

### DI MALANG
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00 -j REJECT

6.
```
SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server 
akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80.
```

- Jawab

### DI SURABAYA
iptables -t nat -A PREROUTING -p tcp -d 10.151.79.90 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.11:80
iptables -t nat -A PREROUTING -p tcp -d 10.151.79.90 --dport 80 -j DNAT --to-destination 192.168.0.10:80

7.
```
Bibah ingin agar semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop.
```

- Jawab

### DI SURABAYA
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A OUTPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
iptables -A LOGGING -j DROP

