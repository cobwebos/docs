---
title: 设置在 RHEL 上的 Azure 虚拟机 (Vm) 上的 IBM Db2 HADR |Microsoft Docs
description: 建立 IBM Db2 LUW Azure 虚拟机 (Vm) RHEL 上的高的可用性。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812124"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Red Hat Enterprise Linux Server 上的 Azure Vm 上的 IBM Db2 LUW 的高可用性

适用于 Linux、 UNIX 和 Windows (LUW) 中的 IBM Db2[高可用性和灾难恢复 (HADR) 配置](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html)运行主数据库实例的一个节点和至少一个运行的辅助数据库实例的节点组成。 更改到主数据库实例复制到辅助数据库实例以同步方式还是以异步方式，具体取决于你的配置。 

本文介绍如何部署和配置 Azure 虚拟机 (Vm)、 安装群集框架，并使用 HADR 配置安装 IBM Db2 LUW。 

本文未介绍如何安装和配置 IBM Db2 LUW HADR 或 SAP 软件安装。 若要帮助您完成这些任务，我们提供对 SAP 和 IBM 安装手册的引用。 本文重点介绍特定于 Azure 环境的部件。 

支持的 IBM Db2 版本为 10.5 及更高版本，如 SAP 说明中所述[1928533]。

在开始安装之前，请参阅以下 SAP 说明和文档：

| SAP 说明 | 描述 |
| --- | --- |
| [1928533] | 在 Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] | Azure 上的 SAP:支持先决条件 |
| [2178632] | Azure 上的 SAP 关键监控指标 |
| [2191498] | Azure 的 Linux 上的 SAP：增强型监视 |
| [2243692] | 在 Azure (IaaS) VM 上的 Linux:SAP 许可证问题 |
| [2002167] | Red Hat Enterprise Linux 7.x：安装和升级 |
| [2694118] | 在 Azure 上的 Red Hat Enterprise Linux HA 加载项 |
| [1999351] | 适用于 SAP 的增强型 Azure 监视故障排除 |
| [2233094] | DB6：在 Azure 上的 SAP 应用程序使用 IBM Db2 for Linux、 UNIX 和 Windows 的其他信息 |
| [1612105] | DB6：有关使用 HADR Db2 的常见问题解答 |


