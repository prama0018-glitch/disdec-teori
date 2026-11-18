# Materi Minggu 6: Distributed File System

**AI yang digunakan:** Gemini (Google)

**Prompt:**

"Buatkan rangkuman materi dari pembahasan Distributed File System. Susun rangkuman sesuai urutan agenda pembahasan pada materi, mulai dari pengertian file system, berbagai file system, cara kerja file system, pengertian distributed file system (DFS), berbagai DFS yang umum, cara kerja DFS, studi kasus HDFS, pengertian cloud storage, relasi cloud storage dengan DFS, tipe cloud storage, hingga studi kasus cloud storage. Gunakan bahasa Indonesia yang formal, ringkas, jelas, dan mudah dipahami. Pertahankan istilah-istilah teknis yang penting serta berikan contoh jika diperlukan sesuai materi."

**Bagian yang disesuaikan dari keluaran prompt ada pada:**

Penyusunan rangkuman disesuaikan mengikuti urutan agenda pada slide perkuliahan secara sistematis. Perbedaan mendasar antara *File System* (FS) dengan hierarki FS serta komparasi teknologi penyimpanan (*DAS, NAS, SAN,* dan *DFS*) dirangkum secara ringkas agar konsep abstraksi penyimpanan mudah dipahami. Selain itu, alur arsitektur HDFS (*NameNode* dan *DataNodes*) serta contoh konfigurasi integrasi *cloud storage* (Amazon S3) dengan DFS disederhanakan tanpa mengubah substansi teknis materi.

---

# Distributed File System

## 1. Pengertian File System

*File System* (FS atau fs) adalah metode yang digunakan oleh sistem operasi untuk mengelola, mengorganisasi, dan mengatur berkas (*file*) pada media penyimpanan fisik seperti *harddisk*, *USB flashdisk*, maupun SSD. 

FS berbeda dengan **hierarki FS**. Hierarki FS (seperti standar FHS pada sistem operasi mirip UNIX) merupakan pengaturan struktur direktori dan berkas yang dilihat langsung oleh pengguna tanpa perlu memedulikan jenis FS yang berjalan di baliknya.

## 2. Berbagai Jenis File System

Setiap sistem operasi mendukung dan menggunakan jenis *file system* yang berbeda:

- **Linux**: `ext4`, `XFS`, `btrfs`, `ReiserFS`
- **DOS / Windows**: `FAT16`, `FAT32`, `NTFS`
- **macOS**: `APFS` (*Apple File System*)

## 3. Cara Kerja File System

Secara umum, mekanisme akses data dari aplikasi pengguna hingga ke perangkat keras penyimpanan berjalan melalui alur berikut:

`Aplikasi (User Space) → System Call Interface → Virtual File System (VFS) → Driver Kernel FS → Device Drivers → Hardware (Media Penyimpanan)`

Aplikasi di *user space* melakukan panggilan sistem (*system calls* seperti `read()` dan `write()`), yang kemudian diteruskan oleh *kernel* melalui *Virtual File System* ke *driver* FS spesifik untuk berinteraksi dengan media penyimpanan fisik.

## 4. Pengertian Distributed File System (DFS)

*Distributed File System* (DFS) adalah sistem abstraksi perangkat lunak yang terkoordinasi atas sekumpulan media penyimpanan di banyak mesin. DFS menyembunyikan kompleksitas heterogenitas perangkat keras, potensi kegagalan (*fault*), dan konkurensi, sehingga jaringan penyimpanan terdistribusi tersebut tampak bagi pengguna layaknya satu media penyimpanan tunggal yang terintegrasi, andal, dan berkinerja tinggi.

**Perbandingan dengan Arsitektur Penyimpanan Lain:**
- **DAS (*Direct-Attached Storage*)**: Penyimpanan yang terpasang langsung ke satu komputer/server (misalnya partisi lokal `/dev/nvme0n1`). DFS umumnya memanfaatkan DAS pada setiap *node*-nya.
- **NAS (*Network-Attached Storage*)**: Penyimpanan bersama melalui jaringan standar (*shared network*) pada level *file system* (menggunakan protokol seperti NFS atau SMB) dengan pengelolaan yang lebih mudah.
- **SAN (*Storage Area Network*)**: Penyimpanan bersama melalui jaringan khusus (*dedicated network*) pada level blok (*block storage*), berkecepatan tinggi namun berbiaya mahal.

## 5. Berbagai DFS yang Umum Digunakan

Beberapa implementasi DFS yang banyak digunakan di industri maupun riset meliputi:

- **Google File System (GFS)** dan **Colossus** (penerus GFS yang digunakan pada layanan YouTube, Gmail, dan Google Docs)
- **HDFS** (*Hadoop Distributed File System*)
- **CephFS**
- **Gluster**
- **Lustre**

