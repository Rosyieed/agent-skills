# Project Rules & Customizations

Dokumen ini berisi pedoman gaya penulisan kode, batasan perilaku, dan instruksi umum yang wajib diikuti oleh seluruh asisten AI (Antigravity) yang bekerja dalam workspace ini.

---

## 🎯 Orientasi Kualitas, Klarifikasi Scope & Batasan Perilaku

- **Prioritas Hasil vs Kecepatan (Quality over Speed)**:
  - Asisten AI dilarang terburu-buru memberikan solusi instan jika berpotensi mengorbankan kualitas logika, keamanan, atau standar arsitektur.
  - Fokus utama adalah **akurasi, ketepatan solusi, dan kepatuhan 100% pada aturan proyek**, bukan sekadar memberikan jawaban cepat.
- **Nol Asumsi & Wajib Klarifikasi (Zero Assumption Policy)**:
  - Dilarang keras membuat asumsi sepihak dalam bentuk apa pun (baik pada logika bisnis, struktur data, alur proses, maupun pendekatan teknis). Tidak ada yang boleh diasumsikan.
  - Segala sesuatu yang tidak terdefinisi secara 100% eksplisit, masih ambigu, memiliki keraguan, atau menyediakan beberapa opsi pendekatan **WAJIB ditanyakan dan dikonfirmasi langsung kepada pengguna** sebelum merancang atau mengeksekusi kode.
- **Disiplin Batasan Lingkup (Strict Scope Boundary)**:
  - Asisten AI wajib fokus penuh pada permasalahan atau fitur yang sedang dikerjakan.
  - Dilarang melakukan perubahan kode di luar *scope* yang disepakati (misalnya *over-engineering* atau refaktorisasi berkas yang tidak relevan) tanpa persetujuan eksplisit dari pengguna.
- **Presisi Tinggi pada Logika Perhitungan & Finansial**:
  - Dilarang menebak atau menggunakan asumsi sendiri pada formula matematika, perhitungan pajak, diskon, persentase, atau nilai finansial.
  - Jika rumus perhitungan belum didefinisikan secara eksplisit di spesifikasi, asisten AI **WAJIB meminta konfirmasi rumus pasti** kepada pengguna sebelum menulis kode/query.
  - Wajib memperhatikan presisi tipe data di database/PHP (misal: gunakan `decimal`, pembulatan resmi `round()`, atau hindari masalah *floating-point precision* pada transaksi keuangan) serta menyertakan *unit test* khusus untuk menguji kalkulasi edge-cases (misal: angka nol, pecahan, desimal berulang).
- **Verifikasi Eksekusi Teruji (Empirical Verification)**:
  - Asisten AI dilarang mengklaim fitur/perbaikan telah selesai hanya dengan mengedit berkas. Wajib melakukan eksekusi perintah pengujian (`php artisan test` atau validasi terkait) untuk memastikan tidak ada *breaking change* sebelum menyerahkan hasil ke pengguna.
- **Pelaporan & Penanganan Error Transparan (Zero Silent Failures)**:
  - Dilarang menyembunyikan error (*swallowing exception*) atau memberikan fallback nilai kosong/palsu secara diam-diam tanpa analisis. Setiap kegagalan eksekusi atau runtime error wajib dicatat pada log serta dilaporkan secara transparan.

---

## 🚀 Aturan Utama Laravel Best Practices

