# BAB II — DASAR TEORI (REVISI)

## 2.1 Dasar Teori

### 2.1.1 Point of Sale (POS)

Point of Sale (POS) merupakan sistem elektronik yang digunakan untuk memproses
transaksi penjualan secara digital pada titik terjadinya penjualan. Pada
perkembangan awalnya, POS hanya berperan sebagai pengganti mesin kasir
konvensional (*cash register*) untuk mencatat nominal pembayaran dan mencetak
struk. Seiring kebutuhan pelaku usaha akan informasi yang terintegrasi, sistem
POS berkembang menjadi sistem informasi yang menggabungkan pencatatan transaksi,
manajemen persediaan, dan pelaporan kinerja usaha dalam satu kesatuan
(Hidayat & Farell, 2023).

Dalam penelitian ini, aplikasi POS dikembangkan berbasis web agar dapat diakses
secara fleksibel melalui peramban (*browser*) standar dari berbagai perangkat
tanpa proses instalasi yang rumit, serta memungkinkan operasi *multi-user* antara
komputer kasir dan perangkat pengelola. Pendekatan POS berbasis web pada kedai
kopi terbukti mempercepat proses transaksi dan meningkatkan kualitas pelayanan
pelanggan melalui pencatatan digital (Darmawan, 2021).

### 2.1.2 Persediaan (Inventory)

Persediaan (*inventory*) adalah aktiva yang dimiliki perusahaan untuk dijual
dalam kegiatan usaha normal atau berupa bahan yang digunakan dalam proses
produksi barang/jasa (Weygandt, Kimmel, & Kieso, 2019). Pada perusahaan
manufaktur maupun usaha kuliner, persediaan umumnya diklasifikasikan menjadi
tiga jenis, yaitu:

1. **Bahan baku (*raw materials*)** — bahan utama yang akan diolah, misalnya
   biji kopi dan susu pada Homwok Coffee.
2. **Barang dalam proses (*work in process*)** — bahan yang sedang diolah namun
   belum selesai.
3. **Barang jadi (*finished goods*)** — produk siap jual, dalam konteks ini berupa
   menu minuman yang disajikan kepada pelanggan.

Pengelolaan persediaan menjadi krusial karena nilai persediaan memengaruhi dua
hal sekaligus: nilai aktiva yang dilaporkan di neraca (persediaan akhir) dan
besarnya beban yang diakui di laporan laba rugi melalui Harga Pokok Penjualan
(Weygandt et al., 2019). Pada komoditas *Food & Beverage* yang bersifat mudah
rusak (*perishable*), pengendalian persediaan juga berkaitan langsung dengan
risiko kerugian akibat kedaluwarsa.

### 2.1.3 Metode Penilaian Persediaan

Karena harga beli bahan baku dapat berbeda-beda antar periode pembelian,
diperlukan suatu metode untuk menentukan biaya mana yang dibebankan ketika
persediaan digunakan/terjual. Terdapat tiga metode penilaian persediaan yang
umum digunakan (Weygandt et al., 2019):

1. **First-In, First-Out (FIFO)** — mengasumsikan bahan yang **pertama masuk**
   adalah yang **pertama keluar**. Biaya yang dibebankan ke HPP berasal dari
   pembelian terlama, sehingga nilai persediaan akhir mencerminkan harga
   pembelian terbaru.
2. **Last-In, First-Out (LIFO)** — kebalikan dari FIFO; bahan yang terakhir
   masuk dibebankan lebih dulu. Metode ini tidak diakui dalam Standar Akuntansi
   Keuangan yang berlaku di Indonesia (PSAK), sehingga jarang diterapkan.
3. **Rata-rata tertimbang (*Weighted Average*)** — membebankan biaya berdasarkan
   harga rata-rata seluruh unit yang tersedia, tanpa membedakan urutan masuk.

Penelitian ini memilih metode **FIFO** karena dua alasan utama. Pertama, FIFO
selaras dengan **alur fisik** bahan baku *Food & Beverage* yang harus digunakan
sesuai urutan masuk untuk menghindari kedaluwarsa. Kedua, FIFO menghasilkan nilai
persediaan akhir yang mendekati harga pasar terkini sehingga lebih representatif
dibanding metode Average (Weygandt et al., 2019). Metode LIFO dan Average tidak
menjadi fokus pembahasan pada penelitian ini sebagaimana dibatasi pada ruang
lingkup penelitian.

### 2.1.4 Harga Pokok Penjualan (HPP)

Harga Pokok Penjualan (HPP) atau *Cost of Goods Sold* (COGS) merupakan akumulasi
seluruh biaya langsung yang dikeluarkan untuk memperoleh atau memproduksi barang
yang **terjual** dalam suatu periode akuntansi (Weygandt et al., 2019). Secara
umum, HPP dirumuskan sebagai berikut:

```
HPP = Persediaan Awal + Pembelian Bersih − Persediaan Akhir
```

