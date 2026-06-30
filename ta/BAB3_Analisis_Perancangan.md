# BAB III — ANALISIS DAN PERANCANGAN SISTEM
Aplikasi Penjualan Kasir dengan Fitur Perhitungan Harga Pokok Penjualan (HPP)
Menggunakan Metode FIFO pada Homwok Coffee

> Catatan penomoran: nomor **Tabel** dan **Gambar** di bawah ini bersifat
> sementara dan disesuaikan dengan tata letak akhir dokumen. Struktur data tabel
> menggunakan Tabel 3.5–3.13 (lihat berkas `Perancangan_Struktur_Data_Tabel.md`),
> ERD pada Gambar 3.2 (berkas `ERD_Relasi_Gambar3-1.md` — sesuaikan captionnya
> menjadi Gambar 3.2), DAD Level 0/1 pada Gambar 3.3/3.4 (berkas
> `DAD_Level0_Konteks.md` dan `DAD_Level1.md`).

---

## 3.1 Deskripsi Sistem

Aplikasi yang dibangun merupakan **aplikasi penjualan kasir (*Point of Sale*)
berbasis web** untuk Homwok Coffee yang dilengkapi fitur perhitungan **Harga
Pokok Penjualan (HPP)** secara otomatis menggunakan **metode First-In First-Out
(FIFO)**. Sistem ini bertujuan menggantikan pencatatan penjualan dan persediaan
yang masih dilakukan secara manual, sekaligus menyediakan perhitungan HPP dan
laba kotor yang selama ini sulit diketahui pemilik usaha secara cepat.

Keunggulan utama sistem terletak pada **keterhubungan antara menu, resep, dan
persediaan bahan baku**. Setiap menu minuman memiliki **komposisi resep** (*Bill
of Materials*) yang merinci kebutuhan bahan baku per porsi. Ketika sebuah menu
terjual, sistem secara otomatis menerjemahkan menu tersebut menjadi pemakaian
bahan baku berdasarkan resepnya, lalu **memotong persediaan dari lot pembelian
terlama lebih dahulu (FIFO)**. Harga beli pada setiap lot yang terpotong menjadi
dasar perhitungan HPP, sehingga HPP dan laba kotor dapat dihitung secara akurat
pada setiap transaksi tanpa perhitungan manual.

Sistem dibangun dengan **arsitektur terpisah (*decoupled*)**: sisi *backend*
menggunakan *framework* **Laravel** yang berperan sebagai penyedia layanan
**REST API**, sedangkan sisi *frontend* menggunakan **Next.js (React)**.
Penyimpanan data menggunakan basis data **MySQL**. Terdapat tiga pihak yang
berinteraksi dengan sistem:

- **Barista (Kasir)** — pengguna utama yang menginput seluruh data operasional,
  meliputi data master (menu, bahan baku, resep, pegawai), pencatatan pembelian
  bahan baku, hingga transaksi penjualan.
- **Manager** — menerima dan menelaah laporan yang dihasilkan sistem, termasuk
  laporan HPP dan laba rugi kotor.
- **Pelanggan** — memberikan pesanan kepada barista dan menerima struk penjualan;
  pelanggan tidak mengakses sistem secara langsung.

---

## 3.2 Analisis Kebutuhan

### 3.2.1 Kebutuhan Fungsional

Kebutuhan fungsional menjelaskan layanan yang harus disediakan sistem. Adapun
kebutuhan fungsional aplikasi ditunjukkan pada tabel berikut.

**Tabel 3.1 Kebutuhan Fungsional**

