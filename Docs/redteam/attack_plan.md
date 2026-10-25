# Red Team Attack Plan (Fase 2)

**Kelompok:** Kelompok 9 - Kelas B
**Attacker Node:** CyberOps Workstation (`192.168.9.10`)
**Target Node Replika:** Ubuntu Server - Smart Soil Monitoring Backend (`192.168.9.70`)
**Monitoring Node:** Security Onion (`192.168.9.90`)

---

## Daftar Skenario Serangan Fase 2

Rencana pengujian ini mencakup **3 skenario dari 3 kategori berbeda** yang menyasar komponen spesifik pada replika backend **Smart Soil Monitoring Tanaman Kopi**:

|  No   | Kategori (Bagian IX)         | Nama Skenario & Teknik                                                                                                                                 | Tools Eksplisit                    | Target pada Node Replika                                         | Relevansi dengan Tech Stack Proyek IoT                                                                                                         | Hasil yang Diharapkan (Baseline vs Hardened)                                                                                                                               |
| :---: | :--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------- | :--------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | **Reconnaissance**           | **Service & Database Port Enumeration:** Pemindaian port terbuka, _banner grabbing_, dan _OS fingerprinting_.                                          | `nmap`, `Wireshark`                | IP Target Node (`Port 1-10000`, fokus pada `22`, `5000`, `5432`) | Menguji apakah database PostgreSQL (replika Supabase) ikut terekspos ke jaringan publik/lokal selain port HTTP API.                            | **Baseline:** Port `22`, `5000`, dan `5432` terlihat terbuka.<br>**Hardened:** Port `5432` tertutup/filtered oleh `ufw` & terdeteksi di Security Onion.                    |
| **2** | **Exploitation / Akses**     | **SSH Credential Brute-Force:** Percobaan login berulang menggunakan daftar password umum terhadap akun server.                                        | `Hydra`, `Nmap NSE` (`ssh-brute`)  | Layanan OpenSSH (`Port 22/tcp`) pada Target Node                 | Server Cloud backend yang dikelola dari jarak jauh rentan diambil alih apabila masih mengizinkan autentikasi password lemah.                   | **Baseline:** Hydra dapat menguji ratusan kombinasi password.<br>**Hardened:** Ditolak oleh _SSH Key-Only Auth_ & IP Attacker diblokir otomatis oleh `fail2ban`.           |
| **3** | **Web / API / Protokol IoT** | **IoT Telemetry Spoofing & Malformed Input Injection:** Pengiriman data kelembaban/pH tanah palsu tanpa otorisasi & injeksi payload pada endpoint API. | `curl`, `OWASP ZAP` / `Burp Suite` | Endpoint HTTP REST API `POST /api/telemetry` (`Port 5000/tcp`)   | Karena ESP32 mengirim telemetri via HTTP ke server cloud, ketiadaan validasi token & input memungkinkan _data poisoning_ pada data lahan kopi. | **Baseline:** Data palsu/ekstrem masuk ke PostgreSQL & tampil di Dashboard.<br>**Hardened:** Ditolak dengan HTTP `401`/`422` & tercatat di `soil_security.log` serta SIEM. |

---

## Detail Langkah Eksekusi per Skenario (Referensi Pengujian Minggu 10)

### Skenario 1: Service & Database Port Enumeration (Reconnaissance)

- **Perintah Eksekusi (Attacker Node):**
  ```bash
  nmap -sV -sC -O -p 22,80,5000,5432 192.168.9.70
  ```
- **Rencana Deteksi Blue Team:** Memantau alert `ET SCAN Potential SSH Scan` dan `ET SCAN Nmap Scripting Engine` pada Security Onion.

### Skenario 2: SSH Credential Brute-Force (Exploitation / Akses)

- **Perintah Eksekusi (Attacker Node):**
  ```bash
  hydra -l kel9 -P /usr/share/wordlists/metasploit/unix_passwords.txt ssh://192.168.9.70 -t 4 -V
  ```
- **Rencana Deteksi Blue Team:** Memantau `/var/log/auth.log` untuk melihat lonjakan `Failed password`, memverifikasi pemblokiran IP pada `sudo fail2ban-client status sshd`, serta mengamati alert percobaan login SSH di Security Onion.

### Skenario 3: IoT Telemetry Spoofing & Input Injection (Web / API / Protokol IoT)

- **Perintah Eksekusi Spoofing Tanpa Token & Nilai Ekstrem (Attacker Node):**
  ```bash
  curl -i -X POST http://192.168.9.70:5000/api/telemetry \
    -H "Content-Type: application/json" \
    -d '{"device_id":"ESP32-PALSU-99","soil_moisture":999.9,"soil_ph":-50.0,"temperature":500.0}'
  ```
- **Perintah Eksekusi Uji Injeksi Payload pada `device_id`:**
  ```bash
  curl -i -X POST http://192.168.9.70:5000/api/telemetry \
    -H "Content-Type: application/json" \
    -H "X-API-Key: TOKEN_SENSOR_KOPI_998877" \
    -d '{"device_id":"ESP32-01'\'' OR 1=1;--","soil_moisture":60.0,"soil_ph":6.5,"temperature":25.0}'
  ```
- **Rencana Deteksi Blue Team:** Memeriksa log aplikasi `soil_security.log` untuk entri `UNAUTHORIZED_ACCESS` dan `ANOMALY_INPUT`, serta memantau trafik HTTP POST berstatus `401`/`422` pada Security Onion.
