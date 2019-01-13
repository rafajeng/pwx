# 创建PV

## 概述 <a id="overview"></a>

本章节描述K8S动态创建PV的方法，也就是**Dynamic Provisioning**. 使用这种方法，PV是在创建PVC的时候根据SC创建出来。

## 步骤 <a id="steps"></a>

### 1. 编写PVC的yaml文件

这里试用Pxtools里的示例:

{% code-tabs %}
{% code-tabs-item title="pxtools/example/pvc-example.yaml" %}
```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-hdd-ha2-rwo
  annotations:
    volume.beta.kubernetes.io/storage-class: px-hdd-ha2-rwo
  namespace: portworx
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 11Gi
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-ssd-ha3-rwx
  annotations:
    volume.beta.kubernetes.io/storage-class: px-ssd-ha3-rwx
  namespace: portworx
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 13Gi

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 2. 执行yaml文件

```text
kubectl apply -f pvc-example.yaml
```

### 3. 查看PVC状态

"Bound"说明PVC成功和PV绑定

```text
kubectl -n portworx get pvc 
```

### 4. 查看PV状态

"Bound"说明PVC成功和PV绑定

```text
kubectl -n portworx get pv
```

### 5. 查看PX卷状态

{% hint style="info" %}
PX卷的名字继承了PV的名字。
{% endhint %}

```text
pxctl volume list
```

