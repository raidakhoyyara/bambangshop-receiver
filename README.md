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
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. RwLock<> berfungsi untuk melakukan sinkronisasi pada penggunaan Vec<Notification> karena bisa jadi ada akses bersamaan oleh banyak thread terhadap NOTIFICATIONS. Sinkronisasi ini diperlukan agar tidak terjadi data race.
RwLock dipilih dibandingkan Mutex karena pada kasus ini operasi pembacaan (read) jauh lebih sering dilakukan dibandingkan penulisan (write). RwLock memungkinkan beberapa thread untuk membaca data bersamaan selama tidak ada thread yang sedang menulis. Sementara itu, Mutex hanya mengizinkan satu thread untuk mengakses data dalam satu waktu, baik untuk membaca maupun menulis. Oleh karena itu, RwLock lebih efisien digunakan dalam kasus ini.
2. Rust tidak mengizinkan perubahan (mutation) langsung pada variabel static karena memiliki jaminan keamanan (safety guarantees) yang ketat. Bedanya dengan Java, yaitu variabel static dapat diubah melalui fungsi statis, hal tersebut bisa menyebabkan data race dalam lingkungan multi-threading.
Rust menjalankan keamanan thread (thread-safety) pada saat kompilasi. Jika ingin menggunakan variabel static yang dapat diubah, maka harus secara eksplisit menggunakan mekanisme sinkronisasi seperti RwLock atau Mutex. Oleh karena itu, digunakan lazy_static yang dikombinasikan dengan RwLock, sehingga compiler Rust dapat memastikan bahwa akses terhadap variabel tersebut tetap aman.

#### Reflection Subscriber-2
1. Ya, saya telah mengeksplorasi src/lib.rs pada Receiver App. Dari eksplorasi tersebut, saya mempelajari bahwa file ini berisi konfigurasi penting seperti APP_CONFIG yang menyimpan nama instance, root URL, dan publisher URL yang diambil dari file .env. Selain itu, terdapat juga REQWEST_CLIENT yang merupakan HTTP client yang digunakan bersama di seluruh aplikasi, serta fungsi bantu seperti compose_error_response. Hal ini berbeda dengan Java Spring Boot, di mana konfigurasi biasanya disimpan dalam application.properties dan pengelolaan bean dilakukan secara otomatis oleh framework.
2. Observer pattern sangat memudahkan dalam menambahkan subscriber baru. Program hanya perlu menjalankan instance baru dari Receiver App dan memanggil endpoint subscribe, tanpa perlu melakukan perubahan apa pun pada Main App. Hal ini dimungkinkan karena adanya loose coupling antara publisher dan subscriber. Namun, jika kita menjalankan lebih dari satu instance Main App, hal tersebut menjadi lebih kompleks karena setiap instance memiliki data SUBSCRIBERS masing-masing yang disimpan di memori. Akibatnya, jika pengguna melakukan subscribe ke instance A tetapi event terjadi di instance B, notifikasi tidak akan terkirim. Untuk mengatasi hal ini, diperlukan penggunaan database bersama atau message broker.
3. Ya, saya telah mencoba membuat pengujian di Postman dengan menambahkan script pada tab Tests. Sebagai contoh, saya membuat pengujian untuk memverifikasi bahwa response memiliki status 200 OK dan body berupa array. Hal ini sangat berguna karena memungkinkan saya untuk memverifikasi perilaku API secara otomatis tanpa harus memeriksa setiap response secara manual. Dalam konteks Group Project, fitur ini akan sangat membantu untuk memastikan seluruh endpoint API berjalan dengan baik sebelum diintegrasikan dengan frontend.
### Postman Screenshot
![Postman Screenshot](postman.png)