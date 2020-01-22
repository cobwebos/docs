---
title: Azure 虚拟机上的大型机重新承载
description: 使用 Microsoft Azure 上的虚拟机（Vm） Rehost 大型机工作负荷，例如基于 IBM Z 的系统。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289792"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 虚拟机上的大型机重新承载

通过将工作负荷从大型机环境迁移到云，你可以使你的基础结构实现现代化，并经常节省成本。 许多工作负荷只需进行轻微的代码更改（如更新数据库的名称）便可传输到 Azure。

一般而言，*大型机*是指大型计算机系统。 具体而言，绝大部分当前正在使用的是 IBM System Z 服务器或可运行 MVS、DOS、VSE、OS/390 或 Z/OS 的 IBM 插件兼容系统。

Azure 虚拟机（VM）用于隔离和管理单个实例上的特定应用程序的资源。 IBM z/OS 等大型机使用逻辑分区（LPARS）来实现此目的。 对于包含关联 COBOL 程序的 CICS 区域，大型机可以使用一个 LPAR，为 IBM Db2 数据库使用单独的 LPAR。 [Azure 上典型的 n 层应用程序](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)将 azure vm 部署到可细分为每个层的子网的虚拟网络中。

Azure Vm 可以运行支持直接迁移方案的大型机模拟环境和编译器。 开发和测试通常是从大型机迁移到 Azure 开发/测试环境的第一个工作负荷。 可以模拟的常见服务器组件包括联机事务处理（OLTP）、批处理和数据引入系统，如下图所示。

![Azure 上的仿真环境使你能够运行基于 z/OS 的系统。](media/01-overview.png)

