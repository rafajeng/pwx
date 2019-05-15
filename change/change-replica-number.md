# 增减副本

## 概述

PX支持1到3个副本，可以在线增减，并且能指定副本位置。

## 步骤

### 查看副本数量

```text
# pxctl volume list
ID                      NAME                                            SIZE    HA      SHARED  ENCRYPTED       IO_PRIORITY     STATUS          SNAP-ENABLED
374947658945558346      pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe        11 GiB  2       no      no              LOW             up - detached   no

# pxctl volume inspect pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
Volume  :  374947658945558346
        Name                     :  pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
        Size                     :  11 GiB
        Format                   :  xfs
        HA                       :  2
        IO Priority              :  LOW
        Creation time            :  May 15 08:51:26 UTC 2019
        Shared                   :  no
        Status                   :  up
        State                    :  detached
        Labels                   :  namespace=portworx,pvc=pvc-hdd-ha2-rwo
        Reads                    :  0
        Reads MS                 :  0
        Bytes Read               :  0
        Writes                   :  0
        Writes MS                :  0
        Bytes Written            :  0
        IOs in progress          :  0
        Bytes used               :  164 KiB
        Replica sets on nodes:
                Set 0
                  Node           : 10.10.176.162 (Pool 0)
                  Node           : 10.10.176.164 (Pool 0)
        Replication Status       :  Up


```

### 添加副本

一次操作只能增加一个副本。

```text
# pxctl volume ha-update pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe --repl 3
Update Volume Replication: Replication update started successfully for volume pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
```

### 查看副本同步进度

"Replication Status : Resync" 说明副本正在同步。“"Replication Status : Up" 说明副本同步完成。

```text
# pxctl volume inspect pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
Volume  :  374947658945558346
        Name                     :  pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
        Size                     :  11 GiB
        Format                   :  xfs
        HA                       :  2
        IO Priority              :  LOW
        Creation time            :  May 15 08:51:26 UTC 2019
        Shared                   :  no
        Status                   :  up
        State                    :  detached
        Labels                   :  namespace=portworx,pvc=pvc-hdd-ha2-rwo
        Reads                    :  0
        Reads MS                 :  0
        Bytes Read               :  0
        Writes                   :  0
        Writes MS                :  0
        Bytes Written            :  0
        IOs in progress          :  0
        Bytes used               :  11 GiB
        Replica sets on nodes:
                Set 0
                  Node           : 10.10.176.164 (Pool 0)
                  Node           : 10.10.176.161 (Pool 0)
                  HA-Increase on : 10.10.176.163 (Pool 0)
        Replication Status       :  Resync
        
# pxctl volume inspect pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
Volume  :  374947658945558346
        Name                     :  pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
        Size                     :  11 GiB
        Format                   :  xfs
        HA                       :  3
        IO Priority              :  LOW
        Creation time            :  May 15 08:51:26 UTC 2019
        Shared                   :  no
        Status                   :  up
        State                    :  detached
        Labels                   :  pvc=pvc-hdd-ha2-rwo,namespace=portworx
        Reads                    :  0
        Reads MS                 :  0
        Bytes Read               :  0
        Writes                   :  0
        Writes MS                :  0
        Bytes Written            :  0
        IOs in progress          :  0
        Bytes used               :  11 GiB
        Replica sets on nodes:
                Set 0
                  Node           : 10.10.176.164 (Pool 0)
                  Node           : 10.10.176.161 (Pool 0)
                  Node           : 10.10.176.163 (Pool 0)
        Replication Status       :  Up
```

### 删减副本

一次操作只能删减一个副本。

```text
# pxctl volume ha-update pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe --repl 2
Update Volume Replication: Replication update started successfully for volume pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe
```

## 指定副本位置

添加和删减副本的时候都可以通过`--node`参数指定目标副本。

例： 双副本卷在10.10.176.161节点上的添加副本

```text
# pxctl volume ha-update pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe \
--repl 3 --node 10.10.176.161
```

例： 三副本卷在10.10.176.161节点上的删除副本

```text
# pxctl volume ha-update pvc-9fcbdf65-76ee-11e9-9cd2-005056a1fafe \
--repl 2 --node 10.10.176.161
```

