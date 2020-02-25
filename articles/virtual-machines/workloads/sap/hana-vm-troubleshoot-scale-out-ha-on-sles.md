---
title: 在 Azure Vm 故障排除中 SAP HANA 向外扩展 HSR-PacemakerMicrosoft Docs
description: 本指南介绍如何检查和排查基于 Azure 虚拟机上运行的 SAP HANA 系统复制 (HSR) 和 Pacemaker on SLES 12 SP3 的复杂 SAP HANA 横向扩展高可用性配置
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: fb90bfff72f41d8d7ccc34d3ad6dd0e9206bb88e
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566227"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>验证 SLES 12 SP3 上的 SAP HANA 横向扩展高可用性设置和排查其问题 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


本文旨在帮助用户检查 Azure 虚拟机 (VM) 上运行的 SAP HANA 横向扩展 Pacemaker 群集配置。 群集设置是结合 SAP HANA 系统复制 (HSR) 和 SUSE RPM 包 SAPHanaSR-ScaleOut 完成的。 仅在 SUSE SLES 12 SP3 上执行了所有测试。 本文各个部分介绍不同的方面，并提供了示例命令和配置文件的摘录。 建议使用这些示例来验证和检查整个群集设置。



## <a name="important-notes"></a>重要事项

仅在 SAP HANA 2.0 上执行了结合 SAP HANA 系统复制和 Pacemaker 的所有 SAP HANA 横向扩展测试。 操作系统版本为 SUSE Linux Enterprise Server 12 SP3 for SAP Applications。 已使用 SUSE 的最新 RPM 包 SAPHanaSR-ScaleOut 来设置 Pacemaker 群集。
SUSE 已发布[此性能优化的安装程序的详细说明][sles-hana-scale-out-ha-paper]。

对于 SAP HANA 向外扩展所支持的虚拟机类型，请查看[SAP HANA 认证的 IaaS 目录][sap-hana-iaas-list]。

结合多个子网和 vNIC 的SAP HANA 横向扩展以及 HSR 的设置存在一个技术问题。 必须使用其中已修复此问题的最新 SAP HANA 2.0 修补程序。 支持以下 SAP HANA 版本： 

* rev2.00.024.04 或更高版本 
* rev2.00.032 或更高版本

如果需要 SUSE 支持，请遵循本[指南][suse-pacemaker-support-log-files]。 请根据本文所述，收集有关 SAP HANA 高可用性 (HA) 群集的所有信息。 SUSE 支持需要使用此信息执行进一步的分析。

