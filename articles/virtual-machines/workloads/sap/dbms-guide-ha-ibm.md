---
title: 在 Azure 虚拟机 （VM） 上设置 IBM Db2 HADR |微软文档
description: 在 Azure 虚拟机 （VM） 上建立 IBM Db2 LUW 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926746"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>使用起搏器的 SUSE Linux 企业服务器上的 Azure VM 上 IBM Db2 LUW 的高可用性

适用于 Linux、UNIX 和 Windows （LUW） 的高[可用性和灾难恢复 （HADR） 配置](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)的 IBM Db2 由运行主数据库实例的一个节点和至少一个运行辅助数据库实例的节点组成。 对主数据库实例的更改会同步或异步复制到辅助数据库实例，具体取决于您的配置。 

本文介绍如何部署和配置 Azure 虚拟机 （VM）、安装群集框架以及使用 HADR 配置安装 IBM Db2 LUW。 

本文不介绍如何使用 HADR 或 SAP 软件安装来安装和配置 IBM Db2 LUW。 为了帮助您完成这些任务，我们提供了 SAP 和 IBM 安装手册的参考。 本文重点介绍特定于 Azure 环境的部件。 

支持的 IBM Db2 版本是 10.5 及更高版本，如 SAP 注释[1928533]中所述。

在开始安装之前，请参阅以下 SAP 说明和文档：

| SAP 注释 | 描述 |
| --- | --- |
| [1928533] | Azure 上的 SAP 应用程序：受支持的产品和 Azure VM 类型 |
| [2015553] | Azure 上的 SAP：支持先决条件 |
| [2178632] | Azure 上 SAP 的关键监视指标 |
| [2191498] | 使用 Azure 在 Linux 上 SAP：增强的监视 |
| [2243692] | Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] | SUSE LINUX Enterprise Server 12：安装说明 |
| [1999351] | 适用于 SAP 的增强型 Azure 监视故障排除 |
| [2233094] | DB6：在 Azure 上使用 IBM Db2 进行 Linux、UNIX 和 Windows 的 SAP 应用程序 - 其他信息 |
| [1612105] | DB6：DB2 上的常见问题解答，带 HADR |


