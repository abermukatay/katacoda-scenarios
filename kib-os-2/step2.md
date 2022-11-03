Для работы по протоколу TLS нашей АС нужен сертификат. В рамках тестового задания будем использовать самоподписанный
сертификат. Для реальных АС должен использоваться сертификат, подписанный УЦ Банка. Отличия будут отмечены в описаниях
примеров.

Формируем самоподписанный сертификат. Уточните параметры генерируемого сертификата в файле

`./certs/req.cnf`{{open}}

Значение commonName можно заменить на произвольное, значения alt_names - на доменные имена,
которые вы будете использовать далее по заданию. Так как сертификат самоподписанный, его можно переделать во время
выполнения любого задания

`openssl req -new -x509 -newkey rsa:2048 -sha256 -nodes -keyout ./certs/key.pem -days 365 -out ./certs/crt.pem -config ./certs/req.cnf`{{execute}}

На выходе получим следующие файлы:

* ./certs/crt.pem - открытая часть сертификата.
* ./certs/key.pem - закрытая часть сертификата (первичный ключ). Ее запрещается передавать. В случае компрометации нужно
  перевыпускать сертификат

Формируем Secret для Openshift. Secret должен состоять из трех файлов:

* Закрытой части
* Открытой части
* Файла с цепочками УЦ. Так как мы используем самоподписанный сертификат, используем его вместо цепочек УЦ

`oc create secret generic ingress-certs --from-file=key.pem=./certs/key.pem --from-file=crt.pem=./certs/crt.pem --from-file=ca.pem=./certs/crt.pem -o yaml --dry-run --validate > ./certs/conf.yml;
oc apply -f ./certs/conf.yml`{{execute}}