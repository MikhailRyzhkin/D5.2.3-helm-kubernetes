# D5.2.3-helm-kubernetes

ЗАДАНИЕ D5.2.3 (HW-03)

Установите Helm Chart prometheus-community/prometheus так, чтобы

в нём не было сервисов:
  - pushgateway;
  - node-exporter;
  - alertnamager;

были только следующие поды:
  - prometheus-server;
  - prometheus-kube-state-metrics.


Решение:
1. Необходимо развернуть kubernetes кластер. Мы будем использовать уже развернутый в заданиях прошлых модулей кластер minikube из 5 нод
image.png

2. Устанавливаем helm для Windows
Сначала ставим Chocolatey в PS с админправами
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
Затем ставим helm
choco install kubernetes-helm
И проверяем версию
helm version
image.png

3. Установка репозитория. На примере установки Prometheus добавим себе репозиторий и просмотрим его содержимое. Выполняем команду:
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
Обновляем репозиторий, чтобы обновить и список чартов:
helm repo update
helm search repo prometheus-community
image.png

4. Устанавка готового чарта. Установка на примере Prometheus Stack в кластер через Helm очень быстро помогает настроить мониторинг.
Создадим свой namespace для мониторинга:
kubectl create ns prometheus
Установим (задеплоим) туда чарт prometheus:
helm upgrade --install -n prometheus prometheus prometheus-community/prometheus
Можно посмотреть, что входит в состав стандартного Prometheus-чарта:
kubectl get pods -n prometheus
image.png

5. Создаём "values.yaml" с следующими настройками:
prometheus-pushgateway:
  enabled: false

prometheus-node-exporter:
  enabled: false

alertmanager:
  enabled: false

prometheus-server:
  enabled: true

prometheus-kube-state-metrics:
  enabled: true
* Смотрим values.yaml данного чарта и ставим false напротив не нужного сервиса в чарте.

6. Применим наши изменения, выполним команду:
helm upgrade --install --namespace prometheus prometheus prometheus-community/prometheus --values values.yaml
image.png