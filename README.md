# 🚀 Шпаргалка по командам Kubernetes

> Полное руководство по работе с `kubectl` — от установки до продвинутых операций

---

## 📑 Содержание

1. [Установка и настройка](#-установка-и-настройка)
2. [Основные команды и информация о кластере](#-основные-команды-и-информация-о-кластере)
3. [Управление подами](#-управление-подами)
4. [Развертывания и ReplicaSets](#-развертывания-и-replicasets)
5. [Службы и сетевое взаимодействие](#-службы-и-сетевое-взаимодействие)
6. [ConfigMaps и секреты](#-configmaps-и-секреты)
7. [Хранилище и тома](#-хранилище-и-тома)
8. [Устранение неполадок](#-устранение-неполадок)
9. [Управление ресурсами](#-управление-ресурсами)
10. [Расширенные операции](#-расширенные-операции)
11. [Производительность и мониторинг](#-производительность-и-мониторинг)
12. [Управление конфигурацией](#-управление-конфигурацией)
13. [Лучшие практики](#-лучшие-практики)

---

## 📦 Установка и настройка

### Установка kubectl

**🍎 macOS (Homebrew)**

```bash
brew install kubectl
```

**🐧 Linux**

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

**🪟 Windows (Chocolatey)**

```bash
choco install kubernetes-cli
```

### ✅ Проверка установки

```bash
# Версия клиента
kubectl version --client

# Версии клиента и сервера
kubectl version

# Информация о кластере
kubectl cluster-info
```

### ⚙️ Настройка kubectl

```bash
# Просмотр текущей конфигурации
kubectl config view

# Список всех контекстов
kubectl config get-contexts

# Переключение на контекст
kubectl config use-context my-cluster

# Установка пространства имен по умолчанию
kubectl config set-context --current --namespace=my-namespace
```

---

## 🌐 Основные команды и информация о кластере

### Информация о кластере

```bash
kubectl cluster-info              # Информация о кластере
kubectl config view               # Конфигурация кластера
kubectl api-resources             # Доступные ресурсы API
kubectl api-versions              # Поддерживаемые версии API
```

### Управление узлами

```bash
kubectl get nodes                 # Список всех узлов
kubectl get nodes -o wide         # Подробная информация
kubectl describe node <name>      # Описание узла
kubectl top nodes                 # Использование ресурсов
```

### Пространства имен

```bash
kubectl get namespaces                    # Список пространств имен
kubectl create namespace my-namespace     # Создать
kubectl delete namespace my-namespace     # Удалить
kubectl get all -n my-namespace           # Ресурсы в пространстве имен
```

---

## 🎯 Управление подами

### Создание и запуск

```bash
kubectl run nginx --image=nginx                              # Простой под
kubectl create -f pod.yaml                                   # Из YAML
kubectl run busybox --image=busybox -- echo "Hello World"    # С командой
kubectl create job hello --image=busybox:1.28 -- echo "Hello" # Job
```

### Просмотр информации

```bash
kubectl get pods                          # Список подов
kubectl get pods -o wide                  # С деталями
kubectl get pods --all-namespaces         # Во всех namespace
kubectl get pods --watch                  # Наблюдение
kubectl describe pod <name>               # Описание пода
```

### Операции с подами

```bash
kubectl logs <pod>                        # Логи пода
kubectl logs -f <pod>                     # Логи в реальном времени
kubectl exec -it <pod> -- /bin/bash       # Shell в поде
kubectl exec -it <pod> -c <container> -- sh  # В конкретном контейнере
kubectl delete pod <name>                 # Удалить под
kubectl delete pod <name> --grace-period=0 --force  # Принудительно
```

---

## 🚢 Развертывания и ReplicaSets

### Создание развертываний

```bash
kubectl create deployment nginx --image=nginx                    # Создать
kubectl create deployment webapp --image=nginx --replicas=3      # С репликами
kubectl apply -f deployment.yaml                                 # Из YAML
kubectl expose deployment nginx --port=80 --type=LoadBalancer    # Как служба
```

### Управление развертываниями

```bash
kubectl get deployments                   # Список
kubectl describe deployment <name>        # Описание
kubectl edit deployment <name>            # Редактирование
kubectl delete deployment <name>          # Удаление
```

### Масштабирование

```bash
kubectl scale deployment nginx --replicas=5              # Масштабировать
kubectl scale rs <name> --replicas=3                     # ReplicaSet
kubectl autoscale deployment nginx --min=2 --max=10 --cpu-percent=80  # Авто
```

### Поэтапные обновления

```bash
kubectl rollout status deployment/nginx              # Статус
kubectl rollout history deployment/nginx             # История
kubectl rollout undo deployment/nginx                # Откат
kubectl rollout undo deployment/nginx --to-revision=2  # К ревизии
```

---

## 🌍 Службы и сетевое взаимодействие

### Открытие служб

```bash
kubectl expose deployment nginx --port=80                    # ClusterIP
kubectl expose deployment nginx --port=80 --type=NodePort    # NodePort
kubectl expose deployment nginx --port=80 --type=LoadBalancer # LoadBalancer
kubectl apply -f service.yaml                                # Из YAML
```

### Обнаружение служб

```bash
kubectl get services              # Список служб
kubectl get svc -o wide           # С деталями
kubectl describe service <name>   # Описание
kubectl get endpoints             # Конечные точки
```

### Перенаправление портов

```bash
kubectl port-forward pod/<name> 8080:80              # Порт пода
kubectl port-forward svc/<name> 8080:80              # Порт службы
kubectl port-forward deployment/<name> 8080:80       # Порт deployment
kubectl port-forward pod/<name> 8080:80 8443:443     # Несколько портов
```

### Управление Ingress

```bash
kubectl get ingress              # Список Ingress
kubectl describe ingress <name>  # Описание
kubectl apply -f ingress.yaml    # Создать из YAML
```

---

## 🔐 ConfigMaps и секреты

### ConfigMaps

```bash
# Создать из литералов
kubectl create configmap app-config --from-literal=database_url=localhost --from-literal=debug=true

# Создать из файла
kubectl create configmap app-config --from-file=app.properties

# Создать из каталога
kubectl create configmap app-config --from-file=config/

# Просмотр
kubectl get configmaps
kubectl describe configmap app-config
kubectl get configmap app-config -o yaml

# Редактирование и удаление
kubectl edit configmap app-config
kubectl delete configmap app-config
```

### Секреты

```bash
# Общий секрет
kubectl create secret generic db-secret --from-literal=username=admin --from-literal=password=secret123

# Из файла
kubectl create secret generic ssl-certs --from-file=tls.crt --from-file=tls.key

# Docker registry
kubectl create secret docker-registry my-registry --docker-server=myregistry.com --docker-username=user --docker-password=pass

# Просмотр
kubectl get secrets
kubectl describe secret db-secret

# Декодировать значение
kubectl get secret db-secret -o jsonpath='{.data.password}' | base64 -d

# Удалить
kubectl delete secret db-secret
```

---

## 💾 Хранилище и тома

### Persistent Volumes (PV)

```bash
kubectl get pv                       # Список
kubectl describe pv <name>           # Описание
kubectl apply -f persistent-volume.yaml  # Создать из YAML
kubectl delete pv <name>             # Удалить
```

### Persistent Volume Claims (PVC)

```bash
kubectl get pvc              # Список
kubectl describe pvc <name>  # Описание
kubectl apply -f pvc.yaml    # Создать из YAML
kubectl delete pvc <name>    # Удалить
```

### Storage Classes

```bash
kubectl get storageclass              # Список
kubectl describe storageclass <name>  # Описание

# Установить по умолчанию
kubectl patch storageclass <name> -p '{"metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

### Операции с томами

```bash
kubectl describe pod <name> | grep -A5 "Mounts:"         # Монтирование
kubectl get pod <name> -o yaml | grep -A10 "volumes:"     # Тома в поде
```

---

## 🔍 Устранение неполадок

### Логи и события

```bash
kubectl logs <pod>                                   # Логи пода
kubectl logs -f <pod>                                # В реальном времени
kubectl logs <pod> --previous                        # Предыдущий контейнер
kubectl logs <pod> -c <container>                    # Конкретный контейнер
kubectl get events --sort-by=.metadata.creationTimestamp  # События
```

### Инспекция ресурсов

```bash
kubectl describe pod <name>          # Под
kubectl describe deployment <name>   # Развертывание
kubectl describe service <name>      # Служба
kubectl describe node <name>         # Узел
```

### Использование ресурсов

```bash
kubectl top nodes                    # Узлы
kubectl top pods                     # Поды
kubectl top pods -n <namespace>      # В пространстве имен
kubectl top pods --sort-by=cpu       # Сортировка по CPU
```

### Интерактивная отладка

```bash
kubectl exec -it <pod> -- /bin/bash                          # Shell
kubectl debug <pod> -it --image=busybox                      # Эфемерный контейнер
kubectl cp <pod>:/path/to/file ./local-file                  # Копировать из пода
kubectl cp ./local-file <pod>:/path/to/destination           # Копировать в под
```

---

## 🛠 Управление ресурсами

### Применение ресурсов

```bash
kubectl apply -f deployment.yaml                             # Один файл
kubectl apply -f deployment.yaml -f service.yaml             # Несколько файлов
kubectl apply -f ./k8s-configs/                              # Каталог
kubectl apply -f https://example.com/manifest.yaml           # Из URL
kubectl apply -f deployment.yaml --dry-run=client -o yaml    # Dry-run
```

### Операции с ресурсами

```bash
kubectl get all                                              # Все ресурсы
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase
kubectl get deployment nginx -o yaml                         # YAML
kubectl get pod <name> -o json                               # JSON
kubectl delete -f deployment.yaml                            # Удалить из файла
kubectl delete pod,service -l app=nginx                      # По метке
```

### Редактирование ресурсов

```bash
kubectl edit deployment <name>                               # Интерактивно

# Патч (strategic merge)
kubectl patch deployment nginx -p '{"spec":{"replicas":3}}'

# JSON patch
kubectl patch pod <name> --type='json' -p='[{"op": "replace", "path": "/metadata/labels/env", "value": "prod"}]'

kubectl replace -f updated-deployment.yaml                   # Замена
```

### Валидация ресурсов

```bash
kubectl diff -f deployment.yaml                              # Различия
kubectl explain pod.spec.containers                          # Структура
kubectl explain deployment --recursive                       # С примерами
kubectl apply -f deployment.yaml --dry-run=client --validate=true
```

---

## ⚡ Расширенные операции

### Управление узлами

```bash
kubectl cordon <node>                                        # Недоступен для планирования
kubectl uncordon <node>                                      # Доступен
kubectl drain <node> --ignore-daemonsets --delete-emptydir-data  # Осушить
kubectl taint nodes <node> key=value:NoSchedule              # Добавить taint
kubectl taint nodes <node> key:NoSchedule-                   # Удалить taint
```

### Метки и аннотации

```bash
kubectl label pod <name> environment=production              # Добавить метку
kubectl label pod <name> environment-                        # Удалить метку
kubectl annotate pod <name> description="Frontend web server" # Аннотация

# Выбор по меткам
kubectl get pods -l environment=production
kubectl get pods -l 'environment in (production,staging)'
```

### Прокси и аутентификация

```bash
kubectl proxy --port=8080                                    # Прокси API
kubectl auth can-i create pods                               # Проверка прав
kubectl auth can-i '*' '*' --as=system:serviceaccount:default:my-sa
kubectl get pods --as=system:serviceaccount:default:my-sa    # От имени SA
kubectl config view --raw -o jsonpath='{.users[*].name}'     # Пользователи
```

### Вспомогательные команды

```bash
kubectl wait --for=condition=Ready pod/<name> --timeout=300s     # Ожидание
kubectl run tmp-pod --rm -i --tty --image=busybox -- /bin/sh     # Временный под
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml  # Генерация YAML
kubectl get pods --sort-by=.metadata.creationTimestamp           # Сортировка
```

---

## 📊 Производительность и мониторинг

### Метрики ресурсов

```bash
kubectl top nodes --sort-by=cpu                    # Узлы по CPU
kubectl top nodes --sort-by=memory                 # Узлы по памяти
kubectl top pods --sort-by=cpu                     # Поды по CPU
kubectl top pods --sort-by=memory -A               # Поды по памяти (все ns)
kubectl top pods --containers=true                 # По контейнерам
kubectl top pods --previous                        # История
```

### Проверки состояния

```bash
kubectl rollout status deployment/<name>                        # Статус deployment
kubectl get pods --field-selector=status.phase=Running          # Готовые поды
kubectl get resourcequota                                       # Квоты
kubectl describe resourcequota <name>                           # Описание квоты
kubectl get componentstatuses                                   # Компоненты кластера
```

### Оптимизация

```bash
kubectl describe node <name> | grep -A5 "Allocated resources:"  # Запросы/лимиты
kubectl get pdb                                                 # Budget disruption
kubectl get hpa                                                 # Автоскейлеры
kubectl get networkpolicy                                       # Сетевые политики
```

### Резервное копирование

```bash
# Все ресурсы в пространстве имен
kubectl get all -o yaml -n <namespace> > backup.yaml

# Конкретный ресурс
kubectl get deployment <name> -o yaml > deployment-backup.yaml

# Список всех ресурсов
kubectl api-resources --verbs=list --namespaced -o name | xargs -n 1 kubectl get --show-kind --ignore-not-found -n <namespace>
```

---

## ⚙️ Управление конфигурацией

### Управление контекстом

```bash
kubectl config current-context                    # Текущий контекст
kubectl config get-contexts                       # Список контекстов
kubectl config use-context <name>                 # Переключение

# Создание нового контекста
kubectl config set-context dev-context --cluster=dev-cluster --user=dev-user --namespace=development
```

### Управление Kubeconfig

```bash
kubectl config view                               # Просмотр

# Установка кластера
kubectl config set-cluster <name> --server=https://cluster-api-url --certificate-authority=/path/to/ca.crt

# Установка учетных данных
kubectl config set-credentials <name> --client-certificate=/path/to/client.crt --client-key=/path/to/client.key

# Слияние файлов
KUBECONFIG=~/.kube/config:~/.kube/config2 kubectl config view --merge --flatten > ~/.kube/merged-config
```

### Настройки по умолчанию

```bash
# Пространство имен по умолчанию
kubectl config set-context --current --namespace=<namespace>

# Формат вывода по умолчанию
kubectl config set-context --current --output=yaml

# Детали конфигурации
kubectl config view -o jsonpath='{.users[*].name}'
kubectl config view --raw
```

---

## 💡 Лучшие практики

### Эффективность команд

```bash
# Псевдонимы
alias k=kubectl
alias kgp='kubectl get pods'
alias kgs='kubectl get services'
alias kgd='kubectl get deployments'

# Короткие имена ресурсов
kubectl get po        # pods
kubectl get svc       # services
kubectl get deploy    # deployments
kubectl get ns        # namespaces
kubectl get no        # nodes

# Наблюдение
kubectl get pods --watch
kubectl get events --watch
```

### Выбор ресурсов

```bash
# По меткам
kubectl get pods -l app=nginx
kubectl get pods -l 'environment in (prod,staging)'
kubectl get pods -l app=nginx,version!=v1.0

# По полю
kubectl get pods --field-selector=status.phase=Running
kubectl get pods --field-selector=spec.nodeName=worker-node-1

# Комбинирование
kubectl get pods -l app=nginx --field-selector=status.phase=Running
```

### Форматирование вывода

```bash
# Форматы
kubectl get pods -o wide
kubectl get pods -o yaml
kubectl get pods -o json
kubectl get pods -o name

# Пользовательские столбцы
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName

# JSONPath
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].image}'
```

### Безопасность и валидация

```bash
# Dry-run
kubectl apply -f deployment.yaml --dry-run=client -o yaml

# Валидация
kubectl apply -f deployment.yaml --validate=true --dry-run=client

# Различия
kubectl diff -f deployment.yaml

# Принудительное удаление
kubectl delete pod <name> --grace-period=0 --force
```

---

<div align="center">

**📚 Полезные ссылки**

[Официальная документация Kubernetes](https://kubernetes.io/docs/) • [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---

*Шпаргалка создана для быстрого доступа к командам kubectl*

</div>
