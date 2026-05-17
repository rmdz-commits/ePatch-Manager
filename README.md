# 🚀 ePatch Manager
![GitHub release (latest by date)](https://img.shields.io/github/v/release/rmdz-commits/ePatch-Manager?style=for-the-badge&color=4265FA)
![Android API](https://img.shields.io/badge/API-23%2B-flat?style=for-the-badge&logo=android&color=3DDC84)
![Language](https://img.shields.io/badge/Language-Java%20%2F%20XML-orange?style=for-the-badge&logo=java)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
**ePatch Manager** adalah aplikasi manajemen modifikasi, pencadangan akun, dan pembaruan aset data secara real-time untuk klon eFootball™. Aplikasi ini dirancang dengan antarmuka modern yang mendukung multi-bahasa dinamis (*Localization*), transisi animasi tingkat tinggi (*Overshoot Interpolator*), serta sistem manajemen repositori otomatis berbasis arsitektur awan hibrida memanfaatkan kekuatan GitHub API & GitHub Releases.
---
## 🌟 Fitur Utama
- **🌍 Lokalisasi Dinamis (Multi-Language Support):** Peralihan bahasa instan dari dalam aplikasi (Bahasa Indonesia, English, Español, Français) tanpa perlu memuat ulang (*restart*) aplikasi.
- **🛡️ Manajemen Perizinan Pintar:** Adaptasi otomatis gerbang keamanan Android dari API legacy hingga Android 11+ (*Scoped Storage / All Files Access Permission*).
- **🕹️ Kloning & Cadangan ID Konami:** Sistem otomasi pencadangan data `.dat` akun klon secara aman, terisolasi, dan terorganisir di ruang penyimpanan lokal.
- **⚡ Expandable Floating Action Button (FAB):** Desain tombol melayang interaktif yang dapat mekar secara halus (*Slide & Fade In from Left*) lengkap dengan banner teks info berbasis komponen `MaterialCardView`.
- **🔍 Fitur Pencarian Real-Time (TextWatcher Delay):** Penyaringan data akun super cepat dengan sistem pencegahan distorsi UI (*Anti-Distract Satpam Logic*) dan penanganan status kosong (*Empty State Skeleton Loading*).
- **🔄 Pembaruan Multi-Kategori:** Pengecekan otomatis berkala yang memisahkan pembaruan sistem aplikasi inti (`APK`) dengan berkas modifikasi permainan (`Patch Asset .tar`).
---
## 📂 Arsitektur Repositori
Repositori ini difungsikan sebagai server pusat data (*Data Core*) pengatur lalu lintas distribusi berkas pembaruan aplikasi. Distribusi dibagi menjadi dua jalur utama demi stabilitas sistem:
```bash
├── .data/
│   └── update.json          # ⚙️ File konfigurasi pusat (Versi & Tautan Unduhan)
├── update.html              # 🌐 Halaman web cadangan informasi versi raw
└── README.md                # 📖 Dokumentasi utama proyek
```
### 🛰️ Sistem Distribusi Berkas
 1. **GitHub Repository (/.data/update.json)**: Berperan sebagai pusat skema konfigurasi (*Source of Truth*). Aplikasi klien akan melakukan *polling* data JSON murni dari repositori ini melalui tautan raw.githubusercontent.com.
 2. **GitHub Releases (/releases)**: Berperan sebagai CDN (*Content Delivery Network*) utama untuk menyimpan biner aplikasi (.apk) dan kompresi aset permainan (.tar). Tautan unduhan langsung (*Direct Link*) rilis ini akan dicatat di dalam berkas konfigurasi JSON.
## ⚙️ Spesifikasi & Struktur Data Konfigurasi
Berkas update.json harus mengikuti struktur array objek murni agar dapat diproses oleh *Engine GSON Parse* pada aplikasi Android tanpa memicu kesalahan sintaksis (*JsonSyntaxException*).
### 🛠️ Skema JSON Murni (/.data/update.json)
```json
[
  {
    "ver_apk": "2.0",
    "ver_patch": "1.3",
    "url_apk": "[https://github.com/rmdz-commits/ePatch-Manager/releases/download/v2.0/ePatch_Manager_v2.0.apk](https://github.com/rmdz-commits/ePatch-Manager/releases/download/v2.0/ePatch_Manager_v2.0.apk)",
    "url_patch": "[https://github.com/rmdz-commits/ePatch-Manager/releases/download/v1.3/patch_efootball_v1.3.tar](https://github.com/rmdz-commits/ePatch-Manager/releases/download/v1.3/patch_efootball_v1.3.tar)"
  }
]
```
### 📋 Dokumentasi Properti JSON

| Properti | Tipe Data | Deskripsi | Aturan Rilis |
| :--- | :--- | :--- | :--- |
| ver_apk | String | Versi aplikasi Android ePatch Manager saat ini. | Naikkan nilai jika ada rilis APK baru di GitHub Releases. |
| ver_patch | String | Versi berkas aset data patch eFootball™ terbaru. | Naikkan nilai jika ada pembaruan kompresi .tar yang baru. |
| url_apk | String | URL unduhan biner aplikasi (.apk). | Harus mengarah ke berkas rilis final di GitHub Releases. |
| url_patch | String | URL unduhan kompresi aset modifikasi (.tar). | Harus mengarah ke asset rilis tag di GitHub Releases. |

## 🔄 Alur Kerja Sistem Pembaruan (Update Workflow)
Aplikasi melakukan validasi hierarkis dua arah untuk memastikan efisiensi pembaruan data penyimpanan perangkat klien:
```text
[Mulai Aplikasi]
       │
       ▼
[Tarik data raw update.json dari GitHub]
       │
       ▼
 Cek Kesamaan Versi APK Lokal vs Server
       ├── (Beda Versi) ──► Set UPDATE_TYPE = "apk" ──► Munculkan Pop-up Update APK
       └── (Sama Versi)
                 │
                 ▼
          Cek Kesamaan Versi Patch Lokal vs Server
                 ├── (Beda Versi) ──► Set UPDATE_TYPE = "patch" ──► Munculkan Pop-up Update Patch
                 └── (Sama Versi) ──► Selesai (Masuk Halaman Utama / Main Grid)
```
## 🏗️ Panduan Deployment Rilis Baru
Setiap kali Anda ingin merilis pembaruan komponen (baik APK maupun Patch), ikuti urutan langkah standar profesional berikut agar sistem pembaruan otomatis di sisi pengguna tidak mengalami kegagalan:
 1. **Unggah Berkas ke Rilis Baru:**
   * Masuk ke tab **Releases** di samping kanan repositori GitHub Anda.
   * Tekan **Draft a new release**, buat tag versi baru (misal v2.0 atau v1.3).
   * Seret berkas .apk baru atau berkas .tar patch Anda ke kotak aset unggahan, tunggu hingga selesai lalu publikasikan rilis.
 2. **Salin Tautan Unduhan Langsung:**
   * Klik kanan pada berkas yang berhasil diunggah di dalam halaman rilis tersebut, lalu pilih **Copy Link Address**.
 3. **Perbarui Konfigurasi Global Repositori:**
   * Buka berkas /.data/update.json di repositori utama Anda.
   * Edit nilai properti versi (ver_apk / ver_patch) ke versi terbaru, dan tempelkan tautan yang sudah disalin tadi ke properti URL berkaitan (url_apk / url_patch).
   * Simpan dan lakukan *commit* perubahan ke *main branch*. Aplikasi pengguna secara instan akan mendeteksi perubahan ini saat dibuka kembali.
## 🛠️ Persyaratan Lingkungan Pengembangan (Development Environment)
 * **Bahasa Utama:** Java (Android SDK level 23 hingga 34)
 * **Komponen UI:** Material Design Components 1.4.0+, AndroidX CardView, CoordinatorLayout.
 * **Mesin Parsing:** Google Gson Library v2.10.1+
 * **Konektivitas Klien:** OkHttp3 Client Web Core System
## 🤝 Kontribusi & Kredit
Proyek ini dibangun dan dikembangkan secara aktif untuk menciptakan ekosistem manajemen utilitas data klon aplikasi Android yang aman dan efisien.
 * **Developer Utama:** Muhammad Sheva Raditya / ShvWeb
 * **Teknologi Penunjang:** Mochi Cloner System, Android Material UI Framework, GitHub Data Distribution System.
### 📄 Lisensi
Hak Cipta © 2026 **ShvWeb**. Proyek ini dilindungi di bawah lisensi resmi **MIT License** — Lihat berkas LICENSE untuk informasi hak penggunaan lebih lanjut.
```
```
