---
description: Create SC
---

# 创建SC

## 概述

SC是**StorageClass**的缩写。K8S用SC来定义**持久卷PV**的模板。通过SC, 用户可以在创建**持久卷申请PVC**的时候同时创建出相应的PV。

Pxtool工具集里提供了常用的SC的yaml文件。

{% hint style="info" %}
如需定制更详细的SC,请参考本章附录
{% endhint %}

## 步骤 <a id="steps"></a>

### 1. 创建yaml文件

```text
$ pxtools/cmd/px-yaml.sh sc
Created file at ./px-yamls/sc/
```

### 2. 创建常用SC

```text
$ kubectl apply -f px-yamls/sc/sc-basic.yaml

$ kubectl get storageclass -o wide 
```

{% hint style="info" %}
Pxtools根据 ”HDD/SSD“, ”独占/共享“，”1/2/3副本“ 这三个配置，提供了12种常见的SC。用户可以根据自己的需要做删减。
{% endhint %}

### \*3. 删除SC

```text
$ kubectl delete sc <SC NAME>
```

## 附录

### SC yaml格式

{% code-tabs %}
{% code-tabs-item title="pxtools/k8s/sc/sc-basic.yaml" %}
```yaml
apiVersion: storage.k8s.io/v1

kind: StorageClass
metadata:
  name: px-hdd-ha3-rwo
parameters:
  priority_io: "low"
  repl: "3"
  shared: "false"
provisioner: kubernetes.io/portworx-volume
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: px-ssd-ha2-rwo
parameters:
  priority_io: "high"
  repl: "2"
  shared: "true"
provisioner: kubernetes.io/portworx-volume
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### PX SC 常见参数

| 参数 | 描述 | 默认值 | 可选值 |
| :--- | :--- | :--- | :--- |
| repl | 副本数 | 1 | 1, 2, 3 |
| shared | 共享卷 | false | true, false |
| priority\_io | 存储池 | low | high, medium, low |
| journal | 日志缓存 | false | true, false |
| io\_profile | IO类型 | sequential | db, sequential, cms |
| sticky | 防删除 | false | true, false |

{% hint style="info" %}
更多参数请参考[官方文档](https://docs.portworx.com/portworx-install-with-kubernetes/storage-operations/create-pvcs/dynamic-provisioning/#using-dynamic-provisioning)
{% endhint %}



