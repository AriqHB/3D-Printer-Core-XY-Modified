# Catatan Klipper Documentation 3D Printer Core XY Modifikasi Sumbu Z

1. **Menyalakan Printer dan Menghubungkan Mikrokontroler**
    - Setiap kali menyalakan printer, Anda harus menyalakan WSL (Windows Subsystem for Linux) dan menghubungkan mikrokontroler melalui command prompt.
    
2. **Proses Attach Mikrokontroler**
    - Pastikan perangkat USB sudah di-share sebelum melakukan attach pada WSL sesuai dengan `busid`-nya.
    
    **Langkah-langkah:**
    - Pertama, cek daftar perangkat USB yang terhubung dengan perintah berikut:
      ```bash
      usbipd list
      ```
      Perangkat yang sudah terhubung akan muncul, termasuk **USB Serial Device (COM_)**. Nomor COM ini akan berbeda-beda tergantung pada komputer yang digunakan.
      
    - Setelah perangkat terlihat sebagai shared, nyalakan WSL menggunakan perintah:
      ```bash
      wsl -u (nama_linux)
      ```
      Ganti `(nama_linux)` dengan nama Linux yang Anda atur saat instalasi.

    - Jika **USB Serial Device** sudah shared, perhatikan nomor **busid**. Lanjutkan dengan attach menggunakan perintah berikut:
      ```bash
      usbipd attach --wsl --busid=(nomor busid USB)
      ```
      Ganti `(nomor busid USB)` dengan busid yang terlihat pada hasil perintah `usbipd list`. Status perangkat harus 'shared' agar dapat di-attach.

3. **Mencari Alamat IP**
    - Setelah perangkat terhubung, Anda bisa mencari alamat IP WSL dengan menggunakan perintah berikut:
      ```bash
      /sbin/ifconfig
      ```

4. **Konfigurasi File `printer.cfg` dan `generic.cfg`**
    - File konfigurasi `printer.cfg` dan `generic.cfg` dipisahkan. Isi dari `printer.cfg` hanya memanggil file konfigurasi lain seperti `mainsail.cfg`, `generic.cfg`, dan mikrokontroler (mcu) dengan perintah berikut:
      ```ini
      [include mainsail.cfg]
      [include generic.cfg]
      ```
    
    - Pada bagian **serial mcu**, sesuaikan dengan tipe mikrokontroler yang digunakan. Anda bisa mencarinya dengan perintah berikut di WSL:
      ```bash
      ls /dev/serial/by-id/*
      ```
    
    - Pastikan **serial mcu** di `generic.cfg` disesuaikan dengan yang ada di `printer.cfg`.
# Catatan Rotation Distance dan Micro Steps untuk 3D Printer Core XY Modifikasi

## 1. Apa Itu Rotation Distance?
**Rotation Distance** adalah parameter di Klipper yang menentukan jarak (dalam milimeter) yang ditempuh oleh printer untuk setiap putaran penuh dari motor stepper. Nilai ini bervariasi tergantung pada jenis sistem penggerak yang digunakan, seperti **belt** atau **lead screw**.

### Sumbu XY (Belt GT2 dengan Pulley 16 Teeth)
Untuk sumbu X dan Y yang digerakkan oleh **belt GT2** dengan **pulley 16 teeth**, perhitungan rotation distance adalah:
```
rotation_distance = <belt_pitch> * <number_of_teeth_on_pulley>
```
- **Belt pitch**: 2 mm
- **Number of teeth on pulley**: 16 teeth

#### Hasil Perhitungan:
```
rotation_distance = 2 mm * 16 teeth = 32 mm
```
Jadi, **rotation_distance** untuk sumbu XY adalah **32 mm**.

### Sumbu Z (Lead Screw T8)
Untuk sumbu Z yang menggunakan **lead screw T8** dengan **4 thread terpisah**, perhitungan rotation distance adalah:

```
rotation_distance = <screw_pitch> * <number_of_separate_threads>
```

- **Screw pitch**: 2 mm
- **Number of separate threads**: 4

#### Hasil Perhitungan:

```
rotation_distance = 2 mm * 4  = 8 mm
```
Jadi, **rotation_distance** untuk sumbu Z adalah **8 mm**.

---

## 2. Apa Itu Micro Steps?
**Micro steps** adalah teknik untuk meningkatkan resolusi motor stepper dengan membagi langkah penuh menjadi beberapa langkah kecil. Setiap motor stepper umumnya bergerak dalam langkah penuh (full steps), tetapi dengan microstepping, kita bisa membagi langkah tersebut menjadi beberapa bagian, yang disebut **microsteps**.

### Penggunaan Microstepping pada TMC2209
Driver **TMC2209** yang digunakan pada printer ini mendukung **microstepping** hingga 256 microsteps per langkah penuh. Namun, nilai umum yang digunakan adalah **16 microsteps** karena ini adalah nilai yang cukup untuk memberikan akurasi tinggi tanpa membebani prosesor.

Dengan **microstepping**:
- Setiap langkah penuh pada motor (1.8° per step = 200 steps per revolution) dipecah menjadi 16 microsteps. 
- Hal ini meningkatkan resolusi dan memberikan kontrol yang lebih halus terhadap gerakan motor.

### Hubungan dengan Rotation Distance
Microstepping **tidak mengubah** nilai **rotation distance** yang dihitung. Klipper secara otomatis memperhitungkan microsteps dalam perhitungannya, sehingga Anda hanya perlu mengatur rotation distance berdasarkan mekanisme fisik printer (belt dan lead screw), sedangkan microstepping diatur melalui driver.

---

## Sumber Referensi:
- [Klipper Documentation: Rotation Distance](https://www.klipper3d.org/Rotation_Distance.html)
- [Klipper Documentation: Stepper Motor](https://www.klipper3d.org/Steppers.html)
