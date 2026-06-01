# 🛏️ Smart Pillow — Sistem Rekod Komunikasi AAC + NFC
### Panduan Setup Firebase (Penuh)

---

## Langkah 1 — Buat Projek Firebase

1. Pergi ke [https://console.firebase.google.com](https://console.firebase.google.com)
2. Klik **"Add project"** → Beri nama (cth: `smart-pillow`)
3. Google Analytics: pilih ya atau tidak (pilihan bebas) → **Create project**

---

## Langkah 2 — Daftarkan Web App & Dapatkan Config

1. Di halaman projek, klik ikon **`</>`** (Web)
2. Beri nama app cth: `Smart Pillow Web`
3. **Jangan** centang Firebase Hosting (kita guna GitHub Pages/Vercel)
4. Klik **Register app**
5. Anda akan nampak blok seperti ini — **salin kesemuanya**:

```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",
  authDomain:        "smart-pillow-xxxx.firebaseapp.com",
  projectId:         "smart-pillow-xxxx",
  storageBucket:     "smart-pillow-xxxx.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abcdef"
};
```

6. Buka `index.html`, cari bahagian berlabel:
   ```
   // ─── STEP 1: REPLACE THIS ENTIRE OBJECT WITH YOUR FIREBASE CONFIG ───
   ```
7. **Gantikan** keseluruhan objek `firebaseConfig` dengan nilai anda

---

## Langkah 3 — Aktifkan Firebase Authentication

1. Di Firebase Console → **Authentication** (menu kiri)
2. Klik **"Get started"**
3. Tab **Sign-in method** → Klik **Email/Password**
4. Toggle **Enable** → **Save**

---

## Langkah 4 — Buat Firestore Database

1. Di Firebase Console → **Firestore Database** (menu kiri)
2. Klik **"Create database"**
3. Pilih **"Start in production mode"** → **Next**
4. Pilih lokasi terdekat (cth: `asia-southeast1` untuk Malaysia) → **Enable**

---

## Langkah 5 — Tetapkan Firestore Security Rules

1. Firestore → Tab **Rules**
2. **Gantikan** semua rules sedia ada dengan yang berikut:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    // Setiap pengguna hanya boleh akses data mereka sendiri
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null
                         && request.auth.uid == userId;
    }

  }
}
```

3. Klik **Publish**

---

## Langkah 6 — Deploy ke GitHub Pages (HTTPS wajib untuk NFC)

### Cara A: Manual (paling mudah)

1. Buat repositori baru di GitHub (public atau private)
2. Upload `index.html` ke root repositori
3. Settings → Pages → Source: **Deploy from branch** → `main` → `/` (root) → Save
4. Laman anda hidup di: `https://<username>.github.io/<repo-name>/`

### Cara B: GitHub Actions (auto-deploy)

Buat fail `.github/workflows/deploy.yml` dengan kandungan dari `deploy.yml` yang disertakan.

### Cara C: Vercel (paling pantas)

```bash
npx vercel --prod
```
Atau sambungkan repo di [vercel.com](https://vercel.com) — deploy automatik setiap push.

---

## Struktur Data Firestore

```
users/
  {uid}/
    students/
      {studentId}/
        name:        "Ahmad Naufal"
        diagnosis:   "Autism Spektrum"
        age:         "9 tahun"
        notes:       "..."
        active:      true
        createdAt:   Timestamp
        updatedAt:   Timestamp

    logs/
      {logId}/
        id:          "makan_minum"
        label:       "Saya nak makan / minum"
        icon:        "🍽️"
        studentId:   "abc123"
        studentName: "Ahmad Naufal"
        source:      "nfc" | "button"
        timestamp:   Timestamp
```

---

## WebNFC — Keperluan & Had

| Platform | Sokongan |
|----------|----------|
| Chrome for Android v89+ | ✅ Penuh |
| Chrome Desktop | ❌ Tidak |
| Safari / Firefox | ❌ Tidak |
| HTTP (bukan HTTPS) | ❌ Tidak berfungsi |
| GitHub Pages / Vercel | ✅ HTTPS automatik |

Jika peranti tidak ada NFC atau pelayar tidak sokong, app akan tunjukkan amaran dan **butang simulasi tetap berfungsi penuh** — merakam ke Firestore dengan `source: "button"`.

---

## Fail Projek

```
/
├── index.html          # SPA lengkap (Firebase + WebNFC + TTS)
├── deploy.yml          # GitHub Actions workflow
└── README.md           # Panduan ini
```

---

## Lesen

MIT — Bebas digunakan untuk tujuan pendidikan khas.
