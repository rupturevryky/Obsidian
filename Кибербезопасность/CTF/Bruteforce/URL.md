***
## Навигация
> 1. [[#gobuster]]
> 2. [[#wfuzz]]
> 3. feroxbuster
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

## wfuzz 
Позволят брутфорсить url по ключевым словам. 
Предустановленная папка: `/usr/share/wordlists/wfuzz/`

