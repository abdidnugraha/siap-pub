# 🧪 SIAP — Testing Documentation

Dokumen ini berisi panduan pengujian sistem SIAP, mencakup **Black-box Testing Checklist**, **Browser Compatibility Testing**, dan **Responsiveness Testing**.

---

## 1. Black-box Testing Checklist

Pengujian fungsional dari perspektif pengguna tanpa melihat source code.

### 1.1 Modul Autentikasi

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 1 | Login dengan NIK & password benar | NIK valid, password benar | Redirect ke `/dashboard`, session aktif | ☐ |
| 2 | Login dengan password salah | NIK valid, password salah | Pesan error "NIK atau password salah" | ☐ |
| 3 | Login dengan NIK tidak terdaftar | NIK tidak ada di DB | Pesan error | ☐ |
| 4 | Akses halaman admin tanpa login | URL `/dashboard` langsung | Redirect ke `/login` | ☐ |
| 5 | Logout | Klik logout | Session dihapus, redirect ke `/login` | ☐ |
| 6 | Akses dashboard setelah logout | URL `/dashboard` | Redirect ke `/login` | ☐ |

### 1.2 Modul WBS — Submit Laporan

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 7 | Submit laporan lengkap tanpa lampiran | Semua field wajib diisi | Halaman sukses + Ticket ID + Secret PIN | ☐ |
| 8 | Submit laporan dengan lampiran gambar | JPG valid < 2MB | Laporan tersimpan, file terkompresi | ☐ |
| 9 | Submit laporan dengan lampiran PDF | PDF valid < 2MB | Laporan tersimpan, file tersalin | ☐ |
| 10 | Submit laporan dengan file > 2MB | File 3MB | Error "ukuran melebihi batas 2MB" | ☐ |
| 11 | Submit laporan dengan tipe file terlarang | File `.exe` | Error "format tidak didukung" | ☐ |
| 12 | Submit laporan dengan link eksternal valid | URL valid | Laporan tersimpan dengan link | ☐ |
| 13 | Submit laporan dengan link tidak valid | Teks bukan URL | Error validasi URL | ☐ |
| 14 | Submit form kosong | Tidak ada input | Semua error validasi muncul | ☐ |
| 15 | Submit laporan kategori kosong | Tanpa pilih kategori | Error "kategori wajib diisi" | ☐ |

### 1.3 Modul WBS — Tracking

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 16 | Tracking dengan Ticket ID & PIN benar | Data valid | Tampil halaman status laporan | ☐ |
| 17 | Tracking dengan PIN salah | Ticket ID benar, PIN salah | Pesan error, tidak ada akses | ☐ |
| 18 | Tracking dengan Ticket ID tidak ada | ID sembarangan | Pesan error | ☐ |
| 19 | Kirim pesan dari halaman tracking | Pesan non-kosong | Pesan muncul di bubble chat | ☐ |
| 20 | Polling chat berfungsi | Investigator kirim pesan dari admin | Pesan muncul di sisi pelapor dalam 3 detik | ☐ |
| 21 | Logout tracking | Klik keluar | Sesi tracking dihapus, redirect ke halaman track | ☐ |

### 1.4 Modul Aspirasi — Submit

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 22 | Submit aspirasi mode Anonymous | Pilih Anonymous | Tidak ada form identitas, aspirasi tersimpan | ☐ |
| 23 | Submit aspirasi mode Confidential | Pilih Confidential | Form identitas muncul, wajib diisi | ☐ |
| 24 | Submit aspirasi mode Public | Pilih Public | Form identitas muncul | ☐ |
| 25 | Submit dengan deskripsi < 10 karakter | Deskripsi sangat pendek | Error validasi | ☐ |

### 1.5 Mading Aspirasi

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 26 | Aspirasi muncul di Mading | Status: reviewed/implemented, mode: public | Card tampil di grid | ☐ |
| 27 | Aspirasi tidak muncul di Mading | Status: pending atau mode: anonymous | Tidak tampil di Mading | ☐ |
| 28 | Filter kategori berfungsi | Pilih kategori tertentu | Hanya aspirasi kategori tersebut tampil | ☐ |
| 29 | Modal detail aspirasi terbuka | Klik card aspirasi | Detail lengkap tampil di modal | ☐ |
| 30 | Paginasi berfungsi | Lebih dari 10 aspirasi | Tombol paginasi muncul dan berfungsi | ☐ |

