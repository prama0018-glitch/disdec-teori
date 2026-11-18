## Materi Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

**AI yang digunakan: Claude Sonnet (Anthropic) & Gemini**

**Prompt:**

Buat rangkuman materi sesuai dengan agenda pembahasan pada slide "Pengenalan Sistem Terdistribusi dan Terdesentralisasi" (mata kuliah Sistem Distribusi, FIF25002T). Urutkan sesuai agenda, dan sertakan tabel perbandingan serta daftar istilah penting.
Bagian yang disesuaikan dari keluaran prompt ada pada: Mengoreksi penempatan contoh database pada tabel CAP Theorem (menukar Apache Cassandra sebagai sistem AP dan MongoDB sebagai sistem CP), menghapus catatan tambahan yang tidak tercantum di slide pada bagian perbandingan sistem, serta menyelaraskan format header sesuai ketentuan tugas.

# Sistem Terdistribusi - Minggu 1: Pengenalan Sistem Terdistribusi dan Terdesentralisasi

## Tujuan Pembelajaran

Setelah mempelajari materi ini, mahasiswa mampu:

- Menjelaskan konsep sistem terdistribusi
- Menjelaskan konsep sistem terdesentralisasi
- Menjelaskan perbedaan sistem terdistribusi dengan sistem terdesentralisasi
- Menyebutkan contoh penerapan nyata keduanya
- Menyebutkan tantangan dan tren terbaru di bidang ini

## 1. Arti Penting Sistem Terdistribusi dan Terdesentralisasi

Alasan sistem ini dibutuhkan:

- Pertumbuhan data dan jumlah pengguna sangat besar
- Layanan modern memerlukan **skalabilitas**
- Sistem harus **fault-tolerant**
- Performa dan ketersediaan harus terjaga 24/7

Tanpa sistem terdistribusi dan terdesentralisasi, layanan seperti Google, Netflix, dan Blockchain tidak mungkin ada.

**Catatan istilah:**

- **Skalabilitas**: kemampuan sistem, jaringan, proses, atau organisasi untuk berkembang dan menangani peningkatan beban kerja, pengguna, atau permintaan tanpa mengorbankan kinerja atau kualitas.
- **Sistem fault-tolerant**: sistem yang tetap beroperasi normal meskipun sebagian komponennya (hardware maupun software) gagal atau mengalami malfungsi.

## 2. Pengertian Sistem Terdistribusi

Menurut Tanenbaum dan van Steen (2007), sistem terdistribusi adalah *a collection of independent computers that appears to its users as a single coherent system*, yaitu kumpulan komputer independen yang tampak bagi penggunanya sebagai satu sistem yang koheren.

Ciri utamanya:

- Komponen bersifat independen
- Berkomunikasi melalui jaringan
- Menyediakan **transparansi**: lokasi, akses, replikasi, dan kegagalan

**Contoh pada slide (toko online berbasis layanan terpisah):** aplikasi mobile dan browser mengakses sistem melalui *API Gateway* dan *Storefront WebApp*. Di belakangnya ada beberapa layanan independen (Account Service, Inventory Service, Shipping Service) yang dihubungkan lewat REST API, dan masing-masing punya database sendiri (Account DB, Inventory DB, Shipping DB). Bagi pengguna, semuanya tampak seperti satu aplikasi.

## 3. Arsitektur Sistem Dasar

| Arsitektur | Penjelasan singkat |
| :--- | :--- |
| **Client-Server** | Banyak client (laptop, ponsel, PC) terhubung melalui internet ke satu server yang menyediakan layanan. Peran keduanya jelas terpisah. |
| **Peer-to-Peer (P2P)** | Setiap node setara dan terhubung langsung satu sama lain, bisa berperan sebagai client sekaligus server. Contoh pada slide: BitTorrent dan mata uang kripto (Bitcoin, Ethereum, dan lainnya). |
| **Hybrid** | Gabungan keduanya. Client terhubung ke *supernode*, antar supernode saling terhubung, dan pertukaran data (misalnya file sharing) tetap bisa langsung antar peer. Contoh pada slide: WhatsApp, Signal, Telegram. |

## 4. Karakteristik Sistem Terdistribusi

- **Transparansi lokasi**: pengguna tidak perlu tahu di mana sumber daya berada
- **Skalabilitas**: mampu menangani pertumbuhan beban
- **Fault tolerance**: tetap berjalan saat sebagian komponen gagal
- **Kinerja tinggi**
- **Heterogenitas**: komponen boleh berbeda jenis hardware, OS, atau jaringan

## 5. Pengertian Sistem Terdesentralisasi

- **Sentralisasi**: ada satu titik kontrol
- **Desentralisasi**: kontrol didistribusikan, sehingga tidak ada *single point of failure*
- Keputusan diambil secara kolektif melalui **konsensus**
- Contoh: Blockchain, DNS, BitTorrent

**Tiga topologi jaringan menurut Paul Baran (1964, *On Distributed Communications*):**

| Topologi | Gambaran |
| :--- | :--- |
| **Centralized (A)** | Semua node terhubung ke satu titik pusat. Jika pusat gagal, seluruh jaringan terputus. |
| **Decentralized (B)** | Ada beberapa hub, masing-masing melayani sekelompok node, dan antar hub saling terhubung. |
| **Distributed (C)** | Jaringan menyerupai jaring (mesh), setiap node punya banyak koneksi dan tidak ada titik pusat yang dominan. |

