Tugas Modul 5: Vlan Trunk OSPF MultiVendor

## Daftar Isi
1. [Deskripsi Singkat](#1-deskripsi-singkat)
2. [Tujuan Tugas](#2-tujuan-tugas)
3. [Tugas Modul](#3-tugas-modul)
    - [Modul 1: Konfigurasi Cisco Switch Jakarta](#modul-1-konfigurasi-cisco-switch-jakarta)
    - [Modul 2: Konfigurasi Cisco Router Jakarta](#modul-2-konfigurasi-cisco-router-jakarta)
    - [Modul 3: Konfigurasi MikroTik Router Jakarta](#modul-3-konfigurasi-mikrotik-router-jakarta)
    - [Modul 4: Konfigurasi Ubuntu Server Jakarta](#modul-4-konfigurasi-ubuntu-server-jakarta)
    - [Modul 5: Konfigurasi FortiGate Jakarta](#modul-5-konfigurasi-fortigate-jakarta)
    - [Modul 6: Konfigurasi MikroTik ISP](#modul-6-konfigurasi-mikrotik-isp)
    - [Modul 7: Konfigurasi Switch dan MikroTik Surabaya](#modul-7-konfigurasi-switch-dan-mikrotik-surabaya)
    - [Modul 8: Konfigurasi FortiGate Surabaya](#modul-8-konfigurasi-fortigate-surabaya)
    - [Modul 9: Konfigurasi GRE Tunnel dan OSPF over GRE](#modul-9-konfigurasi-gre-tunnel-dan-ospf-over-gre)
    - [Modul 10: Pengujian Akhir](#modul-10-pengujian-akhir)

---

## 1. Deskripsi Singkat
Topologi ini merupakan simulasi jaringan *enterprise* yang menghubungkan Jakarta (HQ) dan Surabaya (Branch) melalui GRE Tunnel. Perangkat yang digunakan meliputi Cisco ViOS, Cisco Switch, MikroTik RouterOS, FortiGate, Ubuntu Server, dan client (Tinycore/VPCS).

## 2. Tujuan Tugas
- Mengkonfigurasi VLAN & Trunking pada Cisco Switch.
- Implementasi VRRP (High Availability Gateway).
- Konfigurasi DHCP Server terpusat (ISC-DHCP) & DHCP Relay.
- Konfigurasi Firewall & NAT pada FortiGate.
- Implementasi GRE Tunnel & OSPF Multivendor.

---

## 3. Tugas Modul

### Modul 1: Konfigurasi Cisco Switch Jakarta
- **Target:** VLAN 10, 20, 60.
- **Hasil:** Port access dan trunking telah dikonfigurasi.
- **Bukti:** - [Screenshot Topologi Jakarta]
  - [Screenshot `show vlan brief`]
  - [Screenshot `show interfaces trunk`]

### Modul 2: Konfigurasi Cisco Router Jakarta
- **Target:** Subinterface, VRRP Master (VLAN 10, 60), DHCP Relay.
- **Bukti:**
  - [Screenshot `show ip interface brief`]
  - [Screenshot `show vrrp brief`]
  - [Screenshot Ping ke FortiGate]

### Modul 3: Konfigurasi MikroTik Router Jakarta
- **Target:** VRRP Master (VLAN 20), DHCP Relay.
- **Bukti:**
  - [Screenshot `/ip address print`]
  - [Screenshot `/interface vrrp print`]
  - [Screenshot `/ip dhcp-relay print`]

### Modul 4: Konfigurasi Ubuntu Server Jakarta
- **Target:** ISC-DHCP Server (VLAN 10, 20), Web Server (Nginx).
- **Bukti:**
  - [Screenshot `/etc/dhcp/dhcpd.conf`]
  - [Screenshot Ping 8.8.8.8]

### Modul 5: Konfigurasi FortiGate Jakarta
- **Target:** Edge Firewall, NAT, GRE Tunnel ke Surabaya.
- **Bukti:**
  - [Screenshot Firewall Policy]
  - [Screenshot `get router info ospf neighbor`]
  - [Screenshot Ping Tunnel Surabaya]

### Modul 6: Konfigurasi MikroTik ISP
- **Target:** Routing antar WAN.
- **Bukti:**
  - [Screenshot `/ip firewall nat print`]
  - [Screenshot Ping antar-WAN FortiGate]

### Modul 7: Konfigurasi Switch dan MikroTik Surabaya
- **Target:** VLAN 30, 40, DHCP Server Lokal.
- **Bukti:**
  - [Screenshot `show vlan brief`]
  - [Screenshot IP DHCP Client]
  - [Screenshot Ping Client ke Internet]

### Modul 8: Konfigurasi FortiGate Surabaya
- **Target:** GRE Tunnel ke Jakarta, OSPF.
- **Bukti:**
  - [Screenshot `get router info routing-table ospf`]
  - [Screenshot Ping ke IP Tunnel Jakarta]

### Modul 9: Konfigurasi GRE Tunnel dan OSPF over GRE
- **Target:** Konektivitas antar-site.
- **Bukti:**
  - [Screenshot Ping Tunnel antar-FortiGate]
  - [Screenshot Ping antar-Site Client]

### Modul 10: Pengujian Akhir
- **Target:** Verifikasi seluruh alur traffic dan failover.
- **Bukti:**
  - [Screenshot IP DHCP Client Jakarta & Surabaya]
  - [Screenshot Akses Web Server Jakarta dari Surabaya]
  - [Analisis Singkat Jalur Traffic]

