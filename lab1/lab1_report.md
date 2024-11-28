##### University: [ITMO University](https://itmo.ru/ru/)
##### Faculty: [FICT](https://fict.itmo.ru)
##### Course: [introduction-to-distributed-technologies](https://itmo-ict-faculty.github.io/introduction-to-distributed-technologies)
##### Year: 2024/2025
##### Group: K4111с
##### Author: Sbitnev Aleksandr
##### Lab: Lab1
##### Date of create: 15.11.2024
##### Date of finished:

***

# Отчёт по лабораторной работе №1 "Установка Docker и Minikube, мой первый манифест."


## **Цель работы:**

Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".

## **Ход работы:**
### Подготовка среды

Перед выполнением работы требуется устновить Docker и Minikube.
Так как работа будет выполняться на ОС Windows, с сайта разработчика скачиваются соответствующие .exe файлы.
После этого эти файлы запускаются и устанавливается необходимое ПО.

Проверим кореектность установки в терминале:
![image](https://github.com/user-attachments/assets/9d7383f5-3b46-4a07-8c57-fcb95bbc8307)

Для запуска minikube запустим PowerShell от имени администратора и прмиеним команду: `minikube start`

![image](https://github.com/user-attachments/assets/e63b7c20-b294-4a6a-9082-1bb86855d5d2)


Проверим статус командой `kubectl config get-contexts`:
![image](https://github.com/user-attachments/assets/6eae8a7b-5f68-443d-957c-3a14b8a1f1c2)

### Создание пода

Создаем yaml файл с настройками нашего будущего пода:
```
apiVersion: v1
kind: Pod
metadata:
  name: vault
  labels:
    name: vault
spec:
  containers:
  - name: vault
    image: vault:1.13.3
    ports:
      - containerPort: 8200
```


Применим манифест командой `kubectl apply -f vault.yaml`

![image](https://github.com/user-attachments/assets/c175e161-3c40-4be2-bfa4-4de65b30d3f4)

![image](https://github.com/user-attachments/assets/c019f05b-c6b9-4959-a9b6-72f131c28582)


После этого необходимо создать сервис для доступа к этому контейнер, выполним команду `kubectl expose pod vault --type=NodePort --port=8200`

![image](https://github.com/user-attachments/assets/3b48b14f-6c63-4ac7-a304-38a7d1d979aa)


Прокинем порты командой `kubectl port-forward service/vault 8200:8200`

![image](https://github.com/user-attachments/assets/d999daf3-2402-4a21-a4a2-9cb4772877f9)


Minikube прокинет порт компьютера в контейнер и можем зайти в vault по ссылке http://localhost:8200

![image](https://github.com/user-attachments/assets/ab2ad3bf-409c-4ffe-a125-be18526fdd41)


Найдем токен для входа в логах сервиса с помощью команды `kubectl logs vault`

![image](https://github.com/user-attachments/assets/3ab99c3d-e5c8-4f51-b03f-67623f9d9b59)


Авторизуемся

![image](https://github.com/user-attachments/assets/2bd40ad6-991a-4599-a8a7-ac618021e45b)


После этого можно удлить под командой `kubectl -f delete vault.yaml`

![image](https://github.com/user-attachments/assets/76305632-65f3-4d5d-a751-3740b905d613)


### Результаты лабораторной работы

Файл с разработанным вами манифестом для развертывания "пода" с расширением .yaml:
[vault.yaml](vault.yaml)

Схема организации контейеров и сервисов.

![image](https://github.com/user-attachments/assets/8feb3c4c-4373-4886-82cd-d3b7097eff0c)



Ответы на вопросы, скриншоты c результатами работы предствалены в отчете.

## **Вывод:**

В резултьате выполнения работы были изучены инструменты Minikube и Docker, а такжее развернут первый "под".
