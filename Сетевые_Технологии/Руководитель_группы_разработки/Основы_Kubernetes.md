Kubernetes (K8s) — это платформа с открытым исходным кодом для автоматизации развертывания, масштабирования и управления контейнеризированными приложениями. В этом разделе мы рассмотрим основные компоненты и концепции Kubernetes, а также основные операции для работы с ним.

### 1. Основные понятия Kubernetes

- **Pod** — минимальная единица развертывания в Kubernetes. Это один или несколько контейнеров, которые делят ресурсы, такие как сеть и хранилище. Контейнеры внутри одного pod могут работать вместе, как если бы они были на одной машине.
  
- **Node** — физический или виртуальный сервер, на котором запускаются Pod'ы. Каждый Node в Kubernetes управляется мастер-узлом.

- **Cluster** — группа Node'ов, управляющаяся мастер-узлом. Все узлы внутри кластера работают совместно для управления приложениями и сервисами.

- **Deployment** — объект, который описывает желаемое состояние приложения в кластере. Он управляет репликациями Pod'ов и обеспечивает их масштабирование, обновление и откат.

- **Service** — абстракция для обеспечения доступа к одному или нескольким Pod'ам. С помощью Service можно определить правила для связи с приложением и обеспечить его доступность в сети.

- **Namespace** — логическая изоляция внутри кластера, которая позволяет организовать ресурсы и управлять ими для различных команд и приложений.

- **ConfigMap и Secret** — объекты для хранения конфигураций и чувствительных данных (пароли, ключи и т. д.), которые могут быть использованы приложениями внутри Pods.

### 2. Основные команды kubectl

`kubectl` — это командная строка для взаимодействия с Kubernetes кластером. Вот несколько основных команд:

- **Проверка состояния кластера**:
  ```bash
  kubectl cluster-info
  ```

- **Список всех узлов в кластере**:
  ```bash
  kubectl get nodes
  ```

- **Список всех подов**:
  ```bash
  kubectl get pods
  ```

- **Создание ресурса (например, Pod)**:
  ```bash
  kubectl apply -f pod-definition.yaml
  ```

- **Печать информации о Pod'е**:
  ```bash
  kubectl describe pod <pod-name>
  ```

- **Просмотр логов контейнера в Pod'е**:
  ```bash
  kubectl logs <pod-name>
  ```

- **Удаление ресурса**:
  ```bash
  kubectl delete -f pod-definition.yaml
  ```

- **Подключение к контейнеру внутри Pod'а**:
  ```bash
  kubectl exec -it <pod-name> -- /bin/bash
  ```

### 3. Создание и управление Pod'ами

**Пример описания Pod'а**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
    ports:
    - containerPort: 80
```

Для создания Pod'а из этого манифеста используйте команду:
```bash
kubectl apply -f pod-definition.yaml
```

### 4. Работа с Deployments

**Пример манифеста для Deployment**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx
        ports:
        - containerPort: 80
```

Для создания Deployment из этого манифеста:
```bash
kubectl apply -f deployment-definition.yaml
```

- **Масштабирование Deployment**:
  ```bash
  kubectl scale deployment my-deployment --replicas=5
  ```

- **Обновление Deployment**:
  ```bash
  kubectl set image deployment/my-deployment my-container=nginx:latest
  ```

- **Откат Deployment**:
  ```bash
  kubectl rollout undo deployment/my-deployment
  ```

### 5. Работа с Services

**Пример манифеста для Service**:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

- **Типы Service**:
  - **ClusterIP** (по умолчанию) — доступен только внутри кластера.
  - **NodePort** — доступен извне через конкретный порт на каждом узле.
  - **LoadBalancer** — автоматически создает балансировщик нагрузки для внешнего доступа.

### 6. Хранение данных в Kubernetes

**Persistent Volumes (PV) и Persistent Volume Claims (PVC)** используются для управления данными, которые должны сохраняться при перезапуске Pods.

**Пример PV**:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mnt/data
```

**Пример PVC**:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  resources:
    requests:
      storage: 5Gi
  accessModes:
    - ReadWriteOnce
```

### 7. Автоскейлинг и управление ресурсами

Kubernetes позволяет автоматически масштабировать приложения и управлять их ресурсами (CPU, память).

- **Horizontal Pod Autoscaler** (HPA) автоматически масштабирует количество Pod'ов в зависимости от загрузки CPU:
  ```bash
  kubectl autoscale deployment my-deployment --cpu-percent=50 --min=1 --max=10
  ```

- **Resource Requests и Limits** помогают ограничить использование ресурсов контейнером:
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-pod
  spec:
    containers:
    - name: my-container
      image: nginx
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
  ```

### 8. Мониторинг и логирование

Kubernetes поддерживает интеграцию с различными системами мониторинга, такими как Prometheus, и системами логирования, такими как ELK (Elasticsearch, Logstash, Kibana).

- **Просмотр логов Pod'ов**:
  ```bash
  kubectl logs <pod-name>
  ```

- **Просмотр состояния всех ресурсов в кластере**:
  ```bash
  kubectl get all
  ```

### Заключение

Kubernetes является мощным инструментом для управления контейнерами и автоматизации развертывания приложений. В этом разделе были рассмотрены ключевые концепции и команды Kubernetes, которые помогут вам начать работу с кластером и эффективно управлять ресурсами.
