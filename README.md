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
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [x] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [x] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1

1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough? 

In the Observer pattern, the Subscriber is typically defined as an interface (or a trait in Rust) to allow multiple implementations, making the system more extensible. In the Head First Design Patterns book, this approach ensures that different types of observers can implement their own behavior when receiving updates. However, in the BambangShop case, if all subscribers behave the same way—simply storing a URL and name—then defining a single `struct` is enough. Using a trait would only be necessary if we anticipate different types of subscribers, such as those that receive notifications via Webhooks, emails, or logs. If extensibility is not required, a single model struct simplifies the design without losing functionality.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case? 

The decision between using a `Vec` (list) and `DashMap` (hash map) depends on efficiency and ensuring uniqueness. A `Vec<Subscriber>`allows for simple data storage, but checking uniqueness requires iterating through the entire list, resulting in O(n) time complexity. This approach becomes inefficient as the number of subscribers grows. On the other hand, `DashMap` provides O(1) lookup time, making it more efficient for checking and enforcing uniqueness. Additionally, `DashMap` supports concurrent access, ensuring thread safety without requiring explicit locking mechanisms. Given that `id` in Program and `url` in Subscriber are meant to be unique, `DashMap` is the better choice because it guarantees uniqueness efficiently while also being optimized for concurrent operations.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead? 

Rust enforces strict thread safety, making concurrent access to shared resources a challenge. In the BambangShop case, `SUBSCRIBERS` is a globally shared data structure, which requires a safe way to handle concurrent reads and writes. `DashMap` is designed specifically for this purpose, offering fine-grained locking that only locks individual entries rather than the entire map. While it is possible to implement a Singleton manually using `Arc<RwLock<HashMap>>`, this approach introduces additional complexity and performance overhead due to frequent locking and unlocking operations. `DashMap` effectively acts as a thread-safe Singleton without the need for manual synchronization, making it the best choice for managing subscribers in a concurrent Rust environment.

#### Reflection Publisher-2

Here are the questions for this reflection:
1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?

Separating "Service" and "Repository" from the Model follows the Single Responsibility Principle (SRP), making the codebase more maintainable and scalable. The Repository layer is responsible for data access and persistence, abstracting away database operations, while the Service layer handles business logic and orchestrates interactions between repositories and other components. By doing this, we achieve a cleaner separation of concerns, making it easier to modify database logic without affecting business rules and vice versa. This also enables better unit testing since business logic can be tested independently of the data storage mechanism.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?

If we only use the Model without separating Service and Repository, the code complexity increases significantly. Each model (Program, Subscriber, Notification) would be responsible for both storing data and implementing business logic, leading to tight coupling. For instance, if a change is required in how data is retrieved or modified, it might necessitate changes across multiple models, increasing the risk of unintended side effects. Additionally, interactions between models would become more tangled, as each model might need to query and modify other models directly. This results in a monolithic design, making debugging, scaling, and extending the system more challenging.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.

Postman is an API testing tool that significantly improves the development workflow by allowing us to send HTTP requests, inspect responses, and automate testing. It helps us test endpoints without needing a front-end, ensuring that our API functions correctly. One of the most useful features is the ability to save and organize API requests into collections, making it easier to test different endpoints systematically. Another helpful feature is environment variables, which allow switching between different setups (e.g., development, staging, production) seamlessly. Automated testing and scripting in Postman also help validate responses and detect issues early. These features are invaluable for our Group Project and future software engineering projects, as they ensure API reliability and streamline debugging.

#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?

In this tutorial case, we are using the Push Model of the Observer Pattern. This means that the system actively sends notifications to subscribers as soon as an event occurs. The `NotificationService` pushes updates to all subscribers whenever a product is published, ensuring they receive information immediately. This approach is useful for real-time notifications and eliminates the need for subscribers to check for updates manually.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)

If we were to use the Pull Model, subscribers would have to periodically request updates instead of receiving them automatically. This approach has advantages, such as giving subscribers control over when they fetch data, reducing unnecessary updates, and decreasing the dependency between the publisher and subscribers. However, it also introduces disadvantages, such as increased latency due to the delay in fetching updates, the need for a polling mechanism, and a potential performance issue if many subscribers repeatedly request updates. The Push Model is more efficient in this case because it ensures immediate delivery of notifications without excessive server load.

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.

If we decide not to use multi-threading in the notification process, the system’s performance would be negatively affected. The main issue would be that the notification process would block the main thread, causing delays in handling other requests. If there are many subscribers, sending notifications sequentially would slow down the system significantly. Users might experience delays when publishing a product, as the system would take longer to process each notification. Additionally, the system would struggle with scalability, as a single slow subscriber could delay notifications for all others. Multi-threading is essential to ensure that notifications are sent concurrently, keeping the system responsive and efficient.