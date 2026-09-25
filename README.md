# ⚡ LORD ENERGI — Misi Menyelamatkan Energy Core

Game edukasi IPA berbasis web untuk siswa **SMP Kelas VIII**, materi **Energi Potensial, Energi Kinetik, dan Transformasi Energi**, dibangun dengan model **Discovery Learning** (Stimulus → Pengumpulan Data → Pengolahan Data → Boss Battle). Game dimainkan **secara berkelompok (3–6 orang per kelompok)**.

Game ini adalah aplikasi **frontend murni** (HTML/CSS/JS, tanpa build step) yang bisa langsung dijalankan di GitHub Pages, dan mengirim hasil belajar siswa ke **Google Spreadsheet** melalui **Google Apps Script Web App**.

---

## 1. Struktur Proyek

```
lord-energi/
│
├── index.html              → Identitas siswa + opening story
├── game.html                → Seluruh tahap Discovery Learning + Boss Battle
├── dashboard.html           → Dashboard Guru
│
├── css/
│   └── style.css            → Tema visual "Science Adventure"
│
├── js/
│   ├── questions.js          → Bank soal & data eksperimen
│   ├── discovery.js          → Rumus fisika + rendering simulasi
│   ├── game.js               → Controller utama game (state, navigasi, skor)
│   ├── dashboard.js          → Logic Dashboard Guru (statistik, grafik, tabel)
│   └── api.js                → Koneksi ke Google Apps Script
│
├── google-apps-script/
│   └── Code.gs               → Kode backend Apps Script (salin ke Spreadsheet)
│
├── assets/
│   ├── images/
│   ├── icons/
│   └── audio/
│
└── README.md
```

> Game **tidak membutuhkan Node.js atau server** apa pun untuk berjalan — cukup file statis, cocok untuk GitHub Pages.

---

## 2. Alur Permainan

```
LOGIN SISWA (index.html)
      ↓
OPENING STORY
      ↓
STIMULUS — Misteri Energy Core          (game.html)
      ↓
PENGUMPULAN DATA — Energy Investigation
      ↓
PENGOLAHAN DATA — Energy Analyst
      ↓
TRANSFORMASI ENERGI — Roller Coaster
      ↓
DISCOVERY CHECK (Benar/Salah)
      ↓
BOSS BATTLE — Lord Energi
      ↓
FINAL RESULT → dikirim ke Google Spreadsheet
```

Sesuai prinsip Discovery Learning: **rumus Ep = m×g×h dan Ek = ½×m×v² baru ditampilkan setelah siswa menemukan polanya sendiri** dari data eksperimen, bukan di awal.

---

## 3. Sistem Skor

| Aktivitas | Poin |
|---|---|
| Eksperimen berhasil (per misi) | +50 |
| Analisis benar | +100 |
| Perhitungan benar (validasi rumus) | +100 |
| Boss Battle benar (per serangan) | +150 |
| Menyelesaikan semua level | +250 |

Badge otomatis berdasarkan persentase jawaban benar: **Energy Cadet** (<50%), **Junior Energy Hero** (50–74%), **Skilled Energy Hero** (75–89%), **Master Energy Hero** (≥90%).

---

## 4. Panduan Deployment — Langkah demi Langkah

### A. Membuat Google Spreadsheet & Menghubungkan Apps Script

