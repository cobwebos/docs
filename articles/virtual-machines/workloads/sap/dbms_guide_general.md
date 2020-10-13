---
title: 部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项 | Microsoft Docs
description: 部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项
author: msjuergent
ms.service: virtual-machines
ms.topic: article
ms.date: 09/20/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 1f71d95d61e401e12c76ca5589368eed6cc29ce6
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993284"
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



本指南是介绍如何在 Microsoft Azure 上实施和部署 SAP 软件的文档的一部分。 阅读本指南之前，请先阅读 [规划和实施指南][planning-guide] 以及计划指南的要点。 本文档介绍使用 Azure 基础结构即服务 (IaaS) 功能在 Microsoft Azure 虚拟机 (VM) 上部署 SAP 相关 DBMS 系统时的通用内容。

本文是对 SAP 安装文档和 SAP 说明的补充，这些文档代表在给定平台上安装和部署 SAP 软件的主要资源。

本文介绍在 Azure VM 中运行 SAP 相关 DBMS 系统的注意事项。 其中很少涉及有关特定 DBMS 系统的参考信息。 在本白皮书内，改在本文之后讨论特定的 DBMS 系统。

## <a name="definitions"></a>定义
整个文档使用以下术语：

* **IaaS**：基础结构即服务。
* **PaaS**：平台即服务。
* **SaaS**：软件即服务。
* **SAP 组件**：单个 SAP 应用程序，例如 ERP Central Component (ECC)、Business Warehouse (BW)、Solution Manager 或 Enterprise Portal (EP)。 SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以是不基于 NetWeaver 的应用程序，例如业务对象。
* **SAP 环境**：以逻辑方式组合在一起，用于执行开发、质量保证、培训、灾难恢复或生产等业务功能的一个或多个 SAP 组件。
* **SAP 布局**：表示客户 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
* **SAP 系统**：诸如 SAP ERP 开发系统、SAP Business Warehouse 测试系统或 SAP CRM 生产系统等的 DBMS 层与应用层的组合。 Azure 部署不支持在本地与 Azure 之间分割这两个层。 因此，某个 SAP 系统要么部署在本地，要么部署在 Azure 中。 可以将 SAP 布局中的不同系统部署到 Azure 或本地。 例如，可以将 SAP CRM 开发系统和测试系统部署在 Azure 中，但将 SAP CRM 生产系统部署在本地。
* **跨界**：描述这样一种场景：将 VM 部署到在本地数据中心与 Azure 之间建立了站点到站点、多站点或 ExpressRoute 连接的 Azure 订阅。 在一般的 Azure 文档中，此类部署也称为跨界方案。 

    建立连接是为了将本地域、本地 Active Directory 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器，并可在这些 VM 上运行服务（例如 DBMS 服务）。 可以在部署于本地的 VM 与部署于 Azure 的 VM 之间进行通信和名称解析。 这是用于在 Azure 上部署 SAP 资产最常见的方案。 有关详细信息，请参阅[规划和设计 VPN 网关](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)。

> [!NOTE]
> SAP 生产系统支持对 SAP 系统进行这种跨界部署：运行 SAP 系统的 Azure 虚拟机是本地域的成员。 跨界配置可将部分或完整 SAP 布局部署到 Azure。 即使在 Azure 中运行完整 SAP 布局，也需要这些 VM 成为本地域和 Active Directory/LDAP 的一部分。 
>
> 以前版本的文档提到了混合 IT 方案。 术语“混合”基于本地与 Azure 之间存在跨界连接这一事实。 在此方案中，“混合”还表示 Azure 中的 VM 是本地 Active Directory 的一部分。
>
>

有些 Microsoft 文档在描述跨界方案时稍有不同，特别是针对 DBMS 高可用性配置。 在 SAP 相关的文档中，跨界方案单纯是指站点到站点或专用 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 连接，以及同时涉及本地与 Azure 分布的 SAP 布局。

