
Нет никакой причины, кроме вынужденной, чтобы приличному человеку копаться в такой субстанции, как 1C, тем не менее, Ansible скрипт для развертывания 1С Linux 8.3.21(22) + PostgresPro-13-1c на Debian 11 Bullseye по мотивам статьи [Единый дистрибутив 1С:Предприятие для Linux. Установка сервера](https://interface31.ru/tech_it/2021/10/edinyy-distributiv-1c-dlya-linux-server.html)

Дистриубутив платформы качаем на портале [1С ИТС](https://interface31.ru/version_file?nick=Platform83&ver=8.3.20.1710&path=Platform%5c8_3_20_1710%5cserver64_8_3_20_1710.tar.gz) и помещаем в папку `roles/1c/files/`.

В inventory.yaml прописываем целевые сервер(ы) 1c-app и 1c-db для сервера приложения и сервера БД соответственно. 

## Раскатка с нуля
```
ansible-playbook -i inventory.yaml playbook.yml
```
*NB!* 1С неадекватно восприниманет нижнее подчеркивание в названии баз и полей 
https://forum.infostart.ru/forum15/topic257394/

## Добавление баз
```
ansible-playbook playbook.yml -i inventory.yaml --tags onec-bases --extra-vars='{"onec_bases": [{"name":"test3", "description": "test"}]}'
```

Важно прописать параметры postgres в `pgpro/templates/postgresql.conf` в соответствии со сценарием использования и имеющимся железом. Можно воспользоваться [одним из готовых калькуляторов](http://pgconfigurator.cybertec.at/)


## Обновление платформы
Дистриубутив новой платформы качаем на портале и помещаем в папку `roles/1c/files/`. В inventory.yaml прописываем файл дистрибутива и целевую версию платформы

```
onec_version_name: 8.3.22.1603
onec_distribution_file: server64_8_3_22_1603.tar.gz
```
запускаем платформу

```
ansible-playbook playbook.yml -i inventory.yaml --tags update_platform 
```
сервисы запуска старой платформы будут удалены, но файлы самой платформы останутся. 

На клиентской части так же обновить платформу и [средства администрирования кластера](https://infostart.ru/1c/articles/1347969/)

