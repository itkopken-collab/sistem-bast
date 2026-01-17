# 🚀 Deployment Guide - GitHub + Vercel

## Langkah-Langkah Deployment

### 1️⃣ Push ke GitHub

```bash
# Pastikan Anda di direktori project
cd /home/z/my-project

# Cek status
git status

# Jika ada perubahan baru, commit dulu:
git add .
git commit -m "Your commit message"

# Tambahkan remote GitHub (GANTI dengan URL repository Anda)
git remote add origin https://github.com/USERNAME/sistem-bast-kedai-kopi.git

# Atau jika sudah ada remote, update:
git remote set-url origin https://github.com/USERNAME/sistem-bast-kedai-kopi.git

# Push ke GitHub
git push -u origin main
```

### 2️⃣ Setup Database Production (FREE!)

#### **Opsi A: Supabase (RECOMMENDED - Free)**

1. Buka [supabase.com](https://supabase.com)
2. Sign up / Login dengan GitHub
3. Click **"New Project"**
4. Fill in:
   - Name: `bast-kedai-kopi`
   - Database Password: (buat password kuat - SIMPAN!)
   - Region: Pilih yang terdekat (Singapore/Indonesia)
5. Wait 1-2 minutes for project creation
6. Setelah ready:
   - Go to **Settings** → **Database**
   - Copy **Connection String** (format: `postgresql://postgres.project-ref:password@aws-0-ap-southeast-1.pooler.supabase.com:6543/postgres`)
   - Ini adalah `DATABASE_URL` Anda!

#### **Opsi B: Neon (Alternative - Free)**

1. Buka [neon.tech](https://neon.tech)
2. Click **"Sign Up"** → **"Start for Free"**
3. Connect dengan GitHub/Google
4. Click **"Create a project"**
5. Name: `bast-kedai-kopi`
6. Copy connection string yang muncul

### 3️⃣ Update Prisma Schema untuk Production

Edit file `prisma/schema.prisma`:

```prisma
datasource db {
  provider = "postgresql"  // GANTI dari "sqlite" ke "postgresql"
  url      = env("DATABASE_URL")
}
```

```bash
# Jalankan di terminal
npm install pg  # PostgreSQL client
bun run db:generate
```

### 4️⃣ Setup Gmail SMTP (FREE)

1. Login ke [myaccount.google.com](https://myaccount.google.com)
2. Go to **Security**
3. Enable **2-Step Verification** (jika belum)
4. Scroll ke **"App passwords"**
5. Click **"Generate"**
6. Select app: Mail → Other (custom name) → Enter: "Sistem BAST"
7. Copy 16-character password yang muncul
8. Ini adalah `SMTP_PASS` Anda!

**CATATAN:**
- Jangan gunakan password Gmail biasa
- App Password hanya muncul sekali - SIMPAN!
- Anda bisa membuat banyak app passwords

### 5️⃣ Deploy ke Vercel (FREE)

#### **Langkah A: Connect ke Vercel**

1. Buka [vercel.com](https://vercel.com)
2. Click **"Sign Up"** → Login dengan **GitHub**
3. Vercel akan otomatis connect ke akun GitHub Anda
4. Click **"Add New..."** → **"Project"**

#### **Langkah B: Import Repository**

1. Dari list repository, pilih `sistem-bast-kedai-kopi`
2. Vercel akan otomatis detect Next.js project
3. Click **"Import"**

#### **Langkah C: Configure Project**

**Framework Preset:** Next.js (auto-detected)

**Root Directory:** `./`

**Build Command:** (biarkan default)
```
bun run build
```

**Output Directory:** (biarkan default)
```
.next
```

Click **"Continue"**

#### **Langkah D: Environment Variables (PENTING!)**

Di tab **Environment Variables**, tambahkan semua ini:

```
DATABASE_URL
```
Value: `postgresql://postgres.project-ref:password@host:6543/postgres` (dari Supabase/Neon)

```
SMTP_HOST
```
Value: `smtp.gmail.com`

```
SMTP_PORT
```
Value: `587`

```
SMTP_SECURE
```
Value: `false`

```
SMTP_USER
```
Value: `your-email@gmail.com`

```
SMTP_PASS
```
Value: `xxxx xxxx xxxx xxxx` (16-character App Password dari Gmail)

```
SMTP_FROM
```
Value: `"Sistem BAST Kedai Kopi" <no-reply@kedai-kopi.com>`

```
NEXT_PUBLIC_APP_URL
```
Value: `https://your-app.vercel.app`
*(Catat URL ini dari deployment Vercel)*

```
IT_SUPPORT_EMAIL
```
Value: `it-support@yourdomain.com` atau email pribadi untuk test

```
IT_SUPPORT_NAME
```
Value: `IT Support`

```
IT_MANAGER_EMAIL
```
Value: `it-manager@yourdomain.com` atau email pribadi untuk test

```
IT_MANAGER_NAME
```
Value: `IT Manager`
```

**PENTING:** Check box **"Production"**, **"Preview"**, dan **"Development"** untuk semua environment variables!

Click **"Add"** setelah setiap variable.

#### **Langkah E: Deploy**

1. Click **"Deploy"**
2. Tunggu 2-5 menit untuk proses build & deploy
3. Vercel akan menampilkan progress log
4. Setelah selesai, Anda akan mendapatkan URL seperti:
   ```
   https://sistem-bast-kedai-kopi.vercel.app
   ```

### 6️⃣ Setup Database Migration di Vercel

Setelah deploy pertama berhasil:

1. Go to **Vercel Dashboard** → Project Anda
2. Tab **Settings** → **Environment Variables**
3. Pastikan `DATABASE_URL` sudah correct
4. Open **Deployments** tab
5. Klik **"..."** pada deployment terbaru → **"Redeploy"**

Atau gunakan Vercel CLI untuk migration:

```bash
# Install Vercel CLI
npm i -g vercel

# Login
vercel login

# Jalankan database migration
vercel env pull .env.local
bun run db:push
```

### 7️⃣ Test Production Deployment

1. Buka URL Vercel Anda (misal: `https://sistem-bast-kedai-kopi.vercel.app`)
2. Test form submission:
   - Isi form lengkap
   - Submit
   - Catat No BAST yang muncul
3. Test tracking:
   - Gunakan No BAST untuk tracking status
4. Test email:
   - Cek email IT Support/Manager yang Anda set di environment variables
   - Test approve/reject workflow

---

## ✅ Checklist Sebelum Go-Live

- [ ] GitHub repository sudah dibuat
- [ ] Code sudah push ke GitHub
- [ ] Database production sudah setup (Supabase/Neon)
- [ ] Prisma schema di-update ke PostgreSQL
- [ ] Gmail App Password sudah didapatkan
- [ ] Vercel project sudah connected
- [ ] Semua environment variables sudah di-set di Vercel
- [ ] Deploy pertama berhasil
- [ ] URL production sudah dicatat
- [ ] Form submission test berhasil
- [ ] Email notifications test berhasil
- [ ] Approval workflow test berhasil
- [ ] Tracking feature test berhasil

---

## 🎯 Free Tier Limitations

### Vercel Free Tier:
✅ **Cukup untuk:**
- 100GB bandwidth per month
- 6 build hours per month
- Unlimited deployments
- Serverless functions
- Custom domain (basic)

❌ **Tidak dapat:**
- Custom analytics (butuh Pro)
- Advanced caching (butuh Pro)
- Dedicated team features

### Supabase Free Tier:
✅ **Cukup untuk:**
- 500MB database storage
- 1GB file storage
- 50,000 monthly active users
- 2 concurrent connections
- Unlimited API requests

❌ **Tidak dapat:**
- Daily backups (butuh Pro)
- Pause project (auto-hibernate after 1 week inactive)

**Untuk aplikasi BAST kedai kopi: SEMUANYA CUKUP!**

---

## 🔄 Workflow Update Setelah Deploy

### **Untuk Update Aplikasi:**

```bash
# 1. Edit code di local
# ... buat perubahan ...

# 2. Test lokal
bun run dev

# 3. Commit changes
git add .
git commit -m "feat: Add new feature"

# 4. Push ke GitHub
git push

# 5. Vercel otomatis deploy!
# Check progress di Vercel Dashboard
```

### **Preview Deployments:**

Setiap kali Anda push ke branch baru, Vercel akan membuat preview deployment:

```bash
# Buat branch baru
git checkout -b feature/new-approvals

# ... buat perubahan ...

git add .
git commit -m "feat: Add new approval flow"
git push origin feature/new-approvals

# Vercel akan otomatis deploy preview
# Share preview URL untuk review sebelum merge
```

---

## 🆘 Troubleshooting

### Error: "DATABASE_URL not found"
- Pastikan environment variable sudah di-set di Vercel
- Re-deploy setelah menambah env var
- Check Vercel Dashboard → Settings → Environment Variables

### Error: "SMTP authentication failed"
- Verify SMTP_PASS correct (16-character App Password)
- Check SMTP_USER correct (email Gmail)
- Ensure 2-Step Verification enabled di Gmail
- Check SMTP_HOST = smtp.gmail.com, SMTP_PORT = 587

### Build failed
- Check error logs di Vercel Dashboard → Deployments
- Pastikan package.json dependencies correct
- Run `bun run lint` locally untuk check errors

### Email tidak terkirim
- Test SMTP settings dengan tool online seperti [smtpjs.com](https://www.smtpjs.com/)
- Check spam folder di email penerima
- Verify FROM address valid

### Database connection error
- Verify DATABASE_URL format correct
- Check Supabase/Neon project active
- Test connection dengan psql tool

---

## 📞 Support

Jika mengalami masalah:
1. Check Vercel Deployment Logs
2. Check GitHub Actions logs (jika ada)
3. Review environment variables
4. Test SMTP secara lokal dengan Postman/curl
5. Hubungi IT Support untuk bantuan

---

**Last Updated:** 2025
**Version:** 1.0
