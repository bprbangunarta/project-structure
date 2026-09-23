# PRD / Project Memory

> File ini adalah memory persisten lintas sesi. Protokol baca/update ada di
> [AGENTS.md](../AGENTS.md) bagian 6. Update file ini setiap ada keputusan atau
> progress baru — jangan biarkan informasi penting hanya hidup di riwayat chat.

## Status saat ini

**Fase: template/kerangka project structure.** Repo ini adalah standar/template yang akan
dipakai sebagai titik awal project baru — belum satu produk spesifik. Produk konkret dan
detail domainnya baru ditentukan saat template ini dipakai untuk project nyata.

## Visi produk

Template project structure untuk memulai project baru (kemungkinan besar: internal tool /
admin dashboard, bukan client-facing website) dengan konvensi AI-agent-friendly yang sudah
disiapkan dari awal (memory, backlog, alur kerja per-task, skill yang relevan).

## Keputusan yang sudah diambil

- **[2026-09-23] Stack default: FastAPI (backend) + React (frontend)** — dipilih sebagai
  default template, tapi HARUS dikonfirmasi ulang ke user di awal tiap project baru (lihat
  AGENTS.md bagian 9). Jangan anggap ini final tanpa konfirmasi eksplisit.
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
- **[2026-09-23] Pemanggilan skill dijadikan langkah wajib di alur kerja, bukan cuma tabel
  referensi** (lihat AGENTS.md bagian 5, langkah 2 baru) — user amati skill `ui-ux-pro-max`,
  `web-design-guidelines`, `codebase-design` nggak pernah dipanggil spontan padahal sudah ada
  di tabel "kapan dipakai" bagian 8; cuma `ui-taste` yang kepake karena diminta eksplisit.
  Akar masalah: skill tidak otomatis ke-load, harus dipanggil aktif oleh model di momen itu —
  tabel referensi pasif tidak cukup untuk memicu itu. Solusi: alur kerja per-task sekarang
  punya langkah 2 "Cek skill yang relevan" sebagai checklist eksplisit sebelum implement, dan
  langkah "Laporkan" sekarang wajib sebutkan skill apa yang dipanggil (atau kenapa tidak).
- **[2026-09-23] Formatter default disiapkan di template** (lihat `docs/agents/code-standards.md`)
  — Prettier (`.prettierrc.json`, `.prettierignore` di root) untuk React/JS-TS, Ruff
  (`backend/pyproject.toml`) untuk FastAPI/Python, sesuai stack default di bagian 9. Wajib
  jalan sebelum baris kode pertama, bukan ditunda. Kalau stack final beda dari default (mis.
  pindah ke Vue/Laravel), config ini harus disesuaikan/diganti, jangan dibiarkan basi nempel
  di repo — dicatat instruksinya eksplisit di code-standards.md.

## Open questions

- Produk konkret apa yang akan dibangun pertama kali pakai template ini?
- Untuk project pertama: tetap pakai default FastAPI + React, atau ganti?
- Database & target deploy?

## Next steps

- Saat mulai project nyata pertama dari template ini: konfirmasi **stack** (default FastAPI+React
  atau ganti) DAN konfirmasi **konteks produk** (asumsi saat ini: internal admin dashboard —
  lihat AGENTS.md bagian 8), lalu isi AGENTS.md bagian 3 (overview) dan 9 (stack) sesuai
  jawabannya. Kalau konteks produk berubah jadi client-facing, pasang lagi skill
  `frontend-design` (lihat AGENTS.md bagian 8).
