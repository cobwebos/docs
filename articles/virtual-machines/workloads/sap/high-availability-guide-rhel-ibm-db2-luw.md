---
title: 在 RHEL 上的 Azure 虚拟机（Vm）上设置 IBM Db2 HADR |Microsoft Docs
description: 在 Azure 虚拟机（Vm） RHEL 上建立 IBM Db2 LUW 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: 1a00a3c1e0d34a8c7abbcd5bfc7a6771d9e2a4c3
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983034"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server 上 Azure VM 中 IBM Db2 LUW 的高可用性

[高可用性和灾难恢复（HADR）配置](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)中的 IBM Db2 for LINUX、UNIX 和 WINDOWS （LUW）包含一个运行主数据库实例的节点和至少一个运行辅助数据库实例的节点。 主数据库实例的更改将以同步或异步方式复制到辅助数据库实例，具体取决于你的配置。 

本文介绍如何部署和配置 Azure 虚拟机（Vm）、安装群集框架，以及安装具有 HADR 配置的 IBM Db2 LUW。 

本文不介绍如何通过 HADR 或 SAP 软件安装来安装和配置 IBM Db2 LUW。 为了帮助你完成这些任务，我们提供了对 SAP 和 IBM 安装手册的参考。 本文重点介绍特定于 Azure 环境的部件。 

受支持的 IBM Db2 版本为10.5 及更高版本，如 SAP 说明[1928533]中所述。

在开始安装之前，请参阅以下 SAP 说明和文档：

| SAP 说明 | 说明 |
| --- | --- |
| [1928533] | Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] | Azure 上的 SAP：支持先决条件 |
| [2178632] | Azure 上的 SAP 的关键监视指标 |
| [2191498] | 带有 Azure 的 Linux 上的 SAP：增强型监视 |
| [2243692] | Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [2002167] | Red Hat Enterprise Linux 7.x：安装和升级 |
| [2694118] | Azure 上的 Red Hat Enterprise Linux HA 附加项 |
| [1999351] | 适用于 SAP 的增强型 Azure 监视故障排除 |
| [2233094] | DB6： Azure 上使用 IBM Db2 for Linux、UNIX 和 Windows 的 SAP 应用程序-其他信息 |
| [1612105] | DB6： Db2 上的 FAQ with HADR |


| 文档 | 
| --- |
| [SAP 社区 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含适用于 Linux 的所有必需 SAP 说明 |
| [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]指南 |
| [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]（本文） |
| [适用于 Linux 上的 SAP 的 Azure 虚拟机数据库管理系统（DBMS）部署][dbms-guide]指南 |
| [Azure 上的 SAP 工作负荷规划和部署清单][azr-sap-plancheck] |
| [Red Hat Enterprise Linux 7 的高可用性附加项概述][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin]（高可用性附加产品管理） |
| [High Availability Add-On 参考][rhel-ha-ref] |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp]（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst]（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
| [针对 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2] |
| [IBM Db2 HADR 11。1][db2-hadr-11.1] |
| [IBM Db2 HADR 10。5][db2-hadr-10.5] |
| [RHEL 高可用性群集的支持策略-群集中适用于 Linux、Unix 和 Windows 的 IBM Db2 管理][rhel-db2-supp]



