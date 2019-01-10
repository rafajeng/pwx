# 环境准备

## 硬件

## 操作系统

## 依赖包

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

## pxtools工具集

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

