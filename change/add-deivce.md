---
description: Add data device
---

# 添加数据盘

## 概述 <a id="overview"></a>

添加数据盘是给单个PX节点扩容。

{% hint style="danger" %}
PX目前不支持在线添加数据盘。必须进入维护模式。在维护模式下，PX卷不能响应IO。所以在添加数据盘之前，请把使用PX卷的容器迁移到别的节点上。
{% endhint %}

{% hint style="warning" %}
PX添加数据盘时需要做数据再均衡\(Rebalance\), 而且不能同时添加多块数据盘。所以在即有数据量比较大的时候，添加多块数据盘会消耗很长时间。
{% endhint %}

{% hint style="info" %}
鉴于以上两点，在所有PX卷默认**3副本**的情况下，可以通过[**删除节点**](remove-node.md)**然后重新**[**添加节点**](add-node.md)的方法快速添加多块硬盘。
{% endhint %}

## 步骤

### 1. 进入维护模式

```text
$ pxctl service maintenance --enter
This is a disruptive operation, PX will restart in maintenance mode.
Are you sure you want to proceed ? (Y/N): y
Entering maintenance mode...

$ pxctl status
Status: PX is in maintenance mode
```

### 2. 添加数据盘

```text
$ pxctl service drive add --drive /dev/sdc
Drive add done: Storage rebalance is in progress
```

### 3. 查看进度

```text
$ watch "pxctl service drive add --drive /dev/sdc --operation status"
```

### 4. 确认数据盘添加成功

```text
$ pxctl service drive add --drive /dev/sdc --operation status
Drive add: Storage rebalance complete

$ pxctl service drive show
```

### 5. 退出维护模式

```text
$ pxctl service maintenance --exit
Exiting maintenance mode...

$ journalctl -fu portworx

$ pxctl status
```



