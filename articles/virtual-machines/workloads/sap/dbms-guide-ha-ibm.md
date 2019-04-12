---
title: 设置 Azure 虚拟机 (Vm) 上的 IBM Db2 HADR |Microsoft Docs
description: 建立 IBM Db2 LUW Azure 虚拟机 (Vm) 上的高的可用性。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: d47c64fb8b148e39eab09a33c3241ac7dd3ea537
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502123"
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>IBM Db2 LUW 上的 Azure Vm 上 SUSE Linux Enterprise Server 与 Pacemaker 的高可用性

IBM Db2 LUW （Linux、 Unix 和 Windows） 中[HADR 配置](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)运行主数据库实例的一个节点和至少一个运行的辅助数据库实例的节点组成。 更改到主数据库实例获取复制到辅助数据库实例以同步方式还是以异步方式取决于你的配置。 

本文介绍如何部署和配置虚拟机、 安装群集框架，并安装和配置 IBM Db2 LUW HADR 配置中。 本文将说明如何安装和配置 IBM Db2 LUW HADR 或 SAP 软件安装中。 提供了对 SAP 和 IBM 安装手册的引用来完成这些任务。 重点是特定于 Azure 环境的部分。 

支持的 IBM Db2 版本为 10.5 和 SAP 说明 # 中所述，更高版本[1928533]。

阅读以下 SAP 说明和文档，首先再接近安装：

| SAP 说明 | 描述 |
| --- | --- |
| [1928533] | Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] | Microsoft Azure 上的 SAP：支持先决条件 |
| [2178632] | Microsoft Azure 上的 SAP 关键监视度量值 |
| [2191498] | Azure 的 Linux 上的 SAP：增强型监视 |
| [2243692] | Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2233094] |DB6：使用 IBM Db2 for Linux、 UNIX 和 Windows 的其他信息的 Azure 上的 SAP 应用程序 |
| [1612105] |DB6：Db2 高可用性灾难恢复 (HADR) 的常见问题解答 |


