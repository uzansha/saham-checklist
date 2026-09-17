# Checklist Analisa Saham

Web app sederhana untuk mengisi data Keystats Stockbit dan mendapat kesimpulan otomatis (Kesehatan Fundamental, Valuasi, Price Action), tersimpan di Firestore.

## Isi folder

- `index.html` — aplikasinya (form input + logika kesimpulan + riwayat)
- `firebase-config.js` — tempat kamu isi kredensial Firebase (**wajib diisi**, app tidak akan jalan tanpa ini)

## Langkah 1 — Buat project Firebase (gratis)

1. Buka [console.firebase.google.com](https://console.firebase.google.com), login pakai akun Google.
2. Klik **Add project** → kasih nama bebas (misal `saham-checklist`) → lanjut sampai selesai (bisa skip Google Analytics).
3. Setelah project dibuat, klik ikon **`</>`** (Web app) untuk mendaftarkan web app baru. Kasih nama bebas, **tidak perlu** centang Firebase Hosting (karena kamu hosting di GitHub Pages).
4. Firebase akan menampilkan kode `firebaseConfig` seperti ini:
   ```js
   const firebaseConfig = {
     apiKey: "AIzaSy...",
     authDomain: "saham-checklist-xxxx.firebaseapp.com",
     projectId: "saham-checklist-xxxx",
     storageBucket: "saham-checklist-xxxx.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef"
   };
   ```
5. Copy nilai-nilai itu ke file `firebase-config.js` di folder ini, gantikan placeholder `GANTI_DENGAN_...`.

## Langkah 2 — Aktifkan Firestore Database

1. Di Firebase Console, buka menu **Build > Firestore Database** di sidebar kiri.
2. Klik **Create database**.
3. Pilih **Start in test mode** (biar cepat jalan dulu — nanti bisa diperketat, lihat catatan keamanan di bawah).
4. Pilih lokasi server (misal `asia-southeast2` / Jakarta biar dekat), klik **Enable**.

## Langkah 3 — Upload ke GitHub & aktifkan GitHub Pages

1. Buat repository baru di GitHub (bisa public atau private — kalau private, GitHub Pages butuh akun berbayar; kalau public gratis).
2. Upload 2 file (`index.html` dan `firebase-config.js`) ke repo itu — bisa lewat GitHub web (**Add file > Upload files**) atau `git push` kalau familiar command line.
3. Buka **Settings > Pages** di repo tersebut.
4. Di bagian **Source**, pilih branch `main` dan folder `/root`, klik **Save**.
5. Tunggu 1-2 menit, GitHub akan kasih link seperti `https://username.github.io/nama-repo/` — itu link app kamu.

## Catatan keamanan (penting, tapi opsional untuk penggunaan pribadi)

Karena `firebase-config.js` ikut ter-upload ke repo publik, API key-nya akan terlihat orang lain. Ini **wajar dan aman** untuk Firebase (API key web Firebase memang didesain untuk terlihat publik), TAPI supaya orang lain tidak bisa iseng menulis/menghapus data di Firestore kamu, sebaiknya atur **Firestore Security Rules**:

1. Firebase Console > Firestore Database > tab **Rules**.
2. Untuk penggunaan pribadi tanpa login, minimal batasi rules seperti ini (ganti dari test mode):
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /analisa_saham/{docId} {
         allow read, write: if true;
       }
     }
   }
   ```
   Ini masih terbuka untuk siapa saja yang tahu URL Firestore-nya (jarang ditemukan orang random), tapi kalau mau lebih aman lagi, pertimbangkan menambah Firebase Authentication di masa depan.

## Cara pakai

1. Buka link GitHub Pages kamu.
2. Isi kode saham + semua angka dari Keystats Stockbit sesuai section (Kesehatan Fundamental, Valuasi, Price Action).
3. Klik **Simpan & Analisa** — hasil kesimpulan muncul otomatis dan tersimpan ke riwayat.
4. Riwayat bisa dibuka lagi kapan saja selama internetmu terhubung ke Firestore.

## Kriteria yang dipakai sistem

| Kriteria | Lolos jika |
|---|---|
| ROE | > 15% |
| DER | < 1 |
| CFO vs Net Income | CFO ≥ Net Income |
| PER | ≤ 20x |
| PBV vs ROE | Tidak (PBV > 2 dan ROE < 15%) |
| Price Action | Harga tidak naik, ATAU harga naik dan didukung laba kuartal membaik |

Skor 6/6 = layak dipertimbangkan. Skor ≤3/6 = perlu riset tambahan. Di antaranya = hasil campuran, tinjau kriteria yang gagal satu-satu.
