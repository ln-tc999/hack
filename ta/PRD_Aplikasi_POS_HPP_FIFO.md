# PRD — Aplikasi Penjualan Kasir dengan Perhitungan HPP Metode FIFO
**Homwok Coffee** · Product Requirements Document · v1.0

| | |
|---|---|
| **Produk** | Aplikasi Kasir (POS) berbasis web + perhitungan HPP otomatis (FIFO) |
| **Stack** | Backend: Laravel (REST API) · Frontend: Next.js (React) · DB: MySQL |
| **Pengguna** | Barista (Kasir), Manager · (Pelanggan tidak login) |
| **Status** | Draft untuk mulai pengembangan |

---

## 1. Ringkasan Produk

Homwok Coffee saat ini mencatat penjualan dan persediaan secara manual sehingga
HPP dan laba kotor sulit diketahui dengan cepat dan akurat. Aplikasi ini
menyediakan transaksi kasir sekaligus **menghitung HPP otomatis** dengan metode
**FIFO**: ketika sebuah menu terjual, sistem menerjemahkan menu menjadi pemakaian
bahan baku (lewat resep), memotong **lot pembelian terlama lebih dahulu**, dan
menghitung HPP + laba kotor secara real-time.

### 1.1 Tujuan (Goals)
- Mempercepat transaksi kasir dan mencetak struk.
- Menghitung **HPP per transaksi secara otomatis dan akurat** dengan FIFO.
- Menyediakan laporan penjualan, pembelian, persediaan, perputaran persediaan,
  HPP, dan laba rugi kotor bagi Manager.
- Menjaga akurasi persediaan bahan baku secara real-time.

### 1.2 Bukan Tujuan (Non-Goals — di luar lingkup v1)
- Akuntansi penuh (jurnal umum, neraca, laba rugi bersih, pajak).
- Manajemen pelanggan / membership / loyalty.
- Pembayaran digital (QRIS/e-wallet) — v1 fokus tunai.
- Multi-cabang / multi-outlet.
- Aplikasi mobile native (cukup web responsif).
- Master pemasok terpisah (pemasok diisi sebagai teks pada form pembelian).

---

## 2. Pengguna & Peran (Roles)

| Peran | Akses |
|-------|-------|
| **Barista (Kasir)** | Login; kelola data master (pegawai, menu, bahan baku, resep); catat pembelian; jalankan transaksi penjualan; cetak struk; lihat sisa stok. |
| **Manager** | Login; akses seluruh **laporan** (penjualan, pembelian, persediaan, perputaran, HPP, laba rugi kotor). Read-only terhadap transaksi. |

> Catatan: pemisahan akses berbasis peran (`peran` = `barista` / `manager`).
> Manager tidak mengubah transaksi; Barista tidak wajib akses laporan keuangan.

---

## 3. Lingkup Fungsional (Functional Requirements)

### 3.1 Autentikasi & Otorisasi
- FR-A1: Login dengan `username` + `kata_sandi`; kata sandi disimpan ter-*hash* (bcrypt).
- FR-A2: Sesi/token via **Laravel Sanctum**; setiap request API menyertakan token.
- FR-A3: Middleware peran membatasi endpoint (mis. laporan keuangan hanya manager).
- FR-A4: Logout mencabut token.

### 3.2 Data Master
- FR-M1: CRUD **Pegawai** (nama, username, kata sandi, peran, status aktif).
- FR-M2: CRUD **Menu** (nama, kategori, harga jual, status aktif). Hapus = soft (set `aktif=false`).
- FR-M3: CRUD **Bahan Baku** (nama, satuan, stok minimum).
- FR-M4: Kelola **Resep** per menu: daftar (bahan, takaran, satuan). Satu menu punya banyak baris resep.

