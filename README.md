# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia
### Madeline Clairine Gultom - 2306207846 - ADPRO A

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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
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
> 1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?

Dalam kasus BambangShop ini, `trait` belum diperlukan karena implementasi pada `Subscriber` tidak memerlukan polimorfisme, di mana hal ini adalah tujuan digunakannya `trait`. Oleh karena itu, implementasi menggunakan satu `struct` saja sudah cukup untuk `Subscriber` saat ini.

> 2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?

Pengunaan `DashMap` sudah tepat dalam kasus ini karena dengan menggunakan `DashMap`, kita dapat menyimpan `Subscriber` berdasarkan url sebagai key. Lalu, untuk proses pencarian dan penghapusan juga akan berjalan lebih cepat dibanding `Vec` yang perlu menelusuri satu per satu. Selain itu, `DashMap` juga bersifat *thread-safe*, yaitu kode atau struktur data dapat digunakan oleh beberapa thread secara bersamaan tanpa menyebabkan *race condition* atau *data corruption* sehingga aman untuk penggunaan paralel.

> 3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?

Penggunaan Singleton pattern hanya menjamin adanya satu instance dan setiap akses harus dikunci (lock()) di mana hal ini dapat menyebabkan *bottleneck*. Oleh karena itu, penggunaan `DashMap` sudah tepat karena bersifat *thread-safe concurrent HashMap* yang memungkinkan banyak thread membaca dan menulis data secara bersamaan tanpa perlu *explicit locking*.

#### Reflection Publisher-2
> 1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Dengan memisahkan **Service** dengan **Repository** akan membuat pemeliharaan proyek lebih mudah karena **Service** lebih berfokus untuk logika bisnis dan **Repository** berfokus pada akses data, di mana hal ini juga memenuhi prinsip **Single Responsibility (SRP)**. Selain itu, jika ke depannya terdapat perubahan, seperti mengganti sumber data, kita hanya perlu mengubah **Repository** tanpa mengutak-atik **Model** dan **Service**.

> 2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

Seperti yang telah dijelaskan pada nomor 1, jika semua tergabung pada **Model**, maka logika bisnis dan akses data akan bercampur dalam satu kelas atau `struct`. Hal ini akan membuat kode sulit dikelola, ketika ada perubahan, harus langsung mengubah banyak bagian pada **Model**. Kode akan sulit dibaca dan dipahami jika nantinya proyek memiliki banyak model. Dari sisi *testing* juga akan sulit dijalani karena semua berkumpul dalam satu kelas.

> 3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Ya, pada mata kuliah di semester yang lalu juga sudah sempat menggunakan Postman sehingga sudah cukup familiar. Postman sangat membantu bagi saya pribadi terutama dalam menguji kebenaran kode saya, seperti menguji berbagai metode HTTP (GET, POST, DELETE), melihat respon dari server dalam bentuk JSON, mengrim request tanpa harus menulis kode tambahan. Fitur yang saya sukai dari Postman adalah environment variablesnya yang memperbolehkan untuk menyimpan URL API sehingga mudah digunakan kembali.

#### Reflection Publisher-3