| Kode | Kebutuhan Fungsional | Pengguna |
|------|----------------------|----------|
| KF-01 | Sistem menyediakan fasilitas *login* menggunakan *username* dan kata sandi. | Barista, Manager |
| KF-02 | Sistem dapat mengelola data pegawai (tambah, ubah, nonaktifkan) beserta perannya. | Barista |
| KF-03 | Sistem dapat mengelola data menu beserta harga jualnya. | Barista |
| KF-04 | Sistem dapat mengelola data bahan baku beserta satuan dan batas stok minimum. | Barista |
| KF-05 | Sistem dapat mengelola data resep (komposisi bahan baku per porsi menu). | Barista |
| KF-06 | Sistem dapat mencatat transaksi pembelian bahan baku dan membentuk **lot persediaan FIFO** (mengisi sisa kuantitas tiap lot). | Barista |
| KF-07 | Sistem dapat mencatat transaksi penjualan (pemilihan menu dan jumlah porsi). | Barista |
| KF-08 | Sistem menerjemahkan menu yang terjual menjadi kebutuhan bahan baku berdasarkan resep. | Sistem |
| KF-09 | Sistem menghitung **HPP secara otomatis dengan metode FIFO**, yaitu memotong lot pembelian terlama lebih dahulu. | Sistem |
| KF-10 | Sistem menghitung laba kotor (total penjualan − total HPP) per transaksi. | Sistem |
| KF-11 | Sistem mencetak struk/nota penjualan untuk pelanggan. | Barista |
| KF-12 | Sistem menampilkan informasi sisa stok dan peringatan ketika stok mencapai batas minimum. | Barista |
| KF-13 | Sistem menyajikan Laporan Penjualan. | Manager |
| KF-14 | Sistem menyajikan Laporan Pembelian. | Manager |
| KF-15 | Sistem menyajikan Laporan Persediaan (nilai dan sisa stok per bahan). | Manager |
| KF-16 | Sistem menyajikan Laporan Perputaran Persediaan. | Manager |
| KF-17 | Sistem menyajikan Laporan HPP. | Manager |
| KF-18 | Sistem menyajikan Laporan Laba Rugi Kotor. | Manager |

### 3.2.2 Kebutuhan Non-Fungsional

Kebutuhan non-fungsional menjelaskan batasan dan kualitas layanan yang harus
dipenuhi sistem.

**Tabel 3.2 Kebutuhan Non-Fungsional**

| Kode | Aspek | Kebutuhan Non-Fungsional |
|------|-------|--------------------------|
| KNF-01 | Keamanan | Kata sandi pengguna disimpan dalam bentuk *hash*; hak akses dibatasi sesuai peran (barista/manager). |
| KNF-02 | Kinerja | Proses perhitungan HPP dan pemotongan lot FIFO diselesaikan dalam waktu singkat (≤ beberapa detik) per transaksi. |
| KNF-03 | Integritas Data | Pemotongan lot persediaan dilakukan dalam satu transaksi basis data dengan penguncian baris (*row locking*) agar tetap konsisten saat diakses banyak pengguna. |
| KNF-04 | Kegunaan (*Usability*) | Antarmuka berbasis web yang responsif dan mudah digunakan oleh barista pada saat melayani pelanggan. |
| KNF-05 | Portabilitas | Aplikasi berbasis web sehingga dapat diakses melalui peramban tanpa instalasi khusus pada sisi pengguna. |
| KNF-06 | Pemeliharaan | Arsitektur *backend* dan *frontend* yang terpisah memudahkan pengembangan dan pemeliharaan secara independen. |

### 3.2.3 Sistem Pendukung

Untuk membangun dan mengoperasikan sistem, dibutuhkan perangkat keras, perangkat
lunak, dan pengguna sebagai berikut.

#### 3.2.3.1 Perangkat Keras (*Hardware*)

**Tabel 3.3 Kebutuhan Perangkat Keras**

| No | Perangkat | Spesifikasi Minimum |
|----|-----------|---------------------|
| 1 | Laptop/PC | Prosesor Intel Core i3 (atau setara), RAM 8 GB, penyimpanan SSD 256 GB |
| 2 | Printer | Printer *thermal* 58 mm untuk mencetak struk penjualan |
| 3 | Jaringan | Koneksi internet/jaringan lokal untuk mengakses aplikasi |

#### 3.2.3.2 Perangkat Lunak (*Software*)

**Tabel 3.4 Kebutuhan Perangkat Lunak**