### 3.3 Pembelian (membentuk lot FIFO)
- FR-P1: Catat pembelian: header (nomor, tanggal, pemasok, total) + banyak item.
- FR-P2: Setiap **item pembelian = satu lot** pada `detail_pembelian` dengan `qty_awal`, `sisa_qty` (= qty_awal), `harga_beli` per satuan, `tanggal_kadaluarsa`.
- FR-P3: `total_beli` = Σ(qty × harga_beli) item.
- FR-P4: Satuan & harga beli disimpan dalam **satuan dasar** bahan (mis. gram/ml) agar konsisten dengan takaran resep.

### 3.4 Penjualan + Mesin HPP FIFO (INTI SISTEM)
- FR-S1: Buat transaksi penjualan: pilih menu + jumlah porsi → keranjang.
- FR-S2: Saat disimpan, sistem **menerjemahkan tiap menu ke kebutuhan bahan** (resep × qty porsi).
- FR-S3: Sistem **memotong lot FIFO** (tanggal beli terlama dulu), mencatat tiap potongan ke `pemakaian_bahan`.
- FR-S4: Hitung `hpp_menu` per item, `total_hpp` dan `laba_kotor` per transaksi.
- FR-S5: Bila stok bahan **tidak mencukupi**, transaksi **ditolak** dengan pesan bahan apa yang kurang (lihat §5.3).
- FR-S6: Cetak struk penjualan.
- FR-S7: Seluruh proses dijalankan dalam **satu transaksi DB** dengan penguncian baris lot (lihat §5).

### 3.5 Persediaan
- FR-I1: Sisa stok per bahan = Σ `sisa_qty` seluruh lot bahan tersebut.
- FR-I2: Nilai persediaan = Σ(`sisa_qty` × `harga_beli`) per lot.
- FR-I3: Peringatan bila sisa stok ≤ `stok_minimum`.

### 3.6 Laporan (Manager)
- FR-R1: Laporan Penjualan (per periode): daftar nota, total jual, total HPP, laba kotor.
- FR-R2: Laporan Pembelian (per periode).
- FR-R3: Laporan Persediaan (sisa & nilai per bahan, status stok minimum).
- FR-R4: Laporan Perputaran Persediaan (HPP ÷ rata-rata persediaan).
- FR-R5: Laporan HPP (rincian potongan lot per nota/periode).
- FR-R6: Laporan Laba Rugi Kotor (Σ penjualan − Σ HPP).

---

## 4. Model Data (Skema)

Sembilan tabel. Detail field ada di `Perancangan_Struktur_Data_Tabel.md`
(Tabel 3.5–3.13). Ringkasan untuk implementasi migrasi:

```
pegawai(id_pegawai PK, nama_lengkap, username UQ, kata_sandi, peran[barista|manager], aktif, dibuat_pada)
menu(id_menu PK, nama_menu, kategori, harga_jual DECIMAL(15,2), aktif, dibuat_pada)
bahan_baku(id_bahan PK, nama_bahan, satuan, stok_minimum DECIMAL(12,2), dibuat_pada)
resep(id_resep PK, id_menu FK, id_bahan FK, takaran DECIMAL(12,2), satuan)
pembelian(id_pembelian PK, id_pegawai FK, nomor_pembelian, tanggal_beli DATE, pemasok, total_beli DECIMAL(15,2), dibuat_pada)
detail_pembelian(id_detail_pembelian PK, id_pembelian FK, id_bahan FK,
                 qty_awal DECIMAL(12,2), sisa_qty DECIMAL(12,2),
                 harga_beli DECIMAL(15,2), tanggal_kadaluarsa DATE)        ← LOT FIFO
penjualan(id_penjualan PK, id_pegawai FK, nomor_nota, tanggal_jual DATETIME,
          total_jual DECIMAL(15,2), total_hpp DECIMAL(15,2), laba_kotor DECIMAL(15,2), dibuat_pada)
detail_penjualan(id_detail_penjualan PK, id_penjualan FK, id_menu FK,
                 qty INT, harga_jual DECIMAL(15,2), subtotal DECIMAL(15,2), hpp_menu DECIMAL(15,2))
pemakaian_bahan(id_pemakaian PK, id_detail_penjualan FK, id_bahan FK, id_detail_pembelian FK,
                qty_dipakai DECIMAL(12,2), harga_beli DECIMAL(15,2), subtotal_hpp DECIMAL(15,2))  ← LOG HPP
```

