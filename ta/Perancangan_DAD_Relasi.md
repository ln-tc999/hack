# PERANCANGAN SISTEM — DAD & TABEL RELASI
Aplikasi Penjualan Kasir (POS) dengan Perhitungan HPP Metode FIFO — Homwok Coffee

---

## A. Entitas Eksternal (Terminator)

| Kode | Entitas    | Peran |
|------|-----------|-------|
| —    | Pengelola | Mengelola data master (menu, bahan baku, resep), input nota pembelian, melihat laporan. |
| —    | Kasir     | Melakukan transaksi penjualan dan mencetak nota. |
| —    | Pelanggan | Menerima nota/struk penjualan. |

---

## B. DAD Level 0 (Diagram Konteks)

Seluruh sistem digambarkan sebagai **satu proses tunggal**. Arah panah = arah
aliran data.

```
                       data menu, bahan baku, resep,
                       data pembelian bahan baku
        ┌────────────┐ ───────────────────────────────►  ┌──────────────────────────┐
        │            │                                    │                          │
        │  PENGELOLA │                                    │     0                    │
        │            │ ◄───────────────────────────────   │  APLIKASI POS HPP FIFO   │
        └────────────┘  laporan penjualan, laporan        │     HOMWOK COFFEE        │
                        pembelian, persediaan, laba rugi   │                          │
                                                           │                          │
        ┌────────────┐  data pesanan / penjualan          │                          │
        │            │ ───────────────────────────────►   │                          │
        │   KASIR    │                                    │                          │
        │            │ ◄───────────────────────────────   │                          │
        └────────────┘  nota penjualan, info sisa stok    │                          │
                                                           │                          │
                                       nota / struk        │                          │
                        ◄────────────────────────────────  │                          │
        ┌────────────┐                                     └──────────────────────────┘
        │ PELANGGAN  │
        └────────────┘
```

**Ringkasan aliran data Konteks:**

| Dari → Ke | Data |
|-----------|------|
| Pengelola → Sistem | data menu, data bahan baku, data resep, data pembelian |
| Sistem → Pengelola | laporan penjualan, laporan pembelian, laporan persediaan, laporan laba rugi (HPP) |
| Kasir → Sistem | data pesanan / penjualan |
| Sistem → Kasir | nota penjualan, info sisa stok |
| Sistem → Pelanggan | nota / struk penjualan |

---

## C. DAD Level 1

Proses tunggal di atas didekomposisi menjadi **5 proses utama** dengan 9 simpanan
data (*data store*).

**Daftar Proses:**

| No | Proses | Keterangan |
|----|--------|-----------|
| 1.0 | Autentikasi Pengguna | Login & verifikasi hak akses (Pengelola/Kasir). |
| 2.0 | Pengelolaan Data Master | CRUD menu, bahan baku, dan komposisi resep. |
| 3.0 | Pencatatan Pembelian | Catat nota pembelian → tiap baris detail menjadi lot FIFO (sisa_qty). |
| 4.0 | Transaksi Penjualan & Hitung HPP FIFO | Catat penjualan, dekomposisi resep, potong lot FIFO, hitung HPP. |
| 5.0 | Pelaporan | Susun laporan pembelian, penjualan, persediaan, dan laba rugi. |

**Daftar Simpanan Data:**

| Kode | Simpanan Data | Keterangan |
|------|---------------|-----------|
| D1 | users | data pengguna |
| D2 | menu | data master menu |
| D3 | bahan_baku | data master bahan baku |
| D4 | resep | komposisi resep (BOM) |
| D5 | pembelian | header nota pembelian |
| D6 | detail_pembelian | item pembelian = **antrian lot FIFO** |
| D7 | penjualan | header nota penjualan |
| D8 | detail_penjualan | item penjualan (per menu) |
| D9 | pemakaian_bahan | log pemotongan FIFO / dasar HPP |

**Diagram DAD Level 1:**

