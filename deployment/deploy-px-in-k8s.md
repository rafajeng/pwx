# 部署PX \(K8s\)

## 概述

PX在Kubernetes环境中是由**yaml**文件定义的**daemonset**，整个部署过程完全容器化和自动化。所以要求用户在部署前，对Kubernetes有基础的知识积累。

## 步骤

### 1. 编写px-opts文件

px-opts文件包含PX的运行参数，以下为示例描述了一个有双网卡和双数据盘的基本配置。

{% code-tabs %}
{% code-tabs-item title="px-opts.txt" %}
```text
-c PX-Cluster1-5802fe79-17e3-484d-8de1-2223513c519b
-k etcd:http://10.10.176.161:19019,etcd:http://10.10.176.162:19019,etcd:http://10.10.176.163:19019
-m ens192
-d ens224
-s /dev/sdb
-s /dev/sdc
-j /dev/sdb
-secret_type kvdb
-x kubernetes
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
PX的所有运行参数请参见附录
{% endhint %}

### 2. 生成部署yaml文件

### 3. 部署yaml文件

### 4. 检查PX状态

