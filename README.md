# ukk_perpus_2024
## Step 11 Perbaikan hak akses
Sejauh ini aplikasi yang kita bangun memang sudah berjalan. Tapi masalahnya, beberapa fitur bisa diakses oleh user dengan role yang bukan hak-nya.<br>
Contohnya halaman http://localhost/ukk_perpus_albi/user (Coba login menggunakan user dengan role peminjam dan petugas). Kedua user dengan role tersebut harusnya tidak bisa membuka halaman ini. Karena yang bisa mengakses halaman ini harusnya user dengan role Administrator.
### 1. Halaman user
Agar halaman user hanya bisa diakses oleh user dengan role Administrator, kita harus menambahkan fungsi untuk memfilter-nya. Sekarang, didalam folder controllers, buka file UserController.php, kemudian tambahkan method construct() berikut sebelum method index().
```
/app/constrollers/UserController.php
public function __construct()
  {
    /**
      * Batasi hak akses hanya untuk Administrator
      * Selain Administrator akan langsung diarahkan kembali ke halaman home
    */
    if ($_SESSION['role'] !== 'Administrator') {
      redirectTo('error', 'Mohon maaf, Anda tidak berhak mengakses halaman ini', '/');
    }
  }
```
Sekarang coba buka lagi url berikut http://localhost/ukk_perpus_albi/user (Login sebagai petugas atau peminjam). Kalau berhasil, system akan otomatis redirect ke halaman home dengan merespon pesan seperti terlihat pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-11/img/111.png)
### 2. Halaman Kategori buku
Halaman [http://localhost/ukk_perpus_albi/buku](http://localhost/ukk_perpus_albi/kategoribuku) hanya boleh diakses oleh user dengan role Administrator dan Petugas saja, Peminjam tidak diperbolehkan. Untuk memperbaikinya, silahkan buka file KategoribukuController.php, lalu tambahkan method construct() berikut tepat sebelum method index().
```
/app/controllers/KategoribukuController.php
public function __construct()
  {
    /**
      * Batasi hak akses hanya untuk Administrator dan Petugas
      * Selain Administrator dan Petugas akan langsung diarahkan kembali ke halaman home
    */
    if ($_SESSION['role'] !== 'Administrator' && $_SESSION['role'] !== 'Petugas') {
      redirectTo('error', 'Mohon maaf, Anda tidak berhak mengakses halaman ini', '/');
    }
  }
```
Jika mengakses halaman ini http://localhost/ukk_perpus_albi/buku dan kalian login sebagai peminjam, harusnya sistem otomatis redirect ke halaman home dengan meresponse pesan seperti pada gambar langkah 1 diatas.
### 3. Halaman Buku
Halaman http://localhost/ukk_perpus_albi/buku hanya boleh diakses oleh user dengan role Administrator dan Petugas saja, Peminjam tidak diperbolehkan. Untuk memperbaikinya, silahkan buka file BukuController.php, lalu tambahkan method construct() berikut tepat sebelum method index().
```
/app/controllers/BukuController.php
public function __construct()
  {
    /**
      * Batasi hak akses hanya untuk Administrator dan Petugas
      * Selain Administrator dan Petugas akan langsung diarahkan kembali ke halaman home
    */
    if ($_SESSION['role'] !== 'Administrator' && $_SESSION['role'] !== 'Petugas') {
      redirectTo('error', 'Mohon maaf, Anda tidak berhak mengakses halaman ini', '/');
    }
  }
```
Jika mengakses halaman ini http://localhost/ukk_perpus_albi/buku dan kalian login sebagai peminjam, harusnya sistem otomatis redirect ke halaman home dengan meresponse pesan seperti pada gambar langkah 1 diatas.
### 4. Menu header
Terakhir, kita perbaiki menu sidebar. Didalam folder templates, buka file header.php. Perhatikan syntaks berikut
```
/app/views/templates/header.php
<?php if ($_SESSION['role'] === 'Administrator'): ?>
          <li class="nav-item <?= menuOpen(['user', 'kategoribuku', 'buku']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku', 'buku']); ?>">
              <i class="nav-icon fas fa-save"></i>
              <p>
                Data
                <i class="right fas fa-angle-left"></i>
              </p>
            </a>
            <ul class="nav nav-treeview">
              <li class="nav-item">
                <a href="<?= urlTo('/user'); ?>" class="nav-link <?= menuActive(['user']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>User</p>
                </a>
              </li>

              <li class="nav-item">
                <a href="<?= urlTo('/kategoribuku'); ?>" class="nav-link <?= menuActive(['kategoribuku']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Kategori Buku</p>
                </a>
              </li>

              <li class="nav-item">
                <a href="<?= urlTo('/buku'); ?>" class="nav-link <?= menuActive(['buku']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Buku</p>
                </a>
              </li>
            </ul>
          </li>
          <?php endif ?>
```
Sekarang ganti potongan syntaks diatas dengan syntaks berikut ini
```
/app/views/templates/header.php
<?php if ($_SESSION['role'] === 'Administrator' || $_SESSION['role'] === 'Petugas'): ?>
          <li class="nav-item <?= menuOpen(['user', 'kategoribuku', 'buku']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku', 'buku']); ?>">
              <i class="nav-icon fas fa-save"></i>
              <p>
                Data
                <i class="right fas fa-angle-left"></i>
              </p>
            </a>
            <ul class="nav nav-treeview">
            <?php if ($_SESSION['role'] === 'Administrator'): ?>
              <li class="nav-item">
                <a href="<?= urlTo('/user'); ?>" class="nav-link <?= menuActive(['user']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>User</p>
                </a>
              </li>
            <?php endif ?>

              <li class="nav-item">
                <a href="<?= urlTo('/kategoribuku'); ?>" class="nav-link <?= menuActive(['kategoribuku']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Kategori Buku</p>
                </a>
              </li>

              <li class="nav-item">
                <a href="<?= urlTo('/buku'); ?>" class="nav-link <?= menuActive(['buku']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Buku</p>
                </a>
              </li>
            </ul>
          </li>
          <?php endif ?>
```
Coba jalankan program di web browser dan login sebagai Administrator, fokus ke menu sidebar, jika sudah benar, harusnya terlihat seperti gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-11/img/112.PNG)<br>
Sekarang coba login sebagai Petugas, maka tampilan menu sidebar akan terlihat seperti pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-11/img/113.PNG)<br>
Dan kalau sekarang kalian coba akses halaman http://localhost/ukk_perpus_albi/user harusnya sistem langsung redirect ke halaman home, dan mengirimkan pesan seperti gambar pada langkah 1 diatas.
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 10](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-10) | 
[Kembali ke step 12](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-12)
