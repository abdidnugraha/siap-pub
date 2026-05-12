# 🚀 SIAP — Roadmap Pengembangan

Dokumen ini merangkum rencana pengembangan lanjutan sistem SIAP. Setiap item roadmap disertai konteks teknis, manfaat, dan estimasi kompleksitas implementasi.

---

## Status Saat Ini (v1.0 — Stable)

Fitur yang telah tersedia dan berfungsi penuh:

- ✅ Anonymous WBS (Whistleblowing System)
- ✅ Suggestion & Aspirasi Board (3 mode privasi)
- ✅ Mading Aspirasi publik
- ✅ Real-Time Anonymous Chat (AJAX Polling)
- ✅ Tracking Laporan via Ticket ID + Secret PIN
- ✅ RBAC (Super Admin, Operator WBS, Operator Aspirasi)
- ✅ Category-Based Access Control (per-operator)
- ✅ Export Excel (WBS & Aspirasi) dengan filter dinamis
- ✅ Audit Trail System
- ✅ Dynamic Identity System (logo, warna, nama, dll)
- ✅ Secure File Upload (kompresi GD Library, MIME validation)
- ✅ Responsive UI (Mobile + Desktop)
- ✅ Halaman 404 Modern

---

## Roadmap v1.1 — Peningkatan UX & Notifikasi

### 1. 📧 Email Notification System

**Deskripsi:**
Sistem notifikasi email otomatis yang memberitahu pelapor dan investigator tentang pembaruan status laporan.

**Skenario Notifikasi:**
- Pelapor menerima email konfirmasi setelah submit laporan WBS berisi Ticket ID & PIN (sebagai backup dari halaman sukses).
- Pelapor menerima notifikasi saat status laporan berubah (Pending → Processed → Resolved).
- Investigator menerima notifikasi saat ada laporan masuk ke kategori yang mereka tangani.
- Pelapor & Investigator menerima notifikasi saat ada pesan baru di chat (opsional, dapat dinonaktifkan).

**Teknologi yang Direncanakan:**
- **SMTP via PHPMailer** atau **CodeIgniter 4 Email Library**
- Template email HTML yang sesuai dengan branding aplikasi
- Konfigurasi SMTP dari panel admin (host, port, username, password)
- Queue system untuk menghindari delay saat submit (opsional)

**Kompleksitas:** 🟡 Sedang
**Estimasi:** 2–3 hari pengembangan

---

### 2. 🔔 Push Notification (In-App)

**Deskripsi:**
Notifikasi real-time di dalam browser untuk menggantikan polling 3 detik yang saat ini digunakan.

**Teknologi yang Direncanakan:**
- **Server-Sent Events (SSE)** atau **WebSocket** (dengan library Ratchet untuk PHP)
- Untuk kasus sederhana: meningkatkan polling interval menjadi lebih akurat
- Browser Notification API untuk notifikasi di luar tab

**Kompleksitas:** 🔴 Tinggi
**Estimasi:** 1–2 minggu pengembangan

---

## Roadmap v1.2 — Mobile & Aksesibilitas

### 3. 📱 Progressive Web App (PWA)

**Deskripsi:**
Mengubah SIAP menjadi Progressive Web App agar dapat diinstal di perangkat mobile seperti aplikasi native, dengan kemampuan akses offline untuk beberapa fitur.

**Fitur PWA yang Direncanakan:**
- **Web App Manifest** (`manifest.json`) — Nama, ikon, warna tema, orientasi layar
- **Service Worker** — Cache halaman utama dan aset statis untuk akses offline
- **Install Prompt** — Banner "Tambahkan ke Layar Utama" di browser mobile
- **Offline Page** — Halaman fallback saat tidak ada koneksi internet
- **App-like experience** — Layar penuh tanpa address bar, splash screen

**Implementasi:**
```json
// manifest.json
{
  "name": "SIAP — Sistem Aspirasi & Pengaduan",
  "short_name": "SIAP",
  "start_url": "/",
  "display": "standalone",
  "theme_color": "#4F46E5",
  "background_color": "#ffffff",
  "icons": [...]
}
```

**Kompleksitas:** 🟡 Sedang
**Estimasi:** 3–5 hari pengembangan

---

## Roadmap v1.3 — Ekspor & Dokumentasi

### 4. 📄 PDF Export

**Deskripsi:**
Kemampuan mengekspor laporan dalam format PDF, termasuk laporan individu (detail satu tiket) dan laporan rekap berkala.

**Skenario Penggunaan:**
- Export PDF satu laporan WBS lengkap (termasuk histori status dan lampiran thumbnail)
- Export PDF rekap bulanan/tahunan untuk laporan manajemen
- Export PDF detail aspirasi untuk dokumentasi rapat

**Teknologi yang Direncanakan:**
- **DomPDF** (library PHP — tidak memerlukan ekstensi tambahan)
- **wkhtmltopdf** (lebih powerful, mendukung CSS kompleks)
- Template PDF dengan header institusi dari Dynamic Identity System

