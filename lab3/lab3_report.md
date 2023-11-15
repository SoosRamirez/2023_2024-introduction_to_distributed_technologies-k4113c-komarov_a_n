University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2023/2024\
Group: K4113c\
Author: Komarov Alexey Nikolaevich\
Lab: Lab3\
Date of create: 20.09.2023\
Date of finished: 31.09.2023\

# Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

## Описание
   В данной лабораторной работе вы познакомитесь с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.

## Цель работы:
   Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.

## Ход работы:
   В процессе выполнения лабораторной работы были выпонены следующие шаги:
### 1. Был создан манифест configMap с переменными окружения аналогично предыдущей работе.

  ```
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: config
  data:
    REACT_APP_USERNAME: "AlexKK"
    REACT_APP_COMPANY_NAME: "ITMO UNIVERSITY"
  ```

### 2. Из прошлой работы был взят deployment, но был изменен механизм передачи переменных окружения.
Вместо
  ```  
  env:
    - name: REACT_APP_USERNAME
      value: 'AlexKK'
    - name: REACT_APP_COMPANY_NAME
      value: 'ITMO UNIVERSITY'
  ```
было добавлено
```
envFrom:
  - configMapRef:
      name: config
```

### 3. Так же был описан Ingress.
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app
spec:
  tls:
    - hosts:
        - k8s-lab.com
      secretName: app-tls
  rules:
    - host: k8s-lab.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: app
                port:
                  name: http
```
- tls указывает на используемый сертификат, добавленный в minikube.
- в rules описаны правила обработки запросов: хост; Prefix: Это значение используется по умолчанию. Он говорит, что path будет интерпретироваться как префикс и будет сопоставляться с URL-путями запросов; path: Этот параметр определяет URL-путь запроса, который будет использоваться для сопоставления с правилами Ingress и маршрутизации трафика к соответствующему сервису.

### 4. Настройки minikube.

Далее при помощи команды `minikube addons enable ingress` был включен ingress.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_1.png)

Была принята конфигурация из манифеста `kubectl apply -f manifest.yaml`

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_2.png)

Был создан сертификат `openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out itdt.crt -keyout itdt.key`

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_3.png)

Затем сертификат был добавлен в minikube, конфигурация манифеста была принята еще раз `kubectl create secret tls app-tls --key="itdt.key" --cert="itdt.crt"`, `kubectl apply -f manifest.yaml`

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_4.png)

В файле /etc/hosts была сделана запись

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_5.png)

## 5. Подключение по FQDN

Выполним команду `minikube tunnel` для доступа к ingress.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_6.png)

В браузере заходим по указанному в ingress доменному имени и видим

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_7.png)

Посмотрим сертификат 

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_8.png)

## Вывод
Таким образом, было проведено знакомство с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab3_11.png)
