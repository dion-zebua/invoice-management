# Software Requirements Specification (SRS)

## Sistem Generator Invoice Berbasis Web (Laravel & MySQL)

---

### 1. Pendahuluan (Introduction)

#### 1.1 Tujuan Dokumen (Purpose)

Dokumen ini merinci spesifikasi kebutuhan perangkat lunak untuk pembangunan aplikasi **Web Generator Invoice**. Tujuannya adalah memberikan panduan teknis dan fungsional bagi pengembang, penguji, serta pemangku kepentingan dalam memahami alur kerja, batasan sistem, dan fitur yang harus dibangun menggunakan framework Laravel dan basis data MySQL sesuai dengan standar IEEE (Karl Wiegers).

#### 1.2 Lingkup Proyek (Scope)

Sistem ini merupakan aplikasi berbasis web yang dirancang untuk memudahkan pengelolaan produk, pelanggan, dan pembuatan dokumen penagihan (_invoice_) secara digital. Sistem memiliki dua peran utama (_roles_), yaitu **Admin** dan **User (Pengguna Biasa)**, dengan batasan hak akses masing-masing.

#### 1.3 Definisi, Akronim, dan Singkatan

- **Laravel:** PHP _framework_ berbasis arsitektur MVC (_Model-View-Controller_).
- **MySQL:** Sistem manajemen basis data relasional.
- **Admin:** Pengguna dengan hak akses tertinggi untuk mengelola sistem dan akun pengguna.
- **User:** Pengguna aplikasi yang bertugas membuat produk dan menerbitkan invoice.

---

### 2. Deskripsi Keseluruhan (Overall Description)

#### 2.1 Perspektif Produk

Aplikasi ini dikembangkan sebagai sistem mandiri berbasis web (_web-based application_). Sistem menggunakan backend Laravel, basis data MySQL, serta antarmuka responsif untuk memudahkan akses melalui peramban (_browser_) desktop maupun seluler.

#### 2.2 Fungsi Produk (Product Functions)

Ringkasan fitur utama berdasarkan hak akses:

1. **Manajemen Admin:**
   - Manajemen akun pengguna (_CRUD User_).
2. **Manajemen User:**
   - Pengelolaan data produk/layanan (_CRUD Product_).
   - Pembuatan, pengelolaan, dan pengunduhan _invoice_ (_CRUD Invoice_).

#### 2.3 Kelas dan Karakteristik Pengguna (User Classes and Characteristics)

- **Admin:** Mengelola hak akses dan memonitor data pengguna yang terdaftar di dalam sistem.
- **User (Staff/Pemilik Bisnis):** Menggunakan sistem untuk menginput katalog produk dan menghasilkan dokumen _invoice_ untuk keperluan transaksi.

#### 2.4 Batasan Operasional (Constraints)

- Aplikasi memerlukan server yang mendukung PHP (sesuai versi Laravel yang digunakan) dan MySQL.
- Akses sistem memerlukan koneksi internet/intranet dan peramban web.

---

### 3. Persyaratan Spesifik (Specific Requirements)

#### 3.1 Antarmuka Eksternal (External Interfaces)

- **Antarmuka Pengguna (UI):** Tampilan web responsif menggunakan HTML5, CSS/Tailwind CSS, dan JavaScript.
- **Antarmuka Perangkat Lunak:**
  - Backend: PHP (Laravel Framework).
  - Database: MySQL.
  - Pustaka Pendukung: DOMPDF (untuk _export invoice_ ke format PDF).

#### 3.2 Kebutuhan Fungsional (Functional Requirements)

##### A. Modul Autentikasi

- **FR-01:** Sistem menyediakan halaman _login_ untuk yang sama untuk _role_ _Admin_ dan _User_.
- **FR-02:** Sistem harus membatasi akses halaman berdasarkan _role_ setelah proses autentikasi berhasil.

Aturan :

- Batas Login 5 kali dlam 5 menit.
- Login khusus _User_ yang disetujui (default setuju).

##### B. Modul Manajemen User (User Dashboard)

- **FR-03:** Melihat daftar seluruh _user_ yang terdaftar di dalam sistem.
- **FR-04:** Menambah, mengubah, dan menghapus data _user_ (_CRUD User_).

