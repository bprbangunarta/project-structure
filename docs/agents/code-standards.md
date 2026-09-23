# Standar kode & kualitas

> Dirujuk dari [AGENTS.md](../../AGENTS.md) bagian 5 (Alur kerja per-task), langkah 2
> (Implement). Baca file ini sebelum menulis/mengubah kode kalau belum dibaca di sesi ini —
> ini bukan bacaan opsional, ini bagian dari definisi "implement dengan benar".

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
  component/schema yang membungkus validasi + constraint UI sekaligus (lihat AGENTS.md bagian
  9 untuk saran konkret di stack default), supaya tidak diperbaiki manual satu-satu tiap ada
  field baru.
- **Responsivitas layout ≠ responsivitas konten — keduanya wajib dicek terpisah.** Grid/flex
  dari component library otomatis menyesuaikan ukuran container, tapi TIDAK otomatis menjamin
  teks/angka di dalamnya muat. Sumber bug yang sering luput: angka besar di stat card (mis.
  "Rp 1.250.000.000") kepotong/meluber, label/nama panjang di tabel bikin kolom melebar tidak
  wajar, heading yang pas di desktop jadi kepanjangan di mobile. Wajib untuk setiap komponen UI
  baru yang menampilkan data dinamis:
  - Uji dengan **konten realistis/terpanjang yang mungkin terjadi**, bukan data sample pendek
    (mis. nama nasabah terpanjang yang masuk akal, plafon kredit dengan banyak digit) — bukan
    "Budi" dan "Rp 100".
  - Cek tampilan di **minimal 2 lebar layar berbeda** (mobile ~375px, desktop) sebelum
    menganggap selesai — bukan cuma di satu ukuran window default.
  - Kalau teks berpotensi panjang tak terduga (nama, alamat, keterangan), gunakan truncate +
    tooltip/expand, `line-clamp`, atau text wrapping — jangan biarkan meluber keluar kotak atau
    andalkan container auto-grow tanpa batas yang malah merusak layout di sekitarnya.
  - Ukuran teks (angka besar di stat card/dashboard khususnya) pakai unit yang scale wajar
    (mis. Tailwind responsive text classes `text-xl md:text-2xl`, atau `clamp()`), bukan satu
    ukuran fixed besar yang diasumsikan selalu muat.
  - Skill `ui-ux-pro-max` (lihat AGENTS.md bagian 8) relevan persis untuk kasus ini — pakai
    saat membangun komponen data-dense (dashboard, tabel, stat card), jangan cuma dipakai untuk
    styling awal lalu dilupakan pas nambah komponen baru berikutnya.
- **Definition of done = benar-benar diverifikasi** (lihat AGENTS.md bagian 5, langkah 3) —
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
- **Naming convention database (tabel & kolom) wajib satu jenis, jangan campur.** Ini bukan
  cuma soal rapi — nama tabel/kolom yang campur `snake_case` dan `camelCase` di database SQL
  itu sumber bug nyata: identifier tanpa quote di PostgreSQL/MySQL otomatis di-lowercase, jadi
  `namaLengkap` dan `namalengkap` bisa dianggap sama atau malah bentrok tanpa error yang jelas
  — persis gejala "hasilnya nggak jelas" yang sering muncul kalau ini didiamkan. **Default:
  `snake_case` untuk semua nama tabel & kolom** (standar SQL, aman dari masalah case-folding
  itu). Konfirmasi sekali ke user di awal project (bareng pertanyaan Database di AGENTS.md
  bagian 9) — kalau user tidak keberatan, pakai default ini tanpa nanya ulang tiap bikin tabel
  baru. Begitu dikonfirmasi, terapkan konsisten ke SELURUH schema, tidak boleh campur di
  tengah jalan. Layer di atasnya (JSON response API, misalnya) boleh beda konvensi (mis.
  `camelCase` sesuai kebiasaan JS/JSON) — itu urusan mapping di serialization layer (Pydantic
  alias, dsb.), bukan alasan untuk bikin nama kolom database sendiri ikut campur.
- Konvensi file/folder, linter, formatter, dan testing framework spesifik-stack ditambahkan
  di sini setelah AGENTS.md bagian 9 (Stack teknis) diisi — jangan biarkan bagian ini kosong
  lagi begitu stack final.

<!-- TODO: setelah stack dipilih — tambahkan linter/formatter, testing convention,
     struktur folder per fitur/module, dan commit message style di sini. -->
