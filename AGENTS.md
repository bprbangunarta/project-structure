# AGENTS.md

Instruksi ini berlaku untuk semua AI agent (Claude Code, dst.) yang bekerja di repo ini.
Ini adalah sumber kebenaran utama untuk konteks project, struktur, dan cara pakai skill.
`CLAUDE.md` hanya menunjuk ke file ini — edit di sini, bukan di sana.

File ini adalah **index** — beberapa panduan detail dipecah ke `docs/agents/` supaya file
ini tetap ringkas. Baca file yang dirujuk di titik yang disebutkan, jangan cuma baca
judulnya lalu skip isinya.

## 1. Status project

**Fase: template.** Repo ini adalah kerangka awal untuk project baru, bukan produk final.
Bagian 9 sudah punya stack *default* (FastAPI + React) dan bagian 8 punya asumsi konteks
produk (internal admin dashboard) — tapi keduanya cuma default/asumsi kerja yang WAJIB
dikonfirmasi ulang ke user begitu template ini dipakai untuk project nyata (lihat instruksi
konfirmasi di bagian 8 & 9). Jangan anggap default itu keputusan final tanpa konfirmasi.
Begitu dikonfirmasi (dipakai apa adanya atau diganti), catat di `memory/PRD.md` — jangan
biarkan keputusan hanya hidup di riwayat chat.

## 2. Standar kode & referensi eksternal

Dua panduan wajib dibaca sebelum menulis kode — dipecah ke file terpisah supaya tidak
menggelembungkan file index ini:

- **[docs/agents/code-standards.md](docs/agents/code-standards.md)** — standar production-ready
  (bukan prototipe), aturan bug fix lintas-kode, pairing validasi+UI, dan konvensi penulisan
  kode. Baca sebelum langkah "Implement" (bagian 5, langkah 3).
- **[docs/agents/handling-references.md](docs/agents/handling-references.md)** — cara
  menangani referensi eksternal (screenshot UI, kode dari luar, situs lain) supaya tidak
  dicontek 100% dan melanggar aturan project. Baca setiap kali user kasih referensi, sebagai
  bagian dari langkah "Pahami maksud" (bagian 5, langkah 1).

## 3. Project overview

<!-- TODO: isi begitu ada kejelasan produk -->
- **Apa yang dibangun:** belum ditentukan
- **Untuk siapa:** belum ditentukan
- **Masalah yang diselesaikan:** belum ditentukan

## 4. Struktur repo

