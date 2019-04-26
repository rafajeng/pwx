---
description: Deploy etcd
---

# 部署Etcd

## 概述 <a id="overview"></a>

PX使用Etcd做集群发现。生产系统需要搭建**专用**的Etcd集群。道云提供**px-etcd.sh**脚本用于创建和维护专用的Etcd集群

{% hint style="info" %}
生产场景中，px-etcd.sh脚本必须在道云工程师的协助下使用
{% endhint %}

## 最佳实践 <a id="best-practice"></a>

* 使用3节点的Etcd高可用；大规模PX集群可以使用5节点的Etcd高可用。 
* 使用PX的数据网络作为PX-Etcd的Client网络和Peer网路。
* 条件允许，可以把Etcd部署在非PX的节点上

## 步骤 <a id="steps"></a>

{% hint style="info" %}
px-etcd.sh在**pxtools**工具集中。如何获取pxtools请参考章节”[环境准备](prepare-environment.md#pxtools-gong-ju-ji)“。
{% endhint %}

以下示例中，三台主机的PX存储网络的IP分别是： 10.10.176.161， 10.10.176.162， 10.10.176.163 

### 1. 部署第一个节点 <a id="step1"></a>

```text
$ bash pxtools/etcd/px-etcd.sh create --ip 10.10.176.161

$ /opt/pwx-etcd/bin/runc exec portworx-etcd etcdctl cluster-health
member 4afad2f557455f4 is healthy: got healthy result from http://10.10.176.161:19019
cluster is healthy
```

### 2. 部署第二个节点 <a id="step2"></a>

```text
$ bash pxtools/etcd/px-etcd.sh --ip join 10.10.176.161

$ /opt/pwx-etcd/bin/runc exec portworx-etcd etcdctl cluster-health
member 4afad2f557455f4 is healthy: got healthy result from http://10.10.176.161:19019
member ac13a77dc0affc68 is healthy: got healthy result from http://10.10.176.162:19019
cluster is healthy
```

### 3. 部署第三个节点 <a id="step3"></a>

```text
$ bash pxtools/etcd/px-etcd.sh join --ip 10.10.176.161

$ /opt/pwx-etcd/bin/runc exec portworx-etcd etcdctl cluster-health
member 4afad2f557455f4 is healthy: got healthy result from http://10.10.176.161:19019
member 642c48a9ddf6fd84 is healthy: got healthy result from http://10.10.176.163:19019
member ac13a77dc0affc68 is healthy: got healthy result from http://10.10.176.162:19019
```



