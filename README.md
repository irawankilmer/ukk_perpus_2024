# ukk_perpus_2024
## Step 3 LOGIN DAN REGISTER
Pada tahap ini, kita akan menambahkan sistem login dan register, dimana sistem register disini hanya berlaku untuk hak akses peminjam<br>
ok langsung saja ikuti langkah - langkah berikut
### Download template AdminLte3
Untuk tampilan, disini kita akan menggunakan template adminlte3, untuk itu kita harus mendownload nya terlebih dahulu
#### 1. klik link download berikut [AdminLte3](https://github.com/irawankilmer/adminlte3)
#### 2. pindahkan hasil download yang berupa file zip ke dalam folder public
#### 3. extract, lalu ganti namanya menjadi adminlte
#### 4. hapus file zip hasil download tadi
Jika sampai langkah ini belum berhasil, silahkan tanyakan ke teman sekelas atau langsung tanyakan ke guru pembimbing<br>
Ok sekarang kita lanjutkan ke step berikutnya, ikuti langkah - langkah berikut ini.
#### 1. Didalam folder core buat 1 file baru dengan nama functions.php, lalu ketik code berikut
```
/core/functions.php
<?php 
include '../core/Url.php';
include '../core/Controller.php';
include '../core/BaseModel.php';

$url = new Url();
function checkIsNotLogin()
{
	if (!isset($_SESSION['login'])) {
		header("Location:http://localhost/ukk_perpus_2024/login");
	}
}

function urlTo($to)
{
	return 'http://localhost/ukk_perpus_2024'.$to;
}

function redirectTo($icon, $pesan, $tujuan)
{
	setcookie('alert', serialize([$icon, $pesan]), time() + 1, '/');
	header("Location:http://localhost/ukk_perpus_2024".$tujuan);
}
```
#### 2. Ubah isi didalam file index.php yang berada didalam folder public sehingga terlihat seperti berikut
```
/public/index.php
<?php 
session_start();
include '../core/functions.php';
$url->run();
```
#### 3. Didalam folder core, buat file baru dengan nama Controller.php, lalu isikan syntaks berikut
````
/core/Controller.php
<?php 
class Controller
{
	public function view($path, $data = [])
	{
		include '../app/views/'.$path.'.php';
	}

