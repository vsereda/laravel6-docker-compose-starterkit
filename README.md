## Установка LARAVEL

добавим в /etc/hosts:
<p style="color: #F8F8F8; font-weight: bold;">  127.0.0.1	mylocal.org</p>

выполним:
<p style="color: #F8F8F8; font-weight: bold;"> git clone [имя этого репозитоия] mylocal </p>
<p style="color: #F8F8F8; font-weight: bold;"> cd mylocal/ </p>
<p style="color: #F8F8F8; font-weight: bold;"> cp .env.example .env </p>

настроим содержимое .env:

    APP_NAME=MY
    DB_HOST=db
    DB_DATABASE=my-db-name
    DB_USERNAME=my-db-username
    DB_PASSWORD=my-db-password
	APP_URL=https://mylocal.org

выполним:
<p style="color: #F8F8F8; font-weight: bold;"> 
cp ./docker-compose/nginx/conf.d/auth-research.conf.example ./docker-compose/nginx/conf.d/auth-research.conf
</p>

В зависимости от того, хотим дебажить или нет - копируем один из двух файлов:
<p style="color: #F8F8F8; font-weight: bold;">
cp ./docker/php/Dockerfile.example_debug ./docker/php/Dockerfile
</p>
<p style="color: #F8F8F8; font-weight: bold;"> 
cp ./docker/php/Dockerfile ./docker/php/Dockerfile
</p>

далее:
<p style="color: #F8F8F8; font-weight: bold;">
docker-compose up -d --build
</p>

<p style="color: #F8F8F8; font-weight: bold;">
docker-compose exec app composer install
</p>

<p style="color: #F8F8F8; font-weight: bold;">
docker-compose exec app php artisan key:generate
</p>

<p style="color: #F8F8F8; font-weight: bold;">
docker-compose exec app openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /var/www/docker-compose/nginx/ssl/private/nginx-selfsigned.key -out /var/www/docker-compose/nginx/ssl/certs/nginx-selfsigned.crt
</p>

примеры ответов, которые надо будет дать:

	Country Name (2 letter code) [AU]:UA
	State or Province Name (full name) [Some-State]:Kharkiv
	Locality Name (eg, city) []:Kharkiv
	Organization Name (eg, company) [Internet Widgits Pty Ltd]:Bouncy Castles, Inc.
	Organizational Unit Name (eg, section) []:Ministry of Water Slides
	Common Name (e.g. server FQDN or YOUR name) []:mylocal.org
	Email Address []:vladimir45822@gmail.com

далее:
<p style="color: #F8F8F8; font-weight: bold;">
docker-compose exec app openssl dhparam -out /var/www/docker-compose/nginx/dhparam/dhparam.pem 4096
</p>
<p style="color: #F8F8F8; font-weight: bold;">
docker-compose restart nginx
</p>
<p style="color: #F8F8F8; font-weight: bold;">
docker-compose ps
</p>

Должны увидеть что все 3 сервиса ок (точнее там уже больше сервисов, все должны быть Up):

    Name                 Command               State                                   Ports
    ---------------------------------------------------------------------------------------------------------------------------
    laravel-app     docker-php-entrypoint php-fpm    Up      9000/tcp
    laravel-db      docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp,:::3306->3306/tcp, 33060/tcp
    laravel-nginx   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:443->443/tcp,:::443->443/tcp, 0.0.0.0:80->80/tcp,:::80->80/tcp

далее вместо того чтобы каждый раз писать <span  style="color: #F8F8F8; font-weight: bold;">docker-compose exec app</span> можно использовать для входа в контейнер:
<p style="color: #F8F8F8; font-weight: bold;">
docker exec -it laravel-app bash
</p>

должены быть выполнены миграции и посев данных:
<p style="color: #F8F8F8; font-weight: bold;">
php artisan migrate --seed
</p>
