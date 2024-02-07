# ukk_perpus_2024
## Step 10 System Koleksi Pribadi
Pada tahap ini kita akan menambahkan sistem koleksi pribadi, yang diruntukan untuk user dengan role peminjam. <br>
Sebelum mengikuti langkah - langkah pembuatan sistemnya, buka program di web browser dengan mengunjungi url berikut http://localhost/ukk_perpus_albi (Url disesuaikan dengan project masing - masing), kemudian login menggunakan data user yang role nya peminjam.
### 1. Menambahkan Menu Koleksi Pribadi
Pada folder templates, buka file header.php. Lalu cari potongan syntaks berikut.
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['perpustakaan', 'peminjaman']); ?>">
            <a href="#" class="nav-link <?= menuActive(['perpustakaan', 'peminjaman']); ?>">
```
Ganti kedua baris syntaks diatas menjadi seperti berikut
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['perpustakaan', 'peminjaman', 'koleksi']); ?>">
            <a href="#" class="nav-link <?= menuActive(['perpustakaan', 'peminjaman', 'koleksi']); ?>">
```
Masih didalam file header.php, tambahkan syntaks berikut tepat setelah menu Buku Pinjaman
```
/app/views/templates/header.php
<li class="nav-item">
                <a href="<?= urlTo('/koleksi'); ?>" class="nav-link <?= menuActive(['koleksi']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Koleksi Pribadi</p>
                </a>
              </li>
```
Silahkan coba jalankan program di web browser. Kalau berhasil, akan ada menu baru di sidebar seperti terlihat pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-10/img/101.PNG)<br>
Note : Jika tombol Koleksi Pribadi di klik, sistem akan mengembalikan response error. ( Jangan khawatir )
### 2. Menambahkan Controller
Didalam folder controllers, buat satu file baru dengan nama KoleksiController.php, lalu tuliskan syntaks berikut
```
/app/controllers/KoleksiController.php
<?php 
class KoleksiController extends Controller
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
    $data = $this->model('Koleksi')->get();
    $this->view('koleksi/home', $data);
  }

  public function store() 
  {
    if ($this->model('Koleksi')->create([
      'UserID'  => $_POST['UserID'],
      'BukuID'  => $_POST['BukuID']
    ]) > 0) {
      redirectTo('success', 'Selamat, Buku berhasil di tambahkan ke koleksi', '/koleksi');
    } else {
      redirectTo('error', 'Maaf, Buku gagal di tambahkan ke koleksi', '/perpustakaan');
    }
  }

  public function delete($id)
	{
		if ($this->model('Koleksi')->delete($id) > 0) {
			redirectTo('success', 'Selamat, Buku berhasil di hapus dari koleksi!', '/koleksi');
		}
	}
}
```
### 3. Menambahkan model
Didalam folder models, buat satu file baru beri nama Koleksi.php, lalu tuliskan syntaks berikut
```
/app/models/Koleksi.php
<?php 
class Koleksi extends BaseModel
{
  public $table_name    = "koleksipribadi";
  public $table_id      = "KoleksiID";

  public function get()
  {
    $id = $_SESSION['UserID'];
    $query = "SELECT * FROM koleksipribadi
    INNER JOIN buku ON koleksipribadi.BukuID = buku.BukuID
    INNER JOIN users ON koleksipribadi.UserID = users.UserID
    WHERE users.UserID = $id
    ORDER BY koleksipribadi.KoleksiID DESC
    ";

    $result = $this->mysqli->query($query);

    $data = [];
		while ($row = $result->fetch_assoc()) {
			$data[] = $row;
		}

		return $data;
  }
}
```
### 4. Menambahkan view
Didalam folder views buat satu folder baru beri nama koleksi, kemudian didalam koleksi tersebut buat satu file baru namanya home.php, lalu tuliskan syntaks berikut
```
/app/views/koleksi/home.php
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
                    <th>Judul Buku</th>
                    <th>Penulis</th>
                    <th>Penerbit</th>
                    <th>Tindakan</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data as $k): ?>
                    <tr>
                      <td><?= $no; ?></td>
                      <td><?= $k['Judul']; ?></td>
                      <td><?= $k['Penulis']; ?></td>
                      <td><?= $k['Penerbit']; ?></td>
                      <td>
                        <a href="<?= urlTo('/koleksi/'.$k['KoleksiID'].'/delete') ?>" class="btn btn-danger">
                          Delete
                        </a>
                      </td>
                    </tr>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>#</th>
                    <th>Judul Buku</th>
                    <th>Penulis</th>
                    <th>Penerbit</th>
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
Jangan lupa simpan semua perubahan datanya, kemudian coba jalankan di web browser dengan mengunjungi url berikut http://localhost/ukk_perpus_albi/koleksi (url disesuaikan dengan project masing - masing). Kalau berhasil, tampilannya akan terlihat seperti pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-10/img/102.PNG)<br>
### 5. Menambahkan tombol 
Sejauh ini kita sudah berhasil menambahkan menu dan juga view untuk menampilkan data koleksi pribadi. Tapi untuk menambahkan data pada sistem ini, kita harus menambahkan menu tombol baru di halaman [daftar buku](http://localhost/ukk_perpus_albi/perpustakaan) (Yang saat ini jumlah tombolnya hanya ada 2, yaitu detail dan pinjam). Untuk menambahkan tombol tersebut, didalam folder perpustakaan, buka file home.php, lalu tuliskan syntaks berikut tepat setelah tombol Pinjam.
```
/app/view/perpustakaan/home.php
<form action="<?= urlTo('/koleksi/store') ?>" method="post" class="d-inline">
                          <input type="hidden" name="BukuID" value="<?= $buku['BukuID']; ?>">
                          <input type="hidden" name="UserID" value="<?= $_SESSION['UserID']; ?>">
                          <button class="btn btn-success">
                            Masukan ke koleksi pribadi
                          </button>
                        </form>
```
Sekarang coba jalankan program di web broser dengan mengunjungi url berikut http://localhost/ukk_perpus_albi/perpustakaan (url di sesuaikan dengan project masing - masing), kalau berhasil, akan ada tombol baru seperti yang terlihat di lingkaran merah pada gambar dibawah.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-10/img/103.PNG)<br>
Silahkan coba klik tombol yang dilingkari merah pada gambar diatas, jika berhasil system akan redirect ke halaman http://localhost/ukk_perpus_albi/koleksi . Dan akan terlihat satu data baru.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-10/img/104.PNG)
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 9](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-9) | 
[Kembali ke step 11](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-11)
