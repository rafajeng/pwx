# PXC

## 概述 <a id="overview"></a>

PXC指的是PX-Central监控套件。它包括以下组件。

1. Lighthouse
2. Prometheus
3. Grafana
4. AlertManager

## 步骤 <a id="steps"></a>

### 1. 创建yaml文件

```text
pxtools/cli/px-yamls.sh mon --registry daocloud.io/portworx 
Created file
```

### 2. 部署Lighthouse

```text
kubctl apply -f px-yamls/mon/lighthouse.yaml
```

### 3. 部署Prometheus

```text
kubctl apply -f px-yamls/mon/prometheus.yaml
```

### 4. 部署Grafana

```text
kubctl apply -f px-yamls/mon/grafana.yaml
```

### 5. 部署AlertManager

```text
kubctl apply -f px-yamls/mon/alertmanager.yaml
```

### 6. 检查部署

```text
kubectl -n kube-system get deployments -o wide

kubectl -n kube-system get pods -o wide
```

### 7. 访问Grafana

`http://<master_ip>:30950`

![](../.gitbook/assets/image%20%282%29.png)

