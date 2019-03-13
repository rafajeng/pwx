# VMware虚机环境

{% hint style="danger" %}
道云只支持物理机作为PX的生产环境，**不支持虚拟机作为PX的生产环境。**
{% endhint %}

{% hint style="danger" %}
本章节描述在VMware虚拟机环境中测试PX时，如何尽可能的让PX稳定运行。**如果虚拟机环境不满足此章的要求，无论PX出现的任何问题，道云都不予解答**。
{% endhint %}

## VMware虚机的配置要求

| \# | 要求 | 描述 |
| :--- | :--- | :--- |
| 1. | 虚机环境独享 | PX的存储节点必须跑在独立的虚机里。这个虚机不跑任何其它的业务。 |
| 2.  | CPU资源独享 | 虚机的CPU资源必须全部预留\(Reserve\) |
| 3.  | 内存资源独享 | 虚机的内存资源必须全部预留锁定\(Reserve and Lock\) |
| 4. | 根盘资源独享 | 虚机的根盘必须是厚置备置零\(Thick Provisioning Eager Zero\) |
| 5. | 数据盘资源独享 | 虚机的数据盘必须是RAID卡穿透\(Pass Through\)或者是裸设备映射\(Raw Device Mapping\) |
| 6.  | 优先响应 | 虚机的延迟敏感度\(Latency Sensitivity\)必须设置为高\(High\) |
| 7. | NUMA绑定 | 虚机的必须锁定在一个NUMA节点上 |



