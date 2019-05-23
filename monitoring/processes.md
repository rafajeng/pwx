---
description: Processes
---

# 进程

## 概述

K8S里的"portowrx" pod只是负责自动化部署和健康检查。

PX的核心进程跑在独立于K8S和Docker的runC容器里，名字也叫"portworx"。其启停由systemd的portworx.service控制。

PX的runC容器采用了“一个supervisord监护进程带多个子进程"的技巧。

## 观察portworx的runC容器

```text
$ /opt/pwx/bin/runc list
ID          PID         STATUS      BUNDLE         CREATED                          OWNER
portworx    125995      running     /opt/pwx/oci   2019-05-21T08:03:56.733504444Z   root
```

## 观察PX的进程

{% code-tabs %}
{% code-tabs-item title="在PX容器内部观察" %}
```text
$ /opt/pwx/bin/runc exec portworx ps -elf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root          1      0  0  80   0 - 11844 poll_s May21 ?        00:01:38 /usr/bin/python /usr/bin/supervisord -c /etc/supervisor/conf.d/supervisord.conf
0 S root        171      1  0  80   0 - 117323 futex_ May21 ?       00:00:00 /usr/bin/lttng-relayd -o /var/lib/osd/log/px_trace
0 S root        173      1  0  80   0 -  4565 wait   May21 ?        00:00:02 /bin/bash -x /usr/local/bin/lttng.sh --tracefile-diskusage 0 --sub-buf-sz 16384 --sub-buf-num 2 --lttng-running /tmp/lttng_running
0 S root        174      1  0  80   0 - 552889 futex_ May21 ?       00:02:52 /usr/local/bin/pxexec
0 S root        176      1  0  80   0 - 638001 ep_pol May21 ?       00:00:21 /usr/local/bin/px-diag
0 S root        177      1  0  80   0 - 600604 ep_pol May21 ?       00:00:21 /usr/local/bin/px-healthmon
0 S root        182      1  0  80   0 -  5493 wait   May21 ?        00:00:00 /usr/bin/python2.7 /usr/local/bin/start_pxcontroller_pxstorage.py
4 S root        187      1  0  80   0 - 704909 futex_ May21 ?       00:00:23 /usr/local/bin/px-ns
0 S root        191      1  0  80   0 - 42449 pipe_w May21 ?        00:00:00 /usr/bin/python /usr/local/bin/supervisord_event_handler.py
4 S root        261    182  2  80   0 - 619237 futex_ May21 ?       01:31:43 /usr/local/bin/px-storage
4 S root        273    182  3  80   0 - 824248 futex_ May21 ?       01:54:23 /usr/local/bin/px -daemon
0 S root        274    182  0  80   0 -  4576 wait   May21 ?        00:00:04 /bin/bash /usr/local/bin/watchdog.sh
0 S root      86229    173  0  80   0 -  1092 hrtime 20:28 ?        00:00:00 sleep 120
0 S root      86248    274  0  80   0 -  1092 hrtime 20:28 ?        00:00:00 sleep 63
4 R root      86270      0  0  80   0 -  8603 -      20:29 ?        00:00:00 ps -elf
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="在操作系统内观察" %}
```text
$ ps -elf | grep px | grep -v xfs | grep -v oci-mon | grep -vw lh
0 S root     114834 114610  0  80   0 - 882780 futex_ May21 ?       00:28:29 /px-log-tail --follow -P @ -tf  -u portworx.service -u portworx-output.service -u init.scope -n 20000 -p 88270
0 S root     120119  74433  0  80   0 - 28177 pipe_w 20:41 pts/0    00:00:00 grep --color=auto px
4 S root     125929      1  0  80   0 - 28824 do_wai May21 ?        00:00:00 /bin/sh -c while [ 1 ]; do cat /var/run/systemd/pxFIFO | /usr/bin/systemd-cat systemd-cat --identifier portworx; sleep 1; done
0 S root     125931 125929  0  80   0 - 26992 pipe_w May21 ?        00:00:00 cat /var/run/systemd/pxFIFO
0 S root     126916 125995  0  80   0 - 117323 futex_ May21 ?       00:00:00 /usr/bin/lttng-relayd -o /var/lib/osd/log/px_trace
0 S root     126919 125995  0  80   0 - 552889 futex_ May21 ?       00:02:53 /usr/local/bin/pxexec
0 S root     126921 125995  0  80   0 - 638001 ep_pol May21 ?       00:00:21 /usr/local/bin/px-diag
0 S root     126922 125995  0  80   0 - 600604 ep_pol May21 ?       00:00:22 /usr/local/bin/px-healthmon
0 S root     126927 125995  0  80   0 -  5493 do_wai May21 ?        00:00:00 /usr/bin/python2.7 /usr/local/bin/start_pxcontroller_pxstorage.py
4 S root     126932 125995  0  80   0 - 704909 futex_ May21 ?       00:00:23 /usr/local/bin/px-ns
4 S root     127877 126927  2  80   0 - 619237 futex_ May21 ?       01:32:03 /usr/local/bin/px-storage
4 S root     128123 126927  3  80   0 - 824248 futex_ May21 ?       01:54:49 /usr/local/bin/px -daemon

