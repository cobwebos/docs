---
title: Azure 虚拟机上重新承载的大型机
description: 重新托管在大型机工作负荷，如 Microsoft Azure 上使用虚拟机 (Vm) 的基于 IBM Z 的系统。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487439"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure 虚拟机上重新承载的大型机

迁移工作负荷从大型机到云环境，可运行您的基础结构的现代化，并通常节省成本。 许多工作负荷只需进行轻微的代码更改（如更新数据库的名称）便可传输到 Azure。

一般来讲，术语*大型机*意味着大型计算机系统。 具体而言，当前正在使用大多数是 IBM 系统 Z 服务器或运行 MVS、 DOS、 VSE，os/390 或 z/OS 的 IBM 时代系统。

Azure 虚拟机 (VM) 用于隔离和管理的单个实例上的特定应用程序的资源。 大型机如 IBM z/OS 使用逻辑分区 (LPAR) 实现此目的。 大型机可能使用一个 LPAR，CICS 区域关联的 COBOL 程序、 使用和单独 LPAR IBM Db2 数据库。 典型[在 Azure 上的 n 层应用程序](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)将 Azure Vm 部署到可细分为每个层的子网的虚拟网络。

大型机仿真环境和支持直接迁移方案的编译器，可以运行 azure Vm。 开发和测试通常是多个第一个工作负荷从大型机迁移到 Azure 开发/测试环境。 常用的服务器组件，可模拟包括联机事务处理 (OLTP)、 批处理和数据引入系统，如下图所示。

![在 Azure 上的模拟环境，可运行基于 z/OS 的系统。](media/01-overview.png)

