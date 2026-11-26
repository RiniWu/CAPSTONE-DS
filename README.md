# CAPSTONE-DS | AkutuSocks | Dataset Contoh (Diolah dari Dataset Publik)

> **DATA CONTOH**, bukan data AkutuSocks yang sebenarnya. Temuan analisis dari data ini tidak boleh diklaim sebagai kondisi nyata AkutuSocks.

## Sumber Data

| Sumber | Keterangan |
|---|---|
| `retail_sales_ml_apl` | Penjualan retail (hanya dipakai untuk kuantitas) |
| `retail_inventory_ml_apl` | Snapshot stok retail |
| `flipkart_com-ecommerce_sample` | Nama, gambar, harga, dan diskon produk kaos kaki; gambar, keterangan, dan berat sebagai acuan produk retail |

Ketiganya adalah dataset publik. Periksa lisensi masing-masing di halaman Kaggle sebelum dipublikasikan.

## Dua Dataset Utama

| Dataset | Berkas utama | Isi |
|---|---|---|
| **1. Data untuk website** | `dataset_website/produk_data_master.csv` | Satu tabel datar, satu baris per produk x gudang (7.077 baris = 2.359 produk x 3 gudang) |
| **2. Data import Kasir Pintar** | `dataset_kasir_import/data_import_kasir.xlsx` (dan `.csv`) | Berkas ekspor Kasir Pintar contoh, kolom `TEMPLATE_BARANG`, stok mentah |

### Kunci seragam

| Kunci | Arti |
|---|---|
| `kode_barang` | ID produk. Retail: `Product No`; kaos kaki: `FKS-<pid Flipkart>`. Di berkas Kasir Pintar bernama `data_kode_barang`. |
| `id_gudang` | ID gudang: 1 = Gudang A, 2 = Gudang B, 3 = Gudang C |

### Isi `produk_data_master.csv`

| Kelompok kolom | Kolom |
|---|---|
| Identitas produk | `kode_barang`, `nama_barang`, `divisi`, `kategori`, `subkategori` |
| Gudang dan stok | `id_gudang`, `nama_gudang`, `tanggal_cek`, `stok_mentah_diterima`, `stok_setelah_dicek`, `letak_rak`, `minimum_stok` |
| Harga | `harga_beli`, `harga_jual`, `harga_beli_diimputasi`, `diskon_persen` |
| Tampilan | `satuan`, `berat_gram`, `keterangan`, `gambar` |
| Penjualan | `unit_keluar`, `unit_retur` (total semua gudang, hanya kuantitas) |
| Acuan Flipkart | `flipkart_pid`, `flipkart_nama`, `metode_acuan_flipkart` |
| Penanda | `masuk_aplikasi` (True = produk utama, False = acuan), `sumber_data` |

Kamus lengkap: `dokumentasi/kamus_data_produk_data_master.csv`. Kolom `supplier`/brand, `product_url`, waktu crawl, kategori produk Flipkart, dan `toko` sengaja tidak dibawa.

## Struktur Folder

| Folder | Isi |
|---|---|
| `dataset_website/` | `produk_data_master.csv` |
| `dataset_website/database/csv/`, `dataset_website/database/sql/` | Tabel database turunan dari data yang sama (Full-Stack) |
| `dataset_website/pendukung_analisis/` | `penjualan_harian_bersih.csv.gz` (hanya kuantitas; memuat `id_gudang` dan `toko`) |
| `dataset_kasir_import/` | `data_import_kasir.xlsx`, `data_import_kasir.csv` |
| `dataset_kasir_import/pendukung_uji/` | `hasil_diharapkan_pencocokan.csv`, uji negatif (format salah, tanpa kolom kode, semua kode tidak ada, kosong), `template_barang_aplikasi.xlsx` |
| `dataset_kasir_import/pendukung_uji/skenario/` | Skenario 5 tercatat / 3 tersedia fisik (selisih 2 barang) |
| `ai/` | `dataset_intent_latih.csv`, `dataset_intent_uji.csv`, `dataset_intent_lengkap.csv` (1.100 kalimat), `uji_di_luar_konteks_holdout.csv`, `kamus_intent.csv` |
| `01_gathering/`, `02_assessing/`, `03_cleaning/` | Profil dataset, temuan kualitas, dan catatan setiap langkah pembersihan |
| `dokumentasi/` | `pemetaan_kolom_template.csv`, `profil_kolom.csv`, `hasil_validasi.csv`, `kamus_data_produk_data_master.csv` |

