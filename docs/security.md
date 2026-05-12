# 🔐 SIAP — Dokumentasi Keamanan (Security)

Dokumen ini menjelaskan lapisan keamanan yang diterapkan dalam sistem SIAP untuk melindungi data pengguna, integritas laporan, dan stabilitas sistem.

---

## 1. CSRF Protection (Cross-Site Request Forgery)

### Ancaman
CSRF adalah serangan di mana penyerang mengelabui browser pengguna untuk mengirim request jahat ke aplikasi yang sudah terautentikasi, misalnya menghapus data atau mengubah status laporan tanpa sepengetahuan pengguna.

### Implementasi di SIAP

**Global CSRF Protection (CodeIgniter 4):**

CSRF protection diaktifkan secara global di `app/Config/Security.php`:
```php
public string $csrfProtection = 'cookie'; // atau 'session'
public string $tokenName      = 'csrf_token_name';
public string $headerName     = 'X-CSRF-TOKEN';
public bool   $regenerate     = true;
```

**Pada Form HTML:**
```php
// Setiap form yang melakukan POST wajib menyertakan:
<?= csrf_field() ?>
// Menghasilkan: <input type="hidden" name="csrf_test_name" value="...">
```

**Pada AJAX Request (Chat):**
Token CSRF di-inject dari PHP ke JavaScript secara dinamis:
```php
// Di dalam <script>:
formData.append('<?= csrf_token() ?>', '<?= csrf_hash() ?>');
```
Ini memastikan token selalu segar bahkan pada request AJAX real-time.

**Form yang Dilindungi:**
- Form Submit Laporan WBS (`POST /wbs/submit`)
- Form Submit Aspirasi (`POST /suggestion/submit`)
- Form Login (`POST /auth/process`)
- Form Update Status (`POST /wbs/admin/update-status`, `POST /suggestion/admin/update-status`)
- Form Kirim Chat (`POST /wbs/admin/send-message`, `POST /track/send-message`)
- Form Tracking Auth (`POST /track/auth`)
- Semua form di panel Settings

---

## 2. XSS Protection (Cross-Site Scripting)

### Ancaman
XSS memungkinkan penyerang menyisipkan skrip berbahaya ke dalam halaman yang kemudian dieksekusi oleh browser pengguna lain. Ini sangat berbahaya di sistem pelaporan karena output data pengguna ditampilkan ke banyak pihak.

### Implementasi di SIAP

**Escaping Output:**
Semua data dari database yang ditampilkan ke view menggunakan fungsi `esc()` bawaan CodeIgniter 4:
```php
<?= esc($c->title) ?>
<?= esc($complaint->description) ?>
<?= esc($user->name) ?>
```

Fungsi `esc()` secara default menggunakan konteks `html`, yang mengkonversi karakter berbahaya seperti `<`, `>`, `"`, `'`, `&` menjadi HTML entities.

**Escaping sebelum penyimpanan:**
Beberapa field yang tidak memerlukan format HTML juga di-escape saat disimpan:
```php
'message' => esc($this->request->getPost('message'))
```

**Input yang Diescaping:**
- Judul & deskripsi laporan (WBS & Aspirasi)
- Pesan chat dari investigator
- Semua field konfigurasi sistem (app_name, tagline, dll)
- Nama kategori
- Nama dan data user

**JavaScript Template Literals:**
Pada render pesan chat di JavaScript, konten pesan ditampilkan langsung dari JSON API. Untuk keamanan tambahan, data di-escape saat disimpan ke database sebelum dikembalikan via JSON.

---

## 3. IDOR Protection (Insecure Direct Object Reference)

### Ancaman
IDOR terjadi ketika penyerang mengubah ID objek (misalnya ID laporan) di URL/request untuk mengakses data orang lain yang bukan haknya. Contoh: Operator kategori A mengakses laporan kategori B dengan menebak ID.

### Implementasi di SIAP

**Verifikasi Kepemilikan di Setiap Method:**
Setiap endpoint admin yang mengakses data spesifik melakukan double-check:

```php
// Di WbsAdmin.php — method view(), updateStatus(), sendMessage(), getMessages(), download()
private function getAccessibleCategories(): ?array
{
    $roleId = session()->get('role_id');
    if ($roleId === 1) return null; // Superadmin: akses semua

    // Operator: hanya kategori yang ditugaskan
    $allowed = $db->table('operator_categories')
                  ->select('category_id')
                  ->where('user_id', $userId)
                  ->get()->getResultArray();

    return array_column($allowed, 'category_id');
}

// Pada setiap akses data spesifik:
if (!$complaint || ($allowedCategories !== null && !in_array($complaint->category_id, $allowedCategories))) {
    return redirect()->back()->with('error', 'Akses ditolak.');
}
```

**Proteksi File Download:**
Sebelum mengizinkan download lampiran, sistem memverifikasi apakah complaint yang memiliki lampiran tersebut ada dalam kategori yang diizinkan operator:
```php
// Di method download():
$complaint = $this->complaintModel->find($attachment->complaint_id);
if (!$complaint || ($allowedCategories !== null && !in_array($complaint->category_id, $allowedCategories))) {
    return redirect()->back()->with('error', 'Akses file ditolak.');
}
```

