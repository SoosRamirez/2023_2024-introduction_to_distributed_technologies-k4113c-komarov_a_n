University: [ITMO University](https://itmo.ru/ru/)\
Faculty: [FICT](https://fict.itmo.ru)\
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)\
Year: 2023/2024\
Group: K4113c\
Author: Komarov Alexey Nikolaevich\
Lab: Lab4\
Date of create: 20.09.2023\
Date of finished: 31.09.2023\

# Лабораторная работа №4 "Сети связи в Minikube, CNI и CoreDNS."

## Описание
   Это последняя лабораторная работа в которой вы познакомитесь с сетями связи в Minikube. Особенность Kubernetes заключается в том, что у него одновременно работают `underlay` и `overlay` сети, а управление может быть организованно различными CNI.

## Цель работы:
   Познакомиться с CNI Calico и функцией `IPAM Plugin`, изучить особенности работы CNI и CoreDNS.

## Ход работы:
   В процессе выполнения лабораторной работы были выпонены следующие шаги:
### 1. Запуск minikube.
  При запуске minikube необходимо установить плагин `CNI=calico` и режим работы `Multi-Node Clusters` одновеременно
  ```
  minikube start --network-plugin=cni --cni=calico --nodes 2
  ```

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_1.png)
  
### 2. Проверка количества нод.
  Выполним команды 
  ```
  kubectl get nodes
  ```
  ```
  kubectl get pods -l k8s-app=calico-node -A
  ```
  чтобы проверить, что все запустилось правильно. Количество подов должно совпадать с количеством нод.

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_2.png)

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_3.png)
  
### 3. Расставление меток на ноды.
  Для каждой ноды расставим метки в сответствие с регионом. Для этого выполним команду
  ```
  kubectl label nodes <node-name> region=<zone>
  ```

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_4.png)
  
  Проверим, что все получилось.
  ```
  kubectl get nodes -l region=RU
  kubectl get nodes -l region=EN
  ```

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_5.png)

### 4. Создание манифеста calico.
  Опишем следующий манифест
  ```
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: ru-ippool
spec:
  cidr: 192.168.10.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: region == "RU"

---

apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
  name: en-ippool
spec:
  cidr: 192.168.20.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: region == "EN"

  ```

  - `ipipMode` указывает, какой режим туннелирования используется.
  - `natOutgoing` разрешает подам использовать NAT для внешней сети.
  - `nodeSelector` указывает, какие ноды получат ip из этого пула.

  Чтобы создать новый пул, необходимо сначала удалить дефолтный
  ```
  ./calicoctl delete ippools default-ipv4-ippool
  ```
  а потом уже применить только что созданный манифест
  ```
  ./calicoctl create -f -< ippool.yaml
  ```

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_6.png)

### 5. Создание deployment.
  Возьмем деплоймент из прошлой работы, но поменяем назначение сервиса с NodePort на LoadBallancer. 
  ```
  kubectl apply -f manifest.yaml
  ```
  Убедимся, что поды правильно разместились и получили правильные ip.

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_7.png)

### 6. Подключение к подам.
  Выполним команду 
  ```
minikube tunnel
  ```
  чтобы пробросить порты для подключения к нашим подам.

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_8.png)

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_9.png)

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_10.png)

  #### Переменные Container name и Container IP
  Переменные `Container name` и `Container IP` изменяются так как они индивидуальны для каждого контейнера, а сервис выполняет функцию балансировщика нагрузки и отправляет нас то на один, то на другой.

### 7. Ping соседнего пода.
  Пропинговать сосений под можно по его FQDN. Его можно узнать при помощи команды
  ```
kubectl exec <First Pod name> -- nslookup <second pod ip>
  ```
  По полученному FQDN пропинговать можно, используя команду
  ```
kubectl exec <First Pod name> -- ping <second pod fqdn>
  ```

  ![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_11.png)

## Вывод
Таким образом, было проведено знакомство с CNI Calico и функцией `IPAM Plugin`, изучены особенности работы CNI и CoreDNS.

![image](https://github.com/SoosRamirez/2023_2024-introduction_to_distributed_technologies-k4113c-komarov_a_n/blob/main/media/lab4_111.png)