| 文档 | 
| --- |
| [SAP社区维基](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：拥有所有必需的 LINUX SAP 说明 |
| [在 Linux 指南上为 SAP 规划和实现的 Azure 虚拟机][planning-guide] |
| [在 Linux 上为 SAP 部署的 Azure 虚拟机][deployment-guide]（本文） |
| [Azure 虚拟机数据库管理系统 （DBMS） 在 Linux 指南上为 SAP 部署][dbms-guide] |
| [Azure 上的 SAP 工作负荷规划和部署清单][azr-sap-plancheck] |
| [用于 SAP 应用程序的 SUSE Linux 企业服务器 12 SP4 最佳实践指南][sles-for-sap-bp] |
| [SUSE Linux 企业高可用性扩展 12 SP4][sles-ha-guide] |
| [IBM Db2 Azure 虚拟机 DBMS 部署，用于 SAP 工作负荷][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>概述
为了实现高可用性，IBM Db2 LUW 与 HADR 安装在至少两个 Azure 虚拟机上，它们部署在[Azure 可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)或跨[Azure 可用性区域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 

下图显示两个数据库服务器 Azure VM 的设置。 两个数据库服务器 Azure VM 都附加了自己的存储，并且已启动并运行。 在 HADR 中，其中一个 Azure VM 中的一个数据库实例具有主实例的角色。 所有客户端都连接到此主实例。 数据库事务中的所有更改都保存在 Db2 事务日志中本地。 当事务日志记录在本地保留时，记录通过 TCP/IP 传输到第二个数据库服务器、备用服务器或备用实例上的数据库实例。 备用实例通过向前滚动传输的事务日志记录来更新本地数据库。 这样，备用服务器与主服务器保持同步。

HADR 只是一种复制功能。 它没有故障检测，也没有自动接管或故障转移设施。 接管或传输到备用服务器必须由数据库管理员手动启动。 要实现自动接管和故障检测，可以使用 Linux 起搏器聚类功能。 起搏器监视两个数据库服务器实例。 当主数据库服务器实例崩溃时，Pacemaker 会启动备用服务器*的自动*HADR 接管。 起搏器还确保将虚拟 IP 地址分配给新的主服务器。

![IBM Db2 高可用性概述](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

要使 SAP 应用程序服务器连接到主数据库，您需要一个虚拟主机名和一个虚拟 IP 地址。 如果发生故障转移，SAP 应用程序服务器将连接到新的主数据库实例。 在 Azure 环境中，需要[Azure 负载均衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)以 IBM Db2 的 HADR 所需的方式使用虚拟 IP 地址。 

为了帮助您完全理解 IBM Db2 LUW 与 HADR 和起搏器如何适应高可用 SAP 系统设置，下图概述了基于 IBM Db2 数据库的 SAP 系统的高度可用设置。 本文仅介绍 IBM Db2，但它提供了有关如何设置 SAP 系统其他组件的其他文章的参考。

![IBM DB2 高可用性全环境概述](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需步骤的高级概述
要部署 IBM Db2 配置，您需要执行以下步骤：

  + 规划您的环境。
  + 部署 VM。
  + 更新 SUSE Linux 并配置文件系统。
  + 安装和配置起搏器。
  + 安装[高可用 NFS][nfs-ha]。
  + 在[单独的群集上安装 ASCS/ERS。][ascs-ha]
  + 使用分布式/高可用性选项 （SWPM） 安装 IBM Db2 数据库。
  + 安装并创建辅助数据库节点和实例，并配置 HADR。
  + 确认 HADR 工作。
  + 应用起搏器配置来控制 IBM Db2。
  + 配置 Azure 负载均衡器。
  + 安装主应用程序服务器和对话框应用程序服务器。
  + 检查和调整 SAP 应用程序服务器的配置。
  + 执行故障转移和接管测试。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>规划 Azure 基础结构，以便使用 HADR 托管 IBM Db2 LUW

在执行部署之前完成规划过程。 规划为在 Azure 中部署具有 HADR 的 Db2 配置奠定了基础。 下表列出了需要作为 IMB Db2 LUW（SAP 环境数据库部分）规划一部分的关键元素：

| 主题 | 简短说明 |
| --- | --- |
| 定义 Azure 资源组 | 部署 VM、VNet、Azure 负载平衡器和其他资源的资源组。 可以是现有的，也可以是新的。 |
| 虚拟网络/子网定义 | 部署 IBM Db2 和 Azure 负载均衡器的 VM 的位置。 可以现有或新创建。 |
| 托管 IBM Db2 LUW 的虚拟机 | VM 大小、存储、网络、IP 地址。 |
| IBM Db2 数据库的虚拟主机名和虚拟 IP| 用于连接 SAP 应用程序服务器的虚拟 IP 或主机名。 **db-virt-主机名** **db-virt-ip**。 |
| Azure 屏蔽 | Azure 屏蔽或 SBD 屏蔽（强烈建议）。 避免大脑分裂的方法。 |
| SBD VM | SBD 虚拟机大小、存储、网络。 |
| Azure 负载均衡器 | 使用 Db2 数据库的基本或标准（建议）探测端口（我们的建议 62500）**探针端口**。 |
| 名称解析| 名称解析在环境中的工作原理。 强烈建议提供 DNS 服务。 可以使用本地主机文件。 |
    
有关 Azure 中的 Linux 起搏器的详细信息，请参阅[在 Azure 中的 SUSE Linux 企业服务器上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)。

## <a name="deployment-on-suse-linux"></a>在 SUSE Linux 上部署

IBM Db2 LUW 的资源代理包含在 SAP 应用程序的 SUSE Linux 企业服务器中。 对于本文档中描述的设置，必须使用 SAP 应用程序的 SUSE Linux 服务器。 Azure 应用商店包含用于 SAP 应用程序 SUSE 企业服务器 12 的映像，可用于部署新的 Azure 虚拟机。 在 Azure VM 应用商店中选择 VM 映像时，请注意 SUSE 通过 Azure 应用商店提供的各种支持或服务模型。 

### <a name="hosts-dns-updates"></a>主机：DNS 更新
列出所有主机名（包括虚拟主机名），并更新 DNS 服务器以启用正确的 IP 地址以解析主机名。 如果 DNS 服务器不存在，或者您无法更新和创建 DNS 条目，则需要使用参与此方案的各个 VM 的本地主机文件。 如果使用主机文件条目，请确保这些条目已应用于 SAP 系统环境中的所有 VM。 但是，我们建议您使用 DNS，理想情况下，DNS 会扩展到 Azure


### <a name="manual-deployment"></a>手动部署

确保 IBM/SAP 支持 IBM Db2 LUW 的选定操作系统。 Azure VM 和 Db2 版本的受支持操作系统版本列表在 SAP 注释[1928533 中]提供。 各个 Db2 版本的操作系统版本列表在 SAP 产品可用性矩阵中提供。 我们强烈建议至少使用 SLES 12 SP4，因为在此或更高版本的 SUSE Linux 版本中，与 Azure 相关的性能改进。

1. 创建或选择资源组。
1. 创建或选择虚拟网络和子网。
1. 创建 Azure 可用性集或部署可用性区域。
    + 对于可用性集，将最大更新域设置为 2。
1. 创建虚拟机 1。
    + 在 Azure 应用商店中对 SAP 映像使用 SLES。
    + 选择在步骤 3 中创建的 Azure 可用性集，或选择"可用性区域"。
1.  创建虚拟机 2。
    + 在 Azure 应用商店中对 SAP 映像使用 SLES。
    + 选择在步骤 3 中创建的 Azure 可用性集，或选择可用性区域（与步骤 3 中不同的区域）。
1. 将数据磁盘添加到 VM，然后检查文章 IBM [Db2 Azure 虚拟机 DBMS 部署 SAP 工作负载][dbms-db2]中的文件系统设置的建议。

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集
    
要为此 IBM Db2 服务器创建基本起搏器群集，请参阅 [在 Azure 中的 SUSE Linux 企业服务器上设置起搏器][sles-pacemaker]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安装 IBM Db2 LUW 和 SAP 环境

在开始安装基于 IBM Db2 LUW 的 SAP 环境之前，请查看以下文档：

+ Azure 文档
+ SAP 文档
+ IBM 文档

本文档的链接在本文的介绍部分提供。

查看有关在 IBM Db2 LUW 上安装基于 NetWeaver 的应用程序的 SAP 安装手册。

您可以使用[SAP 安装指南查找器][sap-instfind]在 SAP 帮助门户上找到指南。

您可以通过设置以下筛选器来减少门户中显示的参考线数量：

- 我想："安装一个新系统"
- 我的数据库："用于 Linux、Unix 和 Windows 的 IBM Db2"
- 用于 SAP NetWeaver 版本、堆栈配置或操作系统的其他筛选器

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>安装提示，用于使用 HADR 设置 IBM Db2 LUW

要设置主 IBM Db2 LUW 数据库实例，

- 使用高可用性或分布式选项。
- 安装 SAP ASCS/ERS 和数据库实例。
- 备份新安装的数据库。


> [!IMPORTANT] 
> 记下安装期间设置的"数据库通信端口"。 对于两个数据库实例，它必须是相同的端口号

要使用 SAP 同质系统复制过程设置备用数据库服务器，请执行以下步骤：

1. 选择 **"系统复制**选项>**目标系统** > **分布式** > **数据库实例**。
1. 作为复制方法，选择 **"同质系统"，** 以便可以使用备份还原备用服务器实例上的备份。
1. 当您到达用于还原数据库以进行同质系统复制的退出步骤时，请退出安装程序。 从主主机的备份还原数据库。 所有后续安装阶段已在主数据库服务器上执行。
1. 为 IBM Db2 设置 HADR。

   > [!NOTE]
   > 对于特定于 Azure 和起搏器的安装和配置：在通过 SAP 软件配置管理器进行安装过程中，存在关于 IBM Db2 LUW 高可用性的明确问题：
   >+ 不要选择**IBM Db2 纯标额**。
   >+ 不要选择**为多平台安装 IBM Tivoli 系统自动化**。
   >+ 不要选择 **"生成群集配置文件**"。

   当您为 Linux 起搏器使用 SBD 设备时，设置以下 Db2 HADR 参数：
   + HADR 对等窗口持续时间（秒） （HADR_PEER_WINDOW） = 300  
   + HADR 超时值 （HADR_TIMEOUT） = 60

   使用 Azure 起搏器击剑代理时，设置以下参数：
   + HADR 对等窗口持续时间（秒） （HADR_PEER_WINDOW） = 900  
   + HADR 超时值 （HADR_TIMEOUT） = 60

我们建议基于初始故障转移/接管测试的上述参数。 您必须使用这些参数设置测试故障转移和接管的正确功能。 由于单个配置可能有所不同，因此参数可能需要调整。 

> [!IMPORTANT]
> 特定于具有 HADR 配置且具有正常启动的 IBM Db2：辅助数据库或备用数据库实例必须启动并运行，然后才能启动主数据库实例。

为了演示目的和本文中描述的过程，数据库 SID 是**PTR**。

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 哈德哈德检查
配置 HADR 后，状态为"PEER"和"在主节点和备用节点上连接"，请执行以下检查：

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Db2 起搏器配置

在节点发生故障时，使用起搏器进行自动故障转移时，需要相应地配置 Db2 实例和起搏器。 本节介绍这种类型的配置。

以下项以以下任一项为预缀：

- **[A]**： 适用于所有节点
- **[1]**： 仅适用于节点 1 
- **[2]**： 仅适用于节点 2

**[A]** 起搏器配置的先决条件：
1. 使用 db2stop 关闭用户\<db2 sid>两个数据库服务器。
1. 将 db2\<sid>用户的 shell 环境更改为 */bin/ksh*。 我们建议您使用 Yast 工具。 


### <a name="pacemaker-configuration"></a>起搏器配置

> [!IMPORTANT]
> 最近的测试揭示了 Netcat 由于积压和仅处理一个连接的限制而停止响应请求的情况。 netcat 资源停止侦听 Azure 负载均衡器请求，浮动 IP 将不可用。  
> 对于现有的起搏器集群，我们过去曾建议用索卡特取代网猫。 目前，我们建议使用 Azure-lb 资源代理（这是包资源代理的一部分），具有以下包版本要求：
> - 对于 SLES 12 SP4/SP5，版本必须至少为资源代理-4.3.018.a7fb5035-3.30.1。  
> - 对于 SLES 15/15 SP1，版本必须至少为资源代理-4.3.0184.6ee15eb2-4.13.1。  
>
> 请注意，更改将需要短暂的停机时间。  
> 对于现有的起搏器群集，如果配置已更改为使用[Azure 负载均衡器检测强化](https://www.suse.com/support/kb/doc/?id=7024128)中所述的 socat，则无需立即切换到 Azure-lb 资源代理。

**[1]** IBM Db2 特定于 HADR 的起搏器配置：
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** 创建 IBM Db2 资源：
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** 启动 IBM Db2 资源：
* 将起搏器置于维护模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**{1}** 确保群集状态正常，并且所有资源都已启动。 资源运行的节点并不重要。
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>配置了 2 个节点
# <a name="5-resources-configured"></a>配置了 5 个资源

# <a name="online--azibmdb01-azibmdb02-"></a>在线： [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>资源的完整列表：

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>石石石-sbd（石石：外部/sbd）：开始 azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>资源组：g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR（ocf：心跳：IPaddr2）：启动 azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR（ocf：：心跳：azure-lb）：启动 azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>主/从集：msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>大师： [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>奴隶： [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> 您必须使用起搏器工具管理起搏器群集 Db2 实例。 如果使用 db2 命令（如 db2stop），Pacemaker 会将该操作检测为资源故障。 如果执行维护，可以将节点或资源置于维护模式。 起搏器将暂停监视资源，然后您可以使用正常的 db2 管理命令。


### <a name="configure-azure-load-balancer"></a>配置 Azure 负载均衡器
要配置 Azure 负载均衡器，我们建议您使用 Azure 标准负载均衡器 SKU，然后执行以下操作;因此，请使用[Azure 标准负载均衡器 SKU。](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

> [!NOTE]
> 标准负载均衡器 SKU 具有从负载均衡器下方的节点访问公共 IP 地址的限制。 在[SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)文章介绍了如何启用这些节点访问公共 IP 地址的方法

1. 创建前端 IP 池：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 门户中，打开 Azure 负载均衡器，选择**前端 IP 池**，然后选择 **"添加**"。

   b.保留“数据库类型”设置，即设置为“共享”。 输入新的前端 IP 池的名称（例如 **，Db2 连接**）。

   c. 将**分配**设置为**静态**，并输入在开头定义的 IP 地址**虚拟 IP。**

   d.单击“下一步”。 选择“确定”。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 创建新前端 IP 池后，请记下池 IP 地址。

1. 创建后端池：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 门户中，打开 Azure 负载均衡器，选择**后端池**，然后选择 **"添加**"。

   b.保留“数据库类型”设置，即设置为“共享”。 输入新后端池的名称（例如 **，Db2 后端**）。

   c. 选择“添加虚拟机”。****

   d.单击“下一步”。 选择上一步中创建的可用性集或承载 IBM Db2 数据库的虚拟机。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择 IBM Db2 群集的虚拟机。

   f. 选择“确定”。

1. 创建运行状况探测：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 门户中，打开 Azure 负载均衡器，选择**运行状况探测**，然后选择 **"添加**"。

   b.保留“数据库类型”设置，即设置为“共享”。 输入新运行状况探测的名称（例如 **，Db2-hp**）。

   c. 选择**TCP**作为协议和端口**62500**。 将**间隔**值设置为**5**，并将**不正常阈值**设置为**2**。

   d.单击“下一步”。 选择“确定”。

1. 创建负载平衡规则：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 门户中，打开 Azure 负载平衡器，选择 **"负载平衡规则**"，然后选择 **"添加**"。

   b.保留“数据库类型”设置，即设置为“共享”。 输入新的负载均衡器规则的名称（例如 **，Db2-SID）。**

   c. 选择前端 IP 地址、后端池和您之前创建的运行状况探测（例如 **，Db2 前端**）。

   d.单击“下一步”。 将**协议**设置为**TCP**，然后输入端口*数据库通信端口*。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 将“空闲超时”增大到 30 分钟。****

   f. 确保**启用浮动 IP**。

   g. 选择“确定”。


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>更改 SAP 配置文件以使用虚拟 IP 进行连接
要连接到 HADR 配置的主实例，SAP 应用程序层需要使用为 Azure 负载均衡器定义和配置的虚拟 IP 地址。 需要进行以下更改：

/sapmnt/SID\<>/配置文件/DEFAULT。人阵
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/全局/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安装主应用程序服务器和对话框应用程序服务器

安装针对 Db2 HADR 配置的主应用程序服务器和对话框应用程序服务器时，请使用为配置选择的虚拟主机名。 

如果在创建 Db2 HADR 配置之前执行了安装，则按照上一节所述进行更改，如下如下 SAP Java 堆栈。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP_Java 或 Java 堆栈系统 JDBC URL 检查

使用 J2EE 配置工具检查或更新 JDBC URL。 由于 J2EE 配置工具是一个图形工具，因此您需要安装 X 服务器：
 
1. 登录到 J2EE 实例的主应用程序服务器并执行：`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. 在左侧框架中，选择**安全存储**。
1. 在正确的框架中，选择键 jdbc/池/SAPSID\<>/url。
1. 将 JDBC URL 中的主机名更改为虚拟主机名。
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. 选择“添加”****。
1. 要保存更改，请选择左上角的磁盘图标。
1. 关闭配置工具。
1. 重新启动 Java 实例。

## <a name="configure-log-archiving-for-hadr-setup"></a>为 HADR 设置配置日志存档
要为 HADR 设置配置 Db2 日志存档，我们建议您将主数据库和备用数据库配置为具有来自所有日志存档位置的自动日志检索功能。 主数据库和备用数据库都必须能够从数据库实例之一可能存档日志文件的所有日志存档位置检索日志文件。 

日志存档仅由主数据库执行。 如果更改数据库服务器的 HADR 角色，或者发生故障，则新的主数据库负责日志存档。 如果设置了多个日志存档位置，则日志可能会存档两次。 如果发生本地或远程追赶，您可能还必须手动将存档的日志从旧主服务器复制到新主服务器的活动日志位置。

我们建议配置从两个节点写入日志的通用 NFS 共享。 NFS 共享必须高度可用。 

您可以将现有的高可用 NFS 共享用于传输或配置文件目录。 有关详细信息，请参阅：

- [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性][nfs-ha] 
- [SUSE Linux 企业服务器上的 AZURE VM 上的 SAP NetWeaver 的高可用性，具有用于 SAP 应用程序的 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)（创建 NFS 共享）


## <a name="test-the-cluster-setup"></a>测试群集设

本节介绍如何测试 Db2 HADR 设置。 *每个测试都假定您以用户根身份登录*，IBM Db2 主数据库在*azibmdb01*虚拟机上运行。

此处说明了所有测试用例的初始状态：（crm_mon -r 或 crm 状态）

- **crm 状态**是起搏器执行时状态的快照 
- **crm_mon -r**是起搏器状态的连续输出

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

SAP 系统中的原始状态记录在事务 DBACOCKPIT > 配置>概述中，如下图所示：

![DBACockpit - 迁移前](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>测试 IBM Db2 的接管


> [!IMPORTANT] 
> 在开始测试之前，请确保：
> * 起搏器没有任何失败操作（crm 状态）。
> * 没有位置约束（迁移测试的剩下）
> * IBM Db2 HADR 同步工作。 请与用户 db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


通过执行以下命令迁移运行主 Db2 数据库的节点：
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

迁移完成后，crm 状态输出如下所示：
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

SAP 系统中的原始状态记录在事务 DBACOCKPIT > 配置>概述中，如下图所示：

![DBACockpit - 迁移后](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

使用"crm 资源迁移"的资源迁移会创建位置约束。 应删除位置约束。 如果未删除位置约束，则资源无法故障恢复，或者您可能会遇到不需要的接管。 

将资源迁移回*azibmdb01*并清除位置约束
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **crm 资源\<迁移res_name>\<主机>：** 创建位置约束，并可能导致收购问题
- **crm 资源\<清除res_name>：** 清除位置约束
- **crm 资源\<清理res_name>：** 清除资源的所有错误

### <a name="test-the-fencing-agent"></a>测试击剑代理

在这种情况下，我们测试 SBD 屏蔽，我们建议您在使用 SUSE Linux 时执行此操作。

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

应重新启动群集节点*azibmdb01。* IBM Db2 主 HADR 角色将移动到*azibmdb02*。 当*azibmdb01*重新联机时，Db2 实例将移动到辅助数据库实例的角色中。 

如果起搏器服务未在重新启动的前主数据库上自动启动，请确保手动启动它：

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>测试手动接管

您可以通过在*azibmdb01*节点上停止起搏器服务来测试手动接管：
<pre><code>service pacemaker stop</code></pre>

*azibmdb02*上的状态
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

故障转移后，您可以在*azibmdb01*上再次启动服务。
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>在运行 HADR 主数据库的节点上终止 Db2 进程

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Db2 实例将失败，起搏器将报告以下状态：

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker 将重新启动同一节点上的 Db2 主数据库实例，否则它将故障转移到运行辅助数据库实例的节点，并报告错误。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>在运行辅助数据库实例的节点上终止 Db2 进程

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

节点进入未遂声明和报告错误
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 实例在以前分配的辅助角色中重新启动。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>通过运行 HADR 主数据库实例的节点上的 db2stop 力停止数据库

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

作为用户 db2\<sid>执行命令 db2stop 力：
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

检测到故障
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR 辅助数据库实例已提升为主角色
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>在运行 HADR 主数据库实例的节点上重新启动 VM 时崩溃

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

起搏器将辅助实例提升为主要实例角色。 旧主实例将在 VM 之后进入辅助角色，并且所有服务在 VM 重新启动后全部还原：

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>将运行 HADR 主数据库实例的 VM 与"halt"崩溃

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

在这种情况下，Pacemaker 将检测运行主数据库实例的节点未响应。

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

下一步是检查*分裂大脑*的情况。 在幸存的节点确定上次运行主数据库实例的节点已关闭后，将执行资源故障转移。
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


如果节点"停止"，必须通过 Azure 管理工具（在 Azure 门户、PowerShell 或 Azure CLI）重新启动故障节点。 故障节点重新联机后，它将 Db2 实例启动到辅助角色中。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>后续步骤
- [SAP NetWeaver 的高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [在 Azure 中的 SUSE Linux 企业服务器上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