**Indeks penting (kinerja FIFO):**
- `detail_pembelian (id_bahan, sisa_qty)` + join ke `pembelian.tanggal_beli` untuk urutan FIFO.
- `pemakaian_bahan (id_detail_penjualan)`, `pemakaian_bahan (id_detail_pembelian)`.

**Aturan tipe data:** gunakan `DECIMAL` untuk kuantitas/harga (jangan `float`)
agar tidak ada galat pembulatan pada HPP.

---

## 5. Logika Inti: Mesin HPP FIFO

### 5.1 Algoritma (pseudocode)

```
function prosesPenjualan(items):                 # items = [{id_menu, qty_porsi}]
  DB.transaction:                                # ACID
    jual = Penjualan.create(no_nota, tanggal, total=0)
    total_jual = 0; total_hpp = 0

    for it in items:
      menu = Menu.find(it.id_menu)
      detail = DetailPenjualan.create(jual, menu, qty=it.qty_porsi,
                                      harga_jual=menu.harga_jual,
                                      subtotal=menu.harga_jual*it.qty_porsi)
      hpp_menu = 0
      resep = Resep.where(id_menu = menu.id)             # komposisi bahan

      for r in resep:
        butuh = r.takaran * it.qty_porsi                 # kebutuhan bahan (satuan dasar)

        # Ambil lot bahan, TERLAMA dulu, KUNCI barisnya
        lots = DetailPembelian
                 .join(pembelian)
                 .where(id_bahan = r.id_bahan, sisa_qty > 0)
                 .orderBy(pembelian.tanggal_beli ASC, detail_pembelian.id ASC)
                 .lockForUpdate()                          # SELECT ... FOR UPDATE

        for lot in lots:
          if butuh <= 0: break
          ambil = min(lot.sisa_qty, butuh)
          lot.sisa_qty -= ambil; lot.save()
          PemakaianBahan.create(detail, r.id_bahan, lot,
                                qty_dipakai=ambil,
                                harga_beli=lot.harga_beli,
                                subtotal_hpp=ambil*lot.harga_beli)
          hpp_menu += ambil * lot.harga_beli
          butuh    -= ambil

        if butuh > 0:                                      # stok kurang
          ROLLBACK -> error("Stok '%s' tidak cukup", r.bahan.nama)

      detail.hpp_menu = hpp_menu; detail.save()
      total_jual += detail.subtotal
      total_hpp  += hpp_menu

    jual.total_jual = total_jual
    jual.total_hpp  = total_hpp
    jual.laba_kotor = total_jual - total_hpp
    jual.save()
  COMMIT
  return jual
```

### 5.2 Query urutan FIFO (acuan SQL)
```sql
SELECT dp.*
FROM detail_pembelian dp
JOIN pembelian p ON p.id_pembelian = dp.id_pembelian
WHERE dp.id_bahan = :id_bahan AND dp.sisa_qty > 0
ORDER BY p.tanggal_beli ASC, dp.id_detail_pembelian ASC
FOR UPDATE;
```
Urutan **tanggal beli ASC, lalu id ASC** (tie-breaker) memastikan lot tertua
dipakai lebih dahulu sekalipun ada pembelian backdate pada tanggal sama.

### 5.3 Aturan Bisnis & Edge Case
- **Stok kurang** → tolak seluruh transaksi (rollback), jangan biarkan stok negatif.
- **Menu tanpa resep** → tidak boleh dijual (validasi sebelum potong stok).
- **Konsistensi satuan** → takaran resep & harga beli lot wajib satuan dasar yang sama.
- **Concurrency** → `lockForUpdate()` mencegah dua kasir memotong lot yang sama (double-spend).
- **Pembelian backdate** → urutan FIFO tetap berdasarkan `tanggal_beli`.
- **Pembulatan** → semua kuantitas/uang `DECIMAL`; subtotal_hpp dibulatkan konsisten (2 desimal).
- **Soft delete master** → menu/bahan yang sudah terpakai tidak dihapus fisik (set non-aktif).

