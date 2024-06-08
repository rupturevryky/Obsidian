***
## Навигация
> 1. [[#gobuster]]
> 2. [[#DIRB]]
> 3. [[#Dirsearch]]
> 4. [[#wfuzz]]
> 5. feroxbuster
***
## gobuster

Позволят брутфорсить url по ключевым словам. 
Предустановленная папка: `/usr/share/wordlists/dirbuster`

*Пример использования:*
```bush
gobuster dir -u http://10.10.2.13 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
*Пример для брутфорса sub-domain-а*
``` 
gobuster vhost -w /usr/share/wordlists/subdomains-top1million-5000.txt -u http://<url> --append-domain
```
или 
```
gobuster dns -w /usr/share/wordlists/dirb/subdomains-top1million-5000.txt -d <domain>
```
*man gobuster **EXAMPLES**:*
```bush
       gobuster dir ‐u https://mysite.com/path/to/folder ‐c ’session=123456’ ‐t 50 ‐w common‐files.txt  ‐x .php,.html

       gobuster dns ‐d mysite.com ‐t 50 ‐w common‐names.txt

       gobuster s3 ‐w bucket‐names.txt

       gobuster gcs ‐w bucket‐names.txt

       gobuster vhost ‐u https://mysite.com ‐w common‐vhosts.txt

       gobuster fuzz ‐u https://example.com?FUZZ=test ‐w parameter‐names.txt

       gobuster tftp ‐s tftp.example.com B‐w common‐filenames.txt
```
## DIRB

DIRB – сканер веб-контента, который ищет существующие и/или скрытые файлы на сервере. DIRB поставляется с набором словарей для атак, но вы также можете создавать и использовать собственные словари.

С помощью инструмента DIRB можно проводить аудит безопасности веб-приложений и находить специфические веб-объекты. Однако следует помнить, что DIRB не ищет уязвимости.

В практических упражнениях DIRB пригодится для поиска скрытой информации на пользовательских машинах.

### Как использовать DIRB?

Чтобы запустить DIRB на пользовательской машине, необходимо выполнить следующие шаги:

 В консоли введите команду:
```
dirb http://www.hacktory.lab/ wordlList.txt -X .php
```

где,

- `dirb` – исполняемый файл программы
- `http://www.hacktory.lab/` – URL цели
- `wordList.txt` – словари, по которым будет осуществлен поиск файлов (например, dict.txt)

**Опции:**

- `-o <файл\_для\_вывода>` – результат сканирования будет сохранен в файл на диске
- `-X <расширение> | -x <расширения\_файла>` – в сканировании будет выполнен поиск по расширениям

Полный список опций DIRB: [https://kali.tools/?p=108](https://kali.tools/?p=108)

**Примеры запуска:**

- `dirb http://www.hacktory.lab/` – поиск существующих и/или скрытых файлов и директорий
- `dirb http://www.hacktory.lab/ -X .html` – поиск файлов с разрешением .html
- `dirb http://www.hacktory.lab/ /usr/share/dirb/wordlists/apache.txt` – поиск файлов с использованием словаря apache.txt
## Dirsearch

Dirsearch – это простой инструмент командной строки для перебора директорий и файлов. Его можно найти на [Github](https://github.com/maurosoria/dirsearch).

Главное достоинство Dirsearch – удобный интегрированный словарь, имеющий хороший баланс между размером и эффективностью поиска. Он содержит стандартные пути, характерные для многих CMS и стеков технологий. Кроме того, Dirsearch умеет отличать настоящие страницы "200 ОК" от страниц "200 ОК" с текстом "страница не найдена".

Dirsearch похож по своему функционалу на DIRB, но он обладает более расширенными функциями и интерактивностью. Как и DIRB, Dirsearch может понадобиться в практических упражнениях для анализа директорий и поиска скрытых файлов.

### Как использовать Dirsearch?

Чтобы запустить Dirsearch на пользовательской машине, необходимо выполнить следующие шаги:

В консоли введите команду:
```
dirsearch.py --url|-u http://www.hacktory.lab --extension|-e txt
```

где,

- `dirsearch.py` – исполняемый файл программы
- `-u --url http://www.hacktory.lab/` - URL цели
- `-e --extension txt` – поиск файлов с расширением txt

Полный список опций и настроек Dirsearch: [https://kali.tools/?p=3189](https://kali.tools/?p=3189) .

**Примеры запуска:**

- `dirsearch.py -u http://www.hacktory.lab/directory/ -e txt` – поиск файлов с разрешением .txt
- `dirsearch.py -u http://www.hacktory.lab/ -e zip,rar,gz,tar,log,sql` – поиск файлов по нескольким разрешениям
- `dirsearch.py -u http://www.hacktory.lab/ -w dictionary.txt. -e php` – поиск файлов с разрешением .php по словарю dictionary.txt (-w --wordlist)
## wfuzz 
Позволят брутфорсить url по ключевым словам. 
Предустановленная папка: `/usr/share/wordlists/wfuzz/`

