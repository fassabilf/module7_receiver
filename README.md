# BambangShop Receiver App
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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [X] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [X] Commit: `Create Notification model struct.`
    -   [X] Commit: `Create SubscriberRequest model struct.`
    -   [X] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [X] Commit: `Implement add function in Notification repository.`
    -   [X] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [X] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [X] Commit: `Create Notification service struct skeleton.`
    -   [X] Commit: `Implement subscribe function in Notification service.`
    -   [X] Commit: `Implement subscribe function in Notification controller.`
    -   [X] Commit: `Implement unsubscribe function in Notification service.`
    -   [X] Commit: `Implement unsubscribe function in Notification controller.`
    -   [X] Commit: `Implement receive_notification function in Notification service.`
    -   [X] Commit: `Implement receive function in Notification controller.`
    -   [X] Commit: `Implement list_messages function in Notification service.`
    -   [X] Commit: `Implement list function in Notification controller.`
    -   [X] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. Kita menggunakan `RwLock<>` karena data `Vec<Notification>` bersifat *read-heavy*—lebih sering dibaca dibanding diubah. `RwLock` memungkinkan banyak thread membaca data secara paralel (concurrent), tapi hanya mengunci penuh saat ada thread yang menulis. Ini jauh lebih efisien dibanding `Mutex`, yang mengunci akses bahkan saat hanya ingin membaca. Jadi, `RwLock` adalah solusi optimal untuk performa dan thread safety dalam konteks ini.

2. Di Rust, mutasi terhadap static variable tidak diizinkan secara langsung karena bisa menyebabkan *data race* dalam lingkungan multithreading. Ini berbeda dengan Java, yang mengizinkan mutasi static variable tanpa perlindungan eksplisit. Rust menekankan safety — jadi untuk membuat variable global yang bisa diakses dan dimodifikasi, kita perlu memakai `lazy_static` untuk inisialisasi, dan bungkus dengan struktur seperti `RwLock`, `Mutex`, atau `DashMap`. Ini menjamin bahwa setiap akses/modifikasi aman dari race condition dan sesuai dengan sistem ownership Rust.


#### Reflection Subscriber-2

1. **Have you explored things outside of the steps in the tutorial, for example: `src/lib.rs`?**

   Ya, aku sempat eksplor bagian `src/lib.rs` karena penasaran gimana konfigurasi aplikasi diatur. Dari situ, aku belajar bahwa file ini berisi konfigurasi global seperti `APP_CONFIG` dan `REQWEST_CLIENT` yang bisa digunakan oleh semua module. `APP_CONFIG` ternyata mengambil nilai dari file `.env` dengan bantuan `dotenv` dan `Figment`, dan itu sangat penting untuk nyambungin Publisher ke Receiver secara dinamis. Jadi, bagian ini krusial walau jarang disinggung dalam tutorialnya.

2. **Sejak berhasil menjalankan beberapa Receiver, bagaimana Observer pattern membantu penambahan subscriber baru? Dan bagaimana kalau kita jalankan banyak Main app?**

   Observer pattern sangat memudahkan penambahan subscriber karena arsitekturnya memang dirancang untuk dinamis. Kita cukup menjalankan instance baru dari Receiver dan mengirimkan request subscribe ke Main App, dan sistem langsung bisa mengirim notifikasi ke instance baru tersebut tanpa ubah code apa pun. Tapi kalau kita jalankan lebih dari satu Main App, akan muncul masalah baru, karena list subscriber disimpan secara lokal di memory Main App. Jadi perlu solusi tambahan seperti shared database atau message broker supaya bisa tetap sinkron antar instance Main App.

3. **Sudah coba buat test sendiri atau eksplor dokumentasi Postman collection?**

   Iya, aku sempat coba buat koleksi baru di Postman untuk test berbagai skenario seperti subscribe ganda, unsubscribe, dan notifikasi ke banyak Receiver sekaligus. Fitur paling berguna menurutku adalah Postman **Environment Variables**, karena kita bisa set URL dasar `localhost:8001`, `8002`, dll dan ganti-ganti dengan cepat. Fitur **Test Script** juga menarik buat otomatisasi cek response, dan mungkin bisa berguna banget kalau dipakai untuk project kelompok nanti biar semua anggota tim bisa testing dengan cara yang sama.