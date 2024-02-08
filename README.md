# ukk_perpus_2024
## Step 14 Perbaikan halaman dashboard/home
Pada step ini kita akan memperbaiki tampilan dashboard/home, untuk mengikuti step ini kita bisa login menggunakan user dengan role apapun (Administrator, Petugas atau Peminjam.
### 1. Perbaiki function
Pertama, didalam folder core buka file functions.php, lalu perhatikan baris syntaks berikut
```
/core/functions.php
include '../core/Url.php';
include '../core/Controller.php';
include '../core/BaseModel.php';

$url = new Url();
```
Kemudian tambahkan syntaks berikut tepat dibawah/setelah variable $url diatas.
```
/core/functions.php
$db = new BaseModel();
```
Masih didalam file functions.php, sekarang tambahkan function berikut
```
/core/functions.php
function hitung($table)
{
	global $db;
	$result = $db->mysqli->query("SELECT * FROM ".$table);

	return $result->num_rows;
}
```
### 2. Perbaiki tampilan home
Didalam folder views, buka file home.php, kemudian ganti semua isinya dengan syntaks berikut
```
/app/views/home.php
<?php include '../app/views/templates/header.php'; ?>
<div class="container-fluid">
<div class="row">
          <div class="col-lg-3 col-6">
            <!-- small box -->
            <div class="small-box bg-info">
              <div class="inner">
                <p>Buku</p>
                <h3><?= hitung('buku'); ?></h3>

              </div>
              <div class="icon">
                <i class="ion ion-bag"></i>
              </div>
            </div>
          </div>
          <!-- ./col -->
          <div class="col-lg-3 col-6">
            <!-- small box -->
            <div class="small-box bg-success">
              <div class="inner">
                <p>Kategori Buku</p>
                <h3><?= hitung('kategoribuku'); ?></h3>

              </div>
              <div class="icon">
                <i class="ion ion-stats-bars"></i>
              </div>
            </div>
          </div>
          <!-- ./col -->
          <div class="col-lg-3 col-6">
            <!-- small box -->
            <div class="small-box bg-warning">
              <div class="inner">
                <p>Buku Yang Dipinjam</p>
                <h3><?= hitung('peminjaman'); ?></h3>

              </div>
              <div class="icon">
                <i class="ion ion-person-add"></i>
              </div>
            </div>
          </div>
          <!-- ./col -->
          <div class="col-lg-3 col-6">
            <!-- small box -->
            <div class="small-box bg-danger">
              <div class="inner">
                <p>User</p>
                <h3><?= hitung('users'); ?></h3>

              </div>
              <div class="icon">
                <i class="ion ion-pie-graph"></i>
              </div>
            </div>
          </div>
          <!-- ./col -->
        </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Silahkan coba jalankan program di web browser dengan mengunjungi halaman home http://localhost/ukk_perpus_albi/ (url disesuaikan dengan project masing - masing). Kalau berhasil tampilannya akan terlihat seperti pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-14/img/134.PNG)
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 13](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-13) | 
[Kembali ke step 15](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-15)
