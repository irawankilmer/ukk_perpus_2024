# ukk_perpus_2024

## Step 1 PEMBUATAN DATABASE
Dalam tulisan ini kita akan membahas soal ukk tahun 2024 paket ke satu yaitu pembuatan Aplikasi Perpustakaan Digital.<br>
Sistem ini mempunyai 3 hak akses yang bisa dilihat pada gambar berikut.<br>
![Alt text](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/Role.PNG) <br>
Untuk melihat soal lengkap, silahkan minta kepada guru pembimbing<br>
Ok, langsung saja masuk ke pembahasan, pada tahap ini kita akan membuat database lengkap dengan beberapa table yang dibutuhkan.

#### 1. Silahkan buka aplikasi xampp, kemudian klik start pada tombol apache dan mysql
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/1.PNG)
#### 2. Buat Database baru dengan cara masuk ke alamat berikut http://localhost/phpmyadmin/
Untuk nama database silahkan disesuaikan, disini saya memberi nama database nya perpus_digital
![](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/2.PNG)
#### 2. Klik di nama database yang baru dibuat
#### 3. Kemudian klik menu sql dibagian atas, lalu masukan script sql berikut
```
CREATE TABLE users(
    UserID INT NOT NULL AUTO_INCREMENT,
    Username VARCHAR(255) UNIQUE,
    Password VARCHAR(255),
    Email VARCHAR(255),
    NamaLengkap VARCHAR(255),
    Alamat TEXT,
    Role ENUM("Administrator", "Petugas", "Peminjam"),
    PRIMARY KEY(UserID)
);

CREATE TABLE buku(
    BukuID INT NOT NULL AUTO_INCREMENT,
    Judul VARCHAR(255),
    Penulis VARCHAR(255),
    Penerbit VARCHAR(255),
    TahunTerbit INT,
    PRIMARY KEY(BukuID)
);

CREATE TABLE kategoribuku(
    KategoriID INT NOT NULL AUTO_INCREMENT,
    NamaKategori VARCHAR(255),
    PRIMARY KEY(KategoriID)
);

CREATE TABLE kategoribuku_relasi(
    KategoriBukuID INT NOT NULL AUTO_INCREMENT,
    BukuID INT NOT NULL,
    KategoriID INT NOT NULL,
    PRIMARY KEY(KategoriBukuID),
    FOREIGN KEY(BukuID) REFERENCES buku(BukuID) ON DELETE CASCADE,
    FOREIGN KEY(KategoriID) REFERENCES kategoribuku(KategoriID) ON DELETE CASCADE
);

CREATE TABLE ulasanbuku(
    UlasanID INT NOT NULL AUTO_INCREMENT,
    UserID INT NOT NULL,
    BukuID INT NOT NULL,
    Ulasan TEXT,
    Rating INT,
    PRIMARY KEY (UlasanID),
    FOREIGN KEY (UserId) REFERENCES users (UserId) ON DELETE CASCADE,
    FOREIGN KEY (BukuID) REFERENCES buku (BukuID) ON DELETE CASCADE
);

CREATE TABLE koleksipribadi(
    KoleksiID INT NOT NULL AUTO_INCREMENT,
    UserID INT NOT NULL,
    BukuID INT NOT NULL,
    PRIMARY KEY (KoleksiID),
    FOREIGN KEY (UserID) REFERENCES users (UserId) ON DELETE CASCADE,
    FOREIGN KEY (BukuID) REFERENCES buku (BukuID) ON DELETE CASCADE
);

CREATE TABLE peminjaman (
    PeminjamanID INT NOT NULL AUTO_INCREMENT,
    UserID INT NOT NULL,
    BukuID INT NOT NULL,
    TanggalPeminjaman DATE,
    TanggalPengembalian DATE,
    StatusPeminjaman VARCHAR(50),
    PRIMARY KEY (PeminjamanID),
    FOREIGN KEY (UserID) REFERENCES users (UserID) ON DELETE CASCADE,
    FOREIGN KEY (BukuID) REFERENCES buku (BukuID) ON DELETE CASCADE
);
```

#### 4. Terakhir, klik tombol kirim/send pada bagian paling bawah
#### 5. Dipojok kanan atas klik menu designer. Kalau berhasil, maka tampilannya akan terlihat seperti gambar berikut

![Alt text](https://github.com/irawankilmer/ukk_perpus_2024/blob/main/img/database%20design.PNG)

### NOTE :
#### - Jika masih ada error silahkan tanyakan ke guru pembimbing
#### - Penjelasan lengkap untuk semua syntaks code dalam tutorial ini bisa dilihat di video playlist chanel youtube berikut https://www.youtube.com/watch?v=dzA-d4S26N4&list=PLo95Mxpc5WqHmF12tRMvIcOv8bt0TYM_d . Penjelasan dalam playlist tersebut mencakup hampir semua materi (Basis Data, SQL, Pemodelan Perangkat Lunak, Pemrograman Berorientasi Objek dan Pemrograman web dan perangkat bergerak) dari kelas XI sampai kelas XII. Silahkan dipelajari
[Lanjut ke step 2](https://github.com/irawankilmer/ukk_perpus_2024/tree/step-2)
