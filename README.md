# Panduan Mengonfigurasi PM2 dan Node.js
## 1. Pastikan Aplikasi Anda Berfungsi dengan Baik
### 1. Jalankan Aplikasi Secara Langsung:
Jalankan aplikasi Anda menggunakan Node.js untuk memastikan tidak ada masalah dengan aplikasi:
```bash
node /root/call/app.js
```
Note : Sesuaikan dengan project anda.

### 2. Periksa Kode Aplikasi:
Pastikan tidak ada perintah process.exit() atau process.kill() yang mungkin menyebabkan aplikasi berhenti.

## 2. Install dan Konfigurasi PM2
### 1. Install PM2 ( Jika Belum Di Install )
```bash
npm install -g pm2
```
### 2. Jalankan Aplikasi dengan PM2:
Jalankan aplikasi Anda menggunakan PM2:
```bash
/root/.nvm/versions/node/v22.4.0/bin/pm2 start /root/call/app.js --name myapp
```
### 3. Simpan Daftar Proses PM2:
Simpan daftar proses PM2 agar PM2 dapat memulihkannya setelah reboot:
```bash
/root/.nvm/versions/node/v22.4.0/bin/pm2 save
```
## 3. Konfigurasi PM2 untuk Startup
### 1. Generate Systemd Startup Script:
Buat skrip startup PM2 untuk systemd:
```bash
/root/.nvm/versions/node/v22.4.0/bin/pm2 startup systemd
```
Note : Catat dan salin perintah output yang diberikan, biasanya systemctl enable pm2-root.

### 2. Enable PM2 Service:
Jalankan perintah output dari langkah sebelumnya untuk mengaktifkan layanan PM2:
```bash
systemctl enable pm2-root
```
## 4. Buat dan Periksa Unit File Systemd
### 1. Periksa Unit File:
Pastikan file unit systemd (/etc/systemd/system/pm2-root.service) memiliki konten berikut:
```bash
[Unit]
Description=PM2 process manager
Documentation=https://pm2.keymetrics.io/
After=network.target

[Service]
Type=forking
User=root
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
Environment=PATH=/root/.nvm/versions/node/v22.4.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
Environment=PM2_HOME=/root/.pm2
PIDFile=/root/.pm2/pm2.pid
Restart=on-failure

ExecStart=/root/.nvm/versions/node/v22.4.0/bin/pm2 resurrect
ExecReload=/root/.nvm/versions/node/v22.4.0/bin/pm2 reload all
ExecStop=/root/.nvm/versions/node/v22.4.0/bin/pm2 kill

[Install]
WantedBy=multi-user.target
```
Note : Kode diatas di generate otomatis oleh pm2

### 2. Reload Systemd Daemon:
Muat ulang daemon systemd untuk menerapkan perubahan:
```bash
sudo systemctl daemon-reload
```
### 3. Restart PM2 Service:
Restart layanan PM2 untuk menerapkan konfigurasi:
```bash
sudo systemctl restart pm2-root
```
### 4. Cek Status Layanan PM2:
Pastikan layanan PM2 aktif dan berjalan:
```bash
sudo systemctl status pm2-root
```
## 5. Periksa Log dan Debugging
### 1. Periksa Log Aplikasi:
Periksa log aplikasi untuk melihat apakah ada kesalahan saat aplikasi dijalankan
```bash
/root/.nvm/versions/node/v22.4.0/bin/pm2 logs
```
### 2. Periksa Log Systemd:
Jika layanan PM2 gagal, periksa log systemd untuk detail kesalahan:
```bash
journalctl -u pm2-root
```

## Note : **Sesuaikan Path Node Anda!**
```bash
which node
```