```
 PENGELOLA                                                                 KASIR
    │                                                                        │
    │ username/password                                  username/password   │
    ▼                                                                        ▼
 ┌──────────────────────┐    data pengguna     ┌─────────────────────────────────┐
 │ 1.0 Autentikasi      │◄────────────────────►│ D1  users                       │
 │     Pengguna         │                      └─────────────────────────────────┘
 └──────────────────────┘
    │ hak akses
    ▼
 ┌──────────────────────┐  data menu     ┌──────────────┐
 │ 2.0 Pengelolaan      │◄──────────────►│ D2  menu     │
 │     Data Master      │  data bahan    ┌──────────────┐
 │  (PENGELOLA)         │◄──────────────►│ D3 bahan_baku│
 │                      │  data resep    ┌──────────────┐
 │                      │◄──────────────►│ D4  resep    │
 └──────────────────────┘                └──────────────┘

           data pembelian (PENGELOLA)
                  │
                  ▼
 ┌──────────────────────┐  simpan header   ┌────────────────────┐
 │ 3.0 Pencatatan       │─────────────────►│ D5  pembelian      │
 │     Pembelian        │  simpan item/lot ┌──────────────────────────┐
 │  (PENGELOLA)         │─────────────────►│ D6 detail_pembelian (LOT)│
 └──────────────────────┘  baca D3 ▲       └──────────────────────────┘

 KASIR ── data pesanan ──►┌─────────────────────────────────────┐
                          │ 4.0 Transaksi Penjualan &           │  baca D2 (harga jual)
 nota & sisa stok ◄────── │     Perhitungan HPP FIFO            │◄──► D2 menu
                          │                                     │  baca D4 (takaran resep)
                          │  - terjemahkan menu → bahan (resep) │◄──► D4 resep
                          │  - potong lot FIFO (oldest first)   │◄──► D6 detail_pembelian (update sisa_qty)
                          │  - hitung HPP & laba kotor          │
                          └─────────────────────────────────────┘
                              │ simpan          │ simpan         │ simpan
                              ▼                 ▼                ▼
                      ┌──────────────┐  ┌───────────────────┐  ┌─────────────────────┐
                      │ D7 penjualan │  │ D8 detail_penjualan│  │ D9 pemakaian_bahan  │
                      └──────────────┘  └───────────────────┘  └─────────────────────┘

 ┌──────────────────────┐ ◄── D5, D6, D7, D8, D9 (baca)
 │ 5.0 Pelaporan        │ ── laporan pembelian ────────────►  PENGELOLA
 │                      │ ── laporan penjualan ────────────►
 │                      │ ── laporan persediaan ───────────►
 │                      │ ── laporan laba rugi (HPP) ──────►
 └──────────────────────┘
```

**Catatan kunci proses 4.0 (inti penelitian):**
Saat satu menu terjual, proses 4.0 membaca komposisi resep (D4) untuk
menerjemahkan menu menjadi sejumlah bahan baku, lalu memotong kuantitas dari
`D6 detail_pembelian` berdasarkan tanggal pembelian **terlama lebih dulu**
(FIFO). Setiap potongan lot dicatat ke `D9 pemakaian_bahan` sebagai dasar
perhitungan HPP, kemudian total HPP dan laba kotor disimpan di `D7 penjualan`.

---

## D. Tabel Relasi (Skema Basis Data)

Notasi: **PK** = Primary Key, **FK** = Foreign Key.

### D1. users
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK**, auto increment |
| nama | VARCHAR(100) | nama pengguna |
| username | VARCHAR(50) | unik, untuk login |
| password | VARCHAR(255) | hash |
| role | ENUM('pengelola','kasir') | hak akses |

### D2. menu
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| nama_menu | VARCHAR(100) | nama produk minuman |
| kategori | VARCHAR(50) | mis. kopi, non-kopi |
| harga_jual | DECIMAL(12,2) | harga jual per porsi |

### D3. bahan_baku
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| nama_bahan | VARCHAR(100) | mis. biji kopi, susu |
| satuan | VARCHAR(20) | gram, ml, pcs |
| stok_minimum | DECIMAL(12,2) | ambang peringatan stok |

