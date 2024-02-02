# ukk_perpus_2024
## Step 2 STRUKTUR FOLDER DAN URL
Pada langkah ini kita akan menambahkan struktur folder dan url, silahkan ikuti langkah - langkah berikut ini
#### 1. Buat folder baru di dalam folder htdocs dengan nama ukk_perpus_2024
#### 2. Masukan folder yang baru dibuat ke dalam text editor, boleh menggunakan sublime text, visual studio code atau yang lainnya
#### 3. Buat 3 folder baru, masing - masing dengan nama app, core dan public
#### 4. Didalam folder root/utama buat file baru dengan nama .htaccess, lalu tuliskan code berikut
```
/.htaccess
<IfModule mod_rewrite.c>
	RewriteEngine On

	RewriteRule ^public/ - [L]

	RewriteCond %{DOCUMENT_ROOT}/public/$1 -f
	RewriteRule (.+) public/$1 [L]

	RewriteRule (.*) public/index.php?page=$1 [L,QSA]
</IfModule>
```
#### 5. Kemudian, didalam folder core buat 1 file baru dengan nama Url.php, lalu tuliskan code berikut
````
/core/Url.php
<?php 
class Url
{
	public function run()
	{
		$controller = $this->getController();
		$method 	= $this->getMethod();
		$parameter 	= $this->getParameter();

		include '../app/controllers/'.$controller.'.php';
		$controller	= new $controller;

		return call_user_func([$controller, $method], $parameter);
	}

	public function getUrl()
	{
		$url = explode('/', rtrim($_GET['page'], '/'));
		$url = empty($url[0]) ? ['home'] : $url;

		return $url;
	}

	public function getController()
	{
		$controller = $this->getUrl();

		return ucfirst($controller[0]).'Controller';
	}

	public function getMethod()
	{
		$method = $this->getUrl();

		if (count($method) === 3) {
			$method = $method[2];
		} elseif (count($method) === 2) {
			$method = $method[1];
		} else {
			$method = 'index';
		}

		return $method;
	}

	public function getParameter()
	{
		$parameter = $this->getUrl();

		return count($parameter) === 3 ? $parameter[1] : null;
	}
}
````
#### 6. Didalam folder app buat folder baru dengan nama controllers, lalu didalamnya buat 1 file baru dengan nama HomeController.php dan ketikan code berikut.
```
/app/controllers/HomeController.php
<?php 
class HomeController 
{
  public function index()
  {
    return "Ini halaman home";
  }
}

```
#### 6. Sekarang, didalam folder public, buat satu file baru dengan nama index.php, kemudian tuliskan code berikut 
```
/public/index.php
<?php 
include '../core/Url.php';
$url = new Url();
print_r($url->getUrl());
```
#### 7. Silahkan coba jalankan di web browser dengan memasukan alamat berikut http://localhost/ukk_perpus_2024, lalu lihat apa yang ditampilkan
### Tugas 1
#### Tambahkan karakter buku di url, sehingga terlihat seperti berikut http://localhost/ukk_perpus_2024/buku, coba cari tahu dan perbaiki apa error nya, nilai 50
#### Didalam file index.php coba panggil semua method dari class Url, lalu catat apa saja yang dikembalikan dari method - method tersebut, nilai 50


### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
[Kembali ke step 1](https://github.com/irawankilmer/ukk_perpus_2024) | 
[Lanjut ke step 3](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-3)
