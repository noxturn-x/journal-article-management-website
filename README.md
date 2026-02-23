# Website Manajemen Artikel
1️⃣ Deskripsi Sistem

Website ini adalah sistem internal berbasis Google Apps Script, Google Spreadsheet, dan Google Drive untuk mengelola proses templating artikel jurnal.

Sistem ini dirancang untuk:
Mengelola artikel per jurnal dan per issue
Mengatur proses claim editor
Mengelola upload dan revisi file .docx
Menghasilkan PDF final secara on-demand
Mencatat log aktivitas
Mendukung multi-user (±4 user) dengan role editor dan admin

Sistem difokuskan pada:
Stabilitas
Pencegahan konflik user
Audit sederhana
Maintenance jangka panjang
Tanpa over-engineering

Skala saat ini:
2 jurnal
±10 artikel per jurnal per tahun
4 user aktif

2️⃣ Key Features
A. Manajemen Jurnal & Issue
Multi jurnal (IMM, IMT, dst.)
Issue dibuat dengan format:
[JOURNAL_ID]_VOL[angka]_NO[angka]_[tahun]

Contoh:
IMM_VOL1_NO2_2026

Otomatis membuat folder issue di Google Drive

B. Manajemen Artikel
1. Pembuatan Artikel
Setiap artikel terhubung ke:
Jurnal
Issue
OJS_ID
OJS_ID unik per jurnal

2. Upload & Reupload File

Hanya menerima file .docx
Rename otomatis:
[JOURNAL_ID]-[OJS_ID].docx

Revisi:
[JOURNAL_ID]-[OJS_ID]-REV[REV_COUNT].docx

Revisi lama tidak dihapus (disimpan sebagai arsip)

3. Claim & Takeover
Artikel dapat di-claim oleh editor
Status berubah menjadi CLAIMED
Takeover tersedia dengan konfirmasi
Semua aktivitas tercatat di LOGS

4. Status Workflow (Sederhana & Tegas)
Status artikel:
NEW
CLAIMED
DONE

Hanya user yang meng-claim yang bisa menandai DONE.

5. PDF On-Demand
Konversi dari DOCX ke PDF hanya saat diminta
File PDF diberi nama:
[JOURNAL_ID]-[OJS_ID].pdf

Jika revisi belum berubah, PDF lama digunakan kembali

6. Log Aktivitas
Semua aksi penting tercatat:
UPLOAD
REUPLOAD
CLAIM
TAKEOVER
DONE
PDF_GENERATE
ERROR

7. Role & Keamanan
Role:
Editor
Admin

Fitur keamanan:
Identitas user aktif selalu tampil
Aksi disesuaikan dengan role
User tidak terdaftar → semua tombol dinonaktifkan
LockService untuk mencegah konflik saat update

3️⃣ Struktur Data
A. Spreadsheet DATABASE (1 File Utama)
Sheet: ARTICLES
ID
JOURNAL_ID
ISSUE_ID
OJS_ID
TITLE
STATUS
CLAIMED_BY
CLAIMED_AT
DONE_AT
REV_COUNT
CURRENT_FILE_ID
PDF_FILE_ID
DRIVE_FOLDER_ID
CREATED_AT
UPDATED_AT

Sheet: ISSUES
ID
JOURNAL_ID
ISSUE_NAME
DRIVE_FOLDER_ID
STATUS
CREATED_AT
CLOSED_AT

Sheet: USERS
EMAIL
ROLE
JOURNAL_IDS

Sheet: LOGS
ID
ARTICLE_ID
ACTION
USER
TIMESTAMP
NOTE

4️⃣ Struktur Google Drive
   /Struktur Google Drive
   
      /Manajemen-Artikel
         /SYSTEM
            MASTER_CONFIG   
            DATABASE
         /REPOSITORY
            /IMM   
               /ISSUES
                  /IMM_VOL1_NO2_2026
                     /IMM-123
                        IMM-123.docx
                        IMM-123-REV1.docx
                        IMM-123.pdf
            /IMT
               /ISSUES

Struktur dipisahkan antara:
File sistem (database)
File artikel (repository)

5️⃣ Struktur Website (UI)
A. Top Bar
Menampilkan:
Nama user
Email
Role
Jurnal akses

B. Dashboard
Summary cards:
Total artikel
NEW
CLAIMED
DONE
Chart status (opsional)
Filter jurnal & issue

C. Artikel List
Table sederhana
Status berwarna
Tombol aksi kontekstual

D. Detail Artikel
Informasi metadata
Informasi status & claim
Upload / Reupload
Claim / Takeover
Generate PDF
Download file

6️⃣ Karakter Sistem
Sistem ini dirancang sebagai:
Internal workflow tool
Stabil untuk 3–7 tahun
Minim konflik multi-user
Mudah dirawat
Tidak over-complex
Tidak bergantung pada layanan eksternal selain Google Workspace
Kesimpulan

Website ini adalah sistem manajemen templating artikel berbasis Google Workspace yang:
Mendukung multi jurnal
Mengelola workflow editor
Mengontrol revisi file
Menghasilkan PDF final
Memiliki audit log
Aman untuk multi-user kecil
Tidak over-engineered

Jika nanti jumlah jurnal dan artikel meningkat signifikan, arsitektur dapat ditingkatkan tanpa perlu merombak total fondasi sistem.
