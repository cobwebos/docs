---
title: 在 RHEL 上的 Azure 虚拟机 （VM） 上设置 IBM Db2 HADR |微软文档
description: 在 Azure 虚拟机 （VM） RHEL 上建立 IBM Db2 LUW 的高可用性。
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
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598691"
---
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



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server 上 Azure VM 中 IBM Db2 LUW 的高可用性

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
| [2002167] | Red Hat Enterprise Linux 7.x：安装和升级 |
| [2694118] | 红帽企业 Linux HA 附加组件在 Azure 上 |
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
| [红帽企业 Linux 7 的高可用性附加组件概述][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin]（高可用性附加产品管理） |
| [High Availability Add-On 参考][rhel-ha-ref] |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp]（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst]（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
| [IBM Db2 Azure 虚拟机 DBMS 部署，用于 SAP 工作负荷][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [RHEL 高可用性群集的支持策略 - 群集中 Linux、Unix 和 Windows 的 IBM Db2 管理][rhel-db2-supp]



## <a name="overview"></a>概述
为了实现高可用性，IBM Db2 LUW 与 HADR 安装在至少两个 Azure 虚拟机上，它们部署在[Azure 可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)或跨[Azure 可用性区域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 

下图显示两个数据库服务器 Azure VM 的设置。 两个数据库服务器 Azure VM 都附加了自己的存储，并且已启动并运行。 在 HADR 中，其中一个 Azure VM 中的一个数据库实例具有主实例的角色。 所有客户端都连接到主实例。 数据库事务中的所有更改都保存在 Db2 事务日志中本地。 当事务日志记录在本地保留时，记录通过 TCP/IP 传输到第二个数据库服务器、备用服务器或备用实例上的数据库实例。 备用实例通过向前滚动传输的事务日志记录来更新本地数据库。 这样，备用服务器与主服务器保持同步。

HADR 只是一种复制功能。 它没有故障检测，也没有自动接管或故障转移设施。 接管或传输到备用服务器必须由数据库管理员手动启动。 要实现自动接管和故障检测，可以使用 Linux 起搏器聚类功能。 起搏器监视两个数据库服务器实例。 当主数据库服务器实例崩溃时，Pacemaker 会启动备用服务器*的自动*HADR 接管。 起搏器还确保将虚拟 IP 地址分配给新的主服务器。

![IBM Db2 高可用性概述](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

要使 SAP 应用程序服务器连接到主数据库，您需要一个虚拟主机名和一个虚拟 IP 地址。 如果发生故障转移，SAP 应用程序服务器将连接到新的主数据库实例。 在 Azure 环境中，需要[Azure 负载均衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)以 IBM Db2 的 HADR 所需的方式使用虚拟 IP 地址。 

为了帮助您完全理解 IBM Db2 LUW 与 HADR 和起搏器如何适应高可用 SAP 系统设置，下图概述了基于 IBM Db2 数据库的 SAP 系统的高度可用设置。 本文仅介绍 IBM Db2，但它提供了有关如何设置 SAP 系统其他组件的其他文章的参考。

![IBM DB2 高可用性全环境概述](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需步骤的高级概述
要部署 IBM Db2 配置，您需要执行以下步骤：

  + 规划您的环境。
  + 部署 VM。
  + 更新 RHEL Linux 并配置文件系统。
  + 安装和配置起搏器。
  + 设置[glusterfs 群集][glusterfs]或[Azure NetApp 文件][anf-rhel]
  + 在[单独的群集上安装 ASCS/ERS。][ascs-ha-rhel]
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
| Azure 屏蔽 | 防止了避免大脑分裂的方法。 |
| Azure 负载均衡器 | 使用 Db2 数据库的基本或标准（建议）探测端口（我们的建议 62500）**探针端口**。 |
| 名称解析| 名称解析在环境中的工作原理。 强烈建议提供 DNS 服务。 可以使用本地主机文件。 |
    
有关 Azure 中的 Linux 起搏器的详细信息，请参阅[在 Azure 中的红帽企业 Linux 上设置起搏器][rhel-pcs-azr]。

## <a name="deployment-on-red-hat-enterprise-linux"></a>在红帽企业 Linux 上部署

IBM Db2 LUW 的资源代理包含在红帽企业 Linux 服务器 HA 加载项中。 对于本文档中描述的设置，应使用红帽企业 Linux 进行 SAP。 Azure 应用商店包含用于 SAP 或更高版本红帽企业 Linux 7.4 的图像，可用于部署新的 Azure 虚拟机。 在 Azure VM 应用商店中选择 VM 映像时，请注意红帽通过 Azure 应用商店提供的各种支持或服务模型。

### <a name="hosts-dns-updates"></a>主机：DNS 更新
列出所有主机名（包括虚拟主机名），并更新 DNS 服务器以启用正确的 IP 地址以解析主机名。 如果 DNS 服务器不存在，或者您无法更新和创建 DNS 条目，则需要使用参与此方案的各个 VM 的本地主机文件。 如果使用主机文件条目，请确保这些条目已应用于 SAP 系统环境中的所有 VM。 但是，我们建议您使用 DNS，理想情况下，DNS 会扩展到 Azure


### <a name="manual-deployment"></a>手动部署

确保 IBM/SAP 支持 IBM Db2 LUW 的选定操作系统。 Azure VM 和 Db2 版本的受支持操作系统版本列表在 SAP 注释[1928533 中]提供。 各个 Db2 版本的操作系统版本列表在 SAP 产品可用性矩阵中提供。 我们强烈建议 SAP 至少使用红帽企业 Linux 7.4，因为在此或更高版本中与 Azure 相关的性能改进。

1. 创建或选择资源组。
1. 创建或选择虚拟网络和子网。
1. 创建 Azure 可用性集或部署可用性区域。
    + 对于可用性集，将最大更新域设置为 2。
1. 创建虚拟机 1。
    + 在 Azure 应用商店中为 SAP 映像使用红帽企业 Linux。
    + 选择在步骤 3 中创建的 Azure 可用性集，或选择"可用性区域"。
1.  创建虚拟机 2。
    + 在 Azure 应用商店中为 SAP 映像使用红帽企业 Linux。
    + 选择在步骤 3 中创建的 Azure 可用性集，或选择可用性区域（与步骤 3 中不同的区域）。
1. 将数据磁盘添加到 VM，然后检查文章 IBM [Db2 Azure 虚拟机 DBMS 部署 SAP 工作负载][dbms-db2]中的文件系统设置的建议。

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集
    
要为此 IBM Db2 服务器创建基本起搏器群集，请参阅 [在 Azure 中的红帽企业 Linux 上设置起搏器][rhel-pcs-azr]。 

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

#### <a name="red-hat-firewall-rules"></a>红帽防火墙规则
红帽企业 Linux 默认启用了防火墙。 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>安装提示，用于使用 HADR 设置 IBM Db2 LUW

要设置主 IBM Db2 LUW 数据库实例，

- 使用高可用性或分布式选项。
- 安装 SAP ASCS/ERS 和数据库实例。
- 备份新安装的数据库。

> [!IMPORTANT] 
> 记下安装期间设置的"数据库通信端口"。 对于两个数据库实例，它必须是相同的端口号。
>![SAP SWPM 端口定义](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>用于 Azure 的 IBM Db2 HADR 设置

   使用 Azure 起搏器击剑代理时，设置以下参数：

   - HADR 对等窗口持续时间（秒） （HADR_PEER_WINDOW） = 240  
   - HADR 超时值 （HADR_TIMEOUT） = 45

我们建议基于初始故障转移/接管测试的上述参数。 您必须使用这些参数设置测试故障转移和接管的正确功能。 由于单个配置可能有所不同，因此参数可能需要调整。 

> [!NOTE]
> 特定于具有 HADR 配置且具有正常启动的 IBM Db2：辅助数据库或备用数据库实例必须启动并运行，然后才能启动主数据库实例。

   
> [!NOTE]
> 对于特定于 Azure 和起搏器的安装和配置：在通过 SAP 软件配置管理器进行安装过程中，存在关于 IBM Db2 LUW 高可用性的明确问题：
>+ 不要选择**IBM Db2 纯标额**。
>+ 不要选择**为多平台安装 IBM Tivoli 系统自动化**。
>+ 不要选择 **"生成群集配置文件**"。
>![SAP SWPM - DB2 HA 选项](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


要使用 SAP 同质系统复制过程设置备用数据库服务器，请执行以下步骤：

1. 选择 **"系统复制**选项>**目标系统** > **分布式** > **数据库实例**。
1. 作为复制方法，选择 **"同质系统"，** 以便可以使用备份还原备用服务器实例上的备份。
1. 当您到达用于还原数据库以进行同质系统复制的退出步骤时，请退出安装程序。 从主主机的备份还原数据库。 所有后续安装阶段已在主数据库服务器上执行。

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR 的红帽防火墙规则
添加防火墙规则以允许流量到 DB2 和 DB2 之间，以便 HADR 正常工作：
+ 数据库通信端口。 如果使用分区，则添加这些端口。
+ HADR 端口（DB2 参数的值HADR_LOCAL_SVC）
+ Azure 探测端口
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 哈德哈德检查
为了演示目的和本文中描述的过程，数据库 SID 是**ID2**。

配置 HADR 后，状态为"PEER"和"在主节点和备用节点上连接"，请执行以下检查：

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



## <a name="db2-pacemaker-configuration"></a>Db2 起搏器配置

在节点发生故障时，使用起搏器进行自动故障转移时，需要相应地配置 Db2 实例和起搏器。 本节介绍这种类型的配置。

以下项以以下任一项为预缀：

- **[A]**： 适用于所有节点
- **[1]**： 仅适用于节点 1 
- **[2]**： 仅适用于节点 2

**[A]** 起搏器配置的先决条件：
1. 使用 db2stop 关闭用户\<db2 sid>两个数据库服务器。
1. 将 db2\<sid>用户的 shell 环境更改为 */bin/ksh*：
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>起搏器配置

**[1]** IBM Db2 特定于 HADR 的起搏器配置：
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
* 将起搏器置于维护模式。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**{1}** 确保群集状态正常，并且所有资源都已启动。 资源运行的节点并不重要。
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

在线： [ az-idb01 az-idb02 ]

资源的完整列表：

 rsc_st_azure（石：fence_azure_arm）：开始 az-idb01 主/从集：Db2_HADR_ID2主 [Db2_HADR_ID2] 主控： [ az-idb01] 从属： [ az-idb02 ] 资源组： g_ipnc_db2id2_ID2vip_db2id2_ID2 （ocf：isid：IPaddr2）： 开始 az-idb01 nc_db2id2_ID2 （ocf：心跳：azure-lb）： 启动 az-idb01

守护进程状态：共同步：活动/禁用心脏起搏器：活动/禁用 pcsd：活动/启用
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

**[A]** 为探测端口添加防火墙规则：
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. 登录到 J2EE 实例的主应用程序服务器并执行：
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. 在左侧框架中，选择**安全存储**。
1. 在正确的框架中，选择键`jdbc/pool/\<SAPSID>/url`。
1. 将 JDBC URL 中的主机名更改为虚拟主机名。
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. 选择“添加”****。
1. 要保存更改，请选择左上角的磁盘图标。
1. 关闭配置工具。
1. 重新启动 Java 实例。

## <a name="configure-log-archiving-for-hadr-setup"></a>为 HADR 设置配置日志存档
要为 HADR 设置配置 Db2 日志存档，我们建议您将主数据库和备用数据库配置为具有来自所有日志存档位置的自动日志检索功能。 主数据库和备用数据库都必须能够从数据库实例之一可能存档日志文件的所有日志存档位置检索日志文件。 

日志存档仅由主数据库执行。 如果更改数据库服务器的 HADR 角色，或者发生故障，则新的主数据库负责日志存档。 如果设置了多个日志存档位置，则日志可能会存档两次。 如果发生本地或远程追赶，您可能还必须手动将存档的日志从旧主服务器复制到新主服务器的活动日志位置。

我们建议配置公共 NFS 共享或 GlusterFS，其中日志是从两个节点写入的。 NFS 共享或 GlusterFS 必须高度可用。 

您可以使用现有的高可用 NFS 共享或 GlusterFS 进行传输或配置文件目录。 有关详细信息，请参阅：

- [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS][glusterfs] 
- [用于 SAP 应用程序的 Azure NetApp 文件，在红帽企业 Linux 上的 Azure VM 上的 SAP NetWeaver 的高可用性][anf-rhel]
- [Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)（创建 NFS 共享）

## <a name="test-the-cluster-setup"></a>测试群集设

本节介绍如何测试 Db2 HADR 设置。 每个测试都假定 IBM Db2 主数据库在*az-idb01*虚拟机上运行。 必须使用具有 sudo 权限或根（不推荐）的用户。

此处说明了所有测试用例的初始状态：（crm_mon -r 或 pcs 状态）

- **pcs 状态**是起搏器执行时状态的快照 
- **crm_mon -r**是起搏器状态的连续输出

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

SAP 系统中的原始状态记录在事务 DBACOCKPIT > 配置>概述中，如下图所示：

![DBACockpit - 迁移前](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>测试 IBM Db2 的接管


> [!IMPORTANT] 
> 在开始测试之前，请确保：
> * 起搏器没有任何失败操作（pcs 状态）。
> * 没有位置约束（迁移测试的剩下）
> * IBM Db2 HADR 同步工作。 请与用户 db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


通过执行以下命令迁移运行主 Db2 数据库的节点：
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

迁移完成后，crm 状态输出如下所示：
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

SAP 系统中的原始状态记录在事务 DBACOCKPIT > 配置>概述中，如下图所示：

![DBACockpit - 迁移后](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

使用"pcs 资源移动"的资源迁移会创建位置约束。 在这种情况下，位置约束正在阻止在 az-idb01 上运行 IBM Db2 实例。 如果未删除位置约束，则资源不能失败。

删除位置约束，备用节点将在 az-idb01 上启动。
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
群集状态更改为：
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

![DBACockpit - 删除的位置约束](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


将资源迁移回*az-idb01*并清除位置约束
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs 资源\<移动res_name> <host>：** 创建位置约束，并可能导致收购问题
- **pcs 资源\<清除res_name>：** 清除位置约束
- **pcs 资源\<清理res_name>：** 清除资源的所有错误

### <a name="test-a-manual-takeover"></a>测试手动接管

您可以通过在*az-idb01*节点上停止起搏器服务来测试手动接管：
<pre><code>systemctl stop pacemaker</code></pre>

*az-ibdb02*上的状态
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

故障转移后，您可以在*az-idb01*上再次启动服务。
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>在运行 HADR 主数据库的节点上终止 Db2 进程

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Db2 实例将失败，起搏器将移动主节点并报告以下状态：

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

Pacemaker 将重新启动同一节点上的 Db2 主数据库实例，否则它将故障转移到运行辅助数据库实例的节点，并报告错误。

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>在运行辅助数据库实例的节点上终止 Db2 进程

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

节点进入未遂声明和报告错误
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

Db2 实例在以前分配的辅助角色中重新启动。

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>通过运行 HADR 主数据库实例的节点上的 db2stop 力停止数据库

作为用户 db2\<sid>执行命令 db2stop 力：
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

Db2 HADR 辅助数据库实例已提升到主要角色。
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>将运行 HADR 主数据库实例的 VM 与"halt"崩溃

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

在这种情况下，Pacemaker 将检测运行主数据库实例的节点未响应。

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

下一步是检查*分裂大脑*的情况。 在幸存的节点确定上次运行主数据库实例的节点已关闭后，将执行资源故障转移。

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


如果出现内核死机，故障节点将由屏蔽代理恢复。 故障节点重新联机后，您必须启动起搏器群集
<pre><code>sudo pcs cluster start</code></pre> 它将 Db2 实例启动到辅助角色中。

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
