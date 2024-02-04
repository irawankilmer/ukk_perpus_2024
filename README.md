# ukk_perpus_2024
## Step 6 CRUD Kategori Buku
Pada step 6 ini, kita akan menambahkan sytem crud untuk kategori buku, langsung saja ikuti langkah - langkah berikut.
## 1. Menambahkan menu Kategori Buku
Pertama - tama kita tambahkan menu kategori buku terlebih dahulu, berikut cara menambahkannya
#### a. Didalam folder templates, buka file header.php
```
/app/views/templates/header.php
<?php if ($_SESSION['role'] === 'Administrator'): ?>
          <li class="nav-item <?= menuOpen(['user']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user']); ?>">
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
            </ul>
          </li>
          <?php endif ?>
```
#### b. Tambahkan menu kategori buku
Untuk menambahkan menu kategori buku, ganti potongan kode diatas menjadi seperti berikut.
```
/app/views/templates/header.php
<?php if ($_SESSION['role'] === 'Administrator'): ?>
          <li class="nav-item <?= menuOpen(['user', 'kategoribuku']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku']); ?>">
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
            </ul>
          </li>
<?php endif ?>
```
#### b. Jalankan program
Sekarang coba jalankan program di web browser, kalau hasilnya seperti gambar berikut berarti sudah berhasil<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/1a.PNG)<br>
Tapi kalau sekarang kalian coba klik menu Kategori Buku, maka akan mengembalikan response, karena kita belum membuat controller nya
## 2. Menambahkan halaman home pada menu kategori buku.
Untuk menambahkan halaman home pada menu kategori buku, ikuti langkah langkah berikut
#### a. Didalam folder controllers buat satu file baru dengan nama KategoribukuController.php, lalu tuliskan syntaks berikut
```
/app/controllers/KategoribukuController.php
<?php 
class KategoribukuController extends Controller
{
  public function index()
  {
    $data = $this->model('Kategoribuku')->getAll();
    $this->view('kategoribuku/home', $data);
  }

  public function create()
  {
    $this->view('kategoribuku/create');
  }

  public function store()
  {
    if ($this->model('Kategoribuku')->create([
      'NamaKategori'  => $_POST['NamaKategori']
    ]) > 0) {
      redirectTo('success', 'Selamat, Data Kategori Berhasil di Tambahkan', '/kategoribuku');
    } else {
      redirectTo('danger', 'Maaf, Data Kategori gagal di Tambahkan', '/kategoribuku');
    }
  }

  public function edit($id)
  {
    $data = $this->model('KategoriBuku')->getById($id);
    $this->view('kategoribuku/edit', $data);
  }

  public function update($id)
  {
    if ($this->model('Kategoribuku')->update($id) > 0) {
      redirectTo('success', 'Selamat, Data Kategori Berhasil di Update', '/kategoribuku');
    } else {
      redirectTo('danger', 'Maaf, Data Kategori gagal di Update', '/kategoribuku');
    }
  }

  public function delete($id)
	{
		if ($this->model('Kategoribuku')->delete($id) > 0) {
			redirectTo('success', 'Selamat, Data Kategori berhasil di hapus!', '/kategoribuku');
		}
	}
}
```
#### b. Membuat model
Untuk membuat model kategori buku, didalam folder models buat satu file baru dan beri nama Kategoribuku, lalu tuliskan syntaks berikut
```
/app/models/Kategoribuku.php
<?php 
class Kategoribuku extends BaseModel
{
  public $table_name  = "kategoribuku";
  public $table_id    = "KategoriID";
}
```
#### c. Membuat tampilan home
Untuk membuat tampilan home kategori buku, didalam folder views buat satu folder baru beri nama kategoribuku, lalu didalamnya buat satu file dengan nama home.php dan tuliskan syntaks berikut pada file yang baru dibuat.
```
/app/views/kategoribuku/home.php
<?php include '../app/views/templates/header.php'; $no = 1; ?>
<div class="container-fluid">
        <div class="row">
          <div class="col-12">
            <div class="card">
              <div class="card-header">
                <a href="<?= urlTo('/kategoribuku/create'); ?>" class="btn btn-primary float-right">
                  Tambah Kategori
                </a>
              </div>
              <!-- /.card-header -->
              <div class="card-body">
                <table id="example1" class="table table-bordered table-striped">
                  <thead>
                  <tr>
                    <th>#</th>
                    <th>Nama Kategori</th>
                    <th>Tindakan</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data as $k): ?>
                    <tr>
                      <td><?= $no; ?></td>
                      <td><?= $k['NamaKategori']; ?></td>
                      <td>
                        <a 
                        href="<?= urlTo('/kategoribuku/'.$k['KategoriID'].'/edit') ?>"
                        class="btn btn-warning
                        ">
                          Edit
                        </a>

                        <a 
                        href="<?= urlTo('/kategoribuku/'.$k['KategoriID'].'/delete') ?>"
                        class="btn btn-danger
                        ">
                          Delete
                        </a>
                      </td>
                    </tr>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>#</th>
                    <th>Nama Kategori</th>
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
Silahkan coba jalankan di web browser dengan membuka link berikut http://localhost/ukk_perpus_albi/kategoribuku (link di sesuaikan dengan nama project masing - masing).
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/1b.PNG)<br>
Kalau berhasil, tampilannya akan terlihat seperti gambar diatas
#### d. Menambahkan fitur tambah
Sejauh ini kita sudah berhasil menambahkan fitur home pada kategori buku, tapi belum ada data apapun, kalau pun kalian coba klik tombol tambah kategori, maka response yang di kembalikan adalah error, itu karena kita belum menambahkan halaman create.<br>
Untuk menambahkannya, didalam folder app/views/kategoribuku buat satu file baru dengan nama create.php, lalu tuliskan syntaks berikut
```
/app/views/kategoribuku/create.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/kategoribuku/store'); ?>" method="post">
        <div class="form-group">
          <label for="NamaKategori">Nama Kategori</label>
          <input type="text" id="NamaKategori" name="NamaKategori" class="form-control" required>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/kategoribuku'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Tambah Data</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Coba lagi jalankan di web browser, kalau sudah berhasil kalian akan diperlihatkan pada tampilan tambah kategori buku, coba input beberapa data untuk kategori buku
#### e. Menambahkan fitur edit
Untuk menambahkan fitur edit, diddalam folder kategoribuku tambahkan lagi satu file baru dan beri nama edit.php, kemudian tuliskan syntaks berikut
```
/app/view/kategoribuku/edit.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/kategoribuku/'.$data['KategoriID'].'/update'); ?>" method="post">
        <div class="form-group">
          <label for="NamaKategori">Nama Kategori</label>
          <input type="text" id="NamaKategori" 
          name="NamaKategori" 
          class="form-control" 
          value="<?= $data['NamaKategori']; ?>"
          required>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/kategoribuku'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Edit Data</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Kalau sudah silahkan coba lagi jalankan di web browser, coba edit beberapa data yang tadi kalian input
#### f. Fitur hapus
Untuk fitur hapus tidak perlu ada syntaks yang di tambahkan, karena sudah tersedia method delete() didalam KategoribukuController.php

## NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
[Kembali ke step 5](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-5) | 
[Lanjut ke step 7](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-7)