### Menjalankan SQL

Jalankan berurutan dari `dataset_website/database/sql/`:

1. `00_schema.sql`
2. `01_*`, `02_*`, dst. secara berurutan
3. `99_reset_sequences.sql` (terakhir)

## Ringkasan Hasil

- **Produk aplikasi: 300**
  - 267 produk retail (dari 1.810 kandidat, bertingkat per kategori)
  - 33 produk kaos kaki asli dari Flipkart
  - Master lengkap (utama + acuan): 2.359 produk, ada di `produk_data_master.csv`
- **Periode riwayat:** 2025-06-01 s.d. 2026-04-24 (tanggal asli, tidak digeser)
- **Riwayat aktivitas:** 23.561 baris, 478 berstatus Diajukan (menunggu SPV)
- **Gudang:** 40 toko dikelompokkan menjadi 3 gudang (Gudang A, Gudang B, Gudang C), seimbang menurut stok
- **Dataset intent:** 1.100 kalimat, 5 kelas, 880 latih dan 220 uji

## Asumsi dan Keterbatasan (WAJIB dibaca)

1. **Tema retail bukan kaos kaki.** Data retail adalah sepatu dan pakaian anak. Kaos kaki hanya 33 produk asli dari Flipkart (pencarian kata "sock" menangkap 28 produk Phone Socket Holder yang dikeluarkan). **Stok dan penjualan kaos kaki SINTETIS** (parameter `SOCK_RATE_MEDIAN`, `SOCK_RETUR_PROB`); hanya nama, gambar, harga, dan diskon yang berasal dari Flipkart. Harga INR dikali 190 sebagai ASUMSI kasar, harga beli diimputasi, dan ada outlier.
2. **Skala harga retail** dikali 1000 sebagai ASUMSI agar bermagnitudo rupiah; dataset tidak menyebut mata uang.
3. **Kolom keuangan** (`Sales Amount`, `Cogs`) tidak dipakai (batasan project plan). Penjualan hanya dipakai untuk kuantitas.
4. **Acuan Flipkart untuk produk retail bersifat ACAK** per kelompok kategori. Gambar, keterangan, dan berat bukan padanan produk aslinya (lihat `flipkart_pid`, `flipkart_nama`, dan `metode_acuan_flipkart`). Tautan gambar tahun 2016 mungkin sudah mati.
5. **Penerimaan barang tidak ada di sumber.** Saldo awal, penerimaan, dan penyesuaian akhir DIESTIMASI agar riwayat berujung tepat di snapshot stok.
6. **Dua kolom stok:** `stok_mentah_diterima` adalah stok apa adanya dari sumber (bisa negatif); `stok_setelah_dicek` adalah stok setelah dibersihkan (negatif per toko dijadikan 0 sebelum dijumlahkan per gudang, sehingga bisa lebih besar dari stok mentah). Produk tanpa catatan stok di suatu gudang bernilai 0 dengan `tanggal_cek` kosong.
7. **`data_import_kasir` memakai stok MENTAH**, jadi selisih dengan website muncul alami dari data kotor. Format ekspor asli belum ada dari PIC; diasumsikan sama dengan `TEMPLATE_BARANG`. Berkas Kasir Pintar tidak memuat gudang, sehingga pencocokan dilakukan pada total semua gudang.
8. **Akun, riwayat validasi, dan letak rak SINTETIS.** Tidak ada contoh penolakan SPV, opname, maupun impor historis di seed; fitur itu diuji lewat aplikasi memakai berkas di `dataset_kasir_import/`.
9. **Tanggal acuan pengingat** sebaiknya = tanggal data terakhir (2026-04-24), bukan tanggal hari ini.
10. **Dataset intent** dibangkitkan dari template buatan tim (termasuk bahasa santai dan singkatan) dengan entitas dari data retail. Data uji dipisah per template untuk mencegah kebocoran. Variasi kelas `prosedur_stok` terbatas (52 template), sehingga akurasi pada bahasa nyata belum teruji.

## Akun Contoh

Email `@contoh.test`, password `Demo#2026` (hash bcrypt). Hanya untuk pengujian.
