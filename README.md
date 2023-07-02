# Домашнее задание к занятию «Хранение в K8s. Часть 1»

### Задание 1 

**Что нужно сделать**

Создать Deployment приложения, состоящего из двух контейнеров и обменивающихся данными.

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

------

### Задание 2

**Что нужно сделать**

Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.
4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

------

## Ответ:

### Задание 1 

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
 ```yaml
apiVersion : apps/v1
kind: Deployment
metadata:
  name: deployment-ex6
  labels:
    app: ex6
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ex6
  template:
    metadata:
      labels:
        app: ex6
    spec:
      containers:
        - name: busybox
          image: busybox
          command: ['sh', '-c', 'while true; do echo "test" >> /share/test.txt; sleep 5; done']
          volumeMounts:
          - mountPath: /share
            name: share
        - name: network-multitool
          image: wbitt/network-multitool
          volumeMounts:
          - mountPath: /share
            name: share
          env:
          - name: HTTP_PORT
            value: "80"
          - name: HTTPS_PORT
            value: "443"
          ports:
          - containerPort: 80
            name: http-port
          - containerPort: 443
            name: https-port
      volumes:
      - name: share
        emptyDir: {}
```  
2. Сделать так, чтобы busybox писал каждые пять секунд в некий файл в общей директории.
3. Обеспечить возможность чтения файла контейнером multitool.
4. Продемонстрировать, что multitool может читать файл, который периодоически обновляется.
 
![image](https://github.com/askarpoff/kuber_ex6/assets/108946489/a7915658-6f98-425b-b422-5a711e45fd16)

5. Предоставить манифесты Deployment в решении, а также скриншоты или вывод команды из п. 4.

[deployment.yaml](https://github.com/askarpoff/kuber_ex6/blob/main/deployment.yaml)

------

### Задание 2

Создать DaemonSet приложения, которое может прочитать логи ноды.

1. Создать DaemonSet приложения, состоящего из multitool.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemonset-ex6
  labels:
    app: ex6
spec:
  selector:
    matchLabels:
      app: ex6
  template:
    metadata:
      labels:
        app: ex6
    spec:
      containers:
      - name: network-multitool
        image: wbitt/network-multitool
        volumeMounts:
        - name: node-volume
          mountPath: /log/syslog
        env:
        - name: HTTP_PORT
          value: "80"
        - name: HTTPS_PORT
          value: "443"
        ports:
        - containerPort: 80
          name: http-port
        - containerPort: 443
          name: https-port
      volumes:
      - name: node-volume
        hostPath:
          path: /var/log/syslog
```
   
2. Обеспечить возможность чтения файла `/var/log/syslog` кластера MicroK8S.
3. Продемонстрировать возможность чтения файла изнутри пода.

![image](https://github.com/askarpoff/kuber_ex6/assets/108946489/c603aed7-c754-4f7c-9f97-8bdeff449afe)

4. Предоставить манифесты Deployment, а также скриншоты или вывод команды из п. 2.

[daemonset.yaml](https://github.com/askarpoff/kuber_ex6/blob/main/daemonset.yaml)

------
