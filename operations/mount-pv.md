# 挂载PV

```text
pxtools/cli/px-yaml.sh test --registry daocloud.io
Created file at ./px-yamls/test
```

{% code-tabs %}
{% code-tabs-item title="px-yamls/test/rwo-deploy-test" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rwo-deploy-test-nginx
  labels:
    dce.daocloud.io/component: rwo-deploy-test-nginx
    dce.daocloud.io/app: rwo-deploy-test
  namespace: portworx
  annotations:
    deployment.kubernetes.io/revision: '1'
  selfLink: /apis/apps/v1/namespaces/portworx/deployments/rwo-deploy-test-nginx
spec:
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  replicas: 1
  selector:
    matchLabels:
      dce.daocloud.io/component: rwo-deploy-test-nginx
  template:
    metadata:
      labels:
        dce.daocloud.io/component: rwo-deploy-test-nginx
        dce.daocloud.io/app: rwo-deploy-test
      name: rwo-deploy-test-nginx
    spec:
#     imagePullSecrets:
#       - name: "regcred-saicstack"
      initContainers:
      - image: daocloud.io/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: rwo-deploy-test-nginx-init
        command: [
          'sh',
          '-c',
          'echo "<center><h1>Testing volume $( df | grep -E pxd\|pxfs\|pxns )</h1></center>" > /usr/share/nginx/html/index.html'
          ]
        volumeMounts:
        - name: html-root
          mountPath: /usr/share/nginx/html
        resources:
          requests:
            cpu: '100m'
            memory: '100Mi'
          limits:
            cpu: '500m'
            memory: '500Mi'
      containers:
      - image: daocloud.io/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: rwo-deploy-test-nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: html-root
          mountPath: /usr/share/nginx/html
        resources:
          requests:
            cpu: '100m'
            memory: '100Mi'
          limits:
            cpu: '500m'
            memory: '500Mi'
      volumes:
      - name: html-root
        persistentVolumeClaim:
          claimName: pvc-hdd-ha2-rwo
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: px/enabled
                operator: In
                values:
                - "true"
---
apiVersion: v1
kind: Service
metadata:
  name: rwo-deploy-test-nginx
  labels:
    dce.daocloud.io/app: rwo-deploy-test
  namespace: portworx
  annotations:
    io.daocloud.dce.serviceSelectorType: service
  selfLink: /api/v1/namespaces/portworx/services/rwo-deploy-test-nginx
spec:
  type: NodePort
  ports:
    - name: rwo-deploy-test-nginx-80
      protocol: TCP
      targetPort: 80
      port: 80
      nodePort: 31278
  selector:
    dce.daocloud.io/component: rwo-deploy-test-nginx


```
{% endcode-tabs-item %}

{% code-tabs-item title="px-yamls/test/rwx-deploy-test" %}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: rwx-deploy-test-nginx
  labels:
    dce.daocloud.io/component: rwx-deploy-test-nginx
    dce.daocloud.io/app: rwx-deploy-test
  namespace: portworx
  annotations:
    deployment.kubernetes.io/revision: '1'
  selfLink: /apis/apps/v1/namespaces/portworx/deployments/rwx-deploy-test-nginx
spec:
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  replicas: 3
  selector:
    matchLabels:
      dce.daocloud.io/component: rwx-deploy-test-nginx
  template:
    metadata:
      labels:
        dce.daocloud.io/component: rwx-deploy-test-nginx
        dce.daocloud.io/app: rwx-deploy-test
      name: rwx-deploy-test-nginx
    spec:
#     imagePullSecrets:
#       - name: "regcred-saicstack"
      initContainers:
      - image: daocloud.io/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: rwx-deploy-test-nginx-init
        command: [
          'sh',
          '-c',
          'echo "<center><h1>Testing volume $( df | grep -E pxd\|pxfs\|pxns )</h1></center>" > /usr/share/nginx/html/index.html'
          ]
        volumeMounts:
        - name: html-root
          mountPath: /usr/share/nginx/html
        resources:
          requests:
            cpu: '100m'
            memory: '100Mi'
          limits:
            cpu: '500m'
            memory: '500Mi'
      containers:
      - image: daocloud.io/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: rwx-deploy-test-nginx
        ports:
        - containerPort: 80
        volumeMounts:
        - name: html-root
          mountPath: /usr/share/nginx/html
        resources:
          requests:
            cpu: '100m'
            memory: '100Mi'
          limits:
            cpu: '500m'
            memory: '500Mi'
      volumes:
      - name: html-root
        persistentVolumeClaim:
          claimName: pvc-ssd-ha3-rwx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: px/enabled
                operator: In
                values:
                - "true"
---
apiVersion: v1
kind: Service
metadata:
  name: rwx-deploy-test-nginx
  labels:
    dce.daocloud.io/app: rwx-deploy-test
  namespace: portworx
  annotations:
    io.daocloud.dce.serviceSelectorType: service
  selfLink: /api/v1/namespaces/portworx/services/rwx-deploy-test-nginx
spec:
  type: NodePort
  ports:
    - name: rwx-deploy-test-nginx-80
      protocol: TCP
      targetPort: 80
      port: 80
      nodePort: 31282
  selector:
    dce.daocloud.io/component: rwx-deploy-test-nginx

```
{% endcode-tabs-item %}
{% endcode-tabs %}

