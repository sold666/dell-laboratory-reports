#  Отчёт по первому заданию лаборатории Dell Technologies - ВШПИ
## Установка CSI-baremetal на kind

> Установка производилась на Ubuntu-20.04 (Параллельная система)

**Подготовка системы**

+ Установка [go v1.18 linux/amd64](https://go.dev/dl/go1.18.linux-amd64.tar.gz)
```
sudo apt update
sudo apt upgrade
curl -LO https://go.dev/dl/go1.18.linux-amd64.tar.gz
tar -xf go1.18.linux-amd64.tar.gz
# Добавить в ~/.bashrc
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```
+ Установка [kubectl](https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/ "Полное руководство")
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
# Проверка версии
kubectl version --client
```
+ Установка [helm 3](https://helm.sh/docs/intro/install/ "Полное руководство")
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
+ Установка [kind](https://kind.sigs.k8s.io/docs/user/quick-start/ "Полное руководство")
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin/kind
```
**Основная часть**

+ Клонирование двух репозиториев [csi-baremetal](https://github.com/dell/csi-baremetal) и [csi-baremetal-operator](https://github.com/dell/csi-baremetal-operator)
```
git clone https://github.com/dell/csi-baremetal.git
git clone https://github.com/dell/csi-baremetal-operator.git

#or ssh

git clone git@github.com:dell/csi-baremetal.git
git clone git@github.com:dell/csi-baremetal-operator.git
```
+ Выполнение всех действий, описанных в [руководстве](https://github.com/dell/csi-baremetal/blob/4a0fe85e2c0994c61d05bc664605ce6ae9713f5a/docs/CONTRIBUTING.md)
+ Прохождение валидации

**Заключительная часть**

Все поды должны иметь статус RUNNING, как показано в примере
```
root@sold-BOHK-WAX9X:/home/sold# kubectl get pods
NAME                                             READY   STATUS    RESTARTS   AGE
csi-baremetal-controller-595bd45d4f-r2dvn        4/4     Running   0          36h
csi-baremetal-node-controller-867f5bc4fb-dq4fx   1/1     Running   0          36h
csi-baremetal-node-kernel-5.4-g6kfz              4/4     Running   0          36h
csi-baremetal-node-kernel-5.4-tkmbt              4/4     Running   0          36h
csi-baremetal-node-kernel-5.4-xr65d              4/4     Running   0          36h
csi-baremetal-operator-5c68449f7b-sd7x4          1/1     Running   0          36h
csi-baremetal-se-54p8w                           1/1     Running   0          36h
csi-baremetal-se-patcher-g47xf                   1/1     Running   0          36h
web-0                                            1/1     Running   0          36h
web-1                                            1/1     Running   0          36h
web-2                                            1/1     Running   0          36h
```
**Часто встречающиеся ошибки и их решения**

>Помните, что при каждой ошибке стоит удостовериться в правильности ввода команды и ознакомиться со всеми пунктами ниже

+ Перед началом работы проверьте объём свободного пространства в / и /home (необходимо не менее 50 гб.)
```
df -h
```
+ Данный драйвер не рекомендуется устанавливать на сторонние ОС кроме указанных [здесь](https://github.com/dell/csi-baremetal-operator#readme "README") 
+ В ходе установки в командах будут встречаться переменные, которые часто забывают инициализировать
```
vim ~/.bashrc

#Вставьте в данный файл необходимые вам команды
#Засчет этого система сможет работать с ними из любой точки
#Пример

#dell
export REGISTRY=sold666
export CSI_BAREMETAL_DIR=/home/sold/csi-baremetal
export CSI_BAREMETAL_OPERATOR_DIR=/home/sold/csi-baremetal-operator
export GOPATH=/home/sold/go
export PATH=$PATH:$GOPATH/bin
```
+ Изменение тестового StatefulSet csi-baremetal/tests/app/nginx.yaml (при web-3 со статусом PENDING и на кластере имеется 3 worker nodes)
  + Убираем все PVC
  + Уменьшаем количество replicas до 3
```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 3
  podManagementPolicy: Parallel
  selector:
    matchLabels:
      app: nginx
      app.kubernetes.io/name: nginx
  template:
    metadata:
      labels:
        app: nginx
        app.kubernetes.io/name: nginx
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - nginx
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
``` 
+ При выполнении команды
```make load-operator-image OPERATOR_VERSION=${CSI_OPERATOR_VERSION} REGISTRY=${REGISTRY}```
Появляется ошибка "Error response from daemon: manifest for *ваша директория с файлом* not found: manifest unknown make: *** [Makefile.validation:58: pull-operator-image] Ошибка 1"
  1. Открываете Makefile.validation
  2. Закомментируйте строку с командой под pull-operator-image
+ При ошибках с подключением [docker](https://www.docker.com/) стоит переходить на суперпользователя
```
su -
```
