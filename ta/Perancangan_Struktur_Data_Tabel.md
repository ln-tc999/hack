# Perancangan Struktur Data Tabel

Basis data atau *database* adalah kumpulan tabel yang saling terhubung dan
dirancang untuk memungkinkan pengguna dalam mengakses, menyimpan, serta mengolah
data secara efisien. Dalam sistem basis data, informasi disusun dalam bentuk
tabel-tabel yang terstruktur. Untuk membangun Aplikasi Penjualan Kasir (*Point of
Sale*) dengan Perhitungan Harga Pokok Penjualan (HPP) Metode FIFO pada Homwok
Coffee, dibutuhkan beberapa tabel yang digunakan untuk mendukung proses
pengelolaan data master, pencatatan pembelian bahan baku beserta antrian lot
FIFO, transaksi penjualan, serta perhitungan HPP secara otomatis. Adapun
tabel-tabel yang digunakan pada sistem ini dapat dilihat pada tabel berikut.

> Catatan: penomoran tabel (Tabel 3.5 dst.) mengikuti gaya contoh. Sesuaikan
> dengan urutan tabel pada BAB III dokumen Anda.

## Tabel Pengguna
**Primary Key** : id_pengguna

**Tabel 3.5 Struktur Data Tabel Pengguna**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_pengguna | Integer | 11 | ID pengguna |
| 2 | nama_lengkap | Varchar | 100 | Nama lengkap pengguna |
| 3 | username | Varchar | 50 | Nama pengguna untuk login |
| 4 | kata_sandi | Varchar | 255 | Kata sandi pengguna (hash) |
| 5 | peran | Enum | - | Peran pengguna (pengelola/kasir) |
| 6 | aktif | Boolean | - | Status pengguna |
| 7 | dibuat_pada | DateTime | - | Tanggal pembuatan data |

## Tabel Menu
**Primary Key** : id_menu

**Tabel 3.6 Struktur Data Tabel Menu**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_menu | Integer | 11 | ID menu |
| 2 | nama_menu | Varchar | 100 | Nama menu minuman |
| 3 | kategori | Varchar | 50 | Kategori menu (kopi/non-kopi) |
| 4 | harga_jual | Decimal | 15,2 | Harga jual per porsi |
| 5 | aktif | Boolean | - | Status menu |
| 6 | dibuat_pada | DateTime | - | Tanggal pembuatan data |

## Tabel Bahan Baku
**Primary Key** : id_bahan

**Tabel 3.7 Struktur Data Tabel Bahan Baku**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_bahan | Integer | 11 | ID bahan baku |
| 2 | nama_bahan | Varchar | 100 | Nama bahan baku |
| 3 | satuan | Varchar | 20 | Satuan bahan (gram/ml/pcs) |
| 4 | stok_minimum | Decimal | 12,2 | Batas minimum stok |
| 5 | dibuat_pada | DateTime | - | Tanggal pembuatan data |

## Tabel Resep
**Primary Key** : id_resep
**Foreign Key** :
id_menu → Tabel Menu
id_bahan → Tabel Bahan Baku

**Tabel 3.8 Struktur Data Tabel Resep**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_resep | Integer | 11 | ID resep (komposisi) |
| 2 | id_menu | Integer | 11 | ID menu |
| 3 | id_bahan | Integer | 11 | ID bahan baku |
| 4 | takaran | Decimal | 12,2 | Jumlah bahan per satu porsi |
| 5 | satuan | Varchar | 20 | Satuan takaran |

## Tabel Pembelian
**Primary Key** : id_pembelian
**Foreign Key** :
id_pengguna → Tabel Pengguna

**Tabel 3.9 Struktur Data Tabel Pembelian**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_pembelian | Integer | 11 | ID pembelian |
| 2 | id_pengguna | Integer | 11 | ID pengguna (pengelola) |
| 3 | nomor_pembelian | Varchar | 30 | Nomor nota pembelian |
| 4 | tanggal_beli | Date | - | Tanggal pembelian |
| 5 | pemasok | Varchar | 100 | Nama pemasok |
| 6 | total_beli | Decimal | 15,2 | Total nilai pembelian |
| 7 | dibuat_pada | DateTime | - | Tanggal pembuatan data |

## Tabel Detail Pembelian
**Primary Key** : id_detail_pembelian
**Foreign Key** :
id_pembelian → Tabel Pembelian
id_bahan → Tabel Bahan Baku

**Tabel 3.10 Struktur Data Tabel Detail Pembelian (Lot FIFO)**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_detail_pembelian | Integer | 11 | ID detail pembelian (identitas lot) |
| 2 | id_pembelian | Integer | 11 | ID pembelian |
| 3 | id_bahan | Integer | 11 | ID bahan baku |
| 4 | qty_awal | Decimal | 12,2 | Kuantitas saat dibeli |
| 5 | sisa_qty | Decimal | 12,2 | Sisa kuantitas (antrian FIFO) |
| 6 | harga_beli | Decimal | 15,2 | Harga beli per satuan |
| 7 | tanggal_kadaluarsa | Date | - | Tanggal kadaluarsa bahan |

## Tabel Penjualan
**Primary Key** : id_penjualan
**Foreign Key** :
id_pengguna → Tabel Pengguna

**Tabel 3.11 Struktur Data Tabel Penjualan**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_penjualan | Integer | 11 | ID penjualan |
| 2 | id_pengguna | Integer | 11 | ID pengguna (kasir) |
| 3 | nomor_nota | Varchar | 30 | Nomor nota penjualan |
| 4 | tanggal_jual | DateTime | - | Tanggal dan waktu transaksi |
| 5 | total_jual | Decimal | 15,2 | Total harga jual |
| 6 | total_hpp | Decimal | 15,2 | Total HPP hasil FIFO |
| 7 | laba_kotor | Decimal | 15,2 | Laba kotor (total_jual − total_hpp) |
| 8 | dibuat_pada | DateTime | - | Tanggal pembuatan data |

