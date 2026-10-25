# Kelompok 9 - Kelas B

## IP Plan - Blue Team (Defender / Network)

Topologi jaringan simulasi dipecah menjadi dua segmen subnet `/26` yang dipisahkan secara logis oleh gateway antara zona luar (*Attacker*) dan zona dalam (*Target Replika Backend + Monitoring SOC*). Pada implementasi laboratorium virtual (VirtualBox Internal Network `siber`), penyaringan trafik diterapkan langsung pada sisi *Target Node* menggunakan *host-based firewall* (`ufw`) dan pemantauan paket dilakukan secara pasif (*promiscuous mode*) oleh *Monitoring Node*.

* **Block IP:** `192.168.9.0/24`
* **Subnet Mask:** `255.255.255.192` (`/26`)

| Hostname / Peran | IP Address | Network | Sistem Operasi (Implementasi) | Keterangan Peran |
| :--- | :--- | :--- | :--- | :--- |
| **Attacker - Kali Linux** | `192.168.9.10` | `192.168.9.0/26` | Kali Linux | Node pengujian penetrasi Red Team & simulator pengirim trafik sensor. |
| **Target - Ubuntu Server** | `192.168.9.70` | `192.168.9.64/26` | Ubuntu Server LTS | Replika Backend Smart Soil Monitoring Kopi (Flask HTTP API, Dashboard, & PostgreSQL lokal; dimigrasikan dari rencana awal Metasploitable 2). |
| **Monitoring - Security Onion** | `192.168.9.90` | `192.168.9.64/26` | Security Onion (CyberOps) | Node SIEM/IDS Blue Team (`eth1` manajemen `192.168.9.90`, `eth0` *sniffing* pasif tanpa IP). |

---

## Port Layanan & Potensi Eksploitasi Awal (Baseline) - Red Team (Attacker)

Daftar port layanan pada Target Node (`192.168.9.70`) yang menjadi fokus pengujian keamanan sebelum dan sesudah proses *hardening*:

| Port | Service | Peran pada Replika IoT | Potensi Celah pada Kondisi Baseline (Sebelum Hardening) | Status Target Setelah Hardening |
| :---: | :--- | :--- | :--- | :--- |
| **22/tcp** | OpenSSH | Administrasi jarak jauh server cloud | Mengizinkan login `root` serta autentikasi password tanpa *rate-limiting* sehingga rentan terhadap serangan *SSH brute-force* (`Hydra`) sebagai jalur awal (*initial foothold*). | **Open (Terbatas):** Hanya menerima *SSH Key-Based Auth*, `PermitRootLogin no`, & dilindungi `fail2ban`. |
| **5000/tcp** | Python Flask (HTTP) | Endpoint REST API (`POST /api/telemetry`) & Web Dashboard (`GET /`) | Menerima payload JSON telemetri tanah tanpa otorisasi token perangkat (`X-API-Key`) dan tanpa validasi batas nilai fisis sensor sehingga rentan terhadap *telemetry spoofing* / *data poisoning*. | **Open (Tervalidasi):** Wajib header `X-API-Key`, validasi rentang sensor tanah, *parameterized query*, & tercatat di `soil_security.log`. |
| **5432/tcp** | PostgreSQL | Database lokal (Replika Supabase `coffee_soil_db`) | Mendengarkan koneksi luar (`0.0.0.0`), memakai akun superuser bawaan (`postgres/postgres`), dan belum difilter firewall sehingga dapat diakses langsung tanpa melewati API. | **Closed / Filtered:** Diikat hanya ke `127.0.0.1` (`localhost`), diblokir `ufw` dari luar, & aplikasi memakai akun *least-privilege* (`soil_app_user`). |