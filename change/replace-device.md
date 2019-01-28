# 更换数据盘

## 概述 <a id="overview"></a>

当一块数据盘出现故障后，如果硬件层面更换这块数据盘，PX层面也需要执行更换数据盘的操作。

{% hint style="danger" %}
PX目前不支持在线更换数据盘。必须进入维护模式。在维护模式下，PX卷不能响应IO。所以在更换数据盘之前，请把使用PX卷的容器迁移到别的节点上。
{% endhint %}

{% hint style="warning" %}
PX更换数据盘时需要做数据再均衡\(Rebalance\), 而且不能同时添加多块数据盘。所以在即有数据量比较大的时候，更换多块数据盘会消耗很长时间。
{% endhint %}

{% hint style="info" %}
鉴于以上两点，在所有PX卷默认**3副本**的情况下，可以通过[**删除节点**](remove-node.md)**然后重新**[**添加节点**](add-node.md)的方法快速更换多块硬盘。
{% endhint %}

## 步骤 <a id="steps"></a>

###  1. 进入维护模式

```text
$ pxctl service maintenance --enter
This is a disruptive operation, PX will restart in maintenance mode.
Are you sure you want to proceed ? (Y/N): y
Entering maintenance mode...

$ pxctl status
Status: PX is in maintenance mode
```

### 2. 更换数据盘

```text
$ pxctl service drive replace \
--source /dev/sdc --target /dev/sdd \
--operation start --verbose
```

### 3. 查看进度

```text
$ pxctl service drive replace \
--source /dev/sdc --target /dev/sdd \
--operation status --verbose
```

### 4. 确认数据盘更换成功

```text
$ pxctl service drive replace \
--source /dev/sdc --target /dev/sdd \
--operation status --verbose

$ pxctl service drive show
```



