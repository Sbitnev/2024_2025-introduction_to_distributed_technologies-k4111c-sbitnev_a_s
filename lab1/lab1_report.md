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

![image](https://github.com/user-attachments/assets/f421f50e-09e5-4360-8288-d4dfc573f847)




## **Вывод:** 

В резултьате выполнения работы были изучены инструменты Minikube и Docker, а такжее развернут первый "под".
