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
Untuk menambahkan Controller Perpustakaan, Didalam folder controllers buat satu file baru beri nama PerpustakaanController.php, kemudian tuliskan syntaks berikut
```
/app/controllers/PerpustakaanController.php
<?php 
class PerpustakaanController extends Controller
{
  public function __construct()
  {
    /**
      * Batasi hak akses hanya untuk peminjam
      * Selain peminjam akan langsung diarahkan kembali ke halaman home
    */
    if ($_SESSION['role'] !== 'Peminjam') {
      redirectTo('error', 'Mohon maaf, Anda tidak berhak mengakses halaman ini', '/');
    }
  }

  public function index()
  {
    $data = $this->model('KBRelasi')->get();
    $this->view('perpustakaan/home', $data);
  }

  public function detailbuku($id)
  {
    $this->view('perpustakaan/buku', [
      'buku'    => $this->model('Buku')->getById($id),
      'ulasan'  => $this->model('UlasanBuku')->getByBookID($id)
    ]);
  }

  public function ulasanbuku($id)
  {
    $data = $this->model('Buku')->getById($id);
    $this->view('perpustakaan/ulasan', $data);
  }

  public function ulasanstore()
  {
    if ($this->model('Ulasanbuku')->create([
      'UserID'      => $_POST['UserID'],
      'BukuID'      => $_POST['BukuID'],
      'Ulasan'      => $_POST['Ulasan'],
      'Rating'      => $_POST['Rating'],
    ]) > 0) {
      redirectTo('success', 'Selamat, Ulasan Berhasil di Tambahkan', '/perpustakaan/'.$_POST['BukuID'].'/detailbuku');
    } else {
      redirectTo('error', 'Maaf, Ulasan gagal di Tambahkan', '/perpustakaan/'.$_POST['BukuID'].'/detailbuku');
    }
  }
}
```
## 4. Menambahkan view home perpustakaan
Diatas kita sudah menambahkan Controller untuk menangani perpustakaan, agar controller tersebut berjalan dengan lancar kita harus menambahkan view home.<br>
Didalam folder views, buat folder baru dengan nama perpustakaan. Selanjutnya, didalam folder yang baru saja kalian buat tersebut buat satu file baru dengan nama home.php, kemudian tuliskan syntaks berikut.
```
/app/views/perpustakaan/home.php
<?php include '../app/views/templates/header.php'; $no = 1; ?>
<div class="container-fluid">
        <div class="row">
          <div class="col-12">
            <div class="card">
              <div class="card-body">
                <table id="example1" class="table table-bordered table-striped">
                  <thead>
                  <tr>
                    <th>#</th>
                    <th>Kategori</th>
                    <th>Judul</th>
                    <th>Penulis</th>
                    <th>Tindakan</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data as $buku): ?>
                  	<tr>
                  		<td><?= $no; ?></td>
                  		<td><?= $buku['NamaKategori']; ?></td>
                  		<td><?= $buku['Judul']; ?></td>
                  		<td><?= $buku['Penulis']; ?></td>
                      <td>
                        <a 
                          href="<?= urlTo('/perpustakaan/'.$buku['BukuID'].'/detailbuku') ?>"
                          class="btn btn-info
                          ">
                          Detail
                        </a>
                      </td>
                  	</tr>
                  	<?php $no++; ?>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>#</th>
                    <th>Kategori</th>
                    <th>Judul</th>
                    <th>Penulis</th>
                    <th>Tindakan</th>
                  </tr>
                  </tfoot>
                </table>
              </div>
              <!-- /.card-body -->
            </div>
            <!-- /.card -->
          </div>
          <!-- /.col -->
        </div>
        <!-- /.row -->
      </div>
<?php include '../app/views/templates/footer.php'; ?>
```
Coba jalankan program di web browser dengan memasukan url berikut, [http://localhost/ukk_perpus_albi/](http://localhost/ukk_perpus_albi/perpustakaan) ( Sesuaikan url nya dengan project masing - masing ). Kalau berhasil, maka tampilannya akan terlihat seperti gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/85.PNG) <br>
Coba perhatikan, digambar tersebut ada satu data buku lengkap dengan kategorinya, kalau di project kalian belum terdapat data sama sekali, silahkan lihat lagi [step-6](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-6) untuk menambahkan kategori buku, dan [step-7](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-7) untuk menambahkan data buku.
## 5. Menambahkan model ulasan buku
Pada gambar diatas ada tombol detail buku, dimana tombol tersebut akan mengarahkan kita ke halaman detail buku dan juga tempat user untuk menambahkan ulasan terhadap buku tertentu. Kalau sekarang kalian coba klik tombol ( detail ) tersebut, maka response yang dikembalikan adalah error ( Gagal membuka file ../app/models/UlasanBuku.php ).<br>
Untuk menangani error tersebut kita harus menambahkan model baru. Didalam folder models, buat satu file baru beri nama UlasanBuku.php, kemudian tuliskan syntaks berikut
```
/app/models/UlasanBuku.php
<?php 
class Ulasanbuku extends BaseModel
{
  public $table_name  = "ulasanbuku";
  public $table_id    = "UlasanID";

  public function getByBookID($id)
  {
    $result = $this->mysqli->query("
      SELECT * FROM ulasanbuku
      INNER JOIN users ON ulasanbuku.UserID = users.UserID
      WHERE ulasanbuku.BukuID = '$id'
    ");

    $data = [];
		while ($row = $result->fetch_assoc()) {
			$data[] = $row;
		}

		return $data;

  }
}
```
## 6. Menambahkan view detail buku
Pada langkah sebelumnya kita sudah membuat model untuk menangani data detail buku, selanjutnya kita tambahkan view nya.<br>
Didalam folder perpustakaan, buat satu file baru beri nama buku.php, kemudian tuliskan syntaks berikut
```
/app/views/perpustakaan/buku.php
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

                  <a href="<?= urlTo('/perpustakaan'); ?>"
                  class="btn btn-danger btn-block">
                    <b>Kembali</b>
                  </a>
                  <a href="<?= urlTo('/perpustakaan/'.$data['buku']['BukuID'].'/ulasanbuku'); ?>" 
                  class="btn btn-success btn-block">
                    <b>Berikan Ulasan</b>
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
Sekarang coba jalankan lagi di web browser, dengan memasukan url berikut http://localhost/ukk_perpus_albi/perpustakaan , Kemudian klik tombol detail seperti yang ditunjukan pada gambar langkah 4 diatas. Kalau berhasil, tampilannya akan terlihat seperti gambar berikut.
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/86.PNG) <br>
Dari kedua tombol yang ada pada gambar diatas ( Kembali dan Berikan Ulasan ) untuk saat ini yang berfungsi hanya tombol kembali, coba kalian klik tombol kembali, kalau tidak ada error berarti sudah benar.<br>
Tapi kalau kalian coba klik tombol berikan ulasan, maka response nya adalah error, yang kurang lebih pesan error nya seperti berikut ( Warning: include(../app/views/perpustakaan/ulasan.php): Failed to open stream: No such file or directory in ). Sudah ketebak error nya karena apa kan? Yups betul, error tersebut karena gagal membuka file yang bernama ulasan.php didalam folder perpustakaan.<br>
## 7. Menambahkan view ulasan
Seperti yang terlihat diatas, saat ini program memberikan response error, untuk menangani error tersebut buat file baru beri nama ulasan.php didalam folder perpustakaan, kemudian tuliskan syntaks berikut
```
/app/views/perpustakaan/ulasan.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/perpustakaan/ulasanstore'); ?>" method="post">
        <input type="hidden" name="UserID" value="<?= $_SESSION['UserID']; ?>">
        <input type="hidden" name="BukuID" value="<?= $data['BukuID']; ?>">
        <div class="form-group">
          <label for="Judul">Judul Buku</label>
          <input type="text" id="Judul" class="form-control"
          value="<?= $data['Judul'] ?>" readonly>
        </div>

        <div class="form-group">
          <label for="Penulis">Penulis</label>
          <input type="text" id="Penulis" class="form-control" 
          value="<?= $data['Penulis'] ?>" readonly>
        </div>

        <div class="form-group">
          <label for="Ulasan">Ulasan</label>
          <input type="text" id="Ulasan" name="Ulasan" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Rating">Rating</label>
          <select name="Rating" id="Rating" class="form-control">
            <option value="5">5</option>
            <option value="4">4</option>
            <option value="3">3</option>
            <option value="2">2</option>
            <option value="1">1</option>
          </select>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/buku'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Tambah Ulasan</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Sekarang coba jalankan lagi di web browser, kalau berhasil, tampilannya kurang lebih akan terlihat seperti berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/87.PNG)<br>
Coba berikan ulasan dengan mengisi formulir pada kolom ulasan dan rating, kemudian klik tombol "Tambah Ulasan". Kalau berhasil, kalian akan diarahkan kembali ke halaman detail buku, yang kurang lebih tampilannya akan terlihat seperti gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/88.PNG)<br>
Coba perhatikan lingkaran merah pada gambar diatas, itu merupakan data ulasan yang baru saja kalian input.<br>
Selamat, pembuatan sistem ulasan sudah berhasil.
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
[Kembali ke step 6](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-7) | 
[Lanjut ke step 8](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-9)