```
{% endcode-tabs-item %}
{% endcode-tabs %}

| 进程名 | 位置 | 重要性 | 功能 |
| :--- | :--- | :--- | :--- |
| supervisord | 后端 | 核心 | 容器控制并带领其它px进程 |
| px-storage | 后端 | 核心 | 数据读写和复制 |
| px | 前端 | 核心 | 数据读写 |
| px-ns | 前端 | 核心 | 共享卷的export |
| px-diag | 后端 | 辅助 | 诊断日志收集 |
| px-healthmon | 后端 | 辅助 | 健康检查 |
| lttng | 后端 | 辅助 | debug trace日志收集 |

## 监控PX进程的资源占用

### CPU资源

```text
$ top -bd .10 -n 1 -c -p \
"$(pgrep -d',' -f 'px-storage'),$(pgrep -d',' -f 'px-ns'),$(pgrep -d',' -f 'px ')" \
| tail -4
   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
128123 root      20   0 3370724 158388  52396 S  26.7  0.0 127:38.41 /usr/local/bin/px -daemon
127877 root      20   0 2476948 987.6m 292600 S   0.0  0.2 102:13.71 /usr/local/bin/px-storage
126932 root      20   0 2819636  35152  14576 S   0.0  0.0   0:26.48 /usr/local/bin/px-ns
```

### 内存资源

{% code-tabs %}
{% code-tabs-item title="memusage.sh" %}
```bash
$ cat memusage.sh
##!/usr/bin/bash
##set -x

## Print header
 echo -e "Pname\t\tPID\tSize\tResid.\tShared\tData\t%"

## Get the PID of the process name given as argument 1
pidno=$( ps -C "$1" | tail -1 | awk '{print $1}' )

## If the process is running, print the memory usage
if [ -e /proc/$pidno/statm ]; then
  ## Get the memory info
  m=`awk '{OFS="\t";print $1,$2,$3,$6}' /proc/$pidno/statm`
  ## Get the memory percentage
  perc=`top -bd .10 -p $pidno -n 1  | grep $pidno | gawk '{print \$10}'`
  ## print the results
  echo -e "$1\t$pidno\t$m\t$perc";
  ## If the process is not running
else
  echo "$1 is not running";
fi
```
{% endcode-tabs-item %}
{% endcode-tabs %}

```text
$ bash memusage.sh px-storage
Pname           PID     Size    Resid.  Shared  Data    %
px-storage      127877  619237  252831  73150   515894  0.2

$ bash memusage.sh px
Pname           PID     Size    Resid.  Shared  Data    %
px      128123  842681  39421   13099   807460  0.0

$ bash memusage.sh px-ns
Pname           PID     Size    Resid.  Shared  Data    %
px-ns   126932  704909  8772    3644    685682  0.0
```

