# TEK1314-2026-Kel9-KelasB
Kelompok 9 Siber Security TEK61-B

## Skenario Proyek
 
Proyek ini mensimulasikan skenario serangan terhadap **Database Server** dalam lingkungan jaringan tersegmentasi. Arsitektur memisahkan zona luar (attacker) dari zona dalam (target dan monitoring) melalui router yang bertindak sebagai gateway sekaligus firewall, sehingga hanya trafik pada port layanan database (3306/5432) yang diizinkan masuk ke Target-DB-Server.
 
Komponen utama:
- **Attacker Node** (Kali Linux) — mensimulasikan penyerang eksternal
- **Target-DB-Server** (Metasploitable2) — server database yang menjadi sasaran, memiliki service MySQL & PostgreSQL dengan konfigurasi rentan
- **Monitoring Node** (Security Onion) — memantau seluruh trafik pada segmen internal
Skema IP tersedia di [`docs/design/ip_plan.md`](docs/design/ip_plan.md), dan diagram topologi di [`docs/design/topology.png`](docs/design/topology.png).
