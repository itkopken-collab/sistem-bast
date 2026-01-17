# Sistem BAST Kedai Kopi

Aplikasi web untuk sistem Berita Acara Serah Terima (BAST) dengan workflow approval untuk kedai kopi.

## 🎯 Fitur

- Form BAST lengkap dengan tracking approval
- Workflow approval bertingkat (IT Support → IT Manager)
- Notifikasi email otomatis dengan PDF
- Status tracking real-time
- Upload dokumentasi foto
- Field tambahan yang bisa dikustomisasi

## 🛠 Tech Stack

- **Frontend**: Next.js 15 + React 19 + TypeScript
- **UI Library**: shadcn/ui + Tailwind CSS
- **Database**: Prisma ORM + SQLite (development) / PostgreSQL (production)
- **Email**: Nodemailer dengan SMTP
- **PDF**: jsPDF

## 📋 Prasyarat

- Node.js 18+ atau Bun
- Akun GitHub
- Akun Vercel (untuk deployment)
- Akun email dengan SMTP (Gmail recommended)

## 🚀 Cara Deploy ke Production

### 1. Setup Database Production

Aplikasi ini menggunakan SQLite untuk development, tapi untuk production gunakan PostgreSQL:

#### Opsi A: Supabase (Free & Recommended)
1. Daftar di [supabase.com](https://supabase.com)
2. Buat project baru
3. Dapatkan **Database URL** dari Settings → Database
4. Copy URL untuk dipakai di Vercel

#### Opsi B: Neon (Free & Good Alternative)
1. Daftar di [neon.tech](https://neon.tech)
2. Buat project PostgreSQL baru
3. Dapatkan **Connection String**

### 2. Push ke GitHub

```bash
# Inisialisasi git (jika belum)
git init

# Tambah semua file
git add .

# Commit pertama
git commit -m "Initial commit: Sistem BAST Kedai Kopi"

# Buat repository di GitHub, lalu:
git remote add origin https://github.com/USERNAME/REPO-NAME.git
git branch -M main
git push -u origin main
```

### 3. Setup Project di Vercel

1. Buka [vercel.com](https://vercel.com) dan login
2. Klik **"Add New..."** → **"Project"**
3. Import repository GitHub Anda
4. **Framework Preset**: Next.js (auto-detect)
5. Klik **"Continue"**

### 4. Konfigurasi Environment Variables di Vercel

Di tab **Environment Variables**, tambahkan:

```env
# Database (dari Supabase/Neon)
DATABASE_URL=postgresql://user:pass@host:5432/dbname

# SMTP Configuration
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_SECURE=false
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password
SMTP_FROM="Sistem BAST Kedai Kopi" <no-reply@kedai-kopi.com>

# App URL
NEXT_PUBLIC_APP_URL=https://your-app.vercel.app

# IT Support
IT_SUPPORT_EMAIL=it-support@yourdomain.com
IT_SUPPORT_NAME=IT Support

# IT Manager
IT_MANAGER_EMAIL=it-manager@yourdomain.com
IT_MANAGER_NAME=IT Manager
```

**PENTING:**
- Untuk Gmail, buat App Password di: https://myaccount.google.com/apppasswords
- `NEXT_PUBLIC_APP_URL` harus URL Vercel Anda (akan muncul setelah deploy)

### 5. Update Prisma Schema untuk Production

Edit `prisma/schema.prisma`:

```prisma
datasource db {
  provider = "postgresql"  // Ganti dari sqlite ke postgresql
  url      = env("DATABASE_URL")
}
```

### 6. Deploy Pertama

1. Klik **"Deploy"** di Vercel
2. Tunggu proses deployment
3. Buka URL yang diberikan (biasanya `https://your-project.vercel.app`)

### 7. Setup Database di Production

Setelah deploy pertama:

1. Buka Vercel Dashboard → Project Anda
2. Tab **Settings** → **Environment Variables**
3. Scroll ke **Postgres / Database** section
4. Klik **"Connect Database"** atau gunakan URL dari Supabase/Neon
5. Jalankan migration di Vercel:

Di Vercel, buka **Settings** → **Environment Variables** → Add variable:
- Name: `PRISMA_MIGRATION`
- Value: `bun run db:push` atau `prisma db push`

Atau gunakan Vercel Postgres jika Anda pakai itu.

## 📧 Setup Email (Gmail)

### Cara Mendapatkan App Password:

1. Login ke [myaccount.google.com](https://myaccount.google.com)
2. Go to **Security**
3. Scroll ke **2-Step Verification** → Pastikan enabled
4. Klik **App passwords**
5. Buat app baru (contoh: "Sistem BAST")
6. Copy password yang muncul (16 karakter)
7. Gunakan password ini sebagai `SMTP_PASS`

### Setup Email Provider Alternatif:

Jika tidak ingin pakai Gmail:
- **SendGrid**: [sendgrid.com](https://sendgrid.com) - Free 100 emails/day
- **Brevo**: [brevo.com](https://brevo.com) - Free 300 emails/day
- **Amazon SES**: [aws.amazon.com/ses](https://aws.amazon.com/ses) - Pay as you go

## 🔧 Development Local

```bash
# Install dependencies
bun install

# Setup database
bun run db:push

# Run dev server
bun run dev

# Aplikasi akan berjalan di http://localhost:3000
```

## 📱 Cara Menggunakan

### Untuk Vendor:
1. Buka aplikasi
2. Isi Form Pengajuan BAST
3. Upload foto dokumentasi
4. Submit dan catat No BAST yang muncul
5. Lacak status di tab "Lacak Status BAST"

### Untuk IT Support/Manager:
1. Buka email notifikasi
2. Review PDF BAST terlampir
3. Klik tombol Approve/Reject di email
4. Status akan terupdate otomatis

## 🐛 Troubleshooting

### Email tidak terkirim:
- Pastikan SMTP credentials benar
- Cek firewall/network settings
- Pastikan App Password Gmail sudah benar
- Cek spam folder email penerima

### Database error:
- Pastikan `DATABASE_URL` environment variable benar
- Run `bun run db:push` untuk sync schema
- Cek koneksi database di Vercel

### Upload file tidak berfungsi:
- Gunakan storage solution untuk production:
  - Vercel Blob: [vercel.com/docs/storage/vercel-blob](https://vercel.com/docs/storage/vercel-blob)
  - AWS S3
  - Cloudflare R2

## 📝 Catatan Penting untuk Production

1. **File Upload**: Saat ini file disimpan di `public/uploads`. Untuk production:
   - Gunakan Vercel Blob / AWS S3 / Cloudflare R2
   - Update API endpoint untuk upload ke cloud storage

2. **Database**: SQLite hanya untuk development. Production:
   - Gunakan PostgreSQL (Supabase/Neon recommended)
   - Update `prisma/schema.prisma` datasource

3. **Email**: Test dengan email production sebelum live:
   - Gunakan email testing service seperti Mailtrap untuk staging
   - Pastikan SPF/DKIM records setup untuk domain sendiri

4. **Security**:
   - Enable rate limiting di API routes
   - Add authentication untuk IT Support/Manager approval
   - Sanitize all user inputs
   - Add CSRF protection

## 📄 License

Private - Kedai Kopi

## 🤝 Support

Untuk bantuan, hubungi IT Support Kedai Kopi.
