University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2023/2024\
Group: K4113c\
Author: Komarov Alexey Nikolaevich\
Lab: Lab2\
Date of create: 20.09.2023\
Date of finished: 31.09.2023\

# Лабораторная работа №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса."

## Описание
   В данной лабораторной работе вы познакомитесь с развертыванием полноценного веб сервиса с несколькими репликами.

## Цель работы:
   Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.

## Ход работы:
   В процессе выполнения лабораторной работы были выпонены следующие шаги:
### 1. Был создан манифест deployment.

  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: app
    labels:
      app: app
  spec:
    replicas: 2
    selector:
      matchLabels:
        app: app
    template:
      metadata:
        labels:
          app: app
      spec:
        containers:
        - name: itdt-contained-frontend
          image: ifilyaninitmo/itdt-contained-frontend:master
          ports:
          - containerPort: 3000
            name: http
          env:
            - name: REACT_APP_USERNAME
              value: 'AlexKK'
            - name: REACT_APP_COMPANY_NAME
              value: 'ITMO UNIVERSITY'
  ```
  В этом манифесте:
  * `apiVersion` и `kind` определяют тип ресурса как `Deployment`.
  * `metadata` определяет имя ресурса (в данном случае app).
  * `replicas` устанавливает количество реплик (в данном случае 2).
  * `selector` и `template` определяют, какие поды будут управляться этим Deployment. Мы используем метку app: app для связи с подами.
  * Внутри `template` мы определяем контейнер с именем my-container, указываем образ, и задаем переменные окружения с помощью env.

  После создания этого Deployment в Kubernetes, он автоматически создаст две реплики контейнера ifilyaninitmo/itdt-contained-frontend:master с указанными переменными окружения.

### 2. Был создан манифест service.

  ```  
  apiVersion: v1
  kind: Service
  metadata:
    name: app
  spec:
    type: NodePort
    ports:
      - port: 3000
        targetPort: 3000
        protocol: TCP
        name: http
    selector:
      app: app
```

### 3. Запуск и подключени.

Для запуска необходимо выполнить следующую команду
`minikube kubectl -- apply -f manifest.yaml`

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_1.png)

Чтобы проверить, что все успещно создалось, можно посмотреть рабочие deployments, replicaSets и pods.

```
minikube kubectl -- get deployments
minikube kubectl -- get rs
minikube kubectl -- get pods 
```

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_2.png)
![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_3.png)
![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_4.png)

Далее настраиваем проброс портов

`kubectl port-forward service/app 3000:3000`

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_5.png)
![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_6.png)

Имя пользователя и компании не меняются от контейнера к контейнеру и соответствуют тому, что указано в deployment. 

### 4. Логи контейнеров.

Чтобы получить логи контейнера, необходимо выполнить команду:
`minikube kubectl -- logs <имя контейнера>`
Логи представлены на следующем изображении

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_7.png)

## Вывод
Таким образом, было проведено знакомство с типами "контроллеров" развертывания контейнеров и с сетевыми сервисами. Было развернуто свое веб приложение.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab2_8.png)
