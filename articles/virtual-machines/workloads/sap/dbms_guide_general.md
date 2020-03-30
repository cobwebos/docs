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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101364"
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

本指南是有关如何在 Microsoft Azure 上实现和部署 SAP 软件的文档的一部分。 在阅读本指南之前，请阅读[规划和实施指南][planning-guide]。 本文档使用 Azure 基础结构作为服务 （IaaS） 功能，介绍 Microsoft Azure 虚拟机 （VM） 上与 SAP 相关的 DBMS 系统的通用部署方面。

本文补充了 SAP 安装文档和 SAP 说明，它们代表了在给定平台上安装和部署 SAP 软件的主要资源。

本文介绍在 Azure VM 中运行 SAP 相关 DBMS 系统的注意事项。 其中很少涉及有关特定 DBMS 系统的参考信息。 相反，在本文之后，在本文中处理特定的 DBMS 系统。

## <a name="definitions"></a>定义
在整个文档中，使用这些术语：

* **IaaS**：基础设施即服务。
* **PaaS**： 平台即服务。
* **SaaS**： 软件即服务.
* **SAP 组件**：单个 SAP 应用程序，如 ERP 中央组件 （ECC）、业务仓库 （BW）、解决方案管理器或企业门户 （EP）。 SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以基于基于 NetWeaver 的非应用程序（如业务对象）。
* **SAP 环境**：一个或多个 SAP 组件逻辑分组以执行业务功能，如开发、质量保证、培训、灾难恢复或生产。
* **SAP 横向：** 此术语是指客户 IT 环境中的整个 SAP 资产。 SAP 环境包括所有生产和非生产环境。
* **SAP 系统**：DBMS 层和应用程序层的组合，例如 SAP ERP 开发系统、SAP 业务仓库测试系统或 SAP CRM 生产系统。 在 Azure 部署中，不支持在本地和 Azure 之间划分这两个图层。 因此，SAP 系统要么部署在本地，要么部署在 Azure 中。 您可以在 Azure 或本地部署 SAP 横向的不同系统。 例如，您可以在 Azure 中部署 SAP CRM 开发和测试系统，但在本地部署 SAP CRM 生产系统。
* **跨界**：描述将 VM 部署到在本地数据中心和 Azure 之间具有站点到站点、多站点或 Azure 快速路由连接的 Azure 订阅的方案。 在一般的 Azure 文档中，此类部署也称为跨界方案。 

    建立连接是为了将本地域、本地 Active Directory 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器并在这些 VM 上运行服务，如 DBMS 服务。 可以在部署于本地的 VM 与部署于 Azure 的 VM 之间进行通信和名称解析。 此方案是用于在 Azure 上部署 SAP 资产的最常见方案。 有关详细信息，请参阅[规划和设计 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> SAP 系统的跨界部署是运行 SAP 系统的 Azure 虚拟机是本地域的成员，并且支持生产 SAP 系统。 跨界配置可将部分或完整 SAP 布局部署到 Azure。 即使在 Azure 中运行完整的 SAP 环境，也要求这些 VM 成为本地域和活动目录/LDAP 的一部分。 
>
> 在文档的早期版本中，提到了混合 IT 方案。 术语*混合*植根于本地和 Azure 之间存在跨界连接这一事实。 在这种情况下，混合还意味着 Azure 中的 VM 是本地活动目录的一部分。
>
>

某些 Microsoft 文档对跨界方案描述的方式略有不同，尤其是对于 DBMS 高可用性配置。 在 SAP 相关文档中，跨界方案可归结为站点到站点或专用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)连接以及分布在本地和 Azure 之间的 SAP 横向。

## <a name="resources"></a>资源
Azure 上的 SAP 工作负荷上还有其他文章。 从[Azure 上的 SAP 工作负荷开始：开始](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)，然后选择感兴趣的区域。

以下 SAP 说明与 Azure 上的 SAP 有关本文档涵盖的区域。