### D4. resep  *(komposisi / Bill of Materials — relasi M:N menu ↔ bahan_baku)*
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| menu_id | INT | **FK** → menu.id |
| bahan_baku_id | INT | **FK** → bahan_baku.id |
| takaran | DECIMAL(12,2) | jumlah bahan per 1 porsi |
| satuan | VARCHAR(20) | satuan takaran |

### D5. pembelian  *(header nota pembelian)*
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| no_nota_beli | VARCHAR(30) | nomor nota pembelian |
| tanggal | DATE | tanggal pembelian (dasar urutan FIFO) |
| supplier | VARCHAR(100) | nama pemasok |
| total_beli | DECIMAL(12,2) | total nilai pembelian |
| user_id | INT | **FK** → users.id (pengelola) |

### D6. detail_pembelian  *(item pembelian = ANTRIAN LOT FIFO)*
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** (= identitas lot) |
| pembelian_id | INT | **FK** → pembelian.id |
| bahan_baku_id | INT | **FK** → bahan_baku.id |
| qty_awal | DECIMAL(12,2) | kuantitas saat dibeli |
| sisa_qty | DECIMAL(12,2) | sisa kuantitas berjalan (dipotong saat penjualan) |
| harga_beli | DECIMAL(12,2) | harga beli per satuan |
| tanggal_kadaluarsa | DATE | opsional, untuk peringatan |

> Urutan FIFO diambil dari `detail_pembelian` JOIN `pembelian`
> `WHERE sisa_qty > 0 ORDER BY pembelian.tanggal ASC, detail_pembelian.id ASC`.

### D7. penjualan  *(header nota penjualan)*
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| no_nota | VARCHAR(30) | nomor nota penjualan unik |
| tanggal | DATETIME | waktu transaksi |
| user_id | INT | **FK** → users.id (kasir) |
| total_jual | DECIMAL(12,2) | total harga jual |
| total_hpp | DECIMAL(12,2) | total HPP hasil FIFO |
| laba_kotor | DECIMAL(12,2) | total_jual − total_hpp |

### D8. detail_penjualan  *(item per nota penjualan)*
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| penjualan_id | INT | **FK** → penjualan.id |
| menu_id | INT | **FK** → menu.id |
| qty | INT | jumlah porsi menu |
| harga_jual | DECIMAL(12,2) | harga jual saat transaksi |
| subtotal | DECIMAL(12,2) | qty × harga_jual |
| hpp_menu | DECIMAL(12,2) | HPP item ini (hasil FIFO) |

### D9. pemakaian_bahan  *(log pemotongan FIFO per lot — sumber audit HPP)*
| Field | Tipe | Keterangan |
|-------|------|-----------|
| id | INT | **PK** |
| detail_penjualan_id | INT | **FK** → detail_penjualan.id |
| bahan_baku_id | INT | **FK** → bahan_baku.id |
| detail_pembelian_id | INT | **FK** → detail_pembelian.id (lot asal) |
| qty_dipakai | DECIMAL(12,2) | kuantitas dipotong dari lot ini |
| harga_beli | DECIMAL(12,2) | harga beli lot saat dipakai |
| subtotal_hpp | DECIMAL(12,2) | qty_dipakai × harga_beli |

---

## E. Ringkasan Kardinalitas Relasi

```
users (1) ───< (N) pembelian
users (1) ───< (N) penjualan

menu  (1) ───< (N) resep            >─── (1) bahan_baku   [resep = penghubung M:N + atribut takaran]

pembelian (1) ───< (N) detail_pembelian   >─── (1) bahan_baku
penjualan (1) ───< (N) detail_penjualan   >─── (1) menu

detail_penjualan  (1) ───< (N) pemakaian_bahan
detail_pembelian  (1) ───< (N) pemakaian_bahan      [lot asal yang dipotong]
bahan_baku        (1) ───< (N) pemakaian_bahan
```

**Alur audit HPP (dapat ditelusuri penuh):**
`penjualan → detail_penjualan → pemakaian_bahan → detail_pembelian (lot) → pembelian`,
sehingga setiap rupiah HPP pada sebuah nota dapat dipertanggungjawabkan ke nota
pembelian asal bahan bakunya.