| No | Perangkat Lunak | Keterangan |
|----|-----------------|-----------|
| 1 | Sistem Operasi | Windows 10/11 atau macOS |
| 2 | Peramban | Google Chrome / Microsoft Edge / Mozilla Firefox versi terbaru |
| 3 | PHP 8.x + Composer | Lingkungan dan manajer paket untuk *backend* |
| 4 | Laravel | *Framework backend* penyedia layanan REST API |
| 5 | Node.js + npm | Lingkungan eksekusi dan manajer paket untuk *frontend* |
| 6 | Next.js (React) | *Framework frontend* antarmuka pengguna |
| 7 | MySQL | Sistem manajemen basis data (DBMS) |
| 8 | Web Server | Apache/Nginx atau server bawaan pengembangan |
| 9 | Visual Studio Code | Editor kode untuk pengembangan |

#### 3.2.3.3 Pengguna (*Users*)

**Tabel 3.4a Kebutuhan Pengguna**

| No | Pengguna | Tugas |
|----|----------|-------|
| 1 | Barista (Kasir) | Menginput seluruh data master dan operasional, menjalankan transaksi pembelian dan penjualan, serta mencetak struk. |
| 2 | Manager | Menerima dan menelaah laporan, termasuk laporan HPP dan laba rugi kotor. |

Pelanggan tidak termasuk pengguna sistem secara langsung; interaksinya dilakukan
melalui barista dengan menyampaikan pesanan dan menerima struk penjualan.

---

## 3.3 Perancangan Sistem

### 3.3.1 Perancangan Arsitektur Sistem

Sistem dirancang dengan **arsitektur klien-server** berbasis **REST API**.
Aplikasi *frontend* (Next.js) berperan sebagai klien yang menampilkan antarmuka
dan mengirim permintaan HTTP; aplikasi *backend* (Laravel) berperan sebagai
server yang memproses logika bisnis—termasuk algoritma FIFO dan perhitungan
HPP—lalu membaca/menulis data ke basis data MySQL dan mengembalikan respons
dalam format JSON. Arsitektur sistem ditunjukkan pada Gambar 3.1.

**Gambar 3.1 Arsitektur Sistem**

```
   ┌─────────────────────────┐        HTTP request (JSON)       ┌──────────────────────────────┐
   │   KLIEN (FRONTEND)      │  ──────────────────────────────► │     SERVER (BACKEND)         │
   │   Next.js (React)       │                                   │     Laravel — REST API       │
   │   - Halaman & komponen  │  ◄────────────────────────────── │   Controller → Service FIFO  │
   │   - Antarmuka kasir      │        HTTP response (JSON)       │   → Model (Eloquent ORM)     │
   └─────────────────────────┘                                   └───────────────┬──────────────┘
            ▲                                                                     │ SQL
            │ Peramban (Barista / Manager)                                        ▼
            │                                                            ┌──────────────────┐
        Pengguna                                                        │   Basis Data     │
                                                                        │      MySQL       │
                                                                        └──────────────────┘
```

Pemisahan ini membuat antarmuka (Next.js) dan logika bisnis (Laravel) dapat
dikembangkan serta diuji secara independen, dengan REST API sebagai kontrak
komunikasi di antara keduanya.

### 3.3.2 Perancangan Struktur Data Tabel

Basis data sistem terdiri atas sembilan tabel yang saling berhubungan, mencakup
tabel master, tabel transaksi beserta detailnya, dan tabel pendukung perhitungan
HPP. Rincian struktur setiap tabel disajikan pada **Tabel 3.5 sampai Tabel 3.13**
(lihat berkas `Perancangan_Struktur_Data_Tabel.md`). Daftar tabel tersebut adalah:

| Tabel | Nama Tabel | Fungsi |
|-------|-----------|--------|
| 3.5 | Pegawai | Menyimpan data akun pegawai beserta peran (barista/manager). |
| 3.6 | Menu | Menyimpan data menu dan harga jual. |
| 3.7 | Bahan Baku | Menyimpan data bahan baku dan satuan. |
| 3.8 | Resep | Komposisi bahan baku per porsi menu (penghubung menu–bahan). |
| 3.9 | Pembelian | Header transaksi pembelian bahan baku. |
| 3.10 | Detail Pembelian | Item pembelian; setiap baris merupakan **lot FIFO** (berisi sisa kuantitas). |
| 3.11 | Penjualan | Header transaksi penjualan beserta total HPP dan laba kotor. |
| 3.12 | Detail Penjualan | Item menu yang terjual pada satu transaksi. |
| 3.13 | Pemakaian Bahan | Catatan pemotongan lot per item—dasar perhitungan HPP FIFO. |

