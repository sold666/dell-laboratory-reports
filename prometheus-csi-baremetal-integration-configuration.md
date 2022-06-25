# Отчёт по второму заданию лаборатории Dell Technologies - ВШПИ

## Prometheus-CSI-Baremetal integration and configuration

> The installation was performed on Ubuntu-20.04 (Parallel system)

> Before installing the monitoring system, you must have CSI-baremetal installed.
> How to do this can be found here [Installation CSI-baremetal on kind](https://github.com/sold666/dell-laboratory-reports/blob/main/csi-baremetal-deploy.md)

- [Prometheus](#Prometheus)
    - [About](#About)
    - [How it works?](#How_it_works?)
- [Grafana](#Grafana)
    - [About](#About)
- [Deployment](#Deployment)
    - [Installation](#Installation)
        - [First way](#First_way)
        - [Second way](#Second_way)
        - [Ingress](#Ingress)

## Prometheus <img src="https://github.com/devicons/devicon/blob/master/icons/prometheus/prometheus-original-wordmark.svg" alt="Prometheus" width="40" height="40">

### About

**Prometheus** — monitoring system. The main advantages are the possibility of creating flexible
data queries and storing metric values in a time series database, the possibility of automation during administration.

### How it works?

Prometheus scrapes metrics from instrumented jobs, either directly or via an intermediary push gateway for short-lived
jobs.
It stores all scraped samples locally and runs rules over this data to either aggregate and record new time series
from existing data or generate alerts. [Grafana](#Grafana) or other API consumers can be used to visualize the collected
data.

![Prometheus schem](https://user-images.githubusercontent.com/61206345/175059690-3414b07b-2d5a-44ee-b6c0-69b05c692ca0.png)

In short, Prometheus works with key-value pairs. The key describes what we measure, and the value stores the actual
value as a number.
Remember: Prometheus is not designed to store raw information, like plain text. It stores metrics aggregated over a
period of time.

## Grafana <img src="https://github.com/devicons/devicon/blob/master/icons/grafana/grafana-original.svg" alt="Grafana" width="40" height="40">

### About

**Grafana** — is an open source platform for data visualization, monitoring and analysis.

A Grafana dashboard supports multiple panels in a single grid. You can visualize results from multiple data sources
simultaneously. It is a powerful open-source analytical and visualization tool that consists of multiple individual
panels arranged in a grid. The panels interact with configured data sources including (but not limited to)
AWS CloudWatch, Microsoft SQL server, [Prometheus](#Prometheus), MySQL, InfluxDB, and many others.

![Grafana board](https://user-images.githubusercontent.com/61206345/175064091-f8c66d73-f008-4860-a9d1-3bba6a56e222.png)

## Deployment

The exporter may be installed either via Helm or through YAML manifests with the object definitions. It's recommended
to use Helm as it's more convenient to manage the configuration of the deployment.

### Installation

First you need to find out the version of k8s:

``` 
kubectl version
```

#### First way

To find out the release you need, see here
[kube-prometheus](https://github.com/prometheus-operator/kube-prometheus)

```
# create namespace
kubectl create namespace monitoring

# clone
git clone --depth 1 https://github.com/prometheus-operator/kube-prometheus.git -b [your release]

# work with manifests
cd kube-prometheus
cp -r manifests [your directory]

# setup CRD's and Prometheus Operator
kubectl create -f ./manifests/setup/

# setup manifests
kubectl create -f ./manifests/

# check pods
kubectl -n monitoring get pods

# view metrics in Prometheus
kubectl -n monitoring port-forward svc/prometheus-k8s 9090

# view dashboards in Grafana
kubectl -n monitoring port-forward svc/grafana 3000

# view Alert Manager
kubectl -n monitoring port-forward svc/alertmanager-main 9093
```

Then access via http://localhost:'port'. In grafana use the default user:password of `admin:admin`.

If you want to see how Prometheus is configured you should look at the services:

```
kubectl -n monitoring get svc
```

#### Second way

```
# deploy Prometheus
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install [your release] prometheus-community/kube-prometheus-stack
```

#### Ingress

`Ingress` exposes HTTP and HTTPS routes from outside the cluster to services within the cluster.
Traffic routing is controlled by rules defined on the Ingress resource.
An Ingress may be configured to give Services externally-reachable URLs, load balance traffic, terminate SSL / TLS,
and offer name-based virtual hosting. An Ingress controller is responsible for fulfilling the Ingress, usually with a
load balancer, though it may also configure your edge router or additional frontends to help handle the traffic.

An Ingress does not expose arbitrary ports or protocols. Exposing services other than HTTP and HTTPS to the internet you
can use a service of type Service.Type=Node Port or Service.Type=LoadBalancer.

If you wnat to deploy `Ingress` you can use this commands:

```
kubectl get svc
kubectl edit svc prometheus-grafana
#Change to type NodePort
```

![NodePort for Grafana](https://user-images.githubusercontent.com/61206345/175784253-88060cc5-2d41-414f-8116-17a12ea7d210.png)

After the completed operations, you can apply your yaml file:

```
kubectl apply -f ingress.yaml
```

Here are examples of what you should get approximately as a result:

![Prometheus Example](https://user-images.githubusercontent.com/61206345/175784889-42feb23b-2d77-4aac-b123-bec40a73986d.png)
![Pods](https://user-images.githubusercontent.com/61206345/175785058-c57ec45b-8188-4a62-98b6-5cccbdc05f23.png)
![Svc](https://user-images.githubusercontent.com/61206345/175785067-6bc4eb63-d10c-4faf-8104-95dd159412d6.png)
