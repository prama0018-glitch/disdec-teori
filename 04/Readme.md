# Materi Minggu 4: Konsistensi dan Replikasi pada Sistem Terdistribusi

**AI yang digunakan:** Grok (xAI)

**Prompt:**

"Buatkan rangkuman materi dari pembahasan Konsistensi dan Replikasi pada Sistem Terdistribusi. Susun rangkuman sesuai urutan agenda pembahasan pada materi, mulai dari pengertian konsistensi, mengapa konsistensi penting, Teorema CAP, model-model konsistensi, trade-off latensi dengan konsistensi, pengertian replikasi data, jenis replikasi, strategi sinkronisasi replika, protokol replikasi, hingga studi kasus. Gunakan bahasa Indonesia yang formal, ringkas, jelas, dan mudah dipahami. Pertahankan istilah-istilah teknis yang penting serta berikan contoh jika diperlukan sesuai materi."

**Bagian yang disesuaikan dari keluaran prompt ada pada:**

Perubahan dilakukan pada penyajian hierarki model konsistensi agar lebih sistematis, penjelasan Teorema CAP dilengkapi dengan contoh kasus e-commerce yang lebih konkret, serta bagian trade-off latensi vs konsistensi diperjelas dengan ilustrasi dua pusat data. Selain itu, strategi penanganan konflik (LWW, Vector Clock, CRDT, dan OT) disusun ulang agar lebih ringkas dan mudah dipahami, serta contoh CRDT dan OT disederhanakan tanpa mengubah substansi materi.

---

# Konsistensi dan Replikasi pada Sistem Terdistribusi

## 1. Pengertian Konsistensi pada Sistem Terdistribusi

Konsistensi menjamin bahwa semua *node* (komputer/server) dalam sistem memiliki data yang sama atau tampak sama pada waktu tertentu, meskipun data tersebut berada di banyak tempat dan dapat diubah secara bersamaan oleh pengguna atau proses yang berbeda.

Inti dari konsistensi adalah: jika aplikasi mengubah data di satu server, sistem harus memastikan bahwa server lain akhirnya memiliki data yang sama.

## 2. Mengapa Konsistensi Penting?

Jika konsistensi diabaikan, pengguna dapat melihat versi data yang berbeda tergantung dari server tempat data dibaca. Contoh: nasabah mentransfer uang dari rekening A ke B. Server 1 sudah mengurangi saldo A, tetapi Server 2 belum menambah saldo B. Akibatnya, nasabah tidak bisa bertransaksi dari rekening B karena saldo belum mencukupi. Sistem menjadi tidak konsisten.

Tujuan utama konsistensi adalah agar pengguna selalu melihat data yang benar dan *up-to-date*.

## 3. Teorema CAP

Teorema CAP menyatakan bahwa dalam sistem terdistribusi, tidak mungkin mencapai ketiga sifat berikut secara bersamaan:

- **Consistency**: semua *node* memiliki data yang sama.
- **Availability**: data selalu tersedia saat dibutuhkan.
- **Partition Tolerance**: sistem tetap berjalan meskipun terjadi pemisahan jaringan.

Karena partisi jaringan adalah hal yang wajar, sistem harus memilih antara Consistency (CP) atau Availability (AP).

**Contoh e-Commerce:**
- Memilih AP → pengguna tetap bisa belanja, tetapi stok bisa sementara tidak sinkron.
- Memilih CP → sistem menolak transaksi sampai jaringan pulih agar data tetap konsisten.

## 4. Model Konsistensi

Model konsistensi adalah aturan atau jaminan tentang bagaimana dan kapan perubahan data terlihat oleh *node* atau klien lain.

Hierarki model konsistensi (dari kuat ke lemah):
1. **Linearizability** (paling kuat)
2. **Sequential Consistency**
3. **Causal Consistency**
4. **Read-Your-Writes**
5. **Monotonic Read**
6. **Eventual Consistency**
7. **Weak Consistency**

### Penjelasan Model Utama:

