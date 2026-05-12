# 🚀 SIAP — Sistem Informasi Aspirasi dan Pengaduan


**SIAP** adalah platform digital berbasis web yang dirancang untuk mendukung transparansi, akuntabilitas, dan partisipasi aktif civitas akademika maupun masyarakat. Sistem ini memfasilitasi penyampaian pengaduan pelanggaran (*Whistleblowing System*) serta aspirasi dan saran secara aman, terstruktur, dan bertanggung jawab.

Dibangun menggunakan arsitektur modular berbasis **CodeIgniter 4 HMVC**, SIAP menggabungkan sistem pelaporan anonim, manajemen investigasi terpusat, audit aktivitas, dan antarmuka modern yang responsif dalam satu ekosistem aplikasi yang ringan namun *scalable*.

---

## 🚧 Project Status

Currently under active development and internal testing.

---

## ✨ Tujuan Sistem

SIAP dikembangkan sebagai solusi digital untuk:
*   🛡️ Menyediakan kanal pelaporan yang aman dan terpercaya.
*   📊 Meningkatkan transparansi tata kelola institusi.
*   💬 Memfasilitasi komunikasi dua arah secara anonim.
*   🤝 Mendukung budaya organisasi yang partisipatif.
*   ⚡ Mempercepat proses tindak lanjut laporan dan aspirasi.

---

## 🧱 Arsitektur Sistem

SIAP menggunakan pendekatan **Hierarchical Model View Controller (HMVC)** untuk memisahkan setiap fitur ke dalam modul independen yang mudah dikembangkan, diuji, dan dipelihara.

**Struktur Modul:**

app/Modules/
├── Audit/       # Audit aktivitas sistem
├── Auth/        # Authentication & Session Management
├── Dashboard/   # Statistik & Monitoring
├── Landing/     # Landing Page Publik
├── Report/      # Export & Reporting
├── Settings/    # Konfigurasi Sistem
├── Suggestion/  # Aspirasi & Mading Publik
├── Tracking/    # Pelacakan Pengaduan
└── WBS/         # Whistleblowing System

---

## 🛠️ Technology Stack

| Layer | Teknologi |
| :--- | :--- |
| **Backend** | CodeIgniter 4 (HMVC) |
| **Database** | MySQL |
| **Frontend** | Bootstrap 5 + Custom CSS |
| **JavaScript** | ES6+, Fetch API |
| **UI Design** | *Glassmorphism Interface* |
| **Security** | CSRF, XSS Filtering, IDOR Protection |

---

## 📦 Modul Utama

### 👥 Modul Pelapor (Public Portal)
Dirancang **tanpa proses registrasi** agar tetap mudah diakses sekaligus menjaga kerahasiaan identitas pelapor.

*   **✍️ Pengaduan WBS:** Pelaporan pelanggaran serius secara anonim dengan dukungan *Ticket ID* otomatis, *Secret PIN tracking*, upload lampiran bukti, dan komunikasi anonim dua arah.
*   **💡 Aspirasi & Saran:** Media penyampaian kritik, ide, masukan, maupun keluhan ringan. Mendukung mode privasi: **Public**, **Confidential**, dan **Anonymous**.
*   **📰 Mading Aspirasi:** Papan aspirasi publik yang menampilkan saran terverifikasi dalam tampilan modern berbasis *card/grid* dengan filter kategori dan paginasi dinamis.
*   **🔍 Tracking Pengaduan:** Portal pelacakan laporan menggunakan *Ticket ID* dan *Secret PIN* tanpa memerlukan login akun.
*   **💬 Real-Time Anonymous Chat:** Sistem komunikasi anonim dua arah berbasis AJAX yang memungkinkan interaksi *real-time*, sinkronisasi otomatis, *dynamic CSRF handling*, tanpa perlu *refresh* halaman.

### 🛡️ Investigator Dashboard
Dashboard investigator dirancang untuk efisiensi kerja tinggi dengan pendekatan:
*   *Single-screen workflow*
*   *Quick action status*
*   *Evidence preview*
*   Tata letak responsif yang meminimalisir perpindahan halaman saat investigasi berlangsung.

