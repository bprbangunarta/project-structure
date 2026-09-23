# AGENTS.md

Instruksi ini berlaku untuk semua AI agent (Claude Code, dst.) yang bekerja di repo ini.
Ini adalah sumber kebenaran utama untuk konteks project, struktur, dan cara pakai skill.
`CLAUDE.md` hanya menunjuk ke file ini — edit di sini, bukan di sana.

## 1. Status project

**Fase: template.** Repo ini adalah kerangka awal untuk project baru, bukan produk final.
Bagian 10 sudah punya stack *default* (FastAPI + React) dan bagian 9 punya asumsi konteks
produk (internal admin dashboard) — tapi keduanya cuma default/asumsi kerja yang WAJIB
dikonfirmasi ulang ke user begitu template ini dipakai untuk project nyata (lihat instruksi
konfirmasi di bagian 9 & 10). Jangan anggap default itu keputusan final tanpa konfirmasi.
Begitu dikonfirmasi (dipakai apa adanya atau diganti), catat di `memory/PRD.md` — jangan
biarkan keputusan hanya hidup di riwayat chat.

## 2. Standar kualitas: production, bukan prototipe

**Project yang dibangun dari template ini production-ready, bukan demo/POC sekali pakai.**
Ini mengubah default behavior di beberapa titik — kalau ada konflik antara "cepat selesai"
dan aturan di bawah, aturan di bawah yang menang:

- **Jangan tinggalkan mock/stub/placeholder secara diam-diam.** Kalau terpaksa nunda sesuatu
  (integrasi belum siap, keputusan belum final), tandai eksplisit (`# TODO: ...` dengan alasan)
  DAN sebutkan ke user di laporan — jangan biarkan mereka ngira itu implementasi asli.
- **Jangan bypass gate demi cepat selesai**: tidak `--no-verify`, tidak disable lint rule/type
  check tanpa alasan tertulis, tidak `except: pass` atau catch-and-swallow error diam-diam.
  Kalau ada check yang gagal, perbaiki root cause-nya.
- **Validasi & error handling di boundary itu wajib, bukan opsional** — semua input dari user
  atau API eksternal divalidasi; error dikembalikan dengan pesan/status yang jelas (bukan
  stack trace mentah bocor ke response), dan dicatat lewat logging yang berguna untuk debug
  production nanti (bukan `print`/`console.log` tercecer).
- **Jangan hardcode secret, credential, atau URL environment-spesifik.** Semua lewat env var;
  `.env.example` harus selalu sinkron dengan variabel yang benar-benar dipakai kode.
- **Perubahan skema database lewat migration file**, bukan edit manual/ad-hoc ke database.
- **Pilih dependency yang maintained**, hindari package abandoned/deprecated untuk hal krusial,
  dan jangan nambah dependency berat untuk kebutuhan yang bisa diselesaikan dengan sedikit kode.
- **Perbaikan bug = root cause + cek dampak lintas kode, bukan tambal di satu titik.** Sebelum
  menganggap fix selesai: cari apakah pola/logic yang sama dipakai di tempat lain (form lain,
  endpoint lain, komponen sejenis) yang mungkin punya bug yang sama, dan perbaiki bersamaan
  (atau minimal laporkan ke user kalau scope-nya besar). Kalau fix yang benar butuh perubahan
  struktural lebih luas (mis. logic dipindah ke shared function/component), lakukan itu —
  jangan hindari demi "biar diffnya kecil". Fix lokal yang meninggalkan duplikat bug di tempat
  lain bukan fix yang selesai, itu tambal sulam.
- **Validasi & constraint UI adalah satu paket, bukan dua task terpisah.** Setiap kali
  menambah/mengubah aturan validasi field (format, panjang, karakter yang diizinkan, required),
  implementasikan pasangannya di UI pada saat yang sama — input type/mode, pattern, maxlength,
  keyboard numeric untuk field angka, dst. "Validasi backend benar tapi UI masih terima input
  bebas" dianggap belum selesai, bukan boleh diperbaiki nanti. Untuk field dengan pola yang
  berulang di banyak form (nomor telepon, email, NIK, dst.), buat reusable field
  component/schema yang membungkus validasi + constraint UI sekaligus (lihat bagian 10 untuk
  saran konkret di stack default), supaya tidak diperbaiki manual satu-satu tiap ada field baru.
- **Definition of done = benar-benar diverifikasi** (lihat bagian 8, langkah 3) — kode yang
  "kelihatannya benar" tapi belum dijalankan/ditest bukan selesai.
- Kalau demi deadline terpaksa ambil jalan pintas/technical debt, itu harus **keputusan sadar
  yang dikomunikasikan ke user dan dicatat di `memory/PRD.md`** — bukan diam-diam dilakukan
  lalu dilupakan.

## 3. Menangani referensi (gambar, contoh kode, situs lain)

**Masalah yang sering terjadi:** dikasih referensi (screenshot UI, link produk lain, snippet
kode dari luar), lalu referensi itu dicontek 100% — termasuk hal-hal yang sebenarnya harus
tetap ikut aturan project ini (bagian 2, 9, 11). Referensi itu sinyal yang konkret dan
langsung kelihatan, jadi gampang mengalahkan aturan tertulis yang abstrak kalau tidak
disadari secara eksplisit. Jangan biarkan ini terjadi:

- **Referensi hanya untuk aspek spesifik yang diminta, bukan lisensi untuk override semua
  aturan project.** Sebelum implementasi, sebutkan eksplisit ke user: bagian mana dari
  referensi yang mau diadopsi (mis. "layout grid dan struktur navigasinya") dan bagian mana
  yang tetap ikut aturan project ini, bukan ikut referensi (mis. palet warna/tone tetap
  konsisten dengan produk existing, bukan asal contek dari referensi kalau konteksnya beda —
  lihat catatan skill di bagian 9 soal internal admin dashboard vs landing page).
