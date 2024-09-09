## Part 1. Готовый докер

**Установить докер**:
``` shell
sudo apt install docker docker-compouse
```

**Скачивание официального образа Nginx**:
```shell
docker pull nginx
```
![[./assets_D05_SimpleDocker/Pasted image 20240809101541.png]]

**Проверка наличия образа**:
```shell
docker images
```
![[./assets_D05_SimpleDocker/Pasted image 20240809101627.png]]

**Запуск Docker-образа**:
```shell
docker run -d nginx
```
![[./assets_D05_SimpleDocker/Pasted image 20240809101708.png]]

**Проверка запущенных контейнеров**:
```shell
docker ps
```
![[./assets_D05_SimpleDocker/Pasted image 20240809101734.png]]

**Получение информации о контейнере**:
``` shell
docker inspect [container_id|container_name]
```
   ![[./assets_D05_SimpleDocker/Pasted image 20240809103104.png]]

   ![[./assets_D05_SimpleDocker/Pasted image 20240809103130.png]]

   ![[./assets_D05_SimpleDocker/Pasted image 20240809103154.png]]

   ![[./assets_D05_SimpleDocker/Pasted image 20240809103229.png]]

   ![[./assets_D05_SimpleDocker/Pasted image 20240809103247.png]]
   
   Отсюда:
   1. размер контейнера: `187603368` **байт** = - **`179.0` МБ**;
      
      ![[./assets_D05_SimpleDocker/Pasted image 20240809104345.png]]
   2. список замапленных портов: `80/tcp` основного хоста не мапится ни с каким другим портом;
      
      ![[./assets_D05_SimpleDocker/Pasted image 20240809103537.png]]
   3. ip контейнера: `172.17.0.2/16`
      
      ![[./assets_D05_SimpleDocker/Pasted image 20240809103719.png]]

**Остановка Docker-контейнера**:
``` shell
docker stop [container_id|container_name]
```
![[./assets_D05_SimpleDocker/Pasted image 20240809104528.png]]

![[./assets_D05_SimpleDocker/Pasted image 20240809104539.png]]

**Проверка остановки контейнера**:
```shell
docker ps
```
![[./assets_D05_SimpleDocker/Pasted image 20240809104554.png]]

**Запуск Docker с замапленными портами**:
```shell
docker run -d -p 80:80 -p 443:443 nginx
```
![[./assets_D05_SimpleDocker/Pasted image 20240809104700.png]]

**Проверка доступности стартовой страницы Nginx**:
> Открыть браузер и перейдите по адресу `http://localhost:80`. Там должна быть стартовая страница Nginx.

> ![[./assets_D05_SimpleDocker/Pasted image 20240809105059.png]]

**Перезапуск Docker-контейнера**:
``` shell
docker restart [container_id|container_name]
```

![[./assets_D05_SimpleDocker/Pasted image 20240809105207.png]]

---

## Part 2. Операции с контейнером

1. **Чтение конфигурационного файла nginx.conf внутри контейнера**:
   ```shell
   docker exec -it <container_id> cat /etc/nginx/nginx.conf
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810174809.png]]

2. **Создание файла nginx.conf на локальной машине с настройкой по пути _/status_ отдачи страницы статуса сервера nginx**:
   
   ![[./assets_D05_SimpleDocker/Pasted image 20240810180258.png]]

3. **Копирование файла nginx.conf внутрь контейнера**:
   ``` shell
   docker cp nginx.conf <container_id>:/etc/nginx/nginx.conf
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810175454.png]]

4. **Перезапуск nginx внутри контейнера**:
   ```shell
   docker exec -it <container_id> nginx -s reload
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810180135.png]]

5. **Проверка страницы статуса**:
   
   ![[./assets_D05_SimpleDocker/Pasted image 20240810181451.png]]

6. **Экспортируй контейнер в файл `container.tar` через команду `export`**:
   ```shell
   docker export <container_id> > container.tar
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810181706.png]]

