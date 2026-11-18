# Materi Minggu 3: Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

**AI yang digunakan:** Grok (xAI)

**Prompt:**

"Buatkan rangkuman materi dari pembahasan Sinkronisasi dan Koordinasi pada Sistem Terdistribusi. Susun rangkuman sesuai urutan agenda pembahasan pada materi, mulai dari pengertian sinkronisasi, pentingnya sinkronisasi, tantangan sinkronisasi, sinkronisasi waktu fisik, sinkronisasi waktu logis (termasuk Vector Clock), mutual exclusion beserta algoritmanya, hingga pemilihan koordinator. Gunakan bahasa Indonesia yang formal, ringkas, jelas, dan mudah dipahami. Pertahankan istilah-istilah teknis yang penting serta berikan contoh jika diperlukan sesuai materi."

**Bagian yang disesuaikan dari keluaran prompt ada pada:**

Penyesuaian dilakukan pada penyusunan struktur agar lebih mengikuti agenda slide, penyederhanaan penjelasan Data Race vs Race Condition, penambahan contoh praktis e-commerce dan bank, perbaikan alur penjelasan Vector Clock agar lebih runtut, serta penegasan kelebihan dan kekurangan masing-masing algoritma Mutual Exclusion dan Leader Election. Beberapa istilah teknis juga diperjelas agar konsisten dengan materi kuliah.

---

# Sinkronisasi dan Koordinasi pada Sistem Terdistribusi

## 1. Pengertian Sinkronisasi

Sinkronisasi merupakan proses koordinasi proses, data, dan sumber daya di berbagai *node* dalam sistem terdistribusi agar operasi berjalan secara konsisten dan andal. Sinkronisasi bertujuan menjaga konsistensi data, mengelola proses konkuren, serta mencegah terjadinya konflik data (*data race*).

Tanpa sinkronisasi yang baik, sistem terdistribusi rentan mengalami inkonsistensi data. Contoh klasik terjadi pada sistem e-commerce: dua pengguna membaca stok produk yang sama (10), keduanya berhasil membeli, namun stok yang tercatat menjadi 9 padahal seharusnya 8.

## 2. Data Race dan Race Condition

**Data Race** terjadi ketika dua atau lebih *thread* mengakses lokasi memori yang sama secara bersamaan, minimal satu di antaranya melakukan operasi *write*, dan tidak ada mekanisme sinkronisasi. Dampaknya adalah perilaku tidak terdefinisi dan data yang korup.

**Race Condition** terjadi ketika kebenaran program bergantung pada urutan eksekusi yang tidak dapat diprediksi. Contohnya adalah dua transaksi penarikan uang yang membaca saldo yang sama sebelum keduanya mengurangi saldo, sehingga menghasilkan saldo negatif.

Perbedaan utama:
- Data Race berada di level rendah (akses memori).
- Race Condition berada di level tinggi (logika bisnis).

## 3. Mengapa Sinkronisasi Penting?

Sinkronisasi diperlukan untuk:
- Menjaga integritas data
- Menyinkronkan *state* aplikasi di semua *node* (contoh: posisi pemain pada game *online* multiplayer atau data keranjang belanja)
- Mengoordinasikan tugas antar proses
- Mengelola sumber daya bersama
- Mendukung mekanisme *recovery*
- Mencegah *deadlock* dan operasi yang redundan
- Mendukung *load balancing* dan skalabilitas

## 4. Tantangan Sinkronisasi

Beberapa tantangan utama dalam sinkronisasi sistem terdistribusi meliputi:
- *Network latency* dan *partitioning*
- Skalabilitas
- Sistem yang *fault-tolerant*
- *Concurrency control*
- Konsistensi data
- Sinkronisasi waktu antar *node*

## 5. Sinkronisasi Waktu Fisik

Sinkronisasi waktu fisik adalah proses menyelaraskan jam nyata (*real-time clock*) pada setiap *node* dengan standar waktu yang sama.

