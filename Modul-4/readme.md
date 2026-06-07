# Laporan Akhir Modul 4: Firewall & NAT

## 1. Topologi Jaringan Enterprise
Infrastruktur simulasi dikonfigurasi pada *server* PNET Lab dengan arsitektur *multi-vendor* yang memisahkan area menjadi tiga zona utama: *External WAN*, *Internal LAN*, dan *Demilitarized Zone* (DMZ).

<img src="../Dokumentasi_LA/topologi_tumod.jpeg" width="500" />

*Gambar 1.1: Skema Topologi Jaringan Enterprise Multi-Vendor*

---

## 2. Segmentasi & Tabel IP Address

| Perangkat | Antarmuka (Interface) | Alamat IP | Gateway | Deskripsi |
| :--- | :--- | :--- | :--- | :--- |
| **MikroTik ISP** | `ether1`<br>`ether2`<br>`ether3` | DHCP Client<br>10.10.10.1/30<br>172.16.100.1/24 | DHCP<br>-<br>- | Uplink ke Jaringan Lab<br>Link menuju FortiGate port1<br>Gateway Segmen Client-WAN |
| **FortiGate** | `port1`<br>`port2`<br>`port3` | 10.10.10.2/30<br>10.20.20.1/30<br>192.168.20.1/24 | 10.10.10.1<br>-<br>- | Antarmuka luar (WAN)<br>Jalur INSIDE ke Cisco Router<br>Gateway lokal zona DMZ |
| **Cisco Router** | `Gi0/0`<br>`Gi0/2` | 10.20.20.2/30<br>192.168.10.1/24 | 10.20.20.1<br>- | Uplink menuju FortiGate port2<br>Gateway lokal zona LAN |
| **Client LAN (Tinycore)** | `eth0` | 192.168.10.10/24 | 192.168.10.1 | Host internal privat (Linux6) |
| **Client WAN (Tinycore)** | `eth0` | 172.16.100.10/24 | 172.16.100.1 | Host eksternal luar (Linux5) |
| **Ubuntu Server DMZ** | `eth0` | 192.168.20.10/24 | 192.168.20.1 | Web Server Nginx internal DMZ |

---

## 3. Konfigurasi Tiap Perangkat

### A. MikroTik ISP
Mengonfigurasi pengalamatan IP di setiap *interface*, mengaktifkan DHCP Client pada `ether1` untuk akses internet utama lab, serta menetapkan aturan NAT *masquerade* pada jalur keluar.

<img src="../Dokumentasi_LA/tumod9.jpeg" width="500" />

*Gambar 3.1: Alokasi IP Address dan Informasi Routing MikroTik ISP*

### B. Firewall FortiGate
1. **Pengaturan Interface & Akses Manajemen:** Menetapkan IP statis pada `port1`, `port2`, dan `port3` beserta otorisasi protokol akses manajemen (`allowaccess ping https ssh http`).
2. **Routing Table:** Menambahkan rute *default* `0.0.0.0/0` via `10.10.10.1` dan *static route* `192.168.10.0/24` via `10.20.20.2`.

<img src="../Dokumentasi_LA/tumod4.jpeg" width="500" />

*Gambar 3.2: Routing Table Terdaftar pada Sistem FortiGate*

3. **Firewall Policy & VIP (Virtual IP):**
   - Aturan `LAN_to_WAN` (NAT Aktif) dan `LAN_to_DMZ` (NAT Nonaktif).
   - Aturan `WAN_to_DMZ_HTTP` dengan Virtual IP `DMZ_HTTP` untuk meneruskan lalu lintas port 80 publik ke internal server.

<img src="../Dokumentasi_LA/tumod5.jpeg" width="500" />

*Gambar 3.3: Konfigurasi Security Policy Inter-Zone Sisi Dalam*

<img src="../Dokumentasi_LA/tumod6.jpeg" width="500" />

*Gambar 3.4: Kebijakan Akses Zona WAN menuju Area DMZ*

<img src="../Dokumentasi_LA/tumod7.jpeg" width="500" />

*Gambar 3.5: Struktur Aturan Port Forwarding Virtual IP (VIP)*

### C. Cisco Router
Melakukan inisialisasi alamat IP pada port `Gi0/0` dan `Gi0/2`, mengaktifkan fisik antarmuka (`no shutdown`), serta menetapkan rute *default* ke arah INSIDE FortiGate.

<img src="../Dokumentasi_LA/tumod10.jpeg" width="500" />

*Gambar 3.6: Status Interface Logis dan IP Address Cisco vIOS*

### D. End-Device & Server Setup
1. **Client LAN (Linux6):** Penyelarasan profil jaringan IP `192.168.10.10` dan *gateway* `192.168.10.1`.
2. **Client WAN (Linux5):** Penyelarasan profil jaringan IP `172.16.100.10` and *gateway* `172.16.100.1`.

