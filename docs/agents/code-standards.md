# Standar kode & kualitas

> Dirujuk dari [AGENTS.md](../../AGENTS.md) bagian 5 (Alur kerja per-task), langkah 3
> (Implement). Baca file ini sebelum menulis/mengubah kode kalau belum dibaca di sesi ini —
> ini bukan bacaan opsional, ini bagian dari definisi "implement dengan benar".
>
> Aturan yang menyebut UI, database, atau API berlaku kalau project memang punya komponen
> itu (ditentukan saat kickoff, AGENTS.md bagian 1). Sisanya berlaku untuk semua project.

## Production, bukan prototipe

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
  component/schema yang membungkus validasi + constraint UI sekaligus, supaya tidak diperbaiki
  manual satu-satu tiap ada field baru. Contoh di stack FastAPI + React: model Pydantic jadi
  sumber kebenaran validasi, dan di React tiap tipe field berulang dibungkus komponen seperti
  `<PhoneNumberInput />` yang sudah membawa `inputMode`/`pattern`/`maxLength`. Prinsipnya sama
  di stack lain. Berlaku kalau project punya form/UI.
- **Responsivitas layout ≠ responsivitas konten — keduanya wajib dicek terpisah** (project
  ber-UI). Grid/flex
  dari component library otomatis menyesuaikan ukuran container, tapi TIDAK otomatis menjamin
  teks/angka di dalamnya muat. Sumber bug yang sering luput: angka besar di stat card (mis.
  "Rp 1.250.000.000") kepotong/meluber, label/nama panjang di tabel bikin kolom melebar tidak
  wajar, heading yang pas di desktop jadi kepanjangan di mobile. Wajib untuk setiap komponen UI
  baru yang menampilkan data dinamis:
  - Uji dengan **konten realistis/terpanjang yang mungkin terjadi**, bukan data sample pendek
    (mis. nama terpanjang yang masuk akal, nominal dengan banyak digit) — bukan
    "Budi" dan "Rp 100".
  - Cek tampilan di **minimal 2 lebar layar berbeda** (mobile ~375px, desktop) sebelum
    menganggap selesai — bukan cuma di satu ukuran window default.
  - Kalau teks berpotensi panjang tak terduga (nama, alamat, keterangan), gunakan truncate +
    tooltip/expand, `line-clamp`, atau text wrapping — jangan biarkan meluber keluar kotak atau
    andalkan container auto-grow tanpa batas yang malah merusak layout di sekitarnya.
  - Ukuran teks (angka besar di stat card/dashboard khususnya) pakai unit yang scale wajar
    (mis. Tailwind responsive text classes `text-xl md:text-2xl`, atau `clamp()`), bukan satu
    ukuran fixed besar yang diasumsikan selalu muat.
  - Untuk komponen data-dense (dashboard, tabel, stat card), skill `ui-ux-pro-max` (lihat
    AGENTS.md bagian 8) berguna sebagai sumber data (`--domain ux`, `--domain chart`); ikuti
    rencana pemakaian skill di `memory/PRD.md`, jangan cuma dipakai di awal lalu dilupakan
    pas nambah komponen baru.
  - **Sebelum melaporkan task UI selesai, jalankan visual review pakai skill `ui-taste`**
    (lihat AGENTS.md bagian 8) — ini bukan opsional untuk task yang menyentuh UI. Tujuannya
    memastikan hasilnya nggak cuma "rapi secara struktur" tapi juga punya perhatian ke detail
    selayaknya UI/UX designer (hierarchy, spacing, polish), bukan tampilan generik/"AI slop".
- **Definition of done = benar-benar diverifikasi** (lihat AGENTS.md bagian 5, langkah 4) —
  kode yang "kelihatannya benar" tapi belum dijalankan/ditest bukan selesai.
- Kalau demi deadline terpaksa ambil jalan pintas/technical debt, itu harus **keputusan sadar
  yang dikomunikasikan ke user dan dicatat di `memory/PRD.md`** — bukan diam-diam dilakukan
  lalu dilupakan.

