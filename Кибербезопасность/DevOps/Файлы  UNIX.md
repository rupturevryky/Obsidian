`/etc/passwd` — текстовый файл, содержащий список учетных записей пользователей. В него можно добавить своего пользователя с отличным от root именем, но с тем же UID:  

![[etc-passwd.png]]

`/etc/shadow` - содержит список пользователей и хеши их паролей в системе.
***
`/etc/hosts` - файл управления названиями активных служб на своих IP адресах. В рамках CTF, если учебная машина расположена на адресе, например, `10.10.1.1`, можно добавить ей имя домена `<name>.thm`