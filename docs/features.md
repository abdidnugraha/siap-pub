# ✨ SIAP — Daftar Fitur Lengkap

Dokumen ini mendaftar seluruh fitur yang tersedia pada sistem SIAP beserta deskripsi teknisnya.

---

## 1. 🔒 Anonymous WBS (Whistleblowing System)

Fitur inti SIAP yang memungkinkan siapapun melaporkan pelanggaran serius secara anonim, terstruktur, dan aman.

### Detail Fitur:
- **Zero Registration** — Pelapor tidak perlu membuat akun.
- **Ticket ID** — Setiap laporan menghasilkan ID unik berformat `WBS-YYYY-XXXXX` secara otomatis.
- **Secret PIN** — PIN 6-digit acak di-hash menggunakan **Bcrypt** (`password_hash()`). PIN plaintext hanya tampil sekali saat sukses.
- **Kategori Pengaduan** — Laporan dapat dikategorisasi agar terarah ke operator yang tepat.
- **Upload Lampiran Bukti** — Mendukung format JPG, PNG, dan PDF (maks 2MB). Gambar dikompres otomatis via **GD Library**.
- **Link Eksternal** — Alternatif lampiran via URL (Google Drive, YouTube, dll.)
- **Drag & Drop Upload** — Antarmuka upload modern dengan preview gambar sebelum submit.
- **Halaman Sukses** — Menampilkan Ticket ID & PIN sekali dengan opsi cetak sebagai PDF via `window.print()`.

**Files:** `WBS/Controllers/WbsPublic.php`, `WBS/Views/public/form.php`, `WBS/Views/public/success.php`

---

## 2. 💡 Suggestion & Aspirasi Board

Modul penyampaian aspirasi, kritik, dan saran dengan berbagai mode privasi.

### Detail Fitur:
- **3 Mode Privasi:** `Anonymous`, `Confidential`, `Public`
- **Lampiran Fleksibel** — Upload file atau link eksternal.
- **Mading Aspirasi** — Papan publik yang menampilkan aspirasi `reviewed`/`implemented` dengan mode `public`.
  - Card grid 3 kolom (responsif ke 1 kolom di mobile)
  - Filter kategori via floating button + modal
  - Paginasi dinamis
- **Modal Detail** — Klik card untuk melihat isi lengkap tanpa pindah halaman.

**Files:** `Suggestion/Controllers/SuggestionPublic.php`, `Suggestion/Views/public/`

---

## 3. 📊 Export Excel (Reporting System)

Fitur ekspor data untuk keperluan administrasi dan arsip institusi.

### Detail Fitur:
- **Export WBS & Aspirasi** — Kolom: Ticket ID, Kategori, Judul, Deskripsi, Status, Tanggal.
- **Filter Dinamis** — Berdasarkan kategori, status, rentang tanggal, dan tipe laporan.
- **Format Profesional** via **PhpSpreadsheet**:
  - Judul & tanggal generate otomatis
  - Header row berwarna (Indigo `#4F46E5`, teks putih)
  - Border tabel, text wrap, auto-resize kolom
  - Ticket ID diformat teks (mencegah scientific notation)
- **Download Langsung** — File `.xlsx` langsung diunduh, tidak disimpan di server.

**Files:** `Report/Controllers/Report.php`

---

## 4. 💬 Real-Time Anonymous Chat

Komunikasi dua arah antara pelapor anonim dan investigator.

### Detail Fitur:
- **AJAX Polling** — Pesan diambil setiap 3 detik via `setInterval` + `fetch()`.
- **Dual Identity:** `sender_type = 'anonymous'` (pelapor) dan `sender_type = 'investigator'` (operator).
- **Bubble Chat UI** — Gelembung kiri/kanan sesuai pengirim.
- **CSRF Injection Dinamis** — Token CSRF di-inject dari PHP ke JavaScript.
- **Auto Scroll** — Chat otomatis scroll ke bawah saat pesan baru masuk.
- **Tersedia di:** Halaman Tracking pelapor & Halaman admin detail laporan WBS.
- **Word Break Protection** — Teks panjang tanpa spasi tidak merusak layout.

