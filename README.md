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
1. Необходимо развернуть kubernetes кластер. Мы будем использовать уже развернутый в заданиях прошлых модулей кластер minikube из 5 нод.
![Screenshot_1](https://github.com/MikhailRyzhkin/D5.2.3-helm-kubernetes/assets/69116076/b5496831-40cd-43f0-9cd1-96464a8ffaa8)

2. Устанавливаем helm для Windows.
  - Сначала ставим Chocolatey в PS с админправами
    - Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
  - Затем ставим helm
    - choco install kubernetes-helm
  - И проверяем версию
    - helm version
![Screenshot_2](https://github.com/MikhailRyzhkin/D5.2.3-helm-kubernetes/assets/69116076/63de63aa-1b19-4481-b4c7-8fe51045a392)

3. Установка репозитория. На примере установки Prometheus добавим себе репозиторий и просмотрим его содержимое. 
  - Выполняем команду:
    - helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  - Обновляем репозиторий, чтобы обновить и список чартов:
    - helm repo update
    - helm search repo prometheus-community
![Screenshot_3](https://github.com/MikhailRyzhkin/D5.2.3-helm-kubernetes/assets/69116076/729b7e9b-2557-4c53-8cf9-4b4e0076f1d1)

4. Устанавка готового чарта. Установка на примере Prometheus Stack в кластер через Helm очень быстро помогает настроить мониторинг.
  - Создадим свой namespace для мониторинга:
    - kubectl create ns prometheus
  - Установим (задеплоим) туда чарт prometheus:
    - helm upgrade --install -n prometheus prometheus prometheus-community/prometheus
  - Можно посмотреть, что входит в состав стандартного Prometheus-чарта:
    - kubectl get pods -n prometheus
![Screenshot_4](https://github.com/MikhailRyzhkin/D5.2.3-helm-kubernetes/assets/69116076/52006dd6-102f-4e03-9d38-ea1a3fd4d010)

5. Смотрим values.yaml данного чарта и ставим false напротив не нужного сервиса в чарте. Создаём "values.yaml" с соответствующими настройками.

6. Применим наши изменения, выполним команду:
  - helm upgrade --install --namespace prometheus prometheus prometheus-community/prometheus --values values.yaml
![Screenshot_5](https://github.com/MikhailRyzhkin/D5.2.3-helm-kubernetes/assets/69116076/ace4ec9e-96e1-41d7-be5e-9d7ff6c8be6f)