**Kompleksitas:** 🟡 Sedang
**Estimasi:** 3–5 hari pengembangan

---

## Roadmap v2.0 — AI & Analitik

### 5. 🤖 AI Sentiment Analysis

**Deskripsi:**
Analisis sentimen otomatis terhadap laporan aspirasi yang masuk untuk membantu operator memprioritaskan penanganan berdasarkan urgensi dan emosi konten.

**Fitur yang Direncanakan:**
- **Auto-tagging** — Mengategorikan aspirasi berdasarkan konten secara otomatis (infrastruktur, pelayanan, akademik, dll)
- **Sentiment Score** — Label sentimen: Positif / Netral / Negatif / Urgen
- **Priority Flag** — Laporan dengan sentimen sangat negatif atau kata kunci urgensi otomatis mendapat flag prioritas
- **Dashboard Analytics** — Grafik tren sentimen per kategori dan per periode
- **Word Cloud** — Visualisasi kata-kata yang paling sering muncul

**Teknologi yang Direncanakan:**
- Integrasi **Google Natural Language API** atau **OpenAI API** via `curl`/`GuzzleHTTP`
- Atau model lokal menggunakan **Python** (Flask microservice) yang dipanggil dari PHP
- Library NLP bahasa Indonesia: **IndoBERT** atau **Sastrawi**

**Kompleksitas:** 🔴 Tinggi
**Estimasi:** 2–4 minggu pengembangan

---

## Roadmap v2.1 — Skalabilitas

### 6. 🏫 Multi-Campus / Multi-Instansi

**Deskripsi:**
Arsitektur multi-tenant yang memungkinkan satu instalasi SIAP melayani banyak institusi (kampus, perusahaan, instansi pemerintah) secara terisolasi.

**Model Arsitektur yang Dipertimbangkan:**

**Option A — Database per Tenant:**
- Setiap instansi memiliki database terpisah
- Isolasi data sempurna, keamanan tertinggi
- Kompleksitas manajemen lebih tinggi

**Option B — Shared Database dengan `institution_id`:**
- Satu database dengan kolom `institution_id` di setiap tabel
- Lebih efisien dari sisi resource
- Seluruh query harus selalu menyertakan filter `institution_id`

**Fitur yang Direncanakan:**
- **Super-Superadmin** — Role baru di atas Superadmin untuk mengelola semua instansi
- **Subdomain Routing** — `kampus-a.siap.id`, `perusahaan-b.siap.id`
- **Per-instansi Identity** — Setiap instansi memiliki logo, warna, dan nama sendiri
- **Isolated Data** — Laporan instansi A tidak dapat dilihat oleh admin instansi B

**Kompleksitas:** 🔴 Sangat Tinggi
**Estimasi:** 1–3 bulan pengembangan

---

## Roadmap v2.2 — Dashboard & Analitik Lanjutan

### 7. 📊 Analytics Dashboard

**Deskripsi:**
Dashboard analitik terpusat dengan visualisasi data interaktif untuk manajemen tingkat atas.

**Fitur yang Direncanakan:**
- Grafik tren laporan per bulan (bar chart / line chart)
- Distribusi laporan per kategori (pie chart / donut chart)
- Rata-rata waktu penyelesaian laporan
- Heatmap aktivitas laporan
- Leaderboard kategori dengan laporan terbanyak
- Ekspor dashboard sebagai PDF atau gambar

**Teknologi yang Direncanakan:**
- **Chart.js** (ringan, berbasis JavaScript, tidak memerlukan backend tambahan)
- **ApexCharts** (lebih banyak pilihan chart interaktif)
- Data diambil via AJAX dari endpoint API baru

**Kompleksitas:** 🟡 Sedang
**Estimasi:** 1–2 minggu pengembangan

---

## Ringkasan Prioritas

| # | Fitur | Versi | Prioritas | Kompleksitas |
|:---:|:---|:---:|:---:|:---:|
| 1 | Email Notification | v1.1 | ⭐⭐⭐⭐⭐ | 🟡 Sedang |
| 2 | PDF Export | v1.3 | ⭐⭐⭐⭐ | 🟡 Sedang |
| 3 | Progressive Web App | v1.2 | ⭐⭐⭐⭐ | 🟡 Sedang |
| 4 | Analytics Dashboard | v2.2 | ⭐⭐⭐ | 🟡 Sedang |
| 5 | Push Notification | v1.1 | ⭐⭐⭐ | 🔴 Tinggi |
| 6 | AI Sentiment Analysis | v2.0 | ⭐⭐⭐ | 🔴 Tinggi |
| 7 | Multi-Campus | v2.1 | ⭐⭐ | 🔴 Sangat Tinggi |

---

## Kontribusi & Umpan Balik

Usulan fitur baru atau perubahan prioritas dapat disampaikan melalui:
- **Issue Tracker** pada repository proyek
- **Mading Aspirasi** sistem (jika sudah live 😄)

---

*Terakhir diperbarui: Mei 2026*
