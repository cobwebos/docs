---
title: 在 RHEL 上和的高可用性 SAP HANA 向上扩展 |Microsoft Docs
description: 利用 Azure 虚拟机上的和， (Vm) 建立 SAP HANA 的高可用性。
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2020
ms.author: radeltch
ms.openlocfilehash: ce24bf541c5a71c50bb34f5e42aa3452f01b871c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978163"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上的 Azure NetApp 文件 SAP HANA 扩展的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文介绍了如何使用 Azure NetApp 文件 (和) ，在向上扩展部署中配置 SAP HANA 系统复制。 使用示例配置和安装命令、实例编号 **03**和 HANA 系统 ID **HN1** 。 SAP HANA 复制由一个主节点和至少一个辅助节点组成。

如果此文档中的步骤标记有以下前缀，则含义如下：

- **[A]**：该步骤适用于所有节点
- **[1]**：该步骤仅适用于节点1
- **[2]**：该步骤仅适用于节点2
 
请先阅读以下 SAP 说明和文档：

- SAP 说明 [1928533](https://launchpad.support.sap.com/#/notes/1928533)，其中包含：
    - SAP 软件部署支持的 Azure VM 大小的列表。
    - Azure VM 大小的重要容量信息。
    - 支持的 SAP 软件、操作系统 (OS) 和数据库组合。
    - Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本。
- SAP 说明 [2015553](https://launchpad.support.sap.com/#/notes/2015553) 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
- SAP 说明 [405827](https://launchpad.support.sap.com/#/notes/405827) 列出了 HANA 环境的建议文件系统。
- SAP 说明 [2002167](https://launchpad.support.sap.com/#/notes/2002167) 对 Red Hat Enterprise Linux 建议使用操作系统设置。
- SAP 说明 [2009879](https://launchpad.support.sap.com/#/notes/2009879) 对 Red Hat Enterprise Linux 有 SAP HANA 的准则。
- SAP 说明 [2178632](https://launchpad.support.sap.com/#/notes/2178632) 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
- SAP 说明 [2191498](https://launchpad.support.sap.com/#/notes/2191498) 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
- SAP 说明 [2243692](https://launchpad.support.sap.com/#/notes/2243692) 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
- SAP 说明 [1999351](https://launchpad.support.sap.com/#/notes/1999351) 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
- [Sap 社区 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
- [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
- [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
- [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
- [在 pacemaker 群集中 SAP HANA 系统复制。](https://access.redhat.com/articles/3004101)
- 通用 RHEL 文档
    - [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
    - [高可用性 Add-On 管理。](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [高可用性 Add-On 引用。](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [当 HANA 文件系统位于 NFS 共享上时，在 Pacemaker 群集的 Scale-Up 中配置 SAP HANA 系统复制](https://access.redhat.com/solutions/5156571)
- Azure 特定的 RHEL 文档：
    - [RHEL 高可用性群集的支持策略-作为群集成员 Microsoft Azure 虚拟机。](https://access.redhat.com/articles/3131341)
    - [在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 (及更高版本) High-Availability 群集。](https://access.redhat.com/articles/3252491)
    - [在 Red Hat Enterprise Linux 上安装 SAP HANA 以便用于 Microsoft Azure。](https://access.redhat.com/solutions/3193782)
    - [当 HANA 文件系统位于 NFS 共享上时，配置 SAP HANA 向上扩展系统复制 Pacemaker 群集](https://access.redhat.com/solutions/5156571)
- [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序](https://www.netapp.com/us/media/tr-4746.pdf)

## <a name="overview"></a>概述

通常在向上扩展环境中，SAP HANA 的所有文件系统都从本地存储装入。 在[RHEL 上设置 SAP HANA 系统复制](./sap-hana-high-availability-rhel.md)中发布了 Red Hat Enterprise Linux 上的 SAP HANA 系统复制的高可用性

若要在 [Azure NetApp 文件](../../../azure-netapp-files/index.yml) NFS 共享上实现向上扩展系统的 SAP HANA 高可用性，在群集中需要额外的资源配置，以便在一个节点失去对和上 NFS 共享的访问权限时，恢复 HANA 资源。  群集管理 NFS 装载，使其能够监视资源的运行状况。 强制执行文件系统和 SAP HANA 资源之间的依赖关系。  

![和上的 SAP HANA HA 扩展](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

使用每个节点上的 Azure NetApp 文件在 NFS 共享上装载 SAP HANA 文件系统。 文件系统/hana/data、/hana/log 和/hana/shared 对每个节点都是唯一的。 

装入节点1节点 1 (**hanadb1**) 

- /hana/data 上的10.32.2.4：/**hanadb1**-mnt00001
- /hana/log 上的10.32.2.4：/**hanadb1**-mnt00001
- 10.32.2.4：//**hanadb1**-mnt00001 on/hana/shared

已在节点2上装载 (**hanadb2**) 

- /hana/data 上的10.32.2.4：/**hanadb2**-mnt00001
- /hana/log 上的10.32.2.4：/**hanadb2**-mnt00001
- 10.32.2.4：//**hanadb2**-mnt00001 on/hana/shared

> [!NOTE]
> 在两个节点之间不共享文件系统/hana/shared、/hana/data 和/hana/log。 每个群集节点都有其自己的独立文件系统。   

SAP HANA 系统复制配置使用专用的虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示负载均衡器的配置：

- 前端配置：用于 hn1 的 IP 地址 10.32.0.10-db
- 后端配置：连接到应当作为 HANA 系统复制的一部分的所有虚拟机的主网络接口
- 探测端口：端口 62503
- 负载均衡规则： 30313 TCP、30315 TCP、30317 TCP、30340 TCP、30341 TCP、30342 TCP (（如果使用基本 Azure 负载均衡器)   

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>设置 Azure NetApp 文件基础结构

在继续设置 Azure NetApp 文件基础结构之前，请先熟悉 Azure [Netapp 文件文档](../../../azure-netapp-files/index.yml)。

Azure NetApp 文件在多个 [azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中提供。 查看所选 Azure 区域是否提供 Azure NetApp 文件。

有关 azure 区域的 Azure NetApp 文件可用性的信息，请参阅 azure [区域的 Azure Netapp 文件可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)。

部署 Azure NetApp 文件之前，请通过 [注册 Azure netapp 文件说明](../../../azure-netapp-files/azure-netapp-files-register.md)，请求加入 Azure netapp 文件。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和将在其中装载 Azure NetApp 文件资源的 Vm 必须部署在同一 Azure 虚拟网络或对等互连 Azure 虚拟网络中。

1. 如果尚未部署资源，请请求 [加入 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-register.md)。

2. 按照 [创建 netapp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的说明，在所选的 Azure 区域中创建一个 netapp 帐户。

3.  按照 [设置 Azure Netapp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明设置 Azure netapp 文件容量池。

    本文中提供的 HANA 体系结构在 *超* 服务级别使用单个 Azure NetApp 文件容量池。 对于 Azure 上的 HANA 工作负荷，建议使用 Azure NetApp 文件*Ultra*或*高级*[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

4.  按照将 [子网委托给 Azure Netapp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明，将子网委托给 Azure netapp 文件。

5.  按照 [为 Azure Netapp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明部署 Azure netapp 文件量。

    部署卷时，请确保选择 NFSv 4.1 版本。 将卷部署在指定的 Azure NetApp 文件子网中。 将自动分配 Azure NetApp 卷的 IP 地址。

    请记住，Azure NetApp 文件资源和 Azure Vm 必须位于同一个 Azure 虚拟网络中，或者位于对等互连 Azure 虚拟网络中。 例如，hanadb1-mnt00001、hanadb1 和 mnt00001 等，它们是 Azure NetApp 文件卷的文件路径，而 nfs://10.32.2.4/hanadb1-data-mnt00001、nfs://10.32.2.4/hanadb1-log-mnt00001 等是这些文件的路径。
    
    在**hanadb1**上

    - Volume hanadb1-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001)  
    - Volume hanadb1 mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001) 
    - Volume hanadb1 mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001) 
    
    在**hanadb2**上

    - Volume hanadb2-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001) 
    - Volume hanadb2 mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001) 
    - Volume hanadb2 mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001) 

### <a name="important-considerations"></a>重要注意事项

为 SAP HANA 纵向扩展系统创建 Azure NetApp 文件时，请注意以下事项：

- 最小容量池为 4 tib (TiB) 。
- 最小卷大小为 100 gb (GiB) 。
- Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机必须位于同一区域中的同一 Azure 虚拟网络或 [对等互连虚拟网络](../../../virtual-network/virtual-network-peering-overview.md) 中。
- 所选虚拟网络必须具有委托给 Azure NetApp 文件的子网。
- Azure NetApp 文件量的吞吐量是卷配额和服务级别的功能，如 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 当调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量符合 HANA 系统要求。
- 使用 Azure NetApp 文件 [导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以控制允许的客户端、访问类型 (读写、只读等) 。
- Azure NetApp 文件功能尚未识别区域。 目前，该功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与你的 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷的部署更近。

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>在 Azure NetApp 文件上调整 HANA 数据库的大小

Azure NetApp 文件量的吞吐量是卷大小和服务级别的一项功能，如 [Azure Netapp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。

在 Azure 中设计 SAP 基础结构时，请注意 SAP 所需的一些最低存储要求，这些要求转换为最小吞吐量特征：

- /Hana/log 250 mb/秒的读写 (MB/s) ，大小为 1 MB。
- 对于 16 MB 和 64-MB i/o 大小的/hana/data，至少读取 400 MB/s 的活动。
- 对于具有 16 MB 和 64-MB i/o 大小的/hana/data，至少 250 MB/s 的写入活动。

每 1 TiB 的卷配额的 [Azure NetApp 文件吞吐量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)为：

- 高级存储层-64 MiB/s。
- 超存储层-128 MiB/s。

为了满足/hana/data 和/hana/log 的 SAP 最小吞吐量要求和/hana/shared 的准则，建议的大小为：

|    数据量(Volume)    | 高级存储层的大小 | 超存储层的大小 | 支持的 NFS 协议 |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6.3 TiB            |          3.2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 或 v4.1    |


> [!NOTE]
> 此处所述的 Azure NetApp 文件大小调整建议旨在满足 SAP 为其基础结构提供商建议的最低要求。 在实际的客户部署和工作负载情况下，这些大小可能不够用。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。

> [!TIP]
> 你可以动态调整 Azure NetApp 文件卷的大小，而无需 *卸载* 卷、停止虚拟机或停止 SAP HANA。 这种方法可以灵活地满足应用程序的预期和无法预料的吞吐量需求。

> [!NOTE]
> 本文中用于装载/hana/shared 的所有命令都是针对 NFSv 4.1/hana/shared 卷提供的。
> 如果将/hana/shared 卷部署为 NFSv3 卷，请不要忘记调整/hana/shared for NFSv3 的 mount 命令。


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机 

首先，需要创建 Azure NetApp 文件卷。 然后执行以下步骤：

1.  创建资源组。
2.  创建虚拟网络。
3.  创建可用性集。 设置最大更新域。
4.  创建负载均衡器（内部）。 建议使用标准负载均衡器。
    选择在步骤 2 中创建的虚拟网络。
5.  创建虚拟机 1 (**hanadb1**) 。 
6.  创建虚拟机 2 (**hanadb2**) 。  
7.  创建虚拟机时，我们将不会添加任何磁盘，因为所有装入点都将位于 Azure NetApp 文件中的 NFS 共享上。 
8.  如果使用标准负载均衡器，请执行以下配置步骤：
    1.  首先创建前端 IP 池：
        1.  打开负载均衡器，选择**前端 IP 池**，然后选择“添加”。
        1.  输入新前端 IP 池的名称（例如 **hana-frontend**）。
        1.  将 " **分配** " 设置为 " **静态** " 并输入 IP 地址 (例如 **10.32.0.10**) 。
        1.  选择“确定”。
        1.  创建新前端 IP 池后，请记下池 IP 地址。
    1.  接下来创建后端池：
        1.  打开负载均衡器，选择**后端池**，然后选择“添加”。
        1.  输入新后端池的名称（例如 **hana-backend**）。
        1.  选择“添加虚拟机”。
        1.  选择“虚拟机”。
        1.  选择 SAP HANA 群集的虚拟机及其 IP 地址。
        1.  选择 **添加**。
    1.  接下来创建运行状况探测：
        1.  打开负载均衡器，选择**运行状况探测**，然后选择“添加”。
        1.  输入新运行状况探测的名称（例如 **hana-hp**）。
        1.  选择“TCP”作为协议，并选择端口 62503。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
        1.  选择“确定”。
    1.  接下来，创建负载均衡规则：
        1.  打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend、hana-backend 和 hana-hp）。
        1.  选择“HA 端口”。
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保**启用浮动 IP**。
        1.  选择“确定”。

> [!NOTE] 
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)。

9. 或者，如果你的方案指示使用基本负载均衡器，请执行以下配置步骤：
    1.  配置负载均衡器。 首先创建前端 IP 池：
        1.  打开负载均衡器，选择**前端 IP 池**，然后选择“添加”。
        1.  输入新前端 IP 池的名称（例如 **hana-frontend**）。
        1.  将 " **分配** " 设置为 " **静态** " 并输入 IP 地址 (例如 **10.32.0.10**) 。
        1.  选择“确定”。
        1.  创建新前端 IP 池后，请记下池 IP 地址。
    1.  接下来创建后端池：
        1.  打开负载均衡器，选择**后端池**，然后选择“添加”。
        1.  输入新后端池的名称（例如 **hana-backend**）。
        1.  选择“添加虚拟机”。
        1.  选择在步骤 3 中创建的可用性集。
        1.  选择 SAP HANA 群集的虚拟机。
        1.  选择“确定”。
    1.  接下来创建运行状况探测：
        1.  打开负载均衡器，选择**运行状况探测**，然后选择“添加”。
        1.  输入新运行状况探测的名称（例如 **hana-hp**）。
        1.  选择“TCP”作为协议，并选择端口 625**03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
        1.  选择“确定”。
    1.  对于 SAP HANA 1.0，请创建负载均衡规则：
        1.  打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb-3**03**15）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
        1.  将“协议”保留设置为“TCP”，输入端口 3**03**15。 
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保**启用浮动 IP**。
        1.  选择“确定”。
        1.  针对端口 3**03**17 重复上述步骤。
    1.  对于 SAP HANA 2.0，请为系统数据库创建负载均衡规则：
        1.  打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb-3**03**13）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
        1.  将“协议”保留设置为“TCP”，输入端口 3**03**13。 
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保**启用浮动 IP**。
        1.  选择“确定”。
        1.  针对端口 3**03**14 重复上述步骤。
    1.  对于 SAP HANA 2.0，请先为租户数据库创建负载均衡规则：
        1.  打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
        1.  输入新负载均衡器规则的名称（例如 hana-lb-3**03**40）。
        1.  选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 **hana-frontend**）。
        1.  将“协议”保留设置为“TCP”，输入端口 3**03**40。 
        1.  将“空闲超时”增大到 30 分钟。
        1.  确保**启用浮动 IP**。
        1.  选择“确定”。
        1.  针对端口 3**03**41 和 3**03**42 重复上述步骤。

有关 SAP HANA 所需的端口的详细信息，请参阅[SAP HANA 租户数据库](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6)指南或 SAP 说明[2388694](https://launchpad.support.sap.com/#/notes/2388694)中的[连接到租户数据库](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html)的章节。

> [!IMPORTANT]
> 请勿在放置于 Azure 负载均衡器之后的 Azure VM 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数“net.ipv4.tcp_timestamps”设置为“0”。 有关详细信息，请参阅[负载均衡器运行状况探测](../../../load-balancer/load-balancer-custom-probe-overview.md)。 另请参阅 SAP 说明 [2382421](https://launchpad.support.sap.com/#/notes/2382421)。

## <a name="mount-the-azure-netapp-files-volume"></a>装载 Azure NetApp 文件卷

1. **[A]** 创建 HANA 数据库卷的装入点。 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** 验证 NFS 域设置。 请确保将该域配置为默认的 Azure NetApp 文件域，即 **defaultv4iddomain.com** 并且映射设置为 "无 **人**"。

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > 确保在 VM 上设置/etc/idmapd.conf 中的 NFS 域，使其与 Azure NetApp 文件上的默认域配置匹配： **defaultv4iddomain.com**。 如果 NFS 客户端上的域配置不匹配 (例如，VM) 和 NFS 服务器，即 Azure NetApp 配置，则在 Vm 上装载的 Azure NetApp 卷上的文件权限将显示为无人。
    

3. **[1]** 在节点1上装入节点特定的卷 (**hanadb1**)  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** 在节点2上装入节点特定的卷 (**hanadb2**) 
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** 验证是否已使用 NFS 协议版本 NFSv4 装载所有 HANA 卷。

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** 验证 **nfs4_disable_idmapping**。 它应设置为 **Y**。若要创建 **nfs4_disable_idmapping** 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   有关如何更改 **nfs_disable_idmapping** 参数的详细信息，请参阅 [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) 。 


## <a name="sap-hana-installation"></a>SAP HANA 安装

1. **[A]** 为所有主机设置主机名解析。

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts 文件。 此示例演示如何使用 /etc/hosts 文件。 请替换以下命令中的 IP 地址和主机名：

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL For HANA 配置

   根据 RHEL 版本，按以下 SAP 说明配置 RHEL

   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
   - [2777782-SAP HANA DB：适用于 RHEL 8 的建议 OS 设置](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux：运行用 GCC 1.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux：运行用 GCC 7. x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux：运行用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** 安装 SAP HANA

   从 HANA 2.0 SPS 01 开始，MDC 是默认选项。 安装 HANA 系统时，将同时创建 SYSTEMDB 和具有相同 SID 的租户。 在某些情况下，不需要默认租户。 如果你不想要创建初始租户和安装，则可以按照 SAP 说明 [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    从 HANA DVD 运行 hdblcm 程序。 在提示符下输入以下值：  
    选择 "安装"：输入 **1** (安装)   
    选择要安装的其他组件：输入 **1**。  
    输入安装路径 [/hana/shared]：按 Enter 接受默认值  
    输入本地主机名 [..]：按 Enter 接受默认值  
    是否要将其他主机添加到系统?  (y/n) [n]： **n**  
    输入 SAP HANA 系统 ID：输入 **HN1**。  
    输入实例编号 [00]：输入 **03**  
    选择数据库模式/输入索引 [1]：按 Enter 接受默认值  
    选择系统使用情况/输入索引 [4]：为自定义) 输入 **4** 个 (  
    输入数据卷的位置 [/hana/data]：按 Enter 接受默认值  
    输入日志卷的位置 [/hana/log]：按 Enter 接受默认值  
    是否限制最大内存分配? [n]：按 Enter 接受默认值  
    输入主机 ' ... ' 的证书主机名[...]：按 Enter 接受默认值  
    输入 SAP Host Agent User (sapadm) Password：输入主机代理用户密码  
    确认 SAP 主机代理用户 (sapadm) 密码：再次输入主机代理用户密码以确认  
    输入系统管理员 (hn1adm) 密码：输入系统管理员密码  
    确认系统管理员 (hn1adm) 密码：再次输入系统管理员密码以确认  
    输入系统管理员主目录 [/usr/sap/HN1/home]：按 Enter 接受默认值  
    输入系统管理员登录 Shell [/bin/sh]：按 Enter 接受默认值  
    输入系统管理员用户 ID [1001]：按 Enter 接受默认值  
    输入用户组 (sapsys) 的 ID [79]：按 Enter 以接受默认值  
    输入数据库用户 (系统) 密码：输入数据库用户密码  
    确认数据库用户 (系统) 密码：再次输入数据库用户密码以确认  
    重新引导计算机后是否重新启动系统? [n]：按 Enter 接受默认值  
    是否继续? (y/n)：验证摘要。 输入 **y** 以继续  

4. [A] 升级 SAP 主机代理****

   从 [SAP 软件中心](https://launchpad.support.sap.com/#/softwarecenter)下载最新的 SAP 主机代理存档，并运行以下命令来升级代理。 替换存档的路径，使其指向已下载的文件：

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** 配置防火墙

   为 Azure 负载均衡器探测端口创建防火墙规则。

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>配置 SAP HANA 系统复制

按照设置 [SAP HANA 系统复制](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) 中的步骤配置 SAP HANA 系统复制。 

## <a name="cluster-configuration"></a>群集配置

本部分介绍在使用 Azure NetApp 文件在 NFS 共享上安装 SAP HANA 时，群集无缝运行所需的步骤。 

### <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照在 Azure 中的 [Red Hat Enterprise Linux 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的步骤为此 HANA 服务器创建基本 Pacemaker 群集。

### <a name="configure-filesystem-resources"></a>配置 filesystem 资源

在此示例中，每个群集节点都有其自己的 HANA NFS 文件系统/hana/shared、/hana/data 和/hana/log   

1. **[1]** 将群集置于维护模式。

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** 创建 **Hanadb1** 装载的文件系统资源。

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** 创建 **Hanadb2** 装载的文件系统资源。

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` 将属性添加到监视操作，以便每个监视器在文件系统上执行读/写测试。 如果没有此属性，则监视操作仅验证是否已装载文件系统。 这可能是一个问题，因为在连接丢失时，文件系统可能会保持装载（尽管无法访问）。

    `on-fail=fence` 还会将属性添加到监视操作。 使用此选项时，如果监视操作在节点上失败，则会立即隔离该节点。 如果没有此选项，则默认行为是停止依赖于失败资源的所有资源，然后重新启动失败的资源，然后启动依赖于失败资源的所有资源。 此行为不仅会在 SAPHana 资源依赖于失败的资源时花费很长时间，还可以完全失败。 如果承载 HANA 可执行文件的 NFS 服务器不可访问，则无法成功停止 SAPHana 资源。

4. **[1]** 配置位置约束

   配置位置约束，以确保管理 hanadb1 独有装载的资源绝不能在 hanadb2 上运行，反之亦然。

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never`设置了选项，因为每个节点的唯一装载共享同一装入点。 例如， `hana_data1` 使用装入点 `/hana/data` ，并且 `hana_data2` 还使用装入点 `/hana/data` 。 如果在群集启动时检查了资源状态，这会导致探测操作误报，这可能会导致不必要的恢复行为。 可以通过将 `resource-discovery=never`

5. **[1]** 配置属性资源

   配置属性资源。 如果节点的所有 NFS 装载 (/hana/data、/hana/log 和/hana/data) 均已装入，则这些属性将设置为 true，否则将设置为 false。

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** 配置位置约束

   配置位置约束，以确保 hanadb1's 属性资源从不在 hanadb2 上运行，反之亦然。

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** 创建排序约束

   配置排序约束，以便仅在装入节点的所有 NFS 装载后才开始节点的属性资源。
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > 如果配置中包含文件系统、组以外的文件系统，请 `hanadb1_nfs` `hanadb2_nfs` 包括 `sequential=false` 选项，以便在文件系统之间没有排序依赖关系。 所有文件系统都必须在之前开始 `hana_nfs1_active` ，但是它们不需要以彼此相对的顺序启动。 有关详细信息，请如何实现参阅在[Pacemaker 群集中的 Scale-Up 中配置 SAP HANA 系统复制](https://access.redhat.com/solutions/5156571)。

### <a name="configure-sap-hana-cluster-resources"></a>配置 SAP HANA 群集资源

1. 按照 [创建 SAP HANA 群集资源](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) 中的步骤在群集中创建 SAP HANA 资源。 创建 SAP HANA 资源后，需要在 (NFS 装载 SAP HANA 的资源和文件系统之间创建位置规则约束) 

2. **[1]** 配置 SAP HANA 资源与 NFS 装载之间的约束

   将设置位置规则约束，以便仅当节点的所有 NFS 装载均已装载时，SAP HANA 资源才能在该节点上运行。

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   检查群集和所有资源的状态
   > [!NOTE]
   > 本文包含对字词 *从属*的引用，这是 Microsoft 不再使用的术语。 从软件中删除该字词后，我们会将其从本文中删除。
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="test-the-cluster-setup"></a>测试群集设

本部分介绍如何测试设置。 

1. 在开始测试之前，请确保 Pacemaker 没有任何 (任何失败的操作) 的情况下，不存在任何意外的位置约束 (例如，迁移测试) 的 leftovers，而 HANA 系统复制是同步状态，例如，使用 systemReplicationStatus：

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. 在节点无法访问 NFS 共享 (/hana/shared 时验证故障情况的群集配置) 

   SAP HANA 资源代理依赖于二进制文件，它们存储在 `/hana/shared` 中，以便在故障转移过程中执行操作。 `/hana/shared`在提供的方案中，将通过 NFS 装载文件系统。  
   很难模拟失败，其中其中一个服务器失去对 NFS 共享的访问权限。 可以执行的测试是以只读方式重新装载文件系统。
   如果 active 节点上的访问权限丢失，此方法会验证群集是否能够进行故障转移 `/hana/shared` 。     


   **预期结果：** 在成为 `/hana/shared` 只读文件系统时，对 `OCF_CHECK_LEVEL` `hana_shared1` 文件系统执行读/写操作的资源的属性将失败，因为它无法在文件系统上写入任何内容并将执行 HANA 资源故障转移。  当 HANA 节点失去对 NFS 共享的访问权限时，会出现相同的结果。 

   开始测试之前的资源状态：

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   可以使用以下命令将/hana/shared 置于活动群集节点上的只读模式：

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 将根据 stonith (上的操作设置重新启动或关机 `pcs property show stonith-action`) 。  服务器 (hanadb1) 关闭后，HANA 资源将移动到 hanadb2。 可以从 hanadb2 检查群集的状态。

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   建议通过同时执行在 [RHEL 上安装 SAP HANA 系统复制](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)中所述的测试，来全面测试 SAP HANA 群集配置。