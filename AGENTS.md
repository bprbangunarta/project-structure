# AGENTS.md

Instruksi ini berlaku untuk semua AI agent (Claude Code, dst.) yang bekerja di repo ini.
Ini adalah sumber kebenaran utama untuk konteks project, struktur, dan cara pakai skill.
`CLAUDE.md` hanya menunjuk ke file ini — edit di sini, bukan di sana.

## 1. Status project

**Fase: eksplorasi.** Belum ada keputusan produk atau stack yang final. Jangan berasumsi
teknologi tertentu (framework, database, dsb.) kecuali sudah ditulis eksplisit di bagian
"Stack teknis" di bawah atau di `memory/PRD.md`. Kalau instruksi user menyiratkan stack baru,
update kedua file ini setelah keputusan itu dibuat — jangan biarkan keputusan hanya hidup di
riwayat chat.

## 2. Project overview

<!-- TODO: isi begitu ada kejelasan produk -->
- **Apa yang dibangun:** belum ditentukan
- **Untuk siapa:** belum ditentukan
- **Masalah yang diselesaikan:** belum ditentukan

## 3. Struktur repo

```
.
├── AGENTS.md           # file ini — instruksi utama untuk agent
├── CLAUDE.md            # pointer ke AGENTS.md, jangan diisi konten lain
├── memory/
│   └── PRD.md            # memory persisten lintas sesi — lihat bagian 4
├── backend/              # service backend (API, worker, dsb.)
├── frontend/             # aplikasi frontend (web/mobile)
├── .agents/skills/        # skill yang di-install lewat skills.sh
└── skills-lock.json      # lockfile skill (sumber, path, hash)
```

**Kenapa `backend/` dan `frontend/` dipisah di root (bukan digabung jadi satu app):**
tujuannya supaya masing-masing bisa punya `Dockerfile` dan container sendiri-sendiri,
dan project ini bisa dijalankan backend-only, frontend-only, atau full-stack tanpa
perlu merombak struktur. Ketika mulai menambah kode:
- Setiap service/app baru yang butuh container sendiri masuk sebagai folder sejajar
  (`backend/`, `frontend/`, atau nama service lain), bukan nested di dalam salah satunya.
- `docker-compose.yml` (kalau/ketika dibuat) tinggal mount tiap folder sebagai service terpisah.
- Kalau ternyata project ini jadi monolith murni, folder split ini tetap dipertahankan
  demi konsistensi kecuali user eksplisit minta digabung.

## 4. Memory persisten (`memory/PRD.md`)

**Penting:** Claude Code tidak otomatis membaca file selain `CLAUDE.md`/`AGENTS.md` di awal
sesi. Supaya `memory/PRD.md` benar-benar berfungsi sebagai memory lintas percakapan, agent
HARUS mengikuti protokol ini secara eksplisit:

- **Di awal sesi kerja** (sebelum mulai eksplorasi/implementasi), baca `memory/PRD.md` untuk
  dapat konteks project terkini: tujuan, keputusan yang sudah diambil, dan progress.
- **Setelah keputusan penting** (pemilihan stack, perubahan arah produk, milestone selesai),
  update `memory/PRD.md` — jangan tunggu diminta.
- **Yang masuk ke PRD.md:** keputusan dan alasannya (why), status/fase saat ini, open
  questions yang belum terjawab, next steps.
- **Yang TIDAK masuk ke PRD.md:** hal yang bisa diturunkan dari membaca kode langsung
  (struktur file, konvensi penamaan, dependency list) — itu didokumentasikan di sini
  (AGENTS.md) atau cukup dibaca dari kode saat dibutuhkan. PRD.md untuk konteks yang
  *tidak* tersirat dari kode.

## 5. Backlog / inbox (`memory/backlog.md`)

Tempat nampung ide/catatan mentah sebelum jadi task yang jelas (versi ringan dari pola
inbox-processing, tanpa infrastruktur multi-agent/worktree yang belum dibutuhkan di fase
ini). Saat diminta triage, pecah entri di `memory/backlog.md` jadi task jelas, pindahkan ke
`memory/PRD.md` ("Next steps" atau "Keputusan yang sudah diambil"), lalu hapus dari backlog.

## 6. Alur kerja per-task

Sebelum menganggap sebuah task/perubahan selesai, ikuti urutan ini (disiplin dasar, bukan
pipeline otomatis — cukup jalankan langkah-langkahnya secara sadar):

1. **Pahami maksud** — kalau instruksi ambigu atau berdampak besar, klarifikasi dulu ke user
   sebelum implementasi (lihat aturan umum soal kapan harus bertanya).
2. **Implement** — kerjakan perubahan sesuai konvensi yang ada di file ini.
3. **Verifikasi** — jalankan test/build/lint yang relevan kalau tersedia; untuk perubahan UI,
   coba jalankan aplikasinya, jangan cuma percaya diri dari membaca kode.
