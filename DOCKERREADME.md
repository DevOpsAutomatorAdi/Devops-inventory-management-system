🚀 DevOps Inventory Management System (Laravel + MySQL)

A production-style Laravel Inventory Management System deployed using Docker without docker-compose, focusing on manual container orchestration, networking, and persistence — exactly how real DevOps environments work.

🎯 Goal (What We Are Implementing)

This project demonstrates real DevOps practices using Docker:

❌ No docker-compose

✅ Custom Docker bridge network

✅ Separate MySQL container

✅ Separate Laravel application container

✅ Persistent MySQL data using Docker volumes

✅ Configuration via .env variables

✅ Production-like setup (resume & interview ready)

🏗️ Architecture Overview

🔄 Application Flow
Browser → Laravel Container → MySQL Container

1️⃣ Clone the Repository
git clone https://github.com/DevOpsAutomatorAdi/Devops-inventory-management-system.git
cd Devops-inventory-management-system

2️⃣ Create Docker Network (MANDATORY)

Containers communicate using this custom network.

docker network create inventory-net

3️⃣ Create Docker Volume for MySQL Persistence

Ensures database data is not lost if containers restart.

docker volume create inventory_mysql_data

4️⃣ Run MySQL Container (NO docker-compose)
docker run -d \
  --name inventory-mysql \
  --network inventory-net \
  -v inventory_mysql_data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=root123 \
  -e MYSQL_DATABASE=inventory_db \
  -e MYSQL_USER=inventory_user \
  -e MYSQL_PASSWORD=inventory_pass \
  -p 3306:3306 \
  mysql:5.7


✅ MySQL is now persistent and networked

5️⃣ Configure Laravel .env

Create the environment file:

cp .env.example .env

🔧 Update ONLY these values
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


🚨 Important

❌ Do NOT use localhost

✅ Use MySQL container name: inventory-mysql

6️⃣ Laravel Dockerfile (Production-Ready)
FROM php:8.2-cli

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y \
    git curl unzip \
    libpng-dev libonig-dev libxml2-dev libzip-dev libicu-dev \
    libfreetype6-dev libjpeg62-turbo-dev libwebp-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install pdo_mysql mbstring gd xml zip intl

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

COPY . .

RUN composer install --no-interaction --no-dev --optimize-autoloader

RUN chown -R www-data:www-data storage bootstrap/cache

EXPOSE 8000

CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]

7️⃣ Build Laravel Docker Image
docker build -t inventory-laravel-app .

8️⃣ Run Laravel Application Container
docker run -d \
  --name inventory-app \
  --network inventory-net \
  -p 8000:8000 \
  inventory-laravel-app

9️⃣ Generate Laravel Application Key
docker exec -it inventory-app php artisan key:generate

🔟 Run Database Migrations & Seeders
docker exec -it inventory-app php artisan migrate:fresh --seed

1️⃣1️⃣ Create Storage Symlink
docker exec -it inventory-app php artisan storage:link

1️⃣2️⃣ Access the Application

🌐 Open in browser:

http://<EC2-PUBLIC-IP-or-localhost>:8000

🔐 Default Admin Credentials
Email: admin@admin.com
Password: password

1️⃣3️⃣ Verify MySQL Persistence (IMPORTANT)

Stop and remove MySQL container:

docker stop inventory-mysql
docker rm inventory-mysql


Re-run MySQL with the same volume:

docker run -d \
  --name inventory-mysql \
  --network inventory-net \
  -v inventory_mysql_data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=root123 \
  mysql:5.7


✅ Database data is still present → Persistence confirmed

🧠 Key DevOps Concepts Demonstrated

Docker container networking

Persistent storage using Docker volumes

Manual container orchestration (no docker-compose)

Laravel production configuration

MySQL containerized deployment

Real-world CI/CD compatible workflow

📌 Resume-Ready Project Description

DevOps Inventory Management System

Deployed a Laravel 10 application using Docker without docker-compose

Configured MySQL container with persistent storage using Docker volumes

Implemented Docker bridge networking for secure service communication

Automated Laravel setup with migrations, seeders, and environment configuration

Built production-ready Docker images aligned with CI/CD pipelines

📄 License

This project is licensed under the MIT License.
