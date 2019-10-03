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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101364"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

本指南是有关如何在 Microsoft Azure 上实施和部署 SAP 软件的文档的一部分。 阅读本指南之前, 请阅读[规划和实施指南][planning-guide]。 本文档介绍了如何使用 Azure 基础结构即服务 (IaaS) 功能 Microsoft Azure 虚拟机 (Vm) 上的 SAP 相关 DBMS 系统的常规部署方面。

本文对 SAP 安装文档和 SAP 说明进行了补充, 这些文档代表在给定平台上安装和部署 SAP 软件的主要资源。

本文介绍在 Azure VM 中运行 SAP 相关 DBMS 系统的注意事项。 其中很少涉及有关特定 DBMS 系统的参考信息。 在本文档中, 我们将在本文中处理特定 DBMS 系统。

## <a name="definitions"></a>定义
在整个文档中, 将使用以下术语:

* **IaaS**:基础结构即服务。
* **PaaS**:平台即服务。
* **SaaS**:软件即服务。
* **SAP 组件**:单个 SAP 应用程序, 例如 ERP 中心组件 (ECC)、业务仓库 (BW)、解决方案管理器或 Enterprise Portal (EP)。 SAP 组件可以基于传统的 ABAP 或 Java 技术, 也可以基于非 NetWeaver 的应用程序, 例如业务对象。
* **SAP 环境**:逻辑分组的一个或多个 SAP 组件, 用于执行开发、质量保证、培训、灾难恢复或生产等业务功能。
* **SAP 横向**:表示客户 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产的环境。
* **SAP 系统**:DBMS 层和应用程序层的组合, 例如 SAP ERP 开发系统、SAP 业务仓库测试系统或 SAP CRM 生产系统。 在 Azure 部署中, 不支持在本地与 Azure 之间分割这两个层。 因此, SAP 系统要么部署在本地, 要么部署在 Azure 中。 可以在 Azure 中或本地部署 SAP 布局的不同系统。 例如, 你可以在 Azure 中部署 SAP CRM 开发和测试系统, 但在本地部署 SAP CRM 生产系统。
* **跨界**:介绍了这样一种方案: 将 Vm 部署到在本地数据中心与 Azure 之间建立了站点到站点、多站点或 Azure ExpressRoute 连接的 Azure 订阅。 在一般的 Azure 文档中，此类部署也称为跨界方案。 

    建立连接是为了将本地域、本地 Active Directory 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器并在这些 Vm 上运行服务 (例如 DBMS 服务)。 可以在部署于本地的 VM 与部署于 Azure 的 VM 之间进行通信和名称解析。 此方案是在 Azure 上部署 SAP 资产最常见的方案。 有关详细信息，请参阅[规划和设计 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> SAP 系统的跨界部署是指运行 SAP 系统的 Azure 虚拟机是本地域的成员并且支持生产 SAP 系统。 跨界配置可将部分或完整 SAP 布局部署到 Azure。 即使在 Azure 中运行完整的 SAP 布局, 也需要这些 Vm 成为本地域的一部分, 并 Active Directory/LDAP。 
>
> 在文档的以前版本中, 提到了混合 IT 方案。 术语 "*混合*" 是指本地与 Azure 之间存在跨界连接这一事实。 在这种情况下, 混合还意味着 Azure 中的 Vm 是本地 Active Directory 的一部分。
>
>

有些 Microsoft 文档以不同的方式描述跨界方案, 特别是对于 DBMS 高可用性配置。 在与 SAP 相关的文档中, 跨界方案会归结为站点到站点或专用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)连接, 以及在本地与 Azure 之间分发的 SAP 布局。

## <a name="resources"></a>资源
Azure 上的 SAP 工作负荷中还提供了其他相关文章。 Azure 上[的 SAP 工作负荷入门:开始](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) , 然后选择你感兴趣的区域。

以下 SAP 说明与 Azure 上的 SAP 相关, 涉及到本文档中所述的区域。

| 说明文档编号 | 标题 |
| --- | --- |
| [1928533] |Azure 上的 SAP 应用程序:支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视指标 |
| [1409604] |Windows 上的虚拟化：增强型监视 |
| [2191498] |Azure 的 Linux 上的 SAP：增强型监视 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 应用程序：支持的产品和版本 |
| [2233094] |DB6：Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 应用程序:其他信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |Red Hat Enterprise Linux 7.x：安装和升级 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle Database 12c:Linux 上的文件系统支持 |
| [1114181] |Oracle Database 11g:Linux 上的文件系统支持 |