4. **Laporkan** — ringkas apa yang berubah dan langkah berikutnya, jangan diam-diam
   menganggap selesai tanpa verifikasi di atas.
5. **Catat kalau perlu** — kalau task ini mengandung keputusan penting (bukan cuma detail
   implementasi), update `memory/PRD.md` sesuai bagian 4.

## 7. Skill yang terpasang (`.agents/skills/`, lihat `skills-lock.json`)

Skill di-manage lewat [skills.sh](https://www.skills.sh/) — jangan edit isinya manual,
update lewat mekanisme skills.sh supaya `skills-lock.json` tetap akurat.

| Skill | Kapan dipakai |
|---|---|
| `ui-ux-pro-max` | Saat mengerjakan struktur UI, komponen, design system, aksesibilitas, interaksi, responsive layout — referensi cepat untuk style/palette/font-pairing/ikon/chart. |
| `web-design-guidelines` | Saat diminta review UI/aksesibilitas/UX terhadap best practice (mis. "review UI ini", "audit accessibility"). |

**Konteks produk: internal admin dashboard, bukan landing page/marketing site.** Prioritaskan
konsistensi, kejelasan informasi, dan pola UI yang familiar (tabel data, form, navigasi admin)
di atas eksplorasi estetika/brand identity. `frontend-design` (skill untuk distinctive visual
identity ala studio desain) sudah dilepas karena cocoknya untuk produk client-facing, bukan
tooling internal — jangan pasang lagi kecuali scope produk berubah jadi client-facing.

Skill set saat ini fokus ke UI/UX dashboard. Begitu stack backend ditentukan, evaluasi apakah
perlu menambah skill yang relevan (API design, database, testing framework spesifik stack
tersebut) lewat skills.sh, dan catat di tabel ini.

## 8. Stack teknis

**Default: FastAPI (backend) + React (frontend).** Ini bukan keputusan final otomatis —
di awal setiap project/fitur baru yang dimulai dari template ini, agent WAJIB bertanya ke
user dulu: pakai default ini, atau ganti stack lain? Jangan langsung asumsikan default tanpa
konfirmasi, walaupun ini yang tertulis di sini. Begitu user menjawab, catat hasilnya (default
dikonfirmasi, atau stack lain dipilih) di `memory/PRD.md` bagian "Keputusan yang sudah diambil",
lalu update baris di bawah ini kalau override.

- Backend: FastAPI (default, perlu dikonfirmasi ulang tiap project baru)
- Frontend: React (default, perlu dikonfirmasi ulang tiap project baru)
- Database: belum ditentukan
- Deployment/Docker: belum ditentukan

**Catatan soal struktur folder (lihat bagian 3):** FastAPI + React secara alami cocok dengan
split `backend/`/`frontend/` yang sudah ada (dua service independen, dua container). Tapi
kalau user memilih stack full-stack opinionated (mis. Next.js App Router, Laravel, Django
dengan template server-side) yang punya struktur folder sendiri, **konvensi framework itu
yang menang** — jangan paksa masuk ke split `backend/`/`frontend/` generik ini kalau
bertentangan. Diskusikan dan update bagian 3 kalau itu terjadi.

## 9. Konvensi umum

Formatting dasar (indentasi, line ending, trailing whitespace) di-enforce lewat
`.editorconfig` — jangan menyimpang dari situ. Aturan berikut berlaku lintas stack apa pun
yang akhirnya dipilih:

- **Penamaan jelas dan deskriptif.** Nama variabel/fungsi/file menjelaskan dirinya sendiri;
  hindari singkatan ambigu.
- **Jangan premature abstraction.** Jangan bikin helper/wrapper/config generik untuk kasus
  yang baru terjadi sekali. Tiga baris mirip lebih baik daripada abstraksi dini yang salah
  tebak kebutuhan masa depan.
- **Komentar hanya untuk *why* yang tidak jelas** (constraint tersembunyi, workaround bug
  spesifik, alasan non-obvious) — bukan menjelaskan *what* yang sudah jelas dari kode.
- **Jangan tinggalkan dead code**, kode yang di-comment-out, atau implementasi setengah jadi.
- **Jangan menambah validasi/fallback untuk skenario yang tidak mungkin terjadi.** Percaya
  pada guarantee internal; validasi hanya di boundary (input user, API eksternal).
- Konvensi file/folder, linter, formatter, dan testing framework spesifik-stack ditambahkan
  di sini setelah bagian 8 (Stack teknis) diisi — jangan biarkan bagian ini kosong lagi
  begitu stack final.

<!-- TODO: setelah stack dipilih — tambahkan linter/formatter, testing convention,
     struktur folder per fitur/module, dan commit message style di sini. -->
