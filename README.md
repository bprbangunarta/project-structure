# project-structure

Template universal untuk memulai project apa pun (web app, tool internal, landing page, API,
CLI/library, mobile, pipeline data, dst.) dengan konvensi yang ramah AI coding agent (Claude
Code, dll.) sejak awal. Ini kerangka, bukan produk.

Cara pakai: klik **"Use this template"** di halaman GitHub repo ini untuk bikin repo project
baru (riwayat git bersih). Di sesi pertama, agent membaca `AGENTS.md` lalu menjalankan
**kickoff**: menanyakan jenis project, stack, database, naming, dan formatter, membaca skill
terpasang untuk memutuskan mana yang dipakai dan kapan, lalu mencatat semuanya di
`memory/PRD.md` sebelum menulis kode. Tidak ada stack atau jenis project default.

## Peta dokumen

- [AGENTS.md](AGENTS.md) — index instruksi utama untuk AI agent: kickoff, struktur, alur kerja,
  skill, stack. Baca ini duluan.
- `docs/agents/` — panduan detail yang dirujuk dari AGENTS.md: standar kode/kualitas
  ([code-standards.md](docs/agents/code-standards.md)) dan cara menangani referensi
  eksternal ([handling-references.md](docs/agents/handling-references.md)).
- [memory/PRD.md](memory/PRD.md) — memory project lintas sesi: keputusan, rencana pemakaian
  skill, open questions. Dimulai kosong di tiap project baru.
- [memory/backlog.md](memory/backlog.md) — catatan/ide mentah sebelum jadi task.
- [docs/template-decisions.md](docs/template-decisions.md) — riwayat kenapa aturan template ini
  ada (untuk maintainer template; project turunan boleh menghapusnya).
- `.agents/skills/` — skill yang di-install lewat [skills.sh](https://www.skills.sh/),
  dikunci di `skills-lock.json`; `.claude/skills/` berisi symlink supaya terdaftar di Claude
  Code.

## Struktur

```
backend/    # titik awal project multi-service (opsional)
frontend/   # titik awal project multi-service (opsional)
memory/     # PRD + backlog, lihat di atas
```

`backend/` dan `frontend/` hanya titik awal untuk project multi-service; agent menyesuaikan
struktur dengan jenis project setelah kickoff (lihat AGENTS.md bagian 4). Formatter dan
linter sengaja tidak disertakan: agent menyiapkannya sesuai stack yang dipilih, sebelum kode
pertama ditulis.

Detail lengkap dan alasan tiap keputusan ada di `AGENTS.md`.
