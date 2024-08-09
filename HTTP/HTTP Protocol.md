## HTTP Protocol Summary

### HTTP Status Codes

HTTP status codes are three-digit numbers sent by the server in response to a client's request. They indicate the result of the request and help in identifying issues or confirming successful operations.

- **1xx: Informational**
  - `100 Continue`: Indicates that the initial part of a request has been received, and the client should continue with the rest.
  - `101 Switching Protocols`: The server is switching protocols as requested by the client.

- **2xx: Success**
  - `200 OK`: The request was successful, and the server returned the requested resource.
  - `201 Created`: The request was successful, and a new resource was created.
  - `204 No Content`: The request was successful, but there is no content to send back.

- **3xx: Redirection**
  - `301 Moved Permanently`: The resource has been moved to a new URL permanently.
  - `302 Found`: The resource is temporarily available at a different URL.
  - `304 Not Modified`: The resource has not been modified since the last request.

- **4xx: Client Errors**
  - `400 Bad Request`: The server cannot or will not process the request due to a client error.
  - `401 Unauthorized`: Authentication is required and has failed or not been provided.
  - `403 Forbidden`: The client does not have permission to access the requested resource.
  - `404 Not Found`: The requested resource could not be found.

- **5xx: Server Errors**
  - `500 Internal Server Error`: A generic error message when the server encounters an unexpected condition.
  - `502 Bad Gateway`: The server received an invalid response from an upstream server.
  - `503 Service Unavailable`: The server is currently unable to handle the request, often due to temporary overloading or maintenance.

### HTTP Layers

HTTP operates at the application layer of the OSI model, which is the topmost layer responsible for providing network services directly to the user's applications. The OSI model consists of seven layers:

1. **Physical Layer**: Deals with the physical connection between devices.
2. **Data Link Layer**: Responsible for node-to-node data transfer and error detection.
3. **Network Layer**: Manages data routing and forwarding across networks.
4. **Transport Layer**: Ensures reliable data transfer between systems (e.g., TCP, UDP).
5. **Session Layer**: Manages sessions between applications.
6. **Presentation Layer**: Translates data between the application and network formats.
7. **Application Layer**: Interfaces directly with applications (e.g., HTTP, FTP).

### How HTTP Works

HTTP works based on a simple request-response cycle:

1. **Client Sends Request**: The client (e.g., a web browser) sends an HTTP request to the server. This request includes a method (e.g., GET, POST), headers, and sometimes a body (e.g., form data).
   
2. **Server Processes Request**: The server receives the request, processes it, and performs the necessary action (e.g., retrieving a file, querying a database).
   
3. **Server Sends Response**: The server sends back an HTTP response, which includes a status code, headers, and often a body (e.g., HTML content).
   
4. **Client Receives Response**: The client processes the response and renders the content (e.g., displays the web page).

### HTTP Requests

HTTP requests are structured into several components:

1. **Request Line**: Specifies the HTTP method, the requested URI, and the HTTP version.
   - Example: `GET /index.html HTTP/1.1`
   
2. **Headers**: Provide metadata about the request, such as the host, user agent, and accepted content types.
   - Example: `Host: www.example.com`
   
3. **Body**: (Optional) Contains data sent by the client, used in methods like POST or PUT.


![Http Request and Response. An HTTP (Hypertext Transfer Protocol)… | by Raza  | Medium](https://miro.medium.com/v2/resize:fit:1400/0*oy4-WDRk2mYmbNv7.jpg)


### HTTP API

An HTTP API (Application Programming Interface) allows two systems to communicate over the web using HTTP. It typically involves sending HTTP requests to a server and receiving responses. HTTP APIs are commonly used for web services, where clients (e.g., web apps, mobile apps) interact with backend services.

**APIs Use HTTP as a Transport Protocol**: Many web APIs use HTTP as the underlying protocol to communicate between clients and servers. These are often called **HTTP APIs**. When you interact with a web service (e.g., REST API), you typically send HTTP requests to the API server and receive HTTP responses in return.
#### RESTful APIs

REST (Representational State Transfer) is an architectural style for designing networked applications. RESTful APIs adhere to these principles:

- **Statelessness**: Each request from a client to the server must contain all the information needed to understand and process the request.
- **Client-Server**: The client and server are separate entities that interact over the network.
- **Uniform Interface**: Resources are identified by URIs, and operations are performed using standard HTTP methods (GET, POST, PUT, DELETE).
- **Layered System**: The API's architecture may be composed of multiple layers that work together, each responsible for a specific function.

**RESTful APIs**: A popular type of web API that uses HTTP methods (GET, POST, PUT, DELETE) to perform operations on resources. For example, a REST API might allow you to:

- `GET /users/`: Retrieve a list of users.
- `POST /users/`: Create a new user.
- `PUT /users/1`: Update the user with ID 1.
- `DELETE /users/1`: Delete the user with ID 1.


### HTTP API Development with FastAPI

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints.

#### Features of FastAPI

- **Fast**: Built on Starlette for web routing and Pydantic for data validation, making it extremely fast.
- **Easy**: Automatically generates OpenAPI documentation (Swagger) and a user-friendly interface for API testing.
- **Data Validation**: Uses Python type hints for data validation and serialization.
- **Asynchronous**: Supports asynchronous request handling with `async`/`await` syntax for better performance.

#### Example: Building a Simple HTTP API with FastAPI

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

# Define a model for request body data validation
class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

# Endpoint to retrieve an item by its ID
@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}

# Endpoint to create a new item
@app.post("/items/")
async def create_item(item: Item):
    return {"item_name": item.name, "item_price": item.price}

# Endpoint to update an item
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    return {"item_id": item_id, "item_name": item.name, "item_price": item.price}

# Running the server
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

#### Explanation:

1. **FastAPI Application**: We create a `FastAPI` instance, which will handle incoming HTTP requests.
   
2. **Data Model**: We define an `Item` class that uses Pydantic for data validation. This class will be used to validate the data in the request body.

3. **Endpoints**: 
   - `@app.get("/items/{item_id}")`: This endpoint retrieves an item by its ID. The `{item_id}` is a path parameter.
   - `@app.post("/items/")`: This endpoint creates a new item. The request body is automatically validated against the `Item` model.
   - `@app.put("/items/{item_id}")`: This endpoint updates an item based on its ID.

4. **Uvicorn Server**: FastAPI apps are typically run with Uvicorn, an ASGI server, which is fast and efficient.
