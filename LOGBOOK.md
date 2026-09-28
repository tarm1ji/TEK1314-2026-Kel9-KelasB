# **LOG AKTIVITAS MINGGUAN (PBL KEAMANAN SIBER)**

**Kelompok:** Kelompok 9 \- Kelas B

---

### **Minggu 1-3: Fase Setup Lingkungan Lab**

* **Target:** Instalasi VM Kali Linux (Attacker), Metasploitable 2 (Target DB Server), dan Security Onion (Monitoring) serta perancangan topologi jaringan.  
* **Update:**  
  * Merancang topologi dengan block IP `192.168.9.0/24` yang dipecah menjadi dua segmen /26: Zona Luar (`192.168.9.0/26`) untuk Attacker dan Zona Dalam (`192.168.9.64/26`) untuk Target dan Security Onion, dipisahkan oleh router yang berfungsi sebagai gateway sekaligus firewall.  
  * Menetapkan identitas standar: Attacker `ATK-KEL09` (`192.168.9.10`), Target DB Server `SRV-DB-KEL09` (`192.168.9.70`), dan Security Onion `SecOnion` (`192.168.9.90`).  
* **Artefak:**  
  * Docs/phase-1-baseline/assets/Topology.png  
* **Status:** Selesai.

---

### **Minggu 4-6: Konfigurasi Jaringan & Verifikasi Logging (Security Onion)**

* **Target:** Memastikan konektivitas antar segmen dan verifikasi infrastruktur pemantauan NIDS.  
* **Update:**  
  * Menguji transmisi ICMP (Ping) dari Kali (`192.168.9.10`) ke Target (`192.168.9.70`) dan berhasil memverifikasi alert `GPL ICMP_INFO PING *NIX` (sid 2100366\) sebanyak 12 event pada Squert, sensor `seconion-import`, serta terverifikasi silang di Kibana.  
  * **Kendala:** Snort tidak berjalan pada sensor (Kibana 0 hits, folder log harian kosong, hanya proses barnyard2 yang terlihat).  
  * **Solusi:** Parameter `IDS_ENGINE_ENABLED` pada `/etc/nsm/seconion-import/sensor.conf` diubah dari `"no"` menjadi `"yes"`, lalu sensor di-restart dengan `sudo nsm_sensor_ps-restart`.  
  * **Kendala:** Folder `/var/log/snort` belum ada saat Snort diuji manual. **Solusi:** Folder dibuat dan kepemilikannya diatur ke user `sguil`.  
  * **Kendala:** IP jaringan lab pada Kali Linux dan Metasploitable hilang setelah VM di-restart sehingga ping dan SSH gagal. **Solusi:** \[ISI: cara perbaikan permanen yang dipakai\].  
  * **Catatan:** Squert dan Sguil menampilkan waktu dalam UTC, sehingga waktu pengujian dicatat dalam UTC beserta padanan waktu lokalnya.  
* **Artefak:**  
  * Docs/phase-1-baseline/assets/security-onion-icmp-log.png  
* **Status:** Selesai.

---

### **Minggu 7: Hardening Review & Penyusunan Baseline Report**

* **Target:** Penguatan sistem keamanan server target (*Before Attack*) dan pembuatan baseline documentation.  
* **Update:**  
  * **Network Hardening:** Mengaktifkan firewall UFW dan iptables dengan kebijakan *Default Deny Incoming* dan *Allow Outgoing*. SSH (Port 22\) dibatasi dari IP Attacker (`192.168.9.10`) dan subnet lab `192.168.9.0/24`. Port 5432 (PostgreSQL) dan 3306 (MySQL) sengaja tetap aktif sebagai bahan skenario Fase 2\.  
  * **System Hardening:** Menonaktifkan layanan rentan Telnet (Port 23\) dan FTP vsftpd 2.3.4 (Port 21); membuat user non-root `opskel09` dengan hak sudo terbatas pada `/usr/sbin/ufw` dan `/usr/bin/apt-get`; menonaktifkan login root via SSH (`PermitRootLogin no`).  
  * **Update Security Patch:** Menjalankan `sudo apt update && sudo apt upgrade -y` pada 28/09/2026.  
  * Menyusun laporan `Kelompok_9.md` (Baseline Report Fase 1).  
  * **Rencana berikutnya:** Menyimpan snapshot VM setelah baseline selesai, memastikan seluruh service Security Onion berstatus OK (`sudo so-status`), dan memantau aktivitas Fase 2 pada Squert dan Kibana.  
* **Artefak:**  
  * Docs/phase-1-baseline/assets/firewall-config.png  
  * Docs/phase-1-baseline/assets/user-access-config.png  
  * Docs/phase-1-baseline  
* **Status:** Dalam finalisasi laporan.

---

 