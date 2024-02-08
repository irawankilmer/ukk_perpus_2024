# ukk_perpus_2024
## Step 15 Cetak Laporan
Pada step terakhir ini, kita akan menambahkan sistem untuk mencetak laporan buku kedalam bentuk pdf
### 1. Download Dompdf
Agar kita bisa mencetak laporan/data buku menjadi pdf, kita harus menggunakan library dompdf, untuk itu pertama - pertama kita harus mendownload nya terlebih dahulu, silahkan [klik link ini](https://github.com/dompdf/dompdf/releases). Kemudian scroll kebawah, lalu klik link yang dilingkari merah pada gambar dibawah.
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-15/img/151.PNG)<br>
Pindahkan file hasil download ke directory root project kalian masing - masing, kemudian extract. Lihat gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-15/img/152.PNG)<br>
Supaya tidak memakan space, file yang dilingkari merah pada gambar diatas silahkan dihapus saja
### 2. Menambahkan method cetakbuku()
Didalam folder controllers buka file BukuController.php, tempelkan syntaks ini (use Dompdf\Dompdf;) tepat dibawah tag php pembuka, sehingga terlihat seperti pada potongan syntaks berikut
```
/app/controllers/BukuController.php
<?php 
use Dompdf\Dompdf;
class BukuController extends Controller
```
Masih didalam file yang sama, sekarang tambahkan method berikut (simpan sebelum penutup class)
```
/app/controllers/BukuController.php
public function cetakbuku()
  {
    $data = $this->model('KBRelasi')->get();
    $html 	= "<center>";
		$html 	.= "<h1>SMK ASSALAM SAMARANG</h1>";
		$html 	.= "<h2>PERPUSTAKAAN DIGITAL</h2>";
		$html 	.= "<h3>DAFTAR BUKU</h3>";
		$html 	.= "<hr>";
    $html   .= "<table align='center' border='1' cellpadding='10' cellspacing='0'>";
		$html   .= "<tr><th>#</th><th>Kategori</th><th>Judul Buku</th><th>Penulis</th><th>Penerbit</th><th>Tahun Terbit</th></tr>";
    $no = 1;
    foreach ($data as $buku) {
      $html .= "<tr>";
      $html .= "<td>".$no."</td>";
      $html .= "<td>".$buku['NamaKategori']."</td>";
      $html .= "<td>".$buku['Judul']."</td>";
      $html .= "<td>".$buku['Penulis']."</td>";
      $html .= "<td>".$buku['Penerbit']."</td>";
      $html .= "<td>".$buku['TahunTerbit']."</td>";
      $html .= "</tr>";
      $no++;
    }
    $html   .= "</table>";
    $html 	.= "</center>";
    $dompdf = new Dompdf();
		$dompdf->loadHtml($html);
		$dompdf->setPaper('4A', 'potrait');
		$dompdf->render();
		$dompdf->stream('Data Buku', ['Attachment' => 0]);
  }
```
### 3. Menambahkan tombol cetak laporan
Didalam folder buku, buka file home.php, kemudian cari baris syntaks berikut
```
/app/views/buku/home.php
<div class="card-header">
                <a href="<?= urlTo('/buku/create'); ?>" class="btn btn-primary float-right">Tambah Data</a>
              </div>
```
Sekarang ganti baris syntaks diatas dengan syntaks berikut
```
/app/views/buku/home.php
<div class="card-header">
              <a href="<?= urlTo('/buku/cetakbuku'); ?>" class="btn btn-success float-left">Cetak Laporan</a>
                <a href="<?= urlTo('/buku/create'); ?>" class="btn btn-primary float-right">Tambah Data</a>
              </div>
```
### 4. Perbaiki functions
Terakhir, didalam folder core, buka file functions.php, kemuidan tambahkan syntaks berikut, setelah tag penutup php
```
/core/functions.php
include '../dompdf/autoload.inc.php';
```
Sekarang coba jalankan program di web browser dengan mengunjungi halaman berikut http://localhost/ukk_perpus_albi/buku/ (url disesuaikan dengan project masing - masing), kalau berhasil akan ada satu tombol baru yang dilingkari merah pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-15/img/153.PNG)<br>
Silahkan coba klik tombol cetak laporan yang dilingkari merah pada gambar diatas. Kalau programnya berhasil, maka akan diarahkan kehalaman cetakbuku seperti terlihat pada gambar berikut.<br>
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/step-15/img/154.PNG)<br>
Untuk mendownload laporan silahkan klik tombol nomor 1 yang dilingkari merah pada gambar diatas.<br>
Untuk mencetak laporan silahkan klik tombol nomor 2 yang dilingkari merah pada gambar diatas.( Untuk mencetak laporan, pastikan komputer sudah terhubung ke printer )<br>
### Tugas Terakhir
Syarat untuk bisa mengikuti ukk, semua siswa diwajibkan menyelesaikan tugas yang diberikan. Untuk tugas silahkan langsung tanyakan ke guru pembimbing
### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Tulis hanya code yang belum terdapat pada file yang akan di edit saja, jangan ditulis ulang semuanya
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Kembali ke step 14](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-14)
