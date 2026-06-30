# DAD Level 1
Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode FIFO — Homwok Coffee

Disusun mengikuti gaya diagram DAD Level 1 (proses per-pengelolaan data).
Notasi: `data_...` = masukan dari entitas; `record_...` = aliran ke/dari simpanan
data; `Laporan_...` / `struk_...` = keluaran.

Caption gambar: **DAD LEVEL 1 APLIKASI PENJUALAN KASIR DENGAN PERHITUNGAN HPP
METODE FIFO — HOMWOK COFFEE**

---

## Entitas Eksternal
- **BARISTA** — menginput data master, pembelian, dan penjualan; menerima
  nota/struk dan informasi sisa stok.
- **MANAGER** — menerima laporan.

> Pelanggan tidak digambarkan sebagai entitas: pesanannya dicatat barista sebagai
> `data_penjualan`, dan struk diserahkan barista di luar batas sistem.

## Proses
| No | Proses |
|----|--------|
| 1 | KELOLA DATA MENU |
| 2 | KELOLA DATA BAHAN BAKU |
| 3 | KELOLA DATA RESEP |
| 4 | KELOLA DATA PEMBELIAN |
| 5 | KELOLA DATA PENJUALAN |
| 6 | BUAT LAPORAN |

## Simpanan Data (Data Store)
MENU, BAHAN_BAKU, RESEP, PEMBELIAN, DETAIL_PEMBELIAN, PENJUALAN,
DETAIL_PENJUALAN, PEMAKAIAN_BAHAN

---

## Diagram (ASCII)

```
 BARISTA ── data_menu ───────►( 1 KELOLA DATA MENU )── record_menu ──►[ MENU ]
                                                                          │
 BARISTA ── data_bahan_baku ─►( 2 KELOLA DATA       )── record_bahan ──►[ BAHAN_BAKU ]
                              (   BAHAN BAKU         )                     │
                                                                          │
 BARISTA ── data_resep ──────►( 3 KELOLA DATA RESEP )                     │
                                  ▲ record_menu  ◄────────────────────────┤
                                  ▲ record_bahan ◄────────────────────────┤
                                  └─ record_resep ──►[ RESEP ]            │
                                                                          │
 BARISTA ── data_pembelian ──►( 4 KELOLA DATA       )── record_pembelian ►[ PEMBELIAN ]
                              (   PEMBELIAN          )── record_detail ───►[ DETAIL_PEMBELIAN ]
                                  ▲ record_bahan ◄───────────────────────┘   (LOT FIFO)
                                                                          
 BARISTA ─── data_penjualan ►( 5 KELOLA DATA        )◄─ record_menu  (baca harga)
                             (   PENJUALAN          )◄─ record_resep (baca takaran)
                             (                       )⇄ record_detail_pembelian
 nota_penjualan  ◄───────────(  potong lot FIFO,    )   (potong FIFO, update sisa_qty)
 (struk)         ◄───────────(  hitung HPP & laba   )── record_jual ──────►[ PENJUALAN ]
 info_sisa_stok  ◄───────────(                       )── record_detail_jual ►[ DETAIL_PENJUALAN ]
                             └───────────────────────── record_pemakaian ──►[ PEMAKAIAN_BAHAN ]

                              ( 6 BUAT LAPORAN )◄── record_pembelian, record_detail_pembelian,
                                    │                record_jual, record_detail_jual,
                                    │                record_pemakaian, record_bahan, record_menu
                                    ▼
                                MANAGER
                       daftar_menu, daftar_persediaan,
                       Laporan_Penjualan, Laporan_Pembelian,
                       Laporan_Persediaan, Laporan_Perputaran_Persediaan,
                       Laporan_HPP, Laporan_Laba_Rugi_Kotor
```

---

## Rincian Aliran Data per Proses

### 1 — KELOLA DATA MENU (Barista)
- BARISTA → P1: `data_menu`
- P1 → MENU: `record_menu`

### 2 — KELOLA DATA BAHAN BAKU (Barista)
- BARISTA → P2: `data_bahan_baku`
- P2 → BAHAN_BAKU: `record_bahan_baku`

### 3 — KELOLA DATA RESEP (Barista)
- BARISTA → P3: `data_resep`
- MENU → P3: `record_menu` (baca daftar menu)
- BAHAN_BAKU → P3: `record_bahan_baku` (baca daftar bahan)
- P3 → RESEP: `record_resep`

### 4 — KELOLA DATA PEMBELIAN (Barista)
- BARISTA → P4: `data_pembelian`
- BAHAN_BAKU → P4: `record_bahan_baku` (baca bahan)
- P4 → PEMBELIAN: `record_pembelian` (header nota beli)
- P4 → DETAIL_PEMBELIAN: `record_detail_pembelian` (**setiap item = lot FIFO**, isi `sisa_qty`)

### 5 — KELOLA DATA PENJUALAN (Barista) — INTI FIFO/HPP
- BARISTA → P5: `data_penjualan` (barista mencatat pesanan pelanggan)
- MENU → P5: `record_menu` (baca harga jual)
- RESEP → P5: `record_resep` (baca takaran bahan per menu)
- DETAIL_PEMBELIAN ⇄ P5: `record_detail_pembelian` (**potong lot FIFO terlama dulu, update `sisa_qty`**)
- P5 → PENJUALAN: `record_jual` (total jual, total HPP, laba kotor)
- P5 → DETAIL_PENJUALAN: `record_detail_jual`
- P5 → PEMAKAIAN_BAHAN: `record_pemakaian` (log potongan lot = dasar HPP)
- P5 → BARISTA: `nota_penjualan` (struk), `info_sisa_stok`

### 6 — BUAT LAPORAN (Manager)
- Membaca: PEMBELIAN, DETAIL_PEMBELIAN, PENJUALAN, DETAIL_PENJUALAN,
  PEMAKAIAN_BAHAN, BAHAN_BAKU, MENU
- P6 → MANAGER: `daftar_menu`, `daftar_persediaan`, `Laporan_Penjualan`,
  `Laporan_Pembelian`, `Laporan_Persediaan`, `Laporan_Perputaran_Persediaan`,
  `Laporan_HPP`, `Laporan_Laba_Rugi_Kotor`

---

## Keterangan
Proses **5 (Kelola Data Penjualan)** merupakan inti sistem. Saat satu menu
terjual, proses ini membaca komposisi resep (RESEP) untuk menerjemahkan menu
menjadi kebutuhan bahan baku, lalu memotong kuantitas dari DETAIL_PEMBELIAN
berdasarkan tanggal pembelian terlama lebih dahulu (FIFO). Setiap potongan lot
dicatat ke PEMAKAIAN_BAHAN sebagai dasar perhitungan Harga Pokok Penjualan (HPP),
kemudian total HPP dan laba kotor disimpan pada PENJUALAN. Seluruh laporan pada
proses 6 dihasilkan otomatis dan ditujukan kepada Manager.