```
.
├── AGENTS.md              # file ini — instruksi utama untuk agent
├── CLAUDE.md               # pointer ke AGENTS.md, jangan diisi konten lain
├── docs/agents/             # panduan detail (standar kode, cara pakai referensi)
├── memory/
│   ├── PRD.md                # memory persisten lintas sesi — lihat bagian 6
│   └── backlog.md            # catatan mentah — lihat bagian 7
├── backend/                 # service backend (API, worker, dsb.)
├── frontend/                # aplikasi frontend (web/mobile)
├── .agents/skills/           # skill yang di-install lewat skills.sh
└── skills-lock.json         # lockfile skill (sumber, path, hash)
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

## 5. Alur kerja per-task

Sebelum menganggap sebuah task/perubahan selesai, ikuti urutan ini (disiplin dasar, bukan
pipeline otomatis — cukup jalankan langkah-langkahnya secara sadar):

1. **Pahami maksud** — kalau instruksi ambigu atau berdampak besar, klarifikasi dulu ke user
   sebelum implementasi. Kalau user kasih referensi eksternal, baca
   [docs/agents/handling-references.md](docs/agents/handling-references.md) dulu.
2. **Cek skill yang relevan SEBELUM mulai implement** — skill di `.agents/skills/` (tabel
   lengkap di bagian 8) TIDAK otomatis kebaca, harus dipanggil aktif. Sebagai checklist wajib
   tiap task, bukan cuma referensi pasif:
   - Task menyentuh UI/komponen/layout? → panggil `ui-ux-pro-max`.
   - Task merancang/merestrukturisasi module/service (bukan sekadar edit kecil)? → panggil
     `codebase-design`.
   - Task diminta review UI/aksesibilitas? → panggil `web-design-guidelines`.
   - (Lihat bagian 8 untuk daftar lengkap + kondisi kapan skill lain dipasang/dipanggil.)
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

| Skill | Kapan dipakai |
|---|---|
| `ui-ux-pro-max` | Saat mengerjakan struktur UI, komponen, design system, aksesibilitas, interaksi, responsive layout — referensi cepat untuk style/palette/font-pairing/ikon/chart. |
| `web-design-guidelines` | Saat diminta review UI/aksesibilitas/UX terhadap best practice (mis. "review UI ini", "audit accessibility"). |
| `ui-taste` (uizze.sh) | **Setiap kali membangun, redesign, atau review komponen/layar UI** — playbook anti-"AI slop" untuk hierarki visual, layout, dan detail polish. Ini yang secara eksplisit menyasar masalah "tampilan rapi tapi seleranya generik" — dipakai bukan cuma pas styling awal, tapi juga di setiap komponen baru dan sebagai **visual review terakhir** sebelum task UI dianggap selesai (lihat bagian 5, langkah 4). |
| `codebase-design` (mattpocock/skills) | Saat merancang/merestrukturisasi modul kode (bukan UI) — prinsip modul yang "dalam" (interface kecil, perilaku banyak di belakangnya), testable, gampang dipelihara. Pakai saat bikin service/module baru di backend atau struktur komponen kompleks di frontend, bukan untuk perubahan kecil satu file. |

**Asumsi konteks produk saat ini: internal admin dashboard, bukan landing page/marketing
site.** Ini asumsi kerja dari percakapan waktu template ini disusun — SAMA seperti stack di
bagian 9, ini WAJIB dikonfirmasi ulang ke user di awal tiap project baru, bukan otomatis
diwariskan. Kalau dikonfirmasi tetap internal tool: prioritaskan konsistensi, kejelasan
informasi, dan pola UI yang familiar (tabel data, form, navigasi admin) di atas eksplorasi
estetika/brand identity.

**Skill yang TIDAK terpasang tapi disarankan tergantung konteks:**

| Skill | Kapan disarankan |
|---|---|
| `frontend-design` (anthropics/skills) | **Kalau user konfirmasi project ini butuh frontend untuk landing page/marketing site/produk client-facing** (bukan internal dashboard) — pasang lagi via `npx skills add anthropics/skills -s frontend-design`. Skill ini penting untuk kasus itu (arah desain visual yang distinctive, bukan templated); jangan skip hanya karena kebiasaan template ini defaultnya admin-dashboard-context. Sebelumnya dilepas karena konteks produk saat itu internal dashboard, bukan karena skill-nya buruk. |

Skill set saat ini fokus ke UI/UX dashboard. Begitu stack backend ditentukan, evaluasi apakah
perlu menambah skill yang relevan (API design, database, testing framework spesifik stack
tersebut) lewat skills.sh, dan catat di tabel ini.

## 9. Stack teknis

**Default: FastAPI (backend) + React (frontend).** Ini bukan keputusan final otomatis —
di awal setiap project/fitur baru yang dimulai dari template ini, agent WAJIB bertanya ke
user dulu untuk **setiap** baris di bawah (backend, frontend, database, deployment) — pakai
default/rekomendasi, atau ganti? **Jangan pernah memilih sendiri lalu langsung pakai tanpa
konfirmasi**, termasuk database — "belum ditentukan" bukan berarti agent bebas putuskan
sendiri, itu artinya wajib ditanyakan dulu ke user sebelum schema/migration pertama dibuat.
Begitu user menjawab, catat hasilnya di `memory/PRD.md` bagian "Keputusan yang sudah diambil",
lalu update baris di bawah ini.

- Backend: FastAPI (default, perlu dikonfirmasi ulang tiap project baru)
- Frontend: React (default, perlu dikonfirmasi ulang tiap project baru)
- Database: belum ditentukan — **wajib ditanyakan ke user** sebelum bikin schema/migration
  pertama; jangan diam-diam pilih sendiri (mis. auto pilih PostgreSQL/SQLite) walau itu pilihan
  yang masuk akal. Boleh kasih rekomendasi dengan alasannya, tapi keputusan akhir tetap user.
- Deployment/Docker: belum ditentukan

**Catatan soal struktur folder (lihat bagian 4):** FastAPI + React secara alami cocok dengan
split `backend/`/`frontend/` yang sudah ada (dua service independen, dua container). Tapi
kalau user memilih stack full-stack opinionated (mis. Next.js App Router, Laravel, Django
dengan template server-side) yang punya struktur folder sendiri, **konvensi framework itu
yang menang** — jangan paksa masuk ke split `backend/`/`frontend/` generik ini kalau
bertentangan. Diskusikan dan update bagian 4 kalau itu terjadi.

**Catatan implementasi untuk pola validasi+UI:** dengan FastAPI, validasi field didefinisikan
lewat Pydantic model — field constraint (`pattern`, `max_length`, tipe, dst.) ada di satu
tempat sebagai source of truth. Di React, jangan bikin ulang aturan itu secara manual di tiap
form; bungkus tiap tipe field yang berulang (nomor telepon, email, NIK, dst.) jadi komponen
input reusable (mis. `<PhoneNumberInput />`) yang sudah include `inputMode`/`pattern`/
`maxLength` yang sesuai — lihat detail di
[docs/agents/code-standards.md](docs/agents/code-standards.md).
