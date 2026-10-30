# 📖 Panduan & Alur Belajar AI & RAG (Workflow Guide)

Dokumen ini memuat panduan lengkap, aturan alur kerja (*learning workflow*), serta kumpulan perintah (*cheat sheet*) untuk memandu proses belajar dari awal hingga selesai menggunakan GitHub Issues di repositori [Adibmaros/belajar-ai](https://github.com/Adibmaros/belajar-ai).

---

## 🎯 1. Prinsip Utama Belajar

1. **One Step at a Time**: Fokus selesaikan satu issue/tahap sebelum pindah ke tahap berikutnya. Jangan terburu-buru melompat ke tahap lanjut sebelum fondasi dipahami.
2. **Ubah, Ukur, Catat**: Setiap melakukan eksperimen kode (misal: mengubah threshold similarity, mengganti ukuran chunk, atau mencoba prompt baru), catat hipotesis dan hasil perubahannya.
3. **Hands-on & Code First**: Jangan hanya membaca materi; tulis kode latihan secara mandiri sampai berjalan dan pahami *kenapa* kode tersebut bekerja.
4. **Definition of Done (DoD)**: Sebuah issue baru boleh di-close jika seluruh checklist materi dan eksperimen di dalamnya sudah dicoba dan divalidasi.

---

## 🔄 2. Siklus Alur Belajar (Step-by-Step Cycle)

Setiap kali Anda mulai belajar, ikuti siklus 5 langkah ini:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ 1. Pilih Issue  │ ──> │ 2. Bedah & Riset│ ──> │ 3. Tulis Kode   │
│ (gh issue list) │     │ (gh issue view) │     │ (Latihan/Script)│
└─────────────────┘     └─────────────────┘     └────────┬────────┘
                                                         │
┌─────────────────┐     ┌─────────────────┐              │
│ 5. Close Issue  │ <── │ 4. Dokumentasi  │ <────────────┘
│ (gh issue close)│     │ & Commit / Push │
└─────────────────┘     └─────────────────┘
```

1. **Pilih & Buka Issue**: Ambil issue dengan nomor urut terkecil yang masih berstatus `OPEN`.
2. **Pahami Target & Checklist**: Baca kriteria checklist dan eksperimen yang harus dijalankan.
3. **Koding & Eksperimen**: Buat script latihan di dalam repositori (misalnya di folder `scripts/` atau `src/`).
4. **Catat Temuan & Commit**: Simpan kode ke Git dengan pesan commit yang jelas.
5. **Centang & Selesaikan**: Centang checklist di GitHub, berikan catatan kesimpulan, lalu tutup (*close*) issue tersebut.

---

## 💻 3. Panduan Perintah GitHub CLI (`gh`)

Gunakan perintah-perintah berikut langsung di terminal (PowerShell / Command Prompt):

### A. Melihat Daftar Issue (List Issues)
Untuk melihat semua issue yang belum selesai:
```powershell
gh issue list
```

Untuk melihat semua issue (termasuk yang sudah selesai / closed):
```powershell
gh issue list --state all
```

Hanya menampilkan issue dengan label `roadmap`:
```powershell
gh issue list --label "roadmap"
```

---

### B. Melihat Isi & Detail Issue (View Issue)
Untuk membaca deskripsi dan checklist lengkap sebuah issue di terminal:
```powershell
# Ganti angka 1 dengan nomor issue yang ingin dilihat
gh issue view 1
```

Jika Anda ingin membukanya langsung di browser (tampilan web GitHub):
```powershell
gh issue view 1 --web
```

---

### C. Menambahkan Catatan/Komentar ke Issue (Comment Issue)
Sangat disarankan mendokumentasikan temuan atau hasil eksperimen Anda di kolom komentar issue sebelum menutupnya:
```powershell
# Menambah komentar singkat via CLI
gh issue comment 1 --body "Selesai mencoba perhitungan cosine similarity manual di Python. Hasil sesuai dengan rumus dot product / norm."
```

Atau buka editor teks untuk menulis komentar panjang:
```powershell
gh issue comment 1 --editor
```

---

### D. Menutup Issue yang Selesai (Close Issue)
Setelah semua tugas dan checklist selesai:

**Cara 1: Menggunakan Perintah `gh issue close`**
```powershell
# Menutup issue beserta pesan penutup
gh issue close 1 --comment "Semua checklist Tahap 0 selesai dipraktikkan. Skrip latihan tersimpan di folder scripts/tahap-0/."
```

**Cara 2: Otomatis Menutup Melalui Pesan Commit Git**  
Jika Anda melakukan push commit dengan kata kunci khusus seperti `Closes #<nomor>` atau `Fixes #<nomor>`, GitHub akan otomatis menutup issue tersebut saat commit masuk ke branch utama:
```powershell
git commit -m "feat(fondasi): implementasi cosine similarity manual (Closes #1)"
git push origin main
```

**Cara 3: Melalui Tampilan Web GitHub**
1. Buka issue di browser: `gh issue view 1 --web`
2. Centang kotak checklist `[x]` secara manual.
3. Klik tombol **Close issue** di bagian bawah halaman.

---

### E. Membuka Kembali Issue (Reopen Issue)
Jika sewaktu-waktu Anda ingin mengulang atau meninjau kembali materi:
```powershell
gh issue reopen 1 --comment "Membuka kembali untuk review ulang implementasi HNSW."
```

---

## 🛠️ 4. Rekomendasi Struktur Folder Repositori

Agar kode latihan Anda rapi dan mudah diakses kembali, buat folder terstruktur seperti berikut:

```
belajar-ai/
├── ATURAN_BELAJAR.md            # Panduan alur belajar ini
├── Peta Belajar AI & RAG.md     # Roadmap materi utama
├── docs/                        # Catatan teori, diagram, atau ringkasan bacaan
│   ├── tahap-1-transformer.md
│   └── tahap-4-retrieval.md
├── scripts/                     # Kode latihan & eksperimen koding
│   ├── tahap-0/                 # Latihan dasar (Python, Cosine manual, dll)
│   ├── tahap-2/                 # Eksperimen embedding, normalisasi, jarak
│   ├── tahap-3/                 # Skrip parser PDF, chunking & metadata
│   ├── tahap-4/                 # Hybrid search (tsvector + vektor), RRF
│   └── tahap-5/                 # Prompting, streaming SSE, retry logic
└── tests/                       # Golden dataset & skrip evaluasi metrik
    └── golden-dataset.json
```

---

## 🌿 5. Alur Kerja Git yang Dianjurkan (Git Workflow)

1. **Pastikan branch utama bersih dan terbaru:**
   ```powershell
   git checkout main
   git pull origin main
   ```

2. **Buat branch baru untuk tahap yang sedang dikerjakan:**
   ```powershell
   git checkout -b feat/tahap-0-fondasi
   ```

3. **Tulis kode latihan Anda di folder terkait, lalu commit:**
   ```powershell
   git status
   git add .
   git commit -m "feat: tambah latihan python data processing dan cosine similarity"
   ```

4. **Gabungkan kembali ke branch `main`:**
   ```powershell
   git checkout main
   git merge feat/tahap-0-fondasi
   git push origin main
   ```

5. **Tutup issue terkait:**
   ```powershell
   gh issue close 1 --comment "Tahap 0 selesai dikerjakan pada commit $(git rev-parse --short HEAD)"
   ```

---

## 📊 6. Ringkasan Cepat Perintah Harian (Quick Cheat Sheet)

| Aktivitas | Perintah Terminal |
| :--- | :--- |
| **Lihat daftar tugas tersisa** | `gh issue list` |
| **Baca instruksi tugas #X** | `gh issue view X` |
| **Buka tugas #X di browser** | `gh issue view X --web` |
| **Tulis catatan progres** | `gh issue comment X --body "catatan Anda"` |
| **Selesaikan & tandai selesai** | `gh issue close X --comment "Selesai!"` |
| **Periksa status file kerja** | `git status` |
| **Simpan hasil koding** | `git add . && git commit -m "pesan commit"` |
| **Kirim kode ke GitHub** | `git push origin main` |

---

*Selamat berproses! Jadikan setiap issue sebagai batu loncatan nyata menuju pemahaman AI & RAG yang mendalam.*
