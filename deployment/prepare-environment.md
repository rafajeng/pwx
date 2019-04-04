---
description: Prepare Environment
---

# 环境准备

{% hint style="danger" %}
道云只支持物理机作为PX的生产环境，**不支持任何虚拟机作为PX的生产环境。**
{% endhint %}

## 内核依赖包 <a id="dependency"></a>

{% code-tabs %}
{% code-tabs-item title="必须安装" %}
```text
# CentOS原装kernel
$ yum install kernel-headers-$(uname -r) kernel-devel-$(uname -r)

# Elrepo的 LTS kernel
$ yum install kernel-lt-headers-$(uname -r) kernel-lt-devel-$(uname -r)

# Elrepo的 MainLine kernel
$ yum install kernel-ml-headers-$(uname -r) kernel-ml-devel-$(uname -r)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## 工具包

{% code-tabs %}
{% code-tabs-item title="建议安装" %}
```text
$ yum install curl net-tools bind-utils sysstat chrony

$ yum install -y wget telnet traceroute lsof zip unzip \
ipvsadm open-vm-tools bash-completion gdisk parted \
pciutils smartmontools psmisc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## 硬件配置

| 设备 | 最低PoC要求 | 最低生产要求 | 推荐生产配置 |
| :--- | :--- | :--- | :--- |
| CPU | 2核 | 8核 | 16核 |
| 内存 | 16GB | 64GB  | 256GB |
| 网络 | 单千兆 | 双千兆绑定 | 双万绑定 |
| 数据盘 | 50GB硬盘剩余空间 | 一块HDD | 至少一块SSD, 两块HDD |

{% hint style="info" %}
实际操作中, CPU和内存的配置优先服从PaaS平台的规划。
{% endhint %}

## 节点数量 <a id="best-practice"></a>

生产系统至少要**三个节点**的PX。

{% hint style="warning" %}
只有安装了PX的节点才可以挂PX卷
{% endhint %}

## 最佳实践 <a id="best-practice"></a>

### 网卡配置 <a id="network-configuration"></a>

两张物理万兆网卡，各取一个网口，做主备绑定。负载均衡绑定也可以，但是客户的交换机对这种绑定模式有生产级别的支持。如果条件允许，两个网口应该分别接到两台堆叠的交换机上。

绑定的万兆网卡应该只有一个VLAN和一个网段给PX的存储网络专用。如果做不到硬件专用，至少要保证VLAN是专用的。 

### RAID卡配置 <a id="raid-configuration"></a>

如果需要最轻松的运维体验，建议数据盘做RAID1,5,6；如果需要空间最大化，建议数据盘做单盘RAID0。**如果RAID卡有电池保护的读写Cache，建议给物理硬盘打开，提升小IO的处理效率**。不建议给SSD使用RAID Cache。

{% hint style="warning" %}
当前不建议使用所谓的RAID卡**直通模式。**原因是各家硬件厂商对这种模式的技术实现各不相同，成熟度也不同。为了保证部署质量，道云建议给所有的数据盘都做RAID。
{% endhint %}

### 数据盘配置 <a id="device-configuration"></a>

PX的数据盘兼容操作系统可以识别所有块设备，例如SSD, NVME, SAS, SATA,SAN。

{% hint style="info" %}
PX也能在公有云和私有云虚机里运行，所以也支持虚拟数据盘。私有云场景中，道云目前只支持在VMware中做生产部署。公有云场景详情请参考章节“[公有云](../public-cloud.md)”
{% endhint %}

PX会根据数据盘的性能将它们放到高速，中速，低速三个存储池中（也就是“存储分层”的功能）。存储卷在创建时可以绑定其中一个存储池以获取相应的性能。 

{% hint style="info" %}
PX目前只能用SSD/NVME做日志缓冲盘，提升处理小IO的性能。全功能的写缓存还在开发中。
{% endhint %}

数据盘的大小和数量根据实际数据量做规划。建议使用双副本或者三副本卷以保证数据的高可用。假设RAID卡的配置是单盘**RAID0，保守估算可用空间的方法如下：**

| **副本数** | **可用空间** |
| :--- | :--- |
| 双副本 | 总数据盘空间/2.5 |
| 三副本 | 总数据盘空间/3.5 |
| 双副本单副本等量混用 | 总数据盘空间/3 |

### 网络延时

ping测得的网络延时\(latency\)要在**0.1毫秒\(ms\)以下**，才能保证生产级别性能。如果达不到，请咨询道云。 

## 操作系统 <a id="os"></a>

此手册只适用于RHEL和CentOS，版本**7.4**以上, **生产推荐7.5**

{% hint style="info" %}
PX官方支持各种主流Linux发行版。如需支持，请联系道云。
{% endhint %}

### 时间同步和时区

PX使用本地的时间和时区。 **所有的节点必须严格的时间同步和同一时区。**

### 持久化Journal日志

保证**journalctl**在主机重启后仍然能输出历史日志。参考 [https://gist.github.com/JPvRiel/b7c185833da32631fa6ce65b40836887](https://gist.github.com/JPvRiel/b7c185833da32631fa6ce65b40836887)

```text
mkdir -p /var/log/journal
systemd-tmpfiles --create --prefix /var/log/journal
systemctl restart systemd-journald
```

{% hint style="info" %}
如果journald重启失败，则需要重启主机
{% endhint %}

### 启用kernel dump

安装CentOS的时候, **kdump**是默认启用的。如果不小心禁用了，用以下方法启用：

```text
$ yum install kexec-tools
$ systemctl enable kdump
# 重启主机
```

## 防火墙

确保节点间防火墙打开以下端口，**TCP和UDP协议都要启用**

| 端口 | 功能 |
| :--- | :--- |
| 9000 | API备用 |
| 9001 | REST API |
| 9017 | Trace日志 |
| 9018 | 内置Etcd Peer |
| 9019 | 内置Etcd Client |
| 19018 | 外置Etcd Peer |
| 19019 | 外置Etcd Client |

## DNS

{% hint style="warning" %}
所有安装PX的主机名必须可以解析。建议添加解析表到/etc/hosts做静态解析
{% endhint %}

## 依赖包 <a id="dependency"></a>

{% code-tabs %}
{% code-tabs-item title="必须安装" %}
```text
$ yum install kernel-headers-$(uname -r)

$ yum install kernel-devel-$(uname -r)

$ yum install docker  

$ yum install curl net-tools bind-utils sysstat chrony
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="建议安装" %}
```text
$ yum install -y wget telnet traceroute lsof zip unzip \
ipvsadm open-vm-tools bash-completion gdisk parted \
pciutils smartmontools psmisc
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Pxtools工具集 <a id="pxtools"></a>

{% hint style="info" %}
道云提供pxtools以简化PX的部署和维护。道云的PX部署步骤都需要用到pxtools里的工具。
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="必须安装" %}
```text
$ docker run --rm -v $(pwd):/drop daocloud.io/portworx/pxtools
'/pxtools' -> '/drop/pxtools'
  
$ ls -lh
drwxr-xr-x  7 root root   78 Dec  2 18:26 pxtools
```
{% endcode-tabs-item %}
{% endcode-tabs %}

