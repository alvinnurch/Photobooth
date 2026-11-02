# Panduan Pasang Foto Booth Otomatis

Alur di lokasi: tamu mengetuk layar → hitung mundur → foto diambil → QRIS donasi tampil di tablet → tamu membayar dari ponselnya → strip langsung tercetak → QR di strip untuk mengunduh versi digital (gratis).

## Yang dibutuhkan
- Tablet/HP Android dengan Chrome
- Printer Wi-Fi yang mendukung kertas 4×6 (Canon Selphy, Epson, HP, dll.) dan plugin cetak Android (Mopria Print Service atau plugin merek printer)
- Akun Google (untuk menyimpan foto di Drive)
- Internet di lokasi (hotspot pun cukup)

## 1. Siapkan penyimpanan foto (sekali saja, ±10 menit)
1. Di Google Drive, buat folder, misalnya `Foto Booth – Milad 64`. Buka folder itu, salin ID dari URL (bagian setelah `/folders/`).
2. Buka https://script.google.com → **Proyek baru**. Hapus isi bawaan, tempel seluruh isi `Code.gs`.
3. Di bagian atas `Code.gs`, isi `FOLDER_ID`, `NAMA_ACARA`, `NOMINAL_MIN`, dan (opsional) `LOG_SHEET_ID`. Simpan.
4. Simpan kunci Xendit: **Setelan proyek (ikon roda) → Properti skrip → Tambahkan properti**
   - Properti: `XENDIT_SECRET_KEY`
   - Nilai: kunci rahasia dari dasbor Xendit (**Settings → API Keys**). Pastikan kunci punya izin **Money-in: Write**.
5. Klik **Terapkan → Deployment baru → ikon roda → Aplikasi web**.
   - Jalankan sebagai: **Saya**
   - Yang memiliki akses: **Siapa saja**
6. Klik **Terapkan**, izinkan akses Drive dan "layanan eksternal" saat diminta, lalu salin **URL aplikasi web** (berakhiran `/exec`).

Setiap kali `Code.gs` diubah, buat ulang dengan **Kelola deployment → Edit → Versi baru** agar URL-nya tetap sama.

## Donasi lewat QRIS Xendit (syarat cetak)
Aktifkan di tablet: ⚙ → **Cetak setelah donasi lewat QRIS (Xendit)**, lalu isi **Pilihan nominal donasi** (misalnya `5000, 10000, 20000`).

Alur di booth:
1. Setelah pose terakhir, layar hasil menampilkan strip dan pilihan nominal. Cetak **ditahan**.
2. Tamu memilih nominal (atau mengetik sendiri) → **Tampilkan QRIS**. QRIS dari Xendit muncul besar di tablet.
3. Tamu memindai dengan m-banking atau e-wallet di ponselnya. Tablet memeriksa status ke Xendit setiap 2–3 detik.
4. Begitu lunas: bunyi konfirmasi, lalu strip tercetak sesuai *Jumlah cetak per sesi*.
5. Unduh versi digital lewat QR di strip tetap **gratis**.

Tombol operator:
- **Cetak tanpa QRIS**: untuk donasi tunai, tamu undangan, atau saat internet putus. Jika PIN operator diisi, tombol ini meminta PIN.
- **Batal**: tidak mencetak dan kembali ke kamera. Layar donasi juga menutup sendiri jika 3 menit tidak ada aksi.
- **Ganti nominal**: membuat QRIS baru dengan nominal lain.

Pengaturan di `Code.gs`:

| Pengaturan | Fungsi |
|---|---|
| `NOMINAL_MIN` | Donasi terkecil yang diterima (dicek di server, bukan di tablet) |
| `MASA_BERLAKU_QR_MENIT` | Lama QRIS berlaku (bawaan 15 menit) |
| `LOG_SHEET_ID` | Opsional: ID Google Sheet untuk mencatat setiap donasi (waktu, kode foto, nominal, ID Xendit) |

Donasi tunai lewat tombol operator tidak tercatat di Sheet. Catat terpisah bila perlu.

**Uji coba dulu dengan kunci mode uji** (`xnd_development_…`). Di mode ini muncul tombol **Simulasikan bayar (uji)** di bawah QRIS, jadi alur sampai cetak bisa dites tanpa uang sungguhan. Untuk acara, ganti nilai properti `XENDIT_SECRET_KEY` dengan kunci live (`xnd_production_…`). Lalu tekan **Tes sambungan** di tablet: hasilnya harus menampilkan "Xendit: mode live".

Tentang akun Xendit:
- Akun harus sudah **terverifikasi** dan metode **QRIS** aktif. Nama yang muncul di aplikasi bank pembayar adalah nama merchant di akun Xendit, jadi untuk donasi atas nama lembaga, pakai akun atas nama lembaga tersebut.
- Dana masuk ke saldo Xendit, dipotong biaya transaksi QRIS sesuai tarif Xendit.
- Booth butuh internet stabil selama acara, karena QRIS dibuat dan dicek lewat server.

## 2. Pasang aplikasi di tablet
Kamera di Chrome hanya aktif lewat alamat **https://**. Cara termudah:
1. Buka https://app.netlify.com/drop di laptop, seret folder yang berisi `photobooth.html` (ganti namanya jadi `index.html` dulu). Anda langsung mendapat alamat https.
   Alternatif: GitHub Pages, atau hosting milik sendiri.
2. Buka alamat itu di Chrome tablet, izinkan kamera.
3. Menu Chrome → **Tambahkan ke layar utama**, lalu buka dari ikon itu agar tampil layar penuh.

