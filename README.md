# ukk_perpus_2024
## Step 8 ( Peminjam ) Crud Ulasan Buku
Pada langkah ini kita akan menambahkan fitur ulasan buku, dimana pemberi ulasan buku ini merupakan akun yang menggunakan role peminjam. Jika saat ini kalian login sebagai administrator atau petugas, silahkan logout terlebih dahulu, kemudian login kembali menggunakan akun peminjam yang sudah kalian daftarkan di step sebelumnya.<br>
Kalau kalian belum membuat akun peminjam, silahkan ikuti langkah - langkah berikut ini.
## 1. Membuat akun dengan role peminjam
Silahkan jalankan program di web browser, jika saat ini kalian sudah login sebagai admin atau petugas, silahkan logout terlebih dahulu
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/81.PNG)<br>
Jika sudah logout, kalian akan diarahkan ke halaman login seperti gambar diatas, untuk mengakses halaman pendaftaran/registrasi, klik link "Belum punya akun ?" yang ditunjukan dengan lingkaran merah pada gambar diatas. Selanjutnya kalian akan diarahkan ke halaman register seperti yang ditunjukan pada gambar dibawah.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/82.PNG) <br>
Silahkan isi formulir dengan lengkap, boleh juga mengikuti pengisian formulir seperti gambar diatas
#### username             : peminjam1
#### email                : peminjam1@gmail.com
#### nama lengkap         : Suci Aulia Agniani ( Boleh disesuaikan dengan nama masing - masing ) 
#### alamat               : nangkelan
#### password             : 123
#### konfirmasi password  : 123
Catatan : Memasukan username atau email yang sudah terdaftar di database akan mengakibatkan error, karena duplikat data.<br>
Kalau formulir sudah di isi dengan lengkap, silahkan klik tombol daftar seperti yang ditunjukan lingkaran satu pada gambar diatas. Kalau sudah berhasil, silahkan login menggunakan data yang baru saja didaftarkan. Untuk mengakses halaman login, silahkan klik link "Sudah punya akun?" seperti yang ditunjukan oleh lingkaran 2 pada gambar diatas.
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/83.PNG) <br>
Jika sudah masuk kehalaman home seperti gambar diatas, sejauh ini kalian sudah berhasil login menggunakan akun dengan role peminjam.<br>
Coba perhatikan pada menu sidebar diatas, jika sebelumnya login menggunakan akun administrator terlihat menu data, maka fitur data tersebut akan disembunyikan ketika yang login adalah akun dengan role peminjam. Inilah yang disebut hak akses. <br>
Untuk memastikan fitur hak akses sudah berjalan dengan benar, silahkan logout kemudian login kembali menggunakan akun administrator, lihat dibagian sidebar apakah ada menu data?.<br>
Ok lanjut, silahkan logout kemudian login lagi menggunakan akun peminjam yang didaftarkan diatas.
## 2. Menambahkan menu perpustakaan
Untuk menambahkan menu perpustakaan, di folder templates buka file header.php, kemudian tambahkan syntaks berikut tepat sebelum menu logout ( Perhatikan dengan benar batas dari menu logout ) 
```
/app/views/templates/header.php
<?php if ($_SESSION['role'] === 'Peminjam'): ?>
          <li class="nav-item <?= menuOpen(['perpustakaan']); ?>">
            <a href="#" class="nav-link <?= menuActive(['perpustakaan']); ?>">
              <i class="nav-icon fas fa-save"></i>
              <p>
                Perpustakaan
                <i class="right fas fa-angle-left"></i>
              </p>
            </a>
            <ul class="nav nav-treeview">
              <li class="nav-item">
                <a href="<?= urlTo('/perpustakaan'); ?>" class="nav-link <?= menuActive(['perpustakaan']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Daftar Buku</p>
                </a>
              </li>
            </ul>
          </li>
          <?php endif ?>
```
Silahkan coba jalankan program di web browser, kalau menu sidebar terlihat seperti gambar dibawah berarti sudah berhasil.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/84.PNG) <br>
Kalau kalian coba klik menu daftar buku, response yang dikembalikan adalah error, karena kita belum menambahkan Controller perpustakaan
## 3. Menambahkan Controller Perpustakaan


### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
[Kembali ke step 6](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-6) | 
[Lanjut ke step 8](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-8)
