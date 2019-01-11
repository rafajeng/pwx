# 创建StorageClass

 概述

Kubernetes里, StorageClass定义了PV\(持久卷\)的模板。通过StorageClass, 用户可以在创建PVC\(持久卷申请\)的时候同时创建出相应的PV。

Pxtool工具集里提供了常用的StorageClass的yaml文件。

{% hint style="info" %}
如需定制更详细的StorageClass,请参阅本章附录
{% endhint %}

## 步骤 <a id="steps"></a>

### 1. 创建yaml文件

```text
$ pxtools/cli/px-yaml.sh sc -v basic
Created file as ./px-yamls/sc/sc-basic.yaml
```

### 2. 创建常用StorageClass

```text
$ kubectl apply -f px-yamls/sc/sc-basic.yaml

$ kubectl get storageclass -o wide 
```

## 附录

### StorageClass yaml格式

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
```

### PX StorageClass 常见参数

| 参数 | 描述 | 默认值 | 可选值 |
| :--- | :--- | :--- | :--- |
| repl | 副本数 | 1 | 1, 2, 3 |
| shared | 共享卷 | false | true, false |
| priority\_io | 存储池 | low | high, medium, low |
| journal | 日志缓存 | false | true, false |
| io\_profile | IO类型 | sequential | db, sequential, cms |
| sticky | 方删除 | false | true, false |

{% hint style="info" %}
更多参数请见[官方文档](https://docs.portworx.com/portworx-install-with-kubernetes/storage-operations/create-pvcs/dynamic-provisioning/#using-dynamic-provisioning)
{% endhint %}



