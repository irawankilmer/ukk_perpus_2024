# ukk_perpus_2024
## Step 9 ( peminjam ) Sistem Pinjam Buku
Pada langkah sebelumnya kita sudah berhasil menambahkan sistem ulasan buku, dimana user yang bisa memberikan ulasan buku adalah user dengan role peminjam. Masih dengan role peminjam, sekarang kita akan menambahkan fitur pinjam buku, saya asumsikan step sebelumnya sudah berjalan/tidak ada error.
### 1. Tambah Tombol Pinjam
Kalau saat ini kalian sudah menutup project nya, silahkan jalankan kembali dengan mengunjungi url berikut http://localhost/ukk_perpus_albi/perpustakaan (Url sesuaikan dengan project masing - masing). Ingat, disini kalian harus login sebagai peminjam. Kalau user dengan role peminjam sudah dihapus, kalian bisa menambahkannya lagi dengan melihat link berikut [step-8](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-8) .<br>
Didalam folder perpustakaan, buka file home.php, kemudian tambahkan syntaks berikut tepat dibawah tombol detail ( Perhatikan scope tiap tombol )
```
/app/views/perpustakaan/home.php
<form action="<?= urlTo('/peminjaman/store') ?>" method="post" class="d-inline">
                          <input type="hidden" name="BukuID" value="<?= $buku['BukuID']; ?>">
                          <button class="btn btn-primary">
                            Pinjam
                          </button>
                        </form>
```
Silahkan coba lagi jalankan di web browser dengan mengunjungi url berikut http://localhost/ukk_perpus_albi/perpustakaan (Url disesuaikan dengan project masing - masing). Kalau berhasil, kurang lebih tampilannya akan terlihat seperti pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-8/img/pinjma.PNG)<br>
Coba perhatikan gambar diatas, sekarang ada satu tombol baru ( Pinjam ). Coba klik tombol ( Pinjam ), maka response yang dikembalikan adalah error dengan pesan yang kurang lebih seperti berikut ( Warning: include(../app/controllers/PeminjamanController.php): Failed to open stream: No such file or directory in ). Jangan khawatir, error tersebut muncul karena kita belum menambahkan controller untuk menangani peminjaman, di langkah berikutnya akan dijelaskan
### 2. Menambahkan Controller peminjaman
Untuk menangani error pada langkah sebelumnya, didalam folder controllers buat satu file baru beri nama PeminjamanController.php, kemudian tuliskan syntaks berikut
```
/app/controllers/PeminjamanController.php
<?php 
class PeminjamanController extends Controller
{
  public function index()
  {
    $data = $this->model('Peminjaman')->getPinjam();
    $this->view('peminjaman/home', $data);
  }

  public function pinjam($id)
  {
    $data = $this->model('Buku')->getById($id);
    $this->view('peminjaman/pinjam', $data);
  }

  public function store()
  {
    if ($this->model('Peminjaman')->create([
      'UserID'              => $_SESSION['UserID'],
      'BukuID'              => $_POST['BukuID'],
      'TanggalPeminjaman'   => date('Y-m-d'),
      'StatusPeminjaman'    => 'Belum di Kembalikan'
    ]) > 0) {
      redirectTo('success', 'Selamat, Buku berhasil di pinjam', '/peminjaman');
    } else {
      redirectTo('error', 'Maaf, Buku gagal di pinjam', '/peminjaman');
    }
  }

  public function kembalikan($id)
  {
    if ($this->model('Peminjaman')->update($id) > 0) {
			redirectTo('success', 'Selamat, Buku berhasil di kembalikan!', '/peminjaman');
		} else {
			redirectTo('error', 'Maaf, Buku gagal di kembalikan!', '/peminjaman');
		}
  }
}
```
### 3. Menambahkan model peminjaman
Pada langkah sebelumnya kita sudah menambahkan controller untuk menangani peminjaman, tapi kalau kalian coba jalankan lagi program tersebut, response yang dikembalikan akan tetap error, karena controller tersebut membutuhkan model untuk menangani peminjaman yang akan kita buat sekarang.<br>
Didalam folder models, buat satu file baru dengan nama Peminjaman.php, lalu tuliskan syntaks berikut
```
/app/models/Peminjaman.php
<?php 
class Peminjaman extends BaseModel
{
  public $table_name    = "peminjaman";
  public $table_id      = "PeminjamanID";

  public function getPinjam()
  {
    $id = $_SESSION['UserID'];
    $result = $this->mysqli->query("
      SELECT * FROM peminjaman
      INNER JOIN users ON peminjaman.UserID = users.UserID
      INNER JOIN buku ON peminjaman.BukuID = buku.BukuID
      WHERE peminjaman.UserID = $id
    ");

    $data = [];
		while ($row = $result->fetch_assoc()) {
			$data[] = $row;
		}

		return $data;
  }
}
```
Kalau sekarang kalian coba jalankan lagi programnya akan tetap mengembalikan error, walaupun datanya sudah berhasil masuk ke database. Pada saat kita berhasil menambahkan data, maka program akan otomatis redirect ke halaman peminjaman. Yang mana, kita belum membuat view untuk menangani halaman tersebut.
### 4. Menambahkan view home peminjaman
Sekarang, didalam folder view buat satu folder baru namanya peminjaman. Kemudian, didalam folder peminjaman tersebut buat satu file baru dengan nama home.php, lalu tuliskan syntaks berikut
```
/app/views/peminjaman/home.php
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
                    <th>Tanggal Peminjaman</th>
                    <th>Tanggal di Kembalikan</th>
                    <th>Status</th>
                    <th>Tindakan</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data as $buku): ?>
                  	<tr>
                  		<td><?= $no; ?></td>
                  		<td><?= $buku['Judul']; ?></td>
                  		<td><?= $buku['TanggalPeminjaman']; ?></td>
                  		<td><?= $buku['TanggalPengembalian']; ?></td>
                  		<td><?= $buku['StatusPeminjaman']; ?></td>
                      <td>
                        <?php if($buku['StatusPeminjaman'] === 'Belum di Kembalikan'): ?>
                          <form action="<?= urlTo('/peminjaman/'.$buku['PeminjamanID'].'/kembalikan') ?>" method="post">
                          <input type="hidden" name="TanggalPengembalian" value="<?= date('Y-m-d'); ?>">
                          <input type="hidden" name="StatusPeminjaman" value="Sudah di Kembalikan">
                            <button class="btn btn-info">Kembalikan</button>
                          </form>
                        <?php endif ?>
                      </td>
                  	</tr>
                  	<?php $no++; ?>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>#</th>
                    <th>Judul Buku</th>
                    <th>Tanggal Peminjaman</th>
                    <th>Tanggal di Kembalikan</th>
                    <th>Status</th>
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
### 5. Menambahkan menu sidebar peminjaman
Untuk menambahkan menu peminjaman, buka file header.php yang berada di filder templates, perhatikan 2 baris potongan syntaks berikut ( didalam scope menu perpustakaan )
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['perpustakaan']); ?>">
            <a href="#" class="nav-link <?= menuActive(['perpustakaan']); ?>">
```
Ganti kedua baris syntaks diatas dengan syntaks berikut.
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['perpustakaan', 'peminjaman']); ?>">
            <a href="#" class="nav-link <?= menuActive(['perpustakaan', 'peminjaman']); ?>">
