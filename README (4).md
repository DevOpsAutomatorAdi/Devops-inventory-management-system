# 🚀 DevOps Inventory Management System (Laravel + MySQL)

A **production-style Laravel Inventory Management System** deployed using **Docker without docker-compose**, focusing on **manual container orchestration**, networking, and persistence — exactly how real DevOps environments work.

---

## 🎯 Goal (What We Are Implementing)

This project demonstrates **real DevOps practices** using Docker:

- ❌ **No docker-compose**
- ✅ Custom Docker **bridge network**
- ✅ Separate **MySQL container**
- ✅ Separate **Laravel application container**
- ✅ **Persistent MySQL data** using Docker volumes
- ✅ Configuration via `.env` variables
- ✅ **Production-like setup** (resume & interview ready)

---

## 🏗️ Architecture Overview

![Docker Architecture](https://martinjoo.dev/_nuxt/img/docker_overview.a2c2b86.png)

![Laravel Docker MySQL](https://repository-images.githubusercontent.com/309769351/1c0dfc80-1def-11eb-9e5c-641da3e3c9b4)

![Docker Volumes](https://i0.wp.com/codeblog.dotsandbrackets.com/wp-content/uploads/2017/03/docker-volumes.jpg?fit=995%2C328&ssl=1)

### 🔄 Application Flow

```
Browser → Laravel Container → MySQL Container
```

---

## 1️⃣ Clone the Repository

```bash
git clone https://github.com/DevOpsAutomatorAdi/Devops-inventory-management-system.git
cd Devops-inventory-management-system
```

---

## 2️⃣ Create Docker Network (MANDATORY)

```bash
docker network create inventory-net
```

---

## 3️⃣ Create Docker Volume for MySQL Persistence

```bash
docker volume create inventory_mysql_data
```

---

## 4️⃣ Run MySQL Container (NO docker-compose)

```bash
docker run -d   --name inventory-mysql   --network inventory-net   -v inventory_mysql_data:/var/lib/mysql   -e MYSQL_ROOT_PASSWORD=root123   -e MYSQL_DATABASE=inventory_db   -e MYSQL_USER=inventory_user   -e MYSQL_PASSWORD=inventory_pass   -p 3306:3306   mysql:5.7
```

---

## 5️⃣ Configure Laravel `.env`

```bash
cp .env.example .env
```

```env
APP_NAME=InventorySystem
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=inventory-mysql
DB_PORT=3306
DB_DATABASE=inventory_db
DB_USERNAME=inventory_user
DB_PASSWORD=inventory_pass
```

---

## 6️⃣ Laravel Dockerfile (Production-Ready)

```dockerfile
FROM php:8.2-cli

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y     git curl unzip     libpng-dev libonig-dev libxml2-dev libzip-dev libicu-dev     libfreetype6-dev libjpeg62-turbo-dev libwebp-dev     && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp     && docker-php-ext-install pdo_mysql mbstring gd xml zip intl

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
COPY . .

RUN composer install --no-interaction --no-dev --optimize-autoloader
RUN chown -R www-data:www-data storage bootstrap/cache

EXPOSE 8000

CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
```

---

## 7️⃣ Build & Run

```bash
docker build -t inventory-laravel-app .
```

```bash
docker run -d   --name inventory-app   --network inventory-net   -p 8000:8000   inventory-laravel-app
```

---

## 8️⃣ Laravel Post Setup

```bash
docker exec -it inventory-app php artisan key:generate
docker exec -it inventory-app php artisan migrate:fresh --seed
docker exec -it inventory-app php artisan storage:link
```

---

## 🌐 Access Application

```
http://<EC2-PUBLIC-IP>:8000
```

**Default Credentials**

```
Email: admin@admin.com
Password: password
```

---

## 📌 Resume-Ready Description

**DevOps Inventory Management System**
- Dockerized Laravel app without docker-compose
- MySQL container with persistent Docker volumes
- Manual container networking
- Production-ready Docker builds
- CI/CD compatible setup

---

## 📄 License

MIT License
