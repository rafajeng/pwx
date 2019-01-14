# 告警

## PX的告警日志

### 显示告警

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
/opt/pwx/bin/runc exec -e ETCDCTL_API=3 \
portworx-etcd etcdctl get --prefix "pwx/<Cluster ID>/alerts"
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

{% hint style="info" %}
远程抓取告警，请使用REST API, 不建议使用pxctl和etcdctl。
{% endhint %}

### 告警分类

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

