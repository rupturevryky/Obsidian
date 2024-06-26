## Введение 

Предустановленное в **kali** ПО.

**Nikto** - сканер с открытым исходным кодом для веб-серверов. Он выполняет комплексные тесты в отношении как сервера, так и приложения на этом сервере.

У него есть возможность проводить проверки на:

- Потенциально опасные файлы;
- Устаревшие версии серверного ПО и сервисов;
- Специфичные проверки, направленные на специфичные проблемы для серверов;
- странные и необычные заголовки;
- утечки **inode** через заголовок **ETag**;
- использование **WAF**.

Он поддерживает:

- HTTP/HTTPS протоколы,
- Сканирование через прокси — то есть можно поднять TOR - прокси и скрытно сканировать веб ресурсы.
- Поддерживает множество CMS.

NIKTO предустановлен в Kali Linux.

## **Принцип работы**

Имеет кучу параметров. Самый главный из них —`-h [HOST]`, задающий цель. Если цель умеет в **SSL**, стоит указать параметр `-ssl`. Также есть формат вывода ( `-Format`) и возможность работать с **Metasploit**. Инструмент немного устарел, но по-прежнему годится для разведки и взлома совсем уж безнадежных целей.

Сканирует одну цель за раз.

Так же он имеет возможность брутфорсить УЗ по любым продуктам, проверять на наличие БД и на **SQL Injection** (в случае если находит базу) и т.д.

У него нет ограничения по продуктам, он сканируется практически ВСЕ.

Помощь:
`man nikto` или `nikto --help`

### Сканирование вместе с Metasploit

Одна из лучших вещей в **Nikto** заключается в том, что вы можете просто экспортировать информацию, полученную при сканировании, в формат, который сможет прочитать **Metasploit**. Для этого используйте команды для выполнения сканирования, но добавьте к ним в конце флаги `-Format msf+`. Этот формат может помочь быстро сопоставить данные, полученные с помощью эксплойта.

```
nikto -h <IP or hostname> -Format msf+
```

## Использование Nikto на Kali Linux

- `-h` - указать имя хоста или IP адрес цели.

Этого уже может быть достаточно.