- **Strong Consistency**: Setelah data diperbarui, semua *node* langsung melihat versi terbaru. Biasanya menggunakan Two-Phase Commit atau Paxos. Cocok untuk sistem perbankan.
- **Eventual Consistency**: Data tidak langsung sama di semua *node*, tetapi akhirnya akan sama jika tidak ada update baru. Dikenal dengan istilah BASE (*Basically Available, Soft-state, Eventual consistency*). Contoh: Amazon DynamoDB dan Apache Cassandra.
- **Causal Consistency**: Jika satu operasi bergantung pada operasi lain, urutan tersebut harus dijaga. Operasi yang tidak saling berhubungan boleh terlihat dengan urutan berbeda.
- **Read-Your-Writes**: Setelah pengguna menulis data, pembacaan berikutnya oleh pengguna yang sama akan melihat hasil tulisannya.
- **Monotonic Read**: Pengguna tidak akan melihat versi data yang lebih lama dari yang sudah pernah dilihat sebelumnya.
- **Sequential Consistency**: Semua operasi tampak dieksekusi dalam urutan yang sama untuk semua *node*, meskipun tidak harus *real-time*.
- **Linearizability**: Varian terkuat dari strong consistency. Operasi tampak atomik dan langsung, seolah sistem adalah satu *node* tunggal. Digunakan di etcd, ZooKeeper, dan Spanner.

## 5. Trade-off Latensi dengan Konsistensi

Semakin tinggi tingkat konsistensi yang dijamin, biasanya semakin besar latensi yang harus ditanggung pengguna.

- Konsistensi tinggi (Strong) → sistem menunggu semua *node* sinkron → latensi tinggi.
- Konsistensi rendah (Eventual) → sistem langsung merespons → latensi rendah, tetapi data bisa sementara tidak sama.

## 6. Pengertian Replikasi Data

Replikasi data adalah proses membuat dan memelihara salinan data yang sama di beberapa *node* atau server. Tujuannya agar sistem tetap tersedia, andal, dan cepat meskipun terjadi kegagalan pada salah satu *node*.

**Tujuan replikasi:**
- Ketersediaan (*Availability*)
- Kinerja (*Performance*)
- Reliabilitas (*Reliability*)
- Skalabilitas

## 7. Jenis Replikasi

- **Master–Slave (Primary–Secondary)**: Satu *master* menulis data, *slave* hanya membaca. Contoh: PostgreSQL streaming replication.
- **Multi-Master (Peer-to-Peer)**: Semua *node* bisa menulis dan membaca. Lebih fleksibel tetapi rawan konflik. Contoh: Apache Cassandra, MongoDB, Apache Ignite.
- **Synchronous Replication**: Update ditulis ke semua *node* sekaligus sebelum dianggap selesai. Konsisten tetapi lambat.
- **Asynchronous Replication**: Update dikirim setelah transaksi di *master* selesai. Cepat tetapi bisa terjadi *delay*.

## 8. Strategi Sinkronisasi Replika

**Berdasarkan waktu sinkronisasi:**
- Asynchronous → performa tercepat, risiko kehilangan data tertinggi.
- Semi-synchronous → menunggu minimal satu replika (digunakan MySQL).
- Synchronous → konsistensi tertinggi, performa terendah.

**Berdasarkan arah:**
- Master-Slave
- Master-Master (simetris atau asimetris)

**Berdasarkan cara sinkronisasi:**
- Pull-based
- Push-based
- Hybrid

**Penanganan konflik:**
- LWW (*Last Write Wins*)
- Vector Clock
- CRDT (*Conflict-free Replicated Data Types*)
- Operational Transformation (OT)

## 9. Protokol Replikasi

1. **Primary-based (Master-Slave)**
2. **Quorum-based**: Operasi dianggap berhasil jika mendapat konfirmasi dari sejumlah *node* tertentu. Syarat: W + R > N.
3. **State Machine Replication**: Semua replika mengeksekusi perintah yang sama dalam urutan yang sama.
4. **Gossip Protocol**: Digunakan pada Cassandra untuk menyebarkan status dan data secara acak antar *node*.
5. **Chain Replication**: Data ditulis secara berantai dari *head* ke *tail*.

## 10. Studi Kasus

1. **PostgreSQL Streaming Replication** (Master-Slave)
2. **Apache Ignite Master-Master**