## Tabel Detail Penjualan
**Primary Key** : id_detail_penjualan
**Foreign Key** :
id_penjualan → Tabel Penjualan
id_menu → Tabel Menu

**Tabel 3.12 Struktur Data Tabel Detail Penjualan**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_detail_penjualan | Integer | 11 | ID detail penjualan |
| 2 | id_penjualan | Integer | 11 | ID penjualan |
| 3 | id_menu | Integer | 11 | ID menu |
| 4 | qty | Integer | 11 | Jumlah porsi menu |
| 5 | harga_jual | Decimal | 15,2 | Harga jual saat transaksi |
| 6 | subtotal | Decimal | 15,2 | Subtotal (qty × harga_jual) |
| 7 | hpp_menu | Decimal | 15,2 | HPP item ini (hasil FIFO) |

## Tabel Pemakaian Bahan
**Primary Key** : id_pemakaian
**Foreign Key** :
id_detail_penjualan → Tabel Detail Penjualan
id_bahan → Tabel Bahan Baku
id_detail_pembelian → Tabel Detail Pembelian

**Tabel 3.13 Struktur Data Tabel Pemakaian Bahan (Log HPP FIFO)**

| No | Nama Field | Tipe Data | Panjang | Keterangan |
|----|------------|-----------|---------|-----------|
| 1 | id_pemakaian | Integer | 11 | ID pemakaian bahan |
| 2 | id_detail_penjualan | Integer | 11 | ID detail penjualan |
| 3 | id_bahan | Integer | 11 | ID bahan baku |
| 4 | id_detail_pembelian | Integer | 11 | ID lot pembelian asal |
| 5 | qty_dipakai | Decimal | 12,2 | Kuantitas dipotong dari lot |
| 6 | harga_beli | Decimal | 15,2 | Harga beli lot saat dipakai |
| 7 | subtotal_hpp | Decimal | 15,2 | Subtotal HPP (qty_dipakai × harga_beli) |

---

# Relasi Antar Tabel

Relasi antar tabel pada Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode
FIFO pada Homwok Coffee digunakan untuk menghubungkan tabel-tabel yang terdapat
pada basis data sehingga proses pencatatan transaksi penjualan, pengelolaan
persediaan bahan baku, dan perhitungan Harga Pokok Penjualan (HPP) dapat berjalan
secara terintegrasi. Relasi antar tabel pada sistem ini dapat dilihat pada
gambar 3.1.

**Keterangan :**
Relasi Utama / Primary Key (\*)
Relasi Tamu / Foreign Key (\*\*)

Berikut ini merupakan penjelasan relasi antar tabel pada Aplikasi Penjualan Kasir
dengan Perhitungan HPP Metode FIFO pada Homwok Coffee :

1. Relasi antara tabel pengguna dengan tabel pembelian yaitu *one to many*,
   karena satu pengguna dapat menginput lebih dari satu transaksi pembelian.
2. Relasi antara tabel pengguna dengan tabel penjualan yaitu *one to many*,
   karena satu pengguna dapat melakukan lebih dari satu transaksi penjualan.
3. Relasi antara tabel menu dengan tabel resep yaitu *one to many*, karena satu
   menu dapat tersusun atas lebih dari satu bahan baku pada komposisi resep.
4. Relasi antara tabel bahan_baku dengan tabel resep yaitu *one to many*, karena
   satu bahan baku dapat digunakan pada resep lebih dari satu menu.
5. Relasi antara tabel pembelian dengan tabel detail_pembelian yaitu *one to
   many*, karena satu transaksi pembelian dapat memiliki beberapa item bahan baku
   yang masing-masing menjadi satu lot pada antrian FIFO.
6. Relasi antara tabel bahan_baku dengan tabel detail_pembelian yaitu *one to
   many*, karena satu bahan baku dapat memiliki beberapa lot pembelian dari waktu
   pengadaan yang berbeda.
7. Relasi antara tabel penjualan dengan tabel detail_penjualan yaitu *one to
   many*, karena satu transaksi penjualan dapat memuat beberapa item menu.
8. Relasi antara tabel menu dengan tabel detail_penjualan yaitu *one to many*,
   karena satu menu dapat muncul pada beberapa detail transaksi penjualan.
9. Relasi antara tabel detail_penjualan dengan tabel pemakaian_bahan yaitu *one
   to many*, karena penjualan satu item menu dapat memotong beberapa bahan baku
   sesuai komposisi resepnya.
10. Relasi antara tabel detail_pembelian dengan tabel pemakaian_bahan yaitu *one
    to many*, karena satu lot pembelian dapat dipotong oleh beberapa transaksi
    pemakaian bahan secara berurutan berdasarkan metode FIFO.
11. Relasi antara tabel bahan_baku dengan tabel pemakaian_bahan yaitu *one to
    many*, karena satu bahan baku dapat digunakan dalam banyak catatan pemakaian
    bahan pada transaksi penjualan.

Dengan adanya relasi antar tabel tersebut, seluruh data pada sistem dapat saling
terhubung sehingga proses pencatatan pembelian bahan baku, transaksi penjualan,
pemotongan persediaan dengan metode FIFO, serta perhitungan Harga Pokok Penjualan
(HPP) dapat dilakukan secara otomatis dan konsisten.