1. **Buat Google Spreadsheet baru** di [sheets.google.com](https://sheets.google.com), beri nama misalnya `LORD ENERGI - Data Game`.
2. Kamu **tidak perlu membuat sheet/tab secara manual** — kode Apps Script akan otomatis membuat 4 sheet berikut saat pertama kali dipanggil: `STUDENTS`, `GAME_RESULTS`, `LEVEL_RESULTS`, `ANSWERS`. (Jika ingin membuatnya manual lebih dulu, gunakan nama dan urutan kolom persis seperti pada bagian 5 di bawah.)
3. Buka menu **Extensions → Apps Script**.
4. Hapus kode default (`Code.gs`) yang ada di editor, lalu **salin seluruh isi file [`google-apps-script/Code.gs`](google-apps-script/Code.gs)** dari repo ini ke editor tersebut.
5. Simpan project (ikon disket / `Ctrl+S`). Beri nama project, misalnya `LORD ENERGI API`.

### B. Deploy sebagai Web App

1. Di editor Apps Script, klik **Deploy → New deployment**.
2. Klik ikon gear ⚙ di samping "Select type", pilih **Web app**.
3. Isi konfigurasi:
   - **Description**: `LORD ENERGI API v1`
   - **Execute as**: `Me`
   - **Who has access**: `Anyone` *(wajib, agar game di GitHub Pages bisa mengirim data)*
4. Klik **Deploy**.
5. Saat diminta otorisasi, klik **Authorize access**, pilih akun Google-mu, lalu klik **Advanced → Go to (nama project) (unsafe)** jika muncul peringatan, lalu **Allow**. (Peringatan ini muncul karena script belum diverifikasi Google — wajar untuk script buatan sendiri.)
6. Setelah deploy selesai, **salin URL Web App** yang muncul (formatnya seperti `https://script.google.com/macros/s/XXXXXXXXXXXX/exec`).

> **Setiap kali kamu mengubah kode `Code.gs`**, kamu harus membuat **New deployment** baru (atau meng-edit deployment yang ada via **Deploy → Manage deployments → Edit → New version**) agar perubahan berlaku.

### C. Memasukkan URL Web App ke Game

1. Buka file `js/api.js`.
2. Ganti baris berikut:
   ```js
   const WEBAPP_URL = "GANTI_DENGAN_URL_WEB_APP_ANDA";
   ```
   dengan URL yang kamu salin di langkah B.6:
   ```js
   const WEBAPP_URL = "https://script.google.com/macros/s/XXXXXXXXXXXX/exec";
   ```
3. Simpan file. Lakukan ini **sebelum** melakukan deploy ke GitHub Pages (atau commit ulang setelah mengubahnya).

### D. Menguji Pengiriman Data

1. Buka `index.html` langsung di browser (atau jalankan local server sederhana, misalnya `npx serve` / Live Server di VS Code — ini opsional, hanya untuk uji coba lokal).
2. Isi identitas, mainkan game sampai selesai (Screen **Final Result**).
3. Buka kembali Google Spreadsheet-mu — sheet `STUDENTS`, `GAME_RESULTS`, `LEVEL_RESULTS`, dan `ANSWERS` seharusnya sudah terisi baris baru.
4. Jika data tidak muncul, periksa:
   - URL di `js/api.js` sudah benar dan diakhiri `/exec`.
   - Deployment Web App diset **Who has access: Anyone**.
   - Buka Console browser (F12) untuk melihat pesan error jaringan.

### E. Menghubungkan Game dengan GitHub Pages

1. Buat repository baru di GitHub, misalnya `lord-energi`.
2. Upload seluruh isi folder proyek ini (`index.html`, `game.html`, `dashboard.html`, folder `css/`, `js/`, `assets/`, `google-apps-script/`, `README.md`) ke repository tersebut.
3. Buka **Settings → Pages** pada repository.
4. Pada **Source**, pilih branch `main` (atau `master`) dan folder `/ (root)`.
5. Klik **Save**. Tunggu beberapa menit, lalu GitHub akan memberikan URL publik seperti:
   ```
   https://<username-github>.github.io/lord-energi/
   ```
6. Bagikan URL tersebut ke siswa. Dashboard guru dapat diakses di:
   ```
   https://<username-github>.github.io/lord-energi/dashboard.html
   ```

---

## 5. Struktur Google Spreadsheet

### Sheet `STUDENTS`
| Timestamp | Nama Kelompok | Kelas | Anggota | Session ID |
|---|---|---|---|---|

> Game ini dimainkan **berkelompok (3–6 orang)**. Kolom `Anggota` berisi nama-nama anggota kelompok dipisahkan koma.

### Sheet `GAME_RESULTS`
| Timestamp | Session ID | Nama | Kelas | Total Score | Accuracy | Waktu Bermain | Badge | Status |
|---|---|---|---|---|---|---|---|---|

### Sheet `LEVEL_RESULTS`
| Session ID | Nama | Level | Tahap Discovery Learning | Score | Correct | Incorrect | Time |
|---|---|---|---|---|---|---|---|

### Sheet `ANSWERS`
| Session ID | Nama | Question ID | Tahap | Jawaban | Jawaban Benar | Status |
|---|---|---|---|---|---|---|

---

## 6. Dashboard Guru

`dashboard.html` menampilkan:

- **Kartu statistik**: total siswa, siswa yang sudah bermain, siswa yang menyelesaikan game, rata-rata skor, rata-rata persentase.
- **6 grafik**: skor siswa, rata-rata hasil per tahap (Stimulus, Pengumpulan Data, Pengolahan Data, Boss Battle) per kelas, dan perbandingan pemahaman Energi Potensial vs Energi Kinetik.
- **Tabel siswa** dengan pencarian nama, filter kelas, sorting per kolom (klik judul kolom), dan tombol **Refresh Data**.

Jika `WEBAPP_URL` belum dikonfigurasi, dashboard otomatis menampilkan **data contoh (dummy)** agar tetap bisa di-preview tanpa backend.

> **Catatan tahap Stimulus**: sesuai prinsip Discovery Learning, tahap Stimulus adalah tahap observasi murni (tanpa soal berskor) — sehingga nilai "Rata-rata Hasil Stimulus" pada dashboard akan bernilai 0 sampai kamu menambahkan penilaian tambahan pada tahap tersebut jika diinginkan.

---

## 7. Kustomisasi Lanjutan

- **Menambah/mengubah soal**: edit `js/questions.js` (bank soal Boss Battle, Analisis, Discovery Check, dan data eksperimen).
- **Mengubah tampilan**: edit `css/style.css` (variabel warna di bagian `:root`).
- **Mengubah bobot skor**: edit objek `SKOR` di `js/questions.js`.
- **Menambahkan audio/gambar**: taruh file di `assets/audio/` atau `assets/images/`, lalu referensikan di HTML/JS terkait.

---

## 8. Troubleshooting

| Masalah | Kemungkinan Penyebab |
|---|---|
| Data tidak masuk ke Spreadsheet | `WEBAPP_URL` belum diganti, atau akses deployment bukan "Anyone" |
| Dashboard kosong / menampilkan data contoh | `WEBAPP_URL` belum dikonfigurasi, atau deployment GET gagal (cek console browser) |
| Redirect otomatis ke `index.html` saat buka `game.html` | Siswa belum mengisi identitas — identitas disimpan di `sessionStorage`, akan hilang jika tab ditutup |
| Perubahan `Code.gs` tidak berpengaruh | Lupa membuat *New deployment* / *New version* setelah edit kode |

---

Selamat menyelamatkan Energy Core! ⚡