| 说明文档编号 | Title |
| --- | --- |
| [1928533] |Azure 上的 SAP 应用程序：受支持的产品和 Azure VM 类型 |
| [2015553] |微软 Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视指标 |
| [1409604] |Windows 上的虚拟化：增强的监视 |
| [2191498] |使用 Azure 在 Linux 上 SAP：增强的监视 |
| [2039619] |使用 Oracle 数据库在 Microsoft Azure 上的 SAP 应用程序：支持的产品和版本 |
| [2233094] |DB6：使用 IBM DB2 进行 Linux、UNIX 和 Windows 的 Azure 上的 SAP 应用程序：其他信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |红帽企业 Linux 7.x：安装和升级 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle 数据库 12c：Linux 上的文件系统支持 |
| [1114181] |甲骨文数据库 11g：Linux 上的文件系统支持 |


有关所有 SAP Linux 说明的信息，请参阅[SAP 社区 wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

您需要了解 Microsoft Azure 体系结构以及如何部署和操作 Microsoft Azure 虚拟机。 有关详细信息，请参阅[Azure 文档](https://docs.microsoft.com/azure/)。

通常，Windows、Linux 和 DBMS 的安装和配置基本上与您在本地安装的任何虚拟机或裸机相同。 在使用 Azure IaaS 时，有一些体系结构和系统管理实现决策不同。 本文档介绍在使用 Azure IaaS 时要准备的特定体系结构和系统管理差异。


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>用于 RDBMS 部署的 VM 的存储结构
要遵循本章，请阅读并理解[部署指南][deployment-guide][本章][deployment-guide-3]中提供的信息。 在阅读本章之前，您需要了解并了解不同的 VM 系列以及标准和高级存储之间的区别。 

要了解 Azure VM 的 Azure 存储，请参阅：

- [Azure Windows VM 的托管磁盘简介](../../windows/managed-disks-overview.md)。
- [Azure Linux VM 的托管磁盘简介](../../linux/managed-disks-overview.md)。

在基本配置中，我们通常建议部署结构，其中操作系统、DBMS 和最终的 SAP 二进制文件与数据库文件分开。 我们建议在 Azure 虚拟机中运行的 SAP 系统具有基本 VHD 或磁盘，并安装操作系统、数据库管理系统可执行文件和 SAP 可执行文件。 

DBMS 数据和日志文件存储在标准存储或高级存储中。 它们存储在单独的磁盘中，并作为逻辑磁盘附加到原始 Azure 操作系统映像 VM。 对于 Linux 部署，将记录不同的建议，尤其是针对 SAP HANA。

规划磁盘布局时，请找到以下项目之间的最佳平衡：

* 数据文件数目。
* 包含文件的磁盘数目。
* 单个磁盘的 IOPS 配额。
* 每个磁盘的数据吞吐量。
* 每个 VM 大小可能的附加数据磁盘数目。
* VM 可提供的总体存储吞吐量。
* 不同的 Azure 存储类型可以提供的延迟。
* VM SL。

Azure 强制实施每个数据磁盘的 IOPS 配额。 对于托管在标准存储和高级存储上的磁盘，这些配额是不同的。 两种存储类型的 I/O 延迟也有差异。 高级存储提供更好的 I/O 延迟。 

每种不同的 VM 类型都有数量有限的数据磁盘，您可以附加这些磁盘。 另一个限制是，只有某些 VM 类型才能使用高级存储。 通常，您决定根据 CPU 和内存要求使用特定的 VM 类型。 您还可能考虑通常随磁盘数或高级存储磁盘类型进行缩放的 IOPS、延迟和磁盘吞吐量要求。 IOPS 的数量和每个磁盘要实现的吞吐量可能决定磁盘大小，尤其是高级存储。

> [!NOTE]
> 对于 DBMS 部署，我们建议对任何数据、事务日志或重做文件使用高级存储。 是否要部署生产系统还是非生产系统并不重要。

> [!NOTE]
> 为了从 Azure 唯一的单[一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)中受益，连接的所有磁盘都必须是高级存储类型，其中包括基本 VHD。

> [!NOTE]
> 不支持在位于 Azure 数据中心附近的第三方数据中心的存储硬件上托管 SAP 数据库的主要数据库文件（如数据和日志文件）。 对于 SAP 工作负荷，SAP 数据库的数据和事务日志文件仅支持表示为本机 Azure 服务的存储。

数据库文件和日志和重做文件的位置以及您使用的 Azure 存储类型由 IOPS、延迟和吞吐量要求定义。 要有足够的 IOPS，您可能被迫使用多个磁盘或使用更大的高级存储磁盘。 如果使用多个磁盘，请跨包含数据文件或日志和重做文件的磁盘构建软件条带。 在这种情况下，基础高级存储磁盘的 IOPS 和磁盘吞吐量 SL 或标准存储磁盘的最大可实现 IOPS 是生成的条带集累积的。

如上所述，如果您的 IOPS 要求超过单个 VHD 可以提供的数量，请平衡数据库文件所需的 IOPS 数量，跨越多个 VHD。 跨磁盘分发 IOPS 负载的最简单方法是在不同的磁盘上构建软件条带。 然后，将 SAP DBMS 的一些数据文件放在从软件条带中雕刻的 LUN 上。 条带中的磁盘数由 IAP 需求、磁盘吞吐量要求和卷需求驱动。


---
> ![Windows][Logo_Windows] Windows
>
> 我们建议您使用 Windows 存储空间跨多个 Azure VHD 创建条带集。 至少使用 Windows 服务器 2012 R2 或 Windows 服务器 2016。
>
> ![Linux][Logo_Linux] Linux
>
> 仅支持 MDADM 和逻辑卷管理器 （LVM） 在 Linux 上构建软件 RAID。 有关详细信息，请参阅：
>
> - 使用 MDADM[在 Linux 上配置软件 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - [使用 LVM 在 Azure 中的 Linux VM 上配置 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> 由于 Azure 存储保留三个 VHD 映像，因此在条带条时配置冗余没有意义。 您只需配置条带化，以便 I/O 分布在不同的 VHD 上。
>

### <a name="managed-or-nonmanaged-disks"></a>托管或非托管磁盘
Azure 存储帐户是一个管理构造，也是一个限制对象。 标准存储帐户和高级存储帐户之间的限制不同。 有关功能和限制的信息，请参阅[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

对于标准存储，请记住每个存储帐户的 IOPS 有限制。 请参阅文章[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)中包含**总请求速率**的行。 每个 Azure 订阅的存储帐户数也有初始限制。 平衡不同存储帐户中更大的 SAP 环境的 VHD，以避免达到这些存储帐户的限制。 当您谈论几百台虚拟机时，这是一项繁琐的工作，虚拟机具有一千多个 VHD。

由于不建议将 DBMS 部署与 SAP 工作负载结合使用，因此对标准存储的引用和建议仅限于本文[全文](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

为了避免跨不同的 Azure 存储帐户规划和部署 VHD 的管理工作，Microsoft 在 2017 年引入了[Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 托管磁盘可用于标准存储和高级存储。 与非托管磁盘相比，托管磁盘的主要优点是：

- 对于托管磁盘，Azure 在部署时自动将不同的 VHD 分发到不同的存储帐户中。 这样，数据卷、I/O 吞吐量和 IOPS 的存储帐户限制就不会受到冲击。
- 使用托管磁盘，Azure 存储遵循 Azure 可用性集的概念。 如果 VM 是 Azure 可用性集的一部分，则 VM 的基本 VHD 和附加磁盘将部署到不同的故障和更新域中。


> [!IMPORTANT]
> 鉴于 Azure 托管磁盘的优点，我们建议您将 Azure 托管磁盘用于 DBMS 部署和 SAP 部署。
>

要从非托管磁盘转换为托管磁盘，请参阅：

- [将 Windows 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)。
- [将 Linux 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)。


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和数据磁盘的缓存
将磁盘装载到 VM 时，可以选择是否缓存 VM 和位于 Azure 存储中的磁盘之间的 I/O 流量。 标准和高级存储使用两种不同的技术进行此类缓存。

以下建议假定标准 DBMS 的 I/O 特征：

- 它主要是针对数据库的数据文件的读取工作负载。 这些读取对于 DBMS 系统至关重要的性能。
- 根据检查点或恒定流，对数据文件写入以突发形式进行。 平均一天，写入次数少于读取次数。 与从数据文件读取相反，这些写入是异步的，不会阻止任何用户事务。
- 事务日志或重做文件中几乎没有读取。 执行事务日志备份时，异常是较大的 I/O。
- 针对事务或重做日志文件的主负载是写入。 根据工作负载的性质，可以将 I/O 小到 4 KB，或者在其他情况下，I/O 大小为 1 MB 或更多。
- 所有写入都必须以可靠的方式保存在磁盘上。

对于标准存储，可能的缓存类型包括：

* 无
* 读取
* 读取/写入

要获得一致和确定性的性能，请为所有包含 DBMS 相关数据文件、日志和重做文件以及表空间的所有磁盘的标准存储设置缓存到**NONE**。 基础 VHD 的缓存可以保留默认值。

对于高级存储，存在以下缓存选项：

* 无
* 读取
* 读取/写入
* 无 = 写入加速器，仅适用于 Azure M 系列 VM
* 读取和写入加速器，仅适用于 Azure M 系列 VM

对于高级存储，我们建议您对 SAP 数据库**的数据文件**使用 Read 缓存，并为**日志文件的磁盘选择"无缓存"。**

对于 M 系列部署，我们建议您在 DBMS 部署中使用 Azure 写入加速器。 有关 Azure 写入加速器的详细信息、限制和部署，请参阅[启用写入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


### <a name="azure-nonpersistent-disks"></a>Azure 非持久性磁盘
部署 VM 后，Azure VM 提供非持久性磁盘。 在 VM 重新启动的情况下，这些驱动器上的所有内容都已清除。给定的是，数据文件以及数据库的日志和重做文件在任何情况下都不应位于这些未持久化的驱动器上。 某些数据库可能有例外，这些非持久化驱动器可能适用于 tempdb 和临时表空间。 避免将这些驱动器用于 A 系列 VM，因为这些非持久化驱动器的吞吐量与该 VM 系列一起受到限制。 

有关详细信息，请参阅了解[Azure 中的 Windows VM 上的临时驱动器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)。

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM 中的驱动器 D 是非持久化驱动器，由 Azure 计算节点上的一些本地磁盘支持。 因为它未持久化，因此在重新启动 VM 时，对驱动器 D 上的内容所做的任何更改都将丢失。 更改包括存储的文件、已创建的目录和已安装的应用程序。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM 在 /mnt/资源处自动装载驱动器，该驱动器是由 Azure 计算节点上的本地磁盘支持的未持久驱动器。 由于它是非持久化的，因此在重新启动 VM 时，对 /mnt/资源中的内容所做的任何更改都将丢失。 更改包括存储的文件、已创建的目录和已安装的应用程序。
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 存储复原
Microsoft Azure 存储将基本 VHD 与操作系统和附加的磁盘或 Blob 存储在至少三个单独的存储节点上。 这种类型的存储称为本地冗余存储 （LRS）。 LRS 是 Azure 中所有类型的存储的默认值。

还有其他冗余方法。 有关详细信息，请参阅[Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
>高级存储是存储数据库以及日志和重做文件的 DBMS VM 和磁盘的推荐存储类型。 高级存储的唯一可用冗余方法是 LRS。 因此，您需要配置数据库方法，以便将数据库数据复制到另一个 Azure 区域或可用性区域。 数据库方法包括 SQL Server 始终打开、Oracle 数据防护和 HANA 系统复制。


> [!NOTE]
> 对于 DBMS 部署，不建议对标准存储使用异地冗余存储 （GRS）。 GRS 严重影响性能，并且不遵守附加到 VM 的不同 VHD 的写入顺序。 不遵守不同 VHD 的写入顺序可能会导致复制目标端的数据库不一致。 如果数据库、日志和重做文件分布在多个 VHD 上（通常情况下，在源 VM 端），则会出现这种情况。



## <a name="vm-node-resiliency"></a>VM 节点复原
Azure 为 VM 提供了多个不同的 SL。 有关详细信息，请参阅虚拟机的[SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)最新版本。 由于 DBMS 层通常对 SAP 系统中的可用性至关重要，因此您需要了解可用性集、可用性区域和维护事件。 有关这些概念的详细信息，请参阅[管理 Azure 中的 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)和管理 Azure 中的 Linux[虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

对于具有 SAP 工作负载的生产 DBMS 方案，最低建议是：

- 在同一 Azure 区域中的单独可用性集中部署两个 VM。
- 在同一 Azure 虚拟网络中运行这两个 VM，并将 NIC 从同一子网中连接。
- 使用数据库方法来保留第二个 VM 的热备用服务器。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA 系统复制。

您还可以在另一个 Azure 区域中部署第三个 VM，并使用相同的数据库方法在另一个 Azure 区域中提供异步副本。

有关如何设置 Azure 可用性集的信息，请参阅[本教程](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)。



## <a name="azure-network-considerations"></a>Azure 网络注意事项
在大规模 SAP 部署中，请使用 Azure[虚拟数据中心](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)的蓝图。 将其用于虚拟网络配置以及组织不同部分的权限和角色分配。

这些最佳实践是数百个客户部署的结果：

- SAP 应用程序部署的虚拟网络无法访问互联网。
- 数据库 VM 与应用程序层在同一虚拟网络中运行。
- 虚拟网络中的 VM 具有专用 IP 地址的静态分配。 有关详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- 在本地 DBMS VM 上安装防火墙*时，不会*设置与 DBMS VM 的路由限制。 相反，流量路由是使用[网络安全组 （NSG）](https://docs.microsoft.com/azure/virtual-network/security-overview)定义的。
- 要分离和隔离对 DBMS VM 的流量，请向 VM 分配不同的 NIC。 每个 NIC 都获取不同的 IP 地址，并且每个 NIC 都分配给不同的虚拟网络子网。 每个子网都有不同的 NSG 规则。 网络流量的隔离或分离是路由的度量。 它不用于为网络吞吐量设置配额。

> [!NOTE]
> 通过 Azure 分配静态 IP 地址意味着将它们分配给各个虚拟 NIC。 不要在来宾操作系统中为虚拟 NIC 分配静态 IP 地址。 某些 Azure 服务（如 Azure 备份）依赖于以下事实：至少主虚拟 NIC 设置为 DHCP，而不是静态 IP 地址。 有关详细信息，请参阅对[Azure 虚拟机备份进行故障排除](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 要将多个静态 IP 地址分配给 VM，请为 VM 分配多个虚拟 NIC。
>


> [!IMPORTANT]
> 不支持在 SAP 应用程序与 SAP NetWeaver、Hybris 或 S/4HANA SAP 系统 DBMS 层之间的通信路径中配置[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)。 此限制是出于功能和性能原因。 SAP 应用程序层和 DBMS 层之间的通信路径必须是直接的。 如果这些 ASG 和 NSG 规则允许直接通信路径，则限制不包括[应用程序安全组 （ASG） 和 NSG 规则](https://docs.microsoft.com/azure/virtual-network/security-overview)。 
>
> 不支持网络虚拟设备的其他方案包括：
>
> * 代表 Linux 起搏器群集节点和 SBD 设备的 Azure VM 之间的通信路径，如 SAP[应用程序 SUSE Linux 企业服务器上的 AZURE VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)中所述。
> * Azure VM 和 Windows 服务器横向扩展文件服务器 （SOF） 之间的通信路径，如在[Windows 故障转移群集上的 SAP ASCS/SCS 实例群集中所述，使用 Azure 中的文件共享](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)。 
>
> 通信路径中的网络虚拟设备可以轻松地使两个通信伙伴之间的网络延迟加倍。 它们还可以限制 SAP 应用程序层和 DBMS 层之间关键路径的吞吐量。 在某些客户方案中，网络虚拟设备可能导致起搏器 Linux 群集失败。 在这些案例中，Linux 起搏器群集节点之间的通信通过网络虚拟设备与其 SBD 设备通信。
>

> [!IMPORTANT]
> 另一个*不支持*的设计是 SAP 应用程序层和 DBMS 层隔离到不同的 Azure 虚拟网络中，这些网络彼此不[相互对等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 我们建议您使用 Azure 虚拟网络中的子网而不是使用不同的 Azure 虚拟网络来隔离 SAP 应用程序层和 DBMS 层。 
>
> 如果您决定不遵循建议，而是将两个层隔离到不同的虚拟网络中，则必须对两个虚拟网络[进行对等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 
>
> 请注意，两[个对等](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虚拟网络之间的网络流量需要转移成本。 由多个 TB 组成的庞大数据量在 SAP 应用程序层和 DBMS 层之间交换。 如果将 SAP 应用程序层和 DBMS 层隔离到两个对等 Azure 虚拟网络之间，则可以累积大量成本。

在 Azure 可用性集中使用两个 VM 进行生产 DBMS 部署。 还对 SAP 应用程序层以及两个 DBMS VM 的管理和运营流量使用单独的路由。 参看下图：

![两个子网中两个 VM 的关系图](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure 负载均衡器重定向流量
使用专用虚拟 IP 地址用于 SQL Server 始终打开或 HANA 系统复制等功能需要配置 Azure 负载均衡器。 负载均衡器使用探测端口来确定活动 DBMS 节点，并将流量专用路由到该活动数据库节点。 

如果数据库节点有故障转移，则 SAP 应用程序无需重新配置。 相反，最常见的 SAP 应用程序体系结构会根据专用虚拟 IP 地址重新连接。 同时，负载均衡器通过将对专用虚拟 IP 地址的流量重定向到第二个节点来响应节点故障转移。

Azure 提供了两个不同的[负载均衡器 SKU：](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)一个基本 SKU 和一个标准 SKU。 除非要跨 Azure 可用性区域进行部署，否则基本负载均衡器 SKU 可以正常。

DBMS VM 和 SAP 应用程序层之间的流量是否始终通过负载均衡器路由？ 这取决于配置负载均衡器的方式。 

此时，到 DBMS VM 的传入流量始终通过负载均衡器路由。 从 DBMS VM 到应用程序层 VM 的传出流量路由取决于负载均衡器的配置。 

负载均衡器提供 DirectServerReturn 选项。 如果配置了该选项，则从 DBMS VM 定向到 SAP 应用程序层的流量*不会*通过负载均衡器路由。 相反，它直接转到应用程序层。 如果未配置 DirectServerReturn，则返回 SAP 应用程序层的流量将通过负载均衡器路由。

我们建议您将 DirectServerReturn 与位于 SAP 应用程序层和 DBMS 层之间的负载均衡器组合使用。 此配置可减少两个层之间的网络延迟。

有关如何使用 SQL Server 始终打开设置此配置的示例，请参阅为[Azure 中始终处于打开的可用性组配置 ILB 侦听器](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)。

如果使用已发布的 GitHub JSON 模板作为 Azure 中 SAP 基础结构部署的参考，请[研究此模板以进行 SAP 3 层系统](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)。 在此模板中，您还可以看到负载均衡器的正确设置。

### <a name="azure-accelerated-networking"></a>Azure 加速网络
为了进一步减少 Azure VM 之间的网络延迟，我们建议您选择[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 在为 SAP 工作负荷部署 Azure VM 时使用它，尤其是 SAP 应用程序层和 SAP DBMS 层。

> [!NOTE]
> 并非所有 VM 类型都支持加速网络。 上一篇文章列出了支持加速网络的 VM 类型。
>

---
> ![Windows][Logo_Windows] Windows
>
> 要了解如何部署具有 Windows 加速网络的 VM，请参阅[创建具有加速网络的 Windows 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。
>
> ![Linux][Logo_Linux] Linux
>
> 有关 Linux 发行版的详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。
>
>

---

> [!NOTE]
> SUSE、Red Hat 和 Oracle Linux 的最新版本支持加速网络。 旧版本（如 SLES 12 SP2 或 RHEL 7.2）不支持 Azure 加速网络。
>


## <a name="deployment-of-host-monitoring"></a>部署主机监视
为了在 Azure 虚拟机中使用 SAP 应用程序，SAP 需要从运行 Azure 虚拟机的物理主机获取主机监视数据的能力。 需要有特定的 SAP 主机代理补丁级别，才能在 SAPOSCOL 和 SAP 主机代理中启用此功能。 SAP 说明 [1409604] 中介绍了确切的补丁级别。

有关向 SAPOSCOL 和 SAP 主机代理提供主机数据的组件的部署以及这些组件的生命周期管理的详细信息，请参阅[部署指南][deployment-guide]。


## <a name="next-steps"></a>后续步骤
有关特定 DBMS 的详细信息，请参阅：

- [适用于 SAP 工作负荷的 SQL Server Azure 虚拟机 DBMS 部署](dbms_guide_sqlserver.md)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](dbms_guide_oracle.md)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](dbms_guide_ibm.md)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和内容服务器部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP HANA 操作指南](hana-vm-operations.md)
- [Azure 虚拟机的 SAP HANA 高可用性](sap-hana-availability-overview.md)
- [Azure 虚拟机上的 SAP HANA 备份指南](sap-hana-backup-guide.md)

