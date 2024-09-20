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
