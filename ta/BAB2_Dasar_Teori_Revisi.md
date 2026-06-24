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
jaringan. Aplikasi ini dikembangkan dengan arsitektur **terpisah (*decoupled*)**
antara sisi backend dan frontend, sehingga teknologi pendukungnya diuraikan ke
dalam empat bagian berikut.

#### 2.1.7.1 Arsitektur Client–Server dan RESTful API

Aplikasi dirancang menggunakan arsitektur *client–server* yang terpisah, di mana
sisi *backend* (peladen) dan sisi *frontend* (klien) merupakan dua bagian mandiri
yang berkomunikasi melalui antarmuka pemrograman aplikasi (*Application
Programming Interface*/API). Gaya arsitektur API yang digunakan adalah
**Representational State Transfer (REST)**, yaitu pola komunikasi *stateless*
berbasis protokol HTTP yang mengakses sumber daya (*resource*) melalui *endpoint*
dengan metode standar seperti GET, POST, PUT, dan DELETE (Fielding, 2000).

Pertukaran data antara backend dan frontend dilakukan dalam format **JSON
(*JavaScript Object Notation*)**, yaitu format teks ringan yang mudah dibaca
mesin maupun manusia. Dengan pendekatan ini, logika bisnis (termasuk kalkulasi
FIFO dan HPP) sepenuhnya berada di sisi backend, sedangkan frontend hanya
bertugas menampilkan data dan menerima masukan pengguna. Pemisahan ini
meningkatkan keteraturan, kemudahan pemeliharaan, dan memungkinkan kedua sisi
dikembangkan secara independen.

#### 2.1.7.2 Laravel dan Pola MVC (Backend/API)

Sisi backend dibangun menggunakan **Laravel**, yaitu kerangka kerja (*framework*)
berbahasa **PHP (*Hypertext Preprocessor*)** yang menerapkan pola arsitektur
**Model-View-Controller (MVC)**. Pola MVC memisahkan tanggung jawab menjadi tiga
lapisan: *Model* (logika data dan interaksi basis data), *Controller* (logika
pengendali dan aturan bisnis), dan *View* (penyajian keluaran) (Pressman & Maxim,
2020).

Pada arsitektur *decoupled* ini, peran lapisan *View* tidak lagi berupa halaman
HTML yang dirender langsung ke pengguna, melainkan digantikan oleh **respons data
dalam format JSON** yang dikirim melalui REST API. Dengan demikian, Laravel
berfungsi sebagai penyedia layanan API yang mengeksekusi logika kalkulasi FIFO,
pemrosesan transaksi penjualan dan pembelian, serta manipulasi data persediaan.

#### 2.1.7.3 React.js dan Next.js (Frontend)

Sisi frontend dibangun menggunakan **React.js**, yaitu pustaka (*library*)
JavaScript untuk membangun antarmuka pengguna berbasis komponen yang dapat
digunakan kembali (*reusable components*). React menerapkan konsep *Virtual DOM*
untuk memperbarui tampilan secara efisien ketika terjadi perubahan data, sehingga
sesuai untuk antarmuka kasir yang bersifat interaktif dan dinamis (Naresvari &
Susetyo, 2025).

Di atas React digunakan **Next.js**, yaitu *framework* React yang menyediakan
fitur seperti perutean (*routing*), *rendering* sisi server (*server-side
rendering*), dan optimasi performa secara terstruktur. Penggunaan Next.js pada
sisi frontend yang dipadukan dengan Laravel pada sisi backend telah diterapkan
pada pengembangan sistem informasi berbasis web (Anugerah & Kosasi, 2024).
Next.js dijalankan pada lingkungan **Node.js**, yaitu *runtime* JavaScript di sisi
server yang dikenal memiliki performa pemrosesan yang baik untuk aplikasi web
(Pratama & Raharja, 2023). Frontend ini bertugas mengonsumsi REST API dari
Laravel untuk menampilkan menu, memproses keranjang penjualan, serta menyajikan
laporan kepada pengguna.

#### 2.1.7.4 Basis Data MySQL

Penyimpanan data menggunakan **MySQL**, yaitu *Relational Database Management
System* (RDBMS) yang menyimpan data secara terstruktur dalam bentuk tabel yang
saling berelasi (Connolly & Begg, 2015). MySQL digunakan untuk menyimpan data
master menu, komposisi resep, data pembelian beserta antrian lot FIFO, data
penjualan, serta log pemakaian bahan sebagai dasar perhitungan HPP. MySQL diakses
oleh backend Laravel melalui lapisan *Model* dengan memanfaatkan *Object
Relational Mapping* (ORM) Eloquent.

---

## Tambahan untuk DAFTAR PUSTAKA (referensi nyata yang perlu ditambahkan)

Sumber berikut nyata dan standar untuk topik yang ditambahkan di atas. Tambahkan
ke Daftar Pustaka dan pastikan setiap sumber benar-benar dikutip di dalam teks:

- Connolly, T., & Begg, C. (2015). *Database Systems: A Practical Approach to
  Design, Implementation, and Management* (6th ed.). Harlow: Pearson Education.
- Heizer, J., Render, B., & Munson, C. (2020). *Operations Management:
  Sustainability and Supply Chain Management* (13th ed.). Harlow: Pearson
  Education.
- Fielding, R. T. (2000). *Architectural Styles and the Design of Network-based
  Software Architectures* (Disertasi Doktoral). University of California, Irvine.
- Naresvari, E., & Susetyo, Y. A. (2025). Penerapan JavaScript React pada
  perancangan front-end website UMKM Jemari Ragil. *IT-Explore: Jurnal Penerapan
  Teknologi Informasi dan Komunikasi*, 4(1), 16–32.
- Anugerah, D. A., & Kosasi, S. (2024). Penerapan Next.js dan GraphQL dalam
  Pengembangan Mobile Web Sistem Informasi Manajemen Puskesmas. *CSRID (Computer
  Science Research and Its Development Journal)*, 16(3), 247–258.
- Pratama, I. P. A. E., & Raharja, I. M. S. (2023). Node.js Performance
  Benchmarking and Analysis at Virtualbox, Docker, and Podman Environment Using
  Node-Bench Method. *JOIV: International Journal on Informatics Visualization*,
  7(4), 2240–2246.

> Verifikasi mandiri sebelum dipakai: (1) **Anugerah & Kosasi** — tahun terbit
> perlu Anda pastikan; metadata DOI menunjukkan 2022 sementara volume 16
> mengindikasikan 2024. Cek langsung di laman jurnal CSRID. (2) Keempat sumber di
> atas nyata dan terindeks, namun pastikan penulisannya sesuai gaya selingkung
> kampus Anda. React.js, Next.js, dan Node.js kini sudah memiliki rujukan jurnal
> (bukan sekadar dokumentasi resmi), sehingga lebih kuat secara akademik.

> Catatan: Weygandt, Kimmel, & Kieso (2019), Romney & Steinbart (2018), serta
> Pressman & Maxim (2020) sudah ada di Daftar Pustaka Anda — pada draf lama ketiga
> sumber tersebut belum dikutip di dalam teks. Versi revisi ini sudah
> memanfaatkannya. Sumber Lestari (2023) dan Oktaviyanti (2025) belum dikutip di
> mana pun; gunakan di Tinjauan Pustaka atau hapus agar tidak menjadi temuan
> penguji.
