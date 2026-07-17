[reaploaded]. 

# Order service
A demo service with a simple interface displaying order data. 
## Launch
1. Clone the repository
```shell
git clone github.com/patechwel/check-my-order
```
2. You should have a .env file at the root of your project. Set the parameters you need in it. Use .env.template as an example.
3. Run containers
```shell
docker compose up -d
or docker compose up -d --build
```
## Points of interaction
- _http://localhost:8080/_ - interface for entering and reading orders by UID
- _http://localhost:8080/swagger_ - documentation page
- _http://localhost:8088/_ - message broker interface with detailed information
- _http://localhost:5050/_ - Database control panel. Login admin@example.com, password admin.
## Architecture
The project uses the following technology stack:
- __Go__
- __html/js__
- __kafka__
- __postgreSQL__
- __swagger__
***
On the web page, the order ID is entered into the input field. After clicking the Get button, a handler is triggered, sending a GET request to /order/{id}.

When our service receives a GET request, it returns a JSON file for that order, and does so in a variety of ways: if the order ID is in the cache, it retrieves it from there; otherwise, it goes to the database and retrieves the data from there, which places the order in the cache. If successful, the JSON file is returned with a 200 status code; otherwise, it returns a 404 status code if the order doesn't exist.

At the same time, orders arrive at the broker. The service reads them, checks for validity, and saves the new order to the cache and the database.   

## Project structure
- __./cmd/main.go__ - entry point.
- __./internal/config__ - processing service configuration files (from .env).
- __./internal/generator__ - order generator. Used for the broker producer to simulate new orders created by users.
- __./internal/infrastucture__ - migrations, database queries, and a database repository.
- __./internal/logger__ - logger implementation via [zap](https://pkg.go.dev/go.uber.org/zap).
- __./internal/model__ - order data model.
- __./internal/transport/handler__ - implementation of the request handler on the service for the "/" and "/order/" endpoints. Here is a folder with documentation generated using [swagger](https://github.com/swaggo/swag).
- __./internal/transport/kafka__ - implementation of the broker's producer and consumer.
- __./pkg/cache__ - cache implementation using [golang-lru](https://github.com/hashicorp/golang-lru).
- __./pkg/validation__ - data validator implementation using [validator](https://github.com/go-playground/validator).
- __./templates__ - web page template.

## TODO
___
- Add tests
- Implement DLQ
- Implement Retry
- Add tracing
- Add metrics
