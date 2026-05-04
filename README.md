Nama: Jessica Tandra  
NPM: 2406355445  
Kelas: Adpro - B  

# Reflection

> 1. What are the key differences between unary, server streaming, and bi-directional streaming RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Unary RPC adalah metode di mana klien mengirim satu request dan menerima satu response dari server. Pola ini umumnya digunakan untuk operasi sederhana, seperti autentikasi pengguna atau pengambilan data tertentu. Pada server streaming, klien mengirim satu request namun server membalas dengan banyak data secara bertahap (stream), ideal untuk skenario pembaruan real-time seperti pantauan harga saham atau berita. Sementara itu, bi-directional streaming memungkinkan klien dan server saling bertukar banyak pesan terus-menerus secara dua arah dalam satu koneksi. Metode dua arah ini sangat tepat digunakan untuk aplikasi yang membutuhkan interaksi komunikasi berkelanjutan, seperti aplikasi obrolan (chat) atau analitik real-time.

> 2. What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Dalam mengimplementasikan layanan gRPC di Rust, enkripsi data harus diprioritaskan dengan menerapkan TLS (Transport Layer Security) pada koneksi HTTP/2 untuk melindungi transmisi pesan dari intersepsi. Terkait autentikasi, gRPC memiliki dukungan bawaan yang dapat dimanfaatkan dengan cara menyisipkan dan memvalidasi token kredensial (seperti JWT) melalui metadata pada setiap request klien. Selanjutnya untuk otorisasi, pengembang harus mengatur hak akses secara ketat di tingkat aplikasi, misalnya dengan memanfaatkan fitur interceptor pada framework Tonic untuk memblokir eksekusi dari klien yang tidak memiliki izin.

> 3. What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications?

Tantangan utamanya adalah menjaga server agar tidak ikut crash ketika koneksi klien tiba-tiba terputus saat obrolan sedang berlangsung. Selain itu, kita harus mengatur batasan ukuran antrean pesan dengan pas supaya server tidak kewalahan ketika menerima pesan yang sangat banyak dalam satu waktu. Terakhir, karena pesan bisa dikirim dan diterima secara bersamaan, memastikan alur komunikasi dua arah ini tetap sinkron dan tidak berantakan.

> 4. What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services?

Keuntungan utama menggunakan ReceiverStream adalah kemudahannya dalam mengubah jalur penerima pesan dari sistem (seperti channel mpsc) menjadi format aliran data (stream) yang bisa langsung digunakan oleh framework Tonic. Berkat bantuan ini, kita tidak perlu repot merakit logika streaming dari nol saat ingin mengirim banyak respons asinkron ke klien secara bertahap. Namun kekurangannya, kita jadi memiliki tugas tambahan untuk memikirkan dan mengatur batasan ukuran antrean pesan (buffer) secara manual agar sistem tidak kelebihan beban atau macet saat lalu lintas datanya padat.

> 5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Agar struktur kode lebih rapi, setiap layanan seperti Payment, Transaction, dan Chat sebaiknya dipisahkan ke dalam file atau modul masing-masing, bukan digabungkan dalam satu file seperti grpc_server.rs. Selain itu, hasil generate dari Protobuf dapat ditempatkan pada satu library terpisah, sehingga bisa digunakan bersama oleh server dan client tanpa perlu duplikasi. Di sisi lain, logika bisnis utama juga sebaiknya tidak dicampur dengan kode komunikasi gRPC agar aplikasinya lebih gampang dites dan dikembangkan ke depannya.

> 6. In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Dalam implementasi MyPaymentService yang ada, logikanya masih sangat sederhana karena hanya mengembalikan status sukses secara langsung tanpa validasi apa pun. Untuk menangani pembayaran di dunia nyata, kita perlu mengintegrasikannya dengan database untuk mengecek saldo dan menyimpan riwayat transaksi secara aman. Selain itu, sistem juga harus disambungkan dengan layanan payment gateway eksternal untuk memproses pemotongan dana secara nyata, serta menambahkan penanganan gagal bayar yang spesifik.

> 7. What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

Penggunaan gRPC membuat arsitektur sistem terdistribusi, khususnya microservices, menjadi jauh lebih cepat dan efisien karena ukurannya yang ringan. Berkat fitur pembuatan kode otomatis (code generation) dari gRPC, sistem yang dibangun dengan bahasa pemrograman yang berbeda-beda tetap bisa saling terhubung dan berkomunikasi dengan mulus. Namun, karena gRPC masih memiliki dukungan yang terbatas pada browser web standar, penggunaannya biasanya lebih difokuskan untuk komunikasi internal antar-server.

> 8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Keuntungan utama HTTP/2 pada gRPC adalah kemampuannya melakukan multiplexing, yaitu mengirim banyak data request dan response secara bersamaan lewat satu koneksi tanpa harus saling menunggu seperti di HTTP/1.1. HTTP/2 juga memiliki sistem kompresi header (HPack) yang secara drastis mengurangi ukuran data yang melintas di jaringan. Kekurangannya, penerapan HTTP/2 ini lebih rumit dan belum didukung oleh semua jenis sistem perantara lawas jika dibandingkan dengan HTTP/1.1 yang sudah sangat universal.

> 9. How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Model komunikasi pada REST API bersifat satu arah, di mana klien harus terus-menerus mengirim request baru hanya untuk mengecek dan mendapatkan data terbaru dari server. Sebaliknya, bidirectional streaming pada gRPC memungkinkan klien dan server membuka satu jalur komunikasi awet untuk saling melempar pesan kapan saja secara real-time. Hal ini membuat gRPC jauh lebih responsif dan hemat sumber daya dibandingkan REST, terutama untuk aplikasi interaktif seperti layanan chat.

> 10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan Protobuf pada gRPC mewajibkan kita membuat cetakan struktur data (schema) yang ketat sejak awal, sehingga pesan langsung tervalidasi otomatis dan ukurannya jauh lebih padat saat dikirim. Sebaliknya, format teks JSON pada REST API memang jauh lebih fleksibel dan mudah dibaca oleh manusia, tetapi memakan ukuran file yang lebih besar dan sering butuh validasi manual. Hasilnya, gRPC sangat unggul untuk sistem internal yang menuntut performa tinggi, sedangkan REST dengan JSON lebih pas jika kemudahan integrasi dengan publik yang menjadi prioritas utamanya.