### 5.4 Contoh uji (acuan unit test — dari BAB II)
3 lot bahan: 5000 g @Rp120, 5000 g @Rp135, 5000 g @Rp150. Pemakaian 6000 g →
HPP = 5000×120 + 1000×135 = **Rp735.000**; sisa = 4000@135 + 5000@150 =
**Rp1.290.000**. Test FIFO service harus menghasilkan angka ini persis.

---

## 6. Arsitektur & API

### 6.1 Arsitektur
```
Next.js (React, browser)  ──HTTP/JSON──►  Laravel REST API  ──SQL──►  MySQL
   antarmuka kasir & laporan               Controller → Service(FIFO) → Eloquent
```
- **Backend**: Laravel — Controller (validasi/HTTP) → Service (logika FIFO/HPP) → Model (Eloquent). Auth via Sanctum.
- **Frontend**: Next.js (App Router) — halaman kasir, master, laporan; fetch ke API; state keranjang di klien.
- **Format**: JSON; status code standar (200/201/422/401/403/409).

### 6.2 Daftar Endpoint (REST)

| Method | Endpoint | Fungsi | Peran |
|--------|----------|--------|-------|
| POST | `/api/login` | Login → token | semua |
| POST | `/api/logout` | Logout | semua |
| GET | `/api/me` | Profil pengguna | semua |
| GET/POST/PUT/DELETE | `/api/pegawai` | CRUD pegawai | barista |
| GET/POST/PUT/DELETE | `/api/menu` | CRUD menu | barista |
| GET/POST/PUT/DELETE | `/api/bahan` | CRUD bahan baku | barista |
| GET | `/api/menu/{id}/resep` | Lihat resep menu | barista |
| POST/PUT | `/api/menu/{id}/resep` | Set komposisi resep | barista |
| GET/POST | `/api/pembelian` | List / catat pembelian (buat lot) | barista |
| GET | `/api/pembelian/{id}` | Detail pembelian | barista |
| GET/POST | `/api/penjualan` | List / **transaksi penjualan (FIFO)** | barista |
| GET | `/api/penjualan/{id}` | Detail nota + rincian HPP | barista |
| GET | `/api/persediaan` | Sisa & nilai stok per bahan | semua |
| GET | `/api/laporan/penjualan?from&to` | Laporan penjualan | manager |
| GET | `/api/laporan/pembelian?from&to` | Laporan pembelian | manager |
| GET | `/api/laporan/persediaan` | Laporan persediaan | manager |
| GET | `/api/laporan/perputaran?from&to` | Perputaran persediaan | manager |
| GET | `/api/laporan/hpp?from&to` | Laporan HPP | manager |
| GET | `/api/laporan/laba-rugi?from&to` | Laba rugi kotor | manager |

### 6.3 Contoh kontrak `POST /api/penjualan`
```jsonc
// Request
{ "items": [ { "id_menu": 1, "qty": 2 }, { "id_menu": 3, "qty": 1 } ] }

// Response 201
{
  "nomor_nota": "NJ-20260630-001",
  "tanggal_jual": "2026-06-30T14:05:00",
  "total_jual": 51000, "total_hpp": 21300, "laba_kotor": 29700,
  "items": [
    { "id_menu": 1, "nama_menu": "Kopi Susu", "qty": 2, "subtotal": 36000, "hpp_menu": 14600 }
  ]
}

// Response 422 (stok kurang)
{ "message": "Stok 'Susu' tidak cukup", "bahan_kurang": ["Susu"] }
```

---

## 7. Antarmuka (Screens)

Mengacu rancangan di `BAB3_Analisis_Perancangan.md` (Gambar 3.5–3.16):

