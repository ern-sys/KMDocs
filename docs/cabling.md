Sip, kita bikin versi gampangnya ya bro. Anggap ini lo jalankan di satu VM (misalnya Ubuntu Server) di Proxmox. Targetnya: 1 IP publik bisa dipakai buat beberapa layanan internal (website, monitoring, dll).

---

### 1. Siapin VM

- Bikin VM di Proxmox (Ubuntu Server / Debian).
    
- Kasih IP **private** (contoh: `10.10.10.100`) dan pastikan bisa akses internet.
    
- Pastikan DNS domain/subdomain kampus nanti bisa diarahkan ke IP publik lo.
    

---

### 2. Install Nginx & Certbot

Login ke VM lalu jalankan:

```bash
sudo apt update
sudo apt install nginx -y
sudo apt install certbot python3-certbot-nginx -y
```

---

### 3. Tes Nginx

Buka browser → ketik IP VM (`http://10.10.10.100`) → harus keluar halaman “Welcome to nginx!”.  
Kalau muncul, artinya Nginx jalan.

---

### 4. Buat Subdomain

Misalnya lo punya domain `npu.ac.id`, atur DNS (di registrar / Cloudflare):

- `monitoring.npu.ac.id` → arahkan ke IP publik lo
    
- `website.npu.ac.id` → arahkan ke IP publik lo
    

---

### 5. Tambah Config Reverse Proxy

Edit config Nginx:

```bash
sudo nano /etc/nginx/sites-available/npu.conf
```

Isinya contoh:

```nginx
server {
    server_name monitoring.npu.ac.id;

    location / {
        proxy_pass http://10.10.1.10:3000;   # contoh Grafana
    }
}

server {
    server_name website.npu.ac.id;

    location / {
        proxy_pass http://10.10.1.20:80;     # contoh Website kampus
    }
}
```

Simpan → lalu aktifkan:

```bash
sudo ln -s /etc/nginx/sites-available/npu.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

### 6. Pasang SSL (Gratis)

Jalankan:

```bash
sudo certbot --nginx -d monitoring.npu.ac.id -d website.npu.ac.id
```

Kalau berhasil, semua akses jadi **https**.

---

### 7. Uji Coba

- Akses `https://monitoring.npu.ac.id` → harus nyambung ke Grafana.
    
- Akses `https://website.npu.ac.id` → harus nyambung ke web kampus.
    

---

### 8. (Opsional) Tambah Proteksi

Misalnya monitoring hanya boleh diakses staff:

```nginx
location / {
    proxy_pass http://10.10.1.10:3000;
    allow 192.168.1.0/24;
    deny all;
}
```

---

Jadi simpel gini aja bro:

- VM jalanin Nginx.
    
- Nginx jadi “pintu gerbang” buat semua subdomain.
    
- Service di belakang tinggal taruh di IP private, gak usah punya IP publik.
    

---

Mau gue bikinin contoh **topologi gambar** biar lebih kebayang arus trafiknya?