Tabel **Pemakaian Bahan** menjadi inti penelusuran (*audit trail*) HPP karena
mencatat lot pembelian mana saja yang dipotong untuk memenuhi setiap item
penjualan, beserta harga beli dan subtotal HPP-nya.

### 3.3.3 Relasi Antar Tabel

Relasi antar tabel menghubungkan seluruh tabel agar pencatatan transaksi,
pengelolaan persediaan, dan perhitungan HPP berjalan terintegrasi. Relasi
ditunjukkan pada **Gambar 3.2** (lihat berkas `ERD_Relasi_Gambar3-1.md`).
Terdapat sebelas relasi *one-to-many*, dengan dua hubungan *many-to-many* yang
diwujudkan melalui tabel penghubung: hubungan menu–bahan baku melalui tabel
**Resep**, dan hubungan detail penjualan–detail pembelian (lot) melalui tabel
**Pemakaian Bahan**. Penjelasan lengkap tiap relasi terdapat pada subbab Relasi
Antar Tabel di berkas struktur data.

### 3.3.4 Diagram Arus Data (DAD)

#### DAD Level 0 (Diagram Konteks)

DAD Level 0 menggambarkan sistem sebagai satu proses tunggal yang berinteraksi
dengan tiga entitas eksternal: **Barista (Kasir)**, **Pelanggan**, dan
**Manager**. Barista menginput seluruh data operasional dan menerima nota serta
informasi sisa stok; Pelanggan memberikan pesanan dan menerima struk; Manager
menerima laporan-laporan termasuk laporan HPP dan laba rugi kotor. Diagram
ditunjukkan pada **Gambar 3.3** (lihat berkas `DAD_Level0_Konteks.md`).

#### DAD Level 1

DAD Level 1 memecah proses tunggal menjadi enam proses, yaitu: (1) Kelola Data
Menu, (2) Kelola Data Bahan Baku, (3) Kelola Data Resep, (4) Kelola Data
Pembelian, (5) Kelola Data Penjualan, dan (6) Buat Laporan. **Proses 5 (Kelola
Data Penjualan)** merupakan inti sistem karena di dalamnya terjadi penerjemahan
menu menjadi kebutuhan bahan baku (membaca Resep), pemotongan lot persediaan
dengan metode FIFO (memutakhirkan sisa kuantitas pada Detail Pembelian), dan
perhitungan HPP yang dicatat ke tabel Pemakaian Bahan. Diagram ditunjukkan pada
**Gambar 3.4** (lihat berkas `DAD_Level1.md`).

### 3.3.5 Rancangan Masukan (*Input*)

Rancangan masukan merupakan rancangan antarmuka formulir untuk memasukkan data ke
dalam sistem. Berikut rancangan form masukan utama.

**Gambar 3.5 Rancangan Form Login**

```
        +------------------------------------------+
        |          HOMWOK COFFEE — LOGIN           |
        +------------------------------------------+
        |                                          |
        |   Username    : [____________________]   |
        |   Kata Sandi  : [____________________]   |
        |                                          |
        |              [     MASUK     ]           |
        |                                          |
        +------------------------------------------+
```

**Gambar 3.6 Rancangan Form Data Pegawai**

```
        +------------------------------------------+
        |             DATA PEGAWAI                 |
        +------------------------------------------+
        |  Nama Lengkap : [____________________]   |
        |  Username     : [____________________]   |
        |  Kata Sandi   : [____________________]   |
        |  Peran        : [ Barista ▼ ]            |
        |  Status       : (•) Aktif  ( ) Non-aktif |
        |                                          |
        |        [ SIMPAN ]    [ BATAL ]           |
        +------------------------------------------+
```

**Gambar 3.7 Rancangan Form Data Menu**

```
        +------------------------------------------+
        |               DATA MENU                  |
        +------------------------------------------+
        |  Nama Menu   : [_____________________]   |
        |  Kategori    : [ Kopi ▼ ]                |
        |  Harga Jual  : Rp [_______________]      |
        |  Status      : (•) Aktif  ( ) Non-aktif  |
        |                                          |
        |        [ SIMPAN ]    [ BATAL ]           |
        +------------------------------------------+
```

