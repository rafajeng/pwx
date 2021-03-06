# Table of contents

* [PX简明手册](README.md)
* [概览](overview.md)
* [部署](deployment/README.md)
  * [环境准备](deployment/prepare-environment.md)
  * [部署Etcd](deployment/deploy-etcd.md)
  * [部署PX](deployment/deploy-px.md)
  * [创建SC](deployment/create-storage-class.md)
  * [其它场景](deployment/other-scenarios/README.md)
    * [VMware虚机环境](deployment/other-scenarios/vmware-vm.md)
    * [K8S CSI](deployment/other-scenarios/k8s-csi.md)
    * [独立存储节点](deployment/other-scenarios/standalone-storage-node.md)
    * [Swarm和Mesos](deployment/other-scenarios/swarm-and-mesos.md)
    * [OpenSSL](deployment/other-scenarios/openssl.md)
* [变更](change/README.md)
  * [添加节点](change/add-node.md)
  * [删除节点](change/remove-node.md)
  * [添加数据盘](change/add-deivce.md)
  * [更换数据盘](change/replace-device.md)
  * [删除数据盘](change/remove-device.md)
  * [增减副本](change/change-replica-number.md)
* [升级](upgrade.md)
* [使用](operations/README.md)
  * [创建PV](operations/create-pv.md)
  * [挂载PV](operations/mount-pv.md)
  * [扩大PV](operations/resize-pv.md)
  * [删除PV](operations/delete-pv.md)
* [监控](monitoring/README.md)
  * [PXC](monitoring/pxc.md)
  * [告警](monitoring/alerts.md)
  * [进程](monitoring/processes.md)
  * [日志](monitoring/logging.md)
  * [DCE-I集成](monitoring/dcei-ji-cheng.md)
* [备份](backup/README.md)
  * [CloudSnap](backup/cloudsnap.md)
  * [CloudMigrate](backup/cloudmigrate.md)
  * [Parsyncfp](backup/parsyncfp.md)
* [性能](performance/README.md)
  * [性能数据](performance/performance-data.md)
  * [全局调教](performance/global-tuning.md)
  * [卷调教](performance/volume-tuning.md)
  * [RAID卡配置](performance/raid-ka-pei-zhi.md)
* [公有云](public-cloud.md)
* [CLI](cli.md)
* [工具](tools/README.md)
  * [px-node.sh](tools/px-node.sh.md)
  * [px-etcd.sh](tools/px-etcd.sh.md)
  * [px-vol.sh](tools/px-vol.sh.md)
  * [px-yaml.sh](tools/px-yaml.sh.md)
  * [storcli](tools/storcli.md)
  * [arcconf](tools/arcconf.md)
  * [perccli](tools/perccli.md)
  * [ssacli](tools/ssacli.md)
* [版本](releases.md)
* [方案](solutions.md)
* [API](api.md)
* [FAQ](faq.md)

