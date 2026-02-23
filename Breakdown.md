# 📌 Project Breakdown

**Website Manajemen Templating Artikel (Google Apps Script + Drive + Spreadsheet)**

---

# PHASE 0 — Finalisasi Fondasi

## 0.1 Tetapkan Scope Final

* 2 jurnal (IMM, IMT)
* ±4 user (Editor & Admin)
* Status: `NEW`, `CLAIMED`, `DONE`
* Revisi disimpan (tidak dihapus)
* PDF di-generate on-demand
* Tidak ada fitur enterprise tambahan

## 0.2 Siapkan Struktur Google Drive

```
/Manajemen-Artikel
   /SYSTEM
   /REPOSITORY
      /IMM
      /IMT
```

> Jangan mulai coding sebelum struktur ini fix.

---

# PHASE 1 — Setup Database (Spreadsheet)

## 1.1 Buat Spreadsheet: `DATABASE`

### Sheet: ARTICLES

```
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
```

---

### Sheet: ISSUES

```
ID
JOURNAL_ID
ISSUE_NAME
DRIVE_FOLDER_ID
STATUS
CREATED_AT
CLOSED_AT
```

---

### Sheet: USERS

```
EMAIL
ROLE
JOURNAL_IDS
```

---

### Sheet: LOGS

```
ID
ARTICLE_ID
ACTION
USER
TIMESTAMP
NOTE
```

## 1.2 Tambahkan 4 user ke sheet USERS

Pastikan email benar. Salah satu huruf saja bisa bikin sistem “diam tapi gagal”.

---

# PHASE 2 — Setup Apps Script Project

## 2.1 Buat Project Web App

* Simpan di folder `/SYSTEM`
* Deploy sebagai Web App

## 2.2 Buat File Konfigurasi (`config.gs`)

Simpan:

* `DATABASE_ID`
* `ROOT_REPOSITORY_ID`
* `JOURNAL_FOLDER_IDS`

> Hindari hardcode ID di banyak file.

---

# PHASE 3 — Backend Modular Structure

Pisahkan fungsi agar mudah dirawat.

---

## 3.1 `authService.gs`

Fungsi:

* `getCurrentUser()`
* `validateUser()`
* `getUserRole()`

Jika email tidak terdaftar → semua aksi diblokir.

---

## 3.2 `journalService.gs`

Fungsi:

* `getJournals()`
* `validateJournalAccess(user, journalId)`

---

## 3.3 `issueService.gs`

Fungsi:

* `createIssue()`
* `getIssuesByJournal()`
* `generateIssueFolder()`

Validasi:

* Nama issue unik
* Format benar

---

## 3.4 `articleService.gs`

Fungsi inti:

* `createArticle()`
* `getArticles()`
* `claimArticle()`
* `takeoverArticle()`
* `markDone()`
* `incrementRevision()`
* `updateStatus()`

Semua operasi tulis wajib pakai:

```javascript
const lock = LockService.getDocumentLock();
lock.waitLock(30000);
```

---

## 3.5 `fileService.gs`

Fungsi:

* `uploadOriginalDocx()`
* `uploadRevisionDocx()`
* `generatePDF()`
* `renameFile()`
* `createArticleFolder()`

Validasi:

* Hanya `.docx`
* Rename otomatis sesuai format
* Jangan hapus revisi lama

---

## 3.6 `logService.gs`

Fungsi:

* `writeLog(action, articleId, note)`
* `writeError(error)`

Semua aksi penting wajib dicatat.

---

# PHASE 4 — UI Implementation

## 4.1 Struktur Layout

```
[ TOP BAR ]
[ SUMMARY CARDS ]
[ FILTER ]
[ ARTICLE TABLE ]
```

---

## 4.2 Top Bar (Wajib)

Menampilkan:

* Nama
* Email
* Role
* Jurnal akses

Jika user tidak terdaftar:

* Tampilkan banner merah
* Disable semua tombol

---

## 4.3 Dashboard Cards

4 kartu utama:

* Total Artikel
* NEW
* CLAIMED
* DONE

Opsional:

* Pie chart status (Google Charts)

---

## 4.4 Article Table

Kolom:

* ID
* Journal
* Issue
* OJS_ID
* Status (warna)
* Claimed_By
* REV_COUNT
* Action

Warna:

* NEW → Abu
* CLAIMED → Kuning
* DONE → Hijau

---

## 4.5 Article Detail View

Menampilkan:

* Metadata
* Status
* Riwayat revisi
* Tombol aksi:

  * Upload
  * Reupload
  * Claim
  * Takeover
  * Generate PDF
  * Done

Semua aksi penting pakai confirmation modal.

---

# PHASE 5 — Workflow Implementation

Urutan implementasi:

1. Create Issue
2. Create Article
3. Upload Original
4. Claim
5. Reupload Revision
6. Generate PDF
7. Mark Done

> Jangan bangun semua sekaligus. Uji tiap langkah.

---

# PHASE 6 — Validation Layer

Tambahkan validasi:

* File hanya `.docx`
* OJS_ID unik per jurnal
* Claim hanya jika `STATUS = NEW`
* DONE hanya oleh `CLAIMED_BY`
* Cegah double claim

---

# PHASE 7 — Error Handling

Semua fungsi server-side:

```javascript
try {
  ...
} catch (e) {
  logError(e);
  return { success: false, message: "Terjadi kesalahan sistem." };
}
```

Jangan kirim error mentah ke UI.

---

# PHASE 8 — Testing Scenario (Wajib)

Simulasikan:

* Dua user claim bersamaan
* User upload file salah
* Generate PDF berulang
* Login dengan akun tidak terdaftar

Kalau lolos semua → sistem stabil.

---

# PHASE 9 — Deployment

Deploy sebagai:

* Execute as: Me
* Access: Specific users

Whitelist hanya email resmi.

---

# PHASE 10 — Maintenance Checklist

Setiap 6 bulan:

* Backup Spreadsheet
* Cek log error
* Cek folder duplikat
* Bersihkan file tidak terpakai

---

# Timeline Realistis

| Tahap               | Estimasi       |
| ------------------- | -------------- |
| Setup Database      | 1 hari         |
| Backend Modular     | 3–4 hari       |
| UI                  | 2–3 hari       |
| Testing & Hardening | 2 hari         |
| **Total**           | ±10 hari kerja |

---

# Prioritas Implementasi

1. Database & Auth
2. Claim System
3. Upload & Rename File
4. Revision System
5. PDF Generation
6. UI Polish

Kalau dibalik, hasilnya UI cantik tapi backend berantakan.

---

# Ringkasan Akhir

Sistem ini dirancang untuk:

* Stabil untuk skala kecil
* Aman untuk multi-user
* Minim konflik
* Mudah dirawat 3–7 tahun
* Tidak over-engineered

Bangun rapi dari awal, dan Anda tidak perlu bongkar ulang setahun lagi.
