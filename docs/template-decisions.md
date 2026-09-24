# Riwayat keputusan template

> Catatan untuk **maintainer template ini**: kenapa aturan di `AGENTS.md` dan `docs/agents/`
> ada, dan temuan pengujian nyata yang melatarbelakanginya. Ini BUKAN memory project — memory
> project ada di `memory/PRD.md` (dimulai kosong di tiap project turunan). Agent tidak perlu
> membaca file ini untuk mengerjakan project; project turunan boleh menghapusnya.
>
> Entri lama tetap ditulis apa adanya sebagai riwayat. Nomor "bagian N" di entri
> bertanggal 2026-09-23 merujuk struktur AGENTS.md pada saat itu. Kalau sebuah entri sudah
> digantikan, ada tanda **[DIGANTIKAN]** atau **[DIKOREKSI]**.

## Keputusan

- **[2026-09-23] [DIGANTIKAN 2026-09-25] Stack default: FastAPI (backend) + React
  (frontend)** — dipilih sebagai default template, tapi HARUS dikonfirmasi ulang ke user di
  awal tiap project baru (lihat AGENTS.md bagian 9). Digantikan: template dibuat universal,
  tidak ada stack default lagi (lihat entri 2026-09-25 di bawah).
- **[2026-09-23] Skill `frontend-design` dilepas dari instalasi default**, tapi tetap
  disarankan tergantung konteks (lihat AGENTS.md bagian 8, tabel "skill yang disarankan"):
  pasang lagi kalau user konfirmasi project butuh frontend client-facing (landing
  page/marketing site). Dipertahankan terpasang: `ui-ux-pro-max`, `web-design-guidelines`.
- **[2026-09-23] Struktur folder `backend/`/`frontend/` dipisah di root** — untuk fleksibilitas
  containerization (Docker) per-service. Catatan: kalau stack akhir yang dipilih adalah
  framework full-stack opinionated (Next.js, Laravel, dst.), konvensi framework itu yang
  menang, bukan split ini (lihat AGENTS.md bagian 9).
- **[2026-09-23] Standar kualitas: production-ready, bukan prototipe** — semua project dari
  template ini harus dianggap production-ready sejak awal (lihat
  `docs/agents/code-standards.md`): tidak boleh ada mock/stub diam-diam, tidak boleh bypass
  test/lint, wajib validasi & error handling di boundary, tidak hardcode secret, migration
  untuk perubahan skema DB, dan jalan pintas/technical debt harus dikomunikasikan + dicatat
  di sini, bukan didiamkan.
- **[2026-09-23] Aturan menangani referensi (gambar/kode/situs lain)** — referensi cuma
  diadopsi untuk aspek spesifik yang diminta, bukan lisensi untuk override aturan project
  (lihat `docs/agents/handling-references.md`). Ini merespons pengalaman berulang: agent
  ditunjukkan referensi (mis. screenshot UI) lalu mencontek 100% dan lupa aturan project yang
  sudah ditulis. Kalau referensi bentrok sama aturan tertulis, wajib stop dan tanya user,
  bukan diam-diam milih.
- **[2026-09-23] Bug fix wajib cek dampak lintas kode + validasi & UI constraint satu paket**
  (lihat `docs/agents/code-standards.md` dan AGENTS.md bagian 9) — merespons pengalaman
  berulang: (1) perbaikan cuma di satu titik tanpa cek pola serupa di tempat lain → tambal
  sulam menumpuk; (2) validasi backend benar tapi UI field (mis. nomor telepon) masih terima
  input bebas, jadi perbaikan berulang tiap ada field baru. Solusi: definition-of-done
  sekarang eksplisit mencakup cek dampak lintas kode untuk bug fix, dan reusable field
  component (validasi + UI constraint sekaligus) untuk field dengan pola berulang.
- **[2026-09-23] AGENTS.md dipecah jadi index + `docs/agents/`** (code-standards.md,
  handling-references.md) supaya file utama tidak menggelembung terus tiap ada aturan baru.
  AGENTS.md tetap satu-satunya file yang auto-terbaca; file di `docs/agents/` dirujuk eksplisit
  di titik yang relevan (langkah "Implement"/"Pahami maksud" di alur kerja per-task) — bukan
  auto-load, jadi instruksi rujukan itu wajib tetap ada dan jangan dihapus saat AGENTS.md
  diedit lagi nanti. Repo GitHub juga dijadikan "Template repository" supaya instantiate
  project baru pakai "Use this template" (riwayat git bersih), bukan clone manual.