某些大型机工作负载可以相对轻松地迁移到 Azure，而另一些则可以使用合作伙伴解决方案重新承载在 Azure 上。 有关选择合作伙伴解决方案的详细指导， [Azure 大型机迁移中心](https://azure.microsoft.com/migration/mainframe/)可以提供帮助。

## <a name="mainframe-migration"></a>大型机迁移

Rehost、重新生成、替换或停用？ IaaS 还是 PaaS？ 若要确定适用于大型机应用程序的正确迁移策略，请参阅 Azure 体系结构中心中的[大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)指南。

## <a name="micro-focus-rehosting-platform"></a>微聚焦重新承载平台

微聚焦企业服务器是可用的最大大型机重新承载平台之一。 可以使用它在 Azure 上成本较低的 x86 平台上运行 z/OS 工作负荷。

开始使用：

- [在 Azure 上安装企业服务器和企业开发人员](./microfocus/set-up-micro-focus-azure.md)
- [在 Azure 上设置适用于企业开发人员的 CICS BankDemo](./microfocus/demo.md)
- [在 Azure 上的 Docker 容器中运行企业服务器](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure 上的 TmaxSoft OpenFrame

TmaxSoft OpenFrame 是一种常用的大型机重新承载解决方案，用于提升和移动方案。 Azure 上的 OpenFrame 环境适用于开发、演示、测试或生产工作负荷。

开始使用：

- [TmaxSoft OpenFrame 入门](./tmaxsoft/get-started.md)
- [下载电子书](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 开发和测试环境（IBM zD & T）设置 Azure 上的非生产环境，你可以将其用于基于 Z/OS 的应用程序的开发、测试和演示。

Azure 上的仿真环境可以通过应用程序开发人员控制的分布（ADCDs）托管不同类型的 Z 实例。 你可以在 Azure 上运行 zD & T Personal Edition、zD & T Parallel Sysplex 和 zD & T Enterprise Edition，并 Azure Stack。

开始使用：

- [在 Azure 上设置 IBM zD & T 12。0](./ibm/install-ibm-z-environment.md)
- [在 zD 上设置 ADCD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 pureScale 环境为 Azure 提供了一个数据库群集。 它与原始环境并不完全相同，但它提供了类似的可用性和规模，因为在并行 Sysplex 设置中运行的是 IBM DB2 for z/OS。

若要开始，请参阅[Azure 上的 IBM DB2 pureScale](/azure/virtual-machines/linux/ibm-db2-purescale-azure)。

## <a name="considerations"></a>注意事项

将大型机工作负荷迁移到 Azure 基础结构即服务（IaaS）时，可以选择多种类型的按需、可缩放的计算资源（包括 Azure Vm）。 Azure 提供了一系列[Linux](/azure/virtual-machines/linux/overview)和[Windows](/azure/virtual-machines/windows/overview) vm。

### <a name="compute"></a>计算

Azure 计算能力比大型机容量比较高。 如果考虑将大型机工作负载转移到 Azure，请将每秒1000000说明的大型机指标（MIPS）与虚拟 Cpu 进行比较。 

了解如何[将大型机计算移到 Azure](./concepts/mainframe-compute-azure.md)。

### <a name="high-availability-and-failover"></a>高可用性和故障转移

Azure 提供基于承诺的服务级别协议（Sla）。 默认情况下，有多个9的可用性，并且可以通过本地或基于异地的服务复制优化 Sla。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

使用 Azure IaaS （例如 VM），特定系统功能提供故障转移支持，例如，故障转移群集实例和可用性集。 使用 Azure 平台即服务（PaaS）资源时，平台会自动处理故障转移。 示例包括[AZURE SQL 数据库](/azure/sql-database/sql-database-technical-overview)和[Azure Cosmos DB](/azure/cosmos-db/introduction)。

### <a name="scalability"></a>可伸缩性

大型机通常会向上扩展，而云环境会扩大。Azure 提供了一系列[Linux](/azure/virtual-machines/linux/sizes)和[Windows](/azure/virtual-machines/windows/sizes)大小来满足你的需求。 云还可以向上或向下缩放以匹配确切的用户规范。 基于使用情况的计费模型，计算能力、存储和服务按需[缩放](/azure/architecture/best-practices/auto-scaling)。

### <a name="storage"></a>存储空间

在云中，你有一系列灵活、可缩放的存储选项，你只需为所需的内容付费。 [Azure 存储](/azure/storage/common/storage-introduction)为数据对象提供可大规模缩放的对象存储，为云提供文件系统服务、可靠的消息传送存储，以及 NoSQL 存储。 对于 VM，托管和非托管磁盘提供持久且安全的磁盘存储。

了解如何[将大型机存储移到 Azure](./concepts/mainframe-storage-azure.md)。

### <a name="backup-and-recovery"></a>备份和恢复

维护自己的灾难恢复站点可能是一种昂贵的做法。 Azure 为本地或区域级别或异地冗余的[备份](/azure/backup/backup-introduction-to-azure-backup)、[恢复](/azure/site-recovery/site-recovery-overview)和[冗余](/azure/storage/common/storage-redundancy)提供了易于实施和经济高效的选项。

## <a name="azure-government-for-mainframe-migrations"></a>适用于大型机迁移的 Azure 政府

很多公共部门实体都喜欢将其大型机应用程序迁移到更新式的灵活平台。 Microsoft Azure 政府是全局 Microsoft Azure 平台的物理上分离的实例，适用于联邦、州和地方政府系统。 它为美国政府机构及其合作伙伴提供世界一流的安全、保护和符合性服务。

Azure 政府为需要此类环境的系统提供了一种临时权限来操作（P-ATO），FedRAMP 对系统的影响很大。

若要开始，请下载[适用于大型机应用程序 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)。

## <a name="next-steps"></a>后续步骤

要求我们的[合作伙伴](partner-workloads.md)帮助你迁移或 rehost 你的大型机应用程序。 

另请参阅：

- [有关大型机主题的白皮书](mainframe-white-papers.md)
- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [故障排除](/azure/virtual-machines/troubleshooting/)
- [揭密大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