## <a name="overview"></a>概述
为实现高可用性，使用 HADR 的 IBM Db2 LUW 安装在至少两个 Azure 虚拟机上，这些虚拟机部署在[azure 可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)或跨[Azure 可用性区域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 

以下图形显示了两个数据库服务器 Azure Vm 的设置。 两个数据库服务器 Azure Vm 都附加了其自己的存储，并已启动并运行。 在 HADR 中，一个 Azure Vm 中的一个数据库实例具有主实例的角色。 所有客户端均连接到主实例。 数据库事务中的所有更改都将在 Db2 事务日志中本地保存。 当事务日志记录在本地保存时，记录将通过 TCP/IP 传输到第二个数据库服务器、备用服务器或备用实例上的数据库实例。 备用实例通过前滚传输的事务日志记录来更新本地数据库。 通过这种方式，备用服务器与主服务器保持同步。

HADR 只是一种复制功能。 它没有故障检测，也没有自动接管或故障转移功能。 接管或传输到备用服务器时，必须由数据库管理员手动启动。 若要实现自动接管和故障检测，可以使用 Linux Pacemaker 群集功能。 Pacemaker 监视两个数据库服务器实例。 当主数据库服务器实例发生故障时，Pacemaker 将通过备用服务器启动*自动*HADR 接管。 Pacemaker 还可确保将虚拟 IP 地址分配给新的主服务器。

![IBM Db2 高可用性概述](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

若要让 SAP 应用程序服务器连接到主数据库，需要使用虚拟主机名和虚拟 IP 地址。 发生故障转移时，SAP 应用程序服务器将连接到新的主数据库实例。 在 Azure 环境中，需要使用[azure 负载均衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)来使用虚拟 IP 地址，这与 IBM DB2 的 HADR 所需的方式相同。 

为了帮助你充分了解 IBM Db2 LUW 与 HADR 和 Pacemaker 如何适应高度可用的 SAP 系统设置，下图概述了基于 IBM Db2 数据库的 SAP 系统的高可用性设置。 本文仅介绍 IBM Db2，但它提供了有关如何设置 SAP 系统的其他组件的其他文章的参考。

![IBM DB2 高可用性完整环境概述](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需步骤的高级概述
若要部署 IBM Db2 配置，需执行以下步骤：

  + 规划您的环境。
  + 部署 Vm。
  + 更新 RHEL Linux 并配置文件系统。
  + 安装和配置 Pacemaker。
  + 设置[glusterfs 群集][glusterfs]或[Azure NetApp 文件][anf-rhel]
  + [在单独的群集上安装 ASCS/ERS][ascs-ha-rhel]。
  + 安装具有分布式/高可用性选项的 IBM Db2 数据库（SWPM）。
  + 安装和创建辅助数据库节点和实例，并配置 HADR。
  + 确认 HADR 是否正常工作。
  + 应用 Pacemaker 配置以控制 IBM Db2。
  + 配置 Azure 负载均衡器。
  + 安装主应用程序服务器和对话框应用程序服务器。
  + 检查并改编 SAP 应用程序服务器的配置。
  + 执行故障转移和接管测试。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>通过 HADR 规划用于托管 IBM Db2 LUW 的 Azure 基础结构

在执行部署之前完成规划过程。 规划为在 Azure 中使用 HADR 部署 Db2 的配置奠定了基础。 下表列出了需要作为 IMB Db2 LUW （SAP 环境的数据库部分）规划的一部分的关键元素：

| 主题 | 简短说明 |
| --- | --- |
| 定义 Azure 资源组 | 部署 VM、VNet、Azure 负载均衡器和其他资源的资源组。 可以是现有的或新的。 |
| 虚拟网络/子网定义 | 用于 IBM Db2 和 Azure 负载均衡器的 Vm 的部署位置。 可以是现有的，也可以是新创建的。 |
| 托管 IBM Db2 LUW 的虚拟机 | VM 大小、存储、网络、IP 地址。 |
| IBM Db2 数据库的虚拟主机名和虚拟 IP| 用于 SAP 应用程序服务器连接的虚拟 IP 或主机名。 **virt-hostname**， **virt-ip**。 |
| Azure 防护 | 防止出现裂脑情况的方法。 |
| Azure 负载均衡器 | 使用的是基本或标准（推荐），用于 Db2 数据库的探测端口（我们的建议62500）**探测端口**。 |
| 名称解析| 名称解析在环境中的工作方式。 强烈建议使用 DNS 服务。 可以使用本地主机文件。 |
    
有关 Azure 中 Linux Pacemaker 的详细信息，请参阅在[azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker][rhel-pcs-azr]。

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上的部署

Red Hat Enterprise Linux Server HA 加载项中包含 IBM Db2 LUW 的资源代理。 对于本文档中所述的设置，应使用适用于 SAP 的 Red Hat Enterprise Linux。 Azure Marketplace 包含适用于 SAP 或更高版本的 Red Hat Enterprise Linux 7.4 的映像，可用于部署新的 Azure 虚拟机。 选择 Azure VM Marketplace 中的 VM 映像时，请注意通过 Azure Marketplace 提供的各种支持或服务模型。

### <a name="hosts-dns-updates"></a>主机： DNS 更新
创建所有主机名（包括虚拟主机名）的列表，并更新您的 DNS 服务器，以便为主机名解析启用适当的 IP 地址。 如果 DNS 服务器不存在，或者你无法更新和创建 DNS 条目，则需要使用参与此方案的各个 Vm 的本地主机文件。 如果使用的是主机文件条目，请确保将条目应用到 SAP 系统环境中的所有 Vm。 但是，我们建议你使用理想情况下扩展到 Azure 中的 DNS


### <a name="manual-deployment"></a>手动部署

请确保 ibm/SAP 对于 IBM Db2 LUW 支持所选操作系统。 SAP 说明[1928533]中提供了 Azure Vm 和 Db2 版本支持的操作系统版本列表。 SAP 产品可用性矩阵中提供了各个 Db2 版本的操作系统版本列表。 我们强烈建议至少为 SAP Red Hat Enterprise Linux 7.4，因为此版本或更高版本中的 Azure 相关性能改进 Red Hat Enterprise Linux 版本。

1. 创建或选择资源组。
1. 创建或选择虚拟网络和子网。
1. 创建 Azure 可用性集或部署可用性区域。
    + 对于可用性集，将 "最大更新域数" 设置为2。
1. 创建虚拟机1。
    + 使用 Azure Marketplace 中的 SAP 映像 Red Hat Enterprise Linux。
    + 选择在步骤3中创建的 Azure 可用性集，或选择 "可用性区域"。
1.  创建虚拟机2。
    + 使用 Azure Marketplace 中的 SAP 映像 Red Hat Enterprise Linux。
    + 选择在步骤3中创建的 Azure 可用性集，或选择 "可用性区域" （与步骤3中的区域不同）。
1. 向 Vm 添加数据磁盘，然后在[IBM Db2 Azure 虚拟机 DBMS 部署的 SAP 工作负荷][dbms-db2]一文中查看文件系统设置的建议。

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集
    
若要为此 IBM Db2 服务器创建基本 Pacemaker 群集，请参阅在 [Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker][rhel-pcs-azr]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安装 IBM Db2 LUW 和 SAP 环境

在开始安装基于 IBM Db2 LUW 的 SAP 环境之前，请查看以下文档：

+ Azure 文档
+ SAP 文档
+ IBM 文档

本文的介绍部分提供了本文档的链接。

请查看有关在 IBM Db2 LUW 上安装基于 NetWeaver 的应用程序的 SAP 安装手册。
可以使用[Sap 安装指南][sap-instfind]查找器在 sap 帮助门户中找到指南。

可以通过设置以下筛选器来减少门户中显示的指南数：
- 我想要： "安装新系统"
- 我的数据库： "IBM Db2 for Linux、Unix 和 Windows"
- SAP NetWeaver 版本、堆栈配置或操作系统的其他筛选器

#### <a name="red-hat-firewall-rules"></a>Red Hat 防火墙规则
默认情况下，Red Hat Enterprise Linux 启用防火墙。 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>有关通过 HADR 设置 IBM Db2 LUW 的安装提示

若要设置主 IBM Db2 LUW 数据库实例，请执行以下操作：

- 使用高可用性或 distributed 选项。
- 安装 SAP ASCS/ERS 和数据库实例。
- 备份新安装的数据库。

> [!IMPORTANT] 
> 记下在安装过程中设置的 "数据库通信端口"。 这两个数据库实例的端口号必须相同。
>![SAP SWPM 端口定义](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>适用于 Azure 的 IBM Db2 HADR 设置

   使用 Azure Pacemaker 防护代理时，请设置以下参数：

   - HADR 对等窗口持续时间（秒）（HADR_PEER_WINDOW） = 240  
   - HADR 超时值（HADR_TIMEOUT） = 45

建议根据初始故障转移/接管测试来实现前面的参数。 必须测试故障转移的正确功能并接管这些参数设置。 由于各个配置可能会有所不同，因此参数可能需要调整。 

> [!NOTE]
> 特定于 IBM Db2 和具有正常启动的 HADR 配置：辅助或备用数据库实例必须启动并运行，然后才能启动主数据库实例。

   
> [!NOTE]
> 对于特定于 Azure 和 Pacemaker 的安装和配置：在安装过程中，通过 SAP 软件预配管理器，对于 IBM Db2 LUW 的高可用性有一个明确的问题：
>+ 不要选择**IBM Db2 pureScale**。
>+ 请勿选择 "**安装适用于 Multiplatforms 的 IBM Tivoli 系统自动化**"。
>+ 不要选择 "**生成群集配置文件**"。
>![SAP SWPM-DB2 HA 选项](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


若要使用 SAP 同类系统复制过程来设置备用数据库服务器，请执行以下步骤：

1. 选择 "**系统复制**" 选项 > "**目标系统** > **分布式** > **数据库实例**"。
1. 作为复制方法，请选择 "**同类系统**"，以便可以使用 "备份" 在备用服务器实例上还原备份。
1. 当你到达用于为同类系统副本还原数据库的退出步骤时，请退出安装程序。 从主主机的备份中还原数据库。 所有后续安装阶段都已在主数据库服务器上执行。

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>适用于 DB2 HADR 的 Red Hat 防火墙规则
添加防火墙规则以允许流量流向 DB2，并使 HADR 介于 DB2 之间工作：
+ 数据库通信端口。 如果使用分区，则也要添加这些端口。
+ HADR 端口（DB2 参数 HADR_LOCAL_SVC 值）
+ Azure 探测端口
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 检查
出于演示目的和本文中所述的过程，数据库 SID 为**ID2**。

配置 HADR 并在主节点和备用节点上连接状态后，请执行以下检查：

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 配置

如果在发生节点故障时使用 Pacemaker 进行自动故障转移，则需要相应地配置 Db2 实例和 Pacemaker。 本部分介绍此类型的配置。

以下各项带有前缀：

- **[A]**：适用于所有节点
- **[1]**：仅适用于节点1 
- **[2]**：仅适用于节点2

**[A]** Pacemaker 配置的先决条件：
1. 通过 db2stop 将两个数据库服务器与\<用户 db2 sid 一起关闭>。
1. 将 db2\<sid> 用户的 shell 环境更改为 */bin/ksh*：
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker 配置

**[1]** IBM Db2 HADR 特定的 Pacemaker 配置：
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** 创建 IBM Db2 资源：
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** 启动 IBM Db2 资源：
* 将 Pacemaker 置于维护模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** 确保群集状态为 "正常"，并且所有资源均已启动。 资源在哪个节点上运行并不重要。
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online： [az-idb01 az-idb02]

完整的资源列表：

 rsc_st_azure （stonith： fence_azure_arm）：已启动 az-idb01 Master/从属集： Db2_HADR_ID2 主 [Db2_HADR_ID2] Master： [az-idb01] 资源组： [az-idb02] 资源组： g_ipnc_db2id2_ID2 vip_db2id2_ID2 （ocf：： IPaddr2： Idb01）：已启动 az-ocf nc_db2id2_ID2 （idb01：：检测信号： azure-lb）：已启动 az-

Daemon 状态： corosync： active/disabled pacemaker： active/disabled pcsd： active/enabled
</pre>

> [!IMPORTANT]
> 必须使用 Pacemaker 工具管理 Pacemaker 群集 Db2 实例。 如果使用 db2 命令（如 db2stop），则 Pacemaker 会将操作检测为资源故障。 如果要执行维护，可以将节点或资源置于维护模式。 Pacemaker 挂起监视资源，然后可以使用普通的 db2 管理命令。


### <a name="configure-azure-load-balancer"></a>配置 Azure 负载均衡器
若要配置 Azure 负载均衡器，建议使用[azure 标准负载均衡器 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) ，然后执行以下操作：

> [!NOTE]
> 标准负载均衡器 SKU 具有从负载均衡器下的节点访问公共 IP 地址的限制。 [使用 Azure 标准负载均衡器在 SAP 高可用性方案中的虚拟机的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)文章介绍了如何启用这些节点来访问公共 IP 地址



1. 创建前端 IP 池：

   a. 在 Azure 门户中，打开 Azure 负载均衡器，选择 "**前端 IP 池**"，然后选择 "**添加**"。

   b. 输入新前端 IP 池的名称（例如， **Db2 连接**）。

   c. 将 "**分配**" 设置为 "**静态**"，并输入在**开头定义的 "ip 地址**"。

   d. 选择“确定”  。

   e. 创建新前端 IP 池后，请记下池 IP 地址。

1. 创建后端池：

   a. 在 Azure 门户中，打开 Azure 负载均衡器，选择 "**后端池**"，然后选择 "**添加**"。

   b. 输入新后端池的名称（例如， **Db2-后**端）。

   c. 选择“添加虚拟机”。****

   d. 选择在上一步中创建的托管 IBM Db2 数据库的可用性集或虚拟机。

   e. 选择 IBM Db2 群集的虚拟机。

   f. 选择“确定”  。

1. 创建运行状况探测：

   a. 在 Azure 门户中，打开 Azure 负载均衡器，选择 "**运行状况探测**"，然后选择 "**添加**"。

   b. 输入新运行状况探测的名称（例如， **Db2-hp**）。

   c. 选择 " **TCP** " 作为协议和端口**62500**。 保持**间隔**值设置为**5**，并将 "不**正常阈值**" 设置为 " **2**"。

   d. 选择“确定”  。

1. 创建负载均衡规则：

   a. 在 Azure 门户中，打开 Azure 负载均衡器，选择 "**负载均衡规则**"，然后选择 "**添加**"。

   b. 输入新负载均衡器规则的名称（例如**Db2-SID**）。

   c. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如， **Db2-前端**）。

   d. 将**协议**设置为 " **TCP**"，并输入端口*数据库通信端口*。

   e. 将“空闲超时”增大到 30 分钟。****

   f. 确保**启用浮动 IP**。

   g. 选择“确定”  。

**[A]** 为探测端口添加防火墙规则：
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>对 SAP 配置文件进行更改，以使用虚拟 IP 进行连接
若要连接到 HADR 配置的主实例，SAP 应用程序层需要使用为 Azure 负载均衡器定义和配置的虚拟 IP 地址。 需要进行以下更改：

/sapmnt/\<SID>/profile/default。DEFAULT.PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安装主应用程序和对话框应用程序服务器

在针对 Db2 HADR 配置安装主应用程序服务器和对话框应用程序服务器时，请使用为配置选择的虚拟主机名。 

如果在创建 Db2 HADR 配置之前执行了安装，请按照上一节中所述的方式进行更改，并遵循 SAP Java stack。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 或 Java stack 系统 JDBC URL 检查

使用 J2EE 配置工具检查或更新 JDBC URL。 由于 J2EE 配置工具是图形工具，因此需要安装 X server：
 
1. 登录到 J2EE 实例的主应用程序服务器并执行以下操作：
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. 在左框架中，选择 "**安全存储**"。
1. 在右侧框中，选择 "密钥`jdbc/pool/\<SAPSID>/url`"。
1. 将 JDBC URL 中的主机名更改为虚拟主机名。
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. 选择“添加”  。
1. 若要保存所做的更改，请在左上角选择磁盘图标。
1. 关闭配置工具。
1. 重新启动 Java 实例。

## <a name="configure-log-archiving-for-hadr-setup"></a>配置 HADR 安装的日志存档
若要为 HADR 安装程序配置 Db2 日志存档，我们建议你将主数据库和备用数据库配置为从所有日志存档位置自动执行日志检索功能。 主数据库和备用数据库都必须能够从所有日志存档位置检索日志存档文件，其中一个数据库实例可能会将日志文件存档。 

日志存档仅在主数据库中执行。 如果更改数据库服务器的 HADR 角色或发生故障，则新的主数据库将负责日志存档。 如果已设置多个日志存档位置，则日志可能会存档两次。 在本地或远程追赶的情况下，你可能还必须手动将存档的日志从旧的主服务器复制到新的主服务器的活动日志位置。

建议配置公共 NFS 共享或 GlusterFS，其中日志是从这两个节点写入的。 NFS 共享或 GlusterFS 必须高度可用。 

可以使用现有的高可用 NFS 共享或 GlusterFS 传输或配置文件目录。 有关详细信息，请参见:

- [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS][glusterfs] 
- [Azure Vm 上的 SAP NetWeaver 高可用性，适用于 SAP 应用程序的 Azure NetApp 文件 Red Hat Enterprise Linux][anf-rhel]
- [Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)（用于创建 NFS 共享）

## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试 Db2 HADR 设置。 每个测试都假设 IBM Db2 主副本正在*az idb01*虚拟机上运行。 必须使用具有 sudo 权限的用户或 root （不推荐）。

此处说明了所有测试用例的初始状态：（crm_mon r 或 pc 状态）

- **pc 状态**是执行时 Pacemaker 状态的快照 
- **crm_mon-r**是 Pacemaker 状态的连续输出

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

SAP 系统中的原始状态记录在 DBACOCKPIT > Configuration > 概述中，如下图所示：

![DBACockpit-预迁移](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>IBM Db2 的测试接管


> [!IMPORTANT] 
> 开始测试之前，请确保：
> * Pacemaker 不包含任何失败的操作（pc 状态）。
> * 没有位置约束（迁移测试的 leftovers）
> * IBM Db2 HADR 同步正在运行。 检查用户 db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


通过执行以下命令迁移运行主 Db2 数据库的节点：
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

完成迁移后，crm 状态输出如下所示：
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

SAP 系统中的原始状态记录在 DBACOCKPIT > Configuration > 概述中，如下图所示：

![DBACockpit-迁移后](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

具有 "pc 资源移动" 的资源迁移创建位置约束。 在这种情况下，位置约束阻止在 az-idb01 上运行 IBM Db2 实例。 如果不删除位置约束，则资源无法故障回复。

删除位置约束，将在 az-idb01 上启动备用节点。
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
和群集状态更改为：
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit-已删除位置约束](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


将资源迁移回*az-idb01*并清除位置约束
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **电脑资源移动\<res_name> <host>：** 创建位置约束，并可能导致接管问题
- **pc 资源清除\<res_name>**：清除位置约束
- **电脑资源清理\<res_name>**：清除资源的所有错误

### <a name="test-a-manual-takeover"></a>测试手动接管

可以通过停止*az-idb01*节点上的 Pacemaker 服务来测试手动接管：
<pre><code>systemctl stop pacemaker</code></pre>

*az-ibdb02 上的*状态
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

故障转移后，可以在*az-idb01*上重新启动该服务。
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>终止运行 HADR 主数据库的节点上的 Db2 进程

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 实例将失败，Pacemaker 将移动主节点并报告以下状态：

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker 将在同一节点上重新启动 Db2 主数据库实例，或将故障转移到运行辅助数据库实例的节点，并报告错误。

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>终止运行辅助数据库实例的节点上的 Db2 进程

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

节点出现故障，并报告错误
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Db2 实例将在之前分配的辅助角色中重新启动。

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>在运行 HADR 主数据库实例的节点上通过 db2stop 强制停止 DB

作为用户 db2\<sid> 执行命令 db2stop 强制：
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

检测到故障：

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Db2 HADR 辅助数据库实例已升级为主角色。
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>通过 "停止" 将运行 HADR 主数据库实例的 VM 崩溃

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

在这种情况下，Pacemaker 将检测到运行主数据库实例的节点没有响应。

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

下一步是检查*裂脑*情况。 在幸存节点确定最后运行主数据库实例的节点关闭后，将执行资源的故障转移。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


发生内核死机时，故障节点将由防护代理 restared。 故障节点重新联机后，必须通过
<pre><code>sudo pcs cluster start</code></pre> 它将 Db2 实例启动到辅助角色。

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>后续步骤
- [SAP NetWeaver 的高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist