# ukk_perpus_2024
## Step 7 CRUD Buku
Dalam step ini kita akan menambahkan sistem crud buku, saya asumsikan pada langkah sebelumnya project sudah berhasil di jalankan dan tidak terdapat error.
## 1. Menambahkan menu buku
Pada langkah pertama kita akan menambahkan menu buku pada sidebar, silahkan buka file header.php didalam folder templates. Lalu perhatikan scope menu data berikut
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['user', 'kategoribuku']); ?>">
	<a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku']); ?>">
```
Perbaharui 2 baris syntaks diatas sehingga terlihat seperti berikut.
```
/app/views/templates/header.php
<li class="nav-item <?= menuOpen(['user', 'kategoribuku', 'buku']); ?>">
            <a href="#" class="nav-link <?= menuActive(['user', 'kategoribuku', 'buku']); ?>">
```
Masih didalam scope data, tambahkan syntaks berikut dan letakan setelah menu kategori buku
```
/app/view/templates/header.php
<li class="nav-item">
	<a href="<?= urlTo('/buku'); ?>" class="nav-link <?= menuActive(['buku']); ?>">
		<i class="far fa-circle nav-icon"></i>
		<p>Buku</p>
	</a>
</li>
```
Coba jalankan program di web browser, dan buka menu data. Kalau sudah berhasil akan ada satu menu baru bernama buku
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/7a.PNG)
## 2. Menambahkan Controller buku
Sebelumnya kita sudah berhasil menambahkan menu buku, tapi kalau sekarang kalian coba klik menu tersebut response yang dikembalikan adalah error, karena kita belum menambahkan controller buku.<br>
Untuk menambahkan controller buku, didalam folder controllers buat satu folder baru dengan nama BukuController.php, lalu tuliskan syntaks berikut
```
/app/controllers/BukuController.php
<?php 
class BukuController extends Controller
{
  public function index()
  {
    $data = $this->model('KBRelasi')->get();
    $this->view('buku/home', $data);
  }

  public function create()
  {
    $data = $this->model('KategoriBuku')->getAll();
    $this->view('buku/create', $data);
  }

  public function store()
  {
    $BukuID = $this->model('Buku')->create([
      'Judul'       => $_POST['Judul'],
      'Penulis'     => $_POST['Penulis'],
      'Penerbit'    => $_POST['Penerbit'],
      'TahunTerbit' => $_POST['TahunTerbit']
    ]);

    $KategoriID = $_POST['KategoriID'];

    if ($this->model('KBRelasi')->create([
      'BukuID'      => $BukuID,
      'KategoriID'  => $KategoriID
    ]) > 0) {
      redirectTo('success', 'Selamat, Buku berhasil di tambahkan', '/buku');
    } else {
      redirectTo('error', 'Maaf, Buku gagal di tambahkan', '/buku/create');
    }
  }

  public function edit($id)
  {
    $data = $this->model('Buku')->getById($id);
    $this->view('buku/edit', $data);
  }

  public function update($id)
  {
    if ($this->model('Buku')->update($id) > 0) {
      redirectTo('success', 'Selamat, Data Buku Berhasil di Update', '/buku');
    } else {
      redirectTo('danger', 'Maaf, Data Buku gagal di Update', '/buku');
    }
  }

  public function delete($id)
	{
		if ($this->model('Buku')->delete($id) > 0) {
			redirectTo('success', 'Selamat, Data Buku berhasil di hapus!', '/buku');
		}
	}
}
```
Jangan dijalankan dulu, karena controller tersebut terhubung ke model buku, model relasi dan view home, yang akan kita buat di tahap selanjutnya
## 3. Menambahkan Model buku
Untuk membuat model buku, didalam folder models buat satu file baru dan beri nama Buku.php, lalu tuliskan syntaks berikut
```
/app/models/Buku.php
<?php 
class Buku extends BaseModel
{
  public $table_name  = "buku";
  public $table_id    = "BukuID";
}
```
## 4. Menambahkan Model relasi buku dan kategori buku
Masih di folder models, buat satu file baru beri nama KBRelasi.php, kemudian tuliskan syntaks berikut
```
/app/models/KBRelasi.php
<?php 
class KBRelasi extends BaseModel
{
  public $table_name    = "kategoribuku_relasi";
  public $table_id      = "KategoriBukuID";

