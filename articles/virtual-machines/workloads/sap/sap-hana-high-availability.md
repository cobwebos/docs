---
title: 在 Azure 虚拟机 (VM) 上设置 SAP HANA 系统复制 | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 上建立 SAP HANA 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: b84b523f919e6b253462139b6888e5eb16248084
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Azure 虚拟机 (VM) 上的 SAP HANA 高可用性

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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

在本地，可以使用 HANA 系统复制或共享存储来建立 SAP HANA 的高可用性。
在 Azure VM 上，Azure 上的 HANA 系统复制是目前唯一受支持的高可用性功能。 SAP HANA 复制由一个主节点和至少一个辅助节点组成。 对主节点上数据所做的更改以同步或异步方式复制到辅助节点。

本文介绍如何部署虚拟机、配置虚拟机、安装群集框架，以及安装和配置 SAP HANA 系统复制。
在示例配置和安装命令等内容中，使用了实例编号 03 和 HANA 系统 ID HN1。

请先阅读以下 SAP 说明和文档

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2205917] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的推荐 OS 设置
* SAP 说明 [1944799] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1984787] 包含有关 SUSE Linux Enterprise Server 12 的一般信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [针对 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署（本文）][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA SR 性能优化方案][suse-hana-ha-guide]本指南包含用于在本地设置 SAP HANA 系统复制的全部所需信息。 请使用本指南作为基准。

## <a name="overview"></a>概述

为实现高可用性，SAP HANA 安装在两台虚拟机上。 数据将使用 HANA 系统复制进行复制。

