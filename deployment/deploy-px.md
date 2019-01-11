# 部署PX

## 概述 <a id="overview"></a>

### 1. Daemonset <a id="daemonset"></a>

PX在K8S环境中是用**daemonset**模式部署，整个部署过程完全容器化和自动化。所以要求用户在部署前对K8S有基础的知识积累。

PX的daemonset里是oci-monitor容器。它是一个辅助功能的容器，只负责部署，启停和监控, 不负责实际的存储功能。oci-monitor容器会自动拉取和解压px-enteprise镜像，部署由systemd控制的px-runc容器。整体控制流如下：

```text
kubelet => docker => oci-monitor => systemd => runc => px-enterprise
```

PX的daemonset部署在kube-system的租户下，有**Node Affinity** \(节点亲和性\), 只会扩展到有**px/enabled=true**的节点上。

Daemonset使用yaml文件定义，建议使用pxtools工具集里的**px-yaml.sh**脚本生成和修改这些yaml文件。

### 2. CSI <a id="csi"></a>

PX对K8S CSI的支持目前处在“技术预览\(technical preview\)"状态，所以此章节的部署模式**不支持CSI**。

{% hint style="info" %}
如果需要预览PX对K8S CSI的支持，请参阅章节”[K8S CSI](other-scenarios/k8s-csi.md)“
{% endhint %}

## 步骤 <a id="steps"></a>

### 1. 编写px-opts文件 <a id="step-1-px-opts"></a>

px-opts文件包含PX的运行参数，以下为示例描述了一个有"双网卡"和"双数据盘"的基本配置:

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

px-opts常用参数：

| 参数 | 解释 |
| :--- | :--- |
| -c &lt;id&gt; | PX集群名，建议添加UUID后缀以保证全局唯一 |
| -k &lt;etcd://host:port&gt; | Etcd地址，Etcd集群的节点地址用逗号隔开 |
| -s &lt;device path&gt; | 数据盘路径，多个盘路径叠加示例：-s /dev/sdb, -s /dev/sdc |
| -d &lt;ethX&gt; | 数据网卡设备名 |
| -m &lt;ethX&gt; | 管理网卡设备名 |
| -x kubernetes | 编排软件为Kubernetes |
| -j &lt;device path&gt; | 日志缓冲盘路径，如果是整块裸盘，PX自动做分区 |
| -secrect\_type kvdb | 秘钥存储为Etcd，用于保存连接第三方服务的登陆信息 |

{% hint style="info" %}
PX的所有运行参数请参见本章”附录“
{% endhint %}

{% hint style="warning" %}
Deamonset默认所有节点的**配置一致**。所以如果有硬件配置不一致的节点，请参阅章节“[添加节点](../kuo-rong/add-nodes.md)“
{% endhint %}

### 2. 创建yaml文件 <a id="step-2-yamls"></a>

```text
$ pxtools/cli/px-yaml.sh oci --file ./px-opts.txt --registry daocloud.io 
Created file as: ./px-yamls/oci/oci-monitor.yaml
```

px-yaml.sh oci 常用参数:

| 参数 | 默认值 | 解释 |
| :--- | :--- | :--- |
| -f, --file | ./px-opts.txt | px-opts文件路径 |
| -r, registry | daocloud.io/portworx | 镜像仓库地址 |
| -s,--secret | NULL | k8s镜像仓库登陆秘钥 |

### 3. 部署oci-monitor容器 <a id="step-3-oci-monitor"></a>

```text
$ kubectl apply -f px-yamls/oci/oci-monitor.yaml

$ kubectl -n kube-system get daemonset portworx
```

### 4. 给需要部署PX的节点打标签 <a id="step-4-node-label"></a>

{% hint style="warning" %}
建议成功部署一个节点之后再部署给下一个节点，便于排查问题。**不建议一开始就给所有的节点打上标签。**
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="第一个节点打上标签后：" %}
```text
$ kubectl label node k8s-node-1 px/enabled=true

$ kubectl -n kube-system get daemonset portworx

$ kubectl -n kube-system get pods -o wide
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="三个节点打上标签后" %}
```text
$ kubectl -n kube-system get daemonset portworx

$ kubectl -n kube-system get pods -o wide
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 5. 查看PX部署日志 <a id="step-5-logs"></a>