7. **Останови контейнер**:
   ```shell
   docker stop <container_id>
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810181803.png]]

8. **Удали образ через `docker rmi [image_id|repository]`, не удаляя перед этим контейнеры**:
   ```shell
   docker rmi <image_id|repository>
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810182103.png]]
   
   Здесь `-f` - force.

9. **Удали остановленный контейнер**:
   ```shell
   docker rm <container_id>
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810182332.png]]

10. **Импортируй контейнер обратно через команду `import`**:
   ```shell
 cat container.tar | docker import - <new_image_name>  
 ```
 ![[./assets_D05_SimpleDocker/Pasted image 20240810182548.png]]

11. **Запусти импортированный контейнер**:
   ```shell
   docker run -d -p 80:80 <new_image_name>
   ```
   ![[./assets_D05_SimpleDocker/Pasted image 20240810185725.png]]

12. **Проверь, что по адресу `localhost:80/status` отдается страничка со статусом сервера nginx**:
   
   ![[./assets_D05_SimpleDocker/Pasted image 20240810185705.png]]

---

## Part 3. Мини веб-сервер

**`hello_page.c`:**

![[./assets_D05_SimpleDocker/Pasted image 20240811222502.png]]

> Установка зависимостей:
>
> ```shell
> sudo apt install libfcgi-dev
> sudo apt install spawn-fcgi
> ```

> Компиляция файла:
> 
> ```shell
> gcc hello_page.c -o hello_page -lfcgi 
> ```

**Запуск мини-сервер через `spawn-fcgi` на порту `8080`:**
``` shell
spawn-fcgi -p 8080 ./hello_page 
```
![[./assets_D05_SimpleDocker/Pasted image 20240811222550.png]]

**Конфигурация Nginx:**

![[./assets_D05_SimpleDocker/Pasted image 20240811222612.png]]
```shell
sudo nginx -s reload
```

Браузер:

![[./assets_D05_SimpleDocker/Pasted image 20240811222639.png]]

---

## Part 4. Свой докер

```dockerfile
# Используем базовый образ с установленным Nginx
FROM nginx

# Копируем исходный код приложения в контейнер
COPY ./server/hello_page.c /usr/src/hello.c
COPY ./server/start_server.sh /usr/src/start_server.sh
# Копируем конфигурационный файл nginx внутрь контейнера
COPY ./nginx/nginx.conf /etc/nginx/nginx.conf

# Устанавливаем необходимые пакеты для сборки FastCGI приложения
RUN apt-get update && apt-get install -y gcc spawn-fcgi libfcgi-dev

# Запуск
ENTRYPOINT ["bash", "/usr/src/start_server.sh"]
```

```nginx.conf
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    #include /etc/nginx/conf.d/*.conf;  # this is commented (!)

    server {
        listen 81;  # http://nginx.org/en/docs/http/ngx_http_core_module.html#listen
        location / {
            fastcgi_pass localhost:8080;  # https://nginx.org/ru/docs/http/ngx_http_fastcgi_module.html#fastcgi_pass
        }
        location /status {
            stub_status on;
        }
    }
}

```

Сборка **Docker** образа:
> Перейти в директорию, содержащую `Dockerfile` и папки `nginx`, `server`
```shell
#!/usr/bin/env bash

docker build . -t server:v2

docker images

docker run --rm -d -p 80:81 -v ./nginx:/etc/nginx --name server2 server:v2

printf "The sever is up. Press <Enter> to stop it.\n"
read -p "Time to stop? " -r

docker stop server2
```

Эта команда запускает контейнер в фоновом режиме (`-d`), маппит порт 80 на хосте к порту 80 в контейнере (`-p 80:80`), и маппит текущую папку `./nginx` в папку `/etc/nginx` внутри контейнера.

![[./assets_D05_SimpleDocker/Pasted image 20240901120653.png]]

**Проверка доступа к серверу.**

Теперь можно проверить доступность сервера на `http://localhost:80`:
- Перейдите по `http://localhost:80` в браузере, чтобы увидеть страницу "Hello World!".
- Перейдите по `http://localhost:80/status` для получения статуса Nginx.

