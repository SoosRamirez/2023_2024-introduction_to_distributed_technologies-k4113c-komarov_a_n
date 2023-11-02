University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2023/2024\
Group: K4113c\
Author: Komarov Alexey Nikolaevich\
Lab: Lab1\
Date of create: 20.09.2023\
Date of finished: 31.09.2023\

# Лабораторная работа №1 "Установка Docket, Minikube, первый Manifest"

## Описание
   Это первая лабораторная работа в которой вы сможете протестировать Docker, установить Minikube и развернуть свой первый "под".

## Цель работы:
   Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".

## Ход работы:
   В процессе выполнения лабораторной работы были выпонены следующие шаги:
### 1. Установлен и зупущен Manikube. Docker уже был установлен ранее.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_1)

### 2. Далее был создан манифест для запуска пода.

   Вот описание того, за что отвечает каждое поле:
   * `metadata` содержит метаданные ресурса, такие как имя и метки (labels), которые помогают в организации и идентификации ресурса.
   * `spec` содержит конфигурацию ресурса, включая параметры, которые определяют его поведение и характеристики.
     *  `containers` содержит описывание каждого контейнера в данном поде.
  
Контейнер был назван `vault`, созданный на основе [образа](https://hub.docker.com/_/vault/) image версии 1.13.3.

### 3. Создание пода.

Чтобы создать под в нашем кластере, необходимо выполнить следующую команду.
```
minikube kubectl -- apply -f manifest.yaml
```
![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_2)

### 4. Создание сервиса для доступа к поду.

Далее следует создать сервис для доступа к этому контейнеру, самый просто вариант - это выполнить команду:
```
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_3)

Так же необходимо настроить проброс портов. Для этого можно воспользоваться следующей командой.
```
minikube kubectl -- port-forward service/vault 8200:8200
```
![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_4)

Теперь подключиться к сервису можно пройдя по адресу [http://localhost:8200](http://localhost:8200).

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_5)

Ответ на вопрос, что сделали команды:
  * `kubectl apply` создает объект kubernetes согласно описанию в переданном файле, URL или из stdin.
  * `kubectl expose` создает новый сервис из указанного объекта по названию. Тип NodePort позволяет открыть сервиса на указанном порту, что позволит подключаться к нему "извне".
  * `kubectl port-forward LOCAL_PORT:REMOTE_PORT` включает проброс портов к поду. Это значит, что все подключения к localhost:LOCAL_PORT будут отправлены на сервис, который слушаеь REMOTE_PORT.

### 5. Аутентификация на сервисе.

Как было сказано, токен для подключения хранится в логах. Получить логи сервиса можно при помощи команды `kubectl logs <название пода>`

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_6)

Данный токен был введен в соответствующее поле на сервисе, был выполнен вход.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_7)


## Вывод
Таким образом, были изучены инструменты Minikube и Docker, развернут свой первый "под".

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/media/lab1_8)
