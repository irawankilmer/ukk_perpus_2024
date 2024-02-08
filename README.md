# ukk_perpus_2024
## Step 13 Menambahkan halaman ulasan buku
Pada step ini kita akan menambahkan ulasan buku pada halaman buku. Agar bisa mengikuti step ini, silahkan login menggunakan user dengan role Administrator atau petugas. Kemudian buka url berikut http://localhost/ukk_perpus_albi/buku
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-13/img/131.PNG)<br>
Coba perhatikan lingkaran merah pada gambar diatas, saat ini halaman tersebut hanya memiliki 2 tombol. Nah untuk menampilkan ulasan buku, nanti kita akan buat tombol untuk mengakses halaman ulasan buku tersebut
### 1. Menambahkan Tombol Lihat Ulasan
Untuk menambahkan tombol lihat ulasan, didalam folder buku, buka file home.php, lalu tuliskan syntaks berikut tepat dibawah tombol delete.
```
/app/views/buku/home.php
<a 
                          href="<?= urlTo('/buku/'.$buku['BukuID'].'/ulasan') ?>"
                          class="btn btn-info
                          ">
                          Lihat Ulasan
                        </a>
```
Silahkan coba jalankan program di web browser dengan mengunjungi url berikut http://localhost/ukk_perpus_albi/buku (url di sesuaikan dengan project masing - masing). Kalau berhasil, akan ada satu tombol baru seperti yang di perlihatkan oleh lingkaran merah pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-13/img/132.PNG)<br>
Note : untuk sementara jangan di klik tombol "lihat ulasan"
### 2. Menambahkan method ulasan()
Didalam folder controllers, buka file BukuController.php, lalu tambahkan syntaks berikut (Simpan sebelum penutup class)
```
/app/controllers/BukuController.php
public function ulasan($id)
  {
    $this->view('buku/ulasan', [
      'buku'    => $this->model('Buku')->getById($id),
      'ulasan'  => $this->model('Ulasanbuku')->getByBookId($id)
    ]);
  }
```
### 3. Menambahkan view ulasan
Terakhir, kita akan menambahkan view untuk menampilkan data ulasan. Didalam folder buku, buat satu file baru beri nama ulasan.php, kemudian tuliskan syntaks berikut
```
/app/views/buku/ulasan.php
<?php include '../app/views/templates/header.php'; ?>
<div class="container-fluid">
        <div class="row">
          <div class="col-md-3">

            <!-- Profile Image -->
            <div class="card card-primary card-outline">
              <div class="card-body box-profile">
                <h3 class="profile-username text-center"><?= $data['buku']['Judul']; ?></h3>
                <ul class="list-group list-group-unbordered mb-3">
                  <li class="list-group-item">
                    <b>Penulis</b> 
                    <label class="badge badge-info float-right"><?= $data['buku']['Penulis']; ?></label>
                  </li>
                  <li class="list-group-item">
                    <b>Penerbit</b> 
                    <label class="badge badge-info float-right"><?= $data['buku']['Penerbit']; ?></label>
                  </li>
                  <li class="list-group-item">
                    <b>TahunTerbit</b> 
                    <label class="badge badge-info float-right"><?= $data['buku']['TahunTerbit']; ?></label>
                  </li>

                  <a href="<?= urlTo('/buku'); ?>"
                  class="btn btn-danger btn-block">
                    <b>Kembali</b>
                  </a>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
            <!-- /.card -->
          </div>
          <!-- /.col -->
          <div class="col-md-9">
            <div class="card card-primary card-outline">
            	<div class="card-header">
            		<h4>Ulasan</h4>
            	</div>

            	<div class="card-body">
            		<table id="example1" class="table table-bordered table-striped">
                  <thead>
                  <tr>
                    <th>Ulasan</th>
                    <th>Rating</th>
                    <th>Pemberi Ulasan</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data['ulasan'] as $ulasan): ?>
                    <tr>
                      <td><?= $ulasan['Ulasan']; ?></td>
                      <td><?= $ulasan['Rating']; ?></td>
                      <td><?= $ulasan['NamaLengkap']; ?></td>
                    </tr>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>Ulasan</th>
                    <th>Rating</th>
                    <th>Pemberi Ulasan</th>
                  </tr>
                  </tfoot>
                </table>
            	</div>
            </div>
          </div>
          <!-- /.col -->
        </div>
        <!-- /.row -->
      </div><!-- /.container-fluid -->
<?php include '../app/views/templates/footer.php'; ?>
```
Sekarang coba jalankan lagi program di web browser dengan mengunjungi halaman berikut http://localhost/ukk_perpus_albi/buku (url disesuaikan dengan project masing - masing). Kalau berhasil, kurang lebih tampilannya akan terlihat seperti pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-13/img/133.PNG)
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 12](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-12) |
[Kembali ke step 14](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-14)
