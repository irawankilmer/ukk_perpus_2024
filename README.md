# ukk_perpus_2024
## Step 5 CRUD PETUGAS
Pada langkah ini kita akan menambahkan fitur crud user tapi dikhususkan untuk role/hak akses petugas<br>
Tapi sebelum ke pembuatan fitur tersebut, kita tambahkan terlebih dahulu data untuk Administratornya<br>
Karena fitur crud user hanya untuk petugas, maka penambahan data Administrator harus dilakukan secara manual, cara nya ikuti langkah - langkah berikut ini
#### 1. Didalam folder public, buka file index.php, lalu ganti dulu isi nya dengan syntaks berikut
```
/public/index.php
<?php
$conn = mysqli_connect('localhost', 'root', '', 'perpus_digital'); # sesuaikan nama database nya 
$password = password_hash('admin', PASSWORD_DEFAULT);
mysqli_query($conn, "INSERT INTO users VALUES (null, 'admin', '$password', 'admin@gmail.com', 'Administrator Pertama', 'Kiarapayung', 1);
");
```
#### 2. Sekarang buka web browser dan arahkan ke link berikut http://localhost/ukk_perpus_2024/
Jika tidak terjadi error, kemungkinan besar data berhasil di tambahkan<br>
#### 3. Buka database, lalu lihat data didalam table users
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-4/img/dd.PNG)
Jika ada data baru seperti terlihat pada gambar diatas berarti sudah berhasil
#### 3. Buka lagi file index.php, lalu ganti lagi isi nya dengan syntaks berikut
```
/public/index.php
<?php 
session_start();
include '../core/functions.php';
$url->run();
```
Sekarang kita lanjutkan ke pembuatan fitur crud petugas, ikuti langkah - langkah berikut ini.
#### 1. Didalam folder controllers buat satu file baru dan beri nama UserController.php, lalu tuliskan syntaks berikut
```
/app/controllers/UserController.php
<?php 
class UserController extends Controller
{
  public function index()
  {
    $data = $this->model('User')->getAll();
    $this->view('user/home', $data);
  }

  public function create()
  {
    $this->view('user/create');
  }

  public function store()
  {
    if ($_POST['Password'] !== $_POST['Konfirmasi_Password']) {
      redirectTo('error', 'Maaf, Konfirmasi password tidak cocok!', '/user/create');
    } else {
      if ($this->model('User')->create([
        'Username'      => $_POST['Username'],
        'Email'         => $_POST['Email'],
        'NamaLengkap'   => $_POST['NamaLengkap'],
        'Alamat'        => $_POST['Alamat'],
        'Password'      => password_hash($_POST['Password'], PASSWORD_DEFAULT),
        'Role'          => 2
      ]) > 0) {
        redirectTo('success', 'Selamat, Data Petugas Berhasil di Tambahkan', '/user');
      } else {
        redirectTo('error', 'Maaf, Username/Email sudah terdaftar', '/user');
      }
    }
  }

  public function edit($id)
  {
    $data = $this->model('User')->getById($id);
    $this->view('user/edit', $data);
  }

  public function update($id)
  {
    if ($this->model('User')->update($id) > 0) {
			redirectTo('success', 'Selamat, Data Petugas berhasil di edit!', '/user');
		} else {
			redirectTo('info', 'Tidak ada perubahan data!', '/user');
		}
  }

  public function delete($id)
	{
		if ($this->model('User')->delete($id) > 0) {
			redirectTo('success', 'Selamat, Data User berhasil di hapus!', '/user');
		}
	}
}
```
#### 2. Buka file header.php, lalu tambahkan syntaks berikut simpan diantara menu home dan menu logout
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
#### 3. Didalam folder core, buka file BaseModel.php, lalu tambahkan beberapa method berikut
```
/core/BaseModel.php
public function getAll()
	{
		$result = $this->mysqli->query("SELECT * FROM $this->table_name ORDER BY $this->table_id DESC");

		$data = [];
		while ($row = $result->fetch_assoc()) {
			$data[] = $row;
		}

		return $data;
	}

	public function getById($id)
	{
		$result = $this->mysqli->query("SELECT * FROM $this->table_name WHERE $this->table_id = '$id'");

		return $result->fetch_assoc();
	}

	public function update($id)
	{
		$values = '';
		foreach ($_POST as $key => $value) {
			$values .= $key." = '".$value."', ";
		}
		$values = rtrim($values, ', ');

		$this->mysqli->query("UPDATE $this->table_name SET $values WHERE $this->table_id = '$id'");

		return $this->mysqli->affected_rows;

	}

	public function delete($id)
	{
		$this->mysqli->query("DELETE FROM $this->table_name WHERE $this->table_id = '$id'");

		return $this->mysqli->affected_rows;
	}
```
#### 4. Didalam folder views buat satu folder baru beri nama user, kemudian didalamnya tambahkan satu file dengan nama home.php, terakhir tuliskan syntaks berikut.
```
/app/views/user/home.php
<?php include '../app/views/templates/header.php'; $no = 1; ?>
<div class="container-fluid">
        <div class="row">
          <div class="col-12">
            <div class="card">
              <div class="card-header">
                <a href="<?= urlTo('/user/create'); ?>" class="btn btn-primary float-right">Tambah Data Petugas</a>
              </div>
              <!-- /.card-header -->
              <div class="card-body">
                <table id="example1" class="table table-bordered table-striped">
                  <thead>
                  <tr>
                    <th>#</th>
                    <th>Nama Lengkap</th>
                    <th>Email</th>
                    <th>Alamat</th>
                    <th>Role</th>
                    <th>Tindakan</th>
                  </tr>
                  </thead>
                  <tbody>
                  <?php foreach ($data as $user): ?>
                  	<tr>
                  		<td><?= $no; ?></td>
                  		<td><?= $user['NamaLengkap']; ?></td>
                  		<td><?= $user['Email']; ?></td>
                  		<td><?= $user['Alamat']; ?></td>
                  		<td><?= $user['Role']; ?></td>
                  		<td>
                  			<?php if($user['Role'] === 'Petugas'): ?>
                          <a href="<?= urlTo('/user/'.$user['UserID'].'/edit') ?>" class="btn btn-warning">
                            Edit
                          </a>
                  			<?php endif ?>

                        <?php if($user['Role'] !== 'Administrator'): ?>
                          <a href="<?= urlTo('/user/'.$user['UserID'].'/delete') ?>" class="btn btn-danger">
                            Delete
                          </a>
                  			<?php endif ?>
                  		</td>
                  	</tr>
                  	<?php $no++; ?>
                  <?php endforeach ?>
                  </tbody>
                  <tfoot>
                  <tr>
                    <th>#</th>
                    <th>Nama Lengkap</th>
                    <th>Email</th>
                    <th>Alamat</th>
                    <th>Role</th>
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
Sampai sini, coba dulu jalankan program di web browser dengan mengunjungi alamat berikut http://localhost/ukk_perpus_albi(sesuiaikan dengan project masing - masing) dan login menggunakan username dan password "admin".<br>
Lalu buka menu user yang bisa dibuka pada menu data, kalau tampilannya sama seperti gambar berikut, artinya sudah berhasil<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-4/img/huhu.PNG)
Kalau masih ada error, ulangi lagi langkah - langkah diatas<br>
#### 5. Didalam folder user, buat satu file baru dan beri nama create.php lalu tuliskan syntaks berikut.
```
/app/views/user/create.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/user/store'); ?>" method="post">
        <div class="form-group">
          <label for="Username">Username</label>
          <input type="text" id="Username" name="Username" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Email">Email</label>
          <input type="email" id="Email" name="Email" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="NamaLengkap">NamaLengkap</label>
          <input type="text" id="NamaLengkap" name="NamaLengkap" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Alamat">Alamat</label>
          <input type="text" id="Alamat" name="Alamat" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Password">Password</label>
          <input type="password" id="Password" name="Password" class="form-control" required>
        </div>

        <div class="form-group">
          <label for="Konfirmasi_Password">Konfirmasi Password</label>
          <input type="password" id="Konfirmasi_Password" name="Konfirmasi_Password" class="form-control" required>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/user'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Tambah Data</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
