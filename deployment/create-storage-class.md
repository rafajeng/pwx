# 创建StorageClass

## 概述 <a id="overview"></a>

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

PX StorageClass 参数

| 参数 | 描述 | 可选值 |
| :--- | :--- | :--- |
| repl | 副本数 |  |
| shared | 共享卷 |  |
| priority\_io | 存储池 |  |

