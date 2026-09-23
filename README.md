# project-structure

Template/kerangka standar untuk memulai project baru dengan konvensi yang ramah
AI coding agent (Claude Code, dll.) sejak awal — bukan produk itu sendiri.

Cara pakai: clone/copy repo ini sebagai titik awal project baru, lalu di sesi
pertama minta agent konfirmasi stack (default: FastAPI + React) dan isi konteks
produknya.

## Peta dokumen

- [AGENTS.md](AGENTS.md) — instruksi utama untuk AI agent: struktur, konvensi,
  protokol memory, skill yang terpasang. Baca ini duluan.
- [memory/PRD.md](memory/PRD.md) — status project, keputusan yang sudah diambil,
  dan rencana berikutnya. Persisten lintas sesi.
- [memory/backlog.md](memory/backlog.md) — catatan/ide mentah sebelum jadi task.
- `.agents/skills/` — skill yang di-install lewat [skills.sh](https://www.skills.sh/),
  dikunci di `skills-lock.json`.

## Struktur

```
backend/    # service backend (default: FastAPI)
frontend/   # aplikasi frontend (default: React)
memory/     # PRD + backlog, lihat di atas
```

Detail lengkap dan alasan tiap keputusan ada di `AGENTS.md`.
