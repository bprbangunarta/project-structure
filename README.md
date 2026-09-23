# project-structure

Template/kerangka standar untuk memulai project baru dengan konvensi yang ramah
AI coding agent (Claude Code, dll.) sejak awal — bukan produk itu sendiri.

Cara pakai: klik **"Use this template"** di halaman GitHub repo ini untuk bikin repo
project baru (dapat riwayat git bersih, bukan bawa-bawa history template ini). Lalu
di sesi pertama, minta agent baca `memory/PRD.md`, konfirmasi stack (default: FastAPI
+ React) dan konteks produk (default: internal admin dashboard), dan isi konteks
produknya — lihat AGENTS.md bagian 1, 3, 8, 9.

## Peta dokumen

- [AGENTS.md](AGENTS.md) — index instruksi utama untuk AI agent: status, struktur,
  alur kerja, skill, stack. Baca ini duluan.
- `docs/agents/` — panduan detail yang dirujuk dari AGENTS.md: standar kode/kualitas
  ([code-standards.md](docs/agents/code-standards.md)) dan cara menangani referensi
  eksternal ([handling-references.md](docs/agents/handling-references.md)).
- [memory/PRD.md](memory/PRD.md) — status project, keputusan yang sudah diambil,
  dan rencana berikutnya. Persisten lintas sesi.
- [memory/backlog.md](memory/backlog.md) — catatan/ide mentah sebelum jadi task.
- `.agents/skills/` — skill yang di-install lewat [skills.sh](https://www.skills.sh/),
  dikunci di `skills-lock.json`.

## Struktur

```
backend/    # service backend (default: FastAPI) — sudah ada starter config Ruff
frontend/   # aplikasi frontend (default: React) — format pakai Prettier (config di root)
memory/     # PRD + backlog, lihat di atas
```

Formatter default sudah disiapkan: `.prettierrc.json`/`.prettierignore` (JS/TS) dan
`backend/pyproject.toml` (Python/Ruff) — sesuaikan/ganti kalau stack final beda dari
default, lihat `docs/agents/code-standards.md`.

Detail lengkap dan alasan tiap keputusan ada di `AGENTS.md`.
