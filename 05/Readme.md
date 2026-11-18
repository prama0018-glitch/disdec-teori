# Materi Minggu 5: Fault Tolerance pada Sistem Terdistribusi dan Terdesentralisasi

**AI yang digunakan:** Gemini (Google)

**Prompt:**

"Buatkan rangkuman materi dari pembahasan Fault Tolerance pada Sistem Terdistribusi dan Terdesentralisasi. Susun rangkuman sesuai urutan agenda pembahasan pada materi, mulai dari pengertian fault tolerance, arti penting fault tolerance pada sistem terdistribusi dan terdesentralisasi, jenis fault, klasifikasi fault berdasarkan sumber, design patterns untuk mencapai fault tolerance, actor model untuk supervisi proses, hingga implementasi fault tolerance pada berbagai bahasa pemrograman dan peranti pengembangan. Gunakan bahasa Indonesia yang formal, ringkas, jelas, dan mudah dipahami. Pertahankan istilah-istilah teknis yang penting serta berikan contoh jika diperlukan sesuai materi."

**Bagian yang disesuaikan dari keluaran prompt ada pada:**

Penyusunan rangkuman disesuaikan agar mengikuti urutan secara sistematis. Penjelasan mengenai *design patterns* (seperti *Retry* menggunakan `tenacity` dan *Circuit Breaker* menggunakan `pybreaker`) diringkas beserta alur statusnya (*closed, open, half-open*) tanpa menyertakan potongan kode mentah yang panjang agar tetap ringkas[cite: 15, 16]. Selain itu, konsep *Actor Model* dan mekanisme supervisinya (*Supervisor-Worker*) dijelaskan secara konseptual berdasarkan ilustrasi pada materi[cite: 20, 21, 22].

---

# Fault Tolerance pada Sistem Terdistribusi dan Terdesentralisasi

## 1. Pengertian Fault Tolerance

*Fault tolerance* adalah kemampuan suatu sistem untuk tetap mempertahankan operasionalnya secara tepat meskipun terjadi kegagalan (*failure*) atau kesalahan (*error*) pada satu atau lebih komponennya. Kemampuan ini sangat krusial bagi sistem yang menuntut *high availability*, *mission-critical*, maupun *life-critical system*.

## 2. Arti Penting Fault Tolerance pada Sistem Terdistribusi dan Terdesentralisasi

Sistem terdistribusi dan terdesentralisasi terdiri atas banyak komponen yang saling terhubung. Koneksi antar komponen tersebut rentan mengalami gangguan, sementara semakin kritikal suatu aplikasi, semakin tinggi pula tuntutan agar sistem tetap berfungsi dengan baik[cite: 5]. Kondisi ini menuntut tingkat *high availability* yang tinggi sehingga potensi kegagalan harus diantisipasi sejak perancangan sistem[cite: 5].

## 3. Jenis Fault

Berdasarkan sifat kemunculannya, *fault* dibagi menjadi empat jenis:

- **Transient Fault**: Kegagalan yang bersifat sementara dan hanya terjadi sekali, misalnya akibat sambaran petir.
- **Intermittent Fault**: Kegagalan yang muncul dan hilang secara berulang-ulang, misalnya akibat *overheat* pada perangkat keras.
- **Permanent Fault**: Kegagalan yang bersifat tetap hingga komponen diperbaiki atau diganti.
- **Byzantine Fault**: Kegagalan ketika suatu komponen berperilaku menyimpang, memberikan hasil yang acak, atau mengirim informasi yang tidak konsisten kepada komponen lain.

## 4. Klasifikasi Fault Berdasarkan Sumber

Berdasarkan sumber penyebabnya, *fault* diklasifikasikan ke dalam tiga kategori utama:

- **Hardware Faults**: Kegagalan pada perangkat keras fisik, seperti *disk crash* atau *power failure*.
- **Software Faults**: Kegagalan akibat kesalahan pada perangkat lunak, seperti *bugs*, *memory leak*, atau *race condition*.
- **Communication Faults**: Kegagalan pada jalur komunikasi jaringan, seperti *packet loss* atau *network partition*.

## 5. Design Patterns untuk Mencapai Fault Tolerance

Terdapat beberapa teknik dan pola desain (*design patterns*) utama untuk mewujudkan sistem yang *fault-tolerant*:

