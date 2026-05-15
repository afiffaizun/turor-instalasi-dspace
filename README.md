# Panduan Instalasi DSpace

## Prasyarat Sistem
- Ubuntu 20.04 LTS atau lebih baru
- Minimal 4GB RAM
- 20GB ruang disk kosong
- Koneksi internet yang stabil
- Akses sudo/root

---

## Masuk ke mode wsl

```bash
sudo su
cd ~
```

## Step 1: Update dan Upgrade Sistem

Pastikan sistem Anda sudah ter-update dengan versi terbaru:

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

**Penjelasan:**
- `apt-get update`: Memperbarui daftar paket yang tersedia
- `apt-get upgrade -y`: Mengupgrade semua paket yang terinstal ke versi terbaru
- Flag `-y`: Otomatis menjawab "yes" untuk semua prompt

---

## Step 2: Install Node.js melalui NVM

### 2.1 Install curl (jika belum terinstal)
```bash
apt-get install curl
```

### 2.2 Install NVM (Node Version Manager)
```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.40.1/install.sh | bash
```

**Referensi:** https://github.com/creationix/nvm/releases

### 2.3 Load NVM ke sesi terminal
```bash
source ~/.bashrc
```

### 2.4 Verifikasi instalasi NVM
```bash
nvm list-remote
```

### 2.5 Install Node.js versi 18.20.4
```bash
nvm install v18.20.4
```

### 2.6 Verifikasi instalasi Node.js
```bash
node --version
npm --version
```

---

## Step 3: Instalasi Yarn

### 3.1 Tambahkan repository Yarn
```bash
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```

### 3.2 Import GPG key Yarn
```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
```

### 3.3 Update dan install Yarn
```bash
sudo apt update && sudo apt install yarn
```

### 3.4 Verifikasi instalasi Yarn
```bash
yarn --version
```

---

## Step 4: Install DSpace Frontend

### 4.1 Download DSpace Angular (Frontend)
```bash
wget -c https://github.com/DSpace/dspace-angular/archive/refs/tags/dspace-8.0.tar.gz
```

### 4.2 Extract file tar
```bash
tar zxvf dspace-8.0.tar.gz
```

### 4.3 Pindahkan dan rename folder
```bash
mv dspace-angular-dspace-8.0 dspace-8-angular
```

### 4.4 Masuk ke direktori DSpace
```bash
cd dspace-8-angular
```

### 4.5 Install dependencies dengan Yarn
```bash
yarn install
```

**Catatan:** Proses ini membutuhkan waktu beberapa menit tergantung kecepatan internet

### 4.6 Keluar dari direktori
```bash
exit
```

---

## Step 5: Konfigurasi User dan Direktori DSpace

### 5.1 Masuk sebagai superuser
```bash
sudo su
```

### 5.2 Lewati
```bash

```

### 5.3 Tambahkan user DSpace
```bash
sudo useradd -m dspace
```

### 5.4 Set password untuk user dspace
```bash
sudo passwd dspace

Masukan password : dspace
```

### 5.5 Buat direktori DSpace
```bash
sudo mkdir /dspace
```

### 5.6 Ubah ownership direktori ke user dspace
```bash
sudo chown dspace /dspace
```

---

## Step 6: Deploy DSpace Frontend ke /opt

### 6.1 Copy direktori DSpace ke /opt
```bash
sudo cp -r dspace-8-angular /opt/
```

### 6.2 Ubah ownership ke user dspace
```bash
sudo chown dspace:dspace -R /opt/dspace-8-angular
```

### 6.3 Masuk ke direktori deployment
```bash
cd /opt/dspace-8-angular
```

### 6.4 Jalankan Yarn development server
```bash
yarn start
```

**Catatan:** 
- Server akan berjalan di mode development
- Proses build awal membutuhkan waktu beberapa menit
- Jangan tutup terminal ini selama frontend berjalan

---

## Step 7: Akses Frontend di Web Browser

### 7.1 Buka browser
Buka web browser favorit Anda (Chrome, Firefox, Edge, dll)

### 7.2 Akses URL lokal
```
http://127.0.0.1:4000
```

**Atau:**
```
http://localhost:4000
```

**Catatan:**
- Port default DSpace Angular adalah 4000
- Jika port sudah digunakan, Yarn akan menggunakan port lain (biasanya 4001, 4002, dst)
- Perhatikan output terminal untuk melihat port yang digunakan