**Gambar 3.8 Rancangan Form Data Bahan Baku**

```
        +------------------------------------------+
        |            DATA BAHAN BAKU               |
        +------------------------------------------+
        |  Nama Bahan    : [__________________]    |
        |  Satuan        : [ gram ▼ ]              |
        |  Stok Minimum  : [__________] (satuan)   |
        |                                          |
        |        [ SIMPAN ]    [ BATAL ]           |
        +------------------------------------------+
```

**Gambar 3.9 Rancangan Form Data Resep**

```
        +-------------------------------------------------+
        |                  DATA RESEP                     |
        +-------------------------------------------------+
        |  Menu : [ Kopi Susu ▼ ]                         |
        |  ---------------------------------------------  |
        |  Bahan Baku        | Takaran | Satuan           |
        |  [ Biji Kopi   ▼ ] | [ 18 ]  | gram             |
        |  [ Susu        ▼ ] | [120 ]  | ml               |
        |  [ Gula        ▼ ] | [ 10 ]  | gram             |
        |  [ + Tambah Baris Bahan ]                       |
        |                                                 |
        |        [ SIMPAN ]    [ BATAL ]                  |
        +-------------------------------------------------+
```

**Gambar 3.10 Rancangan Form Pembelian Bahan Baku**

```
        +-----------------------------------------------------------+
        |                  PEMBELIAN BAHAN BAKU                     |
        +-----------------------------------------------------------+
        |  No. Pembelian : [ OTOMATIS ]   Tanggal : [ 30/06/2026 ]  |
        |  Pemasok       : [_____________________________]          |
        |  -------------------------------------------------------  |
        |  Bahan Baku     | Qty  | Satuan | Harga Beli | Kadaluarsa |
        |  [ Biji Kopi ▼] | [5000]| gram  | Rp[120]    | [ tgl ]    |
        |  [ Susu      ▼] | [10000]| ml   | Rp[ 18]    | [ tgl ]    |
        |  [ + Tambah Baris Item ]                                  |
        |  -------------------------------------------------------  |
        |  Total Beli : Rp 780.000                                  |
        |        [ SIMPAN ]    [ BATAL ]                            |
        +-----------------------------------------------------------+
```

> Catatan: setiap baris item yang disimpan menjadi **satu lot FIFO** pada tabel
> Detail Pembelian, dengan `sisa_qty` awal sama dengan kuantitas yang dibeli.
> Field **Pemasok** diisi langsung sebagai teks (tanpa tabel master pemasok).

**Gambar 3.11 Rancangan Form Penjualan (Kasir)**

```
        +-----------------------------------------------------------+
        |                   TRANSAKSI PENJUALAN                     |
        +-----------------------------------------------------------+
        |  No. Nota : [ OTOMATIS ]        Tanggal : 30/06/2026 14:05 |
        |  ------------------------ DAFTAR MENU -------------------- |
        |  [ Espresso ] [ Kopi Susu ] [ Americano ] [ Latte ] ...   |
        |  -------------------------------------------------------  |
        |  KERANJANG                                                |
        |  Menu          | Qty | Harga    | Subtotal                |
        |  Kopi Susu     | [2] | 18.000   | 36.000   [hapus]        |
        |  Espresso      | [1] | 15.000   | 15.000   [hapus]        |
        |  -------------------------------------------------------  |
        |  Total Jual    : Rp 51.000                                |
        |  Bayar         : Rp [ 100.000 ]                           |
        |  Kembalian     : Rp 49.000                                |
        |        [ BAYAR & CETAK STRUK ]    [ BATAL ]               |
        +-----------------------------------------------------------+
```

> Saat tombol **Bayar & Cetak Struk** ditekan, sistem menjalankan algoritma FIFO:
> menerjemahkan tiap menu menjadi kebutuhan bahan (resep), memotong lot terlama
> lebih dahulu, menghitung HPP dan laba kotor, lalu menyimpan transaksi dan
> mencetak struk.

### 3.3.6 Rancangan Keluaran (*Output*)

