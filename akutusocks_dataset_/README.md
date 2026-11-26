# AkutuStocks | Dataset (diolah dari dataset publik)

**PERHATIAN:** ini DATA CONTOH, bukan data Akutusocks. Tiga sumber: `retail_sales_ml_apl`, `retail_inventory_ml_apl`, dan `flipkart_com-ecommerce_sample`
(dataset publik; periksa lisensi masing-masing di halaman Kaggle sebelum publikasi). Temuan analisis dari data ini tidak boleh diklaim sebagai kondisi nyata Akutusocks.

## Struktur folder (DUA dataset utama)
**Dataset 1: `dataset_website/`**
- `produk_data_master.csv` : SATU tabel datar (produk x gudang): atribut produk utama + acuan, tanggal cek, stok mentah (diterima) dan stok setelah dicek, id_gudang, acuan produk Flipkart. Kamus kolom: `dokumentasi/kamus_data_produk_data_master.csv`.
- `database/csv/`, `database/sql/` : tabel database TURUNAN dari data yang sama (Full-Stack). Jalankan sql/00_schema.sql, lalu 01_*, 02_*, ... berurutan, terakhir 99_reset_sequences.sql.
- `pendukung_analisis/penjualan_harian_bersih.csv.gz` : penjualan harian (hanya kuantitas), memuat id_gudang dan toko.

**Dataset 2: `dataset_kasir_import/`**
- `data_import_kasir.xlsx` / `.csv` : berkas ekspor Kasir Pintar contoh (kolom TEMPLATE_BARANG, stok mentah, ada stok negatif).
- `pendukung_uji/` : hasil_diharapkan_pencocokan.csv (kunci jawaban), uji negatif (format salah, tanpa kolom kode, semua kode tidak ada, kosong), template_barang_aplikasi.xlsx, skenario/ (5 tercatat / 3 tersedia fisik, selisih 2 barang).

**Kunci seragam:** `kode_barang` (ID produk) dan `id_gudang` (1=Gudang A, 2=Gudang B, 3=Gudang C) dipakai sama di semua berkas.

**Pendukung lain:** 01_gathering/, 02_assessing/, 03_cleaning/ (profil, temuan kualitas, catatan cleaning); ai/ (dataset intent, kamus intent, uji di luar konteks); dokumentasi/ (pemetaan kolom, profil kolom, hasil validasi, kamus data master).

## Ringkasan hasil
- Produk aplikasi: 300 = 267 produk retail (dari 1810 kandidat, bertingkat per kategori) + 33 produk kaos kaki asli dari Flipkart. produk_data_master memuat 2359 produk (utama + acuan) x 3 gudang = 7077 baris.
- Periode riwayat: 2025-06-01 s.d. 2026-04-24 (tanggal asli, tidak digeser). Riwayat: 23561 baris. Status Diajukan (menunggu SPV): 478.
- 40 toko dikelompokkan menjadi 3 gudang (Gudang A, Gudang B, Gudang C), seimbang menurut stok.

## Asumsi dan keterbatasan (WAJIB dibaca)
1. Tema data retail adalah **sepatu dan pakaian anak**, bukan kaos kaki. Kaos kaki hanya 33 produk asli dari Flipkart (pencarian kata "sock" menangkap 28 produk Phone Socket Holder yang dikeluarkan). **Stok dan penjualan kaos kaki SINTETIS** (parameter SOCK_RATE_MEDIAN, SOCK_RETUR_PROB); hanya nama, gambar, harga, dan diskon yang berasal dari Flipkart. Harga INR dikali 190 sebagai ASUMSI kasar; harga beli diimputasi (Flipkart tidak punya biaya); ada outlier (mis. kaos kaki Rp 400 ribuan).
2. Harga produk retail dikali 1000 sebagai ASUMSI agar bermagnitudo rupiah; dataset tidak menyebut mata uang.
3. Kolom keuangan penjualan (Sales Amount, Cogs) TIDAK dipakai (batasan project plan). Penjualan retail hanya dipakai untuk kuantitas.
4. Flipkart tidak punya kunci ke data retail. Gambar, keterangan, dan berat dipasangkan ACAK per kelompok kategori untuk produk retail (lihat kolom flipkart_pid, flipkart_nama, metode_acuan_flipkart di produk_data_master.csv); produk kaos kaki memakai data aslinya sendiri. Tautan gambar tahun 2016 mungkin sudah mati.
5. Penerimaan barang tidak ada di data sumber. Stok awal, penerimaan, dan penyesuaian akhir DIESTIMASI agar seluruh riwayat berujung tepat di snapshot stok. 3251 penerimaan diestimasi; 320 dari 900 pasangan barang-gudang punya snapshot lebih rendah daripada perhitungan dari penjualan (dicatat sebagai penyesuaian akhir, status Diajukan).
6. data_import_kasir memakai stok MENTAH inventory (ada stok negatif), jadi selisih dengan website muncul alami dari data kotor. Format ekspor asli belum ada dari PIC; diasumsikan sama dengan TEMPLATE_BARANG.
7. Akun, riwayat validasi, dan letak rak adalah sintetis. Tidak ada contoh penolakan SPV, opname, maupun impor historis di seed; fitur itu diuji lewat aplikasi memakai berkas di dataset_kasir_import/.
8. Tanggal riwayat lama (2025-2026). Fitur pengingat sebaiknya memakai tanggal acuan = tanggal data terakhir (2026-04-24), bukan tanggal hari ini.
9. Dataset intent: kalimat dibangkitkan dari template buatan tim (termasuk bahasa santai/singkatan) dengan entitas dari data retail; data uji dipisah per template. Variasi kelas prosedur_stok terbatas (52 template).
10. `stok_mentah_diterima` = stok apa adanya dari sumber (bisa negatif); `stok_setelah_dicek` = setelah dibersihkan (negatif per toko dijadikan 0 sebelum dijumlahkan per gudang, sehingga bisa lebih besar dari stok mentah). Produk tanpa catatan stok di suatu gudang bernilai 0 dengan `tanggal_cek` kosong. Brand/supplier, URL produk, waktu crawl, dan kategori produk Flipkart sengaja tidak dibawa ke master.
11. Akun contoh: password `Demo#2026` (hash bcrypt), email @contoh.test.
