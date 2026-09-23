# PRD / Project Memory

> File ini adalah memory persisten lintas sesi. Protokol baca/update ada di
> [AGENTS.md](../AGENTS.md) bagian 4. Update file ini setiap ada keputusan atau
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
  AGENTS.md bagian 8). Jangan anggap ini final tanpa konfirmasi eksplisit.
- **[2026-09-23] Skill `frontend-design` dilepas** — cocoknya untuk produk client-facing
  (landing page/marketing site), bukan tooling internal. Dipertahankan: `ui-ux-pro-max`,
  `web-design-guidelines`.
- **[2026-09-23] Struktur folder `backend/`/`frontend/` dipisah di root** — untuk fleksibilitas
  containerization (Docker) per-service. Catatan: kalau stack akhir yang dipilih adalah
  framework full-stack opinionated (Next.js, Laravel, dst.), konvensi framework itu yang
  menang, bukan split ini (lihat AGENTS.md bagian 8).

## Open questions

- Produk konkret apa yang akan dibangun pertama kali pakai template ini?
- Untuk project pertama: tetap pakai default FastAPI + React, atau ganti?
- Database & target deploy?

## Next steps

- Saat mulai project nyata pertama dari template ini: konfirmasi stack (default atau ganti),
  lalu isi AGENTS.md bagian 2 (overview) dan 8 (stack) sesuai jawabannya.
