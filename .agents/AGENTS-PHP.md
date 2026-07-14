# Project Rules & Customizations (PHP)

Dokumen ini berisi pedoman gaya penulisan kode, batasan perilaku, dan instruksi umum yang wajib diikuti oleh seluruh asisten AI (Antigravity) yang bekerja dalam workspace ini untuk proyek berbasis PHP (Non-Framework/Native atau Framework Agnostik).

---

## 🚀 Aturan Utama PHP Developer

- **Type Hinting**: Wajib menggunakan *strict type-hinting* pada parameter metode dan *return type* (kembalian fungsi) untuk semua berkas PHP baru maupun hasil refaktorisasi. Gunakan `declare(strict_types=1);` di awal setiap file PHP jika memungkinkan.
- **Standar Pengodean (Coding Standard)**: Pastikan selalu menggunakan standar penulisan kode PHP sesuai **PSR-12** (atau standar terbaru yang relevan).
- **Manajemen Dependensi**: Selalu gunakan **Composer** untuk mengelola dependensi pihak ketiga. Jangan pernah mengunduh library secara manual dan meletakkannya di dalam folder proyek kecuali tidak ada alternatif lain.
- **Keamanan Basis Data (Prepared Statements)**: Dilarang keras menggunakan *raw SQL string interpolation* yang rentan terhadap SQL Injection. Selalu gunakan **Prepared Statements** (menggunakan PDO atau library abstraksi database lainnya) untuk semua operasi kueri (SELECT, INSERT, UPDATE, DELETE).
- **Pengujian (Testing)**: Wajib menulis berkas pengujian menggunakan **PHPUnit** atau *testing framework* serupa untuk setiap fungsi/metode kritikal guna memastikan keandalan kode.
- **Penanganan Kesalahan (Error Handling)**: Setiap operasi yang memiliki potensi kegagalan (seperti pemanggilan API eksternal, manipulasi file, interaksi database) wajib dibungkus dalam blok `try-catch` dengan pencatatan log (*logging*) yang memadai.

---

## 🎨 Keahlian Tambahan (Brainstorming & UI)

- **Gunakan Skill Brainstorming**: Sebelum memulai perancangan arsitektur baru atau perubahan alur yang kompleks, asisten AI wajib membaca dan menerapkan panduan dari skill `brainstorming` (`.agents/skills/brainstorming/SKILL.md`) guna mengeksplorasi kebutuhan dan menyelaraskan rencana implementasi dengan pengguna.
- **Gunakan Skill Impeccable**: Saat merancang atau memodifikasi tampilan antarmuka (HTML, CSS, Javascript), asisten AI wajib membaca dan mengikuti pedoman dari skill `impeccable` (`.agents/skills/impeccable/SKILL.md`) untuk memastikan antarmuka yang dihasilkan memiliki estetika premium, responsif, dan ramah UX.

---

## 🗄️ Konvensi Keamanan (Security)

- **Sanitasi dan Validasi Input**: Semua input dari pengguna (melalui `$_GET`, `$_POST`, `$_REQUEST`, dll) wajib divalidasi dan disanitasi sebelum diproses.
- **Pencegahan XSS (Cross-Site Scripting)**: Semua output yang ditampilkan ke HTML wajib di-escape menggunakan fungsi seperti `htmlspecialchars()`.
- **Pencegahan CSRF**: Jika membuat form, wajib mengimplementasikan token CSRF untuk melindungi dari serangan Cross-Site Request Forgery.
- **Manajemen Kredensial**: Dilarang keras *hardcode* kata sandi, API key, atau kredensial database di dalam kode sumber. Gunakan *environment variables* (misalnya menggunakan library `vlucas/phpdotenv` atau bawaan server) untuk menyimpan konfigurasi sensitif.

---

## 📂 Konvensi Struktur Berkas dan OOP

- **Prinsip OOP**: Terapkan prinsip-prinsip Object-Oriented Programming (OOP) yang baik, termasuk SOLID, DRY (Don't Repeat Yourself), dan pengkapsulan (Encapsulation).
- **Autoloading**: Gunakan standar **PSR-4** untuk *autoloading* kelas-kelas PHP. Pastikan struktur folder mencerminkan *namespace* dari kelas tersebut.
- **Pemisahan Perhatian (Separation of Concerns)**: Hindari mencampur logika bisnis (*business logic*), akses data (*data access*), dan presentasi (*presentation/HTML*) dalam satu file yang sama (Spaghetti Code). Pisahkan ke dalam kelas-kelas atau lapisan (layer) yang berbeda (misalnya pola MVC).

---

## 🌐 Bahasa Komunikasi

- **Bahasa Respons Asisten AI**: Asisten AI wajib merespons dan berkomunikasi dalam **Bahasa Indonesia** di seluruh percakapan dengan pengguna.
- **Bahasa Kode vs Bahasa Antarmuka (UI)**:
  - **Sisi Teknis (Kode/DB)**: Seluruh penamaan tabel database, kolom, nama kelas, metode, variabel, file penunjang, komentar kode, dan dokumentasi teknis wajib menggunakan **Bahasa Inggris**.
  - **Sisi Tampilan (User View/UI)**: Seluruh tampilan visual, label formulir, placeholder input, pesan peringatan, notifikasi sukses/gagal, dan teks tombol wajib menggunakan **Bahasa Indonesia**.

---

## 🧪 Alur Kerja Pengguna (User Workflow)

1. **Brainstorming & Perencanaan**: Eksplorasi kebutuhan dan susun rencana arsitektur teknis sebelum menulis kode.
2. **Persetujuan**: Minta persetujuan eksplisit dari pengguna sebelum mengeksekusi perubahan besar.
3. **Implementasi**: Tulis kode sesuai rencana dengan mengacu pada standar PSR dan keamanan.
4. **Testing & Validasi**: Jalankan pengujian dan pastikan fitur berjalan sesuai yang diharapkan tanpa error.
5. **Commit Message**: Buat pesan komit Git yang jelas dan deskriptif setelah pekerjaan selesai.