### 📊 Reporting & Export System
Dilengkapi fitur pelaporan untuk mendukung dokumentasi dan administratif institusi:
*   Export laporan WBS ke format Excel (`.xlsx`).
*   Export data aspirasi dan saran.
*   Filter export dinamis berdasarkan: kategori, status, rentang tanggal, dan tipe laporan.
*   Format tabel otomatis rapi, siap cetak, dan ideal untuk kebutuhan arsip manajemen.

---

## 👑 Manajemen Role & Hak Akses

SIAP menerapkan konsep **Role-Based Access Control (RBAC)** untuk menjaga keamanan dan kerahasiaan data.

*   **Super Admin:** Kontrol penuh terhadap manajemen user, pengaturan sistem, kategori, audit trail, identitas aplikasi, dan monitoring data.
*   **Operator WBS:** Hanya dapat mengakses laporan berdasarkan kategori/divisi yang ditugaskan untuk mencegah konflik kepentingan dan kebocoran informasi lintas unit.
*   **Operator Aspirasi:** Bertugas melakukan moderasi, validasi, dan publikasi aspirasi ke mading publik.

---

## 🔄 Workflow Sistem

**Workflow Pengaduan WBS:**
> `Pelapor` ➔ `Submit Laporan & Bukti` ➔ `Generate Ticket ID & Secret PIN` ➔ `Investigasi oleh Operator` ➔ `Komunikasi Anonim` ➔ `Update Status` ➔ `Penyelesaian Laporan`

**Workflow Aspirasi:**
> `Pengguna Mengirim Aspirasi` ➔ `Moderasi Operator` ➔ `Validasi & Review` ➔ `Publikasi ke Mading`

---

## 🔐 Keamanan & Transparansi

### Security Features
Sistem dirancang dengan pendekatan keamanan berlapis:
*   ✅ CSRF Protection
*   ✅ XSS Filtering
*   ✅ IDOR Protection
*   ✅ Password Hashing (Bcrypt)
*   ✅ Secure File Validation
*   ✅ Audit Trail Logging
*   ✅ Session Protection
*   ✅ Category-Based Authorization

### 📜 Audit Trail System
Seluruh aktivitas penting sistem tercatat secara otomatis untuk meningkatkan transparansi, akuntabilitas, dan keamanan operasional. Log mencakup aktivitas login pengguna, perubahan data, pembaruan status, hingga histori investigasi.

---

## 🎨 Dynamic Identity System

Seluruh identitas aplikasi dapat dikustomisasi langsung melalui panel administrator tanpa menyentuh *source code*, meliputi:
**Nama Aplikasi** | **Tagline** | **Logo** | **Favicon** | **Primary Color** | **Footer Institusi**

---

## 📊 Status Pengaduan

| Status | Deskripsi |
| :--- | :--- |
| 🟡 **Pending** | Laporan baru diterima dan menunggu antrean. |
| 🔵 **Processed** | Laporan sedang ditindaklanjuti oleh investigator. |
| 🟢 **Resolved** | Laporan telah selesai diproses dan ditutup. |
| 🔴 **Rejected** | Laporan ditolak karena tidak valid atau kurang bukti. |

---

## 🚀 Scalability & Future Development

SIAP dirancang *modular* dan *scalable* sehingga memungkinkan pengembangan lanjutan seperti:
*   Notifikasi *Real-time* (Push/Email)
*   Progressive Web App (PWA)
*   Dashboard Analytics Terpusat
*   Arsitektur Multi-instansi
*   Export PDF & Integrasi Email Gateway
*   AI Sentiment Analysis untuk Aspirasi

---

## 🎯 Implementasi Sistem

Sistem ini sangat fleksibel dan siap diterapkan pada berbagai sektor:
🏫 Perguruan Tinggi & Sekolah | 🏛️ Instansi Pemerintah | 🏢 Perusahaan & Yayasan | 🏥 Lembaga Pelayanan Publik

---

## 📌 Developer Notes

SIAP dikembangkan dengan fokus pada keamanan data, kemudahan penggunaan, efisiensi investigasi, dan tata kelola digital yang modern. 

> *"Membangun sistem bukan hanya tentang teknologi, tetapi tentang menciptakan ruang agar suara dapat disampaikan dengan aman dan bertanggung jawab."*

## 📌 Source Code Notice

The complete source code for this project is maintained in a private repository for internal deployment and further development.

This repository is intended for portfolio and showcase purposes only.