| 文档 | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes):具有所需的 SAP 说明的所有适用于 Linux |
| [Azure 虚拟机规划和实施适用于 Linux 上的 SAP][planning-guide]指南 |
| [适用于 Linux 上的 SAP 的 azure 虚拟机部署][deployment-guide]（详见本文） |
| [Azure 虚拟机数据库管理 system(DBMS) 部署于 Linux 上的 SAP][dbms-guide]指南 |
| [Azure 规划和部署清单上的 SAP 工作负荷][azr-sap-plancheck] |
| [Red Hat Enterprise Linux 7 的高可用性加载项的概述][rhel-ha-addon] |
| [High Availability Add-On Administration][rhel-ha-admin]（高可用性附加产品管理） |
| [High Availability Add-On 参考][rhel-ha-ref] |
| [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members][rhel-azr-supp]（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
| [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure][rhel-azr-inst]（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
| [SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [对于 RHEL 高可用性群集-支持策略的 IBM Db2 for Linux、 Unix 和 Windows 的群集中的管理][rhel-db2-supp]



## <a name="overview"></a>概述
若要实现高可用性，使用 HADR 的 IBM Db2 LUW 安装在至少两台 Azure 虚拟机上，在部署[Azure 可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)或跨[Azure 可用性区域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)。 

下图显示两个数据库服务器的 Azure Vm 的设置。 这两个数据库服务器的 Azure Vm 具有其自己的存储附加和已启动并运行。 在 HADR，一个 Azure Vm 中的一个数据库实例具有主实例的角色。 所有客户端连接到主实例。 数据库事务中的所有更改都保留在本地，Db2 事务日志中。 因为事务日志记录将保留在本地，记录传输通过 TCP/IP 到的数据库实例上的第二个数据库服务器、 备用服务器或备用实例。 备用实例通过前滚传输的事务日志记录来更新本地数据库。 这样一来，备用服务器保持与主服务器同步。

HADR，仅复制功能。 它具有无故障检测和没有自动接管或故障转移功能。 接管或传输到备用服务器必须手动启动由数据库管理员。 若要实现自动接管和故障检测，可以使用 Linux Pacemaker 聚类分析功能。 Pacemaker 监视两个数据库服务器实例。 主数据库服务器实例何时发生故障，将启动 Pacemaker*自动*HADR 接管通过备用服务器。 Pacemaker 还可确保虚拟 IP 地址分配给新的主服务器。

![IBM Db2 高可用性概述](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

若要使 SAP 应用程序服务器连接到主数据库，你需要虚拟主机名和虚拟 IP 地址。 发生故障转移时，SAP 应用程序服务器将连接到新的主数据库实例。 在 Azure 环境中， [Azure 负载均衡器](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx)需要在具有所需的 HADR 的 IBM Db2 的方法中使用的虚拟 IP 地址。 

为了帮助您充分了解如何使用 HADR 和 Pacemaker 的 IBM Db2 LUW 适用于高度可用的 SAP 系统设置下, 图提供了 SAP 系统基于 IBM Db2 数据库的高度可用设置的概述。 本文介绍了仅 IBM Db2，但它提供了有关如何设置 SAP 系统的其他组件的其他文章的引用。

![IBM DB2 高可用性的完整环境概述](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>所需的步骤的高级概述
若要部署的 IBM Db2 配置，需要执行以下步骤：

  + 规划您的环境。
  + 部署 Vm。
  + 更新 RHEL Linux 和配置文件系统。
  + 安装和配置 Pacemaker。
  + 安装程序[glusterfs 群集][glusterfs] or [Azure NetApp Files][anf-rhel]
  + 安装[单独的群集上的 ASCS/ERS][ascs-ha-rhel]。
  + 使用分布式/高可用性选项 (SWPM) 安装 IBM Db2 数据库。
  + 安装和创建辅助数据库节点和实例，并配置 HADR。
  + 确认的 HADR 正常工作。
  + Pacemaker 配置应用到控件 IBM Db2。
  + 配置 Azure 负载均衡器。
  + 安装主和对话框应用程序服务器。
  + 检查并调整的 SAP 应用程序服务器配置。
  + 执行故障转移和接管测试。



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>计划用于承载使用 HADR 的 IBM Db2 LUW 的 Azure 基础结构

完成规划过程后再执行部署。 计划生成的部署使用 Azure 中的 HADR Db2 配置的基础。 下表列出了需要规划 IMB Db2 LUW （数据库 SAP 环境的一部分） 的一部分的关键元素：

| 主题 | 简短说明 |
| --- | --- |
| 定义 Azure 资源组 | 在其中部署 VM、 VNet、 Azure 负载均衡器和其他资源的资源组。 可以是现有或新。 |
| 虚拟网络 / 子网的定义 | IBM Db2 和 Azure 负载均衡器的 Vm 正在部署位置。 可以是现有或新创建。 |
| 托管 IBM Db2 LUW 的虚拟机 | VM 大小、 存储、 网络、 IP 地址。 |
| 虚拟主机名和虚拟 IP 为 IBM Db2 数据库| 虚拟 IP 或主机名，用于 SAP 应用程序服务器的连接。 **db virt hostname**， **db virt ip**。 |
| Azure 的隔离 | 阻止方法，以避免拆分脑情况。 |
| Azure 负载均衡器 | 使用情况的基本或标准 （推荐），探测 Db2 数据库 （我们建议 62500） 的端口**探测端口**。 |
| 名称解析| 名称解析的工作原理在环境中。 强烈建议 DNS 服务。 可以使用本地主机文件。 |
    
有关在 Azure 中 Linux Pacemaker 的详细信息，请参阅[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker][rhel-pcs-azr]。

## <a name="deployment-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上的部署

IBM Db2 LUW 的资源代理包括在 Red Hat Enterprise Linux Server HA 加载项中。 对于本文档中所述的设置，应为 SAP 使用 Red Hat Enterprise Linux。 Azure Marketplace 中包含 for Red Hat Enterprise Linux 7.4(service SAP 或更高版本，可用于部署新的 Azure 虚拟机映像。 请注意当你在 Azure VM Marketplace 中选择 VM 映像时通过 Azure Marketplace 的 Red Hat 提供的各种支持或服务模型。

### <a name="hosts-dns-updates"></a>主机：DNS 更新
列出所有的主机名，包括虚拟主机名的列表并更新你的 DNS 服务器来启用主机名解析为正确的 IP 地址。 如果 DNS 服务器不存在或无法更新和创建 DNS 条目，您需要使用单独的 Vm，在此方案中加入的本地主机文件。 如果您使用的主机文件条目，请确保条目将应用到 SAP 系统环境中的所有 Vm。 但是，我们建议使用你的 DNS，理想情况下，扩展到 Azure


### <a name="manual-deployment"></a>手动部署

请确保为 IBM Db2 LUW 所选的操作系统支持通过 IBM/SAP。 SAP 说明中提供了支持的操作系统版本对于 Azure Vm 和 Db2 版本的列表[1928533]。 在 SAP 产品可用性对照表中提供了单个 Db2 版本的操作系统版本的列表。 我们强烈建议由于与 Azure 相关的性能改进，在此或更高版本的一个最少为适用于 SAP 的 Red Hat Enterprise Linux 7.4(service Red Hat Enterprise Linux 版本。

1. 创建或选择资源组。
1. 创建或选择虚拟网络和子网。
1. 创建 Azure 可用性集或部署在可用性区域。
    + 可用性集设置为 2 的最大更新域。
1. 创建虚拟机 1。
    + 使用 SAP 映像在 Azure Marketplace 中的 Red Hat Enterprise Linux。
    + 选择在步骤 3 中创建的 Azure 可用性集或可用性区域。
1.  创建虚拟机 2。
    + 使用 SAP 映像在 Azure Marketplace 中的 Red Hat Enterprise Linux。
    + 选择你在步骤 3 中创建或选择可用区域 （而不是同一个区域如步骤 3 中所示） 的 Azure 可用性集。
1. 将数据磁盘添加到 Vm，然后检查一文中的文件系统设置的建议[SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署][dbms-db2]。

## <a name="create-the-pacemaker-cluster"></a>创建 Pacemaker 群集
    
若要创建此 IBM Db2 服务器的一个基本 Pacemaker 群集，请参阅 [在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker][rhel-pcs-azr]。 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>安装 IBM Db2 LUW 和 SAP 环境

在开始安装基于 IBM Db2 LUW 上的 SAP 环境之前，请查看以下文档：

+ Azure 文档
+ SAP 文档
+ IBM 文档

在本文的介绍性部分中提供了此文档的链接。

检查有关安装基于 NetWeaver 的应用程序在 IBM Db2 LUW 上的 SAP 安装手册。
你可以通过使用查找在 SAP 帮助门户的指南[SAP 安装指南 Finder][sap-instfind]。

您可以减少参考线显示在门户中通过设置以下筛选器的数量：
- 我想："安装新系统"
- 我的数据库："IBM Db2 for Linux、 Unix 和 Windows"
- 为 SAP NetWeaver 版本、 堆栈配置或操作系统的其他筛选器

#### <a name="red-hat-firewall-rules"></a>Red Hat 防火墙规则
Red Hat Enterprise Linux 已默认启用防火墙。 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>设置使用 HADR 的 IBM Db2 LUW 安装提示

若要设置主 IBM Db2 LUW 数据库实例：

- 使用高可用性或分布式的选项。
- 安装 SAP ASCS/ERS 和数据库实例。
- 执行新安装的数据库的备份。

> [!IMPORTANT] 
> 记下在安装过程中设置的"数据库通信端口"。 它必须为两个数据库实例相同的端口号。
>![SAP SWPM 端口定义](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>适用于 Azure 的 IBM Db2 HADR 设置

   当你使用 Azure Pacemaker 隔离代理时，设置以下参数：

   - HADR 对等方窗口持续时间 （秒） (HADR_PEER_WINDOW) = 240  
   - HADR 超时值 (HADR_TIMEOUT) = 45

我们建议基于初始接管故障转移/测试上述参数。 它是必需的特有功能的故障转移和接管测试使用这些参数设置的。 因为各个配置可以改变，则参数可能需要进行调整。 

> [!NOTE]
> 特定于使用与正常启动 HADR 配置 IBM Db2:你可以启动主数据库实例之前，必须启动并运行辅助或备用数据库实例。

   
> [!NOTE]
> 有关安装和配置特定于 Azure 和 Pacemaker 的：在通过 SAP Software Provisioning Manager 安装过程中，还有一个显式的问题有关的 IBM Db2 LUW 的高可用性：
>+ 不要选择**IBM Db2 pureScale**。
>+ 不要选择**安装适用于 Multiplatforms IBM Tivoli 系统自动化**。
>+ 不要选择**生成群集配置文件**。
>![SAP SWPM-DB2 HA 选项](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


若要使用 SAP 同构系统复制过程设置了备用数据库服务器，请执行以下步骤：

1. 选择**系统副本**选项 >**确定的目标系统** > **分布式** > **数据库实例**。
1. 作为复制方法，选择**同构系统**，以便可以使用备份来还原备用服务器实例上的备份。
1. 到达退出步骤同构系统复制将数据库还原后，退出安装程序。 从主主机的备份还原数据库。 已在主数据库服务器上执行所有后续的安装阶段。

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>DB2 HADR 的 Red Hat 的防火墙规则
添加防火墙规则以允许到 DB2 和 HADR 若要运行的 DB2 之间的流量：
+ 数据库的通信端口。 如果使用分区，还添加这些端口。
+ HADR 端口 （DB2 参数 HADR_LOCAL_SVC 值）
+ Azure 的探测端口
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>IBM Db2 HADR 检查
对于演示目的，在这篇文章中所述的过程，数据库 SID 是**ID2**。

已配置 HADR，并且状态为主要和备用节点上的对等方和已连接后，请执行以下检查：

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

当使用 Pacemaker 的自动故障转移出现节点故障时，需要相应地配置您的 Db2 实例和 Pacemaker。 本部分介绍此类型的配置。

以下各项带有前缀：

- **[A]** ：适用于所有节点
- **[1]** ：仅适用于节点 1 
- **[2]** ：仅适用于节点 2

**[A]** 系统 Pacemaker 配置必备组件：
1. 关闭这两个数据库服务器与用户 db2\<sid > 与 db2stop。
1. 更改用于 db2 的 shell 环境\<sid > 用户*ksh/bin/* :
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker 配置

**[1]** IBM Db2 HADR 特定于 Pacemaker 配置：
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
* 退出维护模式将 Pacemaker。
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** 确保群集状态为确定，启动的所有资源。 哪一节点运行的资源并不重要。
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

联机: [az idb01 az-idb02]

资源的完整列表：

 rsc_st_azure   (stonith:fence_azure_arm):    已启动的 az idb01 主/从集：母版 Db2_HADR_ID2 master [Db2_HADR_ID2]: [az idb01] 从属服务: [az idb02] 资源组： g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2):     启动 az idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb):    已启动的 az idb01

守护程序状态： corosync： 活动/已禁用 pacemaker： 主动/禁用 pcsd： 活动/已启用
</pre>

> [!IMPORTANT]
> 您必须管理 Pacemaker 群集的 Db2 实例使用 Pacemaker 工具。 如果使用 db2 命令，如 db2stop，Pacemaker 检测到的资源为失败的操作。 如果在执行维护，可以将节点或资源放在维护模式下。 Pacemaker 挂起监视资源，然后可以使用正常的 db2 管理命令。


### <a name="configure-azure-load-balancer"></a>配置 Azure 负载均衡器
若要配置 Azure 负载均衡器，我们建议你使用[Azure 标准负载均衡器 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) ，然后执行以下操作：

1. 创建前端 IP 池：

   a. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**前端 IP 池**，然后选择**添加**。

   b. 输入新前端 IP 池的名称 (例如， **Db2 连接**)。

   c. 设置**赋值**到**静态**，并输入 IP 地址**虚拟 IP**开始时定义。

   d. 选择“确定”  。

   e. 创建新前端 IP 池后，请记下池 IP 地址。

1. 创建后端池：

   a. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**后端池**，然后选择**添加**。

   b. 输入新后端池的名称 (例如， **Db2 后端**)。

   c. 选择“添加虚拟机”。 

   d. 选择可用性集或承载在上一步中创建的 IBM Db2 数据库的虚拟机。

   e. 选择 IBM Db2 群集的虚拟机。

   f. 选择“确定”  。

1. 创建运行状况探测：

   a. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**运行状况探测**，然后选择**添加**。

   b. 输入新的运行状况探测的名称 (例如， **Db2 hp**)。

   c. 选择**TCP**作为协议和端口**62500**。 保持**间隔**值设置为**5**，并保留**不正常阈值**值设置为**2**。

   d. 选择“确定”  。

1. 创建负载均衡规则：

   a. 在 Azure 门户中，打开 Azure 负载均衡器中，选择**负载均衡规则**，然后选择**添加**。

   b. 输入新负载均衡器规则的名称 (例如， **Db2 SID**)。

   c. 选择的前端 IP 地址、 后端池和之前创建的运行状况探测 (例如， **Db2 前端**)。

   d. 保持**协议**设置为**TCP**，并输入端口*数据库通信端口*。

   e. 将“空闲超时”增大到 30 分钟。 

   f. 确保**启用浮动 IP**。

   g. 选择“确定”  。

**[A]** 探测端口添加防火墙规则：
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>对要用于连接的虚拟 IP 的 SAP 配置文件进行更改
若要连接到主实例的 HADR 配置中，将 SAP 应用程序层需要使用您定义和配置 Azure 负载均衡器的虚拟 IP 地址。 不需要以下更改：

/sapmnt/\<SID >/配置文件/默认值。PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>安装主和对话框应用程序服务器

在安装主，针对 Db2 HADR 配置的对话框应用程序服务器，使用虚拟主机名你选择的配置。 

如果您执行安装，然后创建 Db2 HADR 配置，使所做的更改，如所述在上一部分中并按如下所示为 SAP Java 堆栈。

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java 或 Java 堆栈系统 JDBC URL 检查

J2EE 配置工具用于检查或更新 JDBC URL。 由于 J2EE 配置工具是一种图形工具，你需要 X 服务器安装：
 
1. 登录到主应用程序服务器的 J2EE 实例并执行：
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. 在左框架中，选择**安全存储区**。
1. 在右栏中，选择每个池的 jdbc 密钥 / \<SAPSID>/url。
1. 将 JDBC URL 中的主机名更改为虚拟主机名。
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. 选择**添加**。
1. 若要保存所做的更改，请选择左上角的磁盘图标。
1. 关闭配置工具。
1. 重新启动 Java 实例。

## <a name="configure-log-archiving-for-hadr-setup"></a>配置日志存档 HADR 安装程序
若要配置的 Db2 日志存档 HADR 安装程序，我们建议您配置主要和备用数据库能够自动日志检索功能中所有日志存档位置。 主要和备用数据库必须能够从哪些其中任何一数据库的实例可能存档日志文件的所有日志存档位置检索日志存档文件。 

日志存档仅由主数据库执行。 如果您更改的数据库服务器的 HADR 角色或新的主数据库发生故障，如果将负责日志存档。 如果设置了多个日志存档位置，可能会两次存档日志。 发生本地或远程的弥补订阅，您可能还需要手动将已存档的日志从旧的主服务器复制到新的主服务器的活动的日志位置。

我们建议配置的常见的 NFS 共享或 GlusterFS，从这两个节点写入日志的位置。 在 NFS 共享或 GlusterFS 必须为高度可用。 

可用于现有高度可用的 NFS 共享或 GlusterFS 传输或配置文件目录。 有关详细信息，请参阅：

- [适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 上的 GlusterFS][glusterfs] 
- [使用 Azure NetApp 文件的 SAP 应用程序的 Red Hat Enterprise Linux 上的 Azure Vm 上的 SAP NetWeaver 的高可用性][anf-rhel]
- [Azure 的 NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction)（以便创建 NFS 共享）

## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试 Db2 HADR 设置。 每个测试都假设上运行 IBM Db2 主*az idb01*虚拟机。 必须使用具有 sudo 权限或 （不推荐） 的根用户。

此处所述的所有测试用例的初始状态: （crm_mon-r 或 pc 状态）

- **pc 状态**是在执行时 Pacemaker 状态的快照 
- **crm_mon r**是连续的 Pacemaker 状态输出

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

事务 DBACOCKPIT 中记录了 SAP 系统中的原始状态 > 配置 > 概述，请在下图中所示：

![DBACockpit - Pre Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>测试 takeover 的 IBM Db2


> [!IMPORTANT] 
> 在开始测试之前，请确保：
> * Pacemaker 没有任何失败的操作 （电脑状态）。
> * 没有任何位置约束 （迁移测试的剩余部分）
> * IBM Db2 HADR 同步正常工作。 请与用户 db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


迁移通过执行以下命令运行主 Db2 数据库的节点：
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

完成的迁移后，crm 状态输出如下所示：
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

事务 DBACOCKPIT 中记录了 SAP 系统中的原始状态 > 配置 > 概述，请在下图中所示：

![DBACockpit - Post Migration](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

使用"移动 pc 资源"的资源迁移过程将创建位置约束。 在这种情况下，位置约束，使 az idb01 上的正在运行 IBM Db2 实例。 如果未删除位置约束，该资源无法故障回复。

将 az idb01 上启动的删除位置约束和备用节点。
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
和群集状态将更改为：
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

![DBACockpit-已删除的位置约束](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


将资源移动回*az idb01*和清除的位置约束
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pcs resource move， \<res_name > <host>:** 创建位置约束，并可能导致接管出现问题
- **pc 资源清除\<res_name >** :清除位置约束
- **pc 资源清理\<res_name >** :清除所有错误的资源

### <a name="test-a-manual-takeover"></a>测试手动接管

可以通过停止上的 Pacemaker 服务来测试手动接管*az idb01*节点：
<pre><code>systemctl stop pacemaker</code></pre>

status on *az-ibdb02*
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

故障转移后，您可以再次启动服务上*az idb01*。
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>终止运行 HADR 主数据库的节点上的 Db2 进程

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

将 Db2 实例失败，并 Pacemaker 将主节点，并报告以下状态：

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

Pacemaker 将重新启动 Db2 主数据库实例在同一节点上的或它将故障转移到正在运行的辅助数据库实例的节点并报告错误。

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>终止运行的辅助数据库实例的节点上的 Db2 进程

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

节点获取到没有规定并报告错误
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

Db2 实例重新启动它之前必须分配辅助角色中。

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>通过运行 HADR 主数据库实例的节点上的 db2stop 强制停止 DB

作为用户 db2\<sid > 执行命令 db2stop 强制：
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

检测到的故障：

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

Db2 HADR 辅助数据库实例已升级到主角色中。
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>崩溃运行具有"halt"的 HADR 主数据库实例的 VM

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

在这种情况下，Pacemaker 将检测到正在运行的主数据库实例的节点没有响应。

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

下一步是检查*裂脑*情况。 工作正常的节点已确定上次运行的主数据库实例的节点已关闭后，将执行资源的故障转移。

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


核心 panic，发生故障的节点将 restared 隔离代理。 发生故障的节点重新联机后，必须启动 pacemaker 群集
<pre><code>sudo pcs cluster start</code></pre> 它将启动到辅助角色的 Db2 实例。

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
- [高可用性体系结构和方案适用于 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker][rhel-pcs-azr]