- **Selalu Gunakan Skill Laravel Best Practices**: Asisten AI wajib membaca, memuat, dan mengikuti seluruh pedoman dari skill `laravel-best-practices` (`.agents/skills/laravel-best-practices/SKILL.md`) setiap kali merancang, memodifikasi, membuat, atau menguji kode PHP/Laravel di dalam workspace ini.
- **Type Hinting & Strict Typing**: Wajib menggunakan *strict type-hinting* pada parameter metode dan *return type* (kembalian fungsi) untuk semua berkas PHP baru maupun hasil refaktorisasi.
- **Validasi Form Request**: Hindari menulis validasi inline langsung di dalam kontroler. Selalu gunakan kelas **Form Request** terpisah (di bawah namespace `App\Http\Requests\`). Gunakan `$request->validated()` saja — dilarang menggunakan `$request->all()`.
- **Transaksi Basis Data Atomic (All-or-Nothing) & Locking**:
  - Seluruh mutasi data yang melibatkan >1 tabel atau operasi kompleks wajib bersifat **100% Atomic (All-or-Nothing)** menggunakan `DB::beginTransaction()`, `DB::commit()`, dan `DB::rollBack()` di dalam blok `try-catch`. Jika ada 1 kegagalan/exception, seluruh operasi wajib dibatalkan total tanpa meninggalkan sisa data menggantung (*partial commit*).
  - Pada transaksi stok, saldo, atau data finansial yang rawan *race condition*, wajib menerapkan pembacaan terproteksi via pessimistic locking (`lockForUpdate()`) atau atomic locks (`Cache::lock()`) untuk mencegah konflik konkurensi.
- **Pencegahan Double-Submit & Debounce (UI / Frontend)**:
  - Setiap tombol aksi atau formulir eksekusi (seperti Simpan, Edit, Hapus, Posting, Approve) **WAJIB memproteksi dari double-click/double-submit**.
  - Tombol submit harus otomatis di-disabled (`disabled="disabled"`) dan menampilkan indikator loading/spinner segera setelah diklik atau saat AJAX request sedang berjalan.
  - Untuk pencarian otomatis, pencarian live/auto-complete, atau input kuantitas interaktif, wajib menerapkan *debounce* (minimal 300–500ms) untuk mencegah penumpukan request HTTP.
- **Pengujian Unit & Fitur (Testing)**: Wajib menulis berkas pengujian fitur (*feature tests*) menggunakan PHPUnit/Pest untuk setiap fitur baru yang dibangun. Gunakan `LazilyRefreshDatabase` dan assertion bawaan seperti `assertModelExists()` guna memastikan keandalan kode 100% lulus sebelum pengerjaan dianggap selesai.
- **Kueri Basis Data & Performa Eloquent**:
  - Hindari penggunaan raw SQL query jika bisa dicapai menggunakan Eloquent ORM atau Query Builder. Dilarang melakukan string concatenation pada SQL dengan input pengguna (gunakan parameter binding).
  - Selalu lakukan *eager loading* (`with()`) atau `withCount()` untuk relasi terkait guna menghindari masalah N+1 query.
  - Pilih hanya kolom yang dibutuhkan (`select('id', 'name')`), hindari `SELECT *`. Gunakan `chunkById()` atau `cursor()` untuk pemrosesan dataset besar agar hemat penggunaan memori.
  - Manfaatkan `addSelect()` subqueries dan `whereIn` + `pluck()` daripada `whereHas` berlebih demi optimasi indeks database.
- **Manajemen Model & Eloquent Casts**:
  - Selalu definisikan `$fillable` atau `$guarded` pada setiap model.
  - Gunakan method `casts()` untuk tipe data casting (termasuk `encrypted` cast untuk kolom sensitif).
- **Arsitektur Controller & Service**:
  - Batasi metode controller agar tetap ramping (< 10 baris). Ekstrak *business logic* yang kompleks ke kelas Service, Single-purpose Action, atau Model Hooks.
- **Penggunaan Helper Laravel vs Native PHP**:
  - Utamakan helper bawaan Laravel (`Str::*`, `Arr::*`, `Number::*`, `Uri::*`, `Str::of()`) daripada fungsi bawaan PHP native.
  - Pastikan selalu menggunakan standar penulisan kode PHP sesuai PSR-12.

---

## 🎨 Keahlian Tambahan (Brainstorming, UI & Review)

- **Gunakan Skill Brainstorming**: Sebelum memulai perancangan fitur baru, perubahan alur bisnis yang kompleks, atau integrasi pihak ketiga, asisten AI wajib membaca dan menerapkan panduan dari skill `brainstorming` (`.agents/skills/brainstorming/SKILL.md`) guna mengeksplorasi kebutuhan dan menyelaraskan rencana implementasi dengan pengguna.
- **Gunakan Skill Impeccable**: Saat membuat, memodifikasi, atau memoles tampilan antarmuka visual (berkas Blade/HTML, CSS, Javascript, dan tata letak UI), asisten AI wajib membaca dan mengikuti pedoman dari skill `impeccable` (`.agents/skills/impeccable/SKILL.md`) untuk memastikan antarmuka yang dihasilkan memiliki estetika premium, responsif, ramah UX, dan bebas dari desain yang polos atau membosankan atau AI Slop.
- **Gunakan Skill Caveman Review**: Setelah menulis, memodifikasi, atau merefaktorisasi kode, asisten AI wajib membaca dan menggunakan panduan dari skill `caveman-review` (`.agents/skills/caveman-review/SKILL.md`) untuk melakukan peninjauan ulang kode (*code review*) secara ultra-ringkas, padat, dan berorientasi aksi (format: lokasi, masalah, solusi) guna memastikan keandalan serta kebersihan kode sebelum diselesaikan.
- **Gunakan Skill Graphify & Verifikasi Kode Langsung**: Saat melakukan analisis arsitektur, penelusuran alur/relasi antar-modul, atau analisis dampak perubahan (*impact analysis*), asisten AI wajib mengutamakan kueri peta pengetahuan (`graphify-out/graph.json` atau `/graphify query "<pertanyaan>"`) untuk menemukan keterkaitan berkas secara presisi. Sebelum melakukan penulisan atau pengeditan kode, asisten AI **TETAP WAJIB membaca dan memeriksa isi berkas fisik secara langsung** guna memastikan kebenaran logika terbaru. Setelah penambahan modul atau perubahan struktur kode yang signifikan, perbarui peta pengetahuan menggunakan `/graphify . --update`.

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
- **Background Jobs & Antrean (Queue)**: Proses komputasi berat, ekspor/impor data besar, atau interaksi dengan API pihak ketiga wajib menggunakan Laravel Queue/Jobs.
  - Nilai `retry_after` pada antrean harus selalu lebih besar dari `timeout` job, sertakan exponential backoff (contoh: `[1, 5, 10]`).
  - Gunakan `ShouldBeUnique` untuk mencegah duplikasi pemrosesan job dan selalu implementasikan metode `failed()` untuk penanganan kegagalan job.
- **Strategi Caching & Data Transien**:
  - Utamakan `Cache::remember()` atau `Cache::flexible()` dibanding get/put manual. Gunakan `once()` atau `Cache::memo()` untuk mengurangi pembacaan berulang dalam satu siklus request.
- **Pengelolaan Aset Frontend (Blade)**: Jangan menulis tag `<script>` atau `<style>` secara acak di tengah-tengah file Blade. Selalu manfaatkan `@push('scripts')` dan `@push('styles')` agar injeksi aset selalu terkumpul rapi di tempat yang semestinya pada layout utama.
- **Standar Format API & HTTP Client**:
  - Jika membangun endpoint API eksternal atau endpoint internal berbasis AJAX/JSON, format respons harus konsisten menggunakan *Laravel API Resources* dan menggunakan struktur standar (contoh: `{ "success": boolean, "message": string, "data": object/array }`).
  - Untuk pemanggilan HTTP eksternal melalui `Http::*`, selalu tentukan `timeout` dan `connectTimeout` eksplisit serta manfaatkan `retry()` dengan exponential backoff.

---

## 🧪 Alur Kerja Pengguna (User Workflow)

- **Brainstorming → Persetujuan → Implementasi → Code Review → Testing → Commit**:
  1. **Brainstorming**: Eksplorasi kebutuhan dan rancangan solusi bersama pengguna sebelum menulis kode.
  2. **Rencana Implementasi**: Susun rencana teknis terperinci dan minta persetujuan eksplisit dari pengguna sebelum mengeksekusi.
  3. **Implementasi**: Tulis kode sesuai rencana yang disetujui. Jika menemukan masalah signifikan, perbarui rencana dan minta persetujuan ulang.
  4. **Code Review (Caveman Review)**: Tinjau ulang kebersihan, keandalan, dan standar kode yang baru ditulis menggunakan skill `caveman-review` untuk memastikan tidak ada celah/masalah tersembunyi.
  5. **Testing**: Jalankan seluruh test suite (`php artisan test`) dan pastikan **100% lulus** sebelum pekerjaan dianggap selesai.
  6. **Commit Message**: Buat pesan komit Git menggunakan skill `work-helper` setelah seluruh kode, review, dan test selesai.

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