**Tracking Session Protection:**
Halaman tracking hanya dapat diakses setelah autentikasi tiket. Session `tracked_complaint_id` yang disimpan digunakan untuk membatasi akses hanya pada satu tiket per sesi, mencegah akses tiket orang lain.

---

## 4. Input Validation

### Implementasi di SIAP

**Server-Side Validation (CodeIgniter 4 Validation Library):**

Form WBS:
```php
$rules = [
    'category_id' => 'required|is_natural_no_zero',
    'title'       => 'required|min_length[5]|max_length[200]',
    'description' => 'required|min_length[10]',
    // File validation (jika ada):
    'attachment'  => 'max_size[attachment,2048]|ext_in[attachment,jpg,jpeg,png,pdf]'
];
```

Form Aspirasi:
```php
$rules = [
    'category_id'  => 'required',
    'privacy_mode' => 'required|in_list[anonymous,confidential,public]',
    'title'        => 'required|min_length[5]',
    'description'  => 'required|min_length[10]',
    // URL validation (opsional):
    'attachment_link' => 'valid_url'
];
```

**NIK Validation (Login):**
```php
$rules = [
    'nik'      => 'required|numeric',
    'password' => 'required'
];
```

**Filter Sanitasi GET Parameters:**
Semua parameter filter (`category`, `status`, `search_ticket`, dll.) dibaca via `$this->request->getGet()` bawaan CI4 yang sudah melakukan sanitasi dasar. Filter `category_id` yang dikirim oleh operator divalidasi ulang terhadap daftar kategori yang diizinkan sebelum digunakan dalam query.

---

## 5. Upload Security

### Ancaman
Upload file yang tidak aman dapat menjadi vektor serangan: eksekusi kode berbahaya via file PHP yang diupload, penyimpanan file terlalu besar (DoS), atau penyajian file berbahaya.

### Implementasi di SIAP

**Validasi Tipe File (Dual Check):**

1. **Extension Whitelist** — Hanya ekstensi yang diizinkan:
   ```php
   $allowedMime = ['image/jpeg', 'image/png', 'application/pdf'];
   // atau via CI4 rule: ext_in[attachment,jpg,jpeg,png,pdf]
   ```

2. **MIME Type Detection dari Konten File** — Menggunakan `getimagesize()` untuk mendeteksi tipe gambar dari konten aktual file (bukan hanya ekstensinya):
   ```php
   $imgInfo = @getimagesize($tempPath);
   if (!$imgInfo) {
       // Bukan gambar valid — salin apa adanya
   }
   $imageType = $imgInfo[2]; // IMAGETYPE_JPEG, IMAGETYPE_PNG, dll.
   ```

**Batas Ukuran File:**
- Maksimal 2MB (2048 KB) per file.
- Dicek sebelum upload di sisi klien (JavaScript) dan dikonfirmasi di server via CI4 rule `max_size[attachment,2048]`.

**Kompresi & Konversi:**
File gambar diproses melalui GD Library (`compress_and_save_file()`):
- Gambar besar di-resize secara proporsional (max 1920x1080).
- JPEG: dikompresi dengan kualitas 70%.
- PNG: Alpha channel dijaga, level kompresi disesuaikan.
- Jika hasil kompresi lebih besar dari aslinya, file asli digunakan sebagai fallback.

**Penyimpanan Aman:**
- File disimpan di `writable/uploads/wbs/` dan `writable/uploads/suggestion/` — **di luar document root** (`/public`), sehingga tidak dapat diakses langsung via URL.
- Nama file dirandomisasi: `bin2hex(random_bytes(8)) . '_' . time() . '.ext'` — mencegah tebak nama file.

**Download Secure:**
```php
return $this->response->download($path, null)->setFileName($attachment->original_name);
// CI4 mengatur header Content-Type dan Content-Disposition secara otomatis
```
File dikirim sebagai download dengan nama file aslinya, bukan nama acak yang disimpan di server.

---

## 6. Keamanan Tambahan

### Password Hashing
```php
// Saat registrasi/pembuatan user:
password_hash($password, PASSWORD_BCRYPT)

// Saat verifikasi login/PIN:
password_verify($inputPassword, $hashedPassword)
```
PIN laporan WBS menggunakan mekanisme yang sama — tidak pernah disimpan dalam bentuk plaintext.

### Session Security
- Session di-set dengan konfigurasi keamanan CodeIgniter 4 standar.
- Setelah login, sesi menyimpan: `user_id`, `role_id`, `name`, `role_name`, `is_suggestion_operator`, `is_logged_in`.
- Sesi tracking (`tracked_complaint_id`) terpisah dari sesi admin untuk isolasi konteks.

### AuthFilter
```php
// app/Filters/AuthFilter.php
public function before(RequestInterface $request, $arguments = null)
{
    if (!session()->get('is_logged_in')) {
        return redirect()->to('/login');
    }
}
```
Filter ini diterapkan pada semua route grup admin (`/dashboard`, `/wbs/admin`, `/suggestion/admin`, `/settings`, `/report`).

---

*Terakhir diperbarui: Mei 2026*
