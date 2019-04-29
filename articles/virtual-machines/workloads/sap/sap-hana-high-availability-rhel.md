---
title: 在 Azure 虚拟机 (VM) 上设置 SAP HANA 系统复制 | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 上建立 SAP HANA 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: 1be3c411a208a2a9da1a4f6a319fdf37cc8aa2dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637790"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上 Azure VM 中 SAP HANA 的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

对于本地开发，可以使用 HANA 系统复制或共享存储来建立 SAP HANA 的高可用性。
在 Azure 虚拟机 (VM) 上，Azure 上的 HANA 系统复制是唯一受支持的高可用性功能。
SAP HANA 复制由一个主节点和至少一个辅助节点组成。 对主节点上数据所做的更改以同步或异步方式复制到辅助节点。

本文介绍如何部署和配置虚拟机、安装群集框架，以及安装和配置 SAP HANA 系统复制。
在示例配置和安装命令中，使用了实例编号 **03** 和 HANA 系统 ID **HN1**。

请先阅读以下 SAP 说明和文档：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表。
  * Azure VM 大小的重要容量信息。
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2002167] 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [针对 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署（本文）][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)（Pacemaker 群集中的 SAP HANA 系统复制）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)（在 Red Hat Enterprise Linux 上安装要在 Microsoft Azure 中使用的 SAP HANA）

## <a name="overview"></a>概述

为实现高可用性，SAP HANA 安装在两台虚拟机上。 数据将使用 HANA 系统复制进行复制。

