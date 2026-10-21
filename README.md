# Dockerize GeStock

Docker setup for running the **[GeStock](https://github.com/SifeYassine/gestock)** Laravel REST API in containers, with a MySQL database and phpMyAdmin, all orchestrated by Docker Compose.

## Services

| Service      | Image                    | Container        | Host Port | Description |
| ------------ | ------------------------ | ---------------- | --------- | ----------- |
| `app`        | Built from `Dockerfile`  | `laravel-docker` | **8080**  | GeStock API on PHP 8.2 + Apache |
| `mysql_db`   | `mysql:latest`           | `mysql_db`       | **3306**  | MySQL database (`GeStock`) with a persistent volume |
| `phpmyadmin` | `phpmyadmin/phpmyadmin`  | `phpmyadmin`     | **8081**  | Web UI for the database |

### What the image does

The `Dockerfile` builds on `php:8.2-apache` and:

1. Enables Apache `mod_rewrite`
2. Installs system libraries and the `pdo`, `pdo_mysql`, and `zip` PHP extensions
3. Copies the GeStock source and installs Composer dependencies
4. Points Apache's document root to Laravel's `public/` directory
5. Sets the correct permissions on `storage/` and `bootstrap/cache/`

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and Docker Compose

## Getting Started

### 1. Clone this repo and the GeStock app

The GeStock source must sit in a `GeStock/` folder next to the `Dockerfile`:

```bash
git clone https://github.com/SifeYassine/dockerize-gestock.git
cd dockerize-gestock
git clone https://github.com/SifeYassine/gestock.git GeStock
```

```
dockerize-gestock/
├── Dockerfile
├── docker-compose.yaml
├── .dockerignore
└── GeStock/          # Laravel app (cloned above)
```

### 2. Configure `GeStock/.env`

```bash
cp GeStock/.env.example GeStock/.env
```

Point the database connection at the Compose MySQL service:

```env
DB_CONNECTION=mysql
DB_HOST=mysql_db
DB_PORT=3306
DB_DATABASE=GeStock
DB_USERNAME=root
DB_PASSWORD=rootpassword
```

### 3. Build and start the containers

```bash
docker compose build
docker compose up -d
```

### 4. Initialize the application

```bash
docker exec -it laravel-docker php artisan key:generate
docker exec -it laravel-docker php artisan migrate
```

### 5. Access

- **API**: <http://localhost:8080>
- **phpMyAdmin**: <http://localhost:8081>

### Stopping

```bash
docker compose down        # stop containers
docker compose down -v     # stop and delete the database volume
```

## Notes

- `docker-compose.yaml` mounts `./Gestock` (lowercase *s*) as a volume while the `Dockerfile` copies `GeStock/`. On case-sensitive systems (Linux), make both names match.
- The database credentials (`rootpassword`) are for **local development only**. Change them before deploying anywhere public.

## Related Repositories

- [gestock](https://github.com/SifeYassine/gestock): Laravel REST API
- [gestock-frontend](https://github.com/SifeYassine/gestock-frontend): Vue.js frontend

## Author

**Sifeddine Yassine** · [@SifeYassine](https://github.com/SifeYassine)
