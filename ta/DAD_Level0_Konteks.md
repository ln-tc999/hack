# DAD Konteks (Level 0)
Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode FIFO — Homwok Coffee

Mengikuti gaya diagram konteks PT Scala: satu proses pusat (0) dikelilingi tiga
entitas eksternal. Aliran `data_...` = masukan ke sistem; `daftar_...` /
`Laporan_...` = keluaran dari sistem.

---

## Tata Letak (mirip contoh)

```
                       data_pengguna,
                       data_menu,
                       data_bahan_baku,
                       data_resep,
                       data_pembelian
   ┌───────────────┐ ──────────────────────────►   ┌──────────────────────┐
   │     KASIR     │                                │                      │
   │               │ ◄──────────────────────────    │                      │
   └───────────────┘   daftar_menu,                 │                      │
        ▲   │          nota_penjualan,              │        0             │
        │   │          info_sisa_stok               │  SISTEM POS HPP      │
        │   │ data_penjualan                        │  FIFO HOMWOK COFFEE  │
        │   └─────────────────────────────────────► │                      │
        │                                           │                      │
        └───────────────────────────────────────────│                      │
            daftar_penjualan_harian                 │                      │
                                                     │                      │
   ┌───────────────┐  data_pengguna, data_menu,     │                      │
   │     ADMIN /   │  data_bahan_baku, data_resep,  │                      │
   │   PENGELOLA   │ ─data_pembelian──────────────► │                      │
   │               │ ◄──────────────────────────    │                      │
   └───────────────┘   daftar_pengguna,             │                      │
                       daftar_menu, daftar_bahan,   │                      │
                       daftar_resep,                │                      │
                       daftar_pembelian,            │                      │
                       daftar_persediaan            │                      │
                                                     │                      │
                          Laporan_Penjualan,        │                      │
                          Laporan_Pembelian,        │                      │
                          Laporan_Persediaan,       │                      │
                          Laporan_Perputaran_       │                      │
                          Persediaan,               │                      │
                          Laporan_HPP,              │                      │
                          Laporan_Laba_Rugi_Kotor   │                      │
   ┌───────────────┐ ◄──────────────────────────    └──────────────────────┘
   │    PEMILIK    │
   │   (OWNER)     │
   └───────────────┘
```

> Catatan tata letak: posisikan **Kasir** di kiri-atas, **Admin/Pengelola** di
> kanan, dan **Pemilik** di kanan-bawah (persis seperti contoh PT Scala:
> Staff_Pegawai – Superadmin – Owner).

---

## Rincian Aliran Data

### Entitas KASIR (operasional — seperti Staff_Pegawai)
**Masukan (Kasir → Sistem):**
- data_penjualan

**Keluaran (Sistem → Kasir):**
- daftar_menu
- nota_penjualan
- info_sisa_stok
- daftar_penjualan_harian

### Entitas ADMIN / PENGELOLA (master data — seperti Superadmin)
**Masukan (Admin → Sistem):**
- data_pengguna
- data_menu
- data_bahan_baku
- data_resep
- data_pembelian

**Keluaran (Sistem → Admin):**
- daftar_pengguna
- daftar_menu
- daftar_bahan_baku
- daftar_resep
- daftar_pembelian
- daftar_persediaan

### Entitas PEMILIK / OWNER (laporan — seperti Owner)
**Keluaran (Sistem → Pemilik):**
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
berinteraksi dengan tiga entitas eksternal. Kasir bertugas memasukkan data
penjualan dan menerima nota serta informasi sisa stok; Admin/Pengelola mengelola
seluruh data master dan pembelian bahan baku; sedangkan Pemilik menerima berbagai
laporan, termasuk laporan Harga Pokok Penjualan (HPP) dan laba rugi kotor yang
dihitung otomatis menggunakan metode FIFO.
