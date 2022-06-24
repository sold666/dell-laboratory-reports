#  Отчёт по второму заданию лаборатории Dell Technologies - ВШПИ
## Prometheus-CSI-Baremetal integration and configuration

> The installation was performed on Ubuntu-20.04 (Parallel system)

> Before installing the monitoring system, you must have CSI-baremetal installed. 
> How to do this can be found here [Installation CSI-baremetal on kind](https://github.com/sold666/dell-laboratory-reports/blob/main/csi-baremetal-deploy.md) 
- [Prometheus](#Prometheus)
  - [About](#About)
  - [How it works?](#How it works?)
- [Grafana](#Grafana)
  - [About](#About)
- [Deployment](#Deployment)
  - [Installation](#Installation)
  - [Advanced Installation](#Advanced installation)

## Prometheus <img src="https://github.com/devicons/devicon/blob/master/icons/prometheus/prometheus-original-wordmark.svg" alt="Prometheus" width="40" height="40">
### About
**Prometheus** — monitoring system. The main advantages are the possibility of creating flexible
data queries and storing metric values in a time series database, the possibility of automation during administration.
### How it works?
Prometheus scrapes metrics from instrumented jobs, either directly or via an intermediary push gateway for short-lived jobs. 
It stores all scraped samples locally and runs rules over this data to either aggregate and record new time series 
from existing data or generate alerts. [Grafana](#Grafana) or other API consumers can be used to visualize the collected data.

![image](https://user-images.githubusercontent.com/61206345/175059690-3414b07b-2d5a-44ee-b6c0-69b05c692ca0.png)

## Grafana <img src="https://github.com/devicons/devicon/blob/master/icons/grafana/grafana-original.svg" alt="Grafana" width="40" height="40">
### About
**Grafana** — is an open source platform for data visualization, monitoring and analysis.

A Grafana dashboard supports multiple panels in a single grid. You can visualize results from multiple data sources 
simultaneously. It is a powerful open-source analytical and visualization tool that consists of multiple individual 
panels arranged in a grid. The panels interact with configured data sources including (but not limited to) 
AWS CloudWatch, Microsoft SQL server, [Prometheus](#Prometheus), MySQL, InfluxDB, and many others.

![image](https://user-images.githubusercontent.com/61206345/175064091-f8c66d73-f008-4860-a9d1-3bba6a56e222.png)

## Deployment
The exporter may be installed either via Helm or through YAML manifests with the object definitions. It's recommended to use Helm as it's more convenient to manage the configuration of the deployment.
### Installation
### Advanced Installation
