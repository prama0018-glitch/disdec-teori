# Materi Minggu 2: Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

**AI yang digunakan:** ChatGPT

**Prompt:**

"Buatkan rangkuman materi dari pembahasan Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi . Susun rangkuman sesuai urutan agenda pembahasan pada materi, mulai dari pengertian proses, manajemen proses, sistem terdistribusi, model komunikasi pesan, IPC, microservices, komunikasi sinkron dan asinkron, masalah komunikasi antarproses, protokol komunikasi, sinkronisasi, logical clock, koordinasi proses, tantangan sistem terdistribusi, hingga isu terkini dan diskusi. Gunakan bahasa Indonesia yang formal, ringkas, jelas, dan mudah dipahami. Pertahankan istilah-istilah teknis yang penting serta berikan contoh jika diperlukan."

**Bagian yang disesuaikan dari keluaran prompt ada pada:**

Penyesuaian dilakukan pada penggunaan bahasa agar lebih ringkas dan mudah dipahami, perbaikan beberapa penjelasan konsep seperti Shared Memory, Message Passing, RPC, dan HTTP/3, serta penambahan contoh sederhana pada pembahasan Lamport Logical Clock. Selain itu, struktur subbab disusun mengikuti urutan agenda pembahasan dan beberapa istilah teknis diperjelas agar tidak menimbulkan kesalahpahaman.

---

# Proses dan Komunikasi Antar Proses pada Sistem Terdistribusi

## 1. Pengertian Proses

Proses merupakan program yang sedang dijalankan oleh sistem operasi. Program bersifat pasif karena hanya berupa kumpulan instruksi yang tersimpan di media penyimpanan, sedangkan proses bersifat aktif karena sedang dieksekusi oleh CPU.

Dalam menjalankan proses, sistem operasi menggunakan *scheduler* untuk mengatur penggunaan CPU oleh proses atau thread. Pengaturan tersebut melibatkan mekanisme *context switching* dan *preemption* agar eksekusi dapat berjalan secara bergantian.

## 2. Manajemen Proses

Manajemen proses merupakan kegiatan sistem operasi dalam mengatur dan mengendalikan proses yang sedang berjalan. Beberapa aktivitas utamanya meliputi:

1. **Process Creation:** Proses pembuatan proses baru dengan menyediakan sumber daya seperti memori, file, dan informasi proses.
2. **Process Migration:** Pemindahan proses atau beban kerja dari satu komputer ke komputer lain untuk meningkatkan performa dan menyeimbangkan beban.
3. **Process Monitoring:** Pemantauan kondisi proses, penggunaan CPU, memori, serta status proses untuk memastikan sistem berjalan dengan baik.

## 3. Sistem Terdistribusi dan Komunikasi

Sistem terdistribusi merupakan sekumpulan komputer atau *node* yang saling terhubung melalui jaringan dan bekerja sama untuk menjalankan suatu sistem. Setiap node memiliki sumber daya dan proses masing-masing, tetapi dapat berkomunikasi untuk mencapai tujuan bersama.

Tujuan komunikasi antar-node meliputi pertukaran data, koordinasi proses, sinkronisasi, menjaga konsistensi data, meningkatkan keandalan, serta mendukung skalabilitas sistem.

Contoh penerapannya adalah *multiplayer online games*, aplikasi Gojek dan Grab, serta aplikasi WhatsApp.

## 4. Model Komunikasi Pesan

Model komunikasi pesan merupakan cara proses saling bertukar informasi dalam sistem terdistribusi. Terdapat tiga model komunikasi utama:

1. **Komunikasi Langsung:** Pengirim dan penerima berkomunikasi secara langsung menggunakan alamat dan port tertentu. Contohnya adalah *Socket Programming* menggunakan Python.
2. **Komunikasi Tidak Langsung:** Komunikasi dilakukan melalui perantara seperti *mailbox* atau *message queue*, sehingga pengirim dan penerima tidak harus berkomunikasi secara langsung.
3. **Publish/Subscribe (Pub/Sub):** Pengirim (*publisher*) mengirimkan pesan ke suatu *topic*, kemudian pesan diterima oleh penerima (*subscriber*) yang berlangganan pada topic tersebut.

## 5. Proses pada Sistem Terdistribusi dan IPC

