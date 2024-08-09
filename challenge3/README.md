## Application Using Docker

### Overview

This project is part of a series of Docker challenges and involves creating a full-stack application with Docker Compose. The application consists of three key services: a MariaDB database, a Node.js backend, and an NGINX web server. The primary objective is to containerize each service and ensure they communicate effectively through Docker's networking capabilities.

### Project Structure

The repository is organized as follows:

```
.
├── docker
│   ├── api
│   │   ├── Dockerfile
│   │   ├── package.json
│   │   ├── server.js
│   ├── db
│   │   ├── init
│   │   │   ├── init.sql
│   │   ├── Dockerfile
│   ├── nginx
│   │   ├── Dockerfile
│   │   ├── nginx.conf
├── .env
├── docker-compose.yml
└── README.md
```
- **docker/api**: Contains the Node.js application code and Dockerfile.
- **docker/db**: Contains the database initialization SQL script and Dockerfile.
- **docker/nginx**: Contains the NGINX configuration and Dockerfile.
- **.env**: A file containing environment variables for database configuration.
- **docker-compose.yml**: The Docker Compose file that orchestrates the services.

### Services

#### MariaDB (db)

- **Image**: `mariadb`
- **Environment Variables**:
    - `MYSQL_ROOT_PASSWORD`: The root password for the MariaDB instance.
    - `MYSQL_DATABASE`: The name of the database to be created.
    - `MYSQL_USER`: The username for database access.
    - `MYSQL_PASSWORD`: The password for the above user.
- **Volumes**:
    - Maps the `init.sql` script to `/docker-entrypoint-initdb.d/init.sql` to initialize the database with predefined data.
- **Ports**:
    - Exposes port 3306 to the host machine.

#### Node.js Service (node-service)

- **Build Context**: `./docker/api`
- **Environment Variables**:
    - `DB_HOST`: The hostname for the database service (`db`).
    - `DB_USERNAME`: The username for database access.
    - `DB_PASSWORD`: The password for the database user.
    - `DB_DATABASE`: The name of the database to connect to.
- **Ports**: The port configuration is commented out to avoid conflicts during scaling.

#### NGINX Web Server (nginx)

- **Build Context**: `./docker/nginx`
- **Configuration File**: `nginx.conf`
- **Ports**:
    - Maps port 80 from the container to port 8080 on the host machine.

### Output

The application can be accessed via the following URLs:

- `http://localhost:8080/api/stats`  
    Example Output:
    ```json
    {
        "status": "success",
        "contents": {
            "MemFree": 3297452,
            "MemAvailable": 6280532
        },
        "pid": 1,
        "hostname": "0d7ea6bda6ab",
        "counter": 0
    }
    ```

- `http://localhost:8080/api/books`  
    Example Output:
    ```json
    [
        {"id": 1, "title": "To Kill a Mockingbird", "author": "Harper Lee"},
        {"id": 2, "title": "1984", "author": "George Orwell"},
        {"id": 3, "title": "Pride and Prejudice", "author": "Jane Austen"},
        {"id": 4, "title": "The Great Gatsby", "author": "F. Scott Fitzgerald"}
    ]
    ```

- `http://localhost:8080/api/books/1`  
    Example Output:
    ```json
    {
        "id": 1,
        "title": "To Kill a Mockingbird",
        "author": "Harper Lee"
    }
    ```


## Changes Made

The original project was a full-stack application with the docker folder and files in the folder.

The changes made to the project are as follows:

1. Added a `.env` file to store environment variables for the database configuration.

2. Added the `docker-compose.yml` file to define the services and their configurations.

3. Modified the 'DockerFile' in the 'api' folder to copy from the current folder (.) rather than ./docker/api it was using before since the dockerfile is already in the folder.


### Running the Application

To run the application, follow these steps:

1. Clone the repository to your local machine.

2. Navigate to the project directory.

3. Create a `.env` file with the following content:
    ```plaintext
    MYSQL_ROOT_PASSWORD=root
    MYSQL_DATABASE=books
    MYSQL_USER=user
    MYSQL_PASSWORD=password
    ```

4. Run the following command to start the services:
    ```bash
    docker-compose up -d
    ```
    - This command will build the images and start the containers in detached mode.
    - up starts the services defined in the `docker-compose.yml` file.
    - -d runs the containers in the background.

5. Access the application using the following URLs:
    - `http://localhost:8080/api/stats`
    - `http://localhost:8080/api/books`
    - `http://localhost:8080/api/books/1`