Dari rumus tersebut terlihat bahwa besarnya HPP sangat ditentukan oleh **nilai
persediaan akhir**, dan nilai persediaan akhir itu sendiri bergantung pada
**metode penilaian persediaan** yang dipilih (FIFO, LIFO, atau Average). Inilah
yang menjadikan pemilihan metode penilaian (Sub-bab 2.1.3) berkaitan langsung
dengan keandalan perhitungan HPP.

Dalam konteks Homwok Coffee, komponen HPP mencakup biaya perolehan bahan baku
utama (biji kopi dan susu) serta bahan pendukung (sirup, *paper cup*, *plastic
cup*, dan sedotan) yang benar-benar terpakai untuk menyajikan menu yang terjual.
Perhitungan HPP yang akurat menentukan keandalan laporan laba rugi dan ketepatan
penetapan harga jual; sebaliknya, HPP yang tidak akurat menimbulkan bias pada
estimasi laba dan evaluasi profitabilitas usaha (Romney & Steinbart, 2018).

### 2.1.5 Mekanisme dan Algoritma Metode FIFO

Metode FIFO didasarkan pada asumsi bahwa bahan baku yang pertama kali masuk ke
gudang adalah bahan yang pertama kali digunakan. Di dalam sistem aplikasi, FIFO
diimplementasikan menggunakan struktur data **antrian lot pembelian** (*purchase
lot queue*) yang mencatat setiap entri bahan masuk secara kronologis beserta
parameter: tanggal masuk, kuantitas, dan harga beli per unit.

Ketika transaksi penjualan terjadi, sistem memotong kuantitas dari **lot
pembelian paling awal** terlebih dahulu. Apabila kuantitas yang dibutuhkan
melebihi sisa pada lot terlama, pemotongan dilanjutkan ke lot berikutnya secara
berurutan hingga seluruh kebutuhan terpenuhi. Nilai HPP dihitung dari akumulasi
biaya lot-lot yang terpotong tersebut.

**Catatan konseptual (penting):** Pada metode FIFO, **HPP mencerminkan harga
pembelian yang lebih lama (lot terdahulu)**, sedangkan **persediaan akhir yang
mencerminkan harga terbaru**. Pada kondisi harga bahan baku yang cenderung naik
(inflasi), FIFO menghasilkan HPP yang lebih rendah dan laba kotor yang lebih
tinggi dibanding metode Average (Weygandt et al., 2019).

**Contoh numerik (potongan lot FIFO):**

Misalkan terdapat tiga lot pembelian biji kopi:

| Lot | Tanggal Masuk | Kuantitas | Harga Beli/gram | Nilai Lot |
|-----|---------------|-----------|-----------------|-----------|
| 1   | 01 Mar 2026   | 5.000 g   | Rp120           | Rp600.000 |
| 2   | 10 Mar 2026   | 5.000 g   | Rp135           | Rp675.000 |
| 3   | 20 Mar 2026   | 5.000 g   | Rp150           | Rp750.000 |

Jika total penjualan menu pada suatu periode mengonsumsi **6.000 gram** biji
kopi, pemotongan FIFO dilakukan sebagai berikut:

| Sumber | Kuantitas Dipotong | Harga/gram | Subtotal HPP |
|--------|--------------------|-----------|--------------|
| Lot 1  | 5.000 g            | Rp120      | Rp600.000    |
| Lot 2  | 1.000 g            | Rp135      | Rp135.000    |
| **Total HPP** | **6.000 g**  |            | **Rp735.000** |

Setelah transaksi, sisa persediaan adalah: Lot 2 = 4.000 g dan Lot 3 = 5.000 g,
sehingga **nilai persediaan akhir** = (4.000 × Rp135) + (5.000 × Rp150) =
Rp540.000 + Rp750.000 = **Rp1.290.000**. Nilai persediaan akhir ini mencerminkan
harga pembelian terbaru (Rp135 dan Rp150), sesuai karakteristik FIFO.

**Pseudokode algoritma pemotongan FIFO:**

```
fungsi potongFIFO(bahan, qty_dibutuhkan):
    total_hpp = 0
    daftar_lot = ambil lot bahan WHERE sisa_qty > 0 URUTKAN tanggal_masuk ASC
    untuk setiap lot dalam daftar_lot:
        jika qty_dibutuhkan == 0: berhenti
        ambil = minimum(lot.sisa_qty, qty_dibutuhkan)
        total_hpp += ambil * lot.harga_beli
        lot.sisa_qty -= ambil
        qty_dibutuhkan -= ambil
        simpan perubahan lot
    jika qty_dibutuhkan > 0:
        tangani kondisi stok tidak mencukupi
    kembalikan total_hpp
```

> Catatan implementasi: kuantitas (`sisa_qty`) harus bertipe desimal
> (`DECIMAL`) karena konsumsi bahan per porsi bersifat pecahan (mis. 18 gram per
> cangkir), dan proses pemotongan perlu dijalankan dalam satu transaksi basis
> data dengan penguncian baris (*row locking*) untuk menjaga konsistensi pada
> akses *multi-user*.