某些大型机工作负荷可以迁移到 Azure 相对轻松，而其他人可以在 Azure 中使用合作伙伴解决方案重新承载。 选择合作伙伴解决方案，有关详细指南[Azure 大型机迁移中心](https://azure.microsoft.com/migration/mainframe/)可以帮助。

## <a name="mainframe-migration"></a>大型机迁移

重新托管、 重新生成、 替换或停用？ IaaS 还是 PaaS？ 若要确定你的大型机应用程序的正确的迁移策略，请参阅[大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)Azure 体系结构中心中的指南。

## <a name="micro-focus-rehosting-platform"></a>Micro 焦点重新承载平台

Micro 焦点 Enterprise Server 是大型机的一个最大重新承载平台可用。 可以使用其成本较低的 x86 上运行你 z/OS 的工作负荷在 Azure 上的平台。

开始操作：

- [在 Azure 上安装企业服务器和企业开发人员](./microfocus/set-up-micro-focus-azure.md)
- [为企业开发人员在 Azure 上设置 CICS BankDemo](./microfocus/demo.md)
- [在 Azure 上的 Docker 容器中运行企业服务器](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>在 Azure 上的 TmaxSoft OpenFrame

TmaxSoft OpenFrame 是提升和转移方案中使用的常用大型机重新承载解决方案。 在 Azure 上的 OpenFrame 环境适合开发、 演示、 测试或生产工作负荷。

开始操作：

- [开始使用 TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [下载电子书](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z 开发和测试环境 (IBM zD & T) 设置可用于开发、 测试和演示基于 z/OS 的应用程序的 Azure 上的非生产环境。

在 Azure 上的仿真环境可以托管不同类型的应用程序开发人员控制分发版 (ADCDs) 通过 Z 实例。 可以在 Azure 和 Azure Stack 上运行 zD T Personal Edition，zD T 并行 Sysplex 和 zD & T Enterprise Edition。

开始操作：

- [设置 IBM zD & 在 Azure 上的 T 12.0](./ibm/install-ibm-z-environment.md)
- [ZD （& T） 上设置 ADCD](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 pureScale 环境提供 Azure 数据库群集。 它不等同于原始环境中，但它会将类似的可用性和缩放为 IBM DB2 for z/OS 并行 Sysplex 安装程序中运行。

若要开始，请参阅[在 Azure 上的 IBM DB2 pureScale](/azure/virtual-machines/linux/ibm-db2-purescale-azure)。

## <a name="considerations"></a>注意事项

当迁移大型机工作负荷到 Azure 基础结构即服务 (IaaS) 时，可以从多种类型的按需、 可缩放计算资源，包括 Azure Vm 中进行选择。 Azure 提供了一系列[Linux](/azure/virtual-machines/linux/overview)并[Windows](/azure/virtual-machines/windows/overview) Vm。

### <a name="compute"></a>计算

Azure 计算能力来与大型机的容量。 如果您正在考虑将大型机工作负荷转移到 Azure，比较 100 万个说明每秒 (MIPS) 为虚拟 Cpu 的大型机指标。 

了解如何[移到 Azure 的大型机计算](./concepts/mainframe-compute-azure.md)。

### <a name="high-availability-and-failover"></a>高可用性和故障转移

Azure 提供了基于承诺的服务级别协议 (Sla)。 多个 9 可用性是默认情况下，并可以本地或基于异地复制的服务优化 Sla。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

使用 Azure IaaS VM 等时，特定的系统功能提供故障转移支持，例如，故障转移群集实例和[可用性集](/azure/virtual-machines/windows/regions-and-availability#availability-sets)。 当您使用 Azure 平台即服务 (PaaS) 资源时，平台会自动处理故障转移。 示例包括[Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)并[Azure Cosmos DB](/azure/cosmos-db/introduction)。

### <a name="scalability"></a>可伸缩性

大型机通常可以纵向扩展，而云环境可以横向扩展。Azure 提供了一系列[Linux](/azure/virtual-machines/linux/sizes)并[Windows](/azure/virtual-machines/windows/sizes)大小，以满足你的需求。 云还可以扩展或缩减匹配确切的用户规范。 计算 power、 存储和服务[规模](/azure/architecture/best-practices/auto-scaling)按需基于使用情况的计费模式下。

### <a name="storage"></a>存储

在云，一系列灵活、 可扩展的存储选项，并且只为所需内容付费。 [Azure 存储](/azure/storage/common/storage-introduction)为数据对象提供可大规模缩放的对象存储，为云提供文件系统服务、可靠的消息传送存储，以及 NoSQL 存储。 对于 VM，托管和非托管磁盘提供持久且安全的磁盘存储。

了解如何[移动到 Azure 的大型机存储](./concepts/mainframe-storage-azure.md)。

### <a name="backup-and-recovery"></a>备份和恢复

维护你自己的灾难恢复站点可能是昂贵的选择。 Azure 提供的易于实现且具有成本效益选项[备份](/azure/backup/backup-introduction-to-azure-backup)，[恢复](/azure/site-recovery/site-recovery-overview)，并[冗余](/azure/storage/common/storage-redundancy)在本地或区域级别，或通过异地冗余。

## <a name="azure-government-for-mainframe-migrations"></a>对于大型机迁移的 azure 政府版

许多公共部门实体很乐意将移到一个更现代、 灵活的平台其大型机应用程序。 Microsoft Azure Government 是全局的 Microsoft Azure 平台的物理独立的实例，为联邦、 州和地方政府系统打包。 它提供一流的安全性、 保护和专门适用于美国政府机构及其合作伙伴的符合性服务。

Azure 政府版获得临时运营权限 (P-ATO) 操作的 FedRAMP 高影响的系统的需要此类型的环境。

若要开始，下载[大型机应用程序的 Microsoft Azure 政府版云](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)。

## <a name="next-steps"></a>后续步骤

让我们[合作伙伴](partner-workloads.md)，帮助您迁移或重新托管大型机应用程序。 选择合作伙伴解决方案的详细指导，请参阅[平台现代化联盟](https://www.platformmodernization.org/pages/mainframe.aspx)网站。

另请参阅：

- [有关大型机主题的白皮书](mainframe-white-papers.md)
- [大型机迁移](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [故障排除](/azure/virtual-machines/troubleshooting/)
- [揭秘大型机到 Azure 的迁移](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