## <a name="resources"></a>资源
我们还提供了其他有关 Azure 上的 SAP 工作负载的文章。 请首先阅读 [Azure 上的 SAP 工作负载：入门](./get-started.md)，然后探索感兴趣的领域。

以下 SAP 说明与 Azure 上的 SAP 有关，涉及本文档中介绍的领域。

| 说明文档编号 | 标题 |
| --- | --- |
| [1928533] |Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视指标 |
| [1409604] |Windows 上的虚拟化：增强型监视 |
| [2191498] |Azure 的 Linux 上的 SAP：增强型监视 |
| [2039619] |Microsoft Azure 上使用 Oracle Database 的 SAP 应用程序：支持的产品和版本 |
| [2233094] |DB6：Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 应用程序：其他信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |Red Hat Enterprise Linux 7.x：安装和升级 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle Database 12c：Linux 上的文件系统支持 |
| [1114181] |Oracle Database 11g：Linux 上的文件系统支持 |


如需查看适用于 Linux 的所有 SAP 说明的相关信息，请参阅 [SAP 社区 wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)。

你需要了解 Microsoft Azure 体系结构，并知道如何部署和操作 Microsoft Azure 虚拟机。 有关详细信息，请参阅 [Azure 文档](../../../index.yml)。

一般而言，Windows、Linux 和 DBMS 的安装和配置基本上与在本地安装的任何虚拟机或裸机计算机相同。 使用 Azure IaaS 时的一些体系结构和系统管理实施决策有所不同。 本文档说明了使用 Azure IaaS 时要准备的特定体系结构和系统管理差异。


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>用于 RDBMS 部署的 VM 的存储结构
若要遵循本章节，请阅读并了解中提供的信息：

- [SAP NetWeaver 的 Azure 虚拟机规划和实施指南](./planning-guide.md)
- [适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)
- [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)
- [Azure 虚拟机上的 SAP 工作负荷支持的方案](./sap-planning-supported-configurations.md) 

在阅读本章之前，需要了解不同的 VM 系列以及标准存储和高级存储之间的差异。 

对于 Azure 块存储，强烈建议使用 Azure 托管磁盘。 有关 Azure 托管磁盘的详细信息，请参阅 [Azure vm 的托管磁盘简介](../../managed-disks-overview.md)。

在基本配置中，我们通常推荐部署结构：操作系统、DBMS 和最终的 SAP 二进制文件与数据库文件分开。 更改早期建议，我们建议为提供单独的 Azure 磁盘：

- 操作系统 (基本 VHD 或 OS VHD) 
- 数据库管理系统可执行文件
- SAP 可执行文件（如/usr/sap）

将这些组件分隔到三个不同 Azure 磁盘中的配置可导致更高的复原能力，因为 DBMS 或 SAP 可执行文件过多的日志或转储写入操作不会干扰操作系统磁盘的磁盘配额。 

DBMS 数据和事务/重做日志文件存储在 Azure 支持的块存储或 Azure NetApp 文件中。 它们存储在单独的磁盘中，并作为逻辑磁盘附加到原始 Azure 操作系统映像 VM。 对于 Linux 部署，有不同的建议，尤其是对 SAP HANA 而言。 有关适用于你的方案的功能和不同存储类型的支持，请参阅 [Azure 存储类型 SAP 工作负荷](./planning-guide-storage.md) 一文。 

规划磁盘布局时，请权衡以下各项并找到最佳平衡：

* 数据文件数目。
* 包含文件的磁盘数目。
* 单个磁盘或 NFS 共享的 IOPS 配额。
* 每个磁盘或 NFS 共享的数据吞吐量。
* 每个 VM 大小可能的附加数据磁盘数目。
* VM 可提供的总体存储或网络吞吐量。
* 不同的 Azure 存储类型可以提供的延迟。
* VM SLA。

Azure 会对每个数据磁盘或 NFS 共享强制实施 IOPS 配额。 对于在不同的 Azure 块存储解决方案或共享上托管的磁盘，这些配额是不同的。 这些不同存储类型的 i/o 延迟也不同。 

