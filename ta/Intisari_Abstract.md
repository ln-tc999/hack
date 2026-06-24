# INTISARI

Homwok Coffee merupakan kedai kopi lokal yang bergerak di bidang penjualan
produk minuman dan masih mengelola pencatatan transaksi penjualan secara
konvensional serta belum terintegrasi dengan sistem pengelolaan persediaan
bahan baku. Metode tersebut menimbulkan berbagai kendala, seperti tingginya
risiko kesalahan pencatatan (human error), sulitnya menyajikan laporan penjualan
secara seketika (real-time), serta ketidakakuratan dalam menghitung Harga Pokok
Penjualan (HPP) akibat fluktuasi harga bahan baku antar periode pembelian.
Ketidakakuratan penentuan HPP tersebut berdampak langsung pada ketidaktepatan
penetapan harga jual, bias pada perhitungan laba, lemahnya pengendalian stok,
serta meningkatnya risiko penggunaan bahan baku yang melewati batas kadaluarsa.

Untuk mengatasi permasalahan tersebut, penelitian ini mengembangkan Aplikasi
Penjualan Kasir (Point of Sale) berbasis web yang dibangun menggunakan
framework Laravel pada sisi backend sebagai penyedia layanan REST API dan
Next.js (React) pada sisi frontend sebagai antarmuka pengguna, dengan basis
data MySQL. Sistem dirancang
untuk mengotomatisasi pencatatan transaksi penjualan sekaligus mengintegrasikannya
dengan pengelolaan persediaan, di mana setiap menu yang terjual didekomposisi
menjadi kebutuhan bahan baku berdasarkan komposisi resep. Perhitungan Harga
Pokok Penjualan (HPP) dilakukan secara otomatis menggunakan metode First-In,
First-Out (FIFO) dengan memotong kuantitas dari lot pembelian terlama terlebih
dahulu. Integrasi data secara terpusat memungkinkan penyajian laporan perputaran
persediaan dan laporan estimasi laba rugi kotor berdasarkan HPP per transaksi
secara otomatis dan real-time, sehingga meningkatkan efisiensi operasional,
akurasi laporan keuangan, serta kualitas pengendalian persediaan pada Homwok
Coffee.

**Kata Kunci:** Point of Sale, Harga Pokok Penjualan, FIFO, Persediaan Bahan
Baku, Komposisi Resep, Laravel, Next.js, REST API, MySQL, Kedai Kopi.

---

# ABSTRACT

Homwok Coffee is a local coffee shop engaged in the sale of beverage products
that still records its sales transactions conventionally and has not integrated
them with a raw-material inventory management system. This approach creates
several challenges, including a high risk of human error, difficulty in
presenting real-time sales reports, and inaccuracy in calculating the Cost of
Goods Sold (COGS) caused by fluctuating raw-material prices across purchasing
periods. This COGS inaccuracy directly affects the incorrect setting of selling
prices, bias in profit calculation, weak stock control, and an increased risk of
using expired raw materials.

To address these issues, this research developed a web-based Point of Sale (POS)
application built using the Laravel framework as a REST API backend and Next.js
(React) as the frontend user interface, with a MySQL database. The system was
designed to automate sales transaction recording while
integrating it with inventory management, in which each sold menu item is
decomposed into its raw-material requirements based on its recipe composition.
The Cost of Goods Sold (COGS) is calculated automatically using the First-In,
First-Out (FIFO) method by deducting quantities from the oldest purchase lots
first. Centralized data integration allows inventory turnover reports and gross
profit-and-loss estimation reports based on per-transaction COGS to be generated
automatically and in real time, thereby improving operational efficiency,
financial reporting accuracy, and inventory control at Homwok Coffee.

**Keywords:** Point of Sale, Cost of Goods Sold, FIFO, Raw-Material Inventory,
Recipe Composition, Laravel, Next.js, REST API, MySQL, Coffee Shop.