```
Masih didalam file header.php dan masih didalam scope menu perpustakaan, tambahkan syntaks berikut tepat dibawah menu daftar buku ( Perhatikan scope tiap menu )
```
/app/views/templates/header.php
<li class="nav-item">
                <a href="<?= urlTo('/peminjaman'); ?>" class="nav-link <?= menuActive(['peminjaman']); ?>">
                  <i class="far fa-circle nav-icon"></i>
                  <p>Buku Pinjaman</p>
                </a>
              </li>
```
Sekarang coba jalankankan lagi program nya di web browser dengan mengunjungi url berikut http://localhost/ukk_perpus_albi/peminjaman ( Url disesuaikan dengan project masing - masing ). Kalau berhasil, tampilannya akan terlihat seperti pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-9/img/91.PNG)<br>
Coba perhatikan gambar diatas, disana terdapat menu baru ( Buku pinjaman ), satu data pinjaman buku dan juga tombol kembalikan. Sekarang coba klik tombol kembalikan, kalau berhasil, maka tampilannya akan berubah seperti gambar berikut.
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-9/img/92.PNG)<br>
### Tugas
#### Sebutkan perubahan apa saja setelah user menekan tombol kembalikan pada halaman peminjaman, ( di ruang lab langsung samperin saya, kemudian sebutkan perubahannya apa saja, contoh : Pak, pada tutorial step 9 setelah saya menekan tombol kembalikan pada halaman peminjaman ada beberapa perubahan, yaitu... )
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 8](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-8) | 
[Lanjut ke step 10](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-10)