![SAP HANA 高可用性概述](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA SR 安装使用专用的虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示负载均衡器的配置。

* 前端配置
  * hn1-db 的 IP 地址 10.0.0.13
* 后端配置
  * 连接到应当作为 HANA 系统复制的一部分的所有虚拟机的主网络接口
* 探测端口
  * 端口 62503
* 负载均衡规则
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>部署 Linux

SUSE Linux Enterprise Server for SAP Applications 中已随附 SAP HANA 的资源代理。
Azure Marketplace 中包含适用于 SUSE Linux Enterprise Server for SAP Applications 12 的映像，可以用于部署新的虚拟机。

### <a name="deploy-with-template"></a>使用模板进行部署
可以使用 github 上的某个快速启动模板部署全部所需资源。 该模板将部署虚拟机、负载均衡器、可用性集，等等。若要部署模板，请执行以下步骤：

1. 在 Azure 门户中打开[数据库模板][template-multisid-db]或[聚合模板][template-converged]。 
   数据库模板仅创建数据库的负载均衡规则，而聚合模板还会创建 ASCS/SCS 和 ERS（仅限 Linux）实例的负载均衡规则。 如果打算安装基于 SAP NetWeaver 的系统，同时想要在同一台计算机上安装 ASCS/SCS 实例，请使用[聚合模板][template-converged]。
1. 输入以下参数
    1. SAP 系统 ID  
       输入要安装的 SAP 系统的 SAP 系统 ID。 此 ID 将用作已部署资源的前缀。
    1. 堆栈类型（仅当使用聚合模板时才适用）   
       选择 SAP NetWeaver 堆栈类型
    1. OS 类型  
       选择一个 Linux 发行版。 对于本示例，请选择“SLES 12”
    1. 数据库类型  
       选择 HANA
    1. SAP 系统大小  
       新系统将提供的 SAPS 数量。 如果不确定系统需要多少 SAPS，请咨询 SAP 技术合作伙伴或系统集成商
    1. 系统可用性  
       选择 HA
    1. 管理员用户名和管理员密码  
       创建可用于登录计算机的新用户。
    1. 新子网或现有子网  
       确定是要创建新的虚拟网络和子网，还是使用现有子网。 如果已有连接到本地网络的虚拟网络，请选择现有虚拟网络。
    1. 子网 ID  
    虚拟机应当连接到的子网的 ID。 若要将虚拟机连接到本地网络，请选择 VPN 或 Express Route 虚拟网络的子网。 ID 通常类似于 /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

### <a name="manual-deployment"></a>手动部署

1. 创建资源组。
1. 创建虚拟网络
1. 创建可用性集  
   设置最大更新域
1. 创建负载均衡器（内部）  
   选择在第二步中创建的 VNET
1. 创建虚拟机 1  
   请至少使用 SLES4SAP 12 SP1，在此示例中，我们将使用 SLES4SAP 12 SP2 映像 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES for SAP 12 SP2（高级）  
   选择前面创建的可用性集  
1. 创建虚拟机 2  
   请至少使用 SLES4SAP 12 SP1，在此示例中，我们将使用 SLES4SAP 12 SP1 BYOS 映像 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES for SAP 12 SP2（高级）  
   选择前面创建的可用性集  
1. 添加数据磁盘
1. 配置负载均衡器
    1. 创建前端 IP 池
        1. 打开负载均衡器，选择前端 IP 池，并单击“添加”
        1. 输入新前端 IP 池的名称（例如 hana-frontend）
        1. 将“分配”设置为“静态”并输入 IP 地址（例如 **10.0.0.13**）
        1. 单击“确定”
        1. 创建新前端 IP 池后，请记下其 IP 地址
    1. 创建后端池
        1. 打开负载均衡器，单击后端池，并单击“添加”
        1. 输入新后端池的名称（例如 hana-backend）
        1. 单击“添加虚拟机”
        1. 选择前面创建的可用性集
        1. 选择 SAP HANA 群集的虚拟机
        1. 单击“确定”
    1. 创建运行状况探测器
        1. 打开负载均衡器，选择运行状况探测，并单击“添加”
        1. 输入新运行状况探测的名称（例如 hana-hp）
        1. 选择 TCP 作为协议，选择端口 625**03**，将“间隔”保留为 5，将“不正常阈值”保留为 2
        1. 单击“确定”
    1. 创建负载均衡规则
        1. 打开负载均衡器，选择负载均衡规则，并单击“添加”
        1. 输入新负载均衡器规则的名称（例如 hana-lb-3**03**15）
        1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend）
        1. 将协议保留为“TCP”，输入端口 3**03**13
        1. 将空闲超时增大到 30 分钟
        1. **确保启用浮动 IP**
        1. 单击“确定”
        1. 针对端口 3**03**15 和 3**03**17 重复上述步骤

## <a name="create-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的步骤为此 HANA 服务器创建一个基本 Pacemaker 群集。 可以为 SAP HANA 和 SAP NetWeaver (A)SCS 使用同一 Pacemaker 群集。

## <a name="installing-sap-hana"></a>安装 SAP HANA

以下各项带有前缀 **[A]** - 适用于所有节点、**[1]** - 仅适用于节点 1，或 **[2]** - 仅适用于 Pacemaker 群集的节点 2。

1. **[A]** 设置磁盘布局
    1. LVM  
       
       我们通常建议对存储数据和日志文件的卷使用 LVM。 以下示例假设虚拟机上附加了四个应该用于创建两个卷的数据磁盘。

       列出所有可用的磁盘
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       示例输出
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       为想要使用的所有磁盘创建物理卷。    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       为数据文件创建卷组，一个卷组用于日志文件，另一个卷组用于 SAP HANA 的共享目录

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       创建逻辑卷

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       创建装载目录，并复制所有逻辑卷的 UUID
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       创建三个逻辑卷的 fstab 条目
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       将此行插入到 /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       装载新卷
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. 无格式磁盘  
       对于演示系统，可将 HANA 数据和日志文件放在一个磁盘上。 以下命令在 /dev/disk/azure/scsi1/lun0 上创建一个分区，并使用 XFS 格式化该分区。

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       将此行插入到 /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       创建目标目录并装载磁盘。

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** 为所有主机设置主机名解析  
    可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名
    ```bash
    sudo vi /etc/hosts
    ```
    将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** 安装 HANA HA 包  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

若要安装 SAP HANA 系统复制，请遵循 [SAP HANA SR 性能优化方案指南][suse-hana-ha-guide]的第 4 章。

1. [A] 从 HANA DVD 运行 hdblcm
    * 选择安装 -> 1
    * 选择要安装的其他组件 -> 1
    * 输入安装路径 [/hana/shared]：-> 按 ENTER
    * 输入本地主机名 [..]：-> 按 ENTER
    * 是否要将其他主机添加到系统? (y/n) [n]：-> 按 ENTER
    * 输入 SAP HANA 系统 ID：<SID of HANA e.g. HN1>
    * 输入实例编号 [00]：   
  HANA 实例编号。 如果使用了 Azure 模板或者遵循了手动部署步骤，请使用 03
    * 选择数据库模式/输入索引 [1]：-> 按 ENTER
    * 选择系统用途/输入索引 [4]：  
  选择系统用途
    * 输入数据卷的位置 [/hana/data/HN1]：-> 按 ENTER
    * 输入日志卷的位置 [/hana/log/HN1]：-> 按 ENTER
    * 是否限制最大内存分配? [n]：-> 按 ENTER
    * 输入主机 '...' 的证书主机名 [...]：-> 按 ENTER
    * 输入 SAP 主机代理用户 (sapadm) 密码：
    * 确认 SAP 主机代理用户 (sapadm) 密码：
    * 输入系统管理员 (hdbadm) 密码：
    * 确认系统管理员 (hdbadm) 密码：
    * 输入系统管理员主目录 [/usr/sap/HN1/home]：-> 按 ENTER
    * 输入系统管理员登录 Shell [/ bin/sh]：-> 按 ENTER
    * 输入系统管理员用户 ID [1001]：-> 按 ENTER
    * 输入用户组 (sapsys) 的 ID [79]：-> 按 ENTER
    * 输入数据库用户 (SYSTEM) 密码：
    * 确认数据库用户 (SYSTEM) 密码：
    * 重新引导计算机后是否重新启动系统? [n]：-> 按 ENTER
    * 是否继续? (y/n)：   
  验证摘要并输入 y 继续

1. [A] 升级 SAP 主机代理  
  从 [SAP 软件中心][sap-swcenter]下载最新的 SAP 主机代理存档，并运行以下命令升级代理。 替换存档的路径，使其指向已下载的文件。
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>配置 SAP HANA 2.0 系统复制

以下各项带有前缀 **[A]** - 适用于所有节点、**[1]** - 仅适用于节点 1，或 **[2]** - 仅适用于 Pacemaker 群集的节点 2。

1. **[1]** 创建租户数据库

   如果使用的是 SAP HANA 2.0 或 MDC，请为你的 SAP NetWeaver 系统创建一个租户数据库。 将 NW1 替换为你的 SAP 系统的 SID。

   以 `<hanasid`>adm 身份登录并执行以下命令

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** 在第一个节点上配置系统复制
   
   以 `<hanasid`>adm 身份登录并备份数据库

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   将系统 PKI 文件复制到辅助数据库

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   创建主站点。

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** 在第二个节点上配置系统复制
    
    注册第二个节点以启动系统复制。 以 `<hanasid`>adm 身份登录并运行以下命令

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>配置 SAP HANA 1.0 系统复制

1. **[1]** 创建必需的用户

    以 root 身份登录并运行以下命令。 请确保将粗体字符串（HANA 系统 ID HN1 和实例编号 03）替换为你的 SAP HANA 安装的值。

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** 创建密钥存储条目
   
    以 root 身份登录并运行以下命令来创建新的密钥存储条目。

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]** 备份数据库

   以 root 身份登录并备份数据库

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   如果你使用的是多租户安装，则还需要备份租户数据库

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** 在第一个节点上配置系统复制
    
    以 `<hanasid`>adm 身份登录并创建主站点。

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]** 在第二个节点上配置系统复制。

    以 `<hanasid`>adm 身份登录并注册辅助站点。

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>创建 SAP HANA 群集资源

   首先，创建 HANA 拓扑。 在其中一个 Pacemaker 群集节点上运行以下命令。
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   接着，创建 HANA 资源。
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>测试群集设置
本章介绍如何测试设置。 每项测试都假设你是 root 用户，且 SAP HANA 主机在虚拟机 hn1-db-0 上运行。