有三个方法查看日志：

{% code-tabs %}
{% code-tabs-item title="kubelet" %}
```text
$ kubectl -n kube-system logs -f porwtorx-xxxxx portworx
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="journald" %}
```text
$ journalctl -fu portworx*
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="docker" %}
```text
$ docker ps | awk '/px-oci-mon/ {print $1}' | xargs docker logs -f 
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### 6. 查看PX状态 <a id="step-6-pxctl-status"></a>

```text
$ /opt/pwx/bin/pxctl status
```

## 附录 <a id="appendix"></a>

### PX运行参数 <a id="all-px-opts"></a>

{% code-tabs %}
{% code-tabs-item title="px-runc install" %}
```text
$ /opt/pwx/bin/px-runc install --help
Usage: /opt/pwx/bin/px-runc <run|install> [options]

options:
   -c <id>                   [REQUIRED] Specifies the cluster ID that this PX instance is to join
   -k <kvdb://host:port>     [REQUIRED] Points to your key value database, such as an etcd cluster or a consul cluster
   -b                        Use in-built etcd. Provide the kvdb endpoints required for bootstrap with -k option.
   -s <device path>          [REQUIRED unless -a is used] Specifies the various drives that PX should use for storing the data
   -j <device path>          Specifies the drive that PX should use for storing the journal data
   -metadata <device path>   Specifies the drive that PX should use for storing the system meta data
   -oci <dir>                Specify OCI directory (dfl: /opt/pwx/oci)
   -sysd <file>              Specify SystemD service file (dfl: /etc/systemd/system/portworx.service)
   -e key=value              Specify extra environment variables
   -v <dir:dir[:shared,ro]>  Specify extra mounts
   -d <ethX>                 Specify the data network interface
   -m <ethX>                 Specify the management network interface
   -z                        Instructs PX to run in zero storage mode
   -f                        Instructs PX to use an unmounted drive even if it has a filesystem on it
   -a                        Instructs PX to use any available, unused and unmounted drives
   -A                        Instructs PX to use any available, unused and unmounted drives or partitions
   -x <swarm|kubernetes>     Specify scheduler being used in the environment
   -t <token>                Portworx lighthouse token for cluster
   -r <startport>            Start of the portrange Portworx will use for communication (dfl: 9001)

kvdb-options:
   -userpwd <user:passwd>    Username and password for ETCD authentication
   -ca <file>                Specify location of CA file for ETCD authentication
   -cert <file>              Specify location of certificate for ETCD authentication
   -key <file>               Specify location of certificate key for ETCD authentication
   -acltoken <token>         ACL token value used for Consul authentication

px-api-ssl-options:
   -apirootca <file>         Specify self-signed root CA certificate file
   -apicert <file>           Specify node certificate file
   -apikey <file>            Specify node certificate key file

secrets-options:
   -secret_type <type>       Specify the secrets type (<type> is aws, dcos, docker, k8s, kvdb or vault)
   -cluster_secret_key <id>  Specify cluster-wide secret ID

auto-scaling-group-options:
   -max_drive_set_count <#>         Specify maximum number of drive sets PX can create
   -max_storage_nodes_per_zone <#>  Specify the maximum number of storage nodes per zone in PX cluster

EXPERIMENTAL:
   -enable-shared-v4             Enables NFSv4 sharing (EXPERIMENTAL)
   -enable-shared-and-shared-v4  Enables both regular and NFSv4 volume sharing (EXPERIMENTAL)
   -rt_opts <val>                Specify Runtime config options (<val> is high, low, default, or key=#[,key2=#...]) (EXPERIMENTAL)

examples:
   /opt/pwx/bin/px-runc run -k etcd://my.company.com:4001 -c MY_CLUSTER_ID -s /dev/sdc -s /dev/sdb2
   /opt/pwx/bin/px-runc install -k etcd://70.0.1.65:2379 -c MY_CLUSTER_ID -s /dev/sdc -d enp0s8 -m enp0s8 -e MYVAR2=foo
   /opt/pwx/bin/px-runc install -k etcd://70.0.1.65:2379 -c MY_CID -f -a -x swarm -v /var/lib/share:/var/lib/share:shared
```
{% endcode-tabs-item %}
{% endcode-tabs %}





