# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
##### Mengapa tidak menggunakan Interface (Trait) untuk Subscriber di kasus ini?
    Interface (atau Trait di Rust) digunakan untuk mendefinisikan behavior (perilaku/fungsi) yang bisa diimplementasikan secara berbeda-beda oleh berbagai objek. Struct digunakan untuk mendefinisikan bentuk data.

    Dalam sistem BambangShop ini, entitas `Subscriber` hanyalah sebuah struktur data pasif yang menyimpan `url` dan `name` . Tidak ada berbagai jenis pelanggan dengan cara pemrosesan yang kompleks secara internal. Semua pelanggan dikirimi payload JSON yang sama melalui HTTP POST request standar .

##### Mengapa DashMap (Dictionary) lebih baik daripada Vec (List) untuk menyimpan Subscriber?
    `Vec` adalah array. Waktu pencariannya berbanding lurus dengan jumlah data ($O(n)$). `DashMap` adalah hash map. Waktu pencarian dan aksesnya instan ($O(1)$).

    Identitas pelanggan dalam sistem ini bersifat unik, yang direpresentasikan oleh `url` . Jika menggunakan `Vec`, setiap kali sistem ingin menghapus (unsubscribe) pelanggan, program harus menelusuri daftar dari awal sampai menemukan URL yang cocok.
##### Apakah bisa menggunakan pola Singleton tradisional alih-alih DashMap?
    Singleton membatasi pembuatan objek hanya satu instance.
    Di bahasa Rust, penggabungan makro `lazy_static!` dan tipe data DashMap merupakan salah satu cara mengimplementasikan pola Singleton yang thread-safe. Rust sangat ketat soal kepemilikan memori dan mencegah data race (balapan modifikasi data antar thread) saat compile time.

#### Reflection Publisher-2
##### Mengapa "Service" dan "Repository" harus dipisah dari Model?
    Memisahkan antara `Service` dan `Repository` merupakan salah satu prinsip dari SIngle Responsibility Principle (SRP). Setiap class/file yang ada hanya boleh menjalankan 1 kegunaan.
    MVC (Model-View-Controller) tradisional sering menghasilkan "Fat Model" (Model yang obesitas). Dengan memisahkan `Repository`, sistem hanya bertanggung jawab melakukan operasi ke database (CRUD). `Service` pun juga seperti itu, stsem hanya mengurus logika bisnis seperti mengatur payload dan menyuruh thread mengirim notifikasi. Model hanya bertugas mendefinisikan bentuk tipe data.

##### Apa dampaknya jika hanya menggunakan Model tanpa Service/Repository?
    Akan terjadi tight coupling (keterikatan yang kuat). Model `Product`, `Subscriber`, dan `Notification` akan saling bercampur aduk. Mengubah satu baris di `Notification` bisa merusak struktur logika Product. Kode menjadi spaghetti, sangat sulit dibaca, dan mustahil untuk dilakukan unit testing secara terisolasi.

##### Manfaat Fitur Postman
    * Dapat menguji API dengan cepat dan mudah
    * Automated Testing
    * API Documentation
    * Mock Server
    * Environment & Variabel

#### Reflection Publisher-3
##### Variasi Observer Pattern apa yang digunakan (Push atau Pull)?
    Disini saya mutlak menggunakan Push Model. Main App (Publisher) yang secara aktif melakukan inisiatif membuat HTTP POST request dan mendorong (pushing) data JSON Notification langsung ke URL milik Receiver. Receiver hanya pasif menunggu pesan masuk.

##### Apa keuntungan dan kerugian jika menggunakan Pull Model?
    Dalam Pull model, Subscriber yang aktif me-request "Apakah ada data baru?" ke Publisher secara berkala.
    * Keuntungan: Publisher tidak perlu repot menyimpan daftar URL ribuan Subscriber di memori, dan tidak terbebani secara komputasi untuk mengirim pesan.
    * Kerugian: Terjadi pemborosan bandwidth jaringan dan CPU yang masif (polling overhead). Subscriber akan terus-menerus menembak API Publisher meskipun tidak ada produk baru. Selain itu, notifikasi tidak bersifat real-time.

##### Apa yang terjadi jika pengiriman notifikasi tidak menggunakan multi-threading?
    Terjadi antrean Bottleneck fatal. Jika `thread::spawn`  dihapus, sistem akan mengeksekusi pengiriman secara sekuensial. Jika ada 5.000 pelanggan, sistem akan mengirim ke pelanggan ke-1, menunggunya selesai, lalu lanjut ke pelanggan ke-2. Jika server milik pelanggan ke-5 mati dan mengalami timeout selama 30 detik, maka antrean pelanggan ke-6 hingga 5.000 akan terhenti total. Proses API pembuatan produk akan hang dan gagal merespons.