每种不同的 VM 类型可附加的数据磁盘数目有限。 另一个限制是，只有特定的 VM 类型可以使用高级存储。 通常，你会根据 CPU 和内存需求决定使用某个特定的 VM 类型。 你还可能还会考虑 IOPS、延迟和磁盘吞吐量要求，这些要求通常随磁盘数量或高级存储磁盘的类型而改变。 每个磁盘要达到的 IOPS 数和吞吐量可能会成为磁盘大小的决定因素，特别是在使用高级存储时。

> [!NOTE]
> 对于 DBMS 部署，我们建议 Azure 高级存储、适用于 azure NetApp 文件的基于 NFS 共享 (专门用于任何数据、事务日志或重做文件的 SAP HANA) 。 部署生产还是非生产系统并不重要。

> [!NOTE]
> 若要从 Azure 的 [单个 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)中获益，附加的所有磁盘都必须是 azure 高级存储或 azure 超磁盘类型，其中包括基本 VHD (azure 高级存储) 。

> [!NOTE]
> 不支持在位于与 Azure 数据中心相邻的并置第三方数据中心中的存储硬件上托管 SAP 数据库的主数据库文件（如数据和日志文件）。 对于此用例，还不支持通过 Azure Vm 中托管的软件设备所提供的存储。 对于 SAP DBMS 工作负荷，通常情况下，SAP 数据库的数据文件和事务日志文件仅支持表示为本机 Azure 服务的存储。 不同的 DBMS 可能支持不同的 Azure 存储类型。 有关更多详细信息，请参阅[Azure 存储类型 SAP 工作负荷](./planning-guide-storage.md)一文

数据库文件的位置以及使用的 Azure 存储的类型以及所使用的 Azure 存储类型由 IOPS、延迟和吞吐量要求定义。 专门为 Azure 高级存储提供足够的 IOPS，可能需要使用多个磁盘或使用更大的高级存储磁盘。 如果使用多个磁盘，需跨磁盘构建软件带区，其中包含数据文件或日志和重做文件。 在这种情况下，基础高级存储磁盘的 IOPS 和磁盘吞吐量 SLA 或标准存储磁盘的最大可达到 IOPS 是针对生成的带区集累积的。

如果 IOPS 要求超出了单个 VHD 可提供的数量，请在多个 Vhd 之间平衡数据库文件所需的 IOPS 数。 分散磁盘上 IOPS 负载的最简单方式是在不同的磁盘构建一个软件带区。 然后在从软件带区划分出的 LUN 上放置多个 SAP DBMS 数据文件。 条带中的磁盘数取决于 IOPS 要求、磁盘吞吐量需求和卷需求。


---
> ![Windows 存储条带化][Logo_Windows] Windows
>
> 我们建议使用 Windows 存储空间跨多个 Azure VHD 创建此类带区集。 至少使用 Windows Server 2012 R2 或 Windows Server 2016。
>
> ![Linux 存储条带化][Logo_Linux] Linux
>
> 仅支持使用 MDADM 和逻辑卷管理器 (LVM) 在 Linux 上构建软件 RAID。 有关详细信息，请参阅：
>
> - 使用 MDADM [在 Linux 上配置软件 RAID](../../linux/configure-raid.md)
> - [使用 LVM 在 Azure 中的 Linux VM 上配置 LVM](../../linux/configure-lvm.md)
>
>

---

对于 Azure Ultra 磁盘，无需带区，因为你可以定义与磁盘大小无关的 IOPS 和磁盘吞吐量。


> [!NOTE]
> 由于 Azure 存储保留 VHD 的三个映像，因此条带化时配置冗余无意义。 只需配置条带化，I/O 即可分布在不同的 VHD。
>

### <a name="managed-or-nonmanaged-disks"></a>托管或非托管磁盘
Azure 存储帐户是一种管理构造，还是一个具有各种限制的主体。 标准存储帐户和高级存储帐户的相关限制不同。 有关功能和限制的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../../../storage/common/scalability-targets-standard-account.md)。

