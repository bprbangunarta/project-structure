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
  AGENTS.md bagian 10). Jangan anggap ini final tanpa konfirmasi eksplisit.
- **[2026-09-23] Skill `frontend-design` dilepas** — cocoknya untuk produk client-facing
  (landing page/marketing site), bukan tooling internal. Dipertahankan: `ui-ux-pro-max`,
  `web-design-guidelines`.
- **[2026-09-23] Struktur folder `backend/`/`frontend/` dipisah di root** — untuk fleksibilitas
  containerization (Docker) per-service. Catatan: kalau stack akhir yang dipilih adalah
  framework full-stack opinionated (Next.js, Laravel, dst.), konvensi framework itu yang
  menang, bukan split ini (lihat AGENTS.md bagian 10).
- **[2026-09-23] Standar kualitas: production-ready, bukan prototipe** — semua project dari
  template ini harus dianggap production-ready sejak awal (lihat AGENTS.md bagian 2): tidak
  boleh ada mock/stub diam-diam, tidak boleh bypass test/lint, wajib validasi & error handling
  di boundary, tidak hardcode secret, migration untuk perubahan skema DB, dan jalan pintas/
  technical debt harus dikomunikasikan + dicatat di sini, bukan didiamkan.
- **[2026-09-23] Aturan menangani referensi (gambar/kode/situs lain)** — referensi cuma
  diadopsi untuk aspek spesifik yang diminta, bukan lisensi untuk override aturan project
  (lihat AGENTS.md bagian 3). Ini merespons pengalaman berulang: agent ditunjukkan referensi
  (mis. screenshot UI) lalu mencontek 100% dan lupa aturan project yang sudah ditulis. Kalau
  referensi bentrok sama aturan tertulis, wajib stop dan tanya user, bukan diam-diam milih.
- **[2026-09-23] Bug fix wajib cek dampak lintas kode + validasi & UI constraint satu paket**
  (lihat AGENTS.md bagian 2 & 10) — merespons pengalaman berulang: (1) perbaikan cuma di satu
  titik tanpa cek pola serupa di tempat lain → tambal sulam menumpuk; (2) validasi backend
  benar tapi UI field (mis. nomor telepon) masih terima input bebas, jadi perbaikan berulang
  tiap ada field baru. Solusi: definition-of-done sekarang eksplisit mencakup cek dampak
  lintas kode untuk bug fix, dan reusable field component (validasi + UI constraint sekaligus)
  untuk field dengan pola berulang.

## Open questions

- Produk konkret apa yang akan dibangun pertama kali pakai template ini?
- Untuk project pertama: tetap pakai default FastAPI + React, atau ganti?
- Database & target deploy?

## Next steps

- Saat mulai project nyata pertama dari template ini: konfirmasi stack (default atau ganti),
  lalu isi AGENTS.md bagian 4 (overview) dan 10 (stack) sesuai jawabannya.
