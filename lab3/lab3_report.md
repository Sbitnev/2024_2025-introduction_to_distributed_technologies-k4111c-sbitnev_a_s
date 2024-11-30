##### University: [ITMO University](https://itmo.ru/ru/)
##### Faculty: [FICT](https://fict.itmo.ru)
##### Course: [introduction-to-distributed-technologies](https://itmo-ict-faculty.github.io/introduction-to-distributed-technologies)
##### Year: 2024/2025
##### Group: K4111с
##### Author: Sbitnev Aleksandr
##### Lab: Lab3
##### Date of create: 25.11.2024
##### Date of finished: 

***

# Отчёт по лабораторной работе №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."


## **Цель работы:** 

Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.


## **Ход работы:**
Создадим configMap с переменными REACT_APP_USERNAME, REACT_APP_COMPANY_NAME:
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: itdt-cm
data:
  REACT_APP_USERNAME: "Sbitnev"
  REACT_APP_COMPANY_NAME: "ITMO"
```

Создадим replicaSet с 2 репликами контейнера:
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: itdt-rs
spec:
  replicas: 2
  selector:
    matchLabels:
      app: itdt
  template:
    metadata:
      labels:
        app: itdt
    spec:
      containers:
      - name: contained-frontend
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
          - configMapRef:
              name: itdt-cm
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
        ports:
        - containerPort: 3000
```


Включить ingress, командой `minikube addons enable ingress`

![image](https://github.com/user-attachments/assets/d4da9be9-2cd8-4b9b-b453-e4f401a66f28)


Cгенерируем TLS сертификат:
```
openssl req -x509 -newkey rsa:2048 -keyout itdt.me.key -nodes -days 3650  -out itdt.me.crt -subj "/CN=itdt.me"
kubectl create secret tls itdt-tls-certs --cert=itdt.me.crt --key=itdt.me.key
```

![image](https://github.com/user-attachments/assets/f3ed0418-c690-4689-8ff3-3fe5e5527951)

Узнаем minikube ip:
![image](https://github.com/user-attachments/assets/f874368b-cbf6-49c9-9b7d-78ea3a97d7d5)

Добавим в hosts добавить ip  и FQDN.

![image](https://github.com/user-attachments/assets/afe3d279-7274-450c-87ae-e010d8eda8c8)

Перейдем в браузере по FQDN имени.

![image](https://github.com/user-attachments/assets/f4524a4a-6e52-4e1f-b3d6-cc6725dfa110)


Проверим наличие сертификата.

![image](https://github.com/user-attachments/assets/90f10e73-e2fd-49bc-8022-1ab35c2af72f)


### Схема организации контейеров и сервисов нарисованная вами в draw.io или Visio.

![image](https://github.com/user-attachments/assets/b9e75878-5ab3-4c64-8adb-068012dfe657)


## **Вывод:** 

В результате выполнения работы мы познакомились с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube.