**Barista**: Login · Dashboard · Kasir/Penjualan · Daftar Menu · Bahan Baku ·
Resep · Pembelian · Pegawai · Persediaan/Sisa Stok · Struk (cetak).
**Manager**: Login · Dashboard laporan · Laporan Penjualan/Pembelian/
Persediaan/Perputaran/HPP/Laba Rugi Kotor (filter periode, ekspor/cetak).

---

## 8. Kebutuhan Non-Fungsional

| Aspek | Target |
|-------|--------|
| Keamanan | Hash bcrypt; token Sanctum; otorisasi per peran; validasi input. |
| Kinerja | Transaksi penjualan (termasuk FIFO) selesai ≤ 2 detik untuk keranjang wajar. |
| Integritas | Transaksi DB + `FOR UPDATE`; tidak ada stok negatif; HPP konsisten. |
| Usability | Alur kasir minim klik; tampilan responsif. |
| Portabilitas | Berbasis web, jalan di peramban modern. |
| Maintainability | Backend/Frontend terpisah; logika FIFO di Service terisolasi & ber-unit-test. |

---

## 9. Rencana Pengembangan (Milestones)

| Sprint | Fokus | Output |
|--------|-------|--------|
| **0 — Setup** | Repo, Laravel, Next.js, MySQL, migrasi 9 tabel, Sanctum, seeder. | Login jalan; skema DB siap. |
| **1 — Master Data** | CRUD pegawai, menu, bahan baku, resep. | Data master bisa dikelola. |
| **2 — Pembelian** | Form & API pembelian; pembentukan lot FIFO; sisa stok. | Lot terbentuk; persediaan akurat. |
| **3 — Penjualan + HPP (INTI)** | Service FIFO, transaksi penjualan, pemakaian_bahan, struk. | HPP & laba kotor otomatis; unit test §5.4 lulus. |
| **4 — Laporan** | 6 laporan + filter periode + cetak/ekspor. | Manager dapat menelaah laporan. |
| **5 — Polish & QA** | Validasi, peringatan stok minimum, UX, pengujian, dokumentasi. | Siap demo/sidang. |

---

## 10. Kriteria Penerimaan (Definition of Done)

- [ ] Login & otorisasi peran berfungsi (barista vs manager).
- [ ] CRUD seluruh data master berjalan dengan validasi.
- [ ] Pembelian membentuk lot dengan `sisa_qty` benar.
- [ ] Penjualan memotong lot **FIFO** dan menolak bila stok kurang.
- [ ] `total_hpp` & `laba_kotor` benar; **unit test contoh §5.4 menghasilkan HPP Rp735.000**.
- [ ] `pemakaian_bahan` mencatat audit trail lot per item.
- [ ] Persediaan real-time = Σ sisa lot; peringatan stok minimum muncul.
- [ ] Keenam laporan tampil sesuai periode dan dapat dicetak.
- [ ] Struk tercetak dengan benar.
- [ ] Tidak terjadi stok negatif pada uji konkurensi (dua kasir bersamaan).

---

## 11. Risiko & Mitigasi

| Risiko | Mitigasi |
|--------|----------|
| Salah hitung HPP karena pembulatan | Pakai `DECIMAL`, uji dengan kasus §5.4. |
| Race condition multi-kasir | `lockForUpdate()` + transaksi DB. |
| Satuan resep ≠ satuan lot | Standarkan ke satuan dasar; validasi saat input. |
| Menu tanpa resep terjual | Validasi wajib punya resep sebelum dijual. |
| Data master terhapus padahal terpakai | Soft delete (non-aktif), bukan hapus fisik. |

---

## 12. Data Awal (Seed)
- 2 pegawai: 1 barista, 1 manager.
- ±10 menu kopi/non-kopi dengan harga jual.
- ±8 bahan baku (biji kopi, susu, gula, dll.) + stok minimum.
- Resep untuk tiap menu.
- Beberapa pembelian (≥2 lot per bahan, harga berbeda) agar FIFO teruji.
