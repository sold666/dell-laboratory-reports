#  Отчёт по первому заданию лаборатории Dell Technologies - ВШПИ
## Установка CSI-baremetal на kind

> Установка производилась на Ubuntu-20.04 (Вторая система)

**Подготовка системы**

+ Установка [go v1.18 linux/amd64](https://go.dev/dl/go1.18.linux-amd64.tar.gz).
```
sudo apt update
sudo apt upgrade
curl -LO https://go.dev/dl/go1.18.linux-amd64.tar.gz
tar -xf go1.18.linux-amd64.tar.gz
# Добавить в ~/.bashrc
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```
+ Установка [kubectl](https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/ "Полное руководство").
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
# Проверка версии
kubectl version --client
```
+ Установка [helm 3](https://helm.sh/docs/intro/install/ "Полное руководство").
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
+ Установка [kind](https://kind.sigs.k8s.io/docs/user/quick-start/ "Полное руководство").
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin/kind
```
**Основная часть**

+ Клонирование двух репозиториев [csi-baremetal](https://github.com/dell/csi-baremetal) и [csi-baremetal-operator](https://github.com/dell/csi-baremetal-operator).
```
git clone https://github.com/dell/csi-baremetal.git
git clone https://github.com/dell/csi-baremetal-operator.git

#or ssh

git clone git@github.com:dell/csi-baremetal.git
git clone git@github.com:dell/csi-baremetal-operator.git
```
+ Выполнение всех действий, описанных в [руководстве](https://github.com/dell/csi-baremetal/blob/4a0fe85e2c0994c61d05bc664605ce6ae9713f5a/docs/CONTRIBUTING.md) .
+ Прохождение валидации

**Итог**

По итогу все поды должны иметь статус RUNNING 
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