在内部测试期间，通过 Azure 门户正常关闭 VM 时发生了群集设置混淆的问题。 因此，建议通过其他方法测试群集故障转移。 使用强制内核崩溃等方法，或者关闭网络或迁移 msl 资源。 请参阅以下部分的详细信息。 假设有意执行了标准关机过程。 进行维护是有意关闭的最佳示例。 有关详细信息，请参阅[计划内维护](#planned-maintenance)。

此外，在内部测试期间，当群集处于维护模式时，手动 SAP HANA 接管后发生了群集设置混淆的问题。 建议在结束群集维护模式前，手动将其切换回之前的模式。 另一个选项是在将群集置于维护模式之前触发故障转移。 有关详细信息，请参阅[计划内维护](#planned-maintenance)。 SUSE 的文档介绍了有关如何使用 crm 命令来按此方法重置群集的信息。 但在内部测试期间，上述方法非常可靠，永远不会出现任何意外的负面影响。

使用 crm migrate 命令时，请确保清理群集配置。 这会添加你可能不易察觉的位置约束。 这些约束会影响群集行为。 有关更多详细信息，请参阅[计划内维护](#planned-maintenance)。



## <a name="test-system-description"></a>测试系统说明

 使用了一个安装程序来处理 SAP HANA 横向扩展 HA 验证和认证。 它包括两个系统，每个系统分别具有三个 SAP HANA 节点：一个主机、两个辅助角色。 下表列出了 VM 名称和内部 IP 地址。 以下所有验证示例均在这些 VM 上完成。 通过在命令示例中使用这些 VM 名称和 IP 地址，你可以更好地了解这些命令及其输出：


| 节点类型 | VM 名称 | IP 地址 |
| --- | --- | --- |
| 站点 1 上的主节点 | hso-hana-vm-s1-0 | 10.0.0.30 |
| 站点 1 上的工作节点 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| 站点 1 上的工作节点 2 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| 站点 2 上的主节点 | hso-hana-vm-s2-0 | 10.0.0.40 |
| 站点 2 上的工作节点 1 | hso-hana-vm-s2-1 | 10.0.0.41 |
| 站点 2 上的工作节点 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| 多数仲裁节点 | hso-hana-dm | 10.0.0.13 |
| SBD 设备服务器 | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS 服务器 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS 服务器 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>多个子网和 vNIC

遵循 SAP HANA 网络建议在一个 Azure 虚拟网络中创建了三个子网。 Azure 上的 SAP HANA 横向扩展必须在非共享模式下安装。 这意味着每个节点对 /hana/data 和 /hana/log 使用本地磁盘卷。 由于节点仅使用本地磁盘卷，因此不需要为存储定义单独的子网：

- 10.0.2.0/24 用于 SAP HANA 节点间通信
- 10.0.1.0/24 用于 SAP HANA 系统复制 (HSR)
- 10.0.0.0/24 用于其他任何操作

有关与使用多个网络相关的 SAP HANA 配置的信息，请参阅 [SAP HANA global.ini](#sap-hana-globalini)。

群集中的每个 VM 有三个 vNIC，这与子网数相对应。 [如何在 azure 中创建具有多个网络接口卡的 Linux 虚拟机][azure-linux-multiple-nics]介绍了在部署 linux VM 时 azure 上可能出现的路由问题。 此特定路由文章仅适用于使用多个 vNIC 的情况。 SUSE 在 SLES 12 SP3 中按默认方式解决了此问题。 有关详细信息，请参阅[netconfig IN EC2 And Azure 中的多 NIC][suse-cloud-netconfig]。


若要验证 SAP HANA 是否已正确配置为使用多个网络，请运行以下命令。 首先，在 OS 级别检查所有三个子网的所有三个内部 IP 地址是否处于活动状态。 如果定义了使用不同 IP 地址范围的子网，则必须调整命令：

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

下面是站点 2 上第二个工作节点的示例输出。 可以看到 eth0、eth1 和 eth2 的三个不同内部 IP 地址：

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


接下来，验证用于名称服务器和 HSR 的 SAP HANA 端口。 SAP HANA 应该侦听相应的子网。 根据 SAP HANA 实例编号，必须调整命令。 测试系统的实例编号为 **00**。 可通过不同的方式来找出所使用的端口。 

以下 SQL 语句返回实例 ID、实例编号和其他信息：

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

例如，若要查找正确的端口号，可以查看 HANA Studio 中的“配置”，或使用 SQL 语句：

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

若要查找包括 SAP HANA 的 SAP 软件堆栈中使用的每个端口，请搜索[所有 SAP 产品的 tcp/ip 端口][sap-list-port-numbers]。

如果 SAP HANA 2.0 测试系统中的实例编号是 00，则名称服务器的端口号是 30001。 用于 HSR 元数据通信的端口号是 40002。 一种做法是登录到工作节点，然后检查主节点服务。 本文通过尝试连接到站点 2 的主节点来检查站点 2 的工作节点 2。

检查名称服务器端口：

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

若要证实节点间通信使用的是子网 10.0.2.0/24，结果应如以下示例输出所示。
只有通过子网 10.0.2.0/24 才能成功建立连接：

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

现在检查 HSR 端口 40002：

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

若要证实 HSR 通信使用的是子网 10.0.1.0/24，结果应如以下示例输出所示。
只有通过子网 10.0.1.0/24 才能成功建立连接：

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


corosync 配置文件必须在群集中的每个节点（包括多数仲裁节点）上正确。 如果某个节点不能按预期加入群集，请在所有节点上手动创建和/或复制 /etc/corosync/corosync.conf，然后重启服务。 

我们以测试系统中 corosync.conf 的内容作为示例。

第一个节是[群集安装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation)步骤 11 中所述的 totem。 可以忽略 **mcastaddr** 的值。 只需保留现有条目即可。 必须根据[Microsoft Azure SAP HANA 文档][sles-pacemaker-ha-guide]设置**令牌**和**共识**条目。

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

第二个节 logging 与给定的默认值相同：

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

第三个节显示 **nodelist**。 群集的所有节点必须连同其 nodeid 一起显示：

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

在最后一个节 quorum 中，必须正确设置 expected_votes 的值。 它必须是包括多数仲裁节点在内的节点数目。 **two_node** 的值必须是 **0**。 不要彻底删除该条目。 只需将值设置为 **0** 即可。

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


通过 **systemctl** 重启服务：

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD 设备

有关如何在 Azure VM 上设置 SBD 设备的信息，请参阅 [SBD 隔离](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)。

首先，在 SBD 服务器 VM 上检查群集中是否存在用于每个节点的 ACL 条目。 在 SBD 服务器 VM 上运行以下命令：


<pre><code>
targetcli ls
</code></pre>


在测试系统上，该命令的输出如以下示例所示。 必须输入类似于 iqn.2006-04.hso-db-0.local:hso-db-0 的 ACL 名称作为 VM 上的相应发起程序名称。 每个 VM 需要不同的名称。

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

然后检查所有 VM 上的发起程序名称是否不同以及是否对应于上面所示的条目。 下面是站点 1 上工作节点 1 中的示例：

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

输出类似于以下示例：

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

接下来，验证“发现”是否正常工作。 使用 SBD 服务器 VM 的 IP 地址在每个群集节点上运行以下命令：

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

输出应如以下示例所示：

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

下一个证明点是验证该节点是否可以看到 SDB 设备。 在每个节点（包括多数仲裁节点）上执行此项检查：

<pre><code>
lsscsi | grep dbhso
</code></pre>

输出应如以下示例所示。 但名称可能不同。 设备名称还可能在 VM 重启后更改：

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

根据系统的状态，有时重启 iSCSI 服务可以帮助解决问题。 然后运行以下命令：

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


在任一节点上，可以检查所有节点是否**干净**。 请确保在特定的节点上使用正确的设备名称：

<pre><code>
sbd -d /dev/sdm list
</code></pre>

输出应该针对群集中每个节点显示 **clear**：

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


另一项 SBD 检查是 sbd 命令的 dump 选项。 在来自于多数仲裁节点的此示例命令和输出中，设备名称为 sdd 而不是 sdm：

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

所有节点上的输出应该相同（设备名称除外）：

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

SBD 的另外一项检查是验证能否将消息发送到另一个节点。 若要将消息发送到站点 2 上的工作节点 2，请在站点 2 的工作节点 1 上运行以下命令：

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

在目标 VM 端（本示例中为 hso-hana-vm-s2-2）上，可在 /var/log/messages 中找到以下条目：

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

检查 /etc/sysconfig/sbd 中的条目是否对应于[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing) 中的描述。 验证 **/etc/iscsi/iscsid.conf** 中的启动设置是否设置为自动。

以下条目对于 /etc/sysconfig/sbd 非常重要。 在必要时调整 id 值：

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


检查 /etc/iscsi/iscsid.conf 中的启动设置。 下面的 iscsiadm 命令应已指定了所需的设置，如文档中所述。 验证 vi，如果不同，则对其进行手动调整。

此命令设置启动行为：

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

使此条目位于 /etc/iscsi/iscsid.conf 中：

<pre><code>
node.startup = automatic
</code></pre>

在测试和验证期间，重启 VM 后，SBD 设备在有些情况下不再可见。 启动设置与 YaST2 显示的信息之间有差异。 若要检查设置，请执行以下步骤：

1. 启动 YaST2。
2. 在左侧选择“网络服务”。
3. 在右侧向下滚动到“iSCSI 发起程序”并将其选中。
4. 在下一个屏幕中的“服务”选项卡上，会看到节点的唯一发起程序名称。
5. 在发起程序名称的上面，确保“服务启动”值设置为“启动时”。
6. 如果尚未进行此设置，请将它设置为“启动时”而不是“手动”。
7. 接下来，将顶部选项卡切换到“连接的目标”。
8. 在“连接的目标”屏幕中，应会看到 SBD 设备的条目，如以下示例所示：10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso。
9. 检查“启动”值是否设置为“onboot”。
10. 如果不是，请选择“编辑”并更改。
11. 保存更改并退出 YaST2。



## <a name="pacemaker"></a>Pacemaker

正确设置所有项之后，可在每个节点上运行以下命令来检查 Pacemaker 服务的状态：

<pre><code>
systemctl status pacemaker
</code></pre>

输出的顶部应如以下示例所示。 **Active** 后面的状态必须显示为 **loaded** 和 **active (running)** 。 “Loaded”后面的状态必须显示为 enabled。

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

如果设置仍为 disabled，请运行以下命令：

<pre><code>
systemctl enable pacemaker
</code></pre>

若要查看 Pacemaker 中配置的所有资源，请运行以下命令：

<pre><code>
crm status
</code></pre>

输出应如以下示例所示。 多数仲裁 VM (hso-hana-dm) 上的 cln 和 msl 资源显示为已停止，这是正常的。 多数仲裁节点上未安装 SAP HANA。 因此 cln 和 msl 资源显示为已停止。 显示的 VM 总数 (7) 必须正确。 群集中所有 VM 的列出状态必须是 Online。 必须正确识别当前的主要主节点。 本示例中为 hso-hana-vm-s1-0：

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

维护模式是 Pacemaker 的一项重要功能。 在此模式下，无需发起即时群集操作即可进行修改。 例如，VM 重启。 计划内的 OS 或 Azure 基础结构维护就是一个典型用例。 请参阅[计划内维护](#planned-maintenance)。 使用以下命令将 Pacemaker 置于维护模式：

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

使用 crm status 进行检查时，可发现输出中的所有资源已标记为 unmanaged。 在此状态下，群集不会对任何更改（例如启动或停止 SAP HANA）做出反应。
以下示例演示群集处于维护模式时 crm status 命令的输出：

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


此命令示例演示如何结束群集维护模式：

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


另一个 crm 命令可将获取的完整群集配置导入编辑器，以便你可以对其进行编辑。 保存更改后，群集将启动相应的操作：

<pre><code>
crm configure edit
</code></pre>

若要查看完整的群集配置，请使用 crm show 选项：

<pre><code>
crm configure show
</code></pre>



群集资源发生故障后，crm status 命令将显示失败操作的列表。 请查看此输出的如下示例：


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

故障后需要执行群集清理。 再次使用 crm 命令，并使用命令选项 cleanup 来清理这些失败操作条目。 为相应的群集资源命名，如下所示：

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

该命令返回的输出应如以下示例所示：

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>故障转移或接管

如[重要说明](#important-notes)中所述，不应使用标准的正常关机来测试群集故障转移或 SAP HANA HSR 接管。 我们建议触发内核崩溃或强制资源迁移等操作，或者在 VM 的 OS 级别关闭所有网络。 另一种方法是运行 crm \<node\> standby 命令。 请参阅[SUSE 文档][sles-12-ha-paper]。 

下面的三个示例命令可以强制群集故障转移：

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

如[计划内维护](#planned-maintenance)中所述，监视群集活动的好办法是结合 watch 命令运行 SAPHanaSR-showAttr：

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

查看来自 SAP Python 脚本的 SAP HANA 布局状态也有帮助。 群集设置将查找此状态值。 思考工作节点故障的原因时，事情会变得明朗。 如果工作节点发生故障，SAP HANA 不会立即对整个横向扩展系统的运行状况返回错误。 

它会重试几次，以避免不必要的故障转移。 仅当状态从“正常”（返回值 4）更改为“错误”（返回值 1）时，群集才会做出反应。 因此，如果 SAPHanaSR showAttr 的输出显示了状态为 offline 的 VM，这便是正常状况。 但是，没有任何活动能够切换主要站点和辅助站点。 只要 SAP HANA 不返回错误，就不会触发群集活动。

可按如下所示调用 SAP Python 脚本，以用户 \<HANA SID\>adm 的身份监视 SAP HANA 布局运行状况。 可能必须调整路径：

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

此命令的输出应如以下示例所示。 “主机状态”列和“总体主机”状态非常重要。 实际输出比较长，因此横跨了其他列。
为使本文档中的输出表更易于阅读，右侧的大多数列已去除：

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


还有另一个命令可以检查当前群集活动。 终止主站点的主节点后，请查看以下命令和输出尾部。 可以看到转换操作的列表，例如，将以前的辅助主节点 (hso-hana-vm-s2-0) 提升为新的主要主节点。 如果一切正常并且所有活动已完成，则此“转换摘要”列表必须为空。

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>计划内维护 

计划内维护期间存在不同的用例。 问题是要确定这种维护是否只是涉及到 OS 级别的更改、磁盘配置或 HANA 升级之类的基础结构维护。
你可以从 SUSE 的文档中找到其他信息，如[零停机时间][sles-zero-downtime-paper]或[SAP HANA SR 性能优化方案][sles-12-for-sap]。 这些文档还包含有关演示如何手动迁移主要站点的示例。

我们已执行密集的内部测试来验证基础结构维护用例。 为了避免出现与迁移主要站点相关的任何问题，我们决定始终先迁移主要站点，然后再将群集置于维护模式。 这样，就不需要让群集忘记以前的情况：哪一端是主要站点，哪一端是辅助站点。

这种做法存在两种不同的情况：

- **在当前辅助站点上执行计划内维护**。 在这种情况下，只需将群集置于维护模式，并在辅助站点上执行工作，而不会影响群集。

- **在当前主要站点上执行计划内维护**。 要使用户可在维护期间继续工作，需要强制故障转移。 采用这种做法时，必须通过 Pacemaker 触发群集故障转移，而不能仅仅在 SAP HANA HSR 级别触发。 Pacemaker 设置会自动触发 SAP HANA 接管。 还需要在将群集置于维护模式之前完成故障转移。

当前辅助站点上的维护过程如下所示：

1. 将群集置于维护模式。
2. 在辅助站点上完成工作。 
3. 结束群集维护模式。

当前主要站点上的维护过程更复杂：

1. 通过 Pacemaker 资源迁移手动触发故障转移或 SAP HANA 接管。 请参阅下面的详细信息。
2. 群集设置关闭以前的主站点上的 SAP HANA。
3. 将群集置于维护模式。
4. 维护工作完成后，将以前的主站点注册为新的辅助站点。
5. 清理群集配置。 请参阅下面的详细信息。
6. 结束群集维护模式。


迁移资源会将一个条目添加到群集配置。 例如，强制故障转移。 必须在结束维护模式之前清理这些条目。 请参阅以下示例。

首先，通过将 msl 资源迁移到当前辅助主节点来强制群集故障转移。 以下命令将发出已创建“移动约束”的警告：

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


通过 SAPHanaSR-showAttr 命令查看故障转移进程。 若要监视群集状态，请打开专用 shell 窗口并结合 watch 启动命令：

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

输出应显示手动故障转移。 以前的辅助主节点将升级，此示例中为 hso-hana-vm-s2-0。 以前的主站点已停止，以前的主要主节点 hso-hana-vm-s1-0 的 lss值为 1： 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

完成群集故障转移和 SAP HANA 接管之后，根据 [Pacemaker](#pacemaker) 中所述将群集置于维护模式。

命令 SAPHanaSR-showAttr 和 crm status 不会指示有关资源迁移创建的约束的任何信息。 若要显示这些约束，一种做法是使用以下命令显示完整的群集资源配置：

<pre><code>
crm configure show
</code></pre>

在群集配置中，查找以前的手动资源迁移产生的新位置约束。 此示例条目以 location cli- 开头：

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

遗憾的是，此类约束可能对整个群集行为产生影响。 因此，在将整个系统恢复启动之前，必须再次删除这些约束。 使用 unmigrate 命令可以清理以前创建的位置约束。 命名可能有点让人迷惑。 此命令不会尝试将资源迁移回到原始 VM。 而只是删除位置约束，并在运行命令时返回相应的信息：


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

维护工作结束时，请按 [Pacemaker](#pacemaker) 中所示停止群集维护模式。



## <a name="hb_report-to-collect-log-files"></a>使用 hb_report 收集日志文件

若要分析 Pacemaker 群集的问题，运行 hb_report 实用工具会有所帮助，而 SUSE 支持人员也要求这样做。 它会收集分析发生情况时所需的所有重要日志文件。 此示例调用使用特定事件发生的开始时间和结束时间。 另请参阅[重要注意事项](#important-notes)：

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

该命令指明压缩日志文件的放置位置：

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

然后，可以通过标准的 **tar** 命令提取单个文件：

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

查看提取的文件，可以找到所有日志文件。 其中的大多数文件放在群集中每个节点的单独目录内：

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


在指定的时间范围内，当前主节点 hso-hana-vm-s1-0 已终止。 在 journal.log 中，可以找到与以下事件相关的条目：

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

另一个示例是辅助主节点（现已成为新的主要主节点）上的 Pacemaker 日志文件。 下面的摘录显示已终止的主要主节点的状态已设置为 offline：

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


下面的摘录来自于群集站点 2 上的 SAP HANA global.ini 文件。 此示例显示了主机名解析条目，用于使用不同的网络进行 SAP HANA 节点间通信和 HSR 通信：

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

群集解决方案还提供浏览器界面，该界面为偏向于使用菜单和图形界面而不是 shell 级所有命令的用户提供 GUI。
若要使用浏览器界面，请将以下 URL 中的 \<节点\> 替换为实际的 SAP HANA 节点。 然后输入群集（用户群集）的凭据：

<pre><code>
https://&ltnode&gt:7630
</code></pre>

此屏幕截图显示了群集仪表板：


![Hawk 群集仪表板](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


此示例演示[计划内维护](#planned-maintenance)中所述的群集资源迁移产生的位置约束示例：


![在 Hawk 中列出约束](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


还可以在 Hawk 的“历史记录”下上传 hb_report 输出，如下方所示。 请参阅 hb_report 收集日志文件： 

![在 Hawk 中上传 hb_report 输出](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

然后，可使用“历史记录浏览器”浏览 hb_report 输出中包含的所有群集转换：

![Hawk 中 hb_report 输出中的转换](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

下方的最后一张屏幕截图显示单个转换的“详细信息”部分。 群集对主要主节点 hso-hana-vm-s1-0 的崩溃做出反应。 群集现在正在将辅助节点 hso-hana-vm-s2-0 提升为新的主节点：

![Hawk 单个转换](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>后续步骤

本故障排除指南介绍了采用横向扩展配置的 SAP HANA 的高可用性。 除数据库以外，SAP 布局中的另一个重要组件是 SAP NetWeaver 堆栈。 了解[使用 SUSE Enterprise Linux 服务器的 Azure 虚拟机上 SAP NetWeaver 的高可用性][sap-nw-ha-guide-sles]。