	public function model($model)
	{
		include '../app/models/'.$model.'.php';
		return new $model;
	}
}
````
#### 4. Ubah isi didalam file HomeController yang berada didalam folder controllers, sehingga terlihat seperti berikut
```
/app/controllers/HomeController.php
<?php 
class HomeController extends Controller
{
  public function __construct()
  {
    checkIsNotLogin();
  }

  public function index()
  {
    return "Ini halaman home";
  }
}
```
Silahkan coba jalankan di web browser dengan memasukan link berikut http://localhost/ukk_perpus_2024, kalian akan langsung diarahkan ke halaman berikut http://localhost/ukk_perpus_2024/login dan mengembalikan error<br>
Silahkan baca dengan seksama, apa arti dari error tersebut.<br>
Error tersebut mengindikasikan file yang dipanggil yaitu LoginController belum dibuat, pengalihan otomatis ini berkat fungsi checkIsNotLogin() yang dibuat pada langkah satu diatas<br>
Sebelum melanjutkan, silahkan pelajari terlebih dahulu.<br>
Kalau sudah paham, silahkan ikuti lagi langkah - langkah berikut ini.
#### 5. Didalam folder core buat satu file baru dan beri nama BaseModel.php
```
/core/BaseModel.php
<?php 
class BaseModel
{
	public $table_name;
	public $mysqli;

	public function __construct()
	{
		$this->mysqli = new mysqli('localhost', 'root', '', 'ukk_perpus_2024');
	}

	public function getByUsername($username)
	{
		$result = $this->mysqli->query("SELECT * FROM $this->table_name WHERE username = '$username'");

		return $result->fetch_assoc();
	}

	public function create($data)
	{
		$properties = '';
		foreach ($data as $properti => $value) {
			$properties .= $properti.", ";
		}
		$properties = rtrim($properties, ', ');

		$values = '';
		foreach ($data as $key => $value) {
			$values .= "'".$value."', ";
		}
		$values = rtrim($values, ', ');

		$this->mysqli->query("INSERT INTO $this->table_name($properties) VALUES($values)");

		return $this->mysqli->insert_id;
	}
}
```
#### 6. Didalam folder app buat satu folder baru dan beri nama models, dan didalam folder tersebut buat satu file baru dengan nama User.php, lalu tuliskan syntaks berikut
```
/app/models/User.php
<?php 
class User extends BaseModel
{
  public $table_name  = "users";
  public $table_id    = "UserID";
}

```
#### 7. Didalam folder controllers buat satu file baru dengan nama LoginController.php, lalu isikan syntaks berikut
```
/app/controllers/LoginController.php
<?php 
class LoginController extends Controller
{
  public function index()
  {
    $this->view('login');
  }

  public function login()
	{
		$Username = $_POST['Username'];
		$Password = $_POST['Password'];
		$data 	  = $this->model('User')->getByUsername($Username);

		// periksa ketersediaan username
		if (!empty($data)) {
			// Periksa kecocokan password
			if (password_verify($Password, $data['Password'])) {
				$_SESSION['login']		= true;
				$_SESSION['username']	= $data['Username'];
				$_SESSION['role']		  = $data['Role'];
				$_SESSION['UserID']	  = $data['UserID'];
				redirectTo('success', 'Selamat datang kembali!', '/');
			} else {
				redirectTo('error', 'Maaf, Password salah!', '/login');
			}
		} else {
			redirectTo('error', 'Maaf, Username tidak terdafat!', '/login');
		}
	}

  public function register()
  {
    $this->view('register');
  }

  public function registers()
  {
    if ($_POST['Password'] !== $_POST['PasswordConfirm']) {
      redirectTo('error', 'Maaf, Konfirmasi password tidak cocok!', '/login/register');
    } else {
      if ($this->model('User')->create([
        'Username'      => $_POST['Username'],
        'Email'         => $_POST['Email'],
        'NamaLengkap'   => $_POST['NamaLengkap'],
        'Alamat'        => $_POST['Alamat'],
        'Password'      => password_hash($_POST['Password'], PASSWORD_DEFAULT),
	'Role'		=> 3
      ]) > 0) {
        redirectTo('success', 'Selamat, Registrasi berhasil', '/login/register');
      } else {
        redirectTo('error', 'Maaf, Username/Email sudah terdaftar', '/login/register');
      }
    }
  }

  public function logout()
	{
		session_destroy();
		session_unset();
		redirectTo('success', 'Selamat, Anda berhasil logout!', '/login');
	}
}
```
#### 8. Didalam folder app buat satu folder baru dan beri nama views, lalu didalamnya buat satu file baru dengan nama login.php, lalu isikan syntaks berikut
```
/app/views/login.php
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>UKK PERPUS 2024 | Log in</title>

  <!-- Google Font: Source Sans Pro -->
  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&display=fallback">
  <!-- Font Awesome -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/plugins/fontawesome-free/css/all.min.css'); ?>">
  <!-- icheck bootstrap -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/plugins/icheck-bootstrap/icheck-bootstrap.min.css'); ?>">
  <!-- sweetalert2 -->
  <link rel="stylesheet" type="text/css" href="<?= urlTo('/public/adminlte/plugins/sweetalert2/sweetalert2.css'); ?>">
  <!-- Theme style -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/css/adminlte.min.css'); ?>">
</head>
<body class="hold-transition login-page">
<div class="login-box">
  <div class="card">
    <div class="card-body login-card-body">
      <p class="login-box-msg">Selamat Datang Kembali</p>

      <form action="<?= urlTo('/login/login'); ?>" method="post">
        <div class="input-group mb-3">
          <input type="text" class="form-control" name="Username" placeholder="Username">
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-user"></span>
            </div>
          </div>
        </div>
        <div class="input-group mb-3">
          <input type="password" class="form-control" name="Password" placeholder="Password">
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-lock"></span>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col-8">
            
          </div>
          <!-- /.col -->
          <div class="col-4">
            <button type="submit" class="btn btn-primary btn-block">Sign In</button>
          </div>
          <!-- /.col -->
        </div>
      </form>
      <p class="mb-0">
        <a href="<?= urlTo('/login/register'); ?>" class="text-center">Belum punya akun ?</a>
      </p>
    </div>
    <!-- /.login-card-body -->
  </div>
</div>
<!-- /.login-box -->
<!-- sweetalert2 -->
<script src="<?= urlTo('/public/adminlte/plugins/sweetalert2/sweetalert2.min.js'); ?>"></script>
<!-- jQuery -->
<script src="<?= urlTo('/public/adminlte/plugins/jquery/jquery.min.js'); ?>"></script>
<!-- Bootstrap 4 -->
<script src="<?= urlTo('/public/adminlte/plugins/bootstrap/js/bootstrap.bundle.min.js'); ?>"></script>
<!-- AdminLTE App -->
<script src="<?= urlTo('/public/adminlte/js/adminlte.min.js'); ?>"></script>
<?php if (isset($_COOKIE['alert'])): ?>
  <?php $data = unserialize($_COOKIE['alert']); ?>
  <script>
    Swal.fire({
      title: "<?= $data[1]; ?>",
      icon: "<?= $data[0]; ?>",
      showConfirmButton: false,
      timer:2000
    });
  </script>
<?php endif ?>
</body>
</html>
```
#### 9. Terakhir, didalam folder views buat satu file baru dan beri nama register.php
```
/app/views/register.php
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>UKK PERPUS 2024 | Register</title>

  <!-- Google Font: Source Sans Pro -->
  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&display=fallback">
  <!-- Font Awesome -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/plugins/fontawesome-free/css/all.min.css'); ?>">
  <!-- icheck bootstrap -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/plugins/icheck-bootstrap/icheck-bootstrap.min.css'); ?>">
  <!-- sweetalert2 -->
  <link rel="stylesheet" type="text/css" href="<?= urlTo('/public/adminlte/plugins/sweetalert2/sweetalert2.css'); ?>">
  <!-- Theme style -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/css/adminlte.min.css'); ?>">
</head>
<body class="hold-transition login-page">
<div class="login-box">
  <div class="card">
    <div class="card-body login-card-body">
      <p class="login-box-msg">Masukan data dengan lengkap</p>

      <form action="<?= urlTo('/login/registers'); ?>" method="post">
        <div class="input-group mb-3">
          <input type="text" class="form-control" name="Username" placeholder="Username" required>
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-user"></span>
            </div>
          </div>
        </div>

        <div class="input-group mb-3">
          <input type="email" class="form-control" name="Email" placeholder="Email" required>
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-envelope"></span>
            </div>
          </div>
        </div>

        <div class="input-group mb-3">
          <input type="text" class="form-control" name="NamaLengkap" placeholder="Nama Lengkap" required>
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-user"></span>
            </div>
          </div>
        </div>

        <div class="input-group mb-3">
          <input type="text" class="form-control" name="Alamat" placeholder="Alamat" required>
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-home"></span>
            </div>
          </div>
        </div>

        <div class="input-group mb-3">
          <input type="password" name="Password" class="form-control" placeholder="Password" required>
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-lock"></span>
            </div>
          </div>
        </div>

        <div class="input-group mb-3">
          <input type="password" name="PasswordConfirm" class="form-control" placeholder="Konfirmasi Password">
          <div class="input-group-append">
            <div class="input-group-text">
              <span class="fas fa-lock"></span>
            </div>
          </div>
        </div>

        <div class="row">
          <div class="col-8">
            
          </div>
          <!-- /.col -->
          <div class="col-4">
            <button type="submit" class="btn btn-primary btn-block">Daftar</button>
          </div>
          <!-- /.col -->
        </div>
      </form>
      <p class="mb-0">
        <a href="<?= urlTo('/login'); ?>" class="text-center">Sudah punya akun ?</a>
      </p>
    </div>
    <!-- /.login-card-body -->
  </div>
</div>
<!-- /.login-box -->
<!-- sweetalert2 -->
<script src="<?= urlTo('/public/adminlte/plugins/sweetalert2/sweetalert2.min.js'); ?>"></script>
<!-- jQuery -->
<script src="<?= urlTo('/public/adminlte/plugins/jquery/jquery.min.js'); ?>"></script>
<!-- Bootstrap 4 -->
<script src="<?= urlTo('/public/adminlte/plugins/bootstrap/js/bootstrap.bundle.min.js'); ?>"></script>
<!-- AdminLTE App -->
<script src="<?= urlTo('/public/adminlte/js/adminlte.min.js'); ?>"></script>
<?php if (isset($_COOKIE['alert'])): ?>
  <?php $data = unserialize($_COOKIE['alert']); ?>
  <script>
    Swal.fire({
      title: "<?= $data[1]; ?>",
      icon: "<?= $data[0]; ?>",
      showConfirmButton: false,
      timer:2000
    });
  </script>
<?php endif ?>
</body>
</html>
```
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-3/img/login.PNG)
#### 10. Sekarang coba lagi jalankan di web browser, kalau tampilannya sudah sama seperti gambar diatas berarti sudah berhasil, kalau masih ada error, sialhakan ulangi lagi langkah - langkah diatas.
#### 11. Sebelum login buat user baru dengan cara klik tombol belum punya akun ? dibagian bawah pada halaman login
#### 12. Jika pendaftaran sudah berhasil, silahkan coba login menggunakan username dan password yang baru didaftarkan
jangan memasukan username dan email yang sudah terdaftar di database, itu akan mengakibatkan error. karena keterbatasan waktu, saya belum sempat menambahkan fitur untuk menangani masalah tersebut. Bisa dicoba oleh kalian secara mandiri dengan melihat semua materi yang sudah dipelajari di kelas<br>
#### 13. jika berhasil, maka akan diarahkan ke halaman home, yang link nya terlihat seperti berikut http://localhost/ukk_perpus_2024/
Pada halaman home tidak akan ada response apapun, tidak masalah, nanti kita perbaiki.
#### 14. Untuk mencoba fitur logout, di url silahkan arahkan ke halaman berikut http://localhost/ukk_perpus_2024/login/logout
Jika berhasil, halaman akan diarahkan ke halaman login dengan menampilkan pesan sukses seperti berikut "Selamat, Anda berhasil logout"
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
[Kembali ke step 2](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-2) | 
[Lanjut ke step 4](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-4)
