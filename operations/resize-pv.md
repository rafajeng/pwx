# 扩大PV

## 步骤

### 1. 修改PVC的yaml文件中的resource/request/storage参数

这里继续使用pxtools里的示例

{% code-tabs %}
{% code-tabs-item title="px-yamls/test/pvc-test.yaml" %}
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
      storage: 21Gi
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
      storage: 23Gi
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 2. 执行yaml文件

```text
kubectl apply -f ./px-yamls/pvc/pvc-test.yaml
```