## Konvensi penulisan kode

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
- **Naming convention database (tabel & kolom) wajib satu jenis, jangan campur** (kalau
  project memakai database SQL). Ini bukan
  cuma soal rapi — nama tabel/kolom yang campur `snake_case` dan `camelCase` di database SQL
  itu sumber bug nyata: identifier tanpa quote di PostgreSQL/MySQL otomatis di-lowercase, jadi
  `namaLengkap` dan `namalengkap` bisa dianggap sama atau malah bentrok tanpa error yang jelas
  — persis gejala "hasilnya nggak jelas" yang sering muncul kalau ini didiamkan. **Default:
  `snake_case` untuk semua nama tabel & kolom** (standar SQL, aman dari masalah case-folding
  itu). Konfirmasi sekali ke user di awal project (kickoff, AGENTS.md bagian 1) — kalau user
  tidak keberatan, pakai default ini tanpa nanya ulang tiap bikin tabel
  baru. Begitu dikonfirmasi, terapkan konsisten ke SELURUH schema, tidak boleh campur di
  tengah jalan. Layer di atasnya (JSON response API, misalnya) boleh beda konvensi (mis.
  `camelCase` sesuai kebiasaan JS/JSON) — itu urusan mapping di serialization layer (Pydantic
  alias, dsb.), bukan alasan untuk bikin nama kolom database sendiri ikut campur.
- **Bahasa untuk identifier teknis (tabel, kolom, variabel, fungsi, nama file) wajib satu
  bahasa konsisten — default Bahasa Inggris.** Domain project sering punya istilah dalam
  Bahasa Indonesia (nasabah, tunggakan, plafon, dst.), dan itu gampang bikin identifier teknis
  ikut campur (`customers` di satu tabel, `tunggakan` di tabel lain) — sama seperti masalah
  case-mixing di atas, ini bikin schema terasa acak dan menyulitkan pencarian/konsistensi
  jangka panjang. **Default: semua identifier teknis pakai Bahasa Inggris** (mis. `customers`,
  `overdue_amount`, `credit_limit`), walaupun istilah bisnisnya dalam Bahasa Indonesia —
  terjemahkan secara konsisten, jangan campur. Konfirmasi sekali di awal project (kickoff,
  AGENTS.md bagian 1); kalau user minta istilah Indonesia
  dipertahankan di identifier (mis. alasan domain/istilah baku), itu juga boleh, TAPI harus
  konsisten dipakai di semua identifier, bukan campur tergantung siapa yang nulis tabel itu.
  **Teks yang tampil ke user (label UI, pesan error, dokumentasi)** boleh dan sebaiknya tetap
  Bahasa Indonesia — aturan ini cuma untuk identifier teknis di kode/schema, bukan konten
  user-facing.
- **Formatter wajib disiapkan & dijalankan sebelum baris kode pertama ditulis, bukan "nanti
  aja".** Template ini SENGAJA tidak menyertakan file config formatter (stack baru ditentukan
  saat kickoff, jadi config yang dibawa dari awal pasti berisiko basi) — begitu stack
  dikonfirmasi ke user (AGENTS.md bagian 9), agent wajib setup formatter yang sesuai SEBELUM
  menulis kode pertama:
  - Ekosistem JS/TS (React, Next.js, Vue, dst.): Prettier (`.prettierrc`) + linter yang sesuai
    framework-nya (ESLint, dst.), jalankan `prettier --write` konsisten.
  - Python (FastAPI, Django, dst.): Ruff (format + lint sekaligus) di `pyproject.toml`.
  - Stack lain: pakai formatter standar ekosistemnya (mis. Laravel Pint untuk PHP) — jangan
    biarkan project jalan tanpa formatter cuma karena stack-nya kurang umum.
  - Begitu formatter dipilih & disetup, catat di `memory/PRD.md` dan update bagian ini
    (Konvensi penulisan kode) dengan aturan konkretnya, supaya sesi berikutnya nggak setup
    ulang dari nol atau pakai config yang beda-beda tiap sesi.
- Konvensi file/folder, testing framework, dan commit message style spesifik-stack ditambahkan
  di sini setelah AGENTS.md bagian 9 (Stack teknis) diisi — jangan biarkan bagian ini kosong
  lagi begitu stack final.

<!-- TODO: setelah stack dipilih — tambahkan setup formatter konkret, testing convention,
     struktur folder per fitur/module, dan commit message style di sini. -->
