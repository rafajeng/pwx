# 删除节点

## 概述

{% hint style="danger" %}
删除PX点会清除改节点上的数据，请务必谨慎
{% endhint %}

## 步骤

### 1. 把使用PX卷的应用容器全部迁移到别的节点上

### 2. 确保没有任何单副本的卷在即将删除的PX节点上

使用`px-vol.sh`脚本过滤出有副本在目标节点的数据卷列表

```text
$ pxtools/cli/px-vol.sh filter --replica 10.10.176.163
```

{% hint style="danger" %}
如果发现有单副本卷的数据在即将删除的PX节点上，请务必在删除PX节点前将那些卷调整为双副本或者三副本，具体方法请参考章节”[增减副本](../operations/change-replica-number.md)“
{% endhint %}

### 3. 删除K8S节点标签

px/enabled标签被删除后，oci-monitor容器会从节点移除，**但是PX会继续运行**

```text
$ kubectl label node k8s-node-3 px/enabled-
```

### 4. 把即将删除的PX节点置为维护模式

```text
$ pxctl 
```



