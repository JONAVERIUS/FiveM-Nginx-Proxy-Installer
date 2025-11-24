# FiveM Nginx Proxy Auto Installer

Script otomatis untuk menginstal dan mengkonfigurasi proxy server Nginx untuk FiveM di VPS Ubuntu 20.04.

## 📋 Deskripsi

Script ini secara otomatis akan:
- Menginstal Nginx dari repositori resmi
- Mengkonfigurasi Nginx sebagai reverse proxy untuk server FiveM
- (Opsional) Membuat sertifikat SSL gratis menggunakan Let's Encrypt
- Mengatur konfigurasi proxy untuk HTTP/HTTPS dan TCP streaming

## 🎯 Fitur

- ✅ Instalasi otomatis Nginx versi terbaru
- ✅ Konfigurasi proxy untuk FiveM
- ✅ Support SSL/HTTPS gratis dengan Certbot
- ✅ Konfigurasi stream TCP untuk koneksi FiveM
- ✅ Interface interaktif yang mudah digunakan
- ✅ Pembersihan konfigurasi default otomatis

## 📦 Persyaratan

- VPS dengan Ubuntu 20.04
- Akses root atau sudo
- Domain yang sudah mengarah ke IP VPS (jika ingin menggunakan SSL)
- Port yang dibutuhkan:
  - Port 80 (HTTP)
  - Port 443 (HTTPS - jika menggunakan SSL)
  - Port 30120 (default FiveM) atau port kustom server Anda

## 🚀 Cara Instalasi

### 1. Download Script

```bash
wget https://raw.githubusercontent.com/[repository]/install_proxy.sh
```

Atau clone repository:

```bash
git clone https://github.com/[repository]/FiveM-Nginx-Proxy-Installer.git
cd FiveM-Nginx-Proxy-Installer
```

### 2. Berikan Permission Eksekusi

```bash
chmod +x install_proxy.sh
```

### 3. Jalankan Script

```bash
sudo bash install_proxy.sh
```

## 📝 Petunjuk Penggunaan

Saat script berjalan, Anda akan diminta untuk memasukkan informasi berikut:

### 1. Alamat IP & Port Server FiveM

```
Masukkan alamat IP (dengan port) dari server FiveM Anda (Contoh: 1.1.1.1:30120)
Alamat IP & Port Server: 
```

**Contoh input:** `192.168.1.100:30120`

### 2. Nama Domain

```
Masukkan nama domain yang akan digunakan (Contoh: play.domainanda.com)
Nama Domain: 
```

**Contoh input:** `play.servermu.com`

⚠️ **Penting:** Pastikan domain sudah mengarah ke IP VPS Anda sebelum melanjutkan.

### 3. Opsi SSL (HTTPS)

```
Apakah Anda ingin membuat sertifikat SSL (HTTPS) gratis? (y/n)
Buat SSL Otomatis: 
```

- Ketik `y` untuk mengaktifkan SSL (direkomendasikan)
- Ketik `n` untuk menggunakan HTTP saja

## 🔒 Tentang SSL/HTTPS

### Keuntungan Menggunakan SSL:

- ✅ Koneksi aman dan terenkripsi
- ✅ Melindungi data pemain
- ✅ Meningkatkan kepercayaan
- ✅ Gratis menggunakan Let's Encrypt

### Persyaratan SSL:

1. Domain valid yang sudah terdaftar
2. Domain sudah mengarah ke IP VPS (DNS A Record)
3. Port 80 dan 443 terbuka di firewall
4. Email valid (opsional untuk notifikasi pembaruan)

## 📊 Struktur Konfigurasi

Script akan membuat/memodifikasi file-file berikut:

```
/etc/nginx/
├── nginx.conf          # Konfigurasi utama Nginx
├── stream.conf         # Konfigurasi TCP stream untuk FiveM
├── web.conf            # Konfigurasi web server/reverse proxy
└── ssl/                # Folder sertifikat SSL
    ├── fullchain.pem
    └── privkey.pem
```

## 🎮 Cara Koneksi ke Server

### Dengan SSL (HTTPS):

