## Challenge 4: Scaling the Application Using Docker

### Overview

This project builds on the previous Docker challenges and focuses on scaling a full-stack application. The application consists of three key services: a MariaDB database, a Node.js backend, and an NGINX web server. The primary objective of this challenge is to scale the Node.js service from a single instance to multiple instances using Docker Compose, thereby improving load balancing and redundancy.

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

### Scaling the Node.js Service

To handle increased traffic and ensure redundancy, the Node.js service can be scaled up from one instance to multiple instances using Docker Compose.

#### Steps to Scale the Application

- **Scaling the Node.js Service**:

    Use the following command to scale the Node.js service to three instances:

    ```bash
    docker-compose up --scale node-service=3 -d
    ```

    This command will start three instances of the node-service and balance the load between them.

- **Verify Scaling**:

    Access the application multiple times via `http://localhost:8080/api/stats` to verify that requests are being distributed across different instances. The hostname value in the response should vary, indicating that different instances are handling the requests.

- **Check Running Containers**:

    Use `docker-compose ps` to confirm that three instances of the node-service are running:

    ```bash
    docker-compose ps
    ```

    Example output:

    ```
    NAME                        IMAGE                     COMMAND                  SERVICE        CREATED          STATUS          PORTS
    challenge3-db-1             mariadb                   "docker-entrypoint.s…"   db             21 minutes ago   Up 20 minutes   0.0.0.0:3306->3306/tcp
    challenge3-nginx-1          challenge3-nginx          "/docker-entrypoint.…"   nginx          21 minutes ago   Up 20 minutes   0.0.0.0:8080->80/tcp
    challenge3-node-service-1   challenge3-node-service   "docker-entrypoint.s…"   node-service   21 minutes ago   Up 20 minutes   3000/tcp
    challenge3-node-service-2   challenge3-node-service   "docker-entrypoint.s…"   node-service   21 minutes ago   Up 20 minutes   3000/tcp
    challenge3-node-service-3   challenge3-node-service   "docker-entrypoint.s…"   node-service   21 minutes ago   Up 20 minutes   3000/tcp
    ```

### Accessing the Application

After scaling the application, you can continue to access it using the following URLs:

- **View System Stats**: `http://localhost:8080/api/stats`
- **View All Books**: `http://localhost:8080/api/books`
- **View a Specific Book**: `http://localhost:8080/api/books/1`

Each request to `/api/stats` should show a different hostname, indicating that the load is being distributed across the scaled instances.

### Conclusion

By scaling the Node.js service, the application is better equipped to handle increased load, offering improved performance and redundancy. This setup demonstrates the power of Docker Compose in managing and scaling microservices in a production-like environment.