## 6. Cara Kerja DFS

DFS bekerja dengan menggabungkan berbagai *namespace* penyimpanan yang tersebar di beragam lokasi (seperti *data center* lokal, lokasi jarak jauh dengan Linux NFS/Windows SMB, hingga *public cloud*) ke dalam satu **Global Namespace** tunggal. Melalui *Global Namespace* ini, pengguna maupun aplikasi dapat mengakses dan mengelola seluruh data secara transparan tanpa perlu mengetahui lokasi fisik server tempat data disimpan.

## 7. Studi Kasus: HDFS (Hadoop Distributed File System)

HDFS adalah sistem berkas terdistribusi yang bersifat *open-source*, *scalable*, dan *fault-tolerant* untuk menyimpan serta memproses *dataset* berukuran sangat besar di atas klaster *commodity hardware* (perangkat keras standar yang murah dan mudah diganti).

**Arsitektur dan Cara Kerja HDFS:**
- **NameNode (Master)**: Bertugas mengelola *metadata* sistem berkas (seperti nama file, struktur direktori, dan informasi replika) serta mengatur operasi blok (*block ops*).
- **DataNodes (Slave)**: Bertugas menyimpan data aktual yang telah dipecah menjadi blok-blok kecil di berbagai *node* dan rak server (*rack*).
- **Mekanisme Baca/Tulis & Replikasi**: Saat *client* menulis data (*write*), file dipecah menjadi blok dan didistribusikan sekaligus direplikasi ke beberapa *DataNodes* lintas rak untuk menjamin *high availability*. Saat membaca (*read*), *client* meminta lokasi blok ke *NameNode*, lalu mengambil data langsung dari *DataNodes* terdekat.

## 8. Pengertian Cloud Storage

*Cloud Storage* adalah model layanan penyimpanan data daring (*online*) yang memungkinkan pengguna menyimpan dan mengakses data pada server jarak jauh melalui jaringan internet. Infrastruktur fisik penyimpanan dikelola secara terpusat oleh penyedia layanan *cloud* dalam suatu *logical pool* yang dapat tersebar di berbagai lokasi fisik. Contoh layanan *cloud storage* populer antara lain **Amazon S3**, **Azure Blob Storage**, dan **Google Cloud Storage (GCS)**.

## 9. Relasi Cloud Storage dengan DFS

*Cloud Storage* berperan sebagai **tempat penyimpanan fisik/logis data**, sedangkan **pengorganisasian dan operasi sistem berkasnya** dapat dilakukan oleh DFS. Sebagai contoh, HDFS dapat dikonfigurasi (melalui berkas `core-site.xml` menggunakan konektor `fs.s3a`) untuk menjadikan **Amazon S3** sebagai media penyimpanan di balik operasional HDFS.

## 10. Tipe Cloud Storage

Terdapat tiga tipe utama penyimpanan pada lingkungan *cloud*:

1. **Block Storage**: Menggunakan protokol FC atau iSCSI melalui antarmuka *Direct Attached* atau SAN. Memiliki latensi sangat rendah dan ideal untuk data terstruktur serta basis data.
2. **File Storage**: Menggunakan protokol TCP/IP melalui antarmuka NFS atau SMB. Menawarkan performa baik untuk berbagi berkas (*file sharing*) dan *global file locking*.
3. **Object Storage**: Menggunakan protokol TCP/IP melalui antarmuka HTTP/REST (seperti S3). Unggul dalam skalabilitas tanpa batas (*easy scaling*) dan mudah diakses lintas jaringan LAN maupun WAN.

## 11. Studi Kasus Cloud Storage

Berbagai perangkat lunak *open-source* dan *cloud-native* modern mengimplementasikan sistem penyimpanan terdistribusi dan *object storage*:

- **Ceph**: Sistem penyimpanan terdistribusi *open-source* yang menyediakan penyimpanan terpadu (*unified storage*) untuk *Object, Block,* dan *File*.
- **CubeFS**: Sistem penyimpanan *cloud-native* generasi baru untuk data tidak terstruktur yang mendukung berbagai protokol akses sekaligus, termasuk S3, HDFS, dan POSIX.
- **RustFS**: Solusi *object storage* berperforma tinggi (*zero GC, maximum throughput*) yang kompatibel dengan S3 sebagai alternatif pengganti MinIO.
- **PGSTY SILO**: *Fork* komunitas dari MinIO yang menyediakan penyimpanan objek kompatibel S3 lengkap dengan konsol manajemen dan pembaruan keamanan.
- **Rook**: Orkestrator penyimpanan *cloud-native* (*open-source*) yang dirancang khusus untuk mengelola layanan *File, Block,* dan *Object Storage* di lingkungan Kubernetes.