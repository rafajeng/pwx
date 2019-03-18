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

BTFS的发展同Linux kernel紧密联合在一起, 最新的功能和补丁都在合并在最新的Linux kernel里。所以要升级BTFS必须升级Linux kernel。

BTFS

 

