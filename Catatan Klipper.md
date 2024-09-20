# Catatan Klipper Documentation 3D Printer Core XY Modifikasi Sumbu Z
1. Setiap menyalakan printer harus menyalakan wsl dan attach mikrokontroler pada command prompt
2. Untuk attach harus dilihat list nya apakah sudah shared apa belum lalu baru di attach dengan wsl sesuai dengan busid-nya. 
•	Pertama adalah cek `usbipd list` untuk melihat apa saja yang terhubung. Jika sudah shared USB Serial Device (COM_) untuk com tergantung dengan komputer masing-masing. Setelah itu, nyalakan wsl dengan `wsl -u (nama_linux)` dengan nama_linux disesuaikan dengan nama yang telah diatur di awal install linux.
•	Jika USB Serial Device telah shared dapat dilihat nomor busidnya. Langkah kedua dapat attach dengan `usbipd attach --wsl --busid=(nomor busid USB)` dimana nomor busid USB dapat dilihat dari usbipd list dan harus dengan status ‘shared’
3. Ketika sudah terhubung, cari ip address dengan menggunakan perintah `/sbin/ifconfig` pada komen 
4. Printer.cfg dan generic dipisah. Isi dari printer.cfg hanya memanggil mainsail.cfg, generic.cfg dan mcu nya dengan perintah `[include (mainsail/generic)]` seperti pada gambar dibawah. Pada serial mcu dapat disesuaikan sesuai dengan type mikrokontroler dan dicari pada perintah `ls /dev/serial/by-id/*` di command prompt.  Serta, serial mcu pada generic.cfg juga disesuaikan dengan yang di printer.cfg.
