# 🔄 SIAP — Alur Kerja Sistem (Workflow)

Dokumen ini menjelaskan alur kerja utama pada sistem SIAP, mencakup proses dari sisi **Pelapor**, **Operator/Investigator**, **Tracking**, dan **Investigasi**.

---

## 1. Alur Pelapor (Reporter Flow)

Pelapor adalah pengguna publik yang **tidak memerlukan akun** untuk menggunakan sistem. Terdapat dua jenis jalur pelaporan:

### 1.1 Pengaduan WBS (Whistleblowing)

```
[Pelapor Mengunjungi Portal]
        ↓
[Membuka Form Pengaduan WBS]
        ↓
[Mengisi Data Laporan]
   - Kategori Pengaduan
   - Judul Laporan
   - Deskripsi Detail (5W1H)
   - Lampiran Bukti (Opsional: Gambar JPG/PNG atau PDF, maks. 2MB)
   - Link Eksternal Bukti (Opsional)
        ↓
[Sistem Validasi Data]
   - Validasi field wajib
   - Validasi tipe & ukuran file
   - Validasi URL (jika ada link)
   - CSRF Token Check
        ↓
[Sistem Memproses & Menyimpan Laporan]
   - Generate Ticket ID unik (format: WBS-YYYY-XXXXX)
   - Generate Secret PIN 6 digit (diacak, di-hash dengan Bcrypt)
   - Simpan laporan ke database dengan status: PENDING
   - Kompres & simpan file lampiran (jika ada)
        ↓
[Halaman Sukses Ditampilkan]
   - Pelapor menerima Ticket ID
   - Pelapor menerima Secret PIN
   - Instruksi: SIMPAN informasi ini sebelum meninggalkan halaman
   - Opsi: Cetak halaman sebagai PDF
        ↓
[SELESAI — Laporan Masuk ke Antrian]
```

**Endpoint:** `POST /wbs/submit`

---

### 1.2 Pengiriman Aspirasi & Saran

```
[Pelapor Mengunjungi Portal Aspirasi]
        ↓
[Mengisi Form Aspirasi]
   - Kategori Aspirasi
   - Mode Privasi:
       • Anonymous  → Identitas disembunyikan sepenuhnya
       • Confidential → Nama & Instansi disimpan, tidak dipublikasikan
       • Public → Identitas tertera di Mading jika dipublikasikan
   - Judul & Deskripsi Aspirasi
   - Lampiran (Opsional: Gambar/PDF) atau Link Eksternal
        ↓
[Sistem Validasi & Simpan]
   - Status default: PENDING
   - Tidak ada Ticket ID pada aspirasi
        ↓
[Aspirasi Masuk ke Dashboard Operator]
```

**Endpoint:** `POST /suggestion/submit`

---

## 2. Alur Tracking (Pelacakan Laporan)

Tracking memungkinkan pelapor WBS memantau status laporannya **tanpa akun**, hanya menggunakan Ticket ID dan Secret PIN.

```
[Pelapor Mengunjungi Halaman Lacak Laporan]
        ↓
[Memasukkan Ticket ID & Secret PIN]
        ↓
[Sistem Verifikasi]
   - Cari laporan berdasarkan Ticket ID
   - Verifikasi PIN menggunakan password_verify() (Bcrypt)
        ↓
[Jika Valid → Sesi Tracking Dimulai]
   - Session: tracked_complaint_id & tracked_ticket_id disimpan
   - Redirect ke halaman view laporan
        ↓
[Halaman Status Laporan]
   - Informasi Detail Laporan (Accordion, mobile-friendly)
   - Status Terkini (Pending / Processed / Resolved / Rejected)
   - Panel Diskusi Anonim (Real-Time Chat)
        ↓
[Pelapor Dapat Mengirim Pesan ke Investigator]
   - Pesan dikirim via AJAX (Fetch API)
   - Chat ter-polling otomatis setiap 3 detik
   - Identitas pelapor tercatat sebagai 'anonymous'
        ↓
[Pelapor Logout → Sesi Tracking Dihapus]
```

**Endpoints:**
- `POST /track/auth` — Verifikasi Ticket ID & PIN
- `GET /track/view` — Halaman status laporan
- `POST /track/send-message` — Kirim pesan anonim
- `GET /track/get-messages` — Polling pesan (AJAX)
- `GET /track/logout` — Hapus sesi tracking

