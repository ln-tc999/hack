# Gambar 3.1 — Relasi Antar Tabel (ERD)
Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode FIFO — Homwok Coffee

Notasi kaki gagak (*crow's foot*):
`||` = satu (one), `o{` = banyak (many). Jadi `||--o{` berarti **one to many**.

---

## A. Kode Mermaid (untuk dirender jadi gambar)

Salin kode di bawah ke https://mermaid.live atau menu *Insert → Mermaid* pada
draw.io, lalu ekspor sebagai gambar untuk dimasukkan ke dokumen sebagai Gambar 3.1.

```mermaid
erDiagram
    PEGAWAI   ||--o{ PEMBELIAN       : menginput
    PEGAWAI   ||--o{ PENJUALAN       : melakukan
    MENU       ||--o{ RESEP           : memiliki
    BAHAN_BAKU ||--o{ RESEP           : digunakan_pada
    PEMBELIAN  ||--o{ DETAIL_PEMBELIAN: memiliki
    BAHAN_BAKU ||--o{ DETAIL_PEMBELIAN: berstok
    PENJUALAN  ||--o{ DETAIL_PENJUALAN: memiliki
    MENU       ||--o{ DETAIL_PENJUALAN: dijual_pada
    DETAIL_PENJUALAN ||--o{ PEMAKAIAN_BAHAN : memotong
    DETAIL_PEMBELIAN ||--o{ PEMAKAIAN_BAHAN : dipotong
    BAHAN_BAKU       ||--o{ PEMAKAIAN_BAHAN : dipakai

    PEGAWAI {
        int id_pegawai PK
        varchar nama_lengkap
        varchar username
        varchar kata_sandi
        enum peran
        boolean aktif
        datetime dibuat_pada
    }
    MENU {
        int id_menu PK
        varchar nama_menu
        varchar kategori
        decimal harga_jual
        boolean aktif
        datetime dibuat_pada
    }
    BAHAN_BAKU {
        int id_bahan PK
        varchar nama_bahan
        varchar satuan
        decimal stok_minimum
        datetime dibuat_pada
    }
    RESEP {
        int id_resep PK
        int id_menu FK
        int id_bahan FK
        decimal takaran
        varchar satuan
    }
    PEMBELIAN {
        int id_pembelian PK
        int id_pegawai FK
        varchar nomor_pembelian
        date tanggal_beli
        varchar pemasok
        decimal total_beli
        datetime dibuat_pada
    }
    DETAIL_PEMBELIAN {
        int id_detail_pembelian PK
        int id_pembelian FK
        int id_bahan FK
        decimal qty_awal
        decimal sisa_qty
        decimal harga_beli
        date tanggal_kadaluarsa
    }
    PENJUALAN {
        int id_penjualan PK
        int id_pegawai FK
        varchar nomor_nota
        datetime tanggal_jual
        decimal total_jual
        decimal total_hpp
        decimal laba_kotor
        datetime dibuat_pada
    }
    DETAIL_PENJUALAN {
        int id_detail_penjualan PK
        int id_penjualan FK
        int id_menu FK
        int qty
        decimal harga_jual
        decimal subtotal
        decimal hpp_menu
    }
    PEMAKAIAN_BAHAN {
        int id_pemakaian PK
        int id_detail_penjualan FK
        int id_bahan FK
        int id_detail_pembelian FK
        decimal qty_dipakai
        decimal harga_beli
        decimal subtotal_hpp
    }

  
```

---

## B. Diagram ASCII (panduan tata letak)

```
        ┌─────────────────┐
        │    PEGAWAI     │
        │  id_pegawai *  │
        └───────┬─────────┘
         1      │      1
      ┌─────────┴──────────┐
      │ N                  │ N
┌─────▼────────┐    ┌──────▼────────┐
│  PEMBELIAN   │    │   PENJUALAN   │
│ id_pembelian*│    │ id_penjualan* │
│ id_pegawai**│    │ id_pegawai** │
└─────┬────────┘    └──────┬────────┘
      │ 1                  │ 1
      │ N                  │ N
┌─────▼─────────────┐  ┌───▼──────────────────┐
│ DETAIL_PEMBELIAN  │  │  DETAIL_PENJUALAN     │
│ id_detail_pemb. * │  │ id_detail_penj. *     │
│ id_pembelian   ** │  │ id_penjualan       ** │
│ id_bahan       ** │  │ id_menu            ** │
│ sisa_qty (LOT)    │  └───────────┬──────────┘
└──────┬────────────┘              │ 1
       │ 1                         │ N
       │ N            ┌────────────▼───────────────┐
       └─────────────►│      PEMAKAIAN_BAHAN       │◄──── N
                      │ id_pemakaian            *  │      │ 1
                      │ id_detail_penjualan     ** │  ┌───┴──────────┐
                      │ id_detail_pembelian     ** │  │  BAHAN_BAKU  │
                      │ id_bahan                ** │◄─┤  id_bahan *  │
                      │ subtotal_hpp               │ N└───┬──────────┘
                      └────────────────────────────┘   1 │ N
                                                          │
        ┌──────────────┐  N        1   ┌─────────────────▼──┐
        │     MENU     ├───────────────►│       RESEP        │
        │  id_menu *   │  (menu→resep)  │  id_resep       *  │
        └──────┬───────┘                │  id_menu        ** │
               │ 1                       │  id_bahan       ** │ ◄── (bahan→resep)
               │ N (menu→detail_penj.)   │  takaran           │
               └───► DETAIL_PENJUALAN    └────────────────────┘
```

Keterangan: `*` = Primary Key, `**` = Foreign Key.

---

## C. Ringkasan 11 Relasi (one to many)

| No | Relasi | Kardinalitas |
|----|--------|--------------|
| 1 | pegawai → pembelian | 1 : N |
| 2 | pegawai → penjualan | 1 : N |
| 3 | menu → resep | 1 : N |
| 4 | bahan_baku → resep | 1 : N |
| 5 | pembelian → detail_pembelian | 1 : N |
| 6 | bahan_baku → detail_pembelian | 1 : N |
| 7 | penjualan → detail_penjualan | 1 : N |
| 8 | menu → detail_penjualan | 1 : N |
| 9 | detail_penjualan → pemakaian_bahan | 1 : N |
| 10 | detail_pembelian → pemakaian_bahan | 1 : N |
| 11 | bahan_baku → pemakaian_bahan | 1 : N |

Catatan: hubungan **many-to-many** antara `menu` dan `bahan_baku` diwujudkan
melalui tabel penghubung `resep` (relasi 3 dan 4). Hubungan many-to-many antara
`detail_penjualan` dan `detail_pembelian` (satu item menu dapat memotong banyak
lot, satu lot dapat melayani banyak item) diwujudkan melalui tabel `pemakaian_bahan`
(relasi 9 dan 10).
