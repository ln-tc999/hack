# DAD Konteks (Level 0)
Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode FIFO — Homwok Coffee

Mengikuti gaya diagram konteks PT Scala: satu proses pusat (0) dikelilingi entitas
eksternal. Aliran `data_...` = masukan ke sistem; `daftar_...` / `Laporan_...` /
`struk_...` = keluaran dari sistem.

Entitas eksternal pada sistem ini:
- **Pelanggan** — memberikan pesanan dan menerima struk penjualan.
- **Barista (Kasir)** — menginput seluruh data operasional (penjualan, pembelian
  bahan baku, dan data master menu/bahan/resep) serta menerima nota dan informasi
  sisa stok.
- **Manager** — menerima laporan-laporan, termasuk laporan HPP dan laba rugi
  kotor.

---

## Tata Letak (mirip contoh)

```
                       data_penjualan,
                       data_pembelian,
                       data_menu,
                       data_bahan_baku,
                       data_resep
   ┌───────────────┐ ──────────────────────────►   ┌──────────────────────┐
   │    BARISTA     │                                │                      │
   │    (KASIR)    │ ◄──────────────────────────    │                      │
   └───────────────┘   daftar_menu,                 │                      │
                       nota_penjualan,              │        0             │
                       info_sisa_stok,             │  SISTEM POS HPP      │
                       daftar_persediaan            │  FIFO HOMWOK COFFEE  │
                                                     │                      │
   ┌───────────────┐  data_pesanan                  │                      │
   │   PELANGGAN   │ ──────────────────────────►    │                      │
   │               │ ◄──────────────────────────    │                      │
   └───────────────┘   struk_penjualan              │                      │
                                                     │                      │
                          Laporan_Penjualan,        │                      │
                          Laporan_Pembelian,        │                      │
                          Laporan_Persediaan,       │                      │
                          Laporan_Perputaran_       │                      │
                          Persediaan,               │                      │
                          Laporan_HPP,              │                      │
                          Laporan_Laba_Rugi_Kotor   │                      │
   ┌───────────────┐ ◄──────────────────────────    └──────────────────────┘
   │    MANAGER    │
   └───────────────┘
```

> Catatan tata letak: posisikan **Barista (Kasir)** di kiri-atas, **Pelanggan**
> di kiri-bawah, dan **Manager** di kanan-bawah. Proses 0 berada di tengah.

---

## Rincian Aliran Data

### Entitas BARISTA / KASIR (operasional & input data)
**Masukan (Barista → Sistem):**
- data_penjualan
- data_pembelian
- data_menu
- data_bahan_baku
- data_resep

**Keluaran (Sistem → Barista):**
- daftar_menu
- nota_penjualan
- info_sisa_stok
- daftar_persediaan

### Entitas PELANGGAN
**Masukan (Pelanggan → Sistem):**
- data_pesanan

**Keluaran (Sistem → Pelanggan):**
- struk_penjualan

### Entitas MANAGER (penerima laporan)
**Keluaran (Sistem → Manager):**
- Laporan_Penjualan
- Laporan_Pembelian
- Laporan_Persediaan
- Laporan_Perputaran_Persediaan
- Laporan_HPP
- Laporan_Laba_Rugi_Kotor

---

## Keterangan
Caption gambar: **DAD KONTEKS (LEVEL 0) SISTEM POS HPP FIFO HOMWOK COFFEE**

Diagram ini menggambarkan keseluruhan sistem sebagai satu proses tunggal (0) yang
berinteraksi dengan tiga entitas eksternal. Pelanggan memberikan pesanan dan
menerima struk penjualan. Barista (Kasir) bertugas menginput seluruh data
operasional—mulai dari data master (menu, bahan baku, resep), data pembelian
bahan baku, hingga data penjualan—serta menerima nota dan informasi sisa stok.
Manager menerima berbagai laporan, termasuk laporan Harga Pokok Penjualan (HPP)
dan laporan laba rugi kotor yang dihitung otomatis menggunakan metode FIFO.
