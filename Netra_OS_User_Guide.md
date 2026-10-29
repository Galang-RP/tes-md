# Netra OS — Welcome & User Guide

> **Netra OS** adalah distro Linux berbasis **Ubuntu 24.04 LTS** dengan desktop **GNOME**, yang dirancang untuk pembelajaran, analisis lalu lintas data, dan administrasi jaringan komputer. Netra OS menyatukan berbagai toolkit jaringan dalam satu sistem agar mahasiswa Teknik Informatika, siswa SMK TKJ, dan administrator jaringan dapat langsung belajar, menguji, serta melakukan troubleshooting jaringan dengan lebih praktis.

Selamat datang di **Netra OS**! Mulailah dari pemeriksaan koneksi sederhana, pelajari jalur dan layanan jaringan, lalu lanjutkan ke analisis paket serta pengujian performa. Gunakan seluruh alat secara bertanggung jawab dan hanya pada jaringan atau perangkat yang Anda miliki atau telah mendapat izin untuk mengujinya.

---

## Daftar Isi

- [1. Memulai Netra OS](#1-memulai-netra-os)
- [2. Perintah dasar sebelum analisis](#2-perintah-dasar-sebelum-analisis)
- [3. Toolkit jaringan 1–13](#3-toolkit-jaringan-113)
- [4. Fitur unggulan Netra OS](#4-fitur-unggulan-netra-os)
- [5. Alur troubleshooting sederhana](#5-alur-troubleshooting-sederhana)
- [6. Catatan keamanan dan bantuan](#6-catatan-keamanan-dan-bantuan)

---

## 1. Memulai Netra OS

1. **Masuk ke desktop GNOME.** Gunakan menu aplikasi untuk mencari aplikasi seperti Wireshark, Remmina, atau Brave Browser.
2. **Buka Terminal.** Tekan `Ctrl + Alt + T`, atau cari aplikasi **Terminal** dari menu.
3. **Periksa koneksi dan alamat IP.** Jalankan:
   ```bash
   ip addr
   ip route
   ping -c 4 1.1.1.1
   ```
4. **Jalankan alat sesuai kebutuhan.** Alat GUI dapat dibuka dari menu aplikasi; alat CLI dijalankan melalui Terminal.
5. **Gunakan hak administrator seperlunya.** Tambahkan `sudo` hanya untuk perintah yang memang memerlukannya. Baca perintah sebelum menjalankannya.

> **Catatan:** Panduan ini mengasumsikan paket telah tersedia pada instalasi Netra OS. Nama paket atau perintah dapat berbeda menurut versi dan konfigurasi. Pada sistem Linux, sebagian perintah memerlukan koneksi jaringan, hak akses tertentu, atau izin capture.

## 2. Perintah dasar sebelum analisis

| Perintah | Kegunaan |
|---|---|
| `ip addr` | Melihat antarmuka dan alamat IP. |
| `ip route` | Melihat tabel rute dan gateway. |
| `ping -c 4 1.1.1.1` | Menguji konektivitas ke alamat IP tujuan sebanyak 4 kali. |
| `ping -c 4 example.com` | Menguji koneksi sekaligus membantu memeriksa resolusi nama DNS. |
| `hostname` | Menampilkan nama komputer. |
| `ip link` | Melihat antarmuka jaringan. |
| `man nama_perintah` | Membuka manual, misalnya `man traceroute`. |
| `nama_perintah --help` | Menampilkan bantuan singkat jika didukung. |

---

## 3. Toolkit jaringan 1–13

### 1. Wireshark

**Kegunaan:** Menganalisis paket jaringan melalui antarmuka grafis (GUI), melihat protokol, alamat sumber/tujuan, dan detail komunikasi.

**Cara menggunakan:**
1. Buka **Wireshark** dari menu aplikasi atau jalankan `wireshark`.
2. Pilih antarmuka yang aktif, misalnya `eth0`, `enp0s3`, atau `wlan0`.
3. Klik **Start** untuk mulai menangkap paket.
4. Masukkan display filter pada kolom filter, lalu tekan Enter.
5. Klik sebuah paket untuk melihat detail protokol dan byte data.

**Filter dasar:**
```text
dns
http
tcp
udp
icmp
ip.addr == 192.168.1.10
tcp.port == 443
```

**Catatan:** Display filter hanya menyaring tampilan paket yang sudah ditangkap. Capture filter berbeda dan diterapkan sebelum penangkapan. Jika antarmuka tidak muncul atau capture ditolak, periksa izin capture dan konfigurasi sistem. HTTPS biasanya mengenkripsi isi aplikasinya.

### 2. Nmap

**Kegunaan:** Menemukan host yang merespons, memeriksa port terbuka, dan mengenali layanan pada perangkat yang diizinkan untuk diuji.

**Perintah dasar:**
```bash
nmap 192.168.1.1
nmap -sn 192.168.1.0/24
nmap -p 22,53,80,443 192.168.1.1
nmap -sV 192.168.1.1
```

- `nmap 192.168.1.1`: pemeriksaan port umum pada satu host.
- `nmap -sn ...`: penemuan host tanpa pemindaian port.
- `-p`: memilih port tertentu.
- `-sV`: mencoba mengidentifikasi versi layanan.

**Tips:** Ganti alamat contoh dengan alamat jaringan milik Anda. Pemindaian dapat memicu alarm keamanan; dapatkan izin sebelum memindai jaringan pihak lain.

### 3. Traceroute

**Kegunaan:** Menampilkan hop atau perangkat perantara yang dilalui paket menuju tujuan.

**Perintah dasar:**
```bash
traceroute example.com
traceroute 1.1.1.1
```

Jika perintah belum tersedia, pada Ubuntu paketnya biasanya bernama `traceroute`. Tanda `*` dapat berarti router tidak membalas probe; hal itu tidak selalu berarti koneksi putus.

### 4. MTR-tiny

**Kegunaan:** Menggabungkan traceroute dan ping untuk memantau latensi serta perkiraan packet loss pada tiap hop.

**Perintah dasar:**
```bash
mtr example.com
mtr -rw -c 10 example.com
```

- `-r`: membuat laporan lalu keluar.
- `-w`: format laporan lebar.
- `-c 10`: mengirim 10 siklus probe.

Tekan `q` untuk keluar dari mode interaktif. Packet loss pada satu hop saja belum tentu menunjukkan masalah, karena router dapat membatasi balasan ICMP.

### 5. Dnsutils (`dig` dan `nslookup`)

**Kegunaan:** Memeriksa resolusi DNS, record domain, dan server DNS yang digunakan.

**Perintah dasar:**
```bash
dig example.com
dig example.com A
dig example.com MX
dig @1.1.1.1 example.com
nslookup example.com
nslookup example.com 8.8.8.8
```

- `A`: alamat IPv4.
- `AAAA`: alamat IPv6, misalnya `dig example.com AAAA`.
- `MX`: mail server domain.
- `@1.1.1.1`: meminta jawaban dari server DNS yang ditentukan.

### 6. Net-tools (`ifconfig` dan `netstat`)

**Kegunaan:** Menyediakan utilitas jaringan klasik untuk melihat konfigurasi antarmuka dan koneksi. Pada Ubuntu modern, `ip` dan `ss` biasanya lebih disarankan.

**Perintah dasar:**
```bash
ifconfig
netstat -tuln
netstat -rn
```

Alternatif modern:
```bash
ip addr
ss -tuln
ip route
```

- `netstat -tuln`: melihat port TCP/UDP yang sedang mendengarkan dalam bentuk numerik.
- `netstat -rn`: melihat tabel rute.

### 7. Iperf3

**Kegunaan:** Mengukur throughput jaringan antara dua perangkat. Hasil dipengaruhi kondisi jaringan, perangkat, dan konfigurasi pengujian.

**Langkah dasar:**
1. Pada perangkat **server** yang Anda kontrol:
   ```bash
   iperf3 -s
   ```
2. Pada perangkat **client**:
   ```bash
   iperf3 -c 192.168.1.20
   ```
3. Uji arah sebaliknya:
   ```bash
   iperf3 -c 192.168.1.20 -R
   ```

Ganti `192.168.1.20` dengan IP server. Iperf3 umumnya menggunakan TCP port 5201 secara default; pastikan firewall mengizinkannya selama pengujian yang sah.

### 8. Remmina

**Kegunaan:** Klien remote desktop/remote access berbasis GUI untuk mengelola perangkat yang diizinkan, bergantung pada protokol dan plugin yang tersedia.

**Cara menggunakan:**
1. Buka **Remmina** dari menu aplikasi.
2. Buat koneksi baru.
3. Pilih protokol yang tersedia, misalnya **RDP**, **VNC**, atau **SSH**.
4. Masukkan alamat host, port jika diperlukan, dan kredensial yang sah.
5. Simpan profil jika dibutuhkan, lalu hubungkan.

**Contoh SSH melalui Terminal (alternatif):**
```bash
ssh namauser@192.168.1.20
```

**Tips keamanan:** Pastikan host benar dan gunakan kata sandi kuat atau kunci SSH. Jangan menyimpan kredensial pada perangkat bersama yang tidak tepercaya.

### 9. PuTTY

**Kegunaan:** Klien terminal untuk koneksi remote seperti SSH; dukungan Telnet dan Serial bergantung pada kebutuhan serta konfigurasi perangkat. Telnet tidak mengenkripsi lalu lintas.

**Cara menggunakan GUI:**
1. Buka **PuTTY**.
2. Pilih **SSH** sebagai tipe koneksi.
3. Isi **Host Name** dengan IP atau nama host dan port yang benar (umumnya SSH port 22).
4. Klik **Open**, lalu masuk menggunakan akun yang sah.

**Contoh perintah alternatif di Terminal:**
```bash
ssh namauser@192.168.1.20
```

**Catatan:** Pada Linux, PuTTY dapat dipasang atau tersedia sebagai aplikasi terpisah. Untuk koneksi Serial, pilih tipe koneksi Serial dan isi port serta baud rate sesuai dokumentasi perangkat.

### 10. Ipcalc

**Kegunaan:** Membantu menghitung subnet, network address, broadcast address, rentang host, netmask, dan prefix CIDR.

**Perintah dasar:**
```bash
ipcalc 192.168.1.10/24
ipcalc 10.10.0.0/16
```

Contoh konsep: `/24` setara dengan netmask `255.255.255.0` pada IPv4. Perhatikan bahwa jumlah alamat yang dapat dipakai sebagai host bergantung pada ukuran subnet dan kebutuhan khusus jaringan.

### 11. Tcpdump

**Kegunaan:** Menangkap dan menampilkan paket jaringan lewat Terminal (CLI), berguna untuk troubleshooting ketika GUI tidak diperlukan.

**Perintah dasar:**
```bash
sudo tcpdump -D
sudo tcpdump -i any
sudo tcpdump -i any -c 20
sudo tcpdump -i any port 53
sudo tcpdump -i any host 192.168.1.10
sudo tcpdump -i any -w capture.pcap
sudo tcpdump -r capture.pcap
```

- `-D`: menampilkan daftar antarmuka yang tersedia.
- `-i any`: menangkap dari antarmuka yang didukung sistem.
- `-c 20`: berhenti setelah 20 paket.
- `port 53`: menyaring lalu lintas port 53.
- `-w`: menyimpan hasil ke file PCAP.
- `-r`: membaca file PCAP.

Tekan `Ctrl + C` untuk menghentikan capture yang berjalan. File PCAP dapat memuat informasi sensitif; simpan dan bagikan hanya jika diizinkan.

### 12. OpenVPN

**Kegunaan:** Membuat atau mengelola koneksi VPN berdasarkan konfigurasi OpenVPN yang disediakan administrator atau layanan tepercaya.

**Perintah dasar:**
```bash
openvpn --version
sudo openvpn --config client.ovpn
```

- `--version`: melihat versi OpenVPN.
- `--config client.ovpn`: menjalankan koneksi dari file konfigurasi yang tersedia.

Pastikan file konfigurasi `.ovpn` berasal dari sumber tepercaya. Perintah tersebut berjalan di foreground; gunakan `Ctrl + C` untuk menghentikan koneksi. Cara autentikasi dan hak akses dapat berbeda menurut konfigurasi.

### 13. Brave Browser

**Kegunaan:** Browser untuk membuka dokumentasi jaringan, dashboard router, panel administrasi server, dan halaman konfigurasi perangkat.

**Cara menggunakan:**
1. Buka Brave dari menu aplikasi atau jalankan `brave-browser` jika nama perintah itu tersedia.
2. Buka alamat dashboard perangkat, misalnya `https://192.168.1.1` (sesuaikan dengan alamat gateway Anda).
3. Masuk dengan akun administrator yang sah.
4. Periksa dokumentasi resmi perangkat sebelum mengubah konfigurasi.

**Tips:** Jangan mengabaikan peringatan sertifikat tanpa memahami penyebabnya. Hindari menyimpan kata sandi admin di perangkat yang digunakan bersama.

---

## 4. Fitur unggulan Netra OS

### Netra Guide
Panduan offline untuk mempelajari Netra OS, perintah dasar jaringan, subnetting, dan troubleshooting. Gunakan sebagai referensi saat praktikum tanpa harus selalu terhubung ke internet.

### Netra Fine Look
Fitur personalisasi desktop untuk mengubah tema, ikon, wallpaper, serta tampilan jendela sesuai preferensi. Ketersediaan animasi atau efek tertentu bergantung pada implementasi dan konfigurasi GNOME.

### Netra Workspace
Lingkungan kerja yang membantu pengguna mengakses aplikasi jaringan dan mengatur alur kerja pembelajaran maupun administrasi. Anda dapat mengelompokkan pekerjaan berdasarkan kebutuhan, misalnya analisis paket, pemetaan jaringan, dan remote access.

---

## 5. Alur troubleshooting sederhana

Gunakan urutan berikut saat mengalami masalah koneksi:

1. **Periksa antarmuka dan IP**
   ```bash
   ip addr
   ip link
   ```
2. **Periksa gateway**
   ```bash
   ip route
   ping -c 4 ALAMAT_GATEWAY
   ```
3. **Uji koneksi ke IP publik**
   ```bash
   ping -c 4 1.1.1.1
   ```
4. **Uji DNS**
   ```bash
   dig example.com
   nslookup example.com
   ```
5. **Telusuri rute**
   ```bash
   traceroute example.com
   mtr -rw -c 10 example.com
   ```
6. **Periksa port atau layanan yang Anda kelola**
   ```bash
   ss -tuln
   nmap ALAMAT_IP_PERANGKAT
   ```
7. **Analisis paket jika dibutuhkan**
   Gunakan Wireshark atau `tcpdump` dengan filter yang sesuai.

Ganti teks seperti `ALAMAT_GATEWAY` dan `ALAMAT_IP_PERANGKAT` dengan alamat yang benar. Jalankan langkah sesuai konteks; tidak semua masalah membutuhkan seluruh alat di atas.

## 6. Catatan keamanan dan bantuan

- Lakukan pemindaian, packet capture, dan pengujian throughput hanya pada jaringan/perangkat milik sendiri atau yang telah memberi izin.
- Jangan menangkap, menyimpan, atau membagikan data pengguna lain tanpa izin.
- Hindari memasukkan kata sandi, token, private key, atau informasi rahasia ke laporan dan tangkapan layar.
- Gunakan `man nama_perintah` atau `nama_perintah --help` untuk membaca opsi lengkap.
- Jika alat tidak ditemukan, periksa apakah paket terpasang dan apakah nama perintahnya sesuai. Netra OS dirancang berbasis Ubuntu 24.04 LTS, tetapi isi akhir sistem bergantung pada konfigurasi remastering Cubic.

**Selamat belajar dan bereksperimen dengan Netra OS!** Mulailah dari jaringan laboratorium yang aman, catat hasil pengujian, lalu gunakan hasil analisis untuk memahami cara kerja jaringan komputer.
