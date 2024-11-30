# **Instalasi dan Konfigurasi ProFTPD di Debian Server**

## **Pendahuluan**
Dokumentasi ini berisi panduan lengkap untuk menginstal, mengonfigurasi, dan menggunakan ProFTPD pada sistem berbasis Debian. ProFTPD adalah perangkat lunak server FTP yang andal untuk menyediakan layanan FTP.

---

## **Instalasi**

### **Langkah 1: Update Sistem**
Jalankan perintah berikut untuk memperbarui dan meningkatkan sistem Debian Anda:
```bash
sudo apt update && sudo apt upgrade -y
```

### **Langkah 2: Instal ProFTPD**
Instal ProFTPD menggunakan manajer paket:
```bash
sudo apt install proftpd -y
```

Selama proses instalasi:
- Pilih mode **Standalone** ketika diminta.

### **Langkah 3: Verifikasi Instalasi**
Periksa status layanan ProFTPD:
```bash
sudo systemctl status proftpd
```

Jika belum berjalan, aktifkan layanan:
```bash
sudo systemctl start proftpd
sudo systemctl enable proftpd
```

---

## **Konfigurasi**

### **Langkah 1: Edit File Konfigurasi**
Buka file konfigurasi ProFTPD:
```bash
sudo nano /etc/proftpd/proftpd.conf
```

Lakukan perubahan berikut:
```conf
# Mengizinkan lebih banyak proses
MaxInstances 50

# Mode pasif konfigurasi
PassivePorts 30000 35000

# Root direktori default untuk pengguna
DefaultRoot ~

# Izinkan login tanpa shell yang valid
RequireValidShell off

# (Opsional) Pengaturan FTP Anonim
<Anonymous ~ftp>
  User ftp
  Group nogroup
  UserAlias anonymous ftp
  RequireValidShell off
  <Directory *>
    <Limit WRITE>
      DenyAll
    </Limit>
  </Directory>
</Anonymous>
```

### **Langkah 2: Restart Layanan ProFTPD**
Setelah mengedit file, restart layanan ProFTPD:
```bash
sudo systemctl restart proftpd
```

### **Langkah 3: Buat Pengguna FTP**
Tambahkan pengguna untuk akses FTP:
```bash
sudo adduser ftpserver
```

Setel password untuk pengguna selama proses tersebut. Pengguna ini akan memiliki akses ke direktori home-nya untuk FTP.

### **Langkah 4: Buat Direktori FTP (Opsional)**
Buat direktori khusus untuk file FTP:
```bash
sudo mkdir /home/ftpserver
sudo chown ftpserver:ftpserver /home/ftpserver
```

---

## **Penggunaan**

### **Langkah 1: Akses FTP Secara Lokal**
Untuk menguji server FTP secara lokal, gunakan perintah berikut:
```bash
ftp localhost
```
- Masukkan username (`ftpserver`) dan password yang telah dibuat.

### **Langkah 2: Akses FTP dari Perangkat Lain**
1. **Cek Alamat IP Debian Server:**
   ```bash
   ip addr show
   ```
   - Catat alamat IP server (contoh: `192.168.1.10`).

2. **Atur Jaringan di VirtualBox:**
   - Gunakan mode **Bridged Adapter** pada pengaturan jaringan VirtualBox agar server dapat diakses dari perangkat lain.

3. **Buka Port 21 di Firewall (Jika Diperlukan):**
   ```bash
   sudo ufw allow 21/tcp
   sudo ufw allow 30000:35000/tcp
   sudo ufw reload
   ```

4. **Gunakan Aplikasi FTP Client:**
   - Di perangkat lain, instal aplikasi FTP client seperti **FileZilla** atau **WinSCP**.
   - Hubungkan dengan parameter berikut:
     - **Host:** `192.168.1.10` (IP Debian Server)
     - **Username:** `ftpserver`
     - **Password:** (yang telah dibuat)
     - **Port:** `21`

### **Langkah 3: Upload dan Download File**
- **Upload File:** Drag-and-drop file ke direktori server pada aplikasi FTP client.
- **Download File:** Klik kanan pada file di server dan pilih **Download**.

---

## **Opsional: Aktifkan FTP Aman (FTPS)**
1. Instal modul TLS:
   ```bash
   sudo apt install proftpd-mod-tls
   ```

2. Konfigurasi TLS:
   Buka file konfigurasi TLS:
   ```bash
   sudo nano /etc/proftpd/tls.conf
   ```
   Atur path sertifikat yang sesuai.

3. Restart ProFTPD:
   ```bash
   sudo systemctl restart proftpd
   ```

---

## **Pemecahan Masalah**
- **Masalah:** Tidak bisa terhubung dari perangkat lain.
  - Pastikan adapter jaringan VirtualBox diset ke **Bridged Adapter**.
  - Periksa alamat IP server dan pastikan port 21 dapat diakses.

- **Masalah:** Login gagal.
  - Periksa kembali username dan password.
  - Pastikan layanan ProFTPD sedang berjalan.

---

## **Dokumentasi Berhasil**
![Dokumentasi Berhasil](image.png)

