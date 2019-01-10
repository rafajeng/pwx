# 部署Etcd

## 概述

PX使用Etcd做集群发现。生产系统需要搭建**专用**的Etcd集群。道云提供**px-etcd.sh**脚本用于创建和维护专用的Etcd集群

{% hint style="info" %}
生产场景中，px-etcd.sh脚本必须在道云工程师的协助下使用
{% endhint %}

## 最佳实践

* 使用3节点的Etcd高可用；大规模PX集群可以使用5节点的Etcd高可用。 
* 使用PX的数据网络作为PX-Etcd的Client网络和Peer网路。
* 条件允许，可以把Etcd部署在非PX的节点上

## 部署步骤

以下示例中，三台主机的PX存储网络的IP分别是： 10.10.176.161， 10.10.176.162， 10.10.176.163 

### 1. 下载pxtools脚本工具集

```text
$ docker run --rm -v $(pwd):/drop daocloud.io/portworx/pxtools
'/pxtools' -> '/drop/pxtools'
  
$ ls -lh
drwxr-xr-x  7 root root   78 Dec  2 18:26 pxtools
```

### 2. 部署第一个节点

```text
$ bash pxtools-edit/etcd/px-etcd.sh create 10.10.176.161

$ /opt/pwx-etcd/bin/runc exec portworx-etcd etcdctl cluster-health
member 4afad2f557455f4 is healthy: got healthy result from http://10.10.176.161:19019
cluster is healthy
```

### 3. 部署第二个节点

```text

```

### 4. 部署第三个节点

```text

```