![[./assets_D05_SimpleDocker/Pasted image 20240901120900.png]]

![[./assets_D05_SimpleDocker/Pasted image 20240810185705.png]]

**Изменение конфигурации Nginx.**

Чтобы внести изменения в конфигурацию Nginx, отредактируйте файл `./nginx/nginx.conf`. После сохранения файла, перезапустите контейнер:
```shell
docker restart <container_id>
```

---

## Part 5. **Dockle**

Проверка до исправления:

![[./assets_D05_SimpleDocker/Pasted image 20240907144931.png]]

Исправление:
```dockerfile
# Используем базовый образ Debian
FROM debian:bookworm-slim

# Обновляем пакеты и устанавливаем необходимые зависимости, включая NGINX
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    nginx curl gcc spawn-fcgi libfcgi-dev build-essential && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    useradd mini_serv; \
    chown -R mini_serv:mini_serv /etc/nginx/nginx.conf; \
    chown -R mini_serv:mini_serv /var/cache/nginx; \
    chown -R mini_serv:mini_serv /home/; \
    chown -R mini_serv:mini_serv /var/log/nginx /var/lib/nginx; \
    touch /var/run/nginx.pid && chown -R mini_serv:mini_serv /var/run/nginx.pid; \
    chmod u-s /usr/bin/gpasswd; \
    chmod u-s /usr/bin/chsh; \
    chmod u-s /usr/bin/chfn; \
    chmod g-s /usr/bin/expiry; \
    chmod u-s /usr/bin/passwd; \
    chmod g-s /sbin/unix_chkpwd; \
    chmod g-s /usr/bin/chage; \
    chmod g-s /usr/bin/wall; \
    chmod u-s /bin/umount; \
    chmod u-s /bin/mount; \
    chmod u-s /usr/bin/newgrp; \
    chmod u-s /bin/su; \
    chmod u-s /usr/bin/chsh;

WORKDIR /home/
# Копируем файлы
COPY ./hello_page.c ./hello.c
COPY ./start_server.sh .
COPY ./nginx.conf /etc/nginx/nginx.conf

# Переключаемся на пользователя mini_serv для запуска NGINX
USER mini_serv

# Определяем команду запуска
ENTRYPOINT ["bash", "./start_server.sh"]
```

``` nginx.conf
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    #include /etc/nginx/conf.d/*.conf;  # this is commented (!)

    server {
        listen 81;  # http://nginx.org/en/docs/http/ngx_http_core_module.html#listen
        location / {
            fastcgi_pass localhost:8080;  # https://nginx.org/ru/docs/http/ngx_http_fastcgi_module.html#fastcgi_pass
        }
        location /status {
            stub_status on;
        }
    }
}
```
```start_server.sh
#!/usr/bin/env bash


gcc ./hello.c -l fcgi -o ./fcgi_server
spawn-fcgi -p 8080 ./fcgi_server
nginx -g 'daemon off;'
nginx -s reload

```

Итог:

![[./assets_D05_SimpleDocker/Pasted image 20240907172833.png]]

(команды)
``` shell
docker build . -t server:v3
```
```shell
dockle -ak NGINX_GPGKEY -ak NGINX_GPGKEY_PATH server:v3
```
```shell
docker run --rm -it -d -p 80:81 --name server3 server:v3
```
```shell
docker stop server3
```

---

## Part 6. Базовый **Docker Compose**

```docker_compose.yml
version: "3.9"

services:
  mini_serv:
    image: server:v4
    build: ../part_5
    container_name: server4

  nginxer:
    image: nginx:latest
    container_name: nginxer
    ports:
      - 80:8080
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
```
```nginx.conf
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    #include /etc/nginx/conf.d/*.conf;  # this is commented (!)

    server {
        listen 8080;
        location / {
            proxy_pass http://server4:81;
        }
        location /status {
            stub_status on;
        }
    }
}
```

Запуск сборки:

``` shell
docker-compose build
```
```shell
docker-compose up
```

Проверка:

![[./assets_D05_SimpleDocker/Pasted image 20240909162317.png]]