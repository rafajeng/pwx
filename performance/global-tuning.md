---
description: Global Tuning
---

# 全局调教

{% hint style="warning" %}
PX性能的一个主要瓶颈是其底层的btrfs。全局调教目的是提升btrfs的性能。 

添加全局调教参数必须要宕机时间\(Downtime\)
{% endhint %}

## btrfs在PX架构里的功用和带来的限制

| \# | btrfs的功用 | 限制 |
| :--- | :--- | :--- |
| 1 | 底层Datastore | 文件系统Overhead |
| 2 | 多硬盘软RAID | 不能移除盘 |
| 3 | 多硬盘并行读写 |  |
| 4 | 添加硬盘和数据再均衡\(Rebalance\) | 必须离线完成 |
| 5 | Copy On Write 保护 | 严重的写放大 |
| 6 | 校验码\(Checksum\)保护 | 写放大 |
| 7 | 快照功能 |  |

## btrfs对kernel版本的依赖

btrfs的发展同Linux kernel紧密联合在一起, 最新的改进和补丁都在合并在最新的Linux kernel里。所以要升级BTFS必须升级Linux kernel。

## btrfs在RHEL/CentOS里

RHEL/CentOS官方的kernel版本都非常保守。RHEL/CentOS 7.x 的kernel版本一直停留在3.10, 而且红帽官方已经宣布在7.4之后不再backport新的btrfs补丁。所以btrfs在RHEL/CentOS官方kernel里的稳定性和性能都不理想。

**如果使用的是CentOS, 建议升级kernel到elrepo.org的4.4版本以上。** 

## 添加全局调教参数：rt\_opts

PX目前提供两个全局调教参数，关闭部分btrfs功能，以提升其性能。方法是在PX的启动参数里添加rt\_opts, 如下：

{% code-tabs %}
{% code-tabs-item title="px-opts.txt" %}
```text
-c PX-Cluster1-5802fe79-17e3-484d-8de1-2223513c519b
-k etcd:http://10.10.176.161:19019,etcd:http://10.10.176.162:19019,etcd:http://10.10.176.163:19019
-m ens192
-d ens224
-s /dev/sdb
-s /dev/sdc
-j auto
-secret_type kvdb
-x kubernetes
-rt_opts copy_on_write=0,nossd=1
```
{% endcode-tabs-item %}
{% endcode-tabs %}

PX启动后/etc/pwx/config.json会有如下显示：

{% code-tabs %}
{% code-tabs-item title="/etc/pwx/config.json" %}
```text
{
...
    "rt_opts": {
      "copy_on_write": 0,
      "nossd": 1
    },
...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

PX启动后查看btrfs的mount属性

```text
$ /opt/pwx/bin/runc exec portworx mount -l | grep btrfs

/dev/sd on /var/.px/0 type btrfs 
(rw,noatime,nodatasum,nodatacow,nossd,flushoncommit,space_cache,subvolid=5,subvol=/) 
[u=4831a814-5851-4fff-9de5-e610f3e9ffba,i=9196b959-21a4-472f-969a-82428108953c,n=2,pxpool=0]
```

## copy\_on\_write=0

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x89C4;&#x683C;</th>
      <th style="text-align:left">&#x7EC6;&#x8282;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x7248;&#x672C;&#x8981;&#x6C42;</td>
      <td style="text-align:left">1.7.2&#x4EE5;&#x4E0A;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x64CD;&#x4F5C;</td>
      <td style="text-align:left">&#x6DFB;&#x52A0;nocow&#x548C;nosum&#x5230;BTFS&#x7684;mount&#x5C5E;&#x6027;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x6548;&#x679C;</td>
      <td style="text-align:left">&#x7981;&#x7528;Copy On Write&#x548C;&#x6821;&#x9A8C;&#x7801;&#xFF0C;&#x51CF;&#x5C11;overhead&#x548C;&#x5EF6;&#x8FDF;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x4EE3;&#x4EF7;</td>
      <td style="text-align:left">btrfs&#x5931;&#x53BB;&#x540E;&#x7AEF;&#x6570;&#x636E;&#x81EA;&#x7EA0;&#x9519;&#x7684;&#x80FD;&#x529B;</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x8003;&#x91CF;</td>
      <td style="text-align:left">
        <p>&#x5E38;&#x89C1;&#x7684;ext4/xfs&#x6587;&#x4EF6;&#x7CFB;&#x7EDF;&#x4E5F;&#x6CA1;&#x6709;&#x6570;&#x636E;&#x81EA;&#x7EA0;&#x9519;&#x7684;&#x80FD;&#x529B;&#x3002;</p>
        <p>&#x5982;&#x679C;&#x5BA2;&#x6237;&#x63A5;&#x53D7;ext4/xfs&#x7684;&#x5B89;&#x5168;&#x6027;&#xFF0C;&#x90A3;&#x4E48;&#x4E5F;&#x5C31;&#x80FD;&#x63A5;&#x53D7;&#x8FD9;&#x4E2A;&#x53C2;&#x6570;</p>
      </td>
    </tr>
  </tbody>
</table>## nossd=1

| 规格 | 细节 |
| :--- | :--- |
| 版本要求 | 1.7.3以上 |
| 操作 | 添加nossd到btrfs的mount属性 |
| 效果 | 禁用SSD优先写连续空间，避免文件系统碎片化 |
| 代价 | 理论上会减少SSD的寿命 |
| 考量 | 建议性能优先 |

 

