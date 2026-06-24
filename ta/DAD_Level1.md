# DAD Level 1
Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode FIFO — Homwok Coffee

Proses tunggal (0) pada diagram konteks didekomposisi menjadi **5 proses utama**
dengan **9 simpanan data** (*data store*). Notasi: `data_...` = aliran masukan,
`daftar_...` / `Laporan_...` = aliran keluaran.

---

## Daftar Proses

| No | Proses | Aktor |
|----|--------|-------|
| 1.0 | Autentikasi Pengguna | Barista, Manager |
| 2.0 | Pengelolaan Data Master | Barista |
| 3.0 | Pencatatan Pembelian (Lot FIFO) | Barista |
| 4.0 | Transaksi Penjualan & Perhitungan HPP FIFO | Barista, Pelanggan |
| 5.0 | Pelaporan | Manager |

## Daftar Simpanan Data

| Kode | Simpanan Data |
|------|---------------|
| D1 | pengguna |
| D2 | menu |
| D3 | bahan_baku |
| D4 | resep |
| D5 | pembelian |
| D6 | detail_pembelian (lot FIFO) |
| D7 | penjualan |
| D8 | detail_penjualan |
| D9 | pemakaian_bahan |

---

## Diagram DAD Level 1

```
 BARISTA                                                              MANAGER
   │                                                                     │
   │ data_login                                            data_login    │
   ▼                                                                     ▼
 ┌──────────────────────┐   data_pengguna     ┌──────────────────────────────┐
 │  1.0 Autentikasi     │◄───────────────────►│ D1  pengguna                 │
 │      Pengguna        │                     └──────────────────────────────┘
 └──────────────────────┘
   │ hak_akses
   ▼
 ┌──────────────────────┐  data_menu       ┌──────────────┐
 │  2.0 Pengelolaan     │◄────────────────►│ D2  menu     │
 │      Data Master     │  data_bahan_baku ┌──────────────┐
 │      (BARISTA)       │◄────────────────►│ D3 bahan_baku│
 │                      │  data_resep      ┌──────────────┐
 │                      │◄────────────────►│ D4  resep    │
 └──────────────────────┘                  └──────────────┘

 ┌──────────────────────┐  data_pembelian  ┌────────────────────┐
 │  3.0 Pencatatan      │─────────────────►│ D5  pembelian      │
 │      Pembelian       │  data_lot        ┌──────────────────────────┐
 │      (BARISTA)       │─────────────────►│ D6 detail_pembelian (LOT)│
 └──────────────────────┘  baca D3 ▲       └──────────────────────────┘

 PELANGGAN ── data_pesanan ──►┌─────────────────────────────────────┐
                              │ 4.0 Transaksi Penjualan &           │ baca_harga ──► D2 menu
 BARISTA ── data_penjualan ──►│     Perhitungan HPP FIFO            │ baca_takaran ─► D4 resep
                              │                                     │ potong_lot ◄─► D6 detail_pembelian
 struk_penjualan ◄────────────│  - menu → bahan (resep)             │   (update sisa_qty, FIFO)
 nota_penjualan  ◄────────────│  - potong lot FIFO (terlama dulu)   │
 info_sisa_stok  ◄────────────│  - hitung HPP & laba kotor          │
                              └─────────────────────────────────────┘
                                  │ simpan         │ simpan        │ simpan
                                  ▼                ▼               ▼
                          ┌──────────────┐ ┌───────────────────┐ ┌─────────────────────┐
                          │ D7 penjualan │ │ D8 detail_penjualan│ │ D9 pemakaian_bahan  │
                          └──────────────┘ └───────────────────┘ └─────────────────────┘

 ┌──────────────────────┐ ◄── baca D5, D6, D7, D8, D9
 │  5.0 Pelaporan       │ ── Laporan_Pembelian ───────────────►  MANAGER
 │                      │ ── Laporan_Penjualan ───────────────►
 │                      │ ── Laporan_Persediaan ──────────────►
 │                      │ ── Laporan_Perputaran_Persediaan ───►
 │                      │ ── Laporan_HPP ─────────────────────►
 │                      │ ── Laporan_Laba_Rugi_Kotor ─────────►
 └──────────────────────┘
```

---

## Rincian Aliran Data per Proses

### 1.0 Autentikasi Pengguna
- Barista / Manager → Proses: `data_login`
- Proses ⇄ D1 pengguna: verifikasi `data_pengguna`
- Proses → Barista / Manager: `hak_akses`

### 2.0 Pengelolaan Data Master (Barista)
- Barista → Proses: `data_menu`, `data_bahan_baku`, `data_resep`
- Proses ⇄ D2 menu, D3 bahan_baku, D4 resep: simpan & baca
- Proses → Barista: `daftar_menu`, `daftar_bahan_baku`, `daftar_resep`

### 3.0 Pencatatan Pembelian — Lot FIFO (Barista)
- Barista → Proses: `data_pembelian`
- Proses → D5 pembelian: simpan header pembelian
- Proses → D6 detail_pembelian: simpan item sebagai **lot FIFO** (`sisa_qty`)
- Proses ← D3 bahan_baku: baca data bahan
- Proses → Barista: `daftar_pembelian`, `daftar_persediaan`

### 4.0 Transaksi Penjualan & Perhitungan HPP FIFO (Barista, Pelanggan)
- Pelanggan → Proses: `data_pesanan`
- Barista → Proses: `data_penjualan`
- Proses ← D2 menu: baca harga jual
- Proses ← D4 resep: baca takaran bahan per menu
- Proses ⇄ D6 detail_pembelian: **potong lot FIFO** (update `sisa_qty`)
- Proses → D7 penjualan, D8 detail_penjualan, D9 pemakaian_bahan: simpan
- Proses → Pelanggan: `struk_penjualan`
- Proses → Barista: `nota_penjualan`, `info_sisa_stok`

### 5.0 Pelaporan (Manager)
- Proses ← D5, D6, D7, D8, D9: baca data
- Proses → Manager: `Laporan_Pembelian`, `Laporan_Penjualan`,
  `Laporan_Persediaan`, `Laporan_Perputaran_Persediaan`, `Laporan_HPP`,
  `Laporan_Laba_Rugi_Kotor`

---

## Keterangan

Proses **4.0** merupakan inti sistem. Ketika satu menu terjual, proses ini
membaca komposisi resep (D4) untuk menerjemahkan menu menjadi kebutuhan bahan
baku, lalu memotong kuantitas dari `D6 detail_pembelian` berdasarkan tanggal
pembelian terlama lebih dahulu (FIFO). Setiap potongan lot dicatat ke
`D9 pemakaian_bahan` sebagai dasar perhitungan HPP, kemudian total HPP dan laba
kotor disimpan pada `D7 penjualan`. Seluruh laporan pada proses 5.0 dihasilkan
secara otomatis dan ditujukan kepada Manager.
