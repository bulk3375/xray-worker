## 🚀 Panduan Deploy (GitHub Actions)

**A Serverless V2Ray Tunnel Optimized for Indonesia**

Siren is a lightweight and serverless V2Ray tunnel built on [Cloudflare Workers](https://workers.cloudflare.com/), supporting modern proxy protocols.  
It offers fast, secure, and scalable deployment without the need for a traditional VPS.

---

## 🔧 Features

- ✅ **Multi-Protocol Support**

  - VMess
  - Trojan
  - VLESS
  - Shadowsocks

- ✅ **Domain over HTTPS (DoH)**  
  Encrypts DNS queries for improved privacy and security.

---

## 🌐 Endpoints

| Endpoint | Description                       |
| -------- | --------------------------------- |
| `/`      | Main landing page                 |
| `/link`  | Generate shareable proxy links    |
| `/sub`   |                                   |

---

### 1️⃣ Persiapan Akun Cloudflare

1. Pastikan Anda memiliki akun [Cloudflare](https://cloudflare.com) (gratis).  
2. Catat **Account ID** Anda:  
   - Login ke [Cloudflare Dashboard](https://dash.cloudflare.com/).  
   - Di halaman utama, scroll ke kanan bawah – cari **Account ID** (misal: `ea1aab81dc8e22018131832d124a54fc`).  
   - Simpan ID ini, nanti akan digunakan di `wrangler.toml` dan secrets.

---

### 2️⃣ Buat Namespace KV

Worker ini membutuhkan KV untuk menyimpan data.

1. Di Cloudflare Dashboard, buka **Workers & Pages** → **KV**.  
2. Klik **Create namespace**, isi nama: `YUMI`.  
3. Setelah dibuat, klik namanya lalu salin **ID** (misal: `8ff1913fa986482d967e0a5c7e2b0791`).  
   Simpan ID ini – akan dipakai di `wrangler.toml`.

---

### 3️⃣ Buat API Token Cloudflare

Token ini digunakan GitHub Actions untuk mengakses akun Cloudflare Anda.

1. Buka [Cloudflare API Tokens](https://dash.cloudflare.com/profile/api-tokens).  
2. Klik **Create Token** → pilih template **Edit Cloudflare Workers**.   
3. Klik **Continue to summary** → **Create Token**.  
---
### 4️⃣ GitHub Repository Secret

   - Navigate to: GitHub → Your Repo → Settings → Secrets and variables → Actions
   - Add a new secret:
     - Name: `CLOUDFLARE_API_TOKEN`
     - Value: Your API token
> web ui yg saya bikin : https://free-cf.benxx.dpdns.org
> silakan kreasikan sendiri untuk web ui nya

---

### 4️⃣ Siapkan Repository GitHub

#### a. Fork atau clone repositori ini ke akun GitHub Anda.

#### b. Edit file `wrangler.toml`

Buka file `wrangler.toml` di repository Anda (bisa langsung dari web GitHub).  
Sesuaikan nilai berikut:

```toml
name = "xray"                     # Boleh diubah sesuai selera
main = "build/worker/shim.mjs"
compatibility_date = "2024-05-23"
minify = true

# Ganti dengan Account ID Anda (dari langkah 1)
account_id = "ea1aab81dc8e22018131832d124a54fc"
workers_dev = true

[[kv_namespaces]]
binding = "YUMI"
# Ganti dengan ID KV Namespace dari langkah 2
id = "8ff1913fa986482d967e0a5c7e2b0791"

[build]
command = "cargo install worker-build && worker-build --release"

[env.dev]
build = { command = "cargo install worker-build && worker-build --dev" }

[vars]
UUID = "2bcfbfba-b446-4ad5-93ad-72af9e008f61"   # Bisa ganti UUID lain