对于标准存储，请记得，每个存储帐户的 IOPS 数是有限制的。 请参阅 [Azure 存储可伸缩性和性能目标](../../../storage/common/scalability-targets-standard-account.md)一文中包含“总请求率”那一行。 每个 Azure 订阅还有对存储帐户数量的初始限制。 在不同存储帐户中均衡较大 SAP 布局中的 VHD，避免达到这些存储帐户的限制。 对于具有数千个 VHD 的几百个虚拟机而言，这是一件繁琐的工作。

不建议将标准存储与 SAP 工作负荷一起用于 DBMS 部署。 因此，对标准存储的引用和建议仅限于本文的这[篇](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)短文

为避免在不同 Azure 存储帐户中执行规划和部署 VHD 的管理工作，Microsoft 在 2017 年推出了 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 托管磁盘适用于标准存储和高级存储。 与非托管磁盘相比，托管磁盘的主要优势如下：

- 对于托管磁盘，Azure 会在部署时自动在不同的存储帐户中分发不同的 VHD。 这样，就不会超出存储帐户在数据量、I/O 吞吐量和 IOPS 方面的限制。
- 如果使用托管磁盘，Azure 存储会运用 Azure 可用性集的概念。 如果 VM 是 Azure 可用性集的一部分，则 VM 的基本 VHD 和附加磁盘将部署到不同的容错域和更新域中。


> [!IMPORTANT]
> 考虑到 Azure 托管磁盘的优点，我们强烈建议你将 Azure 托管磁盘用于 DBMS 部署和 SAP 部署。
>

若要从非托管磁盘转换为托管磁盘，请参阅：

- [将 Windows 虚拟机从非托管磁盘转换为托管磁盘](../../windows/convert-unmanaged-to-managed-disks.md)。
- [将 Linux 虚拟机从非托管磁盘转换为托管磁盘](../../linux/convert-unmanaged-to-managed-disks.md)。


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和数据磁盘的缓存
将磁盘装载到 VM 时，可以选择是否缓存 VM 与位于 Azure 存储中的磁盘之间的 I/O 流量。

以下建议假设标准 DBMS 的 I/O 特征如下所示：

- 它主要是针对数据库的数据文件的读取工作负载。 此类读取对于 DBMS 系统是性能关键型的。
- 基于检查点或持续流，突发中会产生针对数据文件的写入。 按一天中的平均数来看，写入次数比读取次数少。 与从数据文件读取相反，这些写入是异步的，且不会阻止用户事务。
- 事务日志或重做文件中几乎没有读取。 但执行事务日志备份时的大型 I/O 除外。
- 针对事务或重做日志文件的主要负载是写入。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。
- 必须以一种可靠的方式在磁盘上保留所有写入。

对于标准存储，可能的缓存类型如下：

* 无
* 读取
* 读取/写入

为了获得一致且稳定的性能，请在标准存储上，针对包含 DBMS 相关数据文件、日志和重做文件和表空间的所有磁盘，将缓存设置为“无”。 基础 VHD 的缓存可以保留默认值。

对于 Azure 高级存储，存在以下缓存选项：

* 无
* 读取
* 读取/写入
* 无 + 写入加速器，仅针对 Azure M 系列 VM
* 读取 + 写入加速器，仅针对 Azure M 系列 VM

对于 Azure 高级存储，建议对 SAP 数据库的数据文件使用“读取缓存”，对磁盘的日志文件选择“不缓存” 。

对于 M 系列部署，建议将 Azure 写入加速器用于 DBMS 部署。 有关 Azure 写入加速器的详细信息、限制和部署，请参阅[启用写入加速器](../../how-to-enable-write-accelerator.md)。

对于超磁盘和 Azure NetApp 文件，不提供任何缓存选项。