## 6. Sistem Terdistribusi vs Sistem Terdesentralisasi

| Aspek | Terdistribusi | Terdesentralisasi |
| :--- | :--- | :--- |
| **Fokus** | Pembagian **beban kerja** | Pembagian **kontrol** |
| **Contoh** | Google File System, MinIO Object Storage | Blockchain |

Keduanya saling terkait tetapi berbeda fokus.

## 7. Studi Kasus Sistem Terdistribusi

- **Google File System / HDFS**: penyimpanan data besar
- **Apache Kafka dan Apache Pulsar**: messaging dan streaming
- **Netflix, Twitch, YouTube**: layanan streaming video
- **Instagram, Threads, Facebook, TikTok**: media sosial skala besar

## 8. Studi Kasus Sistem Terdesentralisasi

- **Blockchain**: Bitcoin, Ethereum, Algorand, Sui, Near, Aptos, dan lainnya
- **DNS (Domain Name System)**
- **BitTorrent**

## 9. CAP Theorem (Eric Brewer)

Sering disebut *Brewer's Theorem*, dikemukakan pada tahun 2000. Tiga properti:

| Huruf | Properti | Arti |
| :--- | :--- | :--- |
| **C** | Consistency | Setiap operasi baca mengembalikan penulisan terbaru, atau error |
| **A** | Availability | Setiap permintaan ke node yang tidak gagal mendapat respons non-error, meskipun bukan data terbaru |
| **P** | Partition Tolerance | Sistem tetap berfungsi meskipun komunikasi antar node terputus atau terfragmentasi |

**Inti teorema:** pada *distributed data store*, tidak mungkin mendapatkan *Consistency* dan *Availability* sekaligus karena partisi jaringan pasti bisa terjadi. Saat partisi terjadi, perancang sistem harus memilih antara **AP** atau **CP** (meskipun ada *auto healing* setelah partisi selesai).

| Pilihan | Perilaku saat partisi | Konsekuensi | Contoh |
| :--- | :--- | :--- | :--- |
| **AP** | Permintaan data selalu dilayani | Data bisa tidak sinkron (mengorbankan konsistensi) | Apache Cassandra |
| **CP** | Permintaan *read* maupun *write* ditolak demi menjaga konsistensi | Layanan tidak tersedia sementara (mengorbankan *availability*) | MongoDB |

## 10. Konsistensi Data

- **Strong Consistency**: hasil selalu sama pada setiap pembacaan. Contoh: bank.
- **Eventual Consistency**: data pada akhirnya akan konsisten setelah beberapa waktu. Contoh: media sosial.

## 11. Tantangan

- **Latency dan throughput**
  - **Latency**: seberapa lama suatu permintaan diproses
  - **Throughput**: seberapa banyak permintaan yang dapat diproses per satuan waktu
- **Keamanan**: DDoS dan Sybil attack
- **Kompleksitas manajemen node**
- **Governance (tata kelola)** pada sistem terdesentralisasi

**Sybil Attack:** serangan siber dengan membuat banyak identitas palsu (akun, node, atau pseudonim, bahkan hingga ribuan) secara bersamaan untuk mendominasi pengaruh, suara, atau reputasi dalam jaringan. Biasanya menyasar sistem terdesentralisasi seperti P2P atau blockchain. Dengan menyamar sebagai banyak pengguna sah, penyerang dapat memanipulasi sistem, merusak integritasnya, melakukan tindakan tidak sah, dan mengakses informasi sensitif. Nama "Sybil" berasal dari nama seorang perempuan dengan *identity disorder* pada buku tahun 1973.

## 12. Tren Masa Depan

- **Cloud-native** (Kubernetes, Docker)
- **Edge dan Fog Computing**
- **Web3 dan DeFi**
- **Federated Data / Learning** (AI terdistribusi)

## Ringkasan

- Sistem terdistribusi = berbagi **beban kerja**
- Sistem terdesentralisasi = berbagi **kontrol**
- Keduanya saling terkait tetapi berbeda fokus
- Keduanya menjadi fondasi teknologi modern

## Bahan Diskusi

- Mengapa dunia semakin bergerak menuju distribusi dan desentralisasi?
- Contoh sehari-hari apa yang paling terasa bagi Anda?

## Daftar Istilah

| Istilah | Arti |
| :--- | :--- |
| Skalabilitas | Kemampuan menangani peningkatan beban tanpa menurunkan kinerja |
| Fault-tolerant | Tetap beroperasi meski sebagian komponen gagal |
| Transparansi | Kerumitan distribusi disembunyikan dari pengguna |
| Single point of failure | Satu komponen yang jika gagal membuat seluruh sistem gagal |
| Konsensus | Mekanisme kesepakatan kolektif antar node |
| Partition | Terputusnya komunikasi antar node dalam jaringan |
| Strong consistency | Semua pembacaan selalu mengembalikan data terbaru |
| Eventual consistency | Data akan konsisten pada akhirnya, tidak seketika |
| Latency | Waktu yang dibutuhkan untuk memproses satu permintaan |
| Throughput | Jumlah permintaan yang dapat diproses per satuan waktu |
| Sybil attack | Serangan dengan banyak identitas palsu untuk mendominasi jaringan |