**Files:** `Tracking/Views/view.php`, `WBS/Views/admin/view.php`, `WBS/Controllers/WbsAdmin.php`

---

## 5. 👑 RBAC (Role-Based Access Control)

Sistem hak akses berlapis untuk menjaga keamanan dan kerahasiaan data.

### Struktur Role:

| Role | ID | Deskripsi |
|:---|:---:|:---|
| **Super Admin** | 1 | Akses penuh ke seluruh sistem |
| **Operator** | 2 | Akses terbatas sesuai konfigurasi |

### Hak Akses:

| Fitur | Super Admin | Operator WBS | Operator Aspirasi |
|:---|:---:|:---:|:---:|
| Lihat semua laporan WBS | ✅ | ❌ (per kategori) | ❌ |
| Lihat semua aspirasi | ✅ | ❌ | ✅ |
| Update status WBS | ✅ | ✅ (per kategori) | ❌ |
| Update status Aspirasi | ✅ | ❌ | ✅ |
| Manajemen User | ✅ | ❌ | ❌ |
| Manajemen Kategori | ✅ | ❌ | ❌ |
| Pengaturan Sistem | ✅ | ❌ | ❌ |
| Export Excel | ✅ | ❌ | ❌ |
| Audit Trail | ✅ | ❌ | ❌ |

### Implementasi Teknis:
- **AuthFilter** — CI4 Filter memeriksa session `is_logged_in`.
- **Pivot Table** `operator_categories` — Relasi M:N antara user dan kategori WBS.
- **IDOR Prevention** — Operator tidak dapat mengakses laporan di luar kategorinya walau mengetahui ID-nya.

**Files:** `Filters/AuthFilter.php`, `WBS/Controllers/WbsAdmin.php`, `Settings/Controllers/UserAdmin.php`

---

## 6. 📜 Audit Trail

Pencatatan aktivitas otomatis untuk akuntabilitas dan forensik digital.

### Detail Fitur:
- **Helper `add_audit()`** — Dipanggil di setiap aksi penting.
- **Data Tercatat:** `user_id`, `action`, `table_name`, `record_id`, `created_at`.
- **Tampilan Dashboard** — 50 entri terbaru dengan filter: User, Action keyword, Tabel, Rentang Tanggal.
- **Aktivitas yang Tercatat:**
  - Login berhasil
  - Submit & update laporan (WBS & Aspirasi)
  - Pengiriman pesan chat
  - Tambah/Edit/Hapus User & Kategori
  - Update konfigurasi sistem
  - Download lampiran & Export Excel

**Files:** `Helpers/audit_helper.php`, `Dashboard/Controllers/Dashboard.php`

---

## 7. 🎨 Dynamic Identity System

Identitas visual dan teks aplikasi dapat dikustomisasi dari panel admin tanpa menyentuh source code.

### Konfigurasi yang Dapat Diubah:

| Setting | Deskripsi |
|:---|:---|
| `app_name` | Nama singkat aplikasi |
| `app_fullname` | Nama lengkap aplikasi |
| `app_tagline` | Tagline/slogan |
| `app_logo` | File logo (JPG/PNG, maks 2MB) |
| `app_favicon` | File favicon (ICO/PNG) — otomatis disalin ke `/public/favicon.ico` |
| `primary_color` | Warna utama UI (HEX, dinamis via CSS variable `--color-cyan`) |
| `footer_text` | Teks footer institusi |
| `campus_name` | Nama institusi/kampus |
| `system_email` | Email sistem |

**Caching:** Setting di-cache 24 jam (`86400 detik`). Cache dihapus otomatis saat setting diubah.

---

*Terakhir diperbarui: Mei 2026*
