# 告警

## 显示 <a id="display"></a>

有以下三种方法获取告警：

{% code-tabs %}
{% code-tabs-item title="Pxctl" %}
```text
pxctl service alerts show 
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="Etcdctl" %}
```text
/opt/pwx-etcd/bin/runc exec -e ETCDCTL_API=3 \
portworx-etcd etcdctl get --prefix "pwx/<Cluster ID>/alerts"

/opt/pwx-etcd/bin/runc exec -e ETCDCTL_API=2 \
portworx-etcd etcdctl ls "pwx/<Cluster ID>/alerts"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="REAT API" %}
```text
curl -X GET "http://<PX Node IP>:9001/v1/cluster/alerts/0" \
-H "accept: application/json"
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## 示例

{% code-tabs %}
{% code-tabs-item title="Etcd" %}
```python
{
    "alert_type": 36,
    "id": 37,
    "message": "Node c2045f14-4f21-4265-8c0a-89de427c611d (10.133.100.57) marked down by 3aff8b4f-39f6-4326-a3c6-3883a8a27931 (10.133.100.55): node did not respond to gossip heartbeats",
    "resource": 3,
    "resource_id": "c2045f14-4f21-4265-8c0a-89de427c611d",
    "severity": 2,
    "timestamp": {
        "nanos": 647290688,
        "seconds": 1548659451
    },
    "ttl": 86400
}

{
    "alert_type": 11,
    "id": 38,
    "message": "Node 10.133.100.57 has an Operational Status: Down",
    "resource": 3,
    "resource_id": "c2045f14-4f21-4265-8c0a-89de427c611d",
    "severity": 1,
    "timestamp": {
        "nanos": 659181061,
        "seconds": 1548659451
    },
    "ttl": 172800
}

