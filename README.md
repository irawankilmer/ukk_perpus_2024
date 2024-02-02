# ukk_perpus_2024
#### 1. Buka file HomeController didalam folder controllers, kemudian ganti isi dari method index() sehingga terlihat seperti berikut
```
/app/controllers/HomeController.php
public function index()
  {
    $this->view('home');
  }
```
#### 2. Buka file functions.php didalam folder core, lalu tambahkan beberapa function berikut
```
/core/functions.php
function getTitle()
{
	global $url;
	$title = $url->getUrl();
	if (count($title) === 3) {
		$title = $title[2].' '.$title[0];
	} elseif (count($title) === 2) {
		$title = $title[1].' '.$title[0];
	} else {
		$title = $title[0];
	}

	return ucfirst($title);
}

function menuActive($menu)
{
	global $url;
	$m = $url->getUrl();
	foreach ($menu as $key) {
		if ($m[0] == $key) {
			return 'active';
		}
	}
}

function menuOpen($menu)
{
	global $url;
	$m = $url->getUrl();
	foreach ($menu as $key) {
		if ($m[0] == $key) {
			return 'menu-open';
		}
	}
}
```
#### 3. Didalam folder views buat folder baru dan beri nama templates, kemudian didalamnya buat satu file dengan nama header.php, lalu tuliskan syntaks berikut
```
/app/views/templates/header.php
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>UKK PERPUS 2024 | <?= getTitle(); ?></title>

  <!-- Google Font: Source Sans Pro -->
  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Source+Sans+Pro:300,400,400i,700&display=fallback">
  <!-- Font Awesome -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/plugins/fontawesome-free/css/all.min.css') ?>">
  <!-- Theme style -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/css/adminlte.min.css') ?>">
  <!-- DataTables -->
  <link rel="stylesheet" href="<?= urlTo('/public/adminlte/plugins/datatables-bs4/css/dataTables.bootstrap4.min.css') ?>">
  <!-- sweetalert2 -->
  <link rel="stylesheet" type="text/css" href="<?= urlTo('/public/adminlte/plugins/sweetalert2/sweetalert2.css') ?>">
</head>
<body class="hold-transition sidebar-mini layout-fixed">
<div class="wrapper">

  <!-- Navbar -->
  <nav class="main-header navbar navbar-expand navbar-white navbar-light">
    <!-- Left navbar links -->
    <ul class="navbar-nav">
      <li class="nav-item">
        <a class="nav-link" data-widget="pushmenu" href="#" role="button"><i class="fas fa-bars"></i></a>
      </li>
    </ul>
  </nav>
  <!-- /.navbar -->

  <!-- Main Sidebar Container -->
  <aside class="main-sidebar sidebar-dark-primary elevation-4">

    <!-- Sidebar -->
    <div class="sidebar">
      <!-- Sidebar user panel (optional) -->
      <div class="user-panel mt-3 pb-3 mb-3 d-flex">

        <div class="info">
          <a href="#" class="d-block"><?= $_SESSION['username']; ?></a>
        </div>
      </div>

      <!-- Sidebar Menu -->
      <nav class="mt-2">
        <ul class="nav nav-pills nav-sidebar flex-column" data-widget="treeview" role="menu" data-accordion="false">
          <li class="nav-item">
            <a href="<?= urlTo('/') ?>" class="nav-link <?= menuActive(['home']); ?>">
              <i class="nav-icon fas fa-university"></i>
              <p>Home</p>
            </a>
          </li>

          <li class="nav-item">
            <a href="<?= urlTo('/login/logout') ?>" class="nav-link">
              <i class="nav-icon fas fa-sign-out-alt"></i>
              <p>Logout</p>
            </a>
          </li>
        </ul>
      </nav>
      <!-- /.sidebar-menu -->
    </div>
    <!-- /.sidebar -->
  </aside>

  <!-- Content Wrapper. Contains page content -->
  <div class="content-wrapper">
    <!-- Content Header (Page header) -->
    <div class="content-header">
      <div class="container-fluid">
        <div class="row mb-2">
          <div class="col-sm-6">
            <h1 class="m-0"><?= getTitle(); ?></h1>
          </div><!-- /.col -->
        </div><!-- /.row -->
      </div><!-- /.container-fluid -->
    </div>
    <!-- /.content-header -->

    <!-- Main content -->
    <section class="content">
```
#### 4. Didalam folder templates buat satu file baru dengan nama footer.php, lalu tuliskan syntaks berikut
```
/app/views/templates/footer.php

    </section>
    <!-- /.content -->
  </div>
  <!-- /.content-wrapper -->
  <footer class="main-footer">
    <strong>Copyright &copy; 2023-2024 SMK ASSALAM SAMARANG - GARUT.</strong>
    All rights reserved.
  </footer>

  <!-- Control Sidebar -->
  <aside class="control-sidebar control-sidebar-dark">
    <!-- Control sidebar content goes here -->
  </aside>
  <!-- /.control-sidebar -->
</div>
<!-- ./wrapper -->

<!-- sweetalert2 -->
<script src="<?= urlTo('/public/adminlte/plugins/sweetalert2/sweetalert2.min.js') ?>"></script>
<!-- jQuery -->
<script src="<?= urlTo('/public/adminlte/plugins/jquery/jquery.min.js') ?>"></script>
<!-- DataTables  & Plugins -->
<script src="<?= urlTo('/public/adminlte/plugins/datatables/jquery.dataTables.min.js') ?>"></script>
<script src="<?= urlTo('/public/adminlte/plugins/datatables-bs4/js/dataTables.bootstrap4.min.js') ?>"></script>
<!-- Bootstrap 4 -->
<script src="<?= urlTo('/public/adminlte/plugins/bootstrap/js/bootstrap.bundle.min.js') ?>"></script>
<!-- AdminLTE App -->
<script src="<?= urlTo('/public/adminlte/js/adminlte.js') ?>"></script>
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
<script>
  $(function () {
    $("#example1").DataTable({
      "responsive": true, "lengthChange": false, "autoWidth": false
    }).buttons().container().appendTo('#example1_wrapper .col-md-6:eq(0)');
  });
</script>
</body>
</html>
```
#### 5. Terakhir, didalam folder views buat satu file lagi dan beri nama home.php, lalu tuliskan syntaks berikut
```
/app/views/home.php
<?php include '../app/views/templates/header.php'; ?>
<div class="container-fluid">
</div>
<?php include '../app/views/templates/footer.php'; ?>
```
#### 6. Silahkan coba lagi jalankan di web browser dan login menggunakan akun yang sudah dibuat pada tahap sebelumnya.
Kalau berhasil, halaman dashboard/home akan terlihat seperti pada gambar berikut<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-4/img/home.PNG)

### Tugas 2
#### - Pada gambar diatas hanya terdapat 2 menu, yaitu home dan logout, coba tambah menu buku, nilai 100

### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
[Kembali ke step 3](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-3) | 
[Lanjut ke step 5](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-5)