- **[2026-09-23] Database wajib ikut dikonfirmasi ke user, bukan dipilih sendiri oleh agent**
  (lihat AGENTS.md bagian 9) — ditemukan dari pengujian nyata (sesi agent baru, project
  monitoring kredit): agent tanya stack backend/frontend tapi langsung pilih database sendiri
  tanpa konfirmasi. "Database: belum ditentukan" sekarang eksplisit berarti wajib ditanyakan
  dulu, bukan bebas diputuskan sendiri.
- **[2026-09-23] Naming convention database default `snake_case`, konsisten untuk seluruh
  schema** (lihat `docs/agents/code-standards.md`) — ditemukan dari pengujian yang sama:
  penamaan tabel/kolom campur `snake_case` dan `camelCase`, bukan cuma masalah rapi tapi bisa
  jadi bug nyata (case-folding identifier di PostgreSQL/MySQL). Dikonfirmasi sekali di awal
  project bareng pertanyaan Database, lalu dipatuhi konsisten — layer API/JSON di atasnya
  boleh beda konvensi asal lewat mapping eksplisit, bukan bikin kolom DB ikut campur.
- **[2026-09-23] Responsivitas konten UI wajib dicek terpisah dari responsivitas layout**
  (lihat `docs/agents/code-standards.md`) — ditemukan dari pengujian yang sama: layout/grid
  komponen sudah rapi & otomatis responsive, tapi teks/angka di dalamnya (nominal besar di
  dashboard, label panjang) meluber keluar kotak. Root cause: agent cuma uji dengan data
  sample pendek, bukan konten realistis/terpanjang, dan cuma di satu ukuran layar. Solusi:
  verifikasi UI sekarang wajib pakai konten realistis + cek di minimal 2 lebar layar, plus
  panduan konkret (truncate, responsive text size) dan pengingat pakai skill `ui-ux-pro-max`
  untuk komponen data-dense, bukan cuma dipakai di awal lalu lupa.
- **[2026-09-23] Pasang skill `ui-taste` (uizze.sh) dan `codebase-design` (mattpocock/skills)**
  (lihat AGENTS.md bagian 8) — merespons temuan pengujian: hasil UI rapi secara struktur tapi
  "selera"-nya generik/AI-slop, kurang perhatian detail selayaknya UI/UX designer profesional.
  `ui-taste` (16K install) menyasar persis masalah ini dan sekarang wajib dipakai sebagai
  visual review sebelum task UI dilaporkan selesai (lihat `docs/agents/code-standards.md`).
  `codebase-design` (663K install, author dikenal) dipasang sekalian untuk kualitas arsitektur
  kode backend/frontend, bukan cuma UI. Instal via `npx skills add https://uizze.sh -y` dan
  `npx skills add mattpocock/skills -s codebase-design -y`.
- **[2026-09-23] Bahasa identifier teknis (tabel, kolom, variabel, fungsi, file) wajib satu
  bahasa konsisten, default Bahasa Inggris** (lihat `docs/agents/code-standards.md`) —
  ditemukan dari pengujian yang sama: setelah naming case dibenerin (snake_case), penamaan
  tabelnya sendiri ternyata campur Bahasa Inggris dan Indonesia. Konfirmasi sekali di awal
  project bareng pertanyaan database/naming case; teks user-facing (label UI, pesan) tetap
  boleh Bahasa Indonesia, aturan ini cuma untuk identifier teknis.
- **[2026-09-23] [DIKOREKSI 2026-09-25] Pemanggilan skill dijadikan langkah wajib di alur
  kerja, bukan cuma tabel referensi** (lihat AGENTS.md bagian 5, langkah 2 baru) — user
  amati skill `ui-ux-pro-max`, `web-design-guidelines`, `codebase-design` nggak pernah
  dipanggil spontan padahal sudah ada di tabel "kapan dipakai" bagian 8; cuma `ui-taste` yang
  kepake karena diminta eksplisit. Solusi: alur kerja per-task punya langkah 2 "Cek skill yang
  relevan" sebagai checklist eksplisit sebelum implement, dan langkah "Laporkan" wajib
  menyebut skill apa yang dipanggil (atau kenapa tidak). **Koreksi:** akar masalah yang
  dicatat waktu itu ("model tidak memanggil secara aktif") tidak lengkap. Audit 2026-09-25
  menemukan `ui-ux-pro-max` dan `web-design-guidelines` tidak pernah terdaftar untuk Claude
  Code (hanya Codex/Cursor/Gemini/Copilot/Warp; `.claude/skills/` tidak berisi symlink-nya),
  jadi memang tidak terlihat oleh Claude Code sama sekali. Langkah 2 tetap berguna, tapi hanya
  bekerja kalau skill-nya terdaftar — lihat entri 2026-09-25.