Coba jalankan lagi di web browser, kemudian coba tambahkan satu data dengan mengklik tombol tambah data petugas di halaman user.<br>
Isi semua formulir, memasukan username dan email yang sudah terdaftar di database akan mengakibatkan program error.<br>
Kalau sudah berhasil menambahkan data baru, kurang lebih tampilannya akan terlihat seperti gambar berikut.
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-4/img/heuheu.PNG)
Coba kalian perhatikan gambar diatas.<br>
Ini penting untuk dipahami untuk nanti saat pelaksanaan ukk.<br>
Yang mempunyai tombol edit dan hapus hanya data yang role nya petugas.<br>
Sedangkan data dengan role peminjam hanya memiliki tombol hapus.<br>
Terakhir role Administrator, disini saya sengaja tidak memunculkan tombol edit maupun hapus, alasannya, kalau misalkan kita tidak sengaja menghapus data dengan role Administrator, maka kita harus menambahkan lagi data baru secara manual seperti pada langkah pertama diatas.<br>
Nah kalau peminjam, saya cuma menambahkan tombol hapus, alasannya, karena peminjam merupakan user tamu, jadi seharusnya dia bisa melakukan perubahan data secara mandiri, tapi karena keterbatasan waktu, dalam project ini saya tidak akan menambahkan fitur tersebut.<br>
Supaya cepat dan memenuhi syarat dari CRUD, makannya saya hanya menambahkan tombol edit dan hapus hanya pada role petugas.
#### 6. Terakhir, masih didalam folder user, buat file baru dan beri nama edit, lalu tuliskan syntaks berikut.
```
/app/view/user/edit.php
<?php include '../app/views/templates/header.php'; ?>
<div class="col-md-6">
  <div class="card card-primary">
    <div class="card-body">
      <form action="<?= urlTo('/user/'.$data['UserID'].'/update'); ?>" method="post">
        <div class="form-group">
          <label for="Username">Username</label>
          <input type="text" id="Username" name="Username" class="form-control" value="<?= $data['Username']; ?>" readonly>
        </div>

        <div class="form-group">
          <label for="Email">Email</label>
          <input type="email" id="Email" name="Email" class="form-control" value="<?= $data['Email']; ?>" required>
        </div>

        <div class="form-group">
          <label for="NamaLengkap">NamaLengkap</label>
          <input type="text" id="NamaLengkap" name="NamaLengkap" class="form-control" value="<?= $data['NamaLengkap']; ?>" required>
        </div>

        <div class="form-group">
          <label for="Alamat">Alamat</label>
          <input type="text" id="Alamat" name="Alamat" class="form-control" value="<?= $data['Alamat']; ?>" required>
        </div>

        <div class="form-group">
          <a href="<?= urlTo('/user'); ?>" class="btn btn-danger">Batal</a>
          <button type="submit" class="btn btn-primary float-right">Edit Data</button>
        </div>
      </form>
    </div>
  </div>
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 4](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-4) | 
[Lanjut ke step 6](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-6)
