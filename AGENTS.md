# AGENTS.md

Instruksi ini berlaku untuk semua AI agent (Claude Code, dst.) yang bekerja di repo ini.
Ini adalah sumber kebenaran utama untuk konteks project, struktur, dan cara pakai skill.
`CLAUDE.md` hanya menunjuk ke file ini — edit di sini, bukan di sana.

File ini adalah **index** — beberapa panduan detail dipecah ke `docs/agents/` supaya file
ini tetap ringkas. Baca file yang dirujuk di titik yang disebutkan, jangan cuma baca
judulnya lalu skip isinya.

## 1. Status project & kickoff

**Template universal.** Repo ini kerangka awal untuk jenis project apa pun (web app, tool
internal/dashboard, landing page, API, CLI/library, mobile, pipeline data, dst.). Tidak ada
asumsi soal jenis project, stack, database, atau konteks produk — semuanya ditentukan
bersama user di awal.

**Kickoff (wajib di project baru, sebelum menulis kode atau scaffold apa pun).** Baca
`memory/PRD.md`. Kalau "Keputusan yang sudah diambil" di sana masih kosong, ini project baru:
tanyakan hal berikut ke user — boleh dalam satu putaran, dengan rekomendasi + alasan singkat
per pilihan. Keputusan akhir ada di user: **jangan pilih sendiri lalu jalan**, termasuk untuk
hal yang tampak jelas.

1. **Jenis project & konteks produk** — apa yang dibangun dan untuk siapa; punya UI atau
   tidak; kalau punya UI: tool internal/dashboard atau client-facing (landing page,
   marketing, produk publik). Ini menentukan skill UI mana yang relevan (bagian 8).
2. **Stack per layer yang relevan** — backend, frontend, database, deployment/Docker
   (bagian 9). "Belum ditentukan" bukan izin agent untuk memilih sendiri.
3. **Konvensi penamaan** — default `snake_case` untuk tabel/kolom database dan Bahasa Inggris
   untuk semua identifier teknis (detail di `docs/agents/code-standards.md`). Cukup
   konfirmasi cepat, bukan dibahas dari nol.
4. **Struktur folder** sesuai jenis project (bagian 4) dan **formatter/linter** sesuai stack
   (`docs/agents/code-standards.md`).

Setelah dijawab: catat di `memory/PRD.md`, isi bagian 3 dan 9 di file ini, sesuaikan struktur
(bagian 4), lakukan **onboarding skill** (bagian 8: baca skill terpasang, putuskan sendiri
mana yang dipakai dan kapan, catat rencananya), baru mulai kerja. Kalau PRD.md sudah berisi
keputusan, project sudah berjalan: jangan ulangi kickoff, ikuti keputusan yang tercatat dan
tanya hanya untuk hal baru yang belum diputuskan (mis. fitur yang butuh pilihan stack/database
tambahan). Kalau keputusan sudah ada tapi "Rencana pemakaian skill" di PRD.md belum ada
(project yang dibuat sebelum aturan ini), lakukan onboarding skill dulu sebelum lanjut kerja.
Jangan biarkan keputusan hanya hidup di riwayat chat.

## 2. Standar kode & referensi eksternal

Dua panduan wajib dibaca sebelum menulis kode — dipecah ke file terpisah supaya tidak
menggelembungkan file index ini:

- **[docs/agents/code-standards.md](docs/agents/code-standards.md)** — standar production-ready
  (bukan prototipe), aturan bug fix lintas-kode, pairing validasi+UI, dan konvensi penulisan
  kode. Aturan yang menyebut UI/database/API berlaku kalau project punya komponen itu. Baca
  sebelum langkah "Implement" (bagian 5, langkah 3).
- **[docs/agents/handling-references.md](docs/agents/handling-references.md)** — cara
  menangani referensi eksternal (screenshot UI, kode dari luar, situs lain) supaya tidak
  dicontek 100% dan melanggar aturan project. Baca setiap kali user kasih referensi, sebagai
  bagian dari langkah "Pahami maksud" (bagian 5, langkah 1).

## 3. Project overview

<!-- TODO: isi setelah kickoff -->
- **Jenis project:** belum ditentukan
- **Apa yang dibangun:** belum ditentukan
- **Untuk siapa:** belum ditentukan
- **Masalah yang diselesaikan:** belum ditentukan
- **Punya UI?:** belum ditentukan (kalau ya: tool internal atau client-facing)

## 4. Struktur repo