- **[2026-09-23] Formatter TIDAK disertakan sebagai file config di template** (lihat
  `docs/agents/code-standards.md`) — sempat dicoba sertakan file config Prettier/Ruff langsung
  di template, tapi dibatalkan user: lebih baik jadi instruksi ("setup formatter begitu stack
  dikonfirmasi, sebelum kode pertama ditulis") daripada file konkret yang berisiko basi kalau
  stack final beda dari default. Konsisten dengan pola "jangan komit ke hal spesifik sebelum
  dikonfirmasi" yang sudah dipakai di aturan lain (stack, database, naming).

- **[2026-09-25] Audit skill: registrasi, prioritas, dan pelepasan `web-design-guidelines`**
  (lihat AGENTS.md bagian 8) — hasil audit keempat skill terpasang:
  - `ui-ux-pro-max` tidak terdaftar untuk Claude Code (cek `npx skills list`, kolom Agents).
    Diperbaiki dengan symlink di `.claude/skills/`. Skill baru lewat CLI biasanya sudah
    otomatis, tapi selalu cek kolom Agents setelah install.
  - `ui-ux-pro-max --design-system "credit monitoring internal dashboard"` menghasilkan pola
    landing page ("Start trial", hero) dan gaya Glassmorphism gelap — bertentangan dengan
    `ui-taste` (operate.md: UI produk harus familiar, tanpa dekorasi). Diputuskan `ui-taste`
    menentukan arah visual untuk UI produk, `ui-ux-pro-max` hanya sumber data (domain ux,
    chart, color, typography, stack). Untuk landing page/marketing, `--design-system` boleh
    jadi masukan.
  - Path skrip `ui-ux-pro-max` di SKILL.md memakai `${CLAUDE_PLUGIN_ROOT}` yang tidak diset
    di setup ini; dipakai path `.agents/skills/ui-ux-pro-max/scripts/search.py`. `--persist`
    (menulis folder `design-system/`) butuh persetujuan user.
  - `web-design-guidelines` dilepas: pembungkus 39 baris yang mengambil aturan dari branch
    `main` GitHub via WebFetch tiap dipakai (tidak di-pin, butuh jaringan, instruksi datang
    dari luar repo), dan fungsinya sudah tercakup `ui-taste` + `ui-ux-pro-max`.
  - `codebase-design` dipertahankan; kosakatanya dibatasi ke diskusi desain, laporan ke user
    tetap bahasa sederhana. Rujukan `CONTEXT.md` di skill diarahkan ke PRD.md/AGENTS.md.
- **[2026-09-25] Template dibuat universal untuk starter project apa pun** — sebelumnya
  mengasumsikan FastAPI + React dan konteks "internal admin dashboard". Sekarang: tidak ada
  stack/jenis project default; kickoff wajib di awal project baru (AGENTS.md bagian 1)
  menentukan jenis project, stack per layer, database, naming, dan formatter bersama user;
  aturan yang spesifik UI/database/API berlaku bersyarat; skill UI hanya untuk project
  ber-UI (dilepas di kickoff kalau tidak ada UI); struktur `backend/`+`frontend/` jadi titik
  awal project multi-service, bukan kewajiban. Riwayat keputusan template dipindah ke file
  ini supaya `memory/PRD.md` di project turunan dimulai kosong (sebelumnya membawa riwayat
  template dan bikin konteks project baru tercemar).
- **[2026-09-25] Onboarding skill: agent memutuskan sendiri kapan memakai skill** (lihat
  AGENTS.md bagian 8 dan bagian 5, langkah 2) — user mencoba 2 project dari template: agent
  jauh lebih terarah oleh AGENTS.md dan docs, tapi tidak pernah berpikir memakai skill kecuali
  disuruh. Checklist statis di alur kerja tidak cukup karena agent tidak punya pemahaman
  skill-nya sendiri. Solusi: saat kickoff (dan tiap ada skill baru) agent membaca SKILL.md
  tiap skill terpasang, memutuskan relevan/tidak dan pemicunya untuk project itu, lalu
  menulisnya di bagian "Rencana pemakaian skill" di `memory/PRD.md`. Keputusan itu ikut
  terbaca ulang tiap sesi (mekanisme yang sama dengan memory PRD), dan tiap task diawali satu
  baris keputusan "skill apa dipakai dan kenapa" sehingga melewatkannya kelihatan oleh user.
  Project turunan lama yang belum punya rencana itu melakukan onboarding otomatis begitu
  membaca AGENTS.md terbaru. Ini tetap lapisan instruksi, bukan jaminan; opsi penguat kalau
  masih meleset: hook SessionStart di `.claude/settings.json` yang menampilkan rencana skill.