| 文档 | 
| --- |
| [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)具有所有所需的 SAP 说明适用于 Linux |
| [Azure 虚拟机规划和实施适用于 Linux 上的 SAP] [ planning-guide]指南 |
| [适用于 Linux 上的 SAP 的 azure 虚拟机部署][ deployment-guide] （详见本文） |
| [适用于 Linux 上的 SAP 的 azure 虚拟机 DBMS 部署][ dbms-guide]指南 |
| [Azure 规划和部署清单上的 SAP 工作负荷][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server for SAP Applications 12 SP3 最佳做法指南][sles-for-sap-bp] |
| [SUSE Linux 企业高可用性扩展 12 SP3][sles-ha-guide] |
| [SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2] |
| [IBM Db2 高可用性灾难恢复 11.1][db2-hadr-11.1] |
| [IBM Db2 高可用性灾难恢复 R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>概述
若要实现高可用性，使用 HADR 的 IBM Db2 LUW 安装在至少两台 Azure 虚拟机上，在部署[Azure 可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)或跨[Azure 可用性区域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 下图显示了两个数据库服务器的 Azure Vm 的设置。 这两个数据库服务器的 Azure Vm 具有其自己的存储附加和已启动并运行。 在 HADR，一个 Azure Vm 中的一个数据库实例具有主实例的角色。 所有客户端连接到此主实例。 数据库事务中的所有更改都保留在本地，Db2 事务日志中。 因为事务日志记录将保留在本地，记录传输通过 TCP/IP 到的数据库实例上的第二个数据库服务器、 备用服务器或备用实例。 备用实例通过前滚传输的事务日志记录来更新本地数据库。 因此将备用服务器保持与主服务器同步。

HADR，仅复制功能。 它具有无故障检测和没有自动接管或故障转移功能。 接管或传输到备用必须手动启动由数据库管理员。 若要实现自动接管和故障检测，可以使用 Linux Pacemaker 群集功能。 Pacemaker 监视两个数据库的服务器/实例。 主数据库服务器/实例何时发生故障，将启动 Pacemaker**自动**HADR 接管通过备用服务器并且还可确保虚拟 IP 地址分配给新的主服务器。

![IBM Db2 高可用性概述](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

适用于 SAP 应用程序服务器连接到主数据库需要虚拟主机名和虚拟 IP 地址。 在故障转移的情况下，SAP 应用程序服务器将连接到新的主数据库实例。 在 Azure 环境中， [Azure 负载均衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)需要在所需的 HADR 的 IBM Db2 的方法中使用的虚拟 IP 地址。 

若要完全了解，如何使用 HADR 和 Pacemaker 的 IBM Db2 LUW 适用于高度可用的 SAP 系统设置下, 图显示 SAP 系统基于 IBM Db2 数据库的高度可用设置的概述。 本文介绍了仅 IBM Db2 和引用的其他文章如何设置 SAP 系统的其他组件。

![IBM DB2 HA 完整环境概述](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>所需步骤的高级概述
若要部署的 IBM Db2 配置，需要涉及以下步骤：

  + 规划环境
  + 部署 Vm
  + 更新 SUSE Linux 以及配置文件系统
  + 安装和配置 Pacemaker
  + 安装[高度可用的 NFS][nfs-ha]
  + 安装[单独的群集上的 ASCS/ERS][ascs-ha] 
  + 使用分布式/高可用性选项 (SWPM) 安装 IBM Db2 数据库
  + 安装/创建辅助数据库节点和实例，并配置 HADR
  + 确认工作 HADR
  + Pacemaker 配置应用到控件 IBM Db2
  + 配置 Azure 负载均衡器 
  + 安装主 + 对话框应用程序服务器
  + 检查/调整 SAP 应用程序服务器配置
  + 执行故障转移 / 测试接管



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>规划用于托管使用 HADR 的 IBM Db2 LUW 的 Azure 基础结构

通过进行计划的执行部署之前，请转。 它构建部署的使用在 Azure 中的 HADR Db2 配置的基础。 需要为 IMB Db2 LUW （SAP 环境的数据库部分） 规划的一部分的关键元素。

| 主题 | 简短说明 |
| --- | --- |
| 定义 Azure 资源组 | 在其中部署 VM、 VNet、 Azure 负载均衡器和其他资源的资源组。 可以是现有或新 |
| 虚拟网络 / 子网的定义 | 从何处 IBM Db2 和 Azure 负载均衡器的 Vm 获取要部署。 可以是现有或新创建 |
| 托管 IBM Db2 LUW 的虚拟机 | VM 大小、 存储、 网络、 IP 地址 |
| 虚拟主机名和虚拟 IP 为 IBM Db2 数据库| 虚拟 IP/主机名，用于 SAP 应用程序服务器的连接。 **db-virt-hostname**, **db-virt-ip** |
| Azure 的隔离 | Azure 隔离或 SBD 隔离 （强烈建议）。 方法可避免拆分脑情况下阻止 |
| SBD VM | SBD 虚拟机大小、 存储、 网络 |
| Azure 负载均衡器 | 使用情况的基本或标准 （推荐），探测端口为 Db2 数据库 （我们建议 62500）**探测端口** |
| 名称解析| 名称解析的工作原理在环境中。 强烈建议 DNS 服务。 可以使用本地主机文件 |
    
在 Azure 中 Linux Pacemaker 的使用情况的更多详细信息可在这些文章中找到：

- [在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>SUSE Linux 上的部署

IBM Db2 LUW 的资源代理包含在 SUSE Linux Enterprise Server for SAP 应用程序。 对于本文档中所述的设置，它是必需的 SAP 应用程序使用 SUSE Linux 服务器。 Azure Marketplace 包含一个图像适用于 SUSE Enterprise Server for SAP Applications 12 的可用于部署新的 Azure 虚拟机。 请注意对 Azure 库通过提供由 SUSE，在 Azure VM 库中选择 VM 映像时的不同的支持服务模型。 

### <a name="hosts---dns-updates"></a>主机的 DNS 更新
列出的所有主机名，包括虚拟的主机名列表并更新你的 DNS 服务器来启用主机名解析为正确的 IP 地址。 在示例中，DNS 服务器不存在或您不能更新和创建 DNS 条目，则需要利用参与此方案中的单个 Vm 的本地主机文件。 如果使用的主机文件条目，则需要确保条目将应用到 SAP 系统环境中的所有 Vm。 但是，建议是使用你在理想情况下扩展到 Azure 的 DNS


### <a name="manual-deployment"></a>手动部署

请确保为 IBM Db2 LUW 所选的操作系统支持通过 IBM/SAP。 SAP 说明中提供了支持的操作系统版本对于 Azure Vm 和 Db2 版本的列表[1928533]。 在 SAP 产品可用性对照表中提供了单个 Db2 发行版本的操作系统版本的列表。 我们强烈建议在此或更高版本的 Azure 相关的性能改进引起的 SLES 12 SP3 至少 SUSE Linux 版本。

1. 创建/选择资源组
2. 创建/选择虚拟网络和子网
3. 创建 Azure 可用性集或在可用性区域中部署
    + 设置-设置为 2 的最大更新域的可用性
4. 创建虚拟机 1。
    + 为 Azure 库中的 SAP 图像使用 SLES
    + 选择 Azure 可用性集中创建在步骤 3 或选择可用性区域中
5.  创建虚拟机 2。
    + 为 Azure 库中的 SAP 图像使用 SLES
    + 选择 Azure 可用性集创建在步骤 3 中。 或选择不如步骤 3 中所示的相同区域的可用性区域的。
6. 将数据磁盘添加到 Vm-请检查文件系统安装程序一文中的建议[SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集
    
按照中的步骤 [在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker][sles-pacemaker] 若要创建此 IBM Db2 服务器的一个基本 Pacemaker 群集。 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>安装 IBM Db2 LUW 和 SAP 环境

在开始安装基于 IBM Db2 LUW 上的 SAP 环境之前，请查看 （在本文开头提供的链接）：

+ Azure 文档
+ SAP 文档
+ IBM 文档

检查从 SAP NetWeaver 应用程序安装在 IBM Db2 LUW 上安装手册。

您可以查找在 SAP 帮助门户使用的指南[SAP 安装指南查找器][sap-instfind]

您可以筛选搜索来减少的指南适用于设置筛选器数：

+ 我想："安装新系统"
+ 我的数据库："IBM Db2 for Linux、 Unix 和 Windows"
+ 为 SAP Netweaver 版本、 堆栈配置或操作系统的其他筛选器。

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>设置使用 HADR 的 IBM Db2 LUW 安装提示

设置主 IBM Db2 LUW 数据库实例：

- 使用高可用性或分布式的选项
- 安装 SAP ASCS/ERS 和数据库实例
- 执行新安装的数据库的备份


> [!IMPORTANT] 
> 记下在安装过程中设置的"数据库通信端口"。 它必须是相同的端口号为两个数据库实例

对于使用 SAP 的同构系统复制过程将备用数据库服务器设置，执行以下步骤：

  - 使用系统复制选项-目标系统上分散的数据库实例。
  - 为复制方法，请选择同构系统复制，以便可以使用备份来还原备用的服务器实例上的备份
  - 到达退出步骤同构系统复制将数据库还原后，退出安装程序。 从主主机的备份还原数据库。 已在主数据库服务器上执行所有后续的安装阶段
- 对于 IBM Db2 设置 HADR

> [!NOTE]
> 安装/配置特定于 Azure 和 Pacemaker。 在通过 SAP Software Provisioning Manager 安装过程中，还有一个显式的问题上的 IBM Db2 LUW 的高可用性：
>+ 不要选择 IBM Db2 pureScale
>+ 不要选择"安装 IBM Tivoli 系统自动化的 Multiplatforms
>+ 不要选择"生成群集配置文件"

> [!NOTE]
>当使用 SBD 设备进行 Linux Pacemaker，设置 Db2 HADR 参数
>+ HADR 对等方窗口持续时间 （秒） (HADR_PEER_WINDOW) = 300  
>+ HADR 超时值 (HADR_TIMEOUT) = 60

> [!NOTE]
>使用 Azure Pacemaker 隔离代理：
>+ HADR 对等方窗口持续时间 （秒） (HADR_PEER_WINDOW) = 900  
>+ HADR 超时值 (HADR_TIMEOUT) = 60

参数建议使用基于初始接管故障转移/测试。 是必须要测试的特有功能的接管和使用这些参数设置的故障转移。 单独的配置可能会有所不同，因为这些参数可能需要进行调整。 

> [!IMPORTANT]
> 对于 IBM Db2 使用正常启动的辅助/备用数据库实例的 HADR 配置中的特定于必须先启动并运行可以启动主数据库实例。

对于演示目的，本文档中所述的过程，数据库 SID 是**PTR**。

##### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR Check
一次配置 HADR，状态为主要和备用节点上的对等方和已连接。

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



## <a name="db2-pacemaker-configuration"></a>Db2 Pacemaker 配置

发生节点故障时自动故障转移中使用 Pacemaker 时，需要相应地配置您的 Db2 实例和 Pacemaker。 本部分将介绍此类型的配置。

以下各项带有前缀：

- **[A]** -适用于所有节点
- **[1]** -仅适用于节点 1 
- **[2]** -仅适用于节点 2。

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]** 系统 Pacemaker 配置必备组件：
1. 关闭这两个数据库服务器与用户 db2\<sid > 与 db2stop
2. 更改 shell 环境的 db2\<sid >"/ bin/ksh"的用户，建议使用 Yast 工具 


### <a name="pacemaker-configuration"></a>Pacemaker 配置：

**[1]** IBM Db2 HADR 特定 Pacemaker 配置
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** 创建 IBM Db2 资源
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** 启动 IBM Db2 资源-Pacemaker 将退出维护模式
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** 确保群集状态为确定，启动的所有资源。 资源在哪个节点上运行并不重要。
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 个节点配置
# <a name="5-resources-configured"></a>5 配置的资源

# <a name="online--azibmdb01-azibmdb02-"></a>联机: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>资源的完整列表：

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd):已启动的 azibmdb02
#  <a name="resource-group-gipdb2ptrptr"></a>资源组： g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):     已启动的 azibmdb02
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat： 任何内容):    已启动的 azibmdb02
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>主/从集： msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>主机: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>从属服务: [azibmdb01]
</pre>

> [!IMPORTANT]
> 管理的 Pacemaker 群集的 Db2 实例必须通过 Pacemaker 工具完成。 使用 db2 命令 （如 db2stop) 将会检测到 Pacemaker 作为资源的故障。 对于维护，可以将节点或资源放到维护模式 Pacemaker 将挂起监视资源，可以使用正常 db2 管理命令。


### <a name="configure-azure-load-balancer"></a>配置 Azure 负载均衡器
建议使用[Azure 标准负载均衡器 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)。

1. 配置 Azure 负载均衡器 （通过 Azure 门户）。 首先创建前端 IP 池：

   1. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**前端 IP 池**，然后选择**添加**。
   2. 输入新前端 IP 池的名称 (例如， **Db2 连接**)。
   3. 设置**赋值**到**静态**并输入 IP 地址**虚拟 IP**开始时定义。
   4. 选择“确定”。
   5. 创建新前端 IP 池后，请记下池 IP 地址。

2. 下一步是创建后端池：

   1. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**后端池**，然后选择**添加**。
   2. 输入新后端池的名称 (例如， **Db2 后端**)。
   3. 选择“添加虚拟机”。
   4. 选择承载在步骤 3 中创建的 IBM Db2 数据库的可用性组/虚拟机。
   5. 选择 IBM Db2 群集的虚拟机。
   6. 选择“确定”。

3. 第三步是创建运行状况探测：

   1. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**运行状况探测**，然后选择**添加**。
   2. 输入新的运行状况探测的名称 (例如， **Db2 hp**)。
   3. 选择**TCP**作为协议和端口**62500**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。
   4. 选择“确定”。

4. 创建负载均衡规则：

   1. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**负载均衡规则**，然后选择**添加**。
   2. 输入新负载均衡器规则的名称 (例如， **Db2 SID**)。
   3. 选择的前端 IP 地址、 后端池和之前创建的运行状况探测 (例如， **Db2 前端**)。
   4. 保持**协议**设置为**TCP**，并输入端口*数据库通信端口*。
   5. 将“空闲超时”增大到 30 分钟。
   6. 确保**启用浮动 IP**。
   7. 选择“确定”。


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>对要用于连接的虚拟 IP 的 SAP 配置文件进行更改
SAP 应用程序层需要使用您定义和配置 Azure 负载均衡器连接到主实例的 HADR 配置的虚拟 IP 地址。 以下更改是必需的。

/sapmnt/\<SID >/配置文件/默认值。PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安装主和对话框的应用程序服务器

当安装的主服务器和针对 Db2 HADR 配置的对话框应用程序服务器，您应使用你选取的虚拟主机名进行配置。 

如果您执行安装，然后创建 Db2 HADR 配置，需要进行更改，如所述，如下所示在上一段和 SAP Java 堆栈。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 或 Java 堆栈系统 JDBC URL 检查

J2EE 配置工具用于检查或更新 JDBC URL。 J2EE 配置工具的图形工具，因此需要**X 服务器**安装：
 
1. 登录到主应用程序服务器的 J2EE 实例并执行：
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. 在左框架中，选择安全存储区。
2. 在右栏中，选择每个池的 jdbc密钥/<SAPSID>/url。
2. 将 JDBC URL 中的主机名更改为虚拟主机名 <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. 选择添加。
5. 若要保存所做的更改，请单击左上角的磁盘图标。
5. 关闭配置工具。
5. 重新启动 Java 实例。

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>日志存档 HADR 安装程序的配置
若要配置的 Db2 日志存档 HADR 安装程序，我们建议您配置主要和备用数据库能够自动日志检索功能中所有日志存档位置。 主要和备用数据库必须能够从哪些其中任何一数据库的实例可能存档日志文件的所有日志存档位置检索日志存档文件。 

主数据库仅执行日志存档。 如果您更改的数据库服务器的 HADR 角色或新的主数据库发生故障，如果将负责日志存档。 如果已设置了不同的日志存档位置，可能会两次存档日志，并在本地或远程弥补的情况下，您可能需要手动将已存档的日志从旧的主服务器复制到新的主服务器的活动的日志位置。

我们建议将配置从这两个节点写入日志的位置的公用 NFS 共享。 NFS 必须高度可用。 

可以使用现有高度可用的 NFS 用于传输，配置文件目录。 读取：

- [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性][nfs-ha] 
- [使用 Azure NetApp 文件的 SAP 应用程序的 SUSE Linux Enterprise Server 上的 Azure Vm 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)有关如何使用[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)创建 NFS 共享


## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试 Db2 HADR 设置。 **每个测试都假设，已经以用户根身份登录**，并运行在 IBM Db2 主**azibmdb01**虚拟机。

此处所述的所有测试用例的初始状态: (crm_mon-r 或 crm status)

- **crm 状态**是在执行时的快照 Pacemaker 状态 
- **crm_mon r**是连续的 Pacemaker 状态输出

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

事务 DBACOCKPIT 中记录了 SAP 系统中的原始状态-配置->--> 如概述：

![DBACockpit - Pre Migration](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>测试 takeover 的 IBM Db2。


> [!IMPORTANT] 
> 在开始测试之前，请确保 Pacemaker 不具有任何失败的操作 (crm status) 和没有任何位置约束 （剩余项的迁移测试），并 IBM Db2 HADR 同步正常工作。 请使用与用户 db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


迁移通过执行以下命令运行主 Db2 数据库的节点：
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

完成迁移后，crm 状态输出如下所示：
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

事务 DBACOCKPIT 中记录了 SAP 系统中的原始状态-配置->--> 如概述：![DBACockpit-迁移后](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

使用"crm 资源迁移"的资源迁移过程将创建位置约束。 应删除位置约束。 如果未删除位置约束，该资源不能故障回复，或你可能会遇到不需要的接管。 

将资源迁移回**azibmdb01**并清除位置约束
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- crm 资源迁移 < res_name > <host> -创建位置约束，并可能导致接管出现问题
- crm 资源清除 < res_name >-清除位置约束
- crm 资源清理 < res_name >-清除所有错误的资源


### <a name="test-the-fencing-agent"></a>测试隔离代理

在这种情况下，我们将测试 SBD 隔离，建议使用 SUSE Linux 使用。

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

群集节点**azibmdb01**应重新启动。 IBM Db2 主 HADR 角色要移至**azibmdb02**。 当**azibmdb01**是重新联机，的 Db2 实例会在辅助数据库实例的角色中移动。 

为 Pacemaker 服务不会重新启动在先前的主要自动启动的情况下，请确保手动开头：

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>测试手动接管

可以通过停止上的 Pacemaker 服务来测试手动接管**azibmdb01**节点：
<pre><code>service pacemaker stop</code></pre>

status on **azibmdb02**
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

故障转移后，您可以再次启动服务上**azibmdb01**。
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>终止运行 HADR 主数据库的节点上的 Db2 进程

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

将 Db2 实例失败，Pacemaker 将报告以下状态：

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms


</code></pre>

Pacemaker 将重新启动同一节点或故障转移到运行二次实例化的辅助数据库的节点上的 Db2 主数据库实例，报告的错误。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>终止运行的辅助数据库实例的节点上的 Db2 进程

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

节点获取到没有规定并报告错误
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Db2 实例获取的重新启动到其分配之前在辅助角色

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>通过运行 HADR 主数据库实例的节点上的 db2stop 强制停止 DB

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

作为用户 db2\<sid > 执行命令 db2stop 强制：
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
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Db2 HADR 辅助数据库实例得到提升为主要角色
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>崩溃，重新启动运行 HADR 主数据库实例的节点上的 VM

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker 将升级到主实例角色的辅助实例。 旧的主实例会将移动到辅助角色 VM 后，所有服务均被完全都还原后重新启动 VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>崩溃运行具有"halt"的 HADR 主数据库实例的 VM

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

在这种情况下，Pacemaker 将检测到未响应运行的主数据库实例的节点。

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

下一步是检查**裂脑**情况。 确保节点，最后一次运行的主数据库实例，向下工作正常的节点后，将要执行资源的故障转移
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


在"停止"节点的情况下发生故障的节点必须通过 Azure 管理工具 (门户中，因故，azure Cli，...) 重新启动。将出现故障的节点重新联机后启动到辅助角色的 Db2 实例。

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>后续步骤
请参考此文档：

- [SAP NetWeaver 的高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [上设置 Pacemaker SUSE Linux 输入
- pri
- se 服务器在 Azure 中] (https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

