# Kasir Toko (APK)

Aplikasi Android (Capacitor, satu file HTML) untuk toko grosir dan eceran. Kasir, barang dan stok, purchase order (PO), member dan poin, pesanan antar, dan laporan.

## Fitur
- **Kasir:** scan atau cari barang, mode Eceran atau Grosir, diskon, pembayaran Tunai/QRIS/Transfer, kembalian, struk (cetak atau kirim WhatsApp). Stok berkurang otomatis.
- **Barang dan stok:** kode/barcode, satuan, harga beli, harga eceran dan grosir, batas stok menipis, stok opname.
- **PO:** buat PO, kirim ke supplier, terima barang (boleh sebagian). Stok dan harga beli ikut diperbarui.
- **Laporan:** harian, mingguan, bulanan, tahunan (omzet, jumlah transaksi, laba kotor, produk terlaris, penjualan per kasir) dan ekspor CSV.
- **Member dan poin:** daftar member (nama dan telepon). Tiap belanja Rp 10.000 dapat 1 poin, 1 poin = potongan Rp 100, tingkat Gold bila total belanja mencapai Rp 5.000.000. Pilih member dan tukar poin di layar Kasir.
- **Pesanan antar / ambil:** dari Kasir tekan *Jadikan Pesanan*, isi alamat, jam, dan ongkir. Status: Baru, Diproses, Siap, Selesai (bayar), atau Batal (stok kembali). Pesanan selesai masuk ke laporan.
- **Akun:** peran Pemilik, Admin gudang, dan Kasir; ganti sandi; cadangan data.

Akun awal: `pemilik` / `admin123`. Segera ganti sandi di menu Akun.

## Build APK
Sama seperti proyek reservasi: unggah ke GitHub, lalu jalankan *Actions > Build APK (debug)* dan unduh artifact `kasir-toko-debug-apk`. Untuk rilis bertanda tangan, jalankan `bash scripts/buat-keystore.sh` dan ikuti `release.yml`. Ganti ikon di folder `assets/` sesuai logo toko.

## Batasan
- Pesanan dan member dicatat oleh staf; pelanggan belum punya aplikasi sendiri. Sinkronisasi antar HP (Firebase) sudah ada sebagai dasar untuk aplikasi pelanggan nanti.
- Ongkir dihitung sebagai pendapatan di laporan. Satu satuan per barang. Belum ada hutang supplier dan tutup shift.

## Sinkronisasi antar HP (Firebase Firestore)
Data tetap disimpan di HP (jalan offline), lalu dikirim dan ditarik otomatis tiap 15 detik saat ada internet. Stok, poin, dan total belanja member dikirim sebagai selisih (+/−), jadi dua kasir yang menjual barang sama tidak saling menimpa. Nomor transaksi memakai kode unik per HP agar tidak bentrok.

Persiapan:
1. Buat proyek di console.firebase.google.com, lalu aktifkan **Firestore** dan **Authentication > Email/Password**.
2. Di Authentication, tambah satu pengguna (misal sinkron@tokoku.com + sandi). Akun ini dipakai semua HP.
3. Di Firestore > Rules, tempel:
```
rules_version = '2';
service cloud.firestore { match /databases/{d}/documents {
  match /t/{toko}/{document=**} { allow read, write: if request.auth != null; }
}}
```
4. Di app: Akun > Sinkronisasi. Isi Project ID, API key (Project settings > Web), email, sandi. HP pertama pilih *unggah*, HP lain pilih *ambil*.

Catatan: sandi akun sinkron tersimpan di HP. Nama toko belum ikut tersinkron.
