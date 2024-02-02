# ukk_perpus_2024
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