- **Kalau referensi bentrok dengan aturan tertulis di file ini** (mis. referensi gayanya
  landing page yang flashy, padahal produk ini internal dashboard yang harus konsisten), STOP
  dan tanya user secara eksplisit mana yang menang — jangan diam-diam pilih salah satu.
- **Referensi kode dari luar (StackOverflow, repo lain, AI tool lain) sama perlakuannya:**
  ambil pola/solusi teknisnya, tapi saring lewat konvensi kode di bagian 11 — jangan copy-paste
  gaya penulisan asing yang beda konvensi dari project ini.
- Ini berlaku juga untuk langkah 1 ("Pahami maksud") di alur kerja per-task (bagian 8) — bagian
  dari "memahami maksud" adalah memisahkan mana permintaan eksplisit user dan mana yang cuma
  ikut-ikutan referensi tanpa disadari.

## 4. Project overview

<!-- TODO: isi begitu ada kejelasan produk -->
- **Apa yang dibangun:** belum ditentukan
- **Untuk siapa:** belum ditentukan
- **Masalah yang diselesaikan:** belum ditentukan

## 5. Struktur repo

```
.
├── AGENTS.md           # file ini — instruksi utama untuk agent
├── CLAUDE.md            # pointer ke AGENTS.md, jangan diisi konten lain
├── memory/
│   └── PRD.md            # memory persisten lintas sesi — lihat bagian 6
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
  (AGENTS.md) atau cukup dibaca dari kode saat dibutuhkan. PRD.md untuk konteks yang
  *tidak* tersirat dari kode.

## 7. Backlog / inbox (`memory/backlog.md`)

Tempat nampung ide/catatan mentah sebelum jadi task yang jelas (versi ringan dari pola
inbox-processing, tanpa infrastruktur multi-agent/worktree yang belum dibutuhkan di fase
ini). Saat diminta triage, pecah entri di `memory/backlog.md` jadi task jelas, pindahkan ke
`memory/PRD.md` ("Next steps" atau "Keputusan yang sudah diambil"), lalu hapus dari backlog.

## 8. Alur kerja per-task

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
   implementasi), update `memory/PRD.md` sesuai bagian 6.

## 9. Skill yang terpasang (`.agents/skills/`, lihat `skills-lock.json`)

Skill di-manage lewat [skills.sh](https://www.skills.sh/) — jangan edit isinya manual,
update lewat mekanisme skills.sh supaya `skills-lock.json` tetap akurat.

| Skill | Kapan dipakai |
|---|---|
| `ui-ux-pro-max` | Saat mengerjakan struktur UI, komponen, design system, aksesibilitas, interaksi, responsive layout — referensi cepat untuk style/palette/font-pairing/ikon/chart. |
| `web-design-guidelines` | Saat diminta review UI/aksesibilitas/UX terhadap best practice (mis. "review UI ini", "audit accessibility"). |

**Asumsi konteks produk saat ini: internal admin dashboard, bukan landing page/marketing
site.** Ini asumsi kerja dari percakapan waktu template ini disusun — SAMA seperti stack di
bagian 10, ini WAJIB dikonfirmasi ulang ke user di awal tiap project baru, bukan otomatis
diwariskan. Kalau dikonfirmasi tetap internal tool: prioritaskan konsistensi, kejelasan
informasi, dan pola UI yang familiar (tabel data, form, navigasi admin) di atas eksplorasi
estetika/brand identity — skill `frontend-design` (distinctive visual identity ala studio
desain) sudah dilepas karena cocoknya untuk produk client-facing, jangan pasang lagi kecuali
konfirmasi user mengubah konteks ini jadi client-facing.

Skill set saat ini fokus ke UI/UX dashboard. Begitu stack backend ditentukan, evaluasi apakah
perlu menambah skill yang relevan (API design, database, testing framework spesifik stack
tersebut) lewat skills.sh, dan catat di tabel ini.

## 10. Stack teknis

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

**Catatan soal struktur folder (lihat bagian 5):** FastAPI + React secara alami cocok dengan
split `backend/`/`frontend/` yang sudah ada (dua service independen, dua container). Tapi
kalau user memilih stack full-stack opinionated (mis. Next.js App Router, Laravel, Django
dengan template server-side) yang punya struktur folder sendiri, **konvensi framework itu
yang menang** — jangan paksa masuk ke split `backend/`/`frontend/` generik ini kalau
bertentangan. Diskusikan dan update bagian 5 kalau itu terjadi.

**Catatan implementasi untuk pola validasi+UI (lihat bagian 2):** dengan FastAPI, validasi
field didefinisikan lewat Pydantic model — field constraint (`pattern`, `max_length`, tipe,
dst.) ada di satu tempat sebagai source of truth. Di React, jangan bikin ulang aturan itu
secara manual di tiap form; bungkus tiap tipe field yang berulang (nomor telepon, email, NIK,
dst.) jadi komponen input reusable (mis. `<PhoneNumberInput />`) yang sudah include `inputMode`/
`pattern`/`maxLength` yang sesuai, supaya field baru tinggal pakai komponennya, bukan menulis
constraint UI dari nol tiap kali.

## 11. Konvensi umum

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
  di sini setelah bagian 10 (Stack teknis) diisi — jangan biarkan bagian ini kosong lagi
  begitu stack final.

<!-- TODO: setelah stack dipilih — tambahkan linter/formatter, testing convention,
     struktur folder per fitur/module, dan commit message style di sini. -->