  public function get()
  {
    $query = "SELECT * FROM kategoribuku_relasi
    INNER JOIN buku ON kategoribuku_relasi.BukuID = buku.BukuID
    INNER JOIN kategoribuku ON kategoribuku_relasi.KategoriID = kategoribuku.KategoriID
    ORDER BY buku.BukuID DESC
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
## 5. Menambahkan view home
Sejauh ini kita sudah berhasil menambahkan 2 model yang terhubung ke BukuController, tapi sekarang kalau kalian coba jalankan program nya response yang di kembalikan masih error karena controller tersebut memerlukan view home.<br>
Untuk membuatnya, didalam folder views, buat satu folder beri nama buku dan didalam folder baru tersebut buat satu file dengan nama home.php, kemudian tuliskan syntaks berikut
```
/app/views/buku/home.php
<?php include '../app/views/templates/header.php'; $no = 1; ?>
<div class="container-fluid">
        <div class="row">
          <div class="col-12">
            <div class="card">
              <div class="card-header">
                <a href="<?= urlTo('/buku/create'); ?>" class="btn btn-primary float-right">Tambah Data</a>
              </div>
              <!-- /.card-header -->
              <div class="card-body">
                <table id="example1" class="table table-bordered table-striped">
                  <thead>
                  <tr>
                    <th>#</th>
                    <th>Kategori</th>
                    <th>Judul</th>
                    <th>Penulis</th>
                    <th>Penerbit</th>
                    <th>Tahun Terbit</th>
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
                  		<td><?= $buku['Penerbit']; ?></td>
                  		<td><?= $buku['TahunTerbit']; ?></td>
                      <td>
                        <a 
                          href="<?= urlTo('/buku/'.$buku['BukuID'].'/edit') ?>"
                          class="btn btn-warning
                          ">
                          Edit
                        </a>

                        <a 
                          href="<?= urlTo('/buku/'.$buku['BukuID'].'/delete') ?>"
                          class="btn btn-danger
                          ">
                          Delete
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
                    <th>Penerbit</th>
                    <th>Tahun Terbit</th>
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
Coba jalankan program di web browser dan arahkan ke url berikut http://localhost/ukk_perpus_albi/buku ( sesuaikan dengan url project masing - masing )
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/7b.PNG)<br>
Kalau response nya seperti gambar diatas berarti sudah berhasil.
## 6. Menambahkan view create
Sejauh ini kita sudah berhasil membuat views untuk menampilkan data buku, tapi belum ada data yang ditampilkan. Kalau kalian coba klik tombol tambah data response yang dikembalikan adalah error. Untuk menangani error tersebut, kita harus menambahkan views create.<br>
Didalam folder buku buat satu file baru namanya create.php, lalu tuliskan syntaks berikut
```
/app/views/buku/create.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/buku/store'); ?>" method="post">
        <div class="form-group">
          <label for="KategoriID">Kategori</label>
          <select id="KategoriID" name="KategoriID" class="form-control custom-select">
          <?php foreach ($data as $k): ?>
          <option value="<?= $k['KategoriID']; ?>"><?= $k['NamaKategori']; ?></option>
          <?php endforeach ?>
          </select>
        </div>

        <div class="form-group">
          <label for="Judul">Judul</label>
          <input type="text" id="Judul" name="Judul" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Penulis">Penulis</label>
          <input type="text" id="Penulis" name="Penulis" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Penerbit">Penerbit</label>
          <input type="text" id="Penerbit" name="Penerbit" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="TahunTerbit">TahunTerbit</label>
          <input type="number" id="TahunTerbit" name="TahunTerbit" class="form-control" required>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/buku'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Tambah Data</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Jalankan program di web browser, masih di halaman buku, sekarang coba klik tombol tambah data
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/7c.PNG)<br>
Kalau tampil seperti gambar diatas, berarti sudah berhasil. Sekarang coba isi formulis tersebut lalu klik simpan. Kalau berhasil kalian akan diarahkan ke halaman home buku dan menampilkan sat data yang baru saja kalian input kan.
## 7. Menambahkan view edit
Diatas kita sudah berhasil menampilkan dan menambah data buku, sekarang kita tambahkan sistem untuk edit buku.<br>
Masih didalam folder buku, buat satu file baru dan beri nama edit.php, lalu tuliskan syntaks berikut
```
/app/views/buku/edit.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/buku/'.$data['BukuID'].'/update'); ?>" method="post">
        <div class="form-group">
          <label for="Judul">Judul</label>
          <input type="text" id="Judul" name="Judul" class="form-control"
          value="<?= $data['Judul']; ?>" required>
        </div>

        <div class="form-group">
          <label for="Penulis">Penulis</label>
          <input type="text" id="Penulis" name="Penulis" class="form-control"
          value="<?= $data['Penulis']; ?>" required>
        </div>

        <div class="form-group">
          <label for="Penerbit">Penerbit</label>
          <input type="text" id="Penerbit" name="Penerbit" class="form-control"
          value="<?= $data['Penerbit']; ?>" required>
        </div>

        <div class="form-group">
          <label for="TahunTerbit">TahunTerbit</label>
          <input type="number" id="TahunTerbit" name="TahunTerbit" class="form-control"
          value="<?= $data['TahunTerbit']; ?>" required>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/buku'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Edit Data</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Sekarang coba lagi jalan kan program di web browser, lalu klik tombol edit di data buku yang tadi kalian buat, untuk percobaan silahkan ganti data sesuai dengan keinginan lalu klik tombol edit, jika berhasil kalian akan diarahkan kembali ke halaman home buku dengan pesan "selamat data buku berhasil di edit".<br>
Untuk tombol edit tidak perlu ada syntaks yang ditambahkan, karena didalam file BukuController.php diatas sudah terdapat method delete().
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 6](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-6) | 
[Lanjut ke step 8](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-8)
