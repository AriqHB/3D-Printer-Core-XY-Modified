![Gambar PIN SKR Mini E3 V3][https://github.com/AriqHB/3D-Printer-Core-XY-Modified/blob/main/Foto/PIN%20BTT%20SKR%20MINI%20E3%20V3.png]
[Sumber gambar](https://github.com/bigtreetech/BIGTREETECH-SKR-mini-E3/blob/master/hardware/BTT%20SKR%20MINI%20E3%20V3.0/Hardware/BTT%20E3%20SKR%20MINI%20V3.0_PIN.pdf)

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

---

# Catatan untuk [stepper]

**1. Pengaturan Pin**
  
  Terbagi menjadi tiga step_pin; dir_pin; enable_pin. Untuk pin yang dipakai bisa dilihat pada gambar pin mikrokontroler.
  
  - **step_pin**: untuk mengirimkan sinyal langkah (step) ke driver motor stepper. Setiap kali sinyal step ini berubah dari rendah (low) ke tinggi (high), motor stepper akan bergerak satu langkah.
    
  -	**dir_pin**: untuk mengontrol arah rotasi motor stepper. Ketika pin ini dalam kondisi rendah (low), motor akan bergerak ke satu arah jarum jam (misalnya, maju); ketika pin dalam kondisi tinggi (high), motor akan bergerak ke arah yang berlawanan (misalnya, mundur)
    
    ```
    Tanda seru (!): menunjukkan bahwa logika dari pin tersebut terbalik. Dalam contoh di atas, pin PB4 akan menonaktifkan driver jika dalam kondisi tinggi (high) dan mengaktifkannya jika dalam kondisi rendah (low).
    ```
  -	**enable_pin**: untuk mengaktifkan atau menonaktifkan driver motor stepper. Ketika enable_pin berada dalam kondisi aktif (biasanya logika rendah, tergantung driver), motor akan siap untuk menerima sinyal step dan dir. Ketika dalam kondisi tidak aktif, motor akan dinonaktifkan, sehingga tidak menerima sinyal gerakan.

**2. Rotation Distance**
    
  Nilai ini menggambarkan jarak yang ditempuh oleh sumbu berdasarkan satu putaran penuh motor stepper.
  
  ```
  rotation_distance(xy): 32 mm
  rotation_distance(z): 8 mm
  ``` 

**3. Microsteps**

  `microsteps: 16`
  
  Microstepping meningkatkan resolusi gerakan motor dengan membagi setiap langkah penuh menjadi beberapa langkah kecil.

**4. Full Steps Per Rotation**

  `full_steps_per_rotation: 200`

  Motor NEMA 17 biasanya memiliki 200 langkah per putaran penuh, yang setara dengan langkah 1,8 derajat per langkah.

**5. Gear Ratio**

`gear_ratio: Tidak digunakan`

  Jika tidak ada gearbox yang terpasang, parameter ini tidak perlu diatur.

**6. Step Pulse Duration**

`step_pulse_duration: Sudah diatur`

  Parameter ini mengatur durasi pulsa untuk sinyal langkah, penting untuk memastikan sinyal diterima dengan baik oleh driver.

**7. Endstop Pin**
```
endstop_pin: tmc2209_stepper_x:virtual_endstop
endstop_pin: tmc2209_stepper_y:virtual_endstop
endstop_pin: tmc2209_stepper_z:virtual_endstop
```
  Menggunakan sensorless homing, yang berarti tidak memerlukan switch endstop fisik.

**8. Position Min dan Max**

```
position_min: 0 mm
position_max: 450 mm
```
  Menentukan batas minimum dan maksimum posisi sumbu, penting untuk mencegah gerakan di luar jangkauan fisik printer.

**9. Posisition Endstop**

`position_endstop: 0`
  
  Menandakan bahwa titik endstop berada di posisi awal printer.

**10. Homing Speed**

`homing_speed: 5 mm/s`

  Kecepatan saat proses homing, di mana motor mencari posisi nol.


**11. Homing Retract Distance**

`homing_retract_dist: 5` 
  
  Jarak untuk mundur (dalam mm) sebelum melakukan homing kedua. Default-nya adalah 5 mm, yang memberikan akurasi lebih baik saat homing ulang:

**12. Homing Retract Speed**

  homing_retract_speed: Kecepatan yang digunakan saat sumbu bergerak mundur setelah menyentuh endstop selama homing. Jika parameter ini tidak ditentukan, akan menggunakan homing_speed secara default. Untuk konsistensi, bisa menggunakan kecepatan yang sama:

`homing_retract_speed: 5.0`(default sama dengan homing_speed)

**13. Second Homing Speed**

  second_homing_speed: Kecepatan yang digunakan saat melakukan homing kedua untuk meningkatkan akurasi. Default-nya adalah setengah dari homing_speed:

`second_homing_speed: 2.5`

**14. Homing Positive Direction**

  homing_positive_dir: Menentukan apakah homing akan menyebabkan stepper bergerak ke arah positif (menjauhi nol). Default-nya adalah true jika position_endstop berada di dekat position_max, dan false jika di dekat position_min. Tidak perlu diubah kecuali ada kebutuhan khusus:

`homing_positive_dir: true`  (default)

---

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
# Konfigurasi TMC2209 untuk Extruder

## 1. Pengaturan Pin UART
Setiap driver **TMC2209** membutuhkan pengaturan pin UART yang benar untuk komunikasi dengan firmware Klipper. Berikut adalah penjelasan dari pin-pin yang digunakan:

- **uart_pin**: Menentukan pin fisik pada papan kontrol (**SKR Mini E3 V3**) yang terhubung ke **UART interface** dari driver stepper motor TMC2209. UART memungkinkan komunikasi dua arah antara firmware dan driver, sehingga parameter seperti arus motor dan mode **StealthChop** dapat dikontrol secara dinamis.
  
- **tx_pin**: Pin untuk mengirim data dari Klipper ke driver TMC2209. Data yang dikirim dapat berupa perubahan arus motor atau kecepatan ekstruder secara dinamis.

Contoh konfigurasi pada Klipper:
```ini
uart_pin: (nama pin uart dari datasheet SKR Mini E3 V3)
tx_pin: (nama pin tx dari datasheet SKR Mini E3 V3)
```

## 2. Alamat UART (uart_address)
- Setiap driver **TMC2209** dapat memiliki alamat UART yang unik jika beberapa driver terhubung pada sistem yang sama.
- Pada ekstruder, misalnya, menggunakan:
  ```ini
  uart_address: 0
  uart_address: 1
  uart_address: 2
  ```
  Ini menunjukkan bahwa driver **TMC2209** untuk ekstruder menggunakan alamat UART 3. Klipper akan mengirim sinyal ke driver ini untuk mengatur parameter ekstruder.

## 3. Arus Operasional (run_current)
- **run_current**: Mengontrol arus maksimum yang diberikan ke motor stepper selama operasi. Ini penting untuk menghindari panas berlebih pada motor sambil tetap memberikan daya yang cukup untuk pengoperasian.
- Untuk motor dengan arus maksimum **1.7 Ampere**, run_current bisa diatur antara **1.2A hingga 1.4A**. Sebagai contoh:
  ```ini
  run_current: 1.200
  ```
- Setelah diatur, penting untuk memantau suhu motor. Jika terlalu panas, arus ini mungkin perlu diturunkan lebih lanjut.

## 4. Mode StealthChop (stealthchop_threshold)
- **StealthChop** adalah mode operasi yang membuat motor stepper lebih tenang, terutama pada kecepatan rendah, dengan mengurangi getaran dan kebisingan.
- Pada ekstruder, Anda dapat mengatur:
  ```
  stealthchop_threshold: 999999
  ```
  Nilai ini memastikan bahwa **StealthChop** selalu aktif, bahkan pada kecepatan ekstruder yang sangat tinggi. Namun, pada kecepatan tinggi, Anda mungkin ingin menggunakan **spreadCycle** untuk lebih banyak torsi dan daya.

### Catatan:
- Konfigurasi di atas menggunakan **TMC2209** dengan **motor Nema 17**, disesuaikan dengan pengaturan yang optimal untuk menggerakkan ekstruder pada sistem 3D printer.


Berikut adalah **penulisan ulang G-code macros** yang disesuaikan dengan konfigurasi printer 3D CoreXY Anda berdasarkan pengaturan yang telah Anda berikan. Penyesuaian ini memperhitungkan penggunaan motor untuk menggerakkan extruder (bukan bed), penghilangan kontrol suhu, serta penggunaan endstop virtual dengan driver TMC2209 tanpa sensor homing fisik.

---

## G-code Macros yang Diperbarui

### 1. **[gcode_macro PARK]**

```ini
[gcode_macro PARK]
gcode:
    {% set th = printer.toolhead %}
    # Memindahkan extruder ke posisi parkir di tengah area kerja dan mengangkat ke ketinggian 30 mm
    G0 X{th.axis_maximum.x / 2} Y{th.axis_maximum.y / 2} Z30 #F3000
```

**Penjelasan:**
- **Tujuan:** Memindahkan extruder ke posisi tengah area kerja (X dan Y) dan mengangkatnya ke ketinggian 30 mm untuk memastikan posisi aman setelah pencetakan.
- **Perubahan:** Menambahkan kecepatan gerakan (`F3000`) untuk memastikan gerakan yang cepat dan efisien.

---

### 2. **[gcode_macro G32]**

```ini
[gcode_macro G32]
gcode:
    # Menyimpan keadaan saat ini sebelum leveling
    SAVE_GCODE_STATE NAME=STATE_G32
    
    # Mengatur posisi absolut
    G90
    
    # Melakukan homing semua sumbu menggunakan endstop virtual
    G28
    
    # Melakukan homing ulang setelah leveling
    G28
    
    # Memarkir extruder setelah leveling
    PARK
    
    # Mengembalikan keadaan printer seperti sebelum leveling
    RESTORE_GCODE_STATE NAME=STATE_G32
```

**Penjelasan:**
- **Tujuan:** Melakukan proses homing dan leveling gantry secara otomatis untuk memastikan akurasi posisi extruder sebelum pencetakan dimulai.
- **Perubahan:** Tidak ada perubahan signifikan selain memastikan penggunaan endstop virtual sesuai dengan konfigurasi Anda.

---

### 3. **[gcode_macro PRINT_START]**

```ini
[gcode_macro PRINT_START]
# Digunakan sebagai skrip awal untuk memulai pencetakan - sesuaikan sesuai slicer pilihan Anda
gcode:
    # Melakukan homing dan leveling gantry
    G32                            ; home all axes dan leveling gantry
    
    # Mengatur posisi absolut
    G90                            ; absolute positioning
    
    # Mengangkat extruder ke ketinggian 20 mm dari posisi awal
    G1 Z20 F3000                   ; move extruder away from initial position
```

**Penjelasan:**
- **Tujuan:** Menyiapkan printer untuk memulai pencetakan dengan memastikan semua sumbu berada pada posisi yang benar dan extruder berada pada ketinggian aman.
- **Perubahan:** Mengganti komentar dari "nozzle" menjadi "extruder" untuk mencerminkan perubahan fungsional pada konfigurasi Anda.

---

### 4. **[gcode_macro PRINT_END]**

```ini
[gcode_macro PRINT_END]
# Digunakan sebagai skrip akhir setelah pencetakan selesai - sesuaikan sesuai slicer pilihan Anda
gcode:
    # Menghitung posisi aman untuk menghindari masalah saat memarkir extruder
    {% set th = printer.toolhead %}
    {% set x_safe = th.position.x + (20 if (th.axis_maximum.x - th.position.x > 20) else -20) %}
    {% set y_safe = th.position.y + (20 if (th.axis_maximum.y - th.position.y > 20) else -20) %}
    {% set z_safe = min(th.position.z + 2, th.axis_maximum.z) %}
    
    # Menyimpan keadaan saat ini sebelum melakukan tindakan akhir
    SAVE_GCODE_STATE NAME=STATE_PRINT_END
    
    # Menunggu hingga semua perintah dalam buffer selesai
    M400                           ; wait for buffer to clear
    
    # Nonaktifkan kontrol suhu karena tidak diperlukan
    TURN_OFF_HEATERS               ; mematikan semua pemanas
    
    # Mengatur posisi absolut
    G90                            ; absolute positioning
    
    # Memindahkan extruder ke posisi aman untuk menghindari stringing
    G0 X{x_safe} Y{y_safe} Z{z_safe} F20000  ; move extruder to safe coordinates
    
    # Memarkir extruder ke posisi belakang area kerja
    G0 X{th.axis_maximum.x / 2} Y{th.axis_maximum.y - 2} F3600  ; park extruder at rear
    
    # Mematikan kipas pendingin
    M107                           ; turn off fan
    
    
    # Mengembalikan keadaan printer seperti sebelum PRINT_END
    RESTORE_GCODE_STATE NAME=STATE_PRINT_END MOVE=0
```

**Penjelasan:**
- **Tujuan:** Menyelesaikan proses pencetakan dengan memindahkan extruder ke posisi aman, mematikan komponen yang tidak diperlukan, dan memastikan printer siap untuk pencetakan berikutnya.
- **Perubahan Utama:**
  - **Menghapus Perintah Filament:** Karena Anda tidak menggunakan filament, perintah `G92 E0` dan `G1 E-5.0 F1800` dihapus.
  - **Menyesuaikan Komentar:** Mengubah istilah dari "nozzle" menjadi "extruder" untuk mencerminkan fungsionalitas yang telah dimodifikasi.
  - **Pengaturan Posisi Aman:** Memastikan extruder dipindahkan ke posisi yang aman berdasarkan batas maksimal yang telah dikonfigurasi (45 cm).
  - **Menghapus Kontrol Suhu:** Karena suhu tidak diperlukan, perintah terkait suhu dihilangkan atau dinonaktifkan.

---

## Catatan Tambahan

### 1. **Penggunaan Endstop Virtual**
- Dalam konfigurasi Anda, endstop fisik tidak digunakan. Sebagai gantinya, **endstop virtual** digunakan bersama dengan driver TMC2209.

### 2. **Kontrol Extruder**
- Karena extruder Anda digunakan untuk mengeluarkan mortar tanpa kontrol suhu, semua perintah terkait suhu dan pengaturan filament telah dihapus dari macros.
- Pastikan bahwa motor extruder diatur dengan benar untuk mengendalikan aliran mortar sesuai kebutuhan aplikasi Anda.

### 3. **Keamanan Gerakan**
- Pastikan bahwa kecepatan dan akselerasi yang digunakan dalam G-code macros sesuai dengan kemampuan mekanik printer Anda untuk mencegah kerusakan atau ketidaktepatan posisi.
- Selalu uji macros secara bertahap dan lakukan kalibrasi jika diperlukan untuk memastikan bahwa semua gerakan berjalan sesuai harapan.

### 4. **Penyesuaian Kecepatan dan Posisi**
- **Kecepatan Gerakan (`F` parameter):** Anda dapat menyesuaikan kecepatan gerakan (misalnya, `F3000` atau `F20000`) sesuai dengan kebutuhan spesifik dan kemampuan mekanik printer Anda.
- **Posisi Parkir:** Pastikan bahwa posisi parkir (`X`, `Y`, `Z`) aman dan tidak mengganggu bagian lain dari printer atau area kerja.

### 5. **Dokumentasi dan Backup**
- **Backup Konfigurasi:** Selalu simpan salinan cadangan dari konfigurasi dan G-code macros Anda sebelum melakukan perubahan besar.
- **Dokumentasi:** Dokumentasikan setiap perubahan yang Anda lakukan untuk referensi di masa depan atau untuk anggota tim lainnya.

---

## Sumber Referensi:
- [Klipper Documentation: G-Codes](https://www.klipper3d.org/G-Codes.html)
- [Klipper Documentation: Rotation Distance](https://www.klipper3d.org/Rotation_Distance.html)
- [Klipper Documentation: Stepper Motor](https://www.klipper3d.org/Steppers.html)


[https://github.com/AriqHB/3D-Printer-Core-XY-Modified/blob/main/Foto/PIN%20BTT%20SKR%20MINI%20E3%20V3.png]: https://github.com/AriqHB/3D-Printer-Core-XY-Modified/blob/main/Foto/PIN%20BTT%20SKR%20MINI%20E3%20V3.png