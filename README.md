# ukk_perpus_2024
## Step 12 Halaman Peminjam Buku
Pada step 12 ini kita akan menambahkan halaman Peminjam buku, dimana halaman ini akan menampilkan data user yang meminjam buku.<br>
Agar bisa mengikuti step ini, silahkan login menggunakan user dengan role Administrator atau Petugas.
### 1. Menu Peminjam
Langkah pertama kita akan menambahkan menu peminjam terlebih dahulu, didalam folder templates, buka file header.php, kemudian tambahkan syntaks berikut tepat setelah menu buku
```
/app/views/templates/header.php
<li class="nav-item">
                <a href="<?= urlTo('/peminjam'); ?>" class="nav-link <?= menuActive(['peminjam']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Peminjam</p>
                </a>
              </li>
```
Masih didalam file header.php
Cari potongan syntaks berikut 
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['user', 'kategoribuku', 'buku']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku', 'buku']); ?>">
```
Ganti potongan syntaks code diatas dengan syntaks berikut
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['user', 'kategoribuku', 'buku', 'peminjam']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku', 'buku', 'peminjam']); ?>">
```
Sekarang coba jalankan program di web browser. Kalau berhasil, di sidebar akan ada menu baru (Peminjam) seperti terlihat pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-12/img/121.PNG)<br>
Note : Untuk sementara jangan klik tombol peminjam.
### 2. Controller
Untuk menambahkan controller, didalam folder controllers buat satu file baru beri nama PeminjamController.php, lalu tuliskan syntaks berikut
```
/app/controllers/PeminjamController.php
<?php 
class PeminjamController extends Controller
{
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

  public function index()
  {
    $data = $this->model('Peminjaman')->get();
    $this->view('peminjam/home', $data);
  }
}
```
### 3. Model
Sekarang, didalam folder models buka file Peminjam.php, lalu tambahkan method() berikut (Tempatkan sebelum penutup class)
```
/app/models/Peminjam.php
public function get()
  {
    $id = $_SESSION['UserID'];
    $result = $this->mysqli->query("
      SELECT * FROM peminjaman
      INNER JOIN users ON peminjaman.UserID = users.UserID
      INNER JOIN buku ON peminjaman.BukuID = buku.BukuID
    ");

    $data = [];
		while ($row = $result->fetch_assoc()) {
			$data[] = $row;
		}

		return $data;
  }
```
### 4. view
Terakhir, didalam folder views buat satu folder baru beri nama peminjam, kemudian didalam folder peminjam tersebut buat satu file baru dengan nama home.php, lalu tuliskan syntaks berikut
```
/app/views/peminjam/home.php
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
                    <th>Nama Peminjam</th>
                    <th>Alamat Peminjam</th>
                    <th>Buku Yang di Pinjam</th>
                    <th>Tanggal di Kembalikan</th>
                    <th>Status</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data as $k): ?>
                    <tr>
                      <td><?= $no; ?></td>
                      <td><?= $k['NamaLengkap']; ?></td>
                      <td><?= $k['Alamat']; ?></td>
                      <td><?= $k['Judul']; ?></td>
                      <td><?= $k['TanggalPengembalian']; ?></td>
                      <td><?= $k['StatusPeminjaman']; ?></td>
                    </tr>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>#</th>
                    <th>Nama Peminjam</th>
                    <th>Alamat Peminjam</th>
                    <th>Buku Yang di Pinjam</th>
                    <th>Tanggal di Kembalikan</th>
                    <th>Status</th>
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
Sekarang coba jalankan program di web browser dengan memasukan userl berikut http://localhost/ukk_perpus_albi/peminjam . Kalau berhasil tampilannya akan terlihat seperti gambar berikut
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-12/img/122.PNG)
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 11](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-11) | 
[Kembali ke step 13](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-13)
