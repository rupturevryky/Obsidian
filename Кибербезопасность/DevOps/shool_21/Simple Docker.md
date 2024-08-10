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