Standar waktu yang digunakan saat ini adalah **UTC** (*Universal Coordinated Time*), yang didefinisikan oleh ITU dan berbasis pada TAI (*International Atomic Time*). GMT sudah tidak digunakan, sedangkan LTC (*Coordinated Lunar Time*) masih dalam tahap pengembangan.

Rantai sinkronisasi waktu fisik umumnya berjalan sebagai berikut:
Atomic Clock → Atomic Clock pada satelit GPS → GPS Dongle → Server NTP/PTP → Client (sistem operasi atau perangkat mobile)

Dua protokol utama yang digunakan:
- **NTP** (*Network Time Protocol*): umum digunakan, implementasi populer adalah *chrony*. Akurasi biasanya dalam kisaran milidetik hingga puluhan mikrodetik.
- **PTP** (*Precision Time Protocol* / IEEE 1588): memberikan akurasi jauh lebih tinggi (sub-mikrodetik) apabila *network interface* mendukung *hardware timestamping*. Implementasi di Linux menggunakan *linuxptp* (`ptp4l` dan `phc2sys`).

## 6. Sinkronisasi Waktu Logis

Sinkronisasi waktu logis digunakan untuk menetapkan urutan sebab-akibat (*causal order*) antar kejadian tanpa bergantung pada waktu fisik. Konsep dasar dikemukakan oleh Leslie Lamport.

Salah satu implementasi yang dibahas adalah **Vector Clock**. Setiap proses memiliki vektor berukuran sejumlah proses yang ada. Aturan dasarnya:
- Setiap *event* internal atau sebelum mengirim pesan, nilai komponen sendiri dinaikkan sebanyak 1.
- Saat menerima pesan, proses mengambil nilai maksimum per elemen (*element-wise max*), kemudian menaikkan nilai komponennya sendiri.

Aturan perbandingan:
- Event A terjadi sebelum event B jika semua komponen vektor A lebih kecil atau sama dengan B, dan minimal satu komponen lebih kecil.
- Jika kondisi tersebut tidak terpenuhi, maka kedua event bersifat *concurrent* (independen).

## 7. Tantangan Mutual Exclusion

*Mutual exclusion* adalah prinsip yang memastikan hanya satu proses yang boleh mengakses sumber daya bersama (*critical section*) pada satu waktu. Tujuannya mencegah konflik dan menjaga integritas data.

Prinsip yang harus dipenuhi:
- Mutual Exclusion
- Progress
- Bounded Waiting
- Fairness

Tiga pendekatan solusi yang dibahas:

1. **Centralized Algorithm**  
   Satu *node* bertindak sebagai koordinator. Proses meminta izin, koordinator memberikan akses jika *critical section* kosong.  
   Kelebihan: sederhana dan biaya pesan rendah.  
   Kekurangan: *single point of failure* dan potensi *bottleneck*.

2. **Ricart-Agrawala**  
   Algoritma terdistribusi penuh. Setiap proses mengirim permintaan berstempel waktu dan menunggu balasan OK dari semua proses lain.  
   Kelebihan: lebih *fault tolerant*.  
   Kekurangan: overhead pesan tinggi (2(N−1) pesan per masuk *critical section*).

3. **Token-Based Algorithm**  
   Sebuah *token* unik beredar di antara proses. Hanya proses yang memegang *token* yang boleh masuk *critical section*.  
   Kelebihan: hanya membutuhkan satu pesan per masuk *critical section*.  
   Kekurangan: deteksi dan pemulihan *token* yang hilang relatif sulit.

## 8. Pemilihan Koordinator

Dalam sistem terdistribusi, seringkali dibutuhkan satu koordinator untuk mengelola sumber daya bersama, menyinkronkan operasi, atau membuat keputusan global. Ketika koordinator gagal atau *node* baru bergabung, sistem harus memilih pemimpin baru secara otomatis.

Algoritma yang umum digunakan:
- Bully Algorithm
- Ring Algorithm
- Raft atau Paxos (berbasis konsensus)

Contoh penerapan di industri:
- Apache ZooKeeper menggunakan Zab (*ZooKeeper Atomic Broadcast*), yang mirip dengan Paxos.
- Kubernetes menggunakan Raft melalui etcd.