# Sistem Absensi NFC

Sistem absensi sekolah berbasis kartu NFC, dibangun dengan **Next.js 16**, **Prisma ORM (SQLite)**, **Tailwind CSS 4**, **shadcn/ui**, dan **Web NFC API**.

## Fitur Utama

- **Kiosk Scanner NFC** — UI standby dengan animasi pulse, feedback visual & suara beep, log tap terbaru, serta fallback simulasi tap untuk testing tanpa hardware.
- **Auto-detect Masuk/Pulang** — Sistem menentukan tipe tap berdasarkan tap terakhir hari ini.
- **Status Otomatis** — Tepat waktu (sebelum 07:30) atau Terlambat.
- **Dashboard Statistik** — Stat cards, chart area 7 hari, distribusi siswa per kelas, aktivitas terkini.
- **Manajemen Siswa (CRUD)** — Tambah/edit/hapus siswa, search & filter kelas.
- **Manajemen Kartu NFC** — Daftar kartu baru, assign/unassign kartu ke siswa, lepas kartu.
- **Rekap Absensi** — Filter periode (hari ini / kemarin / 7 hari / 30 hari / custom), filter kelas & status, export CSV.
- **Login Admin** — Autentikasi dengan session 8 jam.
- **Responsif** — Mobile-first dengan sidebar drawer di layar kecil.

## Tech Stack

| Kategori | Teknologi |
|---|---|
| Framework | Next.js 16 (App Router) |
| Bahasa | TypeScript 5 |
| Styling | Tailwind CSS 4 + shadcn/ui (New York) |
| Database | Prisma ORM + SQLite |
| Chart | Recharts |
| Icon | Lucide React |
| Toast | Sonner |
| NFC | Web NFC API (browser native) |

## Struktur Folder

```
absensi-nfc/
├── prisma/
│   ├── schema.prisma          # Skema: Admin, Siswa, NFCCard, Attendance
│   └── seed.js                # Seed data (10 siswa, 13 kartu, 18 absensi)
├── public/                    # Aset statis
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   ├── absensi/tap/route.ts   # POST tap NFC
│   │   │   ├── users/route.ts         # CRUD siswa + kartu NFC
│   │   │   ├── attendance/route.ts    # Rekap absensi
│   │   │   ├── auth/route.ts          # Login admin
│   │   │   └── stats/route.ts         # Statistik dashboard
│   │   ├── layout.tsx        # Root layout (font, toaster)
│   │   ├── page.tsx          # Orchestrator view (landing/scanner/login/dashboard)
│   │   └── globals.css
│   ├── components/
│   │   ├── scanner/
│   │   │   └── ScannerView.tsx        # UI kiosk tap NFC
│   │   ├── dashboard/
│   │   │   ├── LandingView.tsx
│   │   │   ├── LoginView.tsx
│   │   │   ├── DashboardLayout.tsx    # Sidebar + header
│   │   │   ├── OverviewTab.tsx        # Statistik + chart
│   │   │   ├── SiswaTab.tsx           # CRUD siswa + kelola kartu
│   │   │   └── RekapTab.tsx           # Laporan + export CSV
│   │   └── ui/                         # shadcn/ui components
│   ├── hooks/
│   │   └── useWebNfc.ts               # Custom hook Web NFC API
│   └── lib/
│       ├── db.ts                       # Prisma client singleton
│       └── utils.ts                    # Helper (format tanggal, export CSV, auth)
├── .env                      # DATABASE_URL
├── package.json
├── tailwind.config.ts
├── tsconfig.json
├── postcss.config.mjs
├── next.config.ts
├── components.json
└── eslint.config.mjs
```

## Cara Menjalankan

### 1. Prasyarat
- Node.js 18+ (atau Bun)
- npm / pnpm / yarn / bun

### 2. Install Dependencies
```bash
npm install
# atau
bun install
```

### 3. Setup Database
File `.env` sudah berisi:
```
DATABASE_URL=file:./db/custom.db
```

Jalankan perintah berikut untuk membuat database & mengisi seed data:
```bash
npx prisma db push
node prisma/seed.js
# atau
bun run db:push
node prisma/seed.js
```

### 4. Jalankan Dev Server
```bash
npm run dev
# atau
bun run dev
```

Buka http://localhost:3000 di browser.

## Akun Demo

| Halaman | Akses |
|---|---|
| Login Admin | Username: `admin` · Password: `admin123` |

## UID Kartu NFC Demo (untuk Simulasi Tap)

```
04A3B2F1 — Ahmad Fauzi (X IPA 1)
04B4C3E2 — Siti Nurhaliza (X IPA 1)
04C5D4F3 — Budi Santoso (X IPA 2)
04D6E504 — Dewi Lestari (X IPS 1)
04E7F615 — Eko Prasetyo (XI IPA 1)
04F80726 — Fitri Handayani (XI IPA 2)
04091837 — Gilang Ramadhan (XI IPS 1)
041A2948 — Hana Pertiwi (XII IPA 1)
042B3A59 — Indra Gunawan (XII IPA 2)
043C4B6A — Jihan Salsabila (XII IPS 1)
CAD001AA — Kartu cadangan (unassigned)
CAD002BB — Kartu cadangan (unassigned)
CAD003CC — Kartu cadangan (unassigned)
```

Klik **"Simulasi Tap Kartu (Demo)"** di halaman scanner untuk mencoba tanpa hardware.

## Hardware NFC (Opsional)

Untuk integrasi dengan reader NFC sungguhan (mis. ACR122U):

### Opsi A: Web NFC API (Mobile)
Buka aplikasi di Chrome/Edge Android → klik **"Mulai Scan NFC"** → tempel kartu ke bagian belakang HP. UID kartu akan otomatis terkirim ke endpoint `/api/absensi/tap`.

### Opsi B: Reader USB (Desktop)
Reader ACR122U mengirim UID sebagai input keyboard. Anda bisa:
1. Fokus pada input manual di halaman scanner
2. Tempel kartu ke reader (UID otomatis terketik + Enter)
3. Atau buat mini-service WebSocket yang membaca PC/SC dan POST ke `/api/absensi/tap`

## Catatan Keamanan

- Hash password pada demo ini **sangat sederhana** — JANGAN dipakai di produksi.
- Untuk produksi: gunakan `bcrypt` / `argon2` + NextAuth.js dengan JWT httpOnly cookie.
- Database SQLite cocok untuk demo/single-server. Untuk skala besar, ganti ke PostgreSQL/MySQL.

## Lisensi

Bebas digunakan untuk keperluan edukasi & internal sekolah.