---

## Step 8: Koneksi Frontend ke Backend

### 8.1 Edit file konfigurasi
```bash
sudo gedit /opt/dspace-7-angular/config/config.yml
```

**Atau gunakan editor lain:**
```bash
sudo nano /opt/dspace-7-angular/config/config.yml
```
```bash
sudo vi /opt/dspace-7-angular/config/config.yml
```

### 8.2 Konfigurasi Backend URL

Cari baris berikut dalam file config.yml:
```yaml
rest:
  ssl: false
  host: localhost
  port: 8080
  nameSpace: /server
```

Sesuaikan dengan konfigurasi backend DSpace Anda:
- **host**: IP address atau hostname server backend
- **port**: Port backend DSpace (default: 8080)
- **ssl**: true jika menggunakan HTTPS, false untuk HTTP

### 8.3 Restart Yarn server

Setelah mengubah konfigurasi, restart server:
1. Tekan `Ctrl + C` di terminal untuk stop server
2. Jalankan kembali: `yarn start`

---

## Instalasi Backend DSpace (Opsional)

Jika Anda juga perlu menginstal backend DSpace, berikut langkah dasarnya:

### Prasyarat Backend:
- Java JDK 11 atau 17
- PostgreSQL 12 atau lebih baru
- Apache Maven 3.6+
- Apache Ant 1.10+
- Apache Tomcat 9

### Perintah Instalasi Dasar:

```bash
# Install Java
sudo apt install openjdk-11-jdk

# Install PostgreSQL
sudo apt install postgresql postgresql-contrib

# Install Maven
sudo apt install maven

# Install Ant
sudo apt install ant

# Download DSpace Backend
wget https://github.com/DSpace/DSpace/releases/download/dspace-8.0/dspace-8.0-src-release.tar.gz

# Extract dan build
tar -xzf dspace-8.0-src-release.tar.gz
cd dspace-8.0-src-release
mvn package
```

---

## Troubleshooting Umum

### Error: Port 4000 sudah digunakan
```bash
# Cek proses yang menggunakan port 4000
sudo lsof -i :4000

# Kill proses jika perlu
kill -9 <PID>
```

### Error: Yarn command not found
```bash
# Reinstall Yarn
npm install -g yarn
```

### Error: Permission denied
```bash
# Berikan permission yang tepat
sudo chown -R $USER:$USER /opt/dspace-8-angular
```

### Error: Cannot connect to backend
- Pastikan backend DSpace sudah running
- Verifikasi konfigurasi di config.yml
- Cek firewall tidak memblok koneksi

---

## Tips Produksi

### 1. Build untuk Production
```bash
yarn build:prod
```

### 2. Setup dengan PM2 (Process Manager)
```bash
# Install PM2
npm install -g pm2

# Jalankan dengan PM2
pm2 start "yarn start" --name dspace-frontend

# Simpan konfigurasi PM2
pm2 save

# Setup startup script
pm2 startup
```

### 3. Setup Reverse Proxy dengan Nginx
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

---

## Referensi

- **DSpace Official Documentation**: https://wiki.lyrasis.org/display/DSDOC8x
- **DSpace Angular GitHub**: https://github.com/DSpace/dspace-angular
- **DSpace Backend GitHub**: https://github.com/DSpace/DSpace
- **NVM Releases**: https://github.com/nvm-sh/nvm/releases
- **Yarn Documentation**: https://yarnpkg.com/

---

## Informasi Versi

- **DSpace Version**: 8.0
- **Node.js Version**: 18.20.4
- **Angular Version**: 16+ (included in DSpace)
- **Yarn Version**: Latest stable

---

## Dukungan dan Komunitas

- **DSpace User Mailing List**: dspace-community@googlegroups.com
- **DSpace Slack**: https://dspace-org.slack.com
- **Stack Overflow**: Tag `dspace`

---

**Catatan Penting:**
- Panduan ini untuk instalasi development/testing
- Untuk production, gunakan build production dan reverse proxy
- Selalu backup data sebelum melakukan instalasi atau update
- Ikuti best practices keamanan untuk deployment production

---

*Dokumen ini dibuat berdasarkan DSpace 8.0 pada Ubuntu 20.04/22.04 LTS*