Aturan :

- _Admin_ bisa melakukan semua modul.
- _User_ hanya bisa melihat dan mengubah profil sendiri.

##### C. Modul Manajemen Produk (Product Dashboard)

- **FR-05:** Melihat daftar seluruh _produk_ yang terdaftar di dalam sistem.
- **FR-06:** Menambah, mengubah, dan menghapus data _produk_ (_CRUD User_).

Aturan :

- _User_ bisa melakukan semua modul (khusus _produk_ sendiri).
- _Admin_ hanya bisa melihat dan menghapus _produk_ semua _user_.

##### D. Modul Manajemen Invoice (Invoice Dashboard)

- **FR-07:** Melihat daftar seluruh _invoice_ yang terdaftar di dalam sistem.
- **FR-08:** Menambah, mengubah, dan menghapus data _produk_ (_CRUD User_).
- **FR-09:** Membuat _invoice_ baru dengan menentukan tanggal, status, biodata pembeli, memilih produk dari daftar produk atau input manual, menentukan kuantitas, diskon, status pajak, dan menghitung total harga secara otomatis.
- **FR-10:** Sistem menghasilkan nomor _invoice_ secara unik (format acak).
- **FR-11:** Mengunduh atau mencetak _invoice_ dalam format PDF.

Aturan :

- _Admin_ hanya tidak bisa menambah dan mengubah _invoice_.
- _User_ bisa melakukan semua modul (khusus _produk_ sendiri).
- _Invoice_ yang sudah lunas tidak bisa diubah.

#### 3.3 Kebutuhan Non-Fungsional (Non-Functional Requirements)

- **Kinerja (Performance):** Waktu muat halaman rata-rata di bawah 2 detik dalam kondisi jaringan normal.
- **Keamanan (Security):**
  - Kata sandi (_password_) pengguna wajib di-_hash_ menggunakan bcrypt (bawaan Laravel).
  - Penerapan proteksi CSRF (_Cross-Site Request Forgery_) dan pencegahan SQL Injection melalui penggunaan Eloquent ORM / Query Builder Laravel.
- **Ketersediaan (Availability):** Sistem dirancang agar dapat diakses 24/7 pada server web hosting yang stabil.

---

### 4. Skema Basis Data (Database Schema)

Untuk mendukung fungsionalitas di atas, rancangan tabel MySQL yang dibutuhkan meliputi:

#### 4.1 **`users`**

- `id` (PK)
- `name` (VARCHAR)
- `company` (VARCHAR)
- `email` (VARCHAR, Unique)
- `phone` (VARCHAR)
- `password` (VARCHAR)
- `role` (ENUM: 'admin', 'user')
- `payment_methods` (JSON)
  - nama (VARCHAR)
  - metode (VARCHAR)
  - nomor (VARCHAR)
- `created_at`, `updated_at`

#### 4.2 **`products`**

- `id` (PK)
- `user_id` (FK -> users.id)
- `name` (VARCHAR)
- `unit` (VARCHAR)
- `price` (DECIMAL/BIGINT)
- `created_at`, `updated_at`

#### 4.3 **`invoices`**

- `id` (PK)
- `user_id` (FK -> users.id)
- `invoice_number` (VARCHAR, Unique)
- `client_name` (VARCHAR)
- `client_company` (VARCHAR)
- `client_phone` (VARCHAR)
- `tax` (BOOLEAN, Default: false)
- `total_amount` (DECIMAL/BIGINT)
- `down_payment` (DECIMAL/BIGINT)
- `remaining_balance` (DECIMAL/BIGINT)
- `status` (ENUM: 'paid', 'unpaid')
- `due_date` (DATE)
- `created_at`, `updated_at`

#### 4.4 **`invoice_items`**

- `id` (PK)
- `invoice_id` (FK -> invoices.id)
- `name` (VARCHAR)
- `unit` (VARCHAR)
- `price` (DECIMAL/BIGINT)
- `quantity` (INT)
- `subtotal` (DECIMAL/BIGINT)
- `created_at`, `updated_at`

#

ReadMe dibuat menggunakan [readme.so](https://readme.so/editor)
