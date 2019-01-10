# 部署Etcd

## 概述

PX使用Etcd做集群发现。生产系统需要搭建专用的Etcd集群

* 建议使用3节点的PX-Etcd高可用。大规模PX集群可以使用5节点的Etcd集群
* 建议使用PX的数据网络作为PX-Etcd的Client网络和Peer网路
* 道云提供px-etcd.sh脚本用于创建和维护专用的Etcd集群