### <a name="azure-nonpersistent-disks"></a>Azure 非持久性磁盘
部署 VM 后，Azure VM 提供非持久磁盘。 如果 VM 重启，这些驱动器上的所有内容会被擦除。这是因为数据库的数据文件和日志和重做文件决不能放置在这些非持久性驱动器上。 但一些数据库例外，在此情况下，这些非持久性驱动器对于 tempdb 和临时表空间可能是适合的。 应避免将这些驱动器用于 A 系列 VM，因为这些非持久性驱动器在该 VM 系列中的吞吐量有限。 

有关详细信息，请参阅[了解 Azure 中 Windows VM 上的临时驱动器](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)。

---
> ![Windows 非持久化磁盘][Logo_Windows] Windows
>
> Azure VM 中的驱动器 D 是一个非持久性驱动器，由 Azure 计算节点上的部分本地磁盘提供支持。 因为它是非持久性的，所以对驱动器 D 上的内容所做的任何更改都将在重新启动 VM 时丢失。 更改包括存储的文件、创建的目录和安装的应用程序。
>
> ![Linuxnonpersisted 磁盘][Logo_Linux] Linux
>
> Linux Azure VM 会在 /mnt/resource 上自动装载一个非持久性驱动器，该驱动器由 Azure 计算节点上的本地磁盘提供支持。 由于它是非持久性，当 VM 重新启动时，将丢失对 /mnt/resource 中的内容所做的任何更改。 更改包括存储的文件、创建的目录和安装的应用程序。
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 存储复原
Microsoft Azure 存储将基础 VHD（含 OS）以及附加磁盘或 blob 存储到至少三个不同的存储节点。 这种类型的存储称为本地冗余存储 (LRS)。 LRS 是 Azure 中所有存储类型的默认设置。