### 1.6 Panel Admin — WBS

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 31 | Filter laporan berdasarkan kategori | Pilih kategori di dropdown | Hanya laporan kategori tersebut tampil | ☐ |
| 32 | Filter laporan berdasarkan status | Pilih status | Laporan terfilter | ☐ |
| 33 | Superadmin: search by Ticket ID | Input sebagian Ticket ID | Laporan yang cocok tampil | ☐ |
| 34 | Operator akses laporan kategori sendiri | Login sebagai operator | Hanya laporan kategorinya tampil | ☐ |
| 35 | Operator coba akses laporan kategori lain | URL manipulasi ID | Ditolak, redirect + pesan error | ☐ |
| 36 | Update status laporan | Ubah status menjadi "processed" | Status berubah, audit trail tercatat | ☐ |
| 37 | Download lampiran valid | Klik download | File terunduh dengan nama asli | ☐ |
| 38 | Investigator kirim pesan chat | Tulis pesan di panel admin | Pesan muncul di chat | ☐ |

### 1.7 Panel Admin — Aspirasi

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 39 | Update status aspirasi ke "reviewed" | Ubah status | Status berubah, aspirasi tidak muncul di Mading dulu | ☐ |
| 40 | Update status aspirasi ke "implemented" + mode public | Ubah ke implemented | Aspirasi muncul di Mading publik | ☐ |
| 41 | Filter berdasarkan privasi | Pilih filter privasi | Aspirasi terfilter | ☐ |

### 1.8 Panel Admin — Manajemen

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 42 | Tambah user baru | Data user valid | User tersimpan, audit trail tercatat | ☐ |
| 43 | Tambah user dengan NIK duplikat | NIK yang sudah ada | Error "NIK sudah terdaftar" | ☐ |
| 44 | Edit user: assign akses kategori WBS | Centang kategori | Akses tersimpan di `operator_categories` | ☐ |
| 45 | Hapus user sendiri | Login sebagai superadmin, hapus diri | Error "tidak dapat hapus akun sendiri" | ☐ |
| 46 | Tambah kategori baru | Nama & tipe valid | Kategori tersimpan, muncul di dropdown A-Z | ☐ |
| 47 | Hapus kategori yang masih digunakan | Hapus kategori dengan laporan | Error Foreign Key Constraint | ☐ |
| 48 | Update pengaturan sistem | Ubah nama aplikasi | Nama berubah di semua halaman, cache ter-reset | ☐ |
| 49 | Upload logo baru | File JPG < 2MB | Logo berubah di layout | ☐ |
| 50 | Ubah primary color | HEX color baru | Warna UI berubah di semua halaman | ☐ |

### 1.9 Reporting

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 51 | Export WBS tanpa filter | Klik export | File `.xlsx` terunduh dengan semua data | ☐ |
| 52 | Export WBS dengan filter tanggal | Tentukan rentang tanggal | Hanya data dalam rentang tersebut | ☐ |
| 53 | Export Aspirasi | Klik export Aspirasi | File `.xlsx` terunduh | ☐ |

### 1.10 Audit Trail

| # | Test Case | Input | Expected Output | Status |
|:---:|:---|:---|:---|:---:|
| 54 | Login tercatat di audit trail | Login berhasil | Entri "Login Berhasil" muncul di log | ☐ |
| 55 | Update status tercatat | Update status laporan | Entri update muncul di log | ☐ |
| 56 | Filter audit log berdasarkan user | Pilih user tertentu | Hanya log user tersebut tampil | ☐ |

---

## 2. Browser Compatibility Testing

Pengujian kompatibilitas tampilan dan fungsionalitas di berbagai browser.

### Daftar Browser yang Diuji:

| Browser | Versi | Desktop | Mobile | Status |
|:---|:---|:---:|:---:|:---:|
| Google Chrome | Latest | ☐ | ☐ | |
| Mozilla Firefox | Latest | ☐ | ☐ | |
| Microsoft Edge | Latest | ☐ | ☐ | |
| Safari | Latest | ☐ | ☐ | |
| Opera | Latest | ☐ | — | |

### Checklist Per Browser:

| # | Test Item | Keterangan |
|:---:|:---|:---|
| 1 | Landing page render sempurna | Grid 2x2 mobile, grid 2 kolom desktop |
| 2 | Animasi CSS berjalan | Floating animation, hover effects |
| 3 | Modal info (i) berfungsi | Click trigger + dismiss |
| 4 | Glassmorphism terlihat | `backdrop-filter: blur()` support |
| 5 | Font Outfit termuat | Google Fonts tidak diblokir |
| 6 | Form validation muncul | Native & custom error messages |
| 7 | Drag & Drop upload berfungsi | File preview muncul |
| 8 | Real-time chat polling berjalan | Pesan muncul tanpa refresh |
| 9 | Accordion tracking mobile | Buka/tutup dengan animasi |
| 10 | CSS Variable `var()` berfungsi | Primary color dinamis |
| 11 | `fetch()` API berjalan | AJAX request sukses |
| 12 | `window.print()` berfungsi | Halaman sukses WBS |

