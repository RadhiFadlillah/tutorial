# Tutorial Cross Compile go-sqlcipher

Dalam tutorial ini dilakukan _cross compile_ aplikasi Go yang menggunakan package go-sqlcipher dari Ubuntu 64-bit ke Windows.

1. Install `mingw`

   ```bash
   sudo apt install gcc-mingw-w64
   ```

   Pada Ubuntu 16.04, proses ini akan menginstall library 64-bit di `/usr/x86_64-w64-mingw32` dan library 32-bit di `/usr/i686-w64-mingw32`.

2. Download source code untuk OpenSSL 1.0.2j di [sini](https://www.openssl.org/source/), lalu ekstrak. Gunakan OpenSSL versi 1.0.2 sebab versi inilah yang digunakan oleh sqlcipher.

3. _Cross compile_ OpenSSL untuk Windows 32-bit

   Di terminal, masuk ke folder hasil ekstrak tadi, lalu jalankan script `configure` agar menggunakan compiler Windows 32-bit :

   ```bash
   ./Configure mingw shared --cross-compile-prefix=i686-w64-mingw32- --prefix=/usr/i686-w64-mingw32
   ```

   Setelah itu, jalankan perintah `make` dan `sudo make install` di terminal. Setelah installasi selesai, kita dapat melakukan _cross compile_ ke Windows 32-bit menggunakan perintah :

   ```bash
   env CGO_ENABLED=1 GOOS=windows GOARCH=386 CC=i686-w64-mingw32-gcc go build
   ```

4. _Cross compile_ OpenSSL untuk Windows 64-bit

   Masuk ke folder hasil ekstrak, lalu jalankan script `configure` agar menggunakan compiler Windows 64-bit :

   ```bash
   ./Configure mingw64 shared --cross-compile-prefix=x86_64-w64-mingw32- --prefix=/usr/x86_64-w64-mingw32
   ```

   Jalankan perintah `make` dan `sudo make install` di terminal. Setelah installasi selesai, kita dapat melakukan _cross compile_ ke Windows 32-bit menggunakan perintah :

   ```bash
   env CGO_ENABLED=1 GOOS=windows GOARCH=amd64 CC=x86_64-w64-mingw32-gcc go build
   ```
