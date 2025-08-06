# Вордлисты для разведки и фаззинга

## Что такое вордлист?

**Вордлист** — это просто текстовый файл со словами, которые подставляются в запросы во время фаззинга. Используется для:
- перебора директорий и файлов
- подбора поддоменов
- перебора параметров и значений
- атаки на логины и пароли
- поиска уязвимостей (например, LFI)

---

## Основные источники вордлистов

### SecLists

Самая известная и большая коллекция вордлистов:
- https://github.com/danielmiessler/SecLists

Категории:
- `Discovery/Web-Content/` — директории, страницы, расширения
- `Passwords/` — словари паролей
- `Fuzzing/` — специфические для LFI, SQLi, SSTI и др.
- `Usernames/`, `DNS/`, `Payloads/`

Примеры:
```bash
/Discovery/Web-Content/directory-list-2.3-small.txt
/Discovery/Web-Content/web-extensions.txt
/Discovery/DNS/subdomains-top1million-5000.txt
/Fuzzing/LFI/LFI-Jhaddix.txt
```

### Weakpass

Проект, специализирующийся на **реальных паролях** из утечек:

- [https://weakpass.com](https://weakpass.com)

Особенности:

- Актуальные базы паролей (до десятков гигабайт)
- Разбиты по размерам, тематике, хешам
- Идеально подходят для атак по паролям (bruteforce, hashcat, Hydra и т.п.)
---

## Тематические вордлисты

Иногда стоит использовать **специализированные** списки, а не универсальные:

|Назначение|Пример вордлиста|
|---|---|
|LFI|`SecLists/Fuzzing/LFI/LFI-Jhaddix.txt`|
|Поддомены|`SecLists/Discovery/DNS/subdomains-top1million-5000.txt`|
|Расширения файлов|`SecLists/Discovery/Web-Content/web-extensions.txt`|
|Параметры запросов|`burp-parameter-names.txt` (из HTB/ffuf)|

---

## Генерация кастомных списков

Когда нужно подогнать вордлист под конкретную цель:

- Используй **cupp** (Common User Password Profiler)
- Сгенерируй **пермутации** с помощью `altdns`
- Парси сайты и создавай списки из слов контента (`cewl`, `linkfinder`, `wordhound` и др.)

---

## Полезные команды

Создание числового словаря:

```bash
for i in $(seq 1 1000); do echo $i >> ids.txt; done
```