### 2.1.6 Komposisi Resep (Bill of Materials)

*Bill of Materials* (BOM) adalah daftar terstruktur yang merinci seluruh bahan
baku beserta takaran/kuantitas yang dibutuhkan untuk menghasilkan satu unit
produk jadi (Heizer, Render, & Munson, 2020). Dalam penelitian ini, BOM
dianalogikan sebagai **komposisi resep**, yaitu dekomposisi penggunaan bahan baku
untuk setiap satu porsi menu minuman.

Konsep BOM menjadi penghubung antara modul penjualan dan modul persediaan: ketika
satu menu terjual, sistem tidak langsung memotong "satu menu" dari stok,
melainkan menerjemahkannya terlebih dahulu menjadi sejumlah bahan baku sesuai
resep, lalu memotong masing-masing bahan tersebut dari antrian lot FIFO-nya.

Sebagai ilustrasi, komposisi resep satu porsi *Caffe Latte*:

| Bahan Baku   | Takaran | Satuan |
|--------------|---------|--------|
| Biji kopi    | 18      | gram   |
| Susu         | 150     | ml     |
| Gula/sirup   | 10      | ml     |
| Paper cup    | 1       | pcs    |

Dengan demikian, HPP satu porsi *Caffe Latte* merupakan penjumlahan biaya hasil
pemotongan FIFO atas keempat bahan tersebut. Mekanisme dekomposisi resep inilah
yang membedakan penelitian ini dari sistem POS atau sistem persediaan terdahulu
yang umumnya memotong stok pada level produk jual, bukan pada level bahan baku.

### 2.1.7 Rekayasa Perangkat Lunak Berbasis Web

Aplikasi ini dirancang dengan menerapkan prinsip rekayasa perangkat lunak (*RPL*).
RPL merupakan disiplin yang menerapkan pendekatan sistematis dan terukur dalam
pengembangan perangkat lunak melalui serangkaian tahapan yang dikenal sebagai
*Software Development Life Cycle* (SDLC), yang umumnya meliputi komunikasi,
perencanaan, pemodelan, konstruksi, dan penyerahan (Pressman & Maxim, 2020).

Arsitektur berbasis web dipilih agar aplikasi dapat dijalankan secara *multi-user*
dari komputer kasir maupun perangkat genggam pengelola selama terhubung ke
jaringan. Pengembangan dilakukan dengan menerapkan pola arsitektur
**Model-View-Controller (MVC)**, yaitu pemisahan tanggung jawab menjadi tiga
lapisan: *Model* (logika data dan basis data), *View* (antarmuka yang ditampilkan
ke pengguna), dan *Controller* (logika pengendali yang menghubungkan keduanya).
Pemisahan ini memudahkan pemeliharaan dan meningkatkan keteraturan kode
(Pressman & Maxim, 2020). Komponen teknologi yang digunakan meliputi:

1. **PHP (*Hypertext Preprocessor*)** — bahasa pemrograman sisi server
   (*server-side scripting*) untuk mengeksekusi logika kalkulasi FIFO, pemrosesan
   transaksi, dan manipulasi data.
2. **Framework Laravel** — kerangka kerja PHP berbasis pola MVC yang mempercepat
   pengembangan, meningkatkan keamanan dari celah kerentanan umum, dan menjaga
   keteraturan struktur kode.
3. **Basis Data MySQL** — *Relational Database Management System* (RDBMS) untuk
   menyimpan data master menu, komposisi resep, tabel lot persediaan, serta log
   riwayat transaksi secara terstruktur (Connolly & Begg, 2015).

---

## Tambahan untuk DAFTAR PUSTAKA (referensi nyata yang perlu ditambahkan)

Sumber berikut nyata dan standar untuk topik yang ditambahkan di atas. Tambahkan
ke Daftar Pustaka dan pastikan setiap sumber benar-benar dikutip di dalam teks:

- Connolly, T., & Begg, C. (2015). *Database Systems: A Practical Approach to
  Design, Implementation, and Management* (6th ed.). Harlow: Pearson Education.
- Heizer, J., Render, B., & Munson, C. (2020). *Operations Management:
  Sustainability and Supply Chain Management* (13th ed.). Harlow: Pearson
  Education.

> Catatan: Weygandt, Kimmel, & Kieso (2019), Romney & Steinbart (2018), serta
> Pressman & Maxim (2020) sudah ada di Daftar Pustaka Anda — pada draf lama ketiga
> sumber tersebut belum dikutip di dalam teks. Versi revisi ini sudah
> memanfaatkannya. Sumber Lestari (2023) dan Oktaviyanti (2025) belum dikutip di
> mana pun; gunakan di Tinjauan Pustaka atau hapus agar tidak menjadi temuan
> penguji.