Rancangan keluaran merupakan rancangan tampilan informasi yang dihasilkan sistem,
baik berupa struk maupun laporan untuk Manager.

**Gambar 3.12 Rancangan Struk Penjualan**

```
              ============================
                    HOMWOK COFFEE
                 Godean, Yogyakarta
              ============================
              No. Nota : NJ-20260630-001
              Tanggal  : 30/06/2026 14:05
              Kasir    : Barista 1
              ----------------------------
              Kopi Susu   2 x 18.000  36.000
              Espresso    1 x 15.000  15.000
              ----------------------------
              Total       : Rp 51.000
              Bayar       : Rp 100.000
              Kembali     : Rp 49.000
              ============================
                Terima kasih :)
              ============================
```

**Gambar 3.13 Rancangan Laporan Penjualan**

```
   LAPORAN PENJUALAN — Periode: 01/06/2026 s.d. 30/06/2026
   --------------------------------------------------------------------
   No.Nota          | Tanggal      | Total Jual | Total HPP | Laba Kotor
   --------------------------------------------------------------------
   NJ-20260630-001  | 30/06 14:05  |    51.000  |   21.300  |    29.700
   NJ-20260630-002  | 30/06 14:20  |    36.000  |   14.600  |    21.400
   ...
   --------------------------------------------------------------------
   TOTAL            |              |   ...      |   ...     |    ...
```

**Gambar 3.14 Rancangan Laporan Persediaan**

```
   LAPORAN PERSEDIAAN (per 30/06/2026)
   --------------------------------------------------------------------
   Bahan Baku  | Satuan | Sisa Stok | Nilai Persediaan | Ket. Stok Min.
   --------------------------------------------------------------------
   Biji Kopi   | gram   |   4.000   |   Rp 540.000     | Aman
   Susu        | ml     |     800   |   Rp  14.400     | DI BAWAH MINIMUM
   ...
   --------------------------------------------------------------------
   Nilai persediaan dihitung dari sisa kuantitas tiap lot × harga beli lot.
```

**Gambar 3.15 Rancangan Laporan HPP**

```
   LAPORAN HPP — Nota NJ-20260630-001
   --------------------------------------------------------------------
   Menu       | Bahan      | Lot (Tgl Beli) | Qty Pakai | Harga | Subtotal HPP
   --------------------------------------------------------------------
   Kopi Susu  | Biji Kopi  | 01/06 (lot-1)  |   36 g    | 120   |    4.320
   Kopi Susu  | Susu       | 03/06 (lot-2)  |  240 ml   |  18   |    4.320
   Kopi Susu  | Gula       | 02/06 (lot-1)  |   20 g    |  10   |      200
   Espresso   | Biji Kopi  | 01/06 (lot-1)  |   18 g    | 120   |    2.160
   ...
   --------------------------------------------------------------------
   TOTAL HPP NOTA : Rp 21.300
   Pemotongan mengikuti metode FIFO (lot pembelian terlama dipakai lebih dahulu).
```

**Gambar 3.16 Rancangan Laporan Laba Rugi Kotor**

```
   LAPORAN LABA RUGI KOTOR — Periode: 01/06/2026 s.d. 30/06/2026
   --------------------------------------------------------------------
   Total Penjualan (Pendapatan)        : Rp  8.750.000
   Total Harga Pokok Penjualan (HPP)   : Rp  3.610.000  (-)
   --------------------------------------------------------------------
   LABA KOTOR                          : Rp  5.140.000
   --------------------------------------------------------------------
```

---

## Ringkasan Keterkaitan Bab

Perancangan pada bab ini menjadi dasar implementasi pada bab berikutnya. Struktur
data tabel (Tabel 3.5–3.13) dan relasinya (Gambar 3.2) diterjemahkan menjadi
skema basis data MySQL; arsitektur (Gambar 3.1) menjadi acuan pembangunan REST
API Laravel dan antarmuka Next.js; DAD (Gambar 3.3–3.4) menggambarkan alur proses
yang diimplementasikan; serta rancangan masukan (Gambar 3.5–3.11) dan keluaran
(Gambar 3.12–3.16) menjadi acuan pembuatan antarmuka dan laporan.
