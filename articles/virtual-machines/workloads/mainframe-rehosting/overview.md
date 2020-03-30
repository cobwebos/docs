---
title: 大型机在 Azure 虚拟机上重新托管
description: 使用 Microsoft Azure 上的虚拟机 （VM） 重新托管大型机工作负载，例如基于 IBM Z 的系统。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289792"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>大型机在 Azure 虚拟机上重新托管

将工作负载从大型机环境迁移到云使您能够实现基础架构现代化，并经常节省成本。 许多工作负荷只需进行轻微的代码更改（如更新数据库的名称）便可传输到 Azure。

一般来说，*术语大型机*意味着一个大型计算机系统。 具体来说，目前使用的绝大多数是 IBM 系统 Z 服务器或 IBM 插头兼容系统，这些系统运行 MVS、DOS、VSE、OS/390 或 z/OS。

Azure 虚拟机 （VM） 用于隔离和管理单个实例上特定应用程序的资源。 IBM z/OS 等大型机为此使用逻辑分区 （LPARS）。 大型机可能使用一个 LPAR 用于具有关联 COBOL 程序的 CICS 区域，对 IBM Db2 数据库使用单独的 LPAR。 Azure[上的典型 n 层应用程序](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)将 Azure VM 部署到虚拟网络，该虚拟网络可以跨段到每个层的子网中。

Azure VM 可以运行支持升降和移位方案的大型机仿真环境和编译器。 开发和测试通常是从大型机迁移到 Azure 开发/测试环境的第一个工作负载之一。 可以模拟的常见服务器组件包括联机事务进程 （OLTP）、批处理和数据引入系统，如下图所示。

![Azure 上的仿真环境使您能够运行基于 z/OS 的系统。](media/01-overview.png)

