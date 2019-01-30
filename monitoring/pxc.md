# PXC

## 概述 <a id="overview"></a>

PXC指的是PX-Central监控套件。它包括以下组件:

| 组件 |  |
| :--- | :--- |
| Lighthouse |  |

1. Lighthouse
2. Prometheus
3. Grafana
4. AlertManager

## 步骤 <a id="steps"></a>

{% hint style="warning" %}
请务必按照顺序部署执行yaml文件
{% endhint %}

### 1. 创建yaml文件

```text
$ pxtools/cli/px-yamls.sh mon --registry daocloud.io/portworx 
Created file at /root/px-yamls/mon/
```

### 2. 部署lighthouse

```text
$ kubctl apply -f px-yamls/mon/lighthouse.yaml
```

### 3. 部署prometheus Operator

```text
$ kubctl apply -f px-yamls/mon/prometheus-operator.yaml
```

### 4. 部署prometheus配置

```text
$ kubctl apply -f px-yamls/mon/prometheus-rules.yaml
$ kubctl apply -f px-yamls/mon/prometheus-cluster.yaml
```

### 5. 部署prometheus service monitor

```text
$ kubctl apply -f px-yamls/mon/prometheus-sm.yaml
```

### 6. 部署prometheus cluster

```text
$ kubctl apply -f px-yamls/mon/prometheus-cluster.yaml
```

### 7. 部署grafana图形界面

```text
$ kubectl apply -f px-yamls/mon/grafana-rules.yaml
$ kubectl apply -f px-yamls/mon/grafana-deploy.yaml
```

### 8. 部署alertmanager

```text
$ kubectl apply -f px-yamls/mon/alertmanager.yaml
$ kubectl apply -f px-yamls/mon/alertmanager-cluster.yaml
```

### 9.访问grafana

`http://<master_ip>:30950`

![](../.gitbook/assets/image%20%282%29.png)

