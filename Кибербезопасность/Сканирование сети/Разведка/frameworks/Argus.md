### Что это?​

**Argus — это инструмент на основе Python, созданный для оптимизации процесса сбора данных и разведки. С его интуитивно понятным интерфейсом и мощным набором модулей Argus обеспечивает эффективное и продуктивное исследование сетей, веб-приложений и конфигураций безопасности.** **Неважно, проводите ли вы исследования, выполняете оценку безопасности с соответствующим разрешением или просто проявляете интерес к сетевой инфраструктуре — Argus предоставляет вам все необходимые сведения в одном месте.**  
  

### Установка и использование​

Скачиваем репозиторий и устанавливаем нужные модули Python:
```bash
git clone https://github.com/jasonxtn/argus.git
cd argus
pip install -r requirements.txt
```
Запускаем исполняемый файл:
```
python argus.py
```
Как можно наблюдать, инструмент содержит в себе 54 отдельных скрипта которые можно запустить как в комплексе, так и отдельно. Сами эти скрипты поделены на 3 раздела:  

1. **Инструменты сети и инфраструктуры** - Эти инструменты помогут вам собрать данные о сети, раскрыв важные данные о серверах, IP-адресах, DNS-записях и многое другое.
2. **Инструменты анализа веб-приложений** - Эти модули сосредоточены на понимании структуры и безопасности веб-приложений.
3. **Инструменты разведки безопасности и угроз** - Модули безопасности в Argus предназначены для оценки защиты цели и сбора информации об угрозах.

Классной фишкой инструмента является его максимальная простота использования и наибольший результат. Как можем наблюдать под каждым пунктом имеется дополнительный параметр который запускает все скрипты конкретного раздела, а на два нуля можно запустить все разделы сканирования и всё это будет направлено на целевой сайт.  
  
Для наибольшей эффективности предлагаю запустить этот инструмент со всеми категориями и плюсом добавим API из Shodan и VirusTotal, это делается в файле `config/settings.py`.  
  
Рассмотрим что из наиболее интересного для нас есть:
IP-адрес сайта.  
![[Pasted image 20250130180050.png]]  
Открытые порты.  
![[Pasted image 20250130180106.png]]
Подробнейший отчёт из WHOIS Lookup.  
![[Pasted image 20250130180119.png]]
Ссылки на слепки сайта из веб архива.  
![[Pasted image 20250130180128.png]]
CMS сайта.  
![[Pasted image 20250130180140.png]]
Файлы которые имеются на сайте.  
![[Pasted image 20250130180159.png]]
Поиск электронной почты.  
![[Pasted image 20250130180210.png]]
Ссылки на социальные сети, какие технологии используются(например какой веб сервер).  
![[Pasted image 20250130180221.png]]
Какие имеются меры безопасности на сайте.  
 ![[Pasted image 20250130180242.png]] 
Версия SSL сертификата.  
![[Pasted image 20250130180254.png]]
И пожалуй самое вкусное это перечень подобранных CVE под службы запущенные на сайте.  
![[Pasted image 20250130180306.png]]