```
connect https://play.servermu.com
```

### Tanpa SSL (HTTP):

```
connect http://play.servermu.com
```

Atau tambahkan ke server list F8 di FiveM.

## 🔧 Troubleshooting

### SSL Gagal Dibuat

**Masalah:** Certbot gagal membuat sertifikat SSL

**Solusi:**
1. Pastikan domain sudah mengarah ke IP VPS Anda
2. Cek dengan: `nslookup namadomainanda.com`
3. Tunggu propagasi DNS (bisa 1-24 jam)
4. Pastikan port 80 terbuka

### Nginx Gagal Start

**Masalah:** Nginx tidak bisa restart

**Solusi:**
```bash
# Cek status
sudo systemctl status nginx

# Cek konfigurasi
sudo nginx -t

# Lihat log error
sudo tail -f /var/log/nginx/error.log
```

### Port Sudah Digunakan

**Masalah:** Port 80 atau 443 sudah digunakan

**Solusi:**
```bash
# Cek proses yang menggunakan port
sudo lsof -i :80
sudo lsof -i :443

# Stop service yang menggunakan port
sudo systemctl stop apache2  # jika ada Apache
```

### Tidak Bisa Koneksi ke Server

**Masalah:** Tidak bisa connect ke server FiveM

**Solusi:**
1. Pastikan server FiveM sudah berjalan
2. Cek firewall VPS:
```bash
sudo ufw status
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 30120/tcp
```
3. Pastikan IP dan port server FiveM benar

## 🔄 Update Sertifikat SSL

Sertifikat Let's Encrypt berlaku 90 hari. Untuk perpanjangan otomatis:

```bash
# Setup cron job untuk auto-renewal
sudo crontab -e

# Tambahkan baris ini:
0 12 * * * /usr/bin/certbot renew --quiet && systemctl reload nginx
```

## 🗑️ Uninstall

Untuk menghapus instalasi:

```bash
# Stop Nginx
sudo systemctl stop nginx
sudo systemctl disable nginx

# Hapus Nginx
sudo apt-get remove --purge nginx nginx-common

# Hapus konfigurasi
sudo rm -rf /etc/nginx

# Hapus sertifikat SSL
sudo rm -rf /etc/letsencrypt
```

## 📚 Dokumentasi Tambahan

- [Dokumentasi Nginx](https://nginx.org/en/docs/)
- [FiveM Server Documentation](https://docs.fivem.net/docs/server-manual/setting-up-a-server/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)

## ⚙️ Konfigurasi Lanjutan

### Mengubah Timeout

Edit `/etc/nginx/nginx.conf`:

```nginx
proxy_connect_timeout 600;
proxy_send_timeout 600;
proxy_read_timeout 600;
```

### Menambah Domain

Untuk menambah domain lain, edit `/etc/nginx/web.conf`:

```nginx
server_name domain1.com domain2.com;
```

### Custom SSL Certificate

Jika ingin menggunakan sertifikat SSL sendiri:

```bash
# Copy sertifikat ke folder ssl
sudo cp fullchain.pem /etc/nginx/ssl/
sudo cp privkey.pem /etc/nginx/ssl/

# Restart Nginx
sudo systemctl restart nginx
```

## 🆘 Support

Jika mengalami masalah atau butuh bantuan:

1. Cek log Nginx: `sudo tail -f /var/log/nginx/error.log`
2. Cek status service: `sudo systemctl status nginx`
3. Test konfigurasi: `sudo nginx -t`
4. Buka issue di GitHub repository

## 📄 Lisensi

Script ini bebas digunakan untuk keperluan pribadi maupun komersial.

## ⚠️ Disclaimer

- Pastikan Anda memiliki backup sebelum menjalankan script
- Script ini akan memodifikasi konfigurasi Nginx yang ada
- Gunakan dengan tanggung jawab sendiri

## 👨‍💻 Kontributor

Dibuat untuk memudahkan instalasi proxy server FiveM di VPS.

---

**Selamat menggunakan! Semoga server FiveM Anda berjalan lancar! 🎮**