还有其他冗余方法。 有关详细信息，请参阅 [Azure 存储复制](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

> [!NOTE]
> 对于存储数据库和日志和重做文件的 DBMS Vm 和磁盘，azure 高级存储、超磁盘和 Azure NetApp 文件 (专用于 SAP HANA) 是推荐的存储类型。 这些存储类型的唯一可用冗余方法是 LRS。 因此，需要配置数据库方法，以便能够将数据库数据复制其他 Azure 区域或可用性区域。 数据库方法包括 SQL Server Always On、Oracle Data Guard 和 HANA 系统复制。


> [!NOTE]
> 对于 DBMS 部署，不建议为标准存储使用异地冗余存储 (GRS)。 GRS 严重影响性能，并且不遵循附加到 VM 的不同 VHD 的写入顺序。 不遵循不同 VHD 的写入顺序可能会导致复制目标端的数据库不一致。 在源 VM 端，如果数据库、日志和重做文件分散在多个 VHD 上（常见情况），就会出现这种情况。



## <a name="vm-node-resiliency"></a>VM 节点复原
Azure 为 VM 提供不同的 SLA。 有关详细信息，请参阅[虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 的最新版本。 由于 DBMS 层对于 SAP 系统中的可用性至关重要，因此你需要了解可用性集、可用性区域和维护事件。 有关这些概念的详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](../../manage-availability.md)和[在 Azure 中管理 Linux 虚拟机的可用性](../../manage-availability.md)。

针对具有 SAP 工作负载的生产 DBMS 方案的最低建议如下：

- 在同一 Azure 区域的一个单独的可用性集中部署两个 VM。
- 这两个 VM 将在同一 Azure 虚拟网络中运行，且会让 NIC 从同一子网中附加。
- 使用数据库方法来保留第二个 VM 的热备用服务器。 方法可以是 SQL Server Always On、Oracle Data Guard 或 HANA 系统复制。

还可以在另一个 Azure 区域中部署第三个 VM，使用相同数据库方法在另一个 Azure 区域中提供异步副本。

有关如何设置 Azure 可用性集的信息，请参阅[本教程](../../windows/tutorial-availability-sets.md)。



## <a name="azure-network-considerations"></a>Azure 网络注意事项
在大规模 SAP 部署中，使用 [Azure 虚拟数据中心](/azure/architecture/vdc/networking-virtual-datacenter)的蓝图。 将其用于虚拟网络配置和针对组织不同部门的权限和角色分配。

以下最佳实践基于数百次客户部署的结果：

- 将 SAP 应用程序部署到的虚拟网络无法访问 Internet。
- 数据库 Vm 与应用程序层在同一虚拟网络中运行，并在不同于 SAP 应用程序层的子网中分离。
- 虚拟网络中的 VM 具有专用 IP 地址的静态分配。 有关详细信息，请参阅 [Azure 中的 IP 地址类型和分配方法](../../../virtual-network/public-ip-addresses.md)。
- DBMS VM 之间的路由限制不是由安装在本地 DBMS VM 上的防火墙设置。 相反，流量路由是通过[网络安全组 (NSG)](../../../virtual-network/network-security-groups-overview.md) 定义的。
- 若要将流量分离并隔离到 DBMS VM，请为 VM 分配不同的 NIC。 每个 NIC 都有一个不同的 IP 地址，每个 NIC 都分配到不同的虚拟网络子网。 每个子网都有不同的 NSG 规则。 网络流量的隔离或分离是路由的一种措施。 它不用于为网络吞吐量设置配额。

> [!NOTE]
> 通过 Azure 分配静态 IP 地址意味着将其分配给单个虚拟 NIC。 不要将来宾 OS 中的静态 IP 地址分配给虚拟 NIC。 某些 Azure 服务（例如 Azure 备份）依赖于至少主虚拟 NIC 设置为 DHCP 而不是静态 IP 地址这一事实。 有关详细信息，请参阅 [Azure 虚拟机备份疑难解答](../../../backup/backup-azure-vms-troubleshoot.md#networking)。 若要将多个静态 IP 地址分配给某个 VM，需要将多个 vNIC 分配给该 VM。
>


> [!WARNING]
> 不支持在 SAP 应用程序与基于 SAP NetWeaver、Hybris 或 S/4HANA的 SAP 系统的 DBMS 层之间的通信路径中配置 [Azure 网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)。 此限制是出于功能和性能方面的原因。 SAP 应用程序层与 DBMS 层之间的通信路径必须为直接通信路径。 如果 ASG 和 NSG 规则允许直接通信路径，该限制就不包括[应用程序安全组 (ASG) 和 NSG 规则](../../../virtual-network/network-security-groups-overview.md)。 
>
> 不支持网络虚拟设备的其他方案包括：
>
> * 代表 Linux Pacemaker 群集节点的 Azure VM 与 SBD 设备之间的通信路径（如 [SUSE Linux Enterprise Server for SAP Applications 上的 Azure VM 上 SAP NetWeaver 的高可用性](./high-availability-guide-suse.md)所述）。
> * 在按[使用 Azure 中的文件共享在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例](./sap-high-availability-guide-wsfc-file-share.md)所述设置的 Azure VM 与 Windows Server 横向扩展文件服务器 (SOFS) 之间的通信路径中。 
>
> 通信路径中的网络虚拟设备很容易导致两个通信伙伴之间的网络延迟加倍。 它们还可能限制 SAP 应用程序层和 DBMS 层之间关键路径的吞吐量。 在某些客户方案中，网络虚拟设备可能导致 Pacemaker Linux 群集失败。 在这些情况下，Linux Pacemaker 群集节点之间的通信通过网络虚拟设备与其 SBD 设备通信。
>

> [!IMPORTANT]
> 另一个不受支持的设计是将 SAP 应用程序层和 DBMS 层分到相互不[对等互连](../../../virtual-network/virtual-network-peering-overview.md)的不同 Azure 虚拟网络。 建议使用 Azure 虚拟网络中的子网（而不是使用其他 Azure 虚拟网络）将 SAP 应用程序层与 DBMS 层隔离开来。 
>
> 如果决定不遵循建议，而是将两个层分到不同的虚拟网络，则这两个虚拟网络必须[对等互连](../../../virtual-network/virtual-network-peering-overview.md)。 
>
> 请注意，两个[对等互连](../../../virtual-network/virtual-network-peering-overview.md)的 Azure 虚拟网络之间的网络流量会产生传输费用。 SAP 应用层和 DBMS 层之间交换的数据量巨大，可达到许多太字节 (TB)。 如果 SAP 应用程序层和 DBMS 层的分隔处位于两个对等互连的 Azure 虚拟网络之间，累计产生的费用可能会很高。

在 Azure 可用性集中或在两个 Azure 可用性区域之间，将两个 Vm 用于生产 DBMS 部署。 还可以为 SAP 应用程序层和两个 DBMS VM 的管理和操作流量使用单独的路由。 参看下图：

![两个子网中两个 VM 的关系图](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>使用 Azure 负载均衡器重定向流量
SQL Server Always On 或 HANA 系统复制等功能中使用的专用虚拟 IP 地址的使用需要配置 Azure 负载均衡器。 负载均衡器使用探测端口确定活动的 DBMS 节点，并专门将流量路由到该活动的数据库节点。 

如果数据库节点发生故障转移，无需重新配置 SAP 应用程序。 最常用的 SAP 应用程序体系结构将针对专用虚拟 IP 地址重新连接。 同时，负载均衡器通过将专用虚拟 IP 地址将流量重定向到第二个节点，以此作为对节点故障转移的响应。

Azure 提供两种不同的[负载均衡器 SKU](../../../load-balancer/load-balancer-overview.md)：基本 SKU 和标准 SKU。 根据设置和功能的优点，你应该使用 Azure 负载均衡器的标准 SKU。 标准版负载均衡器的一个大优点是，数据流量不会通过负载均衡器本身路由。

有关如何配置内部负载均衡器的示例，请参阅 [教程：在 Azure 虚拟机上手动配置 SQL Server 可用性组](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial.md#create-an-azure-load-balancer)

> [!NOTE]
> 基本 SKU 和标准 SKU 的行为与公共 IP 地址访问相关。 在[SAP 高可用性方案中，使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)文档介绍了如何解决标准 SKU 访问公共 IP 地址的限制。


### <a name="azure-accelerated-networking"></a>Azure 加速网络
为了进一步降低 Azure VM 之间的网络延迟，建议选择 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 在为 SAP 工作负载部署 Azure VM 时使用它，尤其要为 SAP 应用程序层和 SAP DBMS 层使用。

> [!NOTE]
> 并非所有 VM 类型都支持加速网络。 前一篇文章列出了支持加速网络的 VM 类型。
>

---
> ![Windows 加速网络][Logo_Windows] Windows
>
> 若要了解如何部署具有加速网络的 Windows 虚拟机，请参阅[创建具有加速网络的 Windows 虚拟机](../../../virtual-network/create-vm-accelerated-networking-powershell.md)。
>
> ![Linux 加速网络][Logo_Linux] Linux
>
> 有关 Linux 分发版的更多信息，请参阅[创建具有加速网络的 Linux 虚拟机](../../../virtual-network/create-vm-accelerated-networking-cli.md)。
>
>

---

> [!NOTE]
> SUSE、Red Hat 和 Oracle Linux 的最新版本支持加速网络。 较早版本（如 SLES 12 SP2 或 RHEL 7.2）不支持 Azure 加速网络。
>


## <a name="deployment-of-host-monitoring"></a>部署主机监视
若要将 Azure 虚拟机中的 SAP 应用程序用于生产用途，SAP 需要能够从运行 Azure 虚拟机的物理主机获取主机监视数据。 需要有特定的 SAP 主机代理补丁级别，才能在 SAPOSCOL 和 SAP 主机代理中启用此功能。 SAP 说明 [1409604] 中介绍了确切的补丁级别。

有关向 SAPOSCOL 和 SAP 主机代理提供主机数据的组件部署以及这些组件的生命周期管理的详细信息，请参阅[部署指南][deployment-guide]。


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