{
    "alert_type": 53,
    "id": 39,
    "message": "PX is ready on Node: 10.133.100.57 (c2045f14-4f21-4265-8c0a-89de427c611d). CLI accessible at /opt/pwx/bin/pxctl.",
    "resource": 2,
    "resource_id": "PX-test-88dec00b-734c-4e78-a76d-24e7cf65cd47",
    "severity": 3,
    "timestamp": {
        "nanos": 843409961,
        "seconds": 1548659571
    },
    "ttl": 43200
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## 对象 <a id="resource-type"></a>

| 对象 | 子路径 | Resource |
| :--- | :--- | :--- |
| 集群 | Cluster | 3 |
| 节点 | Node | 2 |
| 数据盘 | Drive | 1 |
| 卷 | volume | 0 |

## 等级 <a id="severity"></a>

| 等级 | 代号 | Severity |
| :--- | :--- | :--- |
| 告知 | Notify | 3 |
| 注意 | Warn | 1 |
| 紧急 | Alarm | 2 |

## 编码 <a id="coding"></a>

| 编码 | 类型 | 等级 | 对象 | 描述 |
| :--- | :--- | :--- | :--- | :--- |
| 0 | DriveOperationFailure | ALARM | DRIVE | Triggered when a driver operations such as add or replace fails. |
| 1 | DriveOperationSuccess | NOTIFY | DRIVE | Triggered when a driver operations such as add or replace fails. |
| 2 | DriveStateChange | WARN | DRIVE | Triggered when there is a change in the driver state viz. Free Disk space goes below the recommended level of 10%. |
| 3 | VolumeOperationFailureAlarm | ALARM | VOLUME | Triggered when a volume operation fails. Volume operations could be resize, cloudsnap etc. The alert message will give more info about the specific error case. |
| 4 | VolumeOperationSuccess | NOTIFY | VOLUME | Triggered when a volume operation such as resize succeeds. |
| 5 | VolumeStateChange | WARN | VOLUME | Triggered when there is a change in the state of the volume. |
| 6 | VolGroupOperationFailure | ALARM | CLUSTER | Triggered when a volume group operation fails. |
| 7 | VolGroupOperationSuccess | NOTIFY | CLUSTER | Triggered when a volume group operation succeeds. |
| 8 | VolGroupStateChange | WARN | CLUSTER | Triggered when a volume group’s state changes. |
| 9 | NodeStartFailure | ALARM | CLUSTER | Triggered when a node in the PX cluster fails to start. |
| 10 | NodeStartSuccess | NOTIFY | CLUSTER | Triggered when a node in the PX cluster successfully initializes. |
| 11 | &gt;Internal PX Alert&lt; | - | - | Alert code used for internal PX book keeping. |
| 12 | NodeJournalHighUsage | ALARM | CLUSTER | Triggered when a node’s timestamp journal usage is not within limits. |
| 13 | IOOperation | ALARM | VOLUME | Triggered when an IO operation such as Block Read/Block Write fails. |
| 14-16 | &gt;Internal PX Alerts&lt; | - | - | Alert codes used for internal PX book keeping. |
| 17 | PXInitFailure | ALARM | NODE | Triggered when PX fails to initialize on a node. |
| 18 | PXInitSuccess | NOTIFY | NODE | Triggered when PX successfully initializes on a node. |
| 19 | PXStateChange | WARN | NODE | Triggered when the PX daemon shuts down in error. |
| 20 | VolumeOperationFailureWarn | WARN | VOLUME | Triggered when a volume operation fails. Volume operations could be resize, cloudsnap etc. The alert message will give more info about the specific error case. |
| 21 | StorageVolumeMountDegraded | ALARM | NODE | Triggered when PX storage enters degraded mode on a node. |
| 22 | ClusterManagerFailure | ALARM | NODE | Triggered when Cluster manager on a PX node fails to start. The alert message will give more info about the specific error case. |
| 23 | KernelDriverFailure | ALARM | NODE | Triggered when an incorrect PX kernel module is detected. Indicates that PX is started with an incorrect version of kernel module. |
| 24 | NodeDecommissionSuccess | NOTIFY | CLUSTER | Triggered when a node is successfully decommissioned from PX cluster. |
| 25 | NodeDecommissionFailure | ALARM | CLUSTER | Triggered when a node could not be decommissioned from PX cluster. |
| 26 | NodeDecommissionPending | WARN | CLUSTER | Triggered when a node decommission is kept in pending state as it has data which is not replicated on other nodes. |
| 27 | NodeInitFailure | ALARM | CLUSTER | Triggered when PX fails to initialize on a node. |
| 28 | &gt;Internal PX Alert&lt; | - | - | Alert code used for internal PX book keeping. |
| 29 | NodeScanCompletion | NOTIFY | NODE | Triggered when node media scan completes without error. |
| 30 | VolumeSpaceLow | ALARM | VOLUME | Triggered when the free space available in a volume goes below a threshold. |
| 31 | ReplAddVersionMismatch | WARN | VOLUME | Triggered when a volume HA update fails with version mismatch. |
| 32 | CloudsnapScheduleFailure | ALARM | NODE | Triggered if a cloudsnap schedule fails to configure. |
| 33 | CloudsnapOperationUpdate | NOTIFY | VOLUME | Triggered if a cloudsnap schedule is changed successfully. |
| 34 | CloudsnapOperationFailure | ALARM | VOLUME | Triggered when a cloudsnap operation fails. |
| 35 | CloudsnapOperationSuccess | NOTIFY | VOLUME | Triggered when a cloudsnap operation succeeds. |
| 36 | NodeMarkedDown | WARN | CLUSTER | Triggered when a PX node marks another node down as it is unable to connect to it. |
| 37 | VolumeCreateSuccess | NOTIFY | VOLUME | Triggered when a volume is successfully created. |
| 38 | VolumeCreateFailure | ALARM | VOLUME | Triggered when a volume creation fails. |
| 39 | VolumeDeleteSuccess | NOTIFY | VOLUME | Triggered when a volume is successfully deleted. |
| 40 | VolumeDeleteFailure | ALARM | VOLUME | Triggered when a volume deletion fails. |
| 41 | VolumeMountSuccess | NOTIFY | VOLUME | Triggered when a volume is successfully mounted at the requested path. |
| 42 | VolumeMountFailure | ALARM | VOLUME | Triggered when a volume cannot be mounted at the requested path. |
| 43 | VolumeUnmountSuccess | NOTIFY | VOLUME | Triggered when a volume is successfully unmounted. |
| 44 | VolumeUnmountFailure | ALARM | VOLUME | Triggered when a volume cannot be unmounted. The alert message provides more info about the specific error case. |
| 45 | VolumeHAUpdateSuccess | NOTIFY | VOLUME | Triggered when a volume’s replication factor \(HA factor\) is successfully updated. |
| 46 | VolumeHAUpdateFailure | ALARM | VOLUME | Triggered when an update to volume’s replication factor \(HA factor\) fails. |
| 47 | SnapshotCreateSuccess | NOTIFY | VOLUME | Triggered when a volume is successfully created.Snapshot create success |
| 48 | SnapshotCreateFailure | ALARM | VOLUME | Triggered when a volume snapshot creation fails. |
| 49 | SnapshotRestoreSuccess | NOTIFY | VOLUME | Triggered when a snapshot is successfully restored on a volume. |
| 50 | SnapshotRestoreFailure | ALARM | VOLUME | Triggered when the restore of snapshot fails. |
| 51 | SnapshotIntervalUpdateFailure | ALARM | VOLUME | Triggered when an update of the snapshot interval for a volume fails. |
| 52 | SnapshotIntervalUpdateSuccess | NOTIFY | VOLUME | Triggered when a snapshot interval of a volume is successfully updated. |
| 53 | PXReady | NOTIFY | NODE | Triggered when PX is ready on a node. |
| 54 | StorageFailure | ALARM | NODE | Triggered when the provided storage drives could not be mounted by PX. |

```text
$ pxctl alerts info
Type    ID                              Severity
VOLUME  VolumeOperationFailureAlarm     SEVERITY_TYPE_ALARM
VOLUME  IOOperation                     SEVERITY_TYPE_ALARM
VOLUME  VolumeSpaceLow                  SEVERITY_TYPE_ALARM
VOLUME  CloudsnapOperationFailure       SEVERITY_TYPE_ALARM
VOLUME  VolumeCreateFailure             SEVERITY_TYPE_ALARM
VOLUME  VolumeDeleteFailure             SEVERITY_TYPE_ALARM
VOLUME  VolumeMountFailure              SEVERITY_TYPE_ALARM
VOLUME  VolumeUnmountFailure            SEVERITY_TYPE_ALARM
VOLUME  VolumeHAUpdateFailure           SEVERITY_TYPE_ALARM
VOLUME  SnapshotCreateFailure           SEVERITY_TYPE_ALARM
VOLUME  SnapshotRestoreFailure          SEVERITY_TYPE_ALARM
VOLUME  SnapshotIntervalUpdateFailure   SEVERITY_TYPE_ALARM
VOLUME  SnapshotDeleteFailure           SEVERITY_TYPE_ALARM
VOLUME  CloudMigrationFailure           SEVERITY_TYPE_ALARM
VOLUME  VolumeStateChange               SEVERITY_TYPE_WARNING
VOLUME  VolumeOperationFailureWarn      SEVERITY_TYPE_WARNING
VOLUME  ReplAddVersionMismatch          SEVERITY_TYPE_WARNING
VOLUME  VolumeExtentDiffSlow            SEVERITY_TYPE_WARNING
VOLUME  VolumeExtentDiffOk              SEVERITY_TYPE_WARNING
VOLUME  VolumeOperationSuccess          SEVERITY_TYPE_NOTIFY
VOLUME  CloudsnapOperationUpdate        SEVERITY_TYPE_NOTIFY
VOLUME  CloudsnapOperationSuccess       SEVERITY_TYPE_NOTIFY
VOLUME  VolumeCreateSuccess             SEVERITY_TYPE_NOTIFY
VOLUME  VolumeDeleteSuccess             SEVERITY_TYPE_NOTIFY
VOLUME  VolumeMountSuccess              SEVERITY_TYPE_NOTIFY
VOLUME  VolumeUnmountSuccess            SEVERITY_TYPE_NOTIFY
VOLUME  VolumeHAUpdateSuccess           SEVERITY_TYPE_NOTIFY
VOLUME  SnapshotCreateSuccess           SEVERITY_TYPE_NOTIFY
VOLUME  SnapshotRestoreSuccess          SEVERITY_TYPE_NOTIFY
VOLUME  SnapshotIntervalUpdateSuccess   SEVERITY_TYPE_NOTIFY
VOLUME  SnapshotDeleteSuccess           SEVERITY_TYPE_NOTIFY
VOLUME  VolumeSpaceLowCleared           SEVERITY_TYPE_NOTIFY
VOLUME  CloudMigrationUpdate            SEVERITY_TYPE_NOTIFY
VOLUME  CloudMigrationSuccess           SEVERITY_TYPE_NOTIFY
VOLUME  PXMaxAlertNum                   SEVERITY_TYPE_NOTIFY

DRIVE   DriveOperationFailure           SEVERITY_TYPE_ALARM
DRIVE   DriveStateChange                SEVERITY_TYPE_WARNING
DRIVE   DriveStateChangeClear           SEVERITY_TYPE_WARNING
DRIVE   DriveOperationSuccess           SEVERITY_TYPE_NOTIFY

NODE    PXInitFailure                   SEVERITY_TYPE_ALARM
NODE    ClusterManagerFailure           SEVERITY_TYPE_ALARM
NODE    KernelDriverFailure             SEVERITY_TYPE_ALARM
NODE    CloudsnapScheduleFailure        SEVERITY_TYPE_ALARM
NODE    StorageFailure                  SEVERITY_TYPE_ALARM
NODE    ObjectstoreFailure              SEVERITY_TYPE_ALARM
NODE    PXStateChange                   SEVERITY_TYPE_WARNING
NODE    SharedV4SetupFailure            SEVERITY_TYPE_WARNING
NODE    PXInitSuccess                   SEVERITY_TYPE_NOTIFY
NODE    NodeScanCompletion              SEVERITY_TYPE_NOTIFY
NODE    PXReady                         SEVERITY_TYPE_NOTIFY
NODE    ObjectstoreSuccess              SEVERITY_TYPE_NOTIFY
NODE    ObjectstoreStateChange          SEVERITY_TYPE_NOTIFY

CLUSTER VolGroupOperationFailure        SEVERITY_TYPE_ALARM
CLUSTER NodeStartFailure                SEVERITY_TYPE_ALARM
CLUSTER NodeStateChange                 SEVERITY_TYPE_ALARM
CLUSTER NodeJournalHighUsage            SEVERITY_TYPE_ALARM
CLUSTER ContainerOperationFailure       SEVERITY_TYPE_ALARM
CLUSTER NodeDecommissionFailure         SEVERITY_TYPE_ALARM
CLUSTER NodeInitFailure                 SEVERITY_TYPE_ALARM
CLUSTER ClusterPairFailure              SEVERITY_TYPE_ALARM
CLUSTER VolGroupStateChange             SEVERITY_TYPE_WARNING
CLUSTER ContainerStateChange            SEVERITY_TYPE_WARNING
CLUSTER NodeDecommissionPending         SEVERITY_TYPE_WARNING
CLUSTER NodeMarkedDown                  SEVERITY_TYPE_WARNING
CLUSTER LicenseExpiring                 SEVERITY_TYPE_WARNING
CLUSTER VolGroupOperationSuccess        SEVERITY_TYPE_NOTIFY
CLUSTER NodeStartSuccess                SEVERITY_TYPE_NOTIFY
CLUSTER ContainerOperationSuccess       SEVERITY_TYPE_NOTIFY
CLUSTER NodeDecommissionSuccess         SEVERITY_TYPE_NOTIFY
CLUSTER PXAlertMax                      SEVERITY_TYPE_NOTIFY
CLUSTER ClusterPairSuccess              SEVERITY_TYPE_NOTIFY
```



