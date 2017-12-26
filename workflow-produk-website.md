# _Workflow_ Pengolahan Gambar Produk di Website

Ada beberapa langkah yang harus dilakukan, yaitu :

1. Copy gambar dari kamera. Gunakan yang JPG.
2. Menggunakan Raw Therapee, :
   
   - Aktifkan auto levels untuk semua gambar.
   - Atur _Color Balance_ :
   
     - Temperature 6490
     - Tint 1.002
     - Blue/Red 0.98

3. Upload ke [Clipping Magic](https://clippingmagic.com/), lalu hapus background. Setiap gambar dilakukan _clipping_ sebanyak dua kali :

   - Satu gambar dihapus backgroundnya, tapi body manekin tetap ada. Selanjutnya gambar ini disebut **BG**.
   - Satu dihapus seluruh background selain gambar produk. Selanjutnya gambar ini disebut **FG**.

4. Download hasil _clipping_.
5. Hapus warna **BG** dan cerahkan menggunakan perintah :

   ```
   mogrify -set colorspace RGB -colorspace Gray -level 0%,100%,1.3 -path ../bg *.png
   ```

6. Gabung **FG** dan **BG** :

   ```
   convert bg/01.png fg/01.png -flatten 01.png
   ```

7. Resize ke ukuran 1100x1100 dalam format JPG :

   ```
   mogrify -resize '1100x1100>' -format jpg -path ../img *.png
   ```

8. Kompres gambar JPG :

   ```
   jpegoptim -m 90 --all-progressive *.jpg
   ```

9. Buat thumbnail dengan ukuran 400x400 :

   ```
   mogrify -resize '400x400>' -path ../thumb *.jpg
   ```

10. Upload ke server :

    ```
    rsync --update -razP * [IP]:website
    ```