有关适用于 Linux 的所有 SAP 说明的信息, 请参阅[sap 社区 wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

你需要了解 Microsoft Azure 的体系结构, 以及如何部署和操作 Microsoft Azure 虚拟机。 有关详细信息, 请参阅[Azure 文档](https://docs.microsoft.com/azure/)。

通常, Windows、Linux 和 DBMS 的安装和配置基本上与你在本地安装的任何虚拟机或裸机计算机相同。 使用 Azure IaaS 时, 有一些体系结构和系统管理实施决策有所不同。 本文档介绍了使用 Azure IaaS 时要准备的特定体系结构和系统管理差异。


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>用于 RDBMS 部署的 VM 的存储结构
若要遵循本章节, 请阅读并了解[部署指南][deployment-guide][一章][deployment-guide-3]中介绍的信息。 阅读本章之前, 需要了解并了解不同的 VM 系列以及标准存储和高级存储之间的差异。 

若要了解 Azure Vm 的 Azure 存储, 请参阅:

- [Azure Windows vm 的托管磁盘简介](../../windows/managed-disks-overview.md)。
- [Azure Linux vm 的托管磁盘简介](../../linux/managed-disks-overview.md)。

在基本配置中, 我们通常建议使用部署结构, 其中, 操作系统、DBMS 和最终的 SAP 二进制文件与数据库文件分开。 建议在 Azure 虚拟机中运行的 SAP 系统具有与操作系统、数据库管理系统可执行文件和 SAP 可执行文件一起安装的基本 VHD 或磁盘。 

DBMS 数据和日志文件存储在标准存储或高级存储中。 它们存储在不同的磁盘中, 并作为逻辑磁盘附加到原始的 Azure 操作系统映像 VM。 对于 Linux 部署, 会记录不同的建议, 尤其是 SAP HANA。

规划磁盘布局时, 请在以下各项之间找到最佳平衡:

* 数据文件数目。
* 包含文件的磁盘数目。
* 单个磁盘的 IOPS 配额。
* 每个磁盘的数据吞吐量。
* 每个 VM 大小可能的附加数据磁盘数目。
* VM 可提供的总体存储吞吐量。
* 不同的 Azure 存储类型可以提供的延迟。
* VM Sla。

Azure 强制实施每个数据磁盘的 IOPS 配额。 对于在标准存储和高级存储上托管的磁盘, 这些配额是不同的。 两种存储类型的 I/O 延迟也有差异。 高级存储提供更好的 i/o 延迟。 

每种不同的 VM 类型都有有限数量的可附加的数据磁盘。 另一个限制是, 只有特定的 VM 类型可以使用高级存储。 通常, 你决定基于 CPU 和内存要求使用特定的 VM 类型。 你还可以考虑 IOPS、延迟和磁盘吞吐量需求, 这些需求通常通过磁盘数目或高级存储磁盘类型进行缩放。 每个磁盘要达到的 IOPS 数目和吞吐量可能会规定磁盘大小, 尤其是高级存储。

> [!NOTE]
> 对于 DBMS 部署, 建议对任何数据、事务日志或重做文件使用高级存储。 是否要部署生产或非生产系统并不重要。

> [!NOTE]
> 若要从 Azure 唯一的[单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)中获益, 附加的所有磁盘都必须是高级存储类型, 包括基本 VHD。

> [!NOTE]
> 不支持在位于与 Azure 数据中心相邻的共存第三方数据中心的存储硬件上托管 SAP 数据库的主数据库文件 (如数据和日志文件)。 对于 SAP 工作负荷, SAP 数据库的数据文件和事务日志文件仅支持表示为本机 Azure 服务的存储。

数据库文件的位置和日志和重做文件以及所使用的 Azure 存储类型由 IOPS、延迟和吞吐量要求定义。 若要获得足够的 IOPS, 可能需要使用多个磁盘或使用更大的高级存储磁盘。 如果使用多个磁盘, 请在包含数据文件或日志和重做文件的磁盘上生成一个带软件条带。 在这种情况下, 将对生成的条带集累积总计基础高级存储磁盘的 IOPS 和磁盘吞吐量 Sla, 或标准存储磁盘的最大可实现 IOPS。

如前所述, 如果 IOPS 要求超出了单个 VHD 可提供的数量, 则平衡多个 Vhd 中的数据库文件所需的 IOPS 数。 跨磁盘分发 IOPS 负载的最简单方法是在不同磁盘上构建软件条带。 然后, 将多个划分上的 SAP DBMS 数据文件放在软件条带外。 条带中的磁盘数取决于 IOPs 要求、磁盘吞吐量需求和卷需求。


---
> ![Windows][Logo_Windows] Windows
>
> 建议使用 Windows 存储空间来创建跨多个 Azure Vhd 的条带集。 至少使用 Windows Server 2012 R2 或 Windows Server 2016。
>
> ![Linux][Logo_Linux] Linux
>
> 仅支持使用 MDADM 和逻辑卷管理器 (LVM) 在 Linux 上构建软件 RAID。 有关详细信息，请参阅：
>
> - 使用 MDADM[在 Linux 上配置软件 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - [使用 LVM 在 Azure 中的 Linux VM 上配置 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> 由于 Azure 存储会保留三个 Vhd 映像, 因此在条带化时配置冗余并无意义。 只需配置条带化, 以便在不同的 Vhd 上分发 i/o。
>

### <a name="managed-or-nonmanaged-disks"></a>托管或未托管磁盘
Azure 存储帐户是一种管理构造, 也是限制的一个主题。 标准存储帐户与高级存储帐户之间的限制不同。 有关功能和限制的信息, 请参阅[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

对于标准存储, 请记住, 每个存储帐户的 IOPS 有限制。 请参阅[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)一文中包含 "**总请求速率**" 的行。 每个 Azure 订阅的存储帐户数也有一个初始限制。 在不同的存储帐户之间平衡更大 SAP 布局的 Vhd, 以避免达到这些存储帐户的限制。 当你谈论的数百个虚拟机的 Vhd 超过一千个时, 这种情况很繁琐。

由于不建议将标准存储用于 DBMS 部署与 SAP 工作负荷, 因此, 对标准存储的引用和建议仅限于本文的这[篇](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)短文

为了避免跨不同 Azure 存储帐户规划和部署 Vhd 的管理工作, Microsoft 在2017中引入了[Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 托管磁盘适用于标准存储和高级存储。 与未托管磁盘相比, 托管磁盘的主要优点是:

- 对于托管磁盘, Azure 会在部署时自动在不同的存储帐户之间分发不同的 Vhd。 通过这种方式, 不会命中数据量、i/o 吞吐量和 IOPS 的存储帐户限制。
- 使用托管磁盘, Azure 存储采用 Azure 可用性集的概念。 如果 VM 是 Azure 可用性集的一部分, 则 VM 的基本 VHD 和附加磁盘将部署到不同的容错域和更新域中。


> [!IMPORTANT]
> 考虑到 Azure 托管磁盘的优势, 我们建议你将 Azure 托管磁盘用于 DBMS 部署和 SAP 部署。
>

若要从非托管磁盘转换为托管磁盘, 请参阅:

- [将 Windows 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)。
- [将 Linux 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)。


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和数据磁盘的缓存
将磁盘装载到 Vm 时, 可以选择是否缓存 VM 与 Azure 存储中的磁盘之间的 i/o 流量。 标准存储和高级存储使用两种不同的技术来实现这种类型的缓存。

以下建议为标准 DBMS 假设这些 i/o 特征:

- 它主要是对数据库数据文件的读取工作负荷。 这些读取对 DBMS 系统的性能至关重要。
- 对数据文件进行写入时, 会根据检查点或常量流以猝发的顺序发生。 一天中的平均值, 写入次数比读取次数少。 与从数据文件读取相反, 这些写入是异步的, 不包含任何用户事务。
- 事务日志或重做文件中几乎没有读取。 当执行事务日志备份时, 异常是较大的 i/o。
- 事务或重做日志文件的主负载是写入。 根据工作负荷的性质, 可以将 i/o 设置为小到 4 KB, 而在其他情况下, i/o 大小为 1 MB 或更大。
- 所有写入必须以可靠的方式持久保存在磁盘上。

对于标准存储, 可能的缓存类型为:

* 无
* 读取
* 读取/写入

若要获得一致且确定性的性能, 请将包含 DBMS 相关数据文件、日志和重做文件和表空间的所有磁盘的标准存储设置为 "**无**"。 基础 VHD 的缓存可以保留默认值。

对于高级存储, 存在以下缓存选项:

* None
* 读取
* 读取/写入
* 无 + 写入加速器, 仅适用于 Azure M 系列 Vm
* 读取 + 写入加速器, 仅适用于 Azure M 系列 Vm

对于高级存储, 建议对 SAP 数据库的**数据文件使用 "读取缓存**", 并**为日志文件的磁盘选择 "不缓存"** 。

对于 M 系列部署, 建议使用 Azure 写入加速器进行 DBMS 部署。 有关 Azure 写入加速器的详细信息、限制和部署, 请参阅[启用写入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


### <a name="azure-nonpersistent-disks"></a>Azure 非持久性磁盘
部署 VM 后, Azure Vm 提供非持久性磁盘。 如果 VM 重新启动, 这些驱动器上的所有内容都将被擦除。这是因为数据库的数据文件和日志文件和重做文件在任何情况下都不应位于这些非持久化驱动器上。 某些数据库可能有例外, 这些非持久化驱动器可能适用于 tempdb 和 temp 表空间。 避免对 A 系列 Vm 使用这些驱动器, 因为这些非持久化驱动器的吞吐量受限于该 VM 系列。 

有关详细信息, 请参阅[了解 Azure 中 Windows vm 上的临时驱动器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)。

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM 中的驱动器 D 是非持久化驱动器, 由 Azure 计算节点上的某些本地磁盘支持。 由于它是非持久化的, 因此, 在重新启动 VM 时, 对驱动器 D 上的内容所做的任何更改都将丢失。 更改包括已存储的文件、已创建的目录以及已安装的应用程序。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure Vm 会在/mnt/resource 上自动装载驱动器, 该驱动器由 Azure 计算节点上的本地磁盘支持。 由于它是非持久化的, 重新启动 VM 时, 对/mnt/resource 中的内容所做的任何更改都将丢失。 更改包括已存储的文件、已创建的目录以及已安装的应用程序。
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 存储复原
Microsoft Azure 存储在至少三个单独的存储节点上存储带有 OS 和附加的磁盘或 blob 的基本 VHD。 这种类型的存储称为本地冗余存储 (LRS)。 LRS 是 Azure 中所有类型的存储的默认值。

还有其他冗余方法。 有关详细信息，请参阅 [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
>高级存储是推荐用于存储数据库和日志和重做文件的 DBMS Vm 和磁盘的存储类型。 高级存储的唯一可用冗余方法是 LRS。 因此, 需要配置数据库方法, 使数据库数据复制到其他 Azure 区域或可用性区域。 数据库方法包括 SQL Server Always On、Oracle 数据防护和 HANA 系统复制。


> [!NOTE]
> 对于 DBMS 部署, 不建议使用异地冗余存储 (GRS) 用于标准存储。 GRS 严重影响性能, 不遵循附加到 VM 的不同 Vhd 之间的写入顺序。 不考虑跨不同 Vhd 的写入顺序可能会导致复制目标端的数据库不一致。 如果数据库和日志文件和重做文件在多个 Vhd (通常是在源 VM 端) 上分散, 就会发生这种情况。



## <a name="vm-node-resiliency"></a>VM 节点复原
Azure 为 Vm 提供了几个不同的 Sla。 有关详细信息, 请参阅[虚拟机 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)的最新版本。 由于 DBMS 层对于 SAP 系统中的可用性通常至关重要, 因此您需要了解可用性集、可用性区域和维护事件。 有关这些概念的详细信息, 请参阅在[azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)和[管理 azure 中 Linux 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

具有 SAP 工作负荷的生产 DBMS 方案的最小建议如下:

- 在同一 Azure 区域中的不同可用性集中部署两个 Vm。
- 在同一 Azure 虚拟网络中运行这两个 Vm, 并在同一子网中附加 Nic。
- 使用数据库方法来保留第二个 VM 的热备用服务器。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA 系统复制。

还可以在另一个 Azure 区域中部署第三个 VM, 并使用相同的数据库方法在另一个 Azure 区域中提供异步副本。

有关如何设置 Azure 可用性集的信息, 请参阅[此教程](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)。



## <a name="azure-network-considerations"></a>Azure 网络注意事项
在大规模 SAP 部署中, 使用[Azure 虚拟数据中心](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)的蓝图。 将其用于虚拟网络配置, 并将权限和角色分配给组织的不同部分。

这些最佳实践是数百个客户部署的结果:

- SAP 应用程序部署到的虚拟网络无法访问 internet。
- 数据库 Vm 与应用程序层在同一虚拟网络中运行。
- 虚拟网络中的 Vm 具有专用 IP 地址的静态分配。 有关详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- 在本地 DBMS Vm 上安装防火墙时,*不*会设置对 DBMS vm 的路由限制。 相反, 流量路由是通过[网络安全组 (nsg)](https://docs.microsoft.com/azure/virtual-network/security-overview)定义的。
- 若要将流量分离并隔离到 DBMS VM, 请为 VM 分配不同的 Nic。 每个 NIC 获取不同的 IP 地址, 并将每个 NIC 分配到不同的虚拟网络子网。 每个子网都有不同的 NSG 规则。 网络流量的隔离或分离是用于路由的度量值。 它不用于为网络吞吐量设置配额。

> [!NOTE]
> 通过 Azure 分配静态 IP 地址意味着将其分配给单独的虚拟 Nic。 不要将来宾操作系统中的静态 IP 地址分配给虚拟 NIC。 某些 Azure 服务 (如 Azure 备份) 依赖于至少将主虚拟 NIC 设置为 DHCP, 而不是静态 IP 地址。 有关详细信息, 请参阅[Azure 虚拟机备份故障排除](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 若要为 VM 分配多个静态 IP 地址, 请为 VM 分配多个虚拟 Nic。
>


> [!IMPORTANT]
> 不支持在 sap 应用程序与 SAP NetWeaver、Hybris 或 S/4HANA SAP 系统的 DBMS 层之间的通信路径中配置[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)。 此限制适用于功能和性能方面的原因。 SAP 应用程序层和 DBMS 层之间的通信路径必须是直接通信。 如果这些 ASG 和 NSG 规则允许直接通信路径, 则限制不包括[应用程序安全组 (ASG) 和 NSG 规则](https://docs.microsoft.com/azure/virtual-network/security-overview)。 
>
> 不支持网络虚拟设备的其他方案包括:
>
> * Azure Vm 之间的通信路径, 它们代表 Linux Pacemaker 群集节点和 SBD 设备, 如 sap [NetWeaver 在 Azure vm 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)中所述, 适用于 Sap 应用程序的 SUSE Linux Enterprise Server。
> * 按照[使用 azure 中的文件共享在 Windows 故障转移群集上群集中的 SAP ASCS/SCS 实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)中所述, 在 azure Vm 和 windows Server 横向扩展文件服务器 (SOFS) 之间设置通信路径。 
>
> 通信路径中的网络虚拟设备可以轻松地将两个通信伙伴之间的网络延迟加倍。 它们还可以限制 SAP 应用程序层和 DBMS 层之间的关键路径中的吞吐量。 在某些客户情况下, 网络虚拟设备可能会导致 Pacemaker Linux 群集出现故障。 这种情况下, Linux Pacemaker 群集节点之间的通信通过网络虚拟设备与其 SBD 设备通信。
>

> [!IMPORTANT]
> *不*受支持的另一种设计是, 将 SAP 应用程序层和 DBMS 层分为不同的 Azure 虚拟网络, 这些虚拟网络彼此之间并不[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 建议使用 Azure 虚拟网络中的子网 (而不是使用不同的 Azure 虚拟网络) 隔离 SAP 应用程序层和 DBMS 层。 
>
> 如果决定不遵循建议, 而是将两个层隔离到不同的虚拟网络中, 则必须[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)这两个虚拟网络。 
>
> 请注意, 两个[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虚拟网络之间的网络流量可能会产生费用。 包含许多 tb 的大型数据量在 SAP 应用程序层和 DBMS 层之间进行交换。 如果 SAP 应用程序层和 DBMS 层在两个对等互连 Azure 虚拟网络之间进行隔离, 则可以累积开销。

在 Azure 可用性集中, 将两个 Vm 用于生产 DBMS 部署。 还应为 SAP 应用程序层使用单独的路由, 并对两个 DBMS Vm 使用管理和操作流量。 参看下图：

![两个子网中两个 VM 的关系图](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure 负载均衡器重定向流量
使用 SQL Server Always On 或 HANA 系统复制等功能中使用的专用虚拟 IP 地址需要配置 Azure 负载均衡器。 负载均衡器使用探测端口来确定活动 DBMS 节点, 并以独占方式将流量路由到该活动数据库节点。 

如果数据库节点发生故障转移, 则不需要对 SAP 应用程序进行重新配置。 相反, 最常见的 SAP 应用程序体系结构会重新连接到专用虚拟 IP 地址。 同时, 负载均衡器会通过将流量重定向到第二个节点的专用虚拟 IP 地址, 来响应节点故障转移。

Azure 提供两个不同的[负载均衡器 sku](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): 基本 sku 和标准 sku。 除非你想要跨 Azure 可用性区域进行部署, 否则基本负载均衡器 SKU 会正常。

DBMS Vm 和 SAP 应用程序层之间的流量是否始终通过负载均衡器路由？ 这取决于配置负载均衡器的方式。 

此时, 将始终通过负载均衡器路由到 DBMS VM 的传入流量。 从 DBMS VM 到应用程序层 VM 的传出流量路由取决于负载均衡器的配置。 

负载均衡器提供 DirectServerReturn 选项。 如果配置了该选项, 从 DBMS VM 定向到 SAP 应用程序层的流量*不*会通过负载均衡器路由。 而是直接转到应用程序层。 如果未配置 DirectServerReturn, 则返回到 SAP 应用程序层的流量通过负载均衡器进行路由。

建议将 DirectServerReturn 与在 SAP 应用程序层和 DBMS 层之间的负载均衡器结合在一起进行配置。 此配置可减少两个层之间的网络延迟。

有关如何使用 SQL Server Always On 设置此配置的示例, 请参阅[在 Azure 中配置 Always On 可用性组的 ILB 侦听器](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)。

如果在 Azure 中使用已发布 GitHub JSON 模板作为 SAP 基础结构部署的参考, 请[针对 sap 3 层系统](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)研究此模板。 在此模板中, 你还可以看到负载均衡器的正确设置。

### <a name="azure-accelerated-networking"></a>Azure 加速网络
若要进一步减少 Azure Vm 之间的网络延迟, 建议选择 " [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)"。 部署 Azure Vm 以实现 SAP 工作负荷时, 尤其是对于 SAP 应用程序层和 SAP DBMS 层, 请使用此方法。

> [!NOTE]
> 并非所有 VM 类型都支持加速网络。 前一篇文章列出了支持加速网络的 VM 类型。
>

---
> ![Windows][Logo_Windows] Windows
>
> 若要了解如何使用加速网络为 Windows 部署 Vm, 请参阅[创建具有加速网络的 windows 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。
>
> ![Linux][Logo_Linux] Linux
>
> 有关 Linux 分发的详细信息, 请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。
>
>

---

> [!NOTE]
> SUSE、Red Hat 和 Oracle Linux 的最新版本支持加速网络。 更早版本, 如 SLES 12 SP2 或 RHEL 7.2 不支持 Azure 加速网络。
>


## <a name="deployment-of-host-monitoring"></a>部署主机监视
若要在 Azure 虚拟机中使用 SAP 应用程序, SAP 需要能够从运行 Azure 虚拟机的物理主机获取主机监视数据。 需要有特定的 SAP 主机代理补丁级别，才能在 SAPOSCOL 和 SAP 主机代理中启用此功能。 SAP 说明 [1409604] 中介绍了确切的补丁级别。

有关将主机数据传递到 SAPOSCOL 和 SAP 主机代理的组件以及这些组件的生命周期管理的详细信息, 请参阅[部署指南][deployment-guide]。


## <a name="next-steps"></a>后续步骤
有关特定 DBMS 的详细信息, 请参阅:

- [适用于 SAP 工作负荷的 SQL Server Azure 虚拟机 DBMS 部署](dbms_guide_sqlserver.md)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](dbms_guide_oracle.md)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](dbms_guide_ibm.md)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](dbms_guide_sapase.md)
- [Azure 上的 SAP maxDB、Live Cache 和内容服务器部署](dbms_guide_maxdb.md)
- [Azure 上的 SAP HANA 操作指南](hana-vm-operations.md)
- [Azure 虚拟机的 SAP HANA 高可用性](sap-hana-availability-overview.md)
- [Azure 虚拟机上 SAP HANA 的备份指南](sap-hana-backup-guide.md)

