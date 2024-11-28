##### University: [ITMO University](https://itmo.ru/ru/)
##### Faculty: [FICT](https://fict.itmo.ru)
##### Course: [introduction-to-distributed-technologies](https://itmo-ict-faculty.github.io/introduction-to-distributed-technologies)
##### Year: 2024/2025
##### Group: K4111с
##### Author: Sbitnev Aleksandr
##### Lab: Lab2
##### Date of create: 15.11.2024
##### Date of finished: 

***

# Отчёт по лабораторной работе №2 "Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса."


## **Цель работы:** 
Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомится с сетевыми сервисами и развернуть свое веб приложение.


## **Ход работы:**
### Часть 1


### Часть 2


### Часть 2

Cоздадим манифест deployment с 2 репликами контейнера ifilyaninitmo/itdt-contained-frontend:master и передадим переменные в эти реплики: REACT_APP_USERNAME, REACT_APP_COMPANY_NAME.

[deployment.yaml](deployment.yaml)
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: react-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: react-app
  template:
    metadata:cl
      labels:
        app: react-app
    spec:
      containers:
        - image: ifilyaninitmo/itdt-contained-frontend:master
          name: react-app
          ports:
            - containerPort: 3000
          env:
            - name: REACT_APP_USERNAME
              value: Sbtnev
            - name: REACT_APP_COMPANY_NAME
              value: ITMO
```

Создадим манифест для создания сервиса через который будет доступ на эти "поды".

[service.yaml](service.yaml)
```
apiVersion: v1
kind: Service
metadata:
  name: react-app-service
spec:
  type: LoadBalancer
  selector:
    app: react-app
  ports:
  - port: 3000
    targetPort: 3000
```

Создадим поды и сервис:

![image](https://github.com/user-attachments/assets/a1f6f050-de35-4fb3-a073-c6c4cbd156a6)

Запустим в minikube режим проброса портов и подключимся к контейнерам через веб браузер.

![image](https://github.com/user-attachments/assets/b6461162-a4a5-4936-8d0f-1e806d8b162a)

![image](https://github.com/user-attachments/assets/610d02dc-e110-4a8b-89e9-3fcde504866e)


В таком случае мы всегда пудем попадать на один контейнер, поскольку для работы сервиса LoadBalancer требуется выполнить создание туннеля, выполнив команду `minikube tunnel`

![image](https://github.com/user-attachments/assets/636b9193-b154-40bc-8999-2b157672da38)

Теперь сервис будет менять контейнер:

![image](https://github.com/user-attachments/assets/68dbb40f-3b65-47d0-917c-e33a2230b5cb)

Логи контейнеров:

![image](https://github.com/user-attachments/assets/409359dc-2e02-4e02-a596-6a8040c1d3e2)

Схема организации контейеров и сервисов:


## **Вывод:** 
В результате выполнения работы были изучены типы "контроллеров" развертывания контейнеров и сетевые сервисы и было развернуто свое веб приложение.
