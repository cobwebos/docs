---
title: 在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker | Microsoft Docs
description: 在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 51db372b288ce388f58ca0e7fdcb2e1b97e511de
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755724"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

有两个选项可用来在 Azure 中设置 Pacemaker 群集。 可以使用隔离代理，它负责通过 Azure API 重新启动失败的节点；还可以使用 SBD 设备。

SBD 设备至少需要一个额外的充当 iSCSI 目标服务器并提供 SBD 设备的虚拟机。 不过，也可以与其他 Pacemaker 群集共享这些 iSCSI 目标服务器。 使用 SBD 设备的优点是可以更快地进行故障转移，如果使用本地 SBD 设备，则不需要对操作 pacemaker 群集的方式进行任何更改。 最多可对一个 Pacemaker 群集使用三个 SBD 设备，以允许某个 SBD 设备不可用，例如，在修补 iSCSI 目标服务器的 OS 期间。 若要对每个 Pacemaker 使用多个 SBD 设备，请务必部署多个 iSCSI 目标服务器并从每个 iSCSI 目标服务器连接一个 SBD。 我们建议使用一个或三个 SBD 设备。 如果只配置两个 SBD 设备，而其中一个不可用，则 Pacemaker 无法自动隔离群集节点。 当一个 iSCSI 目标服务器关闭时，若要进行隔离，必须使用三个 SBD 设备，因此需要使用三个 iSCSI 目标服务器。

如果不希望另外投资购买一台虚拟机，也可以使用 Azure 隔离代理。 其缺点在于，如果资源停止失败或者群集节点不再可以彼此通信，则故障转移可能要花费 10 到 15 分钟。

