# 全局调教

{% hint style="warning" %}
PX性能的一个主要瓶颈是其底层的BTFS。全局调教目的是提升BTFS的性能。 
{% endhint %}

## BTFS在PX架构里的功用和带来的限制

| \# | PX架构内BTFS的功用 | 限制 |
| :--- | :--- | :--- |
| 1 | 底层Datastore | 文件系统Overhead |
| 2 | 多硬盘软RAID | 不能移除盘 |
| 3 | 多硬盘并行读写 |  |
| 4 | 添加硬盘和数据再均衡\(Rebalance\) | 必须离线完成 |
| 5 | Copy On Write 保护 | 严重的写放大 |
| 6 | 校验码\(Checksum\)保护 | 写放大 |
| 7 | 快照功能 |  |

## BTFS对kernel版本的依赖

BTFS的发展同Linux kernel紧密联合在一起, 最新的改进和补丁都在合并在最新的Linux kernel里。所以要升级BTFS必须升级Linux kernel。

## BTFS在RHEL/CentOS里

RHEL/CentOS官方的kernel版本都非常保守。RHEL/CentOS 7.x 的kernel版本一直停留在3.10, 而且红帽官方已经宣布在7.4之后不再backport新的BTFS补丁。所以BTFS在RHEL/CentOS官方kernel里的稳定性和性能都不理想。

**如果使用的是CentOS, 建议升级kernel到elrepo.org的4.4版本以上。** 

## 全局调教参数rt\_opts

在PX的安装参数里添加rt\_opts, 例如

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





<table>
  <thead>
    <tr>
      <th style="text-align:left">rt_opts&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x9002;&#x7528;&#x7248;&#x672C;</th>
      <th style="text-align:left">&#x64CD;&#x4F5C;</th>
      <th style="text-align:left">&#x6548;&#x679C;</th>
      <th style="text-align:left">&#x4EE3;&#x4EF7;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">copy_on_write=0</td>
      <td style="text-align:left">
        <p>1.7.2</p>
        <p>&#x4EE5;&#x4E0A;</p>
      </td>
      <td style="text-align:left">&#x6DFB;&#x52A0;nocow&#x548C;nosum&#x5230;mount&#x5C5E;&#x6027;&#x91CC;</td>
      <td
      style="text-align:left">&#x7981;&#x7528;Copy On Write&#x548C;&#x6821;&#x9A8C;&#x7801;</td>
        <td
        style="text-align:left">&#x964D;&#x4F4E;&#x6570;&#x636E;&#x7EA0;&#x9519;&#x80FD;&#x79BB;&#x5F00;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">nossd=1</td>
      <td style="text-align:left">
        <p>1.7.3</p>
        <p>&#x4EE5;&#x4E0A;</p>
      </td>
      <td style="text-align:left">&#x6DFB;&#x52A0;nossd&#x5230;mount&#x5C5E;&#x6027;&#x91CC;</td>
      <td style="text-align:left">
        <p>&#x5173;&#x95ED;SSD&#x7A7A;&#x95F4;&#x4F18;&#x5316;</p>
        <p>&#x907F;&#x514D;&#x6587;&#x4EF6;&#x7CFB;&#x7EDF;&#x788E;&#x7247;&#x5316;&#xFF0C;</p>
      </td>
      <td style="text-align:left">&#x51CF;&#x5C11;SSD&#x5BFF;&#x547D;</td>
    </tr>
  </tbody>
</table> 