<img src="../Dokumentasi_LA/tumod11.jpeg" width="500" />

*Gambar 3.7: Parameter Network Profile pada Host Client LAN*

<img src="../Dokumentasi_LA/tumod12.jpeg" width="500" />

*Gambar 3.8: Parameter Network Profile pada Host Client WAN*

3. **Ubuntu Server DMZ:** Penyetelan IP statis, instalasi paket Nginx, serta kustomisasi file `index.html` sesuai format nama kelompok praktikan.

<img src="../Dokumentasi_LA/tumod13.jpeg" width="500" />

*Gambar 3.9: Status Berjalan Nginx dan Modifikasi Berkas HTML Server DMZ*

---

## 4. Hasil Pengujian Konektivitas

### A. Pengujian Multi-Directional dari Firewall FortiGate
Eksekusi pengujian interkoneksi langsung menggunakan perintah `execute ping` dari kontrol pusat FortiGate ke seluruh arah *gateway node* pendukung (MikroTik, Cisco, Ubuntu DMZ, dan DNS Internet).

<img src="../Dokumentasi_LA/tumod8.jpeg" width="500" />

*Gambar 4.1: Log Output Validasi Koneksi Ping dari Sisi FortiGate*

### B. Pengujian Konektivitas Internal dari Cisco Router
Verifikasi koneksi balik dari area distribusi lokal Cisco Router menuju *gateway interface* FortiGate, IP internal Ubuntu DMZ, serta jaringan internet luar.

<img src="../Dokumentasi_LA/tumod1.jpeg" width="500" />

*Gambar 4.2: Log Output Validasi Koneksi Ping melalui Cisco Router*

### C. Pembuktian Akses Lintas Zona & Aturan Filtrasi
1. **Client LAN ke Gateway Cisco, FortiGate, & DMZ:** Berhasil terkoneksi secara transparan (Reply).
2. **Client LAN Akses HTTP DMZ:** Membuka browser ke `http://192.168.20.10` memuat halaman kustom kelompok praktikan dengan sukses.
3. **Client WAN Ping ke ISP & FortiGate:** Koneksi antarmuka luar berhasil terhubung (Reply).
4. **Client WAN Akses HTTP VIP (`http://10.10.10.2`):** Berhasil memuat halaman Nginx kustom milik Ubuntu DMZ, membuktikan translasi *Destination NAT / Port Forwarding* pada FortiGate bekerja sempurna.
5. **Isolasi Keamanan (Restriksi Paket):** Client WAN mencoba melakukan ping ke IP privat Client LAN maupun IP asli internal DMZ menghasilkan status *Request Time Out* (RTO). Kebijakan ini membuktikan pertahanan perimeter jaringan internal bekerja optimal untuk menahan *scanning* dari luar.

---

## 5. Analisis Hasil dan Kesimpulan

### Analisis Hasil
Tugas modul ini berhasil mengimplementasikan skenario jaringan enterprise berbasis arsitektur *multi-vendor* dengan mengoptimalkan peran FortiGate sebagai pusat kendali keamanan jaringan (*central stateful firewall*) untuk menegakkan hak akses lintas zona secara ketat menggunakan *Security Policy* dan *Address Object*. Penerapan *Source NAT* (*masquerade*) pada MikroTik dan FortiGate memberikan efisiensi translasi agar alamat IP privat lokal tetap mampu menginisiasi koneksi keluar menuju internet publik, sedangkan implementasi *Destination NAT* melalui objek *Virtual IP* (VIP) pada port 80 membuktikan bahwa layanan *Web Server Nginx* di dalam zona DMZ dapat dipublikasikan secara aman ke dunia luar tanpa perlu mengekspos skema topologi privat maupun *resource* penting di area LAN. Aturan filtrasi paket terbukti bekerja konsisten dan responsif, di mana akses publik dari luar (WAN) dibatasi secara ketat hanya untuk protokol HTTP, sementara aktivitas mencurigakan lintas zona seperti utilitas paket ICMP (ping) menuju segmen internal langsung diblokir total (*Request Time Out*) demi menjaga integritas data jaringan lokal.

### Kesimpulan
1. **FortiGate** sukses beroperasi sebagai pusat pengamanan perimeter (*central stateful firewall*) enterprise lintas zona melalui pendefinisian aturan policy yang ketat.
2. Mekanisme **Virtual IP (VIP) / Port Forwarding** berhasil memetakan permintaan publik eksternal menuju infrastruktur server terisolasi pada DMZ secara transparan dan aman.
3. Segmentasi jaringan *enterprise multi-vendor* ini menjamin tercapainya aspek *confidentiality* dan *availability* layanan tanpa mengorbankan integritas data privat pada area LAN lokal.
