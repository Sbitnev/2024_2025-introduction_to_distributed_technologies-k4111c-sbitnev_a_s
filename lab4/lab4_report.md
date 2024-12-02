##### University: [ITMO University](https://itmo.ru/ru/)
##### Faculty: [FICT](https://fict.itmo.ru)
##### Course: [introduction-to-distributed-technologies](https://itmo-ict-faculty.github.io/introduction-to-distributed-technologies)
##### Year: 2024/2025
##### Group: K4111с
##### Author: Sbitnev Aleksandr
##### Lab: Lab4
##### Date of create: 01.12.2024
##### Date of finished: 

***

# Отчёт по лабораторной работе №4 "Сети связи в Minikube, CNI и CoreDNS"


## **Цель работы:** 

Познакомиться с CNI Calico и функцией IPAM Plugin, изучить особенности работы CNI и CoreDNS.

## **Ход работы:**
Данная работа была выполнена на ОС Ubuntu. 

При запуске minikube установим плагин CNI=calico и режим работы Multi-Node Clusters одновеременно.
```
minikube start --nodes 2 --cni calico --kubernetes-version=v1.24.3
```

Проверим количество нод.
```
minikube kubectl -- get nodes
```

![image](https://github.com/user-attachments/assets/1135fcd8-829d-41c9-a2c3-0465e6bc96e0)


Проверим, что необходимые ресурсы созданы.
```
minikube status -p minikube
```

![image](https://github.com/user-attachments/assets/569ecc29-cd77-4618-a895-c308e8fce711)

Установим calicoctl pod, манифест взят с официального сайта calico и немного изменен:
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: calicoctl
  namespace: kube-system

---
apiVersion: v1
kind: Pod
metadata:
  name: calicoctl
  namespace: kube-system
spec:
  nodeSelector:
    kubernetes.io/os: linux
  hostNetwork: true
  serviceAccountName: calicoctl
  containers:
    - name: calicoctl
      image: calico/ctl
      command:
        - /calicoctl
      args:
        - version
        - --poll=1m
      env:
        - name: DATASTORE_TYPE
          value: kubernetes

---

kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: calicoctl
rules:
  - apiGroups: [""]
    resources:
      - namespaces
      - nodes
    verbs:
      - get
      - list
      - update
  - apiGroups: [""]
    resources:
      - nodes/status
    verbs:
      - update
  - apiGroups: [""]
    resources:
      - pods
      - serviceaccounts
    verbs:
      - get
      - list
  - apiGroups: [""]
    resources:
      - pods/status
    verbs:
      - update
  - apiGroups: ["crd.projectcalico.org"]
    resources:
      - bgppeers
      - bgpconfigurations
      - clusterinformations
      - felixconfigurations
      - globalnetworkpolicies
      - globalnetworksets
      - ippools
      - ipreservations
      - kubecontrollersconfigurations
      - networkpolicies
      - networksets
      - hostendpoints
      - ipamblocks
      - blockaffinities
      - ipamhandles
      - ipamconfigs
    verbs:
      - create
      - get
      - list
      - update
      - delete
  - apiGroups: ["networking.k8s.io"]
    resources:
      - networkpolicies
    verbs:
      - get
      - list

---

apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: calicoctl
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: calicoctl
subjects:
  - kind: ServiceAccount
    name: calicoctl
    namespace: kube-system
```

Применим манифест:
```
kubectl apply -f calicoctl.yaml
```

![image](https://github.com/user-attachments/assets/7d6af5a8-f9a8-42cb-9c12-1ee652a5769b)


Укажем label по признаку географического расположения.
```
kubectl label nodes minikube zone=west
kubectl label nodes minikube-m02 zone=east

```

![image](https://github.com/user-attachments/assets/9c319b03-fc59-46a4-8199-d061c7c52a09)


Создадим ip pool, удалим пул по умолчанию:
```
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
   name: zone-west-ippool
spec:
   cidr: 192.168.1.0/24
   ipipMode: Always
   natOutgoing: true
   nodeSelector: zone == "west"
---

apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
   name: zone-east-ippool
spec:
   cidr: 192.168.2.0/24
   ipipMode: Always
   natOutgoing: true
   nodeSelector: zone == "east"
```

```
kubectl exec -i -n kube-system calicoctl -- /calicoctl create -f - < ip_pool.yaml --allow-version-mismatch
kubectl exec -i -n kube-system calicoctl -- /calicoctl  delete ippools default-ipv4-ippool --allow-version-mismatch
kubectl exec -i -n kube-system calicoctl -- /calicoctl  get ippools -o wide --allow-version-mismatch
```

![image](https://github.com/user-attachments/assets/e19546c3-63fa-4df3-97fb-7d3d8f16b11f)

Создадим ресурсы: сервис, config map с требуемыми переменными, а также replicaset, для удобства объединим их в одном файле.
```

```

Применим манифест.
```
kubectl apply -f resorces.yaml
```

![image](https://github.com/user-attachments/assets/c8f64451-cf10-4bd6-bd68-5e832dbfa757)

Дождемся запуска ресурсов:
```
minikube kubectl -- get po -A
```

![image](https://github.com/user-attachments/assets/7f89987a-0532-4674-b81a-4e1a463e440a)


Пробросим порт:
```
kubectl port-forward service/lab4-svc 3000:8000
```

![image](https://github.com/user-attachments/assets/d98aa01b-af34-4392-8a1a-94a5fd2d66e4)

Проверим в праузере работу:

![image](https://github.com/user-attachments/assets/a8acc3cf-d3fc-4f55-ab15-b3331288834b)

Узнаем ip:
```
kubectl get pods -o wide
```

![image](https://github.com/user-attachments/assets/40d41e48-6be0-4cac-847e-445571c8f8eb)

Проверим сетевую связность, используя утилиту ping:
```
kubectl exec -it lab4-app-gk49j -- ping 192.168.2.1
```

![image](https://github.com/user-attachments/assets/67b88e58-3c12-4710-abb2-fd0bdcba6806)

### Схема организации контейеров и сервисов

![image](https://github.com/user-attachments/assets/0b4da23a-9ae2-4db8-9261-40763af04d2c)


## **Вывод:** 

В результате выполнения работы мы познакомились с CNI Calico и функцией IPAM Plugin, изучили особенности работы CNI и CoreDNS.