```
.
├── AGENTS.md              # file ini — instruksi utama untuk agent
├── CLAUDE.md               # pointer ke AGENTS.md, jangan diisi konten lain
├── docs/
│   ├── agents/              # panduan detail (standar kode, cara pakai referensi)
│   └── template-decisions.md # riwayat keputusan template (maintainer; boleh dihapus di project turunan)
├── memory/
│   ├── PRD.md                # memory persisten lintas sesi — lihat bagian 6
│   └── backlog.md            # catatan mentah — lihat bagian 7
├── backend/                 # titik awal project multi-service (lihat di bawah)
├── frontend/                # titik awal project multi-service (lihat di bawah)
├── .agents/skills/           # skill yang di-install lewat skills.sh
├── .claude/skills/           # symlink ke .agents/skills/ supaya terdaftar di Claude Code
└── skills-lock.json         # lockfile skill (sumber, path, hash)
```

`backend/` dan `frontend/` adalah **titik awal untuk project multi-service** (mis. API + web
app): dipisah supaya masing-masing punya `Dockerfile`/container sendiri dan bisa dijalankan
terpisah. Sesuaikan dengan jenis project setelah kickoff, lalu update tree di atas:

- **Multi-service** → pakai split ini; service tambahan masuk sebagai folder sejajar
  (bukan nested), dan `docker-compose.yml` tinggal mount tiap folder sebagai service.
- **Full-stack satu framework** (mis. Next.js, Laravel, Django) → konvensi framework di root
  yang menang; hapus folder kosong yang tidak dipakai.
- **Jenis lain** (CLI, library, mobile, pipeline data, situs statis) → struktur idiomatik
  ekosistemnya (mis. `src/`, `cmd/`, `app/`); hapus `backend/` dan `frontend/` kalau tidak
  relevan.
- Apa pun yang dipilih, catat di `memory/PRD.md`. Folder tambahan di luar tree ini (mis.
  `design-system/` dari skill) hanya dibuat dengan persetujuan user dan harus ditambahkan ke
  tree.

## 5. Alur kerja per-task

Sebelum menganggap sebuah task/perubahan selesai, ikuti urutan ini (disiplin dasar, bukan
pipeline otomatis — cukup jalankan langkah-langkahnya secara sadar):

1. **Pahami maksud** — kalau instruksi ambigu atau berdampak besar, klarifikasi dulu ke user
   sebelum implementasi. Kalau user kasih referensi eksternal, baca
   [docs/agents/handling-references.md](docs/agents/handling-references.md) dulu.
2. **Terapkan rencana skill SEBELUM mulai implement** — skill di `.agents/skills/` tidak
   otomatis dipakai; agent yang memutuskan dan memanggilnya, tanpa menunggu user menyuruh.
   Buka "Rencana pemakaian skill" di `memory/PRD.md` (dibuat saat onboarding, bagian 8),
   lalu tulis SATU BARIS keputusan di awal task: skill apa yang dipanggil dan kenapa, atau
   "tidak ada skill relevan karena ...". Panggil skill sesuai pemicu di rencana itu. Bawaan
   template (kalau rencana project belum ada, lakukan onboarding dulu):
   - Task menyentuh UI (halaman/komponen/layout)? → `ui-taste`, ikuti playbook yang cocok.
     `ui-ux-pro-max` hanya kalau butuh data spesifik (aturan form, chart, aksesibilitas,
     panduan stack) — aturan prioritas di bagian 8.
   - Task merancang/merestrukturisasi module/service (bukan edit kecil)? → `codebase-design`.
   - Project client-facing dan `frontend-design` terpasang? → panggil untuk arah visual.
   Kalau jenis task belum tercakup rencana, evaluasi ulang dan perbarui rencana. Kalau skill
   yang seharusnya relevan tidak muncul atau tidak bisa dipanggil, bilang ke user — jangan
   diam-diam dilewati.
3. **Implement** — baca [docs/agents/code-standards.md](docs/agents/code-standards.md) kalau
   belum di sesi ini, lalu kerjakan perubahan sesuai standar & konvensi di situ.
4. **Verifikasi** — jalankan test/build/lint yang relevan kalau tersedia; untuk perubahan UI,
   coba jalankan aplikasinya, jangan cuma percaya diri dari membaca kode. Komponen yang
   otomatis responsive (grid/flex dari library) TIDAK menjamin kontennya aman — cek eksplisit
   pakai data/teks yang realistis (bukan sample pendek) di beberapa ukuran layar, lihat detail
   di [docs/agents/code-standards.md](docs/agents/code-standards.md). **Untuk task UI, wajib
   panggil skill `ui-taste`** sebagai visual review terakhir sebelum lapor selesai — bukan
   opsional.
