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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 975289f338e638ed0209d4f6cf2a163ced996e42
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67202942"
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

本指南是文档的如何实现和部署 Microsoft Azure 上的 SAP 软件的一部分。 在阅读本指南之前，请阅读[规划和实施指南][planning-guide]。 本文档介绍如何在 Microsoft Azure 虚拟机 (Vm) 上的 SAP 相关 DBMS 系统的一般部署方面使用 Azure 基础结构即服务 (IaaS) 功能。

本文是补充 SAP 安装文档和 SAP 说明，代表安装和部署 SAP 软件的主要资源在给定平台。

本文介绍在 Azure VM 中运行 SAP 相关 DBMS 系统的注意事项。 其中很少涉及有关特定 DBMS 系统的参考信息。 相反，特定的 DBMS 系统处理在本文中，本文档后。

## <a name="definitions"></a>定义
在文档中，使用这些术语：

* **IaaS**:基础结构即服务。
* **PaaS**:平台即服务。
* **SaaS**:软件即服务。
* **SAP 组件**:例如 ERP Central Component (ECC)、 Business Warehouse (BW)、 Solution Manager 或 Enterprise Portal (EP) 单个 SAP 应用程序。 SAP 组件可以基于传统的 ABAP 或 Java 技术或非基于 NetWeaver 的应用程序，如业务对象。
* **SAP 环境**:一个或多个 SAP 组件以逻辑方式组合，用于执行开发、 质量保证、 培训、 灾难恢复或生产等业务功能。
* **SAP 布局**:表示客户 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
* **SAP 系统**:组合的 DBMS 层与应用程序层的例如，SAP ERP 开发系统，SAP Business Warehouse 测试系统或 SAP CRM 生产系统。 在 Azure 部署中，将在本地与 Azure 之间的这两个层不受支持。 因此，SAP 系统是在本地部署或它的部署在 Azure 中。 你可以部署在 Azure 中或在本地的 SAP 环境的不同系统。 例如，可以部署 SAP CRM 开发和测试系统在 Azure 中但部署 SAP CRM 生产系统的本地。
* **跨界**:介绍的方案，其中 Vm 部署到 Azure 订阅具有站点到站点、 多站点，或 Azure ExpressRoute 连接之间的本地数据中心和 Azure。 在一般的 Azure 文档中，此类部署也称为跨界方案。 

    建立连接是为了将本地域、本地 Active Directory 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器，并在这些 Vm，例如 DBMS 服务上运行服务。 可以在部署于本地的 VM 与部署于 Azure 的 VM 之间进行通信和名称解析。 这是用来部署 Azure 上的 SAP 资产最常见的方案。 有关详细信息，请参阅[规划和设计 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)。

> [!NOTE]
> 跨界部署 SAP 系统的支持对于生产 SAP 系统，和是运行 SAP 系统的 Azure 虚拟机所在的本地域成员。 跨界配置可将部分或完整 SAP 布局部署到 Azure。 甚至在 Azure 中运行完整 SAP 布局需要这些 Vm 成为本地域的一部分与 Active Directory/LDAP。 
>
> 在以前版本的文档，混合 IT 方案中所述。 术语*混合*已取得 root 权限是在本地与 Azure 之间建立了跨界连接中。 在这种情况下，混合还表示 Azure 中的 Vm 是在本地 Active Directory 的一部分。
>
>