---

## 3. Alur Operator/Investigator

Operator adalah user admin yang bertugas mengelola laporan sesuai hak akses kategorisasi.

### 3.1 Akses & Hak Kategori

```
[Operator Login]
   - Autentikasi menggunakan NIK & Password
   - Session disimpan: user_id, role_id, name, role_name, is_suggestion_operator
        ↓
[Redirect ke Dashboard]
   - Tampil statistik: jumlah laporan pending
   - Hanya laporan dari kategori yang ditugaskan yang muncul
     (berdasarkan tabel pivot: operator_categories)
```

### 3.2 Pengelolaan Laporan WBS

```
[Operator Membuka Daftar Pengaduan WBS]
   - Filter tersedia: Kategori, Status, Ticket ID
   - Daftar diurutkan: terbaru di atas
   - Hanya laporan kategori yang diizinkan yang tampil
        ↓
[Operator Membuka Detail Laporan]
   - Melihat detail lengkap laporan
   - Melihat lampiran bukti (preview gambar / download PDF)
   - Membaca deskripsi penuh (modal jika terpotong)
   - Akses panel diskusi anonim dua arah
        ↓
[Operator Melakukan Tindak Lanjut]
   ├── Kirim pesan ke pelapor (chat anonim)
   │     - Identitas tercatat sebagai 'investigator'
   │     - Pelapor melihat pesan via polling
   │
   └── Update Status Laporan
         - Pending → Processed → Resolved / Rejected
         - Setiap perubahan status dicatat di:
             • complaint_status_logs (riwayat status)
             • audit_logs (audit trail aktivitas)
```

**Endpoints:**
- `GET /wbs/admin` — Daftar laporan
- `GET /wbs/admin/view/{ticket_id}` — Detail laporan
- `POST /wbs/admin/update-status` — Update status
- `POST /wbs/admin/send-message` — Kirim pesan
- `GET /wbs/admin/get-messages/{id}` — Polling pesan

### 3.3 Pengelolaan Aspirasi

```
[Operator Aspirasi Membuka Daftar Aspirasi]
   - Filter: Kategori, Status, Mode Privasi
   - Aspirasi bersifat 'all-access' — tidak ada filter kategori per operator
        ↓
[Operator Mengubah Status Aspirasi]
   - Pending → Reviewed → Implemented / Rejected
   - Hanya aspirasi dengan status 'Reviewed' atau 'Implemented'
     dengan mode 'Public' yang tampil di Mading
        ↓
[Aspirasi Terverifikasi Muncul di Mading Publik]
```

---

## 4. Alur Investigasi (Super Admin)

Super Admin memiliki akses penuh ke seluruh sistem, termasuk manajemen investigator dan audit trail.

```
[Super Admin Login]
        ↓
[Dashboard Admin]
   - Statistik keseluruhan: WBS pending + total aspirasi
   - Audit Trail Log (50 entri terbaru)
   - Filter log: berdasarkan user, aksi, tabel, tanggal
        ↓
[Manajemen Data]
   ├── Manajemen User
   │     - Tambah, Edit, Hapus user
   │     - Assign role: Super Admin / Operator WBS / Operator Aspirasi
   │     - Assign akses kategori untuk Operator WBS
   │
   ├── Manajemen Kategori
   │     - CRUD kategori WBS dan Aspirasi
   │     - Kategori diurutkan A-Z di semua dropdown
   │
   ├── Pengaturan Sistem
   │     - Nama Aplikasi, Tagline, Logo, Favicon
   │     - Primary Color (dinamis)
   │     - Footer Institusi
   │
   └── Export & Reporting
         - Export WBS / Aspirasi ke Excel (.xlsx)
         - Filter: kategori, status, rentang tanggal
         - Format tabel otomatis, siap arsip
```

---

## 5. Ringkasan Status Laporan

| Status | Deskripsi | Siapa yang Mengubah |
|:---|:---|:---|
| 🟡 **Pending** | Laporan baru diterima, belum diproses | Sistem (otomatis) |
| 🔵 **Processed** | Laporan sedang ditindaklanjuti | Operator/Investigator |
| 🟢 **Resolved** | Laporan selesai diproses | Operator/Investigator |
| 🔴 **Rejected** | Laporan ditolak (tidak valid) | Operator/Investigator |

---

*Terakhir diperbarui: Mei 2026*