![SLES 上的 Pacemaker 概述](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> 在规划和部署 Linux Pacemaker 群集节点与 SBD 设备时，若要实现整个群集配置的整体可靠性，必须做到：所涉及的 VM 与托管 SBD 设备的 VM 之间的路由不通过任何其他设备（例如 [NVA](https://azure.microsoft.com/solutions/network-appliances/)）。 否则，NVA 的问题和维护事件可能会对整个群集配置的稳定性和可靠性产生负面影响。 为了避免此类障碍，在规划和部署 Linux Pacemaker 群集节点与 SBD 设备时，请勿定义通过 NVA 和类似设备路由群集节点与 SBD 设备之间的流量的 NVA 路由规则或[用户定义路由规则](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)。 
>

## <a name="sbd-fencing"></a>SBD 隔离

如果希望使用 SBD 设备进行隔离，请执行以下步骤。

### <a name="set-up-iscsi-target-servers"></a>设置 iSCSI 目标服务器

首先需要创建 iSCSI 目标虚拟机。 可以与多个 Pacemaker 群集共享 iSCSI 目标服务器。

1. 部署新的 SLES 12 SP1 或更高版本的虚拟机并通过 SSH 与其建立连接。 不需要很大的虚拟机。 Standard_E2s_v3 或 Standard_D2s_v3 这样的虚拟机大小就足够了。 确保为 OS 磁盘使用高级存储。

在所有 **iSCSI 目标虚拟机**上运行以下命令。

1. 更新 SLES

   <pre><code>sudo zypper update
   </code></pre>

1. 删除包

   请卸载以下包，避免 targetcli 和 SLES 12 SP3 的已知问题。 可以忽略找不到的包的相关错误

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. 安装 iSCSI 目标包

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. 启用 iSCSI 目标服务

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>在 iSCSI 目标服务器上创建 iSCSI 设备

在所有 **iSCSI 目标虚拟机**上运行以下命令，为 SAP 系统使用的群集创建 iSCSI 磁盘。 以下示例中为多个群集创建 SBD 设备。 其中演示了如何对多个群集使用一个 iSCSI 目标服务器。 在 OS 磁盘中放置 SBD 设备。 确保有足够的空间。

**` nfs`** 用于标识 NFS 群集**ascsnw1**用来标识的 ASCS 群集**NW1**， **dbnw1**用来标识的数据库群集**NW1**， **nfs 0**并**nfs 1** NFS 群集节点的主机名**nw1 xscs 0**和**nw1 xscs 1**的主机名**NW1** ASCS 群集节点，并**nw1-db-0**并**nw1-db-1**是群集节点的主机名的数据库。 请将其替换为群集节点的主机名和 SAP 系统的 SID。

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

使用以下命令检查所有设置是否正确

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>设置 SBD 设备

从群集连接到在最后一步中创建的 iSCSI 设备。
在要创建的新群集的节点上运行以下命令。
以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. **[A]** 连接到 iSCSI 设备

   首先，启用 iSCSI 和 SBD 服务。

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** 在第一个节点上更改发起程序名称

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   更改此文件的内容，以匹配在 iSCSI 目标服务器上创建 iSCSI 设备时使用的 ACL（例如，NFS 服务器的 ACL）。

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** 在第二个节点上更改发起程序名称

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   更改此文件的内容以匹配在 iSCSI 目标服务器上创建 iSCSI 设备时使用的 ACL

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** 重新启动 iSCSI 服务

   现在，重新启动 iSCSI 服务以应用更改

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   连接 iSCSI 设备。 在下面的示例中，10.0.0.17 是 iSCSI 目标服务器的 IP 地址，3260 是默认端口。 <b>iqn.2006 04.nfs.local:nfs</b> 是运行下面第一个命令 (iscsiadm -m discovery) 时列出的目标名称之一。

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   请确保 iSCSI 设备可用并记下设备名称（在以下示例中为 /dev/sde）

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   现在，检索 iSCSI 设备的 ID。

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   该命令列出每个 SBD 设备的三个设备 ID。 建议使用以 scsi-3 开头的 ID，在上面的示例中为

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** 创建 SBD 设备

   使用 iSCSI 设备的设备 ID 在第一个群集节点上创建新的 SBD 设备。

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** 调整 SBD 配置

   打开 SBD 配置文件

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   更改 SBD 设备的属性，启用 pacemaker 集成，并更改 SBD 的启动模式。

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   创建` softdog`配置文件

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   现在，加载模块

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>群集安装

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. [A] 更新 SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** 配置操作系统

   在某些情况下，Pacemaker 会创建多个进程，因此会耗尽允许的进程数。 对于这种情况，群集节点之间的检测信号可能失败，并导致资源故障转移。 我们建议通过设置以下参数来增大允许的进程数上限。

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   减小脏缓存的大小。 有关详细信息，请参阅 [RAM 较大的 SLES 11/12 服务器的写入性能低](https://www.suse.com/support/kb/doc/?id=7010287)。

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configure cloud-netconfig-azure for HA Cluster

   若要防止云网络插件删除 （Pacemaker 必须控制 VIP 分配） 的虚拟 IP 地址如下所示更改网络接口的配置文件。 有关详细信息请参阅[SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633)。 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. [1] 启用 SSH 访问

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. [2] 启用 SSH 访问

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. [1] 启用 SSH 访问

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. [A] 安装隔离代理
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. [A] 设置主机名称解析

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名。 使用 /etc/hosts 的好处是群集可以独立于 DNS（也可能会成为单一故障点）。

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. [1] 安装群集

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. [2] 向群集添加节点

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. [A] 将 hacluster 密码更改为相同的密码

   <pre><code>sudo passwd hacluster
   </code></pre>

1. [A] 将 corosync 配置为使用其他传输，并添加 nodelist。 否则，群集不会工作。

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   如果值不存在或不同，请将以下粗体显示的内容添加到文件。 请确保将令牌更改为 30000，以允许内存保留维护。 有关详细信息，请参阅这篇适用于 [Linux][virtual-machines-linux-maintenance] 或 [Windows][virtual-machines-windows-maintenance] 的文章。 此外，请务必删除参数 mcastaddr。

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   然后重启 corosync 服务

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>创建 Azure 隔离代理 STONITH 设备

STONITH 设备使用服务主体对 Microsoft Azure 授权。 请按照以下步骤创建服务主体。

1. 转到 [https://portal.azure.com](https://portal.azure.com)
1. 打开“Azure Active Directory”边栏选项卡  
   转到“属性”并记下目录 ID。 这是“租户 ID”。
1. 单击“应用注册”
1. 单击“添加”
1. 输入名称，选择应用程序类型“Web 应用/API”，输入登录 URL（例如 http\://localhost），并单击“创建”
1. 不会使用登录 URL，可为它输入任何有效的 URL
1. 选择新应用，并在“设置”选项卡中单击“密钥”
1. 输入新密钥的说明，选择“永不过期”，并单击“保存”
1. 记下值。 此值用作服务主体的**密码**
1. 记下应用程序 ID。 此 ID 用作服务主体的用户名（以下步骤中的“登录 ID”）

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** 为隔离代理创建自定义角色

默认情况下，服务主体无权访问 Azure 资源。 需要为服务主体授予启动和停止（解除分配）群集所有虚拟机的权限。 如果尚未创建自定义角色，可以使用 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) 或 [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 来创建它

将以下内容用于输入文件。 你需要调整内容以适应你的订阅，也就是说，将 c276fc76-9cd4-44c9-99a7-4fd71546436e 和 e91d47c4-76f3-4271-a796-21b4ecfe3624 替换为你的订阅的 ID。 如果只有一个订阅，请删除 AssignableScopes 中的第二个条目。

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** 向服务主体分配自定义角色

将在最后一章中创建的自定义角色“Linux 隔离代理角色”分配给服务主体。 不要再使用“所有者”角色！

1. 转到 [https://portal.azure.com](https://portal.azure.com)
1. 打开“所有资源”边栏选项卡
1. 选择第一个群集节点的虚拟机
1. 选择“访问控制(IAM)”
1. 单击“添加角色分配”
1. 选择角色“Linux 隔离代理角色”
1. 输入前面创建的应用程序名称
1. 点击“保存”

为第二个群集节点重复上述步骤。

### <a name="1-create-the-stonith-devices"></a>**[1]** 创建 STONITH 设备

编辑虚拟机的权限后，可以在群集中配置 STONITH 设备。

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>SBD 的默认 Pacemaker 配置

1. **[1]** 启用 STONITH 设备并设置隔离延迟

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>适用于 Azure 的 pacemaker 配置计划事件

Azure 产品/服务[计划事件](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/scheduled-events)。 预定的事件元数据服务通过提供和留出时间让应用程序准备事件，例如 VM 关闭、 重新部署 VM，等等。资源代理 **[azure 事件](https://github.com/ClusterLabs/resource-agents/pull/1161)** 计划 Azure 事件监视器。 如果检测到事件，则代理将尝试停止受影响的 VM 上的所有资源并将它们移动到群集中的另一个节点。 若要实现的更多的 Pacemaker 资源必须配置。 

1. **[A]** 安装**azure 事件**代理。 

<pre><code>sudo zypper install resource-agents
</code></pre>

2. **[1]** Pacemaker 中配置的资源。 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > 将群集放加入或退出维护模式时配置 azure 事件代理的 Pacemaker 资源后，可能会收到类似的警告消息：  
     警告： 上 bootstrap 选项： 未知的属性 ' hostName_<strong>主机名</strong>  
     警告： 上 bootstrap 选项： 未知的属性 ' azure events_globalPullState  
     警告： 上 bootstrap 选项： 未知的属性 ' hostName_<strong>主机名</strong>  
   > 可以忽略这些警告消息。

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性][sles-nfs-guide]
* [SUSE Linux Enterprise Server for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性][sles-guide]
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
