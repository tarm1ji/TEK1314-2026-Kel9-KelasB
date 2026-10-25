# Logbook & Dokumentasi PBL TEK 1314 - Keamanan Siber

**Kelompok:** Kelompok 9 - Kelas B
**Proyek IoT:** Smart Soil Monitoring System untuk Tanaman Kopi

## Daftar Anggota & Pembagian Peran (Role)

- **Lead:** Wahyu Pratomo
- **Blue Team (Defender / Monitoring):** Hilman Rabbani Dwinarno dan Zenko Erwin
- **Red Team (Attacker / Assessment):** Ahdi Khalida Fathir

---

## Navigasi Dokumentasi Repository (Struktur Bagian XI)

- **Arsitektur & Skema IP (Minggu 4):** [`docs/design/ip_plan.md`](docs/design/ip_plan.md) | [`docs/design/topology.png`](docs/design/topology.png)
- **Presentasi & Deklarasi Tech Stack (Minggu 9):** [`docs/presentation-minggu9/slides.pdf`](docs/presentation-minggu9/slides.pdf) | [`docs/presentation-minggu9/tech_stack_declaration.md`](docs/presentation-minggu9/tech_stack_declaration.md)
- **Hardening Checklist & Bukti Blue Team:** [`docs/blueteam/hardening_checklist.md`](docs/blueteam/hardening_checklist.md) | [`docs/blueteam/evidence/`](docs/blueteam/evidence/)
- **Attack Plan & Bukti Red Team:** [`docs/redteam/attack_plan.md`](docs/redteam/attack_plan.md) | [`docs/redteam/evidence/`](docs/redteam/evidence/)
- **Log Purple Team Loop:** [`docs/purple-team-log.md`](docs/purple-team-log.md)

---

## Logbook Progres Mingguan (Minggu 1 – 9)

> **Catatan Status:** Status "Selesai" pada Minggu ke-9 merujuk pada penyusunan kerangka skenario, analisis root cause awal, pengujian baseline, dan implementasi kontrol mitigasi Blue Team. Eksekusi penuh pengujian Red Team (termasuk tahap Deteksi oleh Blue Team dan verifikasi alert SIEM) dijadwalkan berlangsung pada Minggu ke-10 s.d. Minggu ke-11, sebagaimana tercatat pada [`docs/purple-team-log.md`](docs/purple-team-log.md).

|  Minggu   | Fokus Kegiatan                                                | Progres & Deliverable Kelompok                                                                                                                                                                                                   | Status  |
| :-------: | :------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-----: |
| **1 – 2** | Pembentukan Tim & Kontrak PBL                                 | Pembagian peran Lead, Red Team, dan Blue Team serta penentuan konteks sistem IoT Smart Soil Monitoring Kopi.                                                                                                                     | Selesai |
|   **3**   | Instalasi Environment Lab                                     | Instalasi dan konfigurasi VM Attacker, Target, serta Security Onion pada Laptop Utama dan Laptop Backup.                                                                                                                         | Selesai |
|   **4**   | Perancangan Topologi & IP Plan                                | Penyusunan topologi jaringan simulasi subnet `192.168.9.0/26` & `192.168.9.64/26` (`ip_plan.md`) dan riset port awal.                                                                                                            | Selesai |
| **5 – 6** | Baseline Traffic & Hardening Awal                             | Konfigurasi awal firewall pada Target Node dan pengujian tangkapan baseline traffic di Security Onion (`192.168.9.90`).                                                                                                          | Selesai |
| **7 – 8** | UTS Praktikum & Evaluasi Tengah Semester                      | Pelaksanaan UTS Praktikum dan evaluasi kesesuaian Target Node dengan sistem IoT asli.                                                                                                                                            | Selesai |
|   **9**   | Deklarasi Tech Stack, Hardening 3 Layer, & Attack Plan Fase 2 | Migrasi Target Node (`192.168.9.70`) ke replika Ubuntu Server (Flask HTTP API + PostgreSQL lokal), penerapan 9 kontrol hardening Kategori A/B/C, penyusunan 3 skenario serangan Red Team, dan presentasi Checkpoint Minggu ke-9. | Selesai |