5. **Laporkan** — ringkas apa yang berubah, **skill apa yang dipanggil di langkah 2 & 4**, dan
   langkah berikutnya. Jangan diam-diam menganggap selesai tanpa verifikasi di atas — dan
   jangan diam-diam skip pemanggilan skill yang relevan tanpa bilang alasannya.
6. **Catat kalau perlu** — kalau task ini mengandung keputusan penting (bukan cuma detail
   implementasi), update `memory/PRD.md` sesuai bagian 6.

## 6. Memory persisten (`memory/PRD.md`)

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
  (AGENTS.md/docs/agents/) atau cukup dibaca dari kode saat dibutuhkan. PRD.md untuk konteks
  yang *tidak* tersirat dari kode.

## 7. Backlog / inbox (`memory/backlog.md`)

Tempat nampung ide/catatan mentah sebelum jadi task yang jelas (versi ringan dari pola
inbox-processing, tanpa infrastruktur multi-agent/worktree yang belum dibutuhkan di fase
ini). Saat diminta triage, pecah entri di `memory/backlog.md` jadi task jelas, pindahkan ke
`memory/PRD.md` ("Next steps" atau "Keputusan yang sudah diambil"), lalu hapus dari backlog.

## 8. Skill yang terpasang (`.agents/skills/`, lihat `skills-lock.json`)

