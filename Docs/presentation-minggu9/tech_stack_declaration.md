# Deklarasi Tech Stack & Replikasi Target Node

**Mata Kuliah:** TEK 1314 - Keamanan Siber (D4 Teknologi Rekayasa Komputer SV IPB)
**Kelompok:** Kelompok 9 - Kelas B
**Nama Proyek IoT:** Smart Soil Monitoring System untuk Tanaman Kopi

---

## 1. Gambaran Singkat Alur Sistem IoT Asli

Sistem memantau kualitas tanah perkebunan kopi secara _real-time_ dengan alur komunikasi berikut:

1. **Perangkat Sensor (Edge):** Mikrokontroler **ESP32** membaca data sensor tanah (kelembaban tanah, pH tanah, dan suhu) lalu mengirimkan payload JSON melalui protokol **HTTP REST API** ke Server Cloud.
2. **Backend & Penyimpanan Cloud:** Server Cloud memproses request dan meneruskan penyimpanan data telemetri ke **Supabase** (berbasis PostgreSQL).
3. **Antarmuka Pengguna:** Data telemetri dari database ditampilkan pada **Aplikasi Monitoring** untuk memantau kondisi lahan kopi.

---

## 2. Tabel Pemetaan Tech Stack Asli ke Target Node Replika (VM Lab)

Sesuai prinsip keamanan simulasi PBL TEK 1314, seluruh pengujian keamanan (scanning, eksploitasi, dan hardening) **hanya dilakukan pada Target Node Replika di laboratorium virtual** menggunakan data sintetis/dummy dan tidak menyentuh infrastruktur cloud produksi maupun akun Supabase asli.

| Komponen                        | Teknologi pada Proyek IoT Asli                       | Direplikasi sebagai (Target Node VM Lab)                                | Keterangan Replikasi & Data                                                                               |
| :------------------------------ | :--------------------------------------------------- | :---------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------- |
| **Sistem Operasi Backend**      | Linux Cloud Server (Ubuntu VPS)                      | **Ubuntu Server LTS VM** (`192.168.9.70`)                               | Menggantikan penggunaan VM generik agar identik dengan lingkungan server cloud asli.                      |
| **Database / Penyimpanan Data** | **Supabase** (Managed Cloud PostgreSQL)              | **PostgreSQL Lokal** (`coffee_soil_db` pada Port `5432`)                | Menggunakan _engine_ database yang sama dengan Supabase (PostgreSQL) berisi data telemetri tanah _dummy_. |
| **Protokol Komunikasi Sensor**  | **HTTP REST API** (ESP32 $\rightarrow$ Server Cloud) | **Python Flask HTTP REST API** (`POST /api/telemetry` pada Port `5000`) | Pengiriman data dari ESP32 disimulasikan menggunakan request HTTP dari script Python/`curl`.              |
| **Dashboard / Monitoring**      | Aplikasi Monitoring Tanaman Kopi                     | **Web Dashboard Lokal** (`GET /` pada Port `5000`)                      | Menampilkan tabel pemantauan kelembaban, pH, dan suhu tanah kopi dari PostgreSQL lokal.                   |

---

## 3. Berkas Aplikasi Flask (Baseline vs Hardened)

Replikasi backend menggunakan dua versi berkas aplikasi Flask untuk mendemonstrasikan perbandingan sebelum dan sesudah mitigasi Blue Team:

| Berkas             | Deskripsi                                                                                                                                   |
| :------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------- |
| `app_baseline.py`  | Versi awal (tanpa verifikasi `X-API-Key`, tanpa validasi rentang nilai sensor, tanpa pencatatan log keamanan) — digunakan untuk pengujian _baseline_ pada Skenario 3. |
| `app.py`           | Versi _hardened_ setelah mitigasi Kontrol C1–C3 (verifikasi `X-API-Key` dari `.env`, validasi rentang kelembaban/pH/suhu, user DB _least-privilege_ `soil_app_user`, dan pencatatan ke `soil_security.log`). |