#### <a name="fencing-test"></a>隔离测试

可以通过禁用节点 hn1-db-0 上的网络接口来测试隔离代理的设置。

<pre><code>
sudo ifdown eth0
</code></pre>

现在，虚拟机应会根据群集配置重新启动或停止。
如果将 stonith-action 设置为 off，虚拟机会停止，资源将迁移到正在运行的虚拟机。

如果设置 AUTOMATED_REGISTER ="false"，则再次启动虚拟机后，SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>测试手动故障转移

可以通过停止节点 hn1-db-0 上的 pacemaker 服务来测试手动故障转移。
<pre><code>
service pacemaker stop
</code></pre>

故障转移后，可以再次启动该服务。 如果设置了 AUTOMATED_REGISTER ="false"，则 hn1-db-0 上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>测试迁移

可通过执行以下命令来迁移 SAP HANA 主控节点
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

如果设置了 AUTOMATED_REGISTER="false"，则此命令序列应将 SAP HANA 主控节点以及包含虚拟 IP 地址的组迁移到 hn1-db-1。
hn1-db-0 上的 SAP HANA 资源将无法作为辅助节点启动。 在这种情况下，请执行以下命令，将 HANA 实例配置为辅助节点：

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

迁移过程将创建位置约束，需要再次删除这些约束。

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

此外，还需要清理辅助节点资源的状态

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>后续步骤
* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。 
