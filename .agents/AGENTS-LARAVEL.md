# Project Rules & Customizations

Dokumen ini berisi pedoman gaya penulisan kode, batasan perilaku, dan instruksi umum yang wajib diikuti oleh seluruh asisten AI (Antigravity) yang bekerja dalam workspace ini.

---

## 🚀 Aturan Utama Laravel Developer

- **Selalu Gunakan Skill Laravel Developer**: Asisten AI wajib membaca, memuat, dan mengikuti seluruh pedoman dari skill `laravel-developer` (`.agents/skills/laravel-developer/SKILL.md`) setiap kali merancang, memodifikasi, membuat, atau menguji kode PHP/Laravel di dalam workspace ini.
- **Type Hinting**: Wajib menggunakan *strict type-hinting* pada parameter metode dan *return type* (kembalian fungsi) untuk semua berkas PHP baru maupun hasil refaktorisasi.
- **Validasi Form Request**: Hindari menulis validasi inline langsung di dalam kontroler. Selalu gunakan kelas **Form Request** terpisah (di bawah namespace `App\Http\Requests\`) untuk menangani validasi input pengguna.
- **Transaksi Basis Data & Try-Catch**: Setiap operasi tulis data (*write operations*) seperti insert, update, dan delete yang memiliki kompleksitas atau potensi kegagalan wajib dibungkus dalam blok `DB::beginTransaction()`, `DB::commit()`, dan `DB::rollBack()` bersama penanganan exception `try-catch` serta pencatatan log error terperinci.
- **Pengujian Unit & Fitur (Testing)**: Wajib menulis berkas pengujian fitur (*feature tests*) menggunakan PHPUnit untuk setiap fitur baru yang dibangun guna memastikan keandalan kode 100% lulus sebelum pengerjaan dianggap selesai.
- **Aturan Tambahan Senior Laravel**:
  - Hindari penggunaan raw SQL query jika bisa dicapai menggunakan Eloquent ORM or Query Builder.
  - Selalu lakukan *eager loading* (`with()`) untuk relasi terkait guna menghindari masalah N+1 query.
  - Jangan menuliskan *business logic* rumit secara langsung di dalam controller; gunakan kelas Service atau Model Hooks jika diperlukan.
  - Pastikan selalu menggunakan standar penulisan kode PHP sesuai PSR-12.

---

## 🎨 Keahlian Tambahan (Brainstorming & UI)

- **Gunakan Skill Brainstorming**: Sebelum memulai perancangan fitur baru, perubahan alur bisnis yang kompleks, atau integrasi pihak ketiga, asisten AI wajib membaca dan menerapkan panduan dari skill `brainstorming` (`.agents/skills/brainstorming/SKILL.md`) guna mengeksplorasi kebutuhan dan menyelaraskan rencana implementasi dengan pengguna.
- **Gunakan Skill Impeccable**: Saat membuat, memodifikasi, atau memoles tampilan antarmuka visual (berkas Blade/HTML, CSS, Javascript, dan tata letak UI), asisten AI wajib membaca dan mengikuti pedoman dari skill `impeccable` (`.agents/skills/impeccable/SKILL.md`) untuk memastikan antarmuka yang dihasilkan memiliki estetika premium, responsif, ramah UX, dan bebas dari desain yang polos atau membosankan atau AI Slop.

---

## 🗄️ Konvensi Basis Data & Struktur Proyek

- **Prefix Naming Convention**:
  - Gunakan prefix **`tr_`** untuk seluruh tabel Master atau tabel Relasional (contoh: `tr_product_categories`, `tr_products`).
  - Gunakan prefix **`tt_`** untuk seluruh tabel Transaksional (contoh: `tt_orders`, `tt_invoices`).
- **Kebijakan Penghapusan Data (SoftDeletes)**:
  - Seluruh tabel — baik master (`tr_`) maupun transaksional (`tt_`) — bersikap *non-destructive* (data tidak boleh dihapus secara fisik dari database).
  - Gunakan trait **`SoftDeletes`** bawaan Laravel untuk menangani penghapusan data. Pastikan migrasi menyertakan kolom `$table->softDeletes()` dan model menggunakan `use SoftDeletes;`.
- **Bahasa Kode vs Bahasa Antarmuka (UI)**:
  - **Sisi Teknis (Kode/DB)**: Seluruh penamaan tabel database, kolom, nama model, kontroler, variabel, berkas penunjang, komentar kode, dan dokumentasi teknis wajib menggunakan **Bahasa Inggris**.
  - **Sisi Tampilan (User View/UI)**: Seluruh tampilan visual, label formulir, placeholder input, pesan peringatan, notifikasi sukses/gagal, dan teks tombol wajib menggunakan **Bahasa Indonesia**.
- **Audit Trails**:
  - Setiap tabel master/transaksi wajib menyertakan kolom audit trail **`created_by`** dan **`updated_by`** yang diisi secara otomatis menggunakan Eloquent model hooks saat data dibuat/diperbarui dengan ID pengguna yang sedang masuk.

---

## 🔒 Keamanan & Otorisasi (Guard Pattern)

- **Proteksi Berlapis (Multi-layered Security)**:
  - Setiap pembatasan akses harus diterapkan di **tiga lapisan** secara bersamaan:
    1. **Lapisan UI**: Menyembunyikan/menonaktifkan tombol, opsi dropdown, dan checkbox yang tidak relevan bagi peran pengguna yang sedang masuk (misalnya, menyaring daftar role/permission di form edit).
    2. **Lapisan Validasi (Form Request)**: Menolak payload request yang mencoba menyisipkan data terlarang (misalnya, menugaskan role `owner` melalui request langsung) dengan validasi kustom berbasis closure.
    3. **Lapisan Controller (Backend Guard)**: Menambahkan pengecekan `abort(403)` pada method controller (edit, update, delete) untuk memblokir akses langsung ke resource yang dilindungi.
  - Jangan pernah mengandalkan satu lapisan saja. Jika UI bisa dimanipulasi, backend harus tetap aman.

- **Lockout Visual (UI Lock Badge)**:
  - Pada tabel Datatable, jika suatu baris data tidak boleh dimodifikasi oleh peran pengguna saat ini (misalnya user/role `owner` yang dilihat oleh non-owner), kolom aksi wajib menampilkan badge **Sistem Lock** (`<span class="badge bg-secondary-subtle text-secondary font-size-12"><i class="ri-lock-line me-1"></i> Sistem Lock</span>`) sebagai pengganti tombol aksi Edit/Hapus.

- **Data Preservation**:
  - Saat menyinkronkan hak akses (*syncPermissions*, *syncRoles*), jika pengguna yang sedang masuk tidak memiliki wewenang untuk memodifikasi jenis hak akses tertentu, pastikan hak akses eksisting tersebut tidak terhapus. Ambil data lama dan gabungkan kembali (*merge*) sebelum memanggil sync.
  - Contoh: Admin biasa mengedit user yang sudah memiliki permission manajemen → permission manajemen lama harus tetap terjaga.

---

## 📂 Konvensi Struktur & Penamaan Berkas

- **Form Request**: Kelompokkan Form Request di dalam subfolder berdasarkan domain/modul fitur, **bukan** langsung di root `app/Http/Requests/`.
  - Contoh: `app/Http/Requests/Master/UserManagement/StoreUserRequest.php`
  - Contoh: `app/Http/Requests/Master/RoleManagement/UpdateRoleRequest.php`
  - Contoh: `app/Http/Requests/Transaction/OrderRequest.php`
- **Controller**: Kelompokkan controller di dalam subfolder berdasarkan jenis tabel/modul.
  - Contoh: `app/Http/Controllers/Master/UserController.php`
  - Contoh: `app/Http/Controllers/Transaction/OrderController.php`
- **Service Classes**: Kelompokkan service di dalam subfolder berdasarkan domain/fitur.
  - Contoh: `app/Services/Order/OrderExportService.php`
- **Blade Views**: Organisasikan berkas Blade di dalam subfolder yang mencerminkan struktur menu/modul, idealnya di bawah folder utama `pages/`.
  - Contoh: `resources/views/pages/master/user/index.blade.php`
  - Contoh: `resources/views/pages/transaction/order/create.blade.php`
- **Penamaan Route (Nama/Alias)**: Gunakan format `kategori-modul.aksi` atau `modul.aksi` untuk penamaan route (`name()`).
  - Contoh: `master-user.index`, `master-role.edit`
  - Contoh: `transaction-order.store`
- **Penamaan Migrasi**: Gunakan prefix deskriptif yang jelas sesuai tindakan.
  - Pembuatan tabel: `create_nama_tabel_table`
  - Penambahan kolom: `add_nama_kolom_to_nama_tabel_table`
  - Perubahan/Modifikasi: `modify_nama_kolom_in_nama_tabel_table`
  - Penghapusan: `drop_nama_kolom_from_nama_tabel_table`

---

## ⚙️ Praktik Terbaik & Standar Integrasi

- **Manajemen Environment**: Larangan keras *hardcode* kredensial atau konfigurasi statis di dalam kode. Selalu gunakan file `.env`. Pemanggilan fungsi `env()` hanya diizinkan di dalam file konfigurasi (`config/`). Di dalam logika kode (Controller, Service, dll), wajib menggunakan fungsi `config()`.
- **Background Jobs & Antrean**: Proses komputasi berat, ekspor/impor data besar, atau interaksi dengan API pihak ketiga wajib menggunakan Laravel Queue/Jobs agar antarmuka pengguna (UI) tetap responsif dan tidak mengalami "hang" atau *timeout*.
- **Pengelolaan Aset Frontend (Blade)**: Jangan menulis tag `<script>` atau `<style>` secara acak di tengah-tengah file Blade. Selalu manfaatkan `@push('scripts')` dan `@push('styles')` agar injeksi aset selalu terkumpul rapi di tempat yang semestinya pada layout utama.
- **Standar Format API**: Jika membangun endpoint API eksternal atau endpoint internal berbasis AJAX/JSON, format respons harus konsisten menggunakan *Laravel API Resources* dan menggunakan struktur standar (contoh: `{ "success": boolean, "message": string, "data": object/array }`).

---

## 🧪 Alur Kerja Pengguna (User Workflow)

- **Brainstorming → Persetujuan → Implementasi → Testing → Commit**:
  1. **Brainstorming**: Eksplorasi kebutuhan dan rancangan solusi bersama pengguna sebelum menulis kode.
  2. **Rencana Implementasi**: Susun rencana teknis terperinci dan minta persetujuan eksplisit dari pengguna sebelum mengeksekusi.
  3. **Implementasi**: Tulis kode sesuai rencana yang disetujui. Jika menemukan masalah signifikan, perbarui rencana dan minta persetujuan ulang.
  4. **Testing**: Jalankan seluruh test suite (`php artisan test`) dan pastikan **100% lulus** sebelum pekerjaan dianggap selesai.
  5. **Commit Message**: Buat pesan komit Git menggunakan skill `work-helper` setelah seluruh kode dan test selesai dan diperiksa.

- **Perubahan Iteratif**:
  - Pengguna sering meminta penyesuaian tambahan setelah fitur utama selesai (misalnya: "sesuaikan juga seeder-nya", "tambahkan guard di UI-nya juga"). Asisten harus siap menambahkan lapisan baru tanpa merusak yang sudah ada, dan selalu menjalankan ulang test suite setelah setiap perubahan.

---

## 🌐 Bahasa Komunikasi

- **Bahasa Respons Asisten AI**: Asisten AI wajib merespons dan berkomunikasi dalam **Bahasa Indonesia** di seluruh percakapan dengan pengguna.
- **Pengecualian**: Kode sumber, pesan komit Git, nama variabel/fungsi, dan dokumentasi teknis di dalam kode tetap menggunakan **Bahasa Inggris** (sesuai aturan di bagian Konvensi Basis Data).

---

## 📝 Konvensi Dokumentasi & Spesifikasi

- **Dokumen Spesifikasi Desain**: Simpan di folder `docs/superpowers/specs/` dengan format nama `YYYY-MM-DD-nama-fitur-design.md`.
- **Log Mandiri**: Jika fitur membutuhkan pencatatan log terpisah dari Laravel Log, simpan di bawah `storage/app/` dengan struktur subfolder dan penamaan berkas yang jelas (contoh: `storage/app/order-processing/order-processing-YYYY-MM.log`).
