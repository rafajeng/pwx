# 删除节点

## 概述 <a id="steps"></a>

{% hint style="danger" %}
删除PX点会清除该节点上的数据和配置，请务必在操作前将使用PX卷的容器迁移到别节点上
{% endhint %}

## 步骤 <a id="steps"></a>

### 1. 准备 <a id="preparation"></a>

确保没有任何单副本的卷在即将删除的PX节点上。使用`px-vol.sh`脚本过滤出有副本在目标节点的数据卷列表

```text
$ pxtools/cli/px-vol.sh filter --replica 10.10.176.163
```

{% hint style="danger" %}
如果发现有单副本卷的数据在即将删除的PX节点上，请务必在删除PX节点前将那些卷调整为**双副本或者三副本**，具体方法请参考章节”[增减副本](../operations/change-replica-number.md)“
{% endhint %}

### 3. 删除K8S节点标签

px/enabled标签被删除后，oci-monitor容器会从节点移除，**但是PX会继续运行**

```text
$ kubectl label node k8s-node-3 px/enabled-
```

### 4. 进入维护模式

在即将删除的PX节点上执行：

```text
$ pxctl service maintenance --enter

$ pxctl status
```

### 5. 从PX集群中删除节点

在任意其它任意一个PX节点上执行：

```text
$ pxctl cluster delete 10.10.176.163
```

### 6. 清除PX配置和数据

{% hint style="danger" %}
这一步骤将彻底清除节点上的数据，且不可逆。
{% endhint %}

在已经从PX集群中删除的节点上执行：

```text
pxctools/cli/px-node.sh cleanup
```

