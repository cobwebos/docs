---
title: 部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项 | Microsoft Docs
description: 部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdf5685ad8072175bdabf8938ef293bed6f5cc13
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075195"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[planning-guide]:planning-guide.md 

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

本指南是介绍如何在 Microsoft Azure 上实施和部署 SAP 软件的文档的一部分。 阅读本指南之前，请先阅读[规划和实施指南][planning-guide]。 本文介绍使用 Azure 服务架构 (IaaS) 功能在 Microsoft Azure 虚拟机 (VM) 上部署 SAP 相关 DBMS 系统时通用的各方面内容。

本文是对 SAP 安装文档和 SAP 说明的补充，这些文档代表在给定平台上安装和部署 SAP 软件的主要资源。

本文介绍在 Azure VM 中运行 SAP 相关 DBMS 系统的注意事项。 其中很少涉及有关特定 DBMS 系统的参考信息。 在本白皮书内，改在本文之后讨论特定的 DBMS 系统。

## <a name="definitions-upfront"></a>定义预释
整个文档使用以下术语：

* IaaS：服务架构。
* PaaS：平台即服务。
* SaaS：软件即服务。
* SAP 组件：单个 SAP 应用程序，例如 ECC、BW、Solution Manager 或 EP。  SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以是不基于 NetWeaver 的应用程序，例如业务对象。
* SAP 环境：以逻辑方式组合在一起，用于执行开发、QAS、培训、DR 或生产等业务功能的一个或多个 SAP 组件。
* SAP 布局：表示客户 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
* SAP 系统：诸如 SAP ERP 开发系统、SAP BW 测试系统、SAP CRM 生产系统等的 DBMS 层与应用程序层的组合。在 Azure 部署中，不支持在本地和 Azure 之间分割这两个层。 这意味着，某个 SAP 系统要么部署在本地，要么部署在 Azure 中。 但是，可以将 SAP 布局中的不同系统部署到 Azure 或本地。 例如，可以将 SAP CRM 开发系统和测试系统部署在 Azure 中，同时将 SAP CRM 生产系统部署在本地。
* 跨界：描述这样一种方案：将 VM 部署到在本地数据中心与 Azure 之间建立了站点到站点、多站点或 ExpressRoute 连接的 Azure 订阅。 在一般的 Azure 文档中，此类部署也称为跨界方案。 建立连接是为了将本地域、本地 Active Directory 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器，并可在这些 VM 上运行服务（例如 DBMS 服务）。 可以在部署于本地的 VM 与部署于 Azure 的 VM 之间进行通信和名称解析。 这是在 Azure 上部署 SAP 资产最常见的方案。 有关详细信息，请参阅[规划和设计 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> SAP 生产系统支持对 SAP 系统进行这种跨界部署：运行 SAP 系统的 Azure 虚拟机是本地域的成员。 跨界配置可将部分或完整 SAP 布局部署到 Azure。 即使在 Azure 中运行完整 SAP 布局，也需要这些 VM 成为本地域和 ADS/LDAP 的一部分。 本文档的旧版本曾谈到混合 IT 方案，其中“混合”一词基本上是指本地与 Azure 之间有跨界连接。 在此方案中，“混合”还表示 Azure 中的 VM 是本地 Active Directory 的一部分。
> 
> 

有些 Microsoft 文档在描述跨界方案时稍有不同，特别是针对 DBMS HA 配置。 在 SAP 相关的文档中，跨界方案单纯是指具有站点到站点或专用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 连接，以及将 SAP 布局分布到本地与 Azure 的情况。

## <a name="resources"></a>资源
Azure 上发布了有关 SAP 工作负荷的各种文章。  建议从 [Azure 上的 SAP 工作负荷 - 入门](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)开始，然后选择感兴趣的领域

以下 SAP 说明与 Azure 上的 SAP 有关，涉及本文档中介绍的领域：

| 说明文档编号 | 标题 |
| --- | --- |
| [1928533] |Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视度量值 |
| [1409604] |Windows 上的虚拟化：增强型监视 |
| [2191498] |使用 Azure 的 Linux 上的 SAP：增强型监视 |
| [2039619] |Microsoft Azure 上使用 Oracle 数据库的 SAP 应用程序：支持的产品和版本 |
| [2233094] |DB6：Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 应用程序 — 附加信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |Red Hat Enterprise Linux 7.x：安装和升级 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle Database 12c - Linux 上的文件系统支持 |
| [1114181] |Oracle Database 11g - Linux 上的文件系统支持 |


另请阅读 [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)，其中包含适用于 Linux 的所有 SAP 说明。

应当使用过 Microsoft Azure 体系结构，并知道如何部署和操作 Microsoft Azure 虚拟机。 可在 [Azure 文档](https://docs.microsoft.com/azure/)中查看详细信息。

尽管讨论的是 IaaS，但一般而言，Windows、Linux 和 DBMS 的安装和配置基本上与在本地安装的任何虚拟机或裸机计算机相同。 不过，使用 Azure IaaS 时的一些体系结构和系统管理实施决策有所不同。 本文档旨在说明使用 Azure IaaS 时必须准备好应对的特定体系结构和系统管理差异。


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>用于 RDBMS 部署的 VM 的存储结构
为了完成本章的学习，必须先了解[部署指南][deployment-guide]的[此][deployment-guide-3]章所提供的内容。 在阅读本章之前，必须先了解并熟悉不同的 VM 系列及其差异，以及 Azure 标准存储和[高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)的差异。

对于 Azure VM 的 Azure 存储，应熟悉以下文章：

- [关于 Azure Windows VM 的磁盘存储](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [关于用于 Azure Linux VM 的磁盘存储](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds) 

在基本配置中，我们通常推荐这种结构的部署：操作系统、DBMS 和最终的 SAP 二进制文件与数据库文件分开。 因此，我们建议在 Azure 虚拟机中运行的 SAP 系统将安装包含操作系统、数据库管理系统可执行文件和 SAP 可执行文件的基础 VHD（或磁盘）。 DBMS 数据和日志文件将存储在 Azure 存储（标准或高级存储）的独立磁盘中，并以逻辑磁盘形式附加到原始的 Azure 操作系统映像 VM。 尤其是在 Linux 部署中，可能记录有不同的建议。 尤其是 SAP HANA。  

规划磁盘布局时，需要找到下列各项之间的最佳均衡：

* 数据文件数目。
* 包含文件的磁盘数目。
* 单个磁盘的 IOPS 配额。
* 每个磁盘的数据吞吐量。
* 每个 VM 大小可能的附加数据磁盘数目。
* VM 可提供的总体存储吞吐量。
* 不同的 Azure 存储类型可以提供的延迟。
* VM SLA

Azure 强制实施每个数据磁盘的 IOPS 配额。 对于 Azure 标准存储上托管的磁盘与高级存储上托管的磁盘，这些配额是不同的。 两种存储类型的 I/O 延迟也有差异。  使用高级存储提供更好的 I/O 延迟。 每种不同的 VM 类型可附加的数据磁盘数目有限。 另一个限制是，只有特定的 VM 类型可以使用 Azure 高级存储。 因此，针对特定 VM 类型的决策可能不只受到 CPU 和内存需求影响，还受到 IOPS、延迟和磁盘吞吐量需求影响，这些需求通常通过磁盘数目或高级存储磁盘的类型来调整。 特别是在高级存储中，磁盘的大小可能还取决于每个磁盘需要达到的 IOPS 数目和吞吐量。

> [!NOTE]
> 对于 DBMS 部署，强烈建议将高级存储用于任何数据、事务日志或重做文件。 因此，部署生产还是非生产系统并不重要。

> [!NOTE]
> 若要利用 Azure 的唯一[单个 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，附加的所有磁盘的类型需要是 Azure 高级存储，包括基础 VHD。
>

数据库文件和日志/重做文件的放置以及所使用的 Azure 存储类型应该根据 IOPS、延迟和吞吐量需求来定义。 为了有足够的 IOPS，可能必须使用多个磁盘或使用更大的高级存储磁盘。 如果使用多个磁盘，需跨磁盘构建软件带区，其中包含数据文件或日志/重做文件。 在这种情况下，基础高级存储磁盘的 IOPS 和磁盘吞吐量 SLA 或 Azure 标准存储磁盘的最大可达到 IOPS 是针对生成的带区集累积的。 

如上所述，如果 IOPS 要求超过单个 VHD 可提供的数量，需要平衡许多 VHD 中数据库文件所需的 IOPS 数量。 分散磁盘上 IOPS 负载的最简单方式是在不同的磁盘构建一个软件带区。 然后在从软件带区划分出的 LUN 上放置多个 SAP DBMS 数据文件。 带区中的磁盘数量由 IOP 需求、磁盘吞吐量需求和卷需求决定。 


- - -
> ![Windows][Logo_Windows] Windows
> 
> 我们建议使用 Windows 存储空间跨多个 Azure VHD 创建此类带区集。 建议至少使用 Windows Server 2012 R2 或 Windows Server 2016。
> 
> ![Linux][Logo_Linux] Linux
> 
> 仅支持使用 MDADM 和 LVM（逻辑卷管理器）在 Linux 上构建软件 RAID。 有关详细信息，请阅读以下文章：
> 
> - [使用 MDADM 在 Linux 上配置软件 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - [使用 LVM 在 Azure 中的 Linux VM 上配置 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
> 
> 

- - -
 
> [!NOTE]
> 由于 Azure 存储保留 VHD 的三个映像，因此条带化时配置冗余无意义。 只需配置条带化，I/O 即可分布在不同的 VHD。
>

### <a name="managed-or-non-managed-disks"></a>托管或非托管磁盘
Azure 存储帐户不只是一种管理构造，还是一个具有各种限制的主体。 Azure 标准存储帐户和 Azure 高级存储帐户的限制是有差异的。 [Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)一文列出了精确的功能和限制

对于 Azure 标准存储，要注意每个存储帐户都有 IOPS 限制（[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)一文中包含“总请求速率”的行）。 此外，每个 Azure 订阅有对存储帐户数量的初始限制。 因此，需要在不同存储帐户中平衡较大 SAP 情景的 VHD，避免达到这些存储帐户的限制。 对于具有数千个 VHD 的几百个虚拟机而言，这是一件繁琐的工作。 

由于不建议将 Azure 标准存储用于 DBMS 部署并结合使用 SAP 工作负载，因此对 Azure 标准存储的参考和建议仅限于此简短的[文章](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

为避免在不同 Azure 存储帐户中执行规划和部署 VHD 的管理工作，Microsoft 在 2017 年推出了[托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 托管磁盘适用于 Azure 标准存储和 Azure 高级存储。 与非托管磁盘相比，托管磁盘的主要优势如下：

- 对于托管磁盘，Azure 在部署时跨不同存储帐户分布不同的 VHD，因此可避免达到 Azure 存储帐户在数据量、I/O 吞吐量和 IOPS 方面的限制。
- Azure 存储使用托管磁盘遵循 Azure 可用性集的概念，部署基础 VHD 和附加到不同故障的 VM 的磁盘，并更新域（如果 VM 属于 Azure 可用性集）。


> [!IMPORTANT]
> 鉴于 Azure 托管磁盘的优势，强烈建议将 Azure 托管磁盘用于一般 DBMS 部署和 SAP 部署。
>

若要从非托管磁盘转换为托管磁盘，请参阅以下文章：

- [将 Windows 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [将 Linux 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和数据磁盘的缓存
将磁盘装载到 VM 时，可以选择是否缓存 VM 与位于 Azure 存储中的磁盘之间的 I/O 流量。 Azure 标准存储和高级存储针对这种缓存类型采用两种不同的技术。 

以下建议假设标准 DBMS 的 I/O 特征如下所示：

- 它主要是针对数据库的数据文件的读取工作负载。 此类读取对于 DBMS 系统是性能关键型的
- 基于检查点或持续流，突发中会产生针对数据文件的写入。 不过，平均每天的写入比读取少。 与从数据文件读取相反，这些写入是异步的，且不会阻止用户事务。
- 事务日志或重做文件中几乎没有读取。 但执行事务日志备份时的大型 I/O 除外。 
- 针对事务或重做日志文件的主要负载是写入。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。
- 需要以一种可靠的方式在磁盘上保留所有写入

对于 Azure 标准存储，可能的缓存类型如下：

* 无
* 读取
* 读取/写入

为了获得一致且稳定的性能，应在 Azure 标准存储上，针对包含 DBMS 相关数据文件、日志/重做文件和表空间的所有磁盘，将缓存设置为“无”。 基础 VHD 的缓存可以保留默认值。

对于 Azure 高级存储，提供下列缓存选项：

* 无
* 读取 
* 读取/写入 
* 无 + 写入加速器（仅针对 Azure M 系列 VM）
* 读取 + 写入加速器（仅针对 Azure M 系列 VM）

在 Azure 高级存储中，建议对 SAP 数据库的**数据文件使用“读取缓存”**，**对磁盘的日志文件选择“不缓存”**。

对于 M 系列部署，强烈建议将 Azure 写入加速器用于 DBMS 部署。 有关详细信息、Azure 写入加速器的限制和部署，请参阅文档[写入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。 


### <a name="azure-non-persistent-disks"></a>Azure 非永久磁盘
部署 VM 后，Azure VM 提供非永久磁盘。 如果 VM 重启，这些驱动器上的所有内容将被擦除。因此，数据库的数据文件和日志/重做文件决不能放置在这些非永久性驱动器上。 但一些数据库例外，在此情况下，这些非永久性驱动器对于 tempdb 和临时表空间可能是适合的。 但是，应避免将这些驱动器用于 A 系列 VM，因为这些非永久性驱动器在该 VM 系列中的吞吐量有限。 有关进一步详细信息，请参阅[了解 Microsoft Azure 虚拟机上的临时驱动器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)一文

- - -
> ![Windows][Logo_Windows] Windows
> 
> Azure VM 中的驱动器 D:\ 是一个非持久性驱动器，由 Azure 计算节点上的部分本地磁盘提供支持。 非持久性意味着，当 VM 重新启动时，将丢失对 D:\ 驱动器上的内容所做的任何更改。 “任何更改”是指已存储的文件、已创建的目录、已安装的应用程序等等。
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM 会在 /mnt/resource 上自动装载一个非持久性驱动器，该驱动器由 Azure 计算节点上的本地磁盘提供支持。 非持久性意味着，当 VM 重新启动时，将丢失对 /mnt/resource 中的内容所做的任何更改。 “任何更改”是指已存储的文件、已创建的目录、已安装的应用程序等等。
> 
> 

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 存储复原
Microsoft Azure 存储将基础 VHD（含 OS）以及附加磁盘或 BLOB 存储到至少三个不同的存储节点。 这称为本地冗余存储 (LRS)。 LRS 是 Azure 中所有存储类型的默认设置。 

此外，还有一些其他冗余方法，请参阅 [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)一文。

> [!NOTE]
>自 Azure 高级存储（这是针对 DBMS VM 和存储数据库和日志/重做文件的磁盘的建议存储类型）起，LRS 是唯一可用的方法。 因此，需要配置数据库方法（例如 SQL Server Always On、Oracle Data Guard 或 HANA 系统复制），将数据库数据复制到其他 Azure 区域或其他 Azure 可用性区域。


> [!NOTE]
> 对于 DBMS 部署，不建议使用 Azure 标准存储的异地冗余存储，因为它会严重影响性能，且不遵循附加到 VM 的不同 VHD 中的写入顺序。 由于不遵循跨不同 VHD 的写入顺序，因此如果数据库和日志/重做文件分布在源 VM 端的不同 VHD 中（大多数情况是这样），则极有可能在复制目标端的不一致数据库中结束。

 

## <a name="vm-node-resiliency"></a>VM 节点复原
Azure 平台提供 VM 的不同 SLA。 有关详细信息，可在[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 的最新版本中找到。 由于 DBMS 层通常是 SAP 系统的可用性关键部分，因此用户需要熟悉以下概念：可用性集、可用性区域和维护事件。 以下文章介绍所有这些概念：[在 Azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)和[在 Azure 中管理 Linux 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。  

针对具有 SAP 工作负载的生产 DBMS 方案的最低建议如下：

- 在同一 Azure 区域的一个单独的可用性集中部署两个 VM。
- 这两个 VM 将在同一 Azure VNet 中运行，且会让 NIC 从同一子网中附加。
- 使用数据库方法来保留第二个 VM 的热备用服务器。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA 系统复制。

此外，可以在另一个 Azure 区域中部署第三个 VM，使用相同数据库方法在另一个 Azure 区域中提供异步副本。

此[教程](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)演示了设置 Azure 可用性集的方法。



## <a name="azure-network-considerations"></a>Azure 网络注意事项 
在大型 SAP 部署中，建议使用 [Azure 虚拟数据中心](https://docs.microsoft.com/azure/networking/networking-virtual-datacenter)的蓝图用于 VNet 配置以及向其组织的不同部分分配权限和角色。

数百个客户部署产生了一系列最佳做法：

- 将 SAP 应用程序部署到的 VNet，不可访问 Internet。
- 数据库 VM 在与应用程序层相同的 VNet 中运行。
- VNet 中的 VM 具有专用 IP 地址的静态分配。 有关参考，请参阅 [Azure 中的 IP 地址类型和分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- DBMS VM 之间的路由限制不是由安装在本地 DBMS VM 上的防火墙设置。 流量路由是由 [Azure 网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) 定义
- 为了将流量隔离和分离到 DBMS VM，向 VM 分配不同的 NIC。 其中每个 NIC 具有不同 IP 地址，每个 NIC 分配到不同 VNet 子网，这具有不同的 NSG 规则。 请记住，隔离或分离网络流量只是路由的一种方法，不允许设置网络吞吐量配额。

在 Azure 可用性集中将两个 VM 用于生产 DBMS 部署，以及 SAP 应用程序层的单独路由和两个 DBMS VM 的管理和操作流量，大概的关系图如下：

![两个子网中两个 VM 的关系图](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>用于重定向流量的 Azure 负载均衡器
SQL Server Always On 或 HANA 系统复制等功能中使用的专用虚拟 IP 地址的使用需要配置 Azure 负载均衡器。 Azure 负载均衡器通过探测端口确定活动的 DBMS 节点，并专门将流量路由到该活动的数据库节点。 在数据库节点故障转移时，无需重新配置 SAP 应用程序。 最常用的 SAP 应用程序体系结构将针对专用虚拟 IP 地址重新连接。 同时 Azure 负载均衡器针对专用虚拟 IP 地址将流量重定向到第二个节点，以此作为对节点故障转移的响应。

Azure 提供两个不同的[负载均衡器 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 基本 SKU 和标准 SKU。 除非想要跨 Azure 可用性区域部署，基本负载均衡器 SKU 即可满足需要。 

DBMS VM 和 SAP 应用程序层之间的流量是否始终通过 Azure 负载均衡器路由？ 这取决于配置负载均衡器的方式。 此时，传入 DBMS VM 的流量将始终通过 Azure 负载均衡器路由。 从 DBMS VM 传出到应用程序层 VM 的流量路由取决于 Azure 负载均衡器的配置。 负载均衡器提供 DirectServerReturn 选项。 如果已配置此选项，从 DBMS VM 传到 SAP 应用程序层的流量不会通过 Azure 负载均衡器路由。 它将直接传到应用程序层。 如果未配置 DirectServerReturn，则返回 SAP 应用程序层的流量将通过 Azure 负载均衡器路由

建议结合 SAP 应用程序层和 DBMS 层之间的 Azure 负载均衡器配置 DirectServerReturn，以减少两个层之间的网络延迟

设置此类配置的一个示例是[此文](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)中发布的有关 SQL server Always On 的内容。

如果选择将已发布的 github JSON 模板用作 Azure 中 SAP 基础结构部署的参考，应了解此[ SAP 3 层系统模板](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)。 在此模板中，你将了解 Azure 负载均衡器的正确设置。

### <a name="azure-accelerated-networking"></a>Azure 加速网络
为了进一步减少 Azure VM 之间的网络延迟，强烈建议在为 SAP 工作负载部署 Azure VM 时，选择 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)选项。 尤其是对于 SAP 应用程序层和 SAP DBMS 层。 

> [!NOTE]
> 并非所有 VM 类型都支持加速网络。 参考文章列出了支持加速网络的 VM 类型。 
>  

- - -
> ![Windows][Logo_Windows] Windows
> 
> 对于 Windows，请参阅[创建具有加速网络的 Windows 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)一文，了解相关概念，以及如何部署具有加速网络的 VM
> 
> ![Linux][Logo_Linux] Linux
> 
> 对于 Linux，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)一文，详细了解 Linux 分发。 
> 
> 

- - -

> [!NOTE]
> SUSE、Red Hat 和 Oracle Linux 的最新版本支持加速网络。 较早版本（如 SLES 12 SP2 或 RHEL 7.2）不支持 Azure 加速网络 
>  


## <a name="deployment-of-host-monitoring"></a>部署主机监视
若要将 Azure 虚拟机中的 SAP 应用程序用于生产用途，SAP 需要能够从运行 Azure 虚拟机的物理主机获取主机监视数据。 需要有特定的 SAP 主机代理补丁级别，才能在 SAPOSCOL 和 SAP 主机代理中启用此功能。 SAP 说明 [1409604] 中介绍了确切的补丁级别。

有关向 SAPOSCOL 和 SAP 主机代理提供主机数据的组件部署以及这些组件的生命周期管理的详细信息，请参阅[部署指南][deployment-guide]


## <a name="next-steps"></a>后续步骤
有关特定 DBMS 的文档，请查阅以下文章：

- [适用于 SAP 工作负荷的 SQL Server Azure 虚拟机 DBMS 部署](dbms_guide_sqlserver.md)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](dbms_guide_oracle.md)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](dbms_guide_ibm.md)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和内容服务器部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP HANA 操作指南](hana-vm-operations.md)
- [Azure 虚拟机的 SAP HANA 高可用性](sap-hana-availability-overview.md)
- [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)

