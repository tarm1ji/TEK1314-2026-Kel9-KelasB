**Kelompok 9**

**IP Plan  \-  Blue Team (Defender / Network)** 

Topologi dipecah menjadi dua segmen /26 yang dipisah oleh router sebagai gateway sekaligus firewall antara zona luar (attacker) dan zona dalam (target \+ monitoring). 

Block IP :  192.168.9.0/24  
Subnet : 255.255.255.192

| Hostname | IP Address | Network | OS Direncanakan |
| :---: | :---: | :---: | :---: |
| Attacker \- Kali Linux | 192.168.9.10 | 192.168.9.0/26 |  Kali Linux |
| Target \- DB Server | 192.168.9.70 | 192.168.9.64/26 | Metasploitable 2 |
| Monitoring \- Security Onion | 192.168.9.90 | 192.168.9.64/26 | Security Onion |

**Port yang berpotensi Dibuka / Dieksploitasi \- Red Team (Attacker)** 

| Port | Service | Potensi Celah |
| :---: | :---: | ----- |
| 5432 | PostgreSQL | NjutMetasploitable2 default: MySQL 5.0 tanpa autentikasi root |
| 22 | SSH | Jalur awal (initial foothold) sebelum pivot ke database |
| 3306 | MySQL | Terbuka jika database menjadi backend web app — rentan jika password default/lemah |