某些大型机工作负荷可以相对轻松地迁移到 Azure，而其他工作负荷可以使用合作伙伴解决方案在 Azure 上重新托管。 有关选择合作伙伴解决方案的详细信息[，Azure 大型机迁移中心](https://azure.microsoft.com/migration/mainframe/)可以提供帮助。

## <a name="mainframe-migration"></a>大型机迁移

重新托管、重建、替换或停用？ IaaS 还是 PaaS？ 要确定大型机应用程序的正确迁移策略，请参阅 Azure 体系结构中心中的[大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)指南。

## <a name="micro-focus-rehosting-platform"></a>微焦点重新托管平台

微焦点企业服务器是可用的最大的大型主机重新托管平台之一。 您可以使用它在 Azure 上较便宜的 x86 平台上运行 z/OS 工作负荷。

开始操作：

- [在 Azure 上安装企业服务器和企业开发人员](./microfocus/set-up-micro-focus-azure.md)
- [在 Azure 上为企业开发人员设置 CICS 银行演示](./microfocus/demo.md)
- [在 Azure 上的 Docker 容器中运行企业服务器](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>在 Azure 上使用 TmaxSoft 打开框架

TmaxSoft OpenFrame 是一种流行的大型主机重新托管解决方案，用于升降和换档方案。 Azure 上的 OpenFrame 环境适用于开发、演示、测试或生产工作负荷。

开始操作：

- [使用 TmaxSoft 开放帧入门](./tmaxsoft/get-started.md)
- [下载电子书](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 开发和测试环境（IBM zD&T）在 Azure 上设置了一个非生产环境，可用于基于 z/OS 的应用程序的开发、测试和演示。

Azure 上的仿真环境可以通过应用程序开发人员控制的分发（ADCD）承载不同类型的 Z 实例。 您可以在 Azure 和 Azure 堆栈上运行 zD&T 个人版、zD&T 并行系统版和 zD&T 企业版。

开始操作：

- [在 Azure 上设置 IBM zD&T 12.0](./ibm/install-ibm-z-environment.md)
- [在 zD&T 上设置 ADCD](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 纯 Scale 环境为 Azure 提供了数据库群集。 它与原始环境不同，但它提供与 IBM DB2 类似的可用性和扩展，用于在并行系统设置中运行的 z/OS。

要开始，请参阅[Azure 上的 IBM DB2 纯标。](/azure/virtual-machines/linux/ibm-db2-purescale-azure)

## <a name="considerations"></a>注意事项

将大型机工作负荷作为服务 （IaaS） 迁移到 Azure 基础结构时，可以从多种类型的按需可扩展计算资源（包括 Azure VM）中进行选择。 Azure 提供一系列[Linux](/azure/virtual-machines/linux/overview)和[Windows](/azure/virtual-machines/windows/overview) VM。

### <a name="compute"></a>计算

Azure 计算能力与大型机的容量相比是有利的。 如果您正在考虑将大型机工作负载移动到 Azure，请将每秒 100 万条指令 （MIPS） 的大型机指标与虚拟 CPU 进行比较。 

了解如何[将大型机计算移动到 Azure](./concepts/mainframe-compute-azure.md)。

### <a name="high-availability-and-failover"></a>高可用性和故障转移

Azure 提供基于承诺的服务级别协议 （SL）。 多个九的可用性是默认的，SLA 可以通过本地或基于地理位置的服务复制进行优化。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

使用 Azure IaaS（如 VM），特定的系统功能提供故障转移支持，例如故障转移群集实例和可用性集。 使用 Azure 平台作为服务 （PaaS） 资源时，该平台会自动处理故障转移。 示例包括[Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)和 Azure[宇宙数据库](/azure/cosmos-db/introduction)。

### <a name="scalability"></a>可伸缩性

大型机通常向上扩展，而云环境则横向扩展。Azure 提供一系列[Linux](/azure/virtual-machines/linux/sizes)和[Windows](/azure/virtual-machines/windows/sizes)大小，以满足您的需求。 云还可以向上或向下扩展，以匹配确切的用户规格。 在基于使用情况的计费模型下，计算功率、存储和服务可按需[扩展](/azure/architecture/best-practices/auto-scaling)。

### <a name="storage"></a>存储

在云中，您拥有一系列灵活、可扩展的存储选项，并且您只需根据所需内容付费。 [Azure 存储](/azure/storage/common/storage-introduction)为数据对象提供可大规模缩放的对象存储，为云提供文件系统服务、可靠的消息传送存储，以及 NoSQL 存储。 对于 VM，托管和非托管磁盘提供持久且安全的磁盘存储。

了解如何[将大型机存储移动到 Azure](./concepts/mainframe-storage-azure.md)。

### <a name="backup-and-recovery"></a>备份和恢复

维护自己的灾难恢复站点可能是一个昂贵的提议。 Azure 具有易于实现且经济高效的选项，用于本地或区域级别的[备份](/azure/backup/backup-introduction-to-azure-backup)、[恢复](/azure/site-recovery/site-recovery-overview)和[冗余](/azure/storage/common/storage-redundancy)，或通过异地冗余。

## <a name="azure-government-for-mainframe-migrations"></a>用于大型机迁移的 Azure 政府

许多公共部门实体希望将其大型机应用程序迁移到更现代、更灵活的平台。 Microsoft Azure 政府是全局 Microsoft Azure 平台的物理分离实例，为联邦、州和地方政府系统打包。 它专门为美国政府机构及其合作伙伴提供世界一流的安全、保护和合规服务。

Azure 政府为 FedRAMP 高影响系统赢得了临时运营授权 （P-ATO），适用于需要此类环境的系统。

要开始，请下载[用于大型机应用程序的 Microsoft Azure 政府云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)。

## <a name="next-steps"></a>后续步骤

请我们的[合作伙伴](partner-workloads.md)帮助您迁移或重新托管大型机应用程序。 

另请参阅：

- [有关大型机主题的白皮书](mainframe-white-papers.md)
- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [疑难解答](/azure/virtual-machines/troubleshooting/)
- [将大型机与 Azure 迁移解密](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
