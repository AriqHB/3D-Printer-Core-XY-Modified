# 🛠️ 3D Printer Core XY Termodifikasi dengan Klipper

## Deskripsi Proyek
Proyek ini adalah pembuatan 3D printer dengan konfigurasi **Core XY** yang telah dimodifikasi, menggunakan **Klipper** sebagai firmware untuk mengontrol pergerakan. Printer ini dirancang untuk menghasilkan **mortar** menggunakan extruder khusus yang tidak memerlukan pengaturan suhu. Berikut adalah detail dari modifikasi yang dilakukan:

### Fitur Utama:
- 🔧 **Konfigurasi Core XY Modifikasi**: Dua motor menggerakkan sumbu **X** dan **Y**, sementara dua motor lainnya digunakan untuk mengontrol sumbu **Z**.
- 🔄 **Motor NEMA 17**: Menggunakan 4 motor NEMA 17 untuk pergerakan sumbu dan extruder.
- ⚙️ **Sumbu Z Termodifikasi**: Berbeda dari desain tradisional, sumbu Z yang bergerak adalah **extruder**, bukan print bed, sehingga proses pencetakan lebih stabil.
- 🏗️ **Extruder Mortar**: Extruder tidak menggunakan pengaturan suhu karena yang dikeluarkan adalah **mortar**. Fokus kontrol motor ada pada pengaturan keluaran mortar.
- 📏 **Ukuran Ruang Kerja**: Printer memiliki **ruang gerak 45 cm x 45 cm** untuk pergerakan extruder.

## Langkah-Langkah
1. **Persiapan Hardware**: 
   - Pasang motor NEMA 17 untuk menggerakkan sumbu XY dan Z.
   - Modifikasi extruder untuk keluaran mortar.
   
2. **Instalasi Klipper**:
   - Gunakan **Klipper** sebagai sistem operasi untuk mengontrol pergerakan printer.
   - Ikuti panduan instalasi yang telah disediakan untuk menghubungkan Klipper dengan mikrokontroler dan printer.

3. **Pengaturan Konfigurasi**:
   - Konfigurasi file `printer.cfg` dan `generic.cfg` untuk mengontrol motor XY, Z, dan extruder.
   - Pastikan parameter sesuai dengan ukuran printer dan karakteristik mortar extruder.

## Ruang Lingkup
- Proyek ini cocok untuk aplikasi yang membutuhkan pencetakan bahan berat seperti mortar.
- Modifikasi pada sumbu Z dan extruder memberikan stabilitas dan fleksibilitas dalam proses pencetakan.

## Komponen
- Firmware: [Klipper](https://www.klipper3d.org/)
- Mikrokontroler: SKR Mini E3 V3
- Host Komputer: Laptop Windows dengan WSL
- Motor: NEMA 17

