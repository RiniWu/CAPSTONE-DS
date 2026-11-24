# CAPSTONE-DS | AkutuSocks | Dataset Contoh (Diolah dari Dataset Publik)

> **DATA CONTOH**, bukan data AkutuSocks yang sebenarnya.

## Sumber Data

| Sumber | Keterangan |
|---|---|
| `retail_sales_ml_apl` | Penjualan retail (hanya dipakai untuk kuantitas) |
| `retail_inventory_ml_apl` | Snapshot stok retail |
| `flipkart_com-ecommerce_sample` | Nama, gambar, harga, dan diskon produk kaos kaki |

Ketiganya adalah dataset publik. Periksa lisensi masing-masing di halaman Kaggle sebelum publikasi.

## Struktur Folder

| Folder | Isi |
|---|---|
| `01_gathering/`, `02_assessing/`, `03_cleaning/` | Profil dataset, temuan kualitas, dan catatan setiap langkah pembersihan |
| `fs/csv/`, `fs/sql/` | Tabel database (Full-Stack) |
| `fs/kasir_pintar/` | `barang_aplikasi_format_template.xlsx`, `kasir_benar` (uji impor), uji negatif, `hasil_diharapkan_pencocokan.csv` |
| `fs/skenario_uji/` | Skenario 5 tercatat / 3 tersedia fisik (selisih 2 barang) |
| `ai/` | `dataset_intent_latih.csv`, `dataset_intent_uji.csv`, `dataset_intent_lengkap.csv`, `uji_di_luar_konteks_holdout.csv`, `kamus_intent.csv` |
| `analisis/` | `produk_master_lengkap` (2.326 produk), `stok_aktif_per_toko`, `penjualan_harian_bersih` (hanya kuantitas), `toko_ke_gudang`, `pemetaan_flipkart_acuan` |
| `dokumentasi/` | `pemetaan_kolom_template.csv`, `profil_kolom.csv`, `hasil_validasi.csv` |

### Menjalankan SQL

Jalankan berurutan dari `fs/sql/`:

1. `00_schema.sql`
2. `01_*`, `02_*`, dst. secara berurutan
3. `99_reset_sequences.sql` (terakhir)

## Ringkasan Hasil

- **Produk aplikasi: 300**
  - 267 produk retail (dari 1.810 kandidat, bertingkat per kategori)
  - 33 produk kaos kaki asli dari Flipkart
  - Master lengkap: 2.359 produk (di `analisis/`)
- **Periode riwayat:** 2025-06-01 s.d. 2026-04-24