有些 Microsoft 文档描述跨界方案时稍有不同，特别是针对 DBMS 高可用性配置。 在 SAP 相关的文档，跨界方案可以归结为站点到站点或专用[ExpressRoute](https://azure.microsoft.com/services/expressroute/)连接和分布在本地与 Azure 的 SAP 环境。

## <a name="resources"></a>资源
有可用的其他文章上的 SAP 工作负荷在 Azure 上。 使用启动[在 Azure 上的 SAP 工作负荷：开始](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)，然后选择你感兴趣的区域。

以下 SAP 说明与 Azure 上的 SAP 相关方面本文档中所覆盖的区域。

| 说明文档编号 | 标题 |
| --- | --- |
| [1928533] |在 Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视指标 |
| [1409604] |Windows 上的虚拟化：增强型监视 |
| [2191498] |Azure 的 Linux 上的 SAP：增强型监视 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 应用程序：支持的产品和版本 |
| [2233094] |DB6：使用 IBM DB2 for Linux、 UNIX 和 Windows Azure 上的 SAP 应用程序：其他信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |Red Hat Enterprise Linux 7.x：安装和升级 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle Database 12c:Linux 上的文件系统支持 |
| [1114181] |Oracle Database 11g:Linux 上的文件系统支持 |


在所有 SAP 说明适用于 Linux 的信息，请参阅[SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

需要 Microsoft Azure 体系结构和如何部署和操作 Microsoft Azure 虚拟机的工作知识。 有关详细信息，请参阅[Azure 文档](https://docs.microsoft.com/azure/)。

一般情况下，Windows、 Linux 和 DBMS 的安装和配置是实质上是相同的任何虚拟机或裸机安装在本地计算机。 有一些作为体系结构和系统管理实施决策时使用 Azure IaaS 的不同。 本文档介绍特定于体系结构和准备用于使用 Azure IaaS 时的系统管理差异。


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 部署的 VM 的存储结构
若要执行这一章，阅读并理解中提供的信息[本章][ deployment-guide-3]的[部署指南 》][deployment-guide]。 您需要了解并知道有关的不同 VM 系列和在阅读本章之前的标准和高级存储之间的差异。 

若要了解有关 Azure 存储为 Azure Vm，请参阅：

- [适用于 Azure Windows Vm 的托管磁盘简介](../../windows/managed-disks-overview.md)。
- [介绍 Azure Linux Vm 的托管磁盘](../../linux/managed-disks-overview.md)。

在基本配置中，我们通常建议操作系统、 DBMS 和最终的 SAP 二进制文件是数据库文件分开的部署结构。 我们建议在 Azure 虚拟机中运行的 SAP 系统的基本 VHD 或安装与操作系统、 数据库管理系统可执行文件和 SAP 可执行文件的磁盘。 

DBMS 数据和日志文件存储在标准存储还是高级存储。 它们存储在不同的磁盘并以逻辑磁盘形式附加到原始 Azure 操作系统映像 VM。 对于 Linux 部署，记录了不同的建议，尤其是对于 SAP HANA。

当规划磁盘布局时，发现这些项之间的最佳平衡：

* 数据文件数目。
* 包含文件的磁盘数目。
* 单个磁盘的 IOPS 配额。
* 每个磁盘的数据吞吐量。
* 每个 VM 大小可能的附加数据磁盘数目。
* VM 可提供的总体存储吞吐量。
* 不同的 Azure 存储类型可以提供的延迟。
* VM Sla。

Azure 强制实施每个数据磁盘的 IOPS 配额。 这些配额是不同的标准存储和高级存储上托管的磁盘。 两种存储类型的 I/O 延迟也有差异。 高级存储提供更好的 I/O 延迟。 

每个不同的 VM 类型都有有限的数量的可附加的数据磁盘。 另一个限制是，只有一部分 VM 类型可以使用高级存储。 通常情况下，您决定使用特定 VM 类型根据 CPU 和内存要求。 您还可能会考虑 IOPS、 延迟和磁盘吞吐量要求，通常通过磁盘数目或高级存储磁盘的类型来调整。 若要通过每个磁盘的 IOPS 和吞吐量的数可能会决定磁盘大小，尤其是在使用高级存储。

> [!NOTE]
> 对于 DBMS 部署，我们建议使用高级存储的任何数据，事务日志，或重做文件。 是否想要部署生产或非生产系统，它并不重要。

> [!NOTE]
> 若要从 Azure 中受益的唯一[单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，所有附加的磁盘必须是高级存储类型，其中包括基本虚拟硬盘。

> [!NOTE]
> 托管的主数据库文件，如数据和日志文件位于在归置的第三方数据中心内靠近 Azure 数据中心的存储硬件上的 SAP 数据库的不受支持。 对于 SAP 工作负荷，表示为本机 Azure 服务的唯一存储支持的 SAP 数据库的数据和事务日志文件。

数据库文件和日志和恢复文件和你使用的 Azure 存储的类型的位置由 IOPS、 延迟和吞吐量要求定义。 若要有足够的 IOPS，你可能不得不使用多个磁盘或使用更大的高级存储磁盘。 如果使用多个磁盘，磁盘包含数据文件或日志和重复文件之间构建软件的带区。 在这种情况下、 IOPS 和磁盘吞吐量 Sla 基础高级存储磁盘或标准存储磁盘的最大可实现 IOPS 是累积的结果的条带集。

如已所述，如果你的 IOPS 要求超过了单个 VHD 可提供，平衡的数据库文件需要跨多个 Vhd 的 IOPS 数。 在磁盘之间分配 IOPS 负载的最简单方法是基于不同的磁盘构建一个软件带区。 然后，将多个 SAP dbms 数据文件放在划分出软件条带的 Lun 上。 条带中的磁盘数是根据 IOPs 要求、 磁盘吞吐量需求和卷驱动需求。


---
> ![Windows][Logo_Windows] Windows
>
> 我们建议使用 Windows 存储空间来跨多个 Azure Vhd 中创建的带区集。 请至少使用 Windows Server 2012 R2 或 Windows Server 2016。
>
> ![Linux][Logo_Linux] Linux
>
> 支持仅 MDADM 和逻辑卷管理器 (LVM) 来构建一个软件 RAID Linux 上。 有关详细信息，请参阅：
>
> - [Linux 上配置软件 RAID](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)使用 MDADM
> - [使用 LVM 在 Azure 中的 Linux VM 上配置 LVM](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> Azure 存储会保留三个映像的 Vhd，因为它毫无意义时来条带化配置冗余。 只需配置条带化，以便 I/o 分布在不同的 Vhd。
>

### <a name="managed-or-nonmanaged-disks"></a>托管或非托管磁盘
Azure 存储帐户是一种管理构造以及各种限制的主体。 标准存储帐户和高级存储帐户之间存在差异的限制。 有关功能和限制的信息，请参阅[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。

标准存储，请记住每个存储帐户的 iops，没有限制。 请参阅包含的行**总请求速率**一文中[Azure 存储可伸缩性和性能目标](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)。 此外，还有初始上的每个 Azure 订阅的存储帐户数限制。 平衡跨不同的存储帐户更大的 SAP 环境的 Vhd 来避免达到这些存储帐户的限制。 当谈到几个超过 1000 个 Vhd 的几百个虚拟机，这是枯燥乏味的工作。

不建议结合 SAP 工作负荷的 DBMS 部署中使用标准存储，因为引用和到标准存储的建议仅限于这么短[文章](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

若要避免的规划和部署跨不同的 Azure 存储帐户的 Vhd 的管理工作，Microsoft 引入了[Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)2017年中。 托管的磁盘是适用于标准存储和高级存储。 比较为非托管磁盘的托管磁盘的主优点是：

- 对于托管磁盘，Azure 将分配不同的 Vhd 跨不同的存储帐户会自动在部署时。 这样一来，存储帐户限制的数据量，I/O 吞吐量和 IOPS 未命中。
- 使用托管的磁盘，Azure 存储空间遵循 Azure 可用性集的概念。 如果 VM 是 Azure 可用性集的一部分，基本 VHD 和 VM 的附加的磁盘部署到不同的容错和更新域。


> [!IMPORTANT]
> 给定 Azure 托管磁盘的优点，我们建议你使用 Azure 托管磁盘部署 DBMS 和 SAP 部署的一般情况下。
>

若要从非托管转换为托管磁盘，请参阅：

- [将 Windows 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)。
- [将 Linux 虚拟机从非托管磁盘转换为托管磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)。


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和数据磁盘的缓存
当你将磁盘装载到 Vm 时，可以选择是否缓存 VM 与位于 Azure 存储中这些磁盘之间的 I/O 流量。 标准和高级存储为此类型的缓存使用两个不同的技术。

以下建议为标准的 DBMS 承担这些 I/O 特征：

- 它是主要针对数据库的数据文件的读取工作负荷。 这些读数是关键的 DBMS 系统的性能。
- 针对数据文件的写入发生爆发性地根据检查点或恒定流。 平均一天，有比读取更少写入。 相比于读取数据文件中，这些写入是异步的请勿阻止任何用户事务。
- 事务日志或重做文件中几乎没有读取。 例外情况是大规模 i/o 操作时执行事务日志备份。
- 针对事务或重做日志文件的主要负载是写入。 相关工作负荷的性质，可以有 I/o 最小可为 4 KB，或在其他情况下，1 MB 或更多的 I/O 大小。
- 必须以可靠方式的磁盘上保留所有写入。

对于标准存储，可能的缓存类型为：

* 无
* 读取
* 读取/写入

若要获取一致且稳定的性能，设置为包含与 DBMS 相关的数据文件的所有磁盘的日志和重做文件和表空间到标准存储上缓存**NONE**。 基础 VHD 的缓存可以保留默认值。

对于高级存储，都存在以下缓存选项：

* 无
* 读取
* 读取/写入
* 无 + 写入加速器，它仅适用于 Azure M 系列 Vm
* 读取 + 写入加速器，它仅适用于 Azure M 系列 Vm

对于高级存储，我们建议你使用**读取的数据文件缓存**将 sap 数据库，然后选择**日志文件的磁盘不缓存**。

M 系列部署，建议将 DBMS 部署 Azure 写入加速器。 有关详细信息、 限制和 Azure 写入加速器的部署，请参阅[启用写入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。


### <a name="azure-nonpersistent-disks"></a>Azure 非持久性磁盘
部署 VM 后，azure Vm 提供非持久性磁盘。 在 VM 重新启动的情况下在这些驱动器上的所有内容都擦除。它是考虑到数据文件和日志文件和重做文件的数据库应在任何情况下位于在这些非持久化的驱动器上。 可能存在例外对于某些数据库，这些非持久化的驱动器可能是适用于 tempdb 和临时表空间。 避免使用这些驱动器的 A 系列 Vm，因为这些非持久化的驱动器受限的吞吐量，该 VM 系列。 

有关详细信息，请参阅[了解 Azure 中 Windows Vm 上的临时驱动器](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)。

---
> ![Windows][Logo_Windows] Windows
>
> 在 Azure VM 中的驱动器 D 是持久化的驱动器，这由 Azure 计算节点上的部分本地磁盘。 因为它是持久化，重新启动 VM 时，对驱动器 D 上的内容进行任何更改都会丢失。 更改包括存储的文件、 已创建的目录以及安装的应用程序。
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure Vm 上自动装载在 /mnt/resource 是由本地磁盘上的 Azure 计算节点提供支持的非持久化的驱动器的驱动器。 因为它是持久化，重新启动 VM 时，mnt/资源中的内容所做的任何更改都会丢失。 更改包括存储的文件、 已创建的目录以及安装的应用程序。
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 存储复原
Microsoft Azure 存储将基础 VHD，使用 OS 和附加的磁盘或 blob 存储至少三个单独的存储节点上。 存储此类型被称为本地冗余存储 (LRS)。 LRS 是存储在 Azure 中的所有类型的默认值。

还有一些其他冗余方法。 有关详细信息，请参阅 [Azure 存储复制](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
>高级存储是存储的建议的 DBMS Vm 和磁盘存储数据库和日志和重复文件类型。 高级存储的唯一可用的冗余方法为 LRS。 因此，您需要配置数据库方法，以启用数据库的数据复制到另一个 Azure 区域或可用性区域。 数据库的方法包括 SQL Server Alwayson、 Oracle Data Guard 和 HANA 系统复制。


> [!NOTE]
> 对于 DBMS 部署，适用于标准存储情况下不建议使用异地冗余存储 (GRS)。 GRS 会严重影响性能，以及在附加到 VM 的不同 Vhd 之间不兼容的写顺序。 不履行的写顺序在不同 Vhd 之间可能会导致不一致的数据库在复制目标端。 这种情况下发生如果数据库和日志文件和重做文件分布在多个 Vhd 都很常见的情况下，源 VM 端上。



## <a name="vm-node-resiliency"></a>VM 节点复原
Azure 为 Vm 提供几个不同的 Sla。 有关详细信息，请参阅最新版本的[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)。 由于 DBMS 层是通常可用性的关键 SAP 系统中，您需要了解可用性集、 可用性区域和维护事件。 有关这些概念的详细信息，请参阅[管理 Azure 中 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)并[管理 Azure 中 Linux 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)。

对于生产 SAP 工作负荷的 DBMS 方案的最小建议是：

- 在另一个可用性集在同一 Azure 区域中部署两个 Vm。
- 在同一 Azure 虚拟网络中运行这些两个 Vm 和从相同的子网附加 Nic。
- 使用数据库方法来保留第二个 VM 的热备用服务器。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA 系统复制。

此外可以部署第三个在另一个 Azure 区域中的 VM 并使用相同的数据库方法来提供另一个 Azure 区域中的异步副本。

有关如何设置 Azure 可用性集的信息，请参阅[本教程](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)。



## <a name="azure-network-considerations"></a>Azure 网络注意事项
在大型 SAP 部署中，使用的蓝图[Azure 虚拟数据中心](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)。 将其用于虚拟网络配置和权限和角色分配到你的组织的不同部分。

这些最佳实践是数百个客户部署的结果：

- SAP 应用程序部署到的虚拟网络不能访问 internet。
- 在与应用程序层位于同一虚拟网络中运行数据库 Vm。
- 虚拟网络中的 Vm 的专用 IP 地址静态分配。 有关详细信息，请参阅[IP 地址类型和在 Azure 中的分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。
- 与 DBMS Vm 的路由限制是*不*与安装在本地 DBMS Vm 上的防火墙设置。 相反，流量路由定义与[网络安全组 (Nsg)](https://docs.microsoft.com/azure/virtual-network/security-overview)。
- 若要隔离到 DBMS VM 的流量和分隔，分配到 VM 的不同 Nic。 每个 NIC 获取不同的 IP 地址，并每个 NIC 分配到不同的虚拟网络子网。 每个子网都有不同的 NSG 规则。 隔离或分离的网络流量路由的度量值。 它不用于设置网络吞吐量的配额。

> [!NOTE]
> 将通过 Azure 的静态 IP 地址分配意味着若要将其分配给单独的虚拟 Nic。 不要将来宾 OS 中的静态 IP 地址分配给虚拟 nic。 某些 Azure 服务，例如 Azure 备份依赖于这一事实，在最主要的虚拟 NIC 设置 DHCP 而不属于静态 IP 地址。 有关详细信息，请参阅[排查 Azure 虚拟机备份](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)。 若要将多个静态 IP 地址分配到 VM，请为 VM 分配多个虚拟 Nic。
>


> [!IMPORTANT]
> 配置[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)在 SAP 应用程序和 DBMS 层中的 SAP NetWeaver-之间的通信路径，Hybris-或 S/4HANA 的基于 SAP 系统不受支持。 此限制是出于功能和性能的原因。 SAP 应用程序层和 DBMS 层之间的通信路径必须是直接的。 限制不包括[应用程序安全组 (ASG) 和 NSG 规则](https://docs.microsoft.com/azure/virtual-network/security-overview)如果这些 ASG 和 NSG 规则允许直接通信路径。 
>
> 网络虚拟设备不支持的其他方案中是：
>
> * 表示 Linux Pacemaker 的 Azure Vm 之间的通信路径群集节点和 SBD 设备，如中所述[针对 SAP 应用程序的 SUSE Linux Enterprise Server 上的 Azure Vm 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)。
> * Azure Vm 和 Windows Server 横向扩展文件服务器 (SOFS) 之间的通信路径设置中所述的最多[在 Azure 中使用的文件共享在 Windows 故障转移群集上群集 SAP ASCS/SCS 实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)。 
>
> 通信路径中的网络虚拟设备可以轻松地两倍的两个通信合作伙伴之间的网络延迟。 它们还可以限制在 SAP 应用程序层和 DBMS 层之间的关键路径中的吞吐量。 在某些客户方案中，网络虚拟设备可能会导致 Pacemaker Linux 群集失败。 这些是到其 SBD 设备通过网络虚拟设备通信 Linux Pacemaker 群集节点之间的通信的情况。
>

> [!IMPORTANT]
> 另一种设计的*不*支持为到不同 Azure 虚拟网络都不是 SAP 应用程序层和 DBMS 层的分离[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)彼此。 我们建议通过使用不同的 Azure 虚拟网络中而不是 Azure 虚拟网络的子网隔离的 SAP 应用程序层和 DBMS 层。 
>
> 如果你决定不按照建议操作，而是将两个层隔离到不同虚拟网络，必须为两个虚拟网络[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 
>
> 请注意，网络两个之间的流量[对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)Azure 虚拟网络可能会发生传输成本。 SAP 应用程序层和 DBMS 层之间交换包含数万亿字节的大数据量。 如果 SAP 应用程序层和 DBMS 层与两个对等互连的 Azure 虚拟网络之间，可能会累积大量成本。

在生产中的 Azure 可用性的 DBMS 部署使用两个 Vm 设置。 此外使用单独的 SAP 应用层和两个 DBMS Vm 的管理和操作流量路由。 参看下图：

![两个子网中两个 VM 的关系图](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure 负载均衡器将流量重定向
在 SQL Server Always On 或 HANA 系统复制等功能中使用的专用虚拟 IP 地址的使用需要 Azure 负载均衡器的配置。 负载均衡器使用探测端口来确定活动的 DBMS 节点并将流量路由到该活动的数据库节点以独占方式。 

如果数据库节点的故障转移，则无需为要重新配置的 SAP 应用程序。 相反，最常见的 SAP 应用程序体系结构的重新连接针对的专用虚拟 IP 地址。 同时，负载均衡器作出反应节点故障转移到由将针对的专用虚拟 IP 地址的流量重定向到第二个节点。

Azure 提供两个不同[负载均衡器 Sku](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)： 基本 SKU 和标准 SKU。 除非你想要跨 Azure 可用性区域部署，基本负载均衡器 SKU 执行正常操作。

是 DBMS Vm 和 SAP 应用程序层始终通过负载均衡器路由的时间之间的流量？ 这取决于配置负载均衡器的方式。 

在此期间，通过负载均衡器始终路由到 DBMS VM 的传入流量。 传出流量路由从 DBMS VM 到 VM 的负载均衡器的配置取决于应用程序层。 

负载均衡器提供 DirectServerReturn 选项。 如果配置该选项，则是在 DBMS VM 从定向到 SAP 应用程序层的流量*不*路由通过负载均衡器。 相反，它会直接转到应用程序层。 如果未配置 DirectServerReturn，通过负载均衡器路由到 SAP 应用程序层的返回流量。

我们建议结合放置于 SAP 应用程序层和 DBMS 层之间的负载均衡器配置 DirectServerReturn。 此配置减少了两个层之间的网络延迟。

有关如何设置此配置与 SQL Server Always On 的示例，请参阅[在 Azure 中配置 Always On 可用性组的 ILB 侦听器](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)。

如果使用已发布的 GitHub JSON 模板作为参考为在 Azure 中 SAP 基础结构部署，请参阅[第 3 层 SAP 系统的模板](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)。 在此模板中，您还可以看到负载均衡器的正确设置。

### <a name="azure-accelerated-networking"></a>Azure 加速网络
若要进一步减少 Azure Vm 之间的网络延迟，我们建议你选择[Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 对于 SAP 工作负荷，尤其是对于 SAP 应用程序层和 SAP DBMS 层部署的 Azure Vm 时使用它。

> [!NOTE]
> 并非所有 VM 类型都支持加速网络。 前一篇文章列出了支持加速网络的 VM 类型。
>

---
> ![Windows][Logo_Windows] Windows
>
> 若要了解如何使用加速网络的 Windows 部署的 Vm，请参阅[创建具有加速网络的 Windows 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。
>
> ![Linux][Logo_Linux] Linux
>
> 有关 Linux 分发版的详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。
>
>

---

> [!NOTE]
> SUSE、Red Hat 和 Oracle Linux 的最新版本支持加速网络。 SLES 12 SP2 或 RHEL 7.2 等较旧版本不支持 Azure 加速网络。
>


## <a name="deployment-of-host-monitoring"></a>部署主机监视
对于 Azure 虚拟机中的 SAP 应用程序的生产用途，SAP 要求能够获取主机监视数据与运行 Azure 虚拟机的物理主机。 需要有特定的 SAP 主机代理补丁级别，才能在 SAPOSCOL 和 SAP 主机代理中启用此功能。 SAP 说明 [1409604] 中介绍了确切的补丁级别。

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