IPC (*Inter-Process Communication*) merupakan mekanisme yang digunakan oleh beberapa proses untuk saling berkomunikasi dan bertukar informasi. Pada sistem terdistribusi, IPC diperlukan karena proses dapat berjalan pada komputer yang berbeda dan tidak memiliki memori fisik bersama.

Beberapa mekanisme IPC meliputi:

- **Shared Memory:** Komunikasi menggunakan area memori yang dapat diakses bersama oleh beberapa proses. Umumnya digunakan pada satu komputer.
- **Message Passing:** Komunikasi dengan cara mengirim dan menerima pesan menggunakan operasi seperti `send()` dan `receive()`. Contohnya MPI (*Message Passing Interface*).
- **Remote Procedure Call (RPC):** Mekanisme yang memungkinkan suatu proses memanggil fungsi pada komputer lain seolah-olah memanggil fungsi lokal. Contohnya gRPC.
- **Message-Oriented Middleware (MOM):** Infrastruktur perantara yang membantu proses pengiriman dan penerimaan pesan secara asinkron. Contoh protokolnya adalah AMQP, MQTT, DDS, dan XMPP.

## 6. Studi Kasus: Microservices

*Microservices* merupakan arsitektur aplikasi yang membagi sistem besar menjadi beberapa layanan kecil yang dapat berjalan secara independen. Setiap layanan memiliki fungsi dan tanggung jawab masing-masing serta dapat berkomunikasi melalui jaringan menggunakan HTTP/REST, gRPC, atau *Message Queue*.

Arsitektur ini memudahkan pengembangan, pemeliharaan, dan peningkatan kapasitas aplikasi karena setiap layanan dapat dikelola secara terpisah.

## 7. Komunikasi Sinkron dan Asinkron

Terdapat dua jenis komunikasi yang umum digunakan dalam sistem terdistribusi:

- **Synchronous (Sinkron):** Pengirim mengirimkan permintaan dan menunggu respons dari penerima sebelum melanjutkan proses yang bergantung pada respons tersebut.
- **Asynchronous (Asinkron):** Pengirim mengirimkan permintaan tanpa harus menunggu respons secara langsung sehingga dapat melanjutkan pekerjaan lainnya.

Perbedaan utamanya terletak pada waktu tunggu dan cara proses menangani respons.

## 8. Masalah pada Komunikasi Antarproses

Dalam sistem terdistribusi, komunikasi antarproses dapat menghadapi beberapa permasalahan, yaitu:

- **Reliability:** Menjamin pesan dapat dikirim dan diterima dengan benar. Solusinya dapat menggunakan ACK (*Acknowledgement*) dan *retransmission*.
- **Ordering:** Memastikan pesan diproses sesuai urutan yang dibutuhkan sistem. Salah satu caranya menggunakan nomor urut pesan dan Logical Clock.
- **Security:** Melindungi komunikasi dari akses yang tidak sah melalui enkripsi dan autentikasi.
- **Performance:** Menjaga performa komunikasi dengan memperhatikan *latency* dan *bandwidth* jaringan.

## 9. Protokol Komunikasi

Protokol komunikasi merupakan aturan yang digunakan perangkat atau aplikasi untuk bertukar data melalui jaringan.

Beberapa protokol dan teknologi yang digunakan antara lain:

- **TCP:** Protokol transport yang menyediakan pengiriman data secara andal dan berurutan.
- **UDP:** Protokol transport yang lebih sederhana dan tidak menjamin pengiriman maupun pengurutan data.
- **HTTP:** Protokol komunikasi yang banyak digunakan pada aplikasi berbasis web.
- **REST:** Gaya arsitektur komunikasi yang berorientasi pada sumber daya (*resource*).
- **GraphQL:** Bahasa query untuk meminta dan memanipulasi data melalui API.
- **gRPC:** Teknologi RPC yang menggunakan Protocol Buffers untuk komunikasi antar layanan.

Perkembangan HTTP meliputi HTTP/1.1 yang menggunakan TCP, HTTP/2 yang mendukung multiplexing, serta HTTP/3 yang menggunakan QUIC di atas UDP untuk meningkatkan efisiensi komunikasi.

## 10. Konsep Sinkronisasi

Sinkronisasi merupakan proses menyelaraskan waktu atau aktivitas antar-node dalam sistem terdistribusi. Setiap node memiliki *clock* masing-masing sehingga dapat terjadi perbedaan waktu yang disebut *clock drift*.