Skill di-manage lewat [skills.sh](https://www.skills.sh/) — jangan edit isinya manual,
update lewat mekanisme skills.sh supaya `skills-lock.json` tetap akurat.

**Registrasi:** skill baru harus terdaftar untuk Claude Code, kalau tidak Claude Code tidak
melihatnya sama sekali. Setelah install, jalankan `npx skills list` dan pastikan kolom Agents
memuat "Claude Code" (symlink-nya ada di `.claude/skills/`).

**Onboarding skill (wajib sekali per project, dan tiap ada skill baru dipasang).** Tujuannya
agent memutuskan sendiri skill mana yang dipakai dan kapan, supaya user tidak perlu
menyuruh tiap kali:

1. Baca `SKILL.md` tiap skill di `.agents/skills/` (file utamanya saja; file referensi dan
   data dibuka saat dibutuhkan).
2. Untuk tiap skill, putuskan: relevan untuk project ini atau tidak. Kalau ya, tentukan
   pemicu konkretnya (jenis task/kondisi yang membuatnya dipanggil, mis. "task menyentuh
   halaman atau komponen → `ui-taste`, playbook operate.md karena ini dashboard") dan kapan
   TIDAK dipakai. Kalau tidak relevan, lepas (`npx skills remove <nama>`) atau tandai tidak
   dipakai beserta alasannya.
3. Tulis hasilnya di `memory/PRD.md`, bagian "Rencana pemakaian skill" (tabel: skill,
   relevan?, dipakai saat, tidak dipakai saat), lalu beri tahu user ringkasannya. User boleh
   mengoreksi, tapi keputusan awalnya ada di agent.
4. Setelah itu rencana dijalankan otomatis di awal tiap task (bagian 5, langkah 2). Rencana di
   PRD.md adalah keputusan spesifik project dan yang berlaku kalau berbeda dari tabel bawaan
   di bawah ini.

Tabel di bawah adalah pemicu bawaan template, dipakai sebagai bahan onboarding.

| Skill | Berlaku untuk | Kapan dipakai |
|---|---|---|
| `ui-taste` (uizze.sh) | Project ber-UI | Setiap membangun, redesign, atau review UI. Penentu arah visual untuk UI produk (playbook: new-work, operate untuk dashboard/tool, polish, audit), dan visual review terakhir sebelum task UI dilaporkan selesai (bagian 5, langkah 4). |
| `ui-ux-pro-max` | Project ber-UI | **Sumber data referensi, bukan penentu arah:** aturan UX/aksesibilitas, form, chart, palet, font, panduan per-stack. Dipanggil kalau task butuh data spesifik itu. |
| `codebase-design` (mattpocock/skills) | Semua project non-trivial | Merancang/merestrukturisasi modul kode: modul "dalam" (interface kecil, perilaku banyak di belakangnya), testable, mudah dipelihara. Untuk service/module baru atau struktur kompleks, bukan perubahan kecil satu file. |

**Aturan prioritas & pemakaian:**

- **UI produk (tool internal, dashboard, aplikasi):** `ui-taste` menentukan arah visual —
  familiar, padat-informasi, tanpa dekorasi. Output `--design-system` dari `ui-ux-pro-max`
  tidak dipakai sebagai arah untuk kasus ini: tes pada kueri dashboard internal
  menghasilkan pola landing page dan gaya glassmorphism. Pakai `--domain` (ux, chart, color,
  typography) atau `--stack` saja.
- **UI client-facing (landing page, marketing, produk publik):** `frontend-design` (kalau
  terpasang) dan `ui-taste` menentukan arah; `--design-system` dari `ui-ux-pro-max` boleh
  jadi masukan, tetap dikonfirmasi user.
- **Skrip `ui-ux-pro-max`:** jalankan `python3 .agents/skills/ui-ux-pro-max/scripts/search.py
  "<kueri>" --domain <domain>`. SKILL.md-nya memakai `${CLAUDE_PLUGIN_ROOT}` yang tidak diset
  di sini, jadi path itu gagal. Jangan pakai `--persist` (menulis folder `design-system/`)
  tanpa persetujuan user; kalau disetujui, tambahkan ke tree bagian 4 dan catat di PRD.md.
- **`codebase-design`:** kosakata khususnya (module, seam, adapter) hanya untuk diskusi
  desain; laporan ke user tetap bahasa sederhana. "Design It Twice" (memanggil 3+ sub-agent)
  hanya kalau user minta. Rujukan `CONTEXT.md` di skill itu diganti `memory/PRD.md` dan
  AGENTS.md sebagai sumber bahasa domain.

**Project tanpa UI** (API saja, CLI, library, pipeline data): lepas skill UI saat kickoff
(`npx skills remove ui-taste ui-ux-pro-max`) dan catat di PRD.md.

**Tidak terpasang, tapi disarankan tergantung konteks:**

| Skill | Kapan disarankan |
|---|---|
| `frontend-design` (anthropics/skills) | Kalau kickoff menetapkan project butuh frontend client-facing (landing page, marketing site, produk publik). Pasang via `npx skills add anthropics/skills -s frontend-design`, lalu cek registrasinya (kolom Agents). Penting untuk kasus itu (arah visual distinctive, bukan templated) — jangan dilewati. Tidak terpasang secara default karena tidak semua project punya UI client-facing. |

**Sengaja tidak dipasang:** `web-design-guidelines` (vercel-labs). Skill ini mengambil
aturannya dari branch `main` GitHub lewat WebFetch setiap dipakai (tidak di-pin, butuh
jaringan, instruksi datang dari luar repo), dan fungsinya sudah tercakup `ui-taste` dan
`ui-ux-pro-max`. Jangan dipasang lagi tanpa alasan baru.

Setelah stack ditentukan, evaluasi apakah perlu skill tambahan (API design, database,
testing untuk stack itu) lewat skills.sh, dan catat di tabel ini.

## 9. Stack teknis

**Tidak ada stack default.** Stack ditentukan bersama user saat kickoff (bagian 1) dan diisi
di bawah. Untuk tiap layer, agent boleh memberi rekomendasi + alasan, tapi keputusan akhir di
user: "belum ditentukan" artinya WAJIB ditanyakan dulu — termasuk database (sebelum
schema/migration pertama) — bukan diputuskan sendiri walau pilihannya tampak jelas. Fitur baru
yang butuh pilihan baru (database, queue, auth provider tambahan, dst.) juga ditanyakan dulu.

- Backend: belum ditentukan
- Frontend: belum ditentukan
- Database: belum ditentukan
- Deployment/Docker: belum ditentukan
- Formatter/linter: belum ditentukan

Contoh titik awal per jenis project — bahan diskusi saat kickoff, **bukan default**:

- Web app dengan API + UI terpisah (dua container): mis. FastAPI + React
- Full-stack satu framework: mis. Next.js, Laravel, Django
- Landing page / situs konten: mis. Astro atau Next.js (statis)
- API saja: mis. FastAPI, Express, Go
- CLI / library: bahasa sesuai ekosistem target
- Mobile: mis. React Native, Flutter, native
- Otomasi / pipeline data: mis. Python

Kalau stack yang dipilih punya struktur folder sendiri, konvensi framework itu yang menang
(bagian 4). Catat pilihan final di `memory/PRD.md` dan update daftar di atas.