![SAP HANA 高可用性概述](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA 系统复制设置使用专用的虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示负载均衡器的配置：

* 前端配置：hn1-db 的 IP 地址 10.0.0.13
* 后端配置：连接到应当作为 HANA 系统复制的一部分的所有虚拟机的主网络接口
* 探测端口：端口 62503
* 负载均衡规则：30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP

## <a name="deploy-for-linux"></a>为 Linux 部署

Azure 市场中包含适用于 SUSE Linux Red Hat Enterprise Linux 7.4 for SAP HANA 的映像，可以用于部署新的虚拟机。

### <a name="deploy-with-a-template"></a>使用模板进行部署

可以使用 GitHub 上的某个快速入门模板部署全部所需的资源。 该模板将部署虚拟机、负载均衡器、可用性集，等等。
若要部署模板，请执行以下步骤：

1. 在 Azure 门户中打开[数据库模板][template-multisid-db]。
1. 输入以下参数：
    * **SAP 系统 ID**：输入要安装的 SAP 系统的 SAP 系统 ID。 该 ID 将用作所要部署的资源的前缀。
    * **OS 类型**：选择一个 Linux 发行版。 对于此示例，请选择“RHEL 7”。
    * **数据库类型**：选择“HANA”。
    * **SAP 系统大小**：输入新系统将提供的 SAPS 数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商。
    * **系统可用性**：选择“HA”。
    * **管理员用户名、管理员密码或 SSH 密钥**：创建一个新用户，可用于登录到计算机。
    * **子网 ID**：如果要将 VM 部署到现有 VNet 中，并且该 VNet 中已定义了 VM 应分配到的子网，请指定该特定子网的 ID。 ID 通常如下所示：**/subscriptions/\<订阅 ID>/resourceGroups/\<资源组名称>/providers/Microsoft.Network/virtualNetworks/\<虚拟网络名称>/subnets/\<子网名称>**。 如果要创建新的虚拟网络，请将其留空

### <a name="manual-deployment"></a>手动部署

1. 创建资源组。
1. 创建虚拟网络。
1. 创建可用性集。  
   设置最大更新域。
1. 创建负载均衡器（内部）。
   * 选择在步骤 2 中创建的虚拟网络。
1. 创建虚拟机 1。  
   最低使用 Red Hat Enterprise Linux 7.4 for SAP HANA。 本示例使用 Red Hat Enterprise Linux 7.4 for SAP HANA 映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 选择在步骤 3 中创建的可用性集。
1. 创建虚拟机 2。  
   最低使用 Red Hat Enterprise Linux 7.4 for SAP HANA。 本示例使用 Red Hat Enterprise Linux 7.4 for SAP HANA 映像 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> 选择在步骤 3 中创建的可用性集。
1. 添加数据磁盘。
1. 配置负载均衡器。 首先创建前端 IP 池：

   1. 打开负载均衡器，选择**前端 IP 池**，然后选择“添加”。
   1. 输入新前端 IP 池的名称（例如 **hana-frontend**）。
   1. 将“分配”设置为“静态”并输入 IP 地址（例如 **10.0.0.13**）。
   1. 选择“确定”。
   1. 创建新前端 IP 池后，请记下池 IP 地址。

1. 接下来创建后端池：

   1. 打开负载均衡器，选择**后端池**，然后选择“添加”。
   1. 输入新后端池的名称（例如 **hana-backend**）。
   1. 选择“添加虚拟机”。
   1. 选择在步骤 3 中创建的可用性集。
   1. 选择 SAP HANA 群集的虚拟机。
   1. 选择“确定”。

1. 接下来创建运行状况探测：

   1. 打开负载均衡器，选择**运行状况探测**，然后选择“添加”。
   1. 输入新运行状况探测的名称（例如 **hana-hp**）。
   1. 选择“TCP”作为协议，并选择端口 625**03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。
   1. 选择“确定”。

1. 对于 SAP HANA 1.0，请创建负载均衡规则：

   1. 打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
   1. 输入新负载均衡器规则的名称（例如 hana-lb-3**03**15）。
   1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
   1. 将“协议”保留设置为“TCP”，输入端口 3**03**15。
   1. 将“空闲超时”增大到 30 分钟。
   1. 确保**启用浮动 IP**。
   1. 选择“确定”。
   1. 针对端口 3**03**17 重复上述步骤。

1. 对于 SAP HANA 2.0，请为系统数据库创建负载均衡规则：

   1. 打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
   1. 输入新负载均衡器规则的名称（例如 hana-lb-3**03**13）。
   1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
   1. 将“协议”保留设置为“TCP”，输入端口 3**03**13。
   1. 将“空闲超时”增大到 30 分钟。
   1. 确保**启用浮动 IP**。
   1. 选择“确定”。
   1. 针对端口 3**03**14 重复上述步骤。

1. 对于 SAP HANA 2.0，请先为租户数据库创建负载均衡规则：

   1. 打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
   1. 输入新负载均衡器规则的名称（例如 hana-lb-3**03**40）。
   1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
   1. 将“协议”保留设置为“TCP”，输入端口 3**03**40。
   1. 将“空闲超时”增大到 30 分钟。
   1. 确保**启用浮动 IP**。
   1. 选择“确定”。
   1. 针对端口 3**03**41 和 3**03**42 重复上述步骤。

有关 SAP HANA 所需端口的详细信息，请阅读 [SAP HANA 租户数据库](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6)指南中的[连接到租户数据库](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)一章或 [SAP 说明 2388694][2388694]。

> [!IMPORTANT]
> 不要启用 TCP 放置在 Azure 负载均衡器之后的 Azure Vm 上的时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数设置**net.ipv4.tcp_timestamps**到**0**。 有关详细信息，请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview)。
> 另请参阅 SAP 注释[2382421](https://launchpad.support.sap.com/#/notes/2382421)。 

## <a name="install-sap-hana"></a>安装 SAP HANA

本部分中的步骤使用以下前缀：

* **[A]**：该步骤适用于所有节点。
* **[1]**：该步骤仅适用于节点 1。
* **[2]**：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 设置磁盘布局：**逻辑卷管理器 (LVM)**。

   我们建议对存储数据和日志文件的卷使用 LVM。 以下示例假设虚拟机上附加了四个用于创建两个卷的数据磁盘。

   列出所有可用的磁盘：

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   示例输出：

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   为想要使用的所有磁盘创建物理卷：

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   为数据文件创建卷组。 将一个卷组用于日志文件，将另一个卷组用于 SAP HANA 的共享目录：

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   创建逻辑卷。 线性卷是使用不带 `-i` 开关的 `lvcreate` 创建的。 我们建议创建条带化卷以获得更好的 I/O 性能，`-i` 参数应是基础物理卷的数量。 在本文档中，两个物理卷用于数据卷，因此 `-i` 开关参数设置为 **2**。 一个物理卷用于日志卷，因此未显式使用 `-i` 开关。 对每个数据、日志或共享卷使用多个物理卷时，请使用 `-i` 开关，并将其设置为基础物理卷的数量。

   <pre><code>sudo lvcreate <b>-i 2</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   创建装载目录，并复制所有逻辑卷的 UUID：

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   创建三个逻辑卷的 `fstab` 条目：

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   将以下行插入 `/etc/fstab` 文件：

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   装载新卷：

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** 设置磁盘布局：**无格式磁盘**。

   对于演示系统，可将 HANA 数据和日志文件放在一个磁盘上。 在 /dev/disk/azure/scsi1/lun0 上创建一个分区，并使用 XFS 格式化该分区：

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   将以下行插入 /etc/fstab 文件：

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   创建目标目录并装载磁盘：

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** 为所有主机设置主机名解析。

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts 文件。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名：

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   将以下行插入 /etc/hosts 文件。 根据环境更改 IP 地址和主机名：

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL for HANA 配置

   按照 SAP 说明 [2292690] 和 [2455582] 以及 <https://access.redhat.com/solutions/2447641> 中的说明配置 RHEL。

1. **[A]** 安装 SAP HANA

   要安装 SAP HANA 系统复制，请按照 <https://access.redhat.com/articles/3004101> 执行操作。

   * 从 HANA DVD 运行 hdblcm 程序。 在提示符下输入以下值：
   * 选择安装：输入 **1**。
   * 选择要安装的其他组件：输入 **1**。
   * 输入安装路径 [/hana/shared]：选择 Enter。
   * 输入本地主机名 [..]：选择 Enter。
   * 是否要将其他主机添加到系统? (y/n) [n]：选择 Enter。
   * 输入 SAP HANA 系统 ID：输入 HANA 的 SID，例如：**HN1**。
   * 输入实例编号 [00]：输入 HANA 实例编号。 如果使用了 Azure 模板或者遵循了本文的手动部署部分，请输入 **03**。
   * 选择数据库模式 / 输入索引 [1]：选择 Enter。
   * 选择系统用途/输入索引 [4]：选择系统用途值。
   * 输入数据卷的位置 [/hana/data/HN1]：选择 Enter。
   * 输入日志卷的位置 [/hana/log/HN1]：选择 Enter。
   * 是否限制最大内存分配? [n]：选择 Enter。
   * 输入主机 '...' [...] 的证书主机名：选择 Enter。
   * 输入 SAP 主机代理用户 (sapadm) 密码：输入主机代理用户密码。
   * 确认 SAP 主机代理用户 (sapadm) 密码：再次输入主机代理用户密码以进行确认。
   * 输入系统管理员 (hdbadm) 密码：输入系统管理员密码。
   * 确认系统管理员 (hdbadm) 密码：再次输入系统管理员密码以进行确认。
   * 输入系统管理员主目录 [/usr/sap/HN1/home]：选择 Enter。
   * 输入系统管理员登录 Shell [/ bin/sh]：选择 Enter。
   * 输入系统管理员用户 ID [1001]：选择 Enter。
   * 输入用户组 (sapsys) 的 ID [79]：选择 Enter。
   * 输入数据库用户 (SYSTEM) 密码：输入数据库用户密码。
   * 确认数据库用户 (SYSTEM) 密码：再次输入数据库用户密码以进行确认。
   * 重新引导计算机后是否重新启动系统? [n]：选择 Enter。
   * 是否继续? (y/n)：验证摘要。 按 **y** 继续。

1. **[A]** 升级 SAP 主机代理。

   从 [SAP 软件中心][sap-swcenter]下载最新的 SAP 主机代理存档，并运行以下命令升级代理。 替换存档的路径，使其指向已下载的文件：

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** 配置防火墙

   创建 Azure 负载均衡器探测端口的防火墙规则。

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>配置 SAP HANA 2.0 系统复制

本部分中的步骤使用以下前缀：

* **[A]**：该步骤适用于所有节点。
* **[1]**：该步骤仅适用于节点 1。
* **[2]**：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 配置防火墙

   创建防火墙规则以允许 HANA 系统复制和客户端流量。 [所有 SAP 产品的 TCP/IP 端口](https://help.sap.com/viewer/ports)上均列出了所需端口。 以下命令是允许 HANA 2.0 系统复制和到数据库 SYSTEMDB、HN1 和 NW1 的流量的示例。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** 创建租户数据库。

   如果使用的是 SAP HANA 2.0 或 MDC，请为 SAP NetWeaver 系统创建一个租户数据库。 将 **NW1** 替换为 SAP 系统的 SID。

   作为执行 < hanasid\>adm 以下命令：

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 在第一个节点上配置系统复制：

   备份数据库作为 < hanasid\>adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   将系统 PKI 文件复制到辅助站点：

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   创建主站点：

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在第二个节点上配置系统复制：
    
   注册第二个节点以启动系统复制。 运行以下命令的访问权限 < hanasid\>adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** 检查复制状态

   检查复制状态并等待，直到所有数据库都保持同步。如果状态一直“未知”，请检查防火墙设置。

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>配置 SAP HANA 1.0 系统复制

本部分中的步骤使用以下前缀：

* **[A]**：该步骤适用于所有节点。
* **[1]**：该步骤仅适用于节点 1。
* **[2]**：该步骤仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 配置防火墙

   创建防火墙规则以允许 HANA 系统复制和客户端流量。 [所有 SAP 产品的 TCP/IP 端口](https://help.sap.com/viewer/ports)上均列出了所需端口。 以下命令是允许 HANA 2.0 系统复制的示例。 针对 SAP HANA 1.0 安装对其进行调整。

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** 创建所需的用户。

   以 root 身份运行以下命令。 确保将粗体字符串（HANA 系统 ID **HN1** 和实例编号 **03**）替换为 SAP HANA 安装的值。

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** 创建密钥存储条目。

   若要创建新的密钥存储条目的根以运行以下命令：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** 备份数据库。

   将数据库备份以 root 身份：

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   如果使用的是多租户安装，则还需要备份租户数据库：

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 在第一个节点上配置系统复制。

   创建主站点作为 < hanasid\>adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在第二个节点上配置系统复制。

   注册辅助站点作为 < hanasid\>adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](high-availability-guide-rhel-pacemaker.md) 的步骤，创建适用于此 HANA 服务器的基本 Pacemaker。

## <a name="create-sap-hana-cluster-resources"></a>创建 SAP HANA 群集资源

在所有节点上安装 SAP HANA 资源代理。 确保启用包含程序包的存储库。

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

接下来，创建 HANA 拓扑。 在其中一个 Pacemaker 群集节点上运行以下命令：

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> --clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

接着，创建 HANA 资源：

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.

sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"

sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>

sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false

sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>测试群集设置

本部分介绍如何测试设置。 在开始测试之前，请确保 Pacemaker 没有任何失败的操作（通过 pcs 状态检查）、没有任何意外的位置约束（例如迁移测试的遗留内容），并且 HANA 处于同步状态，例如，使用 systemReplicationStatus：

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>测试迁移

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

可通过执行以下命令来迁移 SAP HANA 主控节点：

<pre><code>[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
</code></pre>

如果设置了 `AUTOMATED_REGISTER="false"`，则此命令应将 SAP HANA 主控节点以及包含虚拟 IP 地址的组迁移到 hn1-db-1。

完成迁移后，“sudo pcs status”输出将如下所示

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

hn1-db-0 上的 SAP HANA 资源将停止。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

迁移过程将创建位置约束，需要再次删除这些约束：

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

使用“pcs status”监视 HANA 资源的状态。 在 hn1-db-0 上启动 HANA 后，输出应如下所示

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>测试 Azure 隔离代理

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

可以通过禁用节点上的网络接口（SAP HANA 作为主控节点运行）来测试 Azure 隔离代理的设置。
请参阅[Red Hat 知识库文章 79523](https://access.redhat.com/solutions/79523)有关如何模拟网络故障的说明。 此示例使用 net_breaker 脚本阻止对网络的所有访问。

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

现在，虚拟机应会根据群集配置重启或停止。
如果将 `stonith-action` 设置为 off，则虚拟机会停止，资源将迁移到正在运行的虚拟机。

> [!NOTE]
> 可能需要 15 分钟虚拟机才能再次联机。

如果设置 `AUTOMATED_REGISTER="false"`，则再次启动虚拟机后，SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

测试之后的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>测试手动故障转移

开始测试之前的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

可以通过停止 hn1-db-0 节点上的群集来测试手动故障转移：

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

故障转移后，可以再次启动该群集。 如果设置了 `AUTOMATED_REGISTER="false"`，则 hn1-db-0 节点上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
</code></pre>

测试之后的资源状态：

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)
