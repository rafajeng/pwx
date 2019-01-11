# 添加节点

## 常规操作

K8S环境中添加PX节点，只需要给新的节点打上**px/enabled=true**的标签即可。

```text
$ kubectl label node k8s-node-4 px/enabled=true
```

## 如何应对异构环境

Daemonset会给每个节点配置同样的PX参数，所以默认每个节点的硬件配置一致。如果遇到不一致的硬件配置，就需要特别处理。常见的有以下两个场景：

#### 1. 数据盘配置不一致

在px-opts文件里使用`-A`参数，PX会默认使用所有裸盘和裸分区，同时避开任何有文件系统的设备； 使用`-j auto`参数, PX自动会把日志缓冲创建在SSD/NVME设备上。例如：

{% code-tabs %}
{% code-tabs-item title="px-opts.txt" %}
```text
-c PX-Cluster1-5802fe79-17e3-484d-8de1-2223513c519b
-k etcd:http://10.10.176.161:19019,etcd:http://10.10.176.162:19019,etcd:http://10.10.176.163:19019
-m ens192
-d ens224
-A
-j auto
-secret_type kvdb
-x kubernetes
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
如果需要PX避开某个落盘或者裸分区，可以现在那些设备上创建XFS文件系统。等PX部署完成之后再清除那些文件系统。具体操作请咨询道云。
{% endhint %}

{% hint style="info" %}
新节点加入PX集群之后，其数据盘配置存入Etcd, 不再受本地配置文件影响
{% endhint %}

#### 2. 网卡名不一致

在给新节点打上px/enable=true之前，使用**px-node.sh**更改PX的runc配置。其原理在/opt/pwx/oci/config.json刚刚创建的一刻立即将其更改，所以是一个loop程序。它会在完成更改之后自动退出。

```text
$ pxtools/cli/px-node.sh reconf -file ./px-opts --watch
```

{% hint style="info" %}
新节点加入PX集群之后，其网卡配置存入Etcd, 不再受本地配置文件影响
{% endhint %}