> **Catatan:** Fitur `backdrop-filter` tidak didukung sepenuhnya di Firefox versi lama. Sistem sudah menyediakan fallback dengan background solid.

---

## 3. Responsiveness Testing

Pengujian tampilan di berbagai ukuran layar menggunakan Chrome DevTools Device Emulator.

### Device Breakpoints:

| Device | Resolusi | Tipe |
|:---|:---|:---|
| iPhone SE | 375 × 667 | Mobile S |
| iPhone 12 Pro | 390 × 844 | Mobile M |
| Samsung Galaxy S20 | 412 × 915 | Mobile L |
| iPad | 768 × 1024 | Tablet |
| iPad Pro | 1024 × 1366 | Tablet L |
| Laptop | 1366 × 768 | Desktop S |
| Desktop HD | 1920 × 1080 | Desktop L |

### Checklist Responsiveness:

#### Landing Page
| # | Test Item | Mobile | Tablet | Desktop |
|:---:|:---|:---:|:---:|:---:|
| 1 | Logo & nama app proporsional | ☐ | ☐ | ☐ |
| 2 | Grid menu 2×2 (mobile) / 2 kolom (desktop) | ☐ | ☐ | ☐ |
| 3 | Lapor & Lacak card full-width | ☐ | ☐ | ☐ |
| 4 | Tidak ada horizontal scroll | ☐ | ☐ | ☐ |
| 5 | Zero-scroll pada mobile (semua konten visible) | ☐ | — | — |
| 6 | Modal deskripsi (i) berfungsi | ☐ | ☐ | — |
| 7 | Footer terlihat tanpa scroll berlebihan | ☐ | ☐ | ☐ |

#### Form WBS & Aspirasi
| # | Test Item | Mobile | Tablet | Desktop |
|:---:|:---|:---:|:---:|:---:|
| 8 | Form input tidak terpotong | ☐ | ☐ | ☐ |
| 9 | Drop zone upload terlihat jelas | ☐ | ☐ | ☐ |
| 10 | Tombol submit full-width di mobile | ☐ | — | — |

#### Halaman Tracking
| # | Test Item | Mobile | Tablet | Desktop |
|:---:|:---|:---:|:---:|:---:|
| 11 | Accordion detail laporan (mobile) | ☐ | — | — |
| 12 | Grid 1 kolom di mobile | ☐ | — | — |
| 13 | Grid 2 kolom di desktop | — | — | ☐ |
| 14 | Chat box height proporsional | ☐ | ☐ | ☐ |
| 15 | Teks panjang tidak merusak layout | ☐ | ☐ | ☐ |

#### Panel Admin
| # | Test Item | Mobile | Tablet | Desktop |
|:---:|:---|:---:|:---:|:---:|
| 16 | Sidebar tersembunyi di mobile | ☐ | — | — |
| 17 | Topbar dengan toggle menu mobile | ☐ | — | — |
| 18 | Tabel dengan horizontal scroll | ☐ | ☐ | ☐ |
| 19 | Admin view grid 1 kolom mobile | ☐ | — | — |
| 20 | Zero-scroll admin view (desktop) | — | — | ☐ |

#### Mading Aspirasi
| # | Test Item | Mobile | Tablet | Desktop |
|:---:|:---|:---:|:---:|:---:|
| 21 | Grid 1 kolom di mobile | ☐ | — | — |
| 22 | Grid 3 kolom di desktop | — | — | ☐ |
| 23 | Floating filter button terlihat | ☐ | ☐ | ☐ |
| 24 | Modal filter dapat ditutup | ☐ | ☐ | ☐ |

---

## 4. Cara Menjalankan Testing

### Setup Lokal:
```bash
# Pastikan aplikasi berjalan di Laragon
# Akses via: http://localhost/siap/public/

# Pastikan database sudah terisi data dummy untuk testing
```

### Tools yang Digunakan:
- **Chrome DevTools** — Untuk responsiveness testing (toggle device toolbar)
- **Browser:** Chrome, Firefox, Edge, Safari
- **Postman** — Untuk testing API endpoint chat (opsional)

### Cara Gunakan Checklist:
- `☐` = Belum diuji
- `✅` = Lulus pengujian
- `❌` = Gagal (catat bug di issue tracker)

---

*Terakhir diperbarui: Mei 2026*