1. **Redundancy and Replication**: Sumber daya tidak hanya ditempatkan pada satu titik, melainkan direplikasi ke berbagai *node* untuk mencegah terjadinya *single point of failure* (SPOF).
2. **Load Balancing**: Mendistribusikan beban kerja ke beberapa *instance* aplikasi yang aktif menggunakan *load balancer* (seperti HAProxy atau nginx) untuk mendukung skalabilitas dan ketersediaan.
3. **Checkpointing and Rollback Recovery**:
   - *Checkpointing*: Menyimpan status (*state*) sistem secara berkala ke media penyimpanan[cite: 12].
   - *Rollback Recovery*: Mengembalikan sistem ke titik *checkpoint* terakhir yang tercatat ketika terjadi kegagalan, sehingga pemulihan tidak perlu dimulai dari awal dan meminimalkan kehilangan data.
4. **Heartbeat and Failure Detection**:
   - *Heartbeat*: Sinyal periodik yang dikirim antar komponen untuk menandakan bahwa komponen masih aktif[cite: 13].
   - *Failure Detection*: Mekanisme pemantauan *heartbeat*; jika sinyal tidak diterima dalam batas waktu tertentu, komponen diasumsikan gagal atau tidak responsif[cite: 13].
5. **Retry Mechanism**: Mencoba kembali komunikasi dengan komponen eksternal yang gagal secara terukur (misalnya menggunakan *library* `tenacity` di Python) sebelum menyatakan operasi gagal sepenuhnya[cite: 15].
6. **Circuit Breaker**: Pola desain pada arsitektur *microservices* untuk mencegah kegagalan berantai (*cascading failure*) dengan memblokir sementara panggilan ke layanan yang bermasalah (misalnya menggunakan `pybreaker`)[cite: 16]. Pola ini bekerja dengan tiga status (*state machine*):
   - **Closed**: Permintaan diizinkan masuk dan dipantau tingkat kegagalannya[cite: 16]. Jika melewati ambang batas (*fail_max*), status berubah menjadi *open*[cite: 16].
   - **Open**: Permintaan langsung ditolak untuk memberi waktu bagi layanan yang gagal agar pulih[cite: 16].
   - **Half-Open**: Setelah batas waktu pemulihan (*reset_timeout*) tercapai, sejumlah permintaan terbatas diizinkan untuk menguji kondisi layanan[cite: 16]. Jika berhasil, sirkuit kembali ke *closed*; jika gagal, kembali ke *open*[cite: 16].
7. **Consensus and Coordination**:
   - *Consensus*: Proses kesepakatan beberapa *node* terhadap satu nilai, status, atau urutan operasi meskipun terjadi gangguan atau penundaan jaringan (contoh: algoritma Raft untuk *database leader election*, komitmen pembaruan *cloud storage*, dan validasi *blockchain*)[cite: 18, 19].
   - *Coordination*: Praktik pengelolaan interaksi antar *node* agar bekerja sama secara konsisten[cite: 18].

## 6. Actor Model untuk Supervisi Proses

*Actor Model* memodelkan unit komputasi sebagai *actor* yang saling berkomunikasi secara asinkron melalui pengiriman pesan ke masing-masing *mailbox*[cite: 20]. Untuk menjamin *fault tolerance*, model ini menerapkan hierarki supervisi (*Supervisor-Worker*)[cite: 21, 22]:

- *Top-level supervisor* mengawasi *supervisor* di bawahnya, dan setiap *supervisor* memantau kondisi (*check if alive*) para *worker actor* yang dikelolanya[cite: 21].
- Apabila salah satu *actor* mengalami kegagalan, *supervisor* dapat mengisolasi masalah dan melakukan tindakan pemulihan seperti memulai ulang (*restart*) *actor* tersebut tanpa mengganggu keseluruhan sistem[cite: 21].

## 7. Implementasi Fault Tolerance pada Berbagai Bahasa Pemrograman dan Peranti Pengembangan

Dukungan *fault tolerance* tersedia baik secara bawaan (*built-in*) maupun melalui pustaka pihak ketiga (*3rd party libraries*)[cite: 22]:

- **Dukungan Bawaan (Built-in)**:
  - **Erlang / Elixir**: Memiliki mekanisme bawaan untuk *Actor Model* dan arsitektur *Supervisor-Worker*[cite: 22].
  - **Pony**: Bahasa pemrograman yang dirancang khusus dengan paradigma *Actor Model*[cite: 22].
- **Pustaka Pihak Ketiga (3rd Party Libraries)**:
  - **Python**: `Xoscar` (untuk *actor model* terdistribusi), `tenacity` (untuk mekanisme *retry*), serta `pybreaker` (untuk *circuit breaker*)[cite: 15, 16, 22].
  - **Java**: `Apache Pekko` (untuk *Actor Model* dan *Supervisor-Worker*) serta `Resilience4j` (untuk pola ketahanan seperti *circuit breaker* dan *retry*)[cite: 22].