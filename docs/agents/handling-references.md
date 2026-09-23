# Menangani referensi (gambar, contoh kode, situs lain)

> Dirujuk dari [AGENTS.md](../../AGENTS.md) bagian 5 (Alur kerja per-task), langkah 1
> (Pahami maksud). Baca file ini setiap kali user memberi referensi eksternal — screenshot
> UI, link produk lain, snippet kode dari luar — sebelum mulai implementasi.

**Masalah yang sering terjadi:** dikasih referensi, lalu referensi itu dicontek 100% —
termasuk hal-hal yang sebenarnya harus tetap ikut aturan project ini ([code-standards.md](code-standards.md),
skill context di AGENTS.md bagian 8). Referensi itu sinyal yang konkret dan langsung
kelihatan, jadi gampang mengalahkan aturan tertulis yang abstrak kalau tidak disadari secara
eksplisit. Jangan biarkan ini terjadi:

- **Referensi hanya untuk aspek spesifik yang diminta, bukan lisensi untuk override semua
  aturan project.** Sebelum implementasi, sebutkan eksplisit ke user: bagian mana dari
  referensi yang mau diadopsi (mis. "layout grid dan struktur navigasinya") dan bagian mana
  yang tetap ikut aturan project ini, bukan ikut referensi (mis. palet warna/tone tetap
  konsisten dengan produk existing, bukan asal contek dari referensi kalau konteksnya beda —
  lihat catatan skill di AGENTS.md bagian 8 soal konteks produk).
- **Kalau referensi bentrok dengan aturan tertulis di project ini** (mis. referensi gayanya
  landing page yang flashy, padahal produk ini internal dashboard yang harus konsisten), STOP
  dan tanya user secara eksplisit mana yang menang — jangan diam-diam pilih salah satu.
- **Referensi kode dari luar (StackOverflow, repo lain, AI tool lain) sama perlakuannya:**
  ambil pola/solusi teknisnya, tapi saring lewat konvensi kode di [code-standards.md](code-standards.md)
  — jangan copy-paste gaya penulisan asing yang beda konvensi dari project ini.
- Bagian dari "memahami maksud" (langkah 1 alur kerja per-task) adalah memisahkan mana
  permintaan eksplisit user dan mana yang cuma ikut-ikutan referensi tanpa disadari.