## 3. Atur aplikasi
Ketuk ikon ⚙ di pojok kanan atas:
- Nama acara, baris kedua, tanggal, warna
- Tempel URL `/exec` tadi → **Tes sambungan** harus menampilkan nama folder Drive
- Template bingkai (lihat bagian berikut)
- PIN operator agar tamu tidak bisa mengubah pengaturan
- Simpan

## Template bingkai per acara
Aplikasi menyimpan banyak template sekaligus di tablet. Sebelum acara, cukup pilih yang dipakai.

Ada dua jenis template:

| Jenis | Ukuran kanvas | Kertas cetak |
|---|---|---|
| **Lembar** | 1200 × 1800 px (4×6 inci) | 4×6 inci |
| **Strip** | 600 × 1800 px (2×6 inci) | 2×6 inci, satu strip per kertas. Jika printer hanya punya ukuran 4×6, pilih "4×6 inci, strip di tengah" di pengaturan (ada tanda potong). |

Setiap sesi foto langsung mencetak **2 lembar** (bisa diubah 1–5 di *Jumlah cetak per sesi*). Semua lembar dikirim dalam satu dialog cetak, jadi tetap satu ketukan.

Aplikasi mengenali jenisnya dari perbandingan ukuran gambar. **Setiap lubang transparan = satu pose.** Tiga lubang berarti tamu difoto tiga kali berturut-turut, dengan hitung mundur di tiap pose ("Pose 2 dari 3"). Jeda antar pose diatur di pengaturan.

Tanpa mendesain apa pun, pilih **Bawaan: strip**. Isinya nama acara di atas, 3 foto (bisa diubah 2–4), lalu keterangan, tanggal, dan QR di bawah.

QR mengarah ke satu strip saja (bukan lembar ganda), jadi tamu mengunduh strip yang rapi.

**Membuat template (Canva, Photoshop, Illustrator, dll.)**
1. Buat kanvas **1200 × 1800 px** untuk lembar, atau **600 × 1800 px** untuk strip.
2. Pakai `template-panduan.png` (lembar) atau `template-strip-panduan.png` (strip) sebagai lapisan acuan. Lapisan ini hanya penanda, jangan ikut diekspor.
3. Desain bingkainya: nama acara, logo, tanggal, ornamen.
4. Kosongkan (hapus latar) bagian tempat foto. Bentuknya bebas: persegi, sudut membulat, lengkung, dan lain-lain.
5. Sisakan pojok yang kosong untuk QR. Bawaannya di kanan bawah, ±290 × 334 px dari tepi 40 px.
6. Ekspor sebagai **PNG berlatar transparan** (di Canva Pro: centang "Latar belakang transparan").

**Memasang di aplikasi**
- ⚙ → Template bingkai → **Unggah template baru**. Bisa pilih beberapa file sekaligus.
- Aplikasi mendeteksi area transparan sebagai tempat foto. Bingkai panduan di layar kamera ikut menyesuaikan bentuknya.
- Ketuk template untuk memakainya. Atur posisi dan ukuran QR, lalu cek di **Pratinjau cetakan**.
- Ganti nama atau hapus template langsung dari kartunya.
- Pilih **Bawaan** untuk kembali ke tata letak teks otomatis (nama acara dari kolom pengaturan).

`contoh-bingkai.png` (lembar) dan `contoh-strip.png` (strip 3 foto) adalah contoh template jadi yang bisa langsung diunggah untuk uji coba.

Lubang foto jangan sampai bersentuhan satu sama lain atau dengan tepi kanvas. Area transparan yang menyatu akan terbaca sebagai satu foto.

Template tersimpan di Chrome pada tablet itu saja. Kalau pindah perangkat atau data Chrome dihapus, unggah ulang file PNG-nya.

## 4. Atur printer (penting agar cetak cepat)
1. Pasang **Mopria Print Service** atau plugin merek printer dari Play Store, sambungkan ke Wi-Fi yang sama dengan printer.
2. Lakukan satu foto uji. Di dialog cetak pilih printer dan ukuran kertas yang sesuai: **2×6 in** untuk strip, atau **4×6 in / 10×15 cm** untuk lembar. Pilih tanpa margin bila ada pilihannya, dan biarkan jumlah salinan **1**, karena aplikasi sudah mengirim 2 halaman. Android biasanya mengingat pilihan terakhir, jadi tamu berikutnya cukup satu ketukan **Cetak**.

Catatan: browser di tablet selalu menampilkan dialog cetak dan tidak bisa mencetak tanpa konfirmasi sama sekali. Jika butuh cetak benar-benar tanpa sentuhan, jalankan aplikasi ini di laptop Windows dengan Chrome mode kios:
`chrome.exe --kiosk --kiosk-printing https://alamat-anda`
(atur printer default ke printer foto, ukuran 4×6, sebelumnya).

## Tips di lokasi
- Tombol remote Bluetooth (shutter tongsis) bisa dipakai sebagai pemicu foto.
- Titik status di kanan atas: hijau = tersambung, kuning = ada foto mengantre, merah = gagal unggah (aplikasi mencoba lagi otomatis; foto tetap tercetak).
- Jika internet putus, foto tetap dicetak. QR akan berfungsi setelah antrean terunggah, selama aplikasi tidak ditutup.
- Semua foto terkumpul di folder Drive tadi; panitia bisa membagikan folder itu setelah acara.