Untuk mengatasi perbedaan tersebut, sistem dapat menggunakan sumber waktu eksternal seperti Atomic Clock, GPS, dan UTC. Protokol yang digunakan untuk membantu sinkronisasi waktu antara lain:

- **NTP (Network Time Protocol):** Protokol untuk menyinkronkan waktu perangkat melalui jaringan.
- **PTP (Precision Time Protocol):** Protokol sinkronisasi waktu dengan tingkat ketelitian yang lebih tinggi dibandingkan NTP pada lingkungan yang mendukungnya.

## 11. Logical Clock

Logical Clock merupakan mekanisme untuk menentukan urutan kejadian dalam sistem terdistribusi tanpa bergantung sepenuhnya pada waktu sebenarnya (*physical clock*). Konsep ini digunakan untuk mengetahui hubungan sebab-akibat (*happens-before*) antarperistiwa.

**Lamport Logical Clock** menggunakan nilai penghitung (*counter*) dengan aturan:

1. Setiap kejadian lokal menambah nilai clock sebesar satu.
2. Ketika mengirim pesan, pengirim menyertakan nilai clock pada pesan.
3. Ketika menerima pesan, penerima memperbarui clock menggunakan rumus:

   `C = max(C_lokal, C_pesan) + 1`

Contohnya, jika proses A mengirim pesan dengan nilai clock 1 kepada proses B yang memiliki clock 1, maka setelah menerima pesan, clock B menjadi 2.

Selain Lamport Clock, terdapat pengembangan lainnya seperti Vector Clocks, Plausible Clocks, Chain Clocks, Interval Tree Clocks, dan Blum Clocks.

## 12. Koordinasi Proses

Koordinasi proses merupakan mekanisme untuk mengatur kerja sama beberapa proses pada komputer yang berbeda agar dapat menggunakan sumber daya secara teratur dan tidak menimbulkan konflik.

Koordinasi diperlukan untuk menjaga konsistensi serta mengurangi kemungkinan terjadinya *race condition* dan *deadlock*.

Contoh teknologi yang digunakan adalah Apache ZooKeeper, etcd, Consul, ClickHouse Keeper, Oxia, dan KRaft.

## 13. Tantangan dan Solusi pada Sistem Terdistribusi

Beberapa tantangan yang sering terjadi pada sistem terdistribusi beserta solusinya adalah:

| Tantangan | Solusi |
|---|---|
| Message Loss (Pesan hilang) | ACK dan pengiriman ulang pesan (*retransmission*). |
| Deadlock (Proses saling menunggu) | Menggunakan *timeout* dan algoritma deteksi deadlock. |
| Data Inconsistency (Data tidak konsisten) | Menggunakan replikasi dan protokol konsensus seperti Raft atau Paxos. |
| Security (Ancaman keamanan) | Menggunakan enkripsi TLS, autentikasi, dan otorisasi. |

## 14. Current Issues dan Diskusi

Beberapa perkembangan dan isu terkini dalam sistem terdistribusi meliputi:

- **Edge Computing dan Fog Computing:** Pemrosesan data dilakukan lebih dekat dengan sumber data untuk mengurangi keterlambatan komunikasi.
- **Observability dan Distributed Tracing:** Digunakan untuk memantau dan melacak aktivitas serta kinerja layanan dalam sistem terdistribusi.
- **Container Orchestration:** Pengelolaan container agar aplikasi dapat dijalankan dan dikembangkan secara efisien. Contohnya Kubernetes dan Docker Swarm.

### Poin Diskusi

**1. Apakah GraphQL merupakan contoh RPC?**

GraphQL bukan RPC dalam pengertian tradisional. GraphQL merupakan bahasa query untuk mengambil dan memanipulasi data melalui API, sedangkan RPC berfokus pada pemanggilan prosedur atau fungsi jarak jauh.

**2. Apakah REST merupakan contoh RPC?**

REST bukan RPC karena REST berorientasi pada sumber daya (*resource*), sedangkan RPC berorientasi pada pemanggilan prosedur atau fungsi. REST umumnya menggunakan metode HTTP seperti GET, POST, PUT, dan DELETE.

**3. Apakah Telemetry merupakan solusi yang etis?**

Telemetry merupakan proses pengumpulan dan pengiriman data mengenai aktivitas serta kinerja sistem. Penggunaannya dapat dianggap etis apabila dilakukan secara transparan, menjaga keamanan informasi, dan tidak melanggar privasi pengguna.