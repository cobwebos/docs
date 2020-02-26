---
title: Azure 上的 SAP： Azure 支持的 SAP 软件
description: 说明支持在 Azure 上部署的 SAP 软件
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08852a9421d714b601d724c5309153a028c960bb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599075"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 部署支持的 SAP 软件
本文介绍如何了解 Azure 部署支持的 SAP 软件以及所需的操作系统版本或 DBMS 版本。

无论你当前的 SAP 软件是受支持的，还是 Azure 中的 SAP 软件支持的操作系统和 DBMS 版本，你都需要访问：

- SAP 支持说明
- SAP 产品可用性矩阵



## <a name="general-restrictions-for-sap-workload"></a>SAP 工作负荷的一般限制
可用于 SAP 工作负荷的 Azure IaaS 服务仅限 x86-64 或 x64 硬件。 没有适用于 SAP 工作负荷的基于 Sparc 或功率 CPU 的产品/服务。 如果客户在其应用程序上运行的操作系统专用于 IBM 大型机或 AS400 等硬件体系结构，或在其中使用的操作系统 hp-ux、Solaris 或 AIX，则需要将其 SAP 应用程序（包括 DBMS）更改为以下操作系统：

- 适用于 x86 64 平台的 Windows server 64 位
- 适用于 x86 64 平台的 SUSE linux 64 位
- 适用于 x86 64 平台的 Red hat Linux 64 位
- X86-64 平台 Oracle Linux 64 位

结合 SAP 软件，不支持任何其他操作系统版本或 Linux 分发版。 本文档稍后将详细介绍特定版本和事例。


## <a name="you-start-here"></a>从这里开始
你的开始点是[SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 在从上到下进行此 SAP 说明时，将显示受支持的软件和 Vm 的多个区域

第一部分列出了通常在 Azure Vm 中的 SAP 软件支持的操作系统的最低要求。 如果未达到这些最低要求并运行这些操作系统的较早版本，则需要将操作系统版本升级到此类最低版本或更高版本。 这是正确的，Azure 通常支持某些操作系统的较旧版本。 但列出的限制或最小版本基于所执行的测试和限定，不会进一步进一步扩展。 


> [!NOTE]
>有一些特定 VM 类型，HANA 大型实例或 SAP 工作负荷将需要更多最新的操作系统版本。 在整个文档中会提到这样的情况。 例如，SAP 说明或其他 SAP 发布中清楚地记录了这种情况。

以下部分列出了支持的版本所支持的常规 SAP 平台，并且更重要的是支持的 SAP 内核。 它列出了支持的和的 NetWeaver/ABAP 或 Java 堆栈，它们需要最少的内核版本。 Azure 上支持较新的 ABAP 堆栈，但不需要最小内核版本，因为对 Azure 的更改是在开始进行更多最近的堆栈的开发后实现的

需要检查：

- 你正在运行的 SAP 应用程序是否被所述的最低版本涵盖。 如果没有，则需要定义新的目标版本、查看 SAP 产品可用性矩阵、新目标版本支持的操作系统版本和 DBMS 组合。 因此，你可以选择正确的操作系统版本和 DBMS 版本
- 是否需要在迁移到 Azure 时更新 SAP 内核
- 是否需要更新 SAP 支持包。 特别是在需要移动到较新的 DBMS 版本的情况下，可能需要的基础支持包


下一部分将详细介绍适用于适用于 Windows 和 Linux 的 Azure 上的 SAP 所支持的其他 SAP 产品和 DBMS 版本。 

> [!NOTE]
> 不同 DBMS 的最小版本是仔细选择的，并且可能不会始终反映 Azure 上不同 DBMS 供应商在 Azure 上的完全支持。 许多与 SAP 工作负荷相关的注意事项是为了定义这些最低版本。 不需要测试和限定旧版 DBMS 版本。 

> [!NOTE]
> 列出的最小版本是代表较早版本的操作系统和数据库版本。 我们强烈建议使用最新的操作系统版本和数据库版本。 在很多情况下，较新的操作系统和数据库版本都考虑到在公有云中运行的使用情况，并改编代码以优化在公有云或更具体地说的 Azure 中运行的代码

## <a name="oracle-dbms-support"></a>Oracle DBMS 支持
Azure 支持的操作系统、Oracle DBMS 版本和 Oracle 功能专门在[SAP 支持说明 #2039619](https://launchpad.support.sap.com/#/notes/2039619)中列出。 在此说明中，其内容可归纳如下：

- 已通过 NetWeaver 认证的 Azure Vm 支持的最低 Oracle 版本是 Oracle 11g Release 2 补丁集3（11.2.0.4）
- 作为来宾操作系统，只有 Windows 和 Oracle Linux 合格。 注释中列出了精确版本的操作系统和相关的最小 DBMS 版本
- 对 Oracle Linux 的支持也会扩展到 Oracle DBMS 客户端。 这意味着所有 SAP 组件（如 ABAP 或 Java Stack 的对话框实例）也需要在 Oracle Linux 上运行。 只有此类 SAP 系统内不能连接到 Oracle DBMS 的 SAP 组件才允许运行其他 Linux 操作系统
- 不支持 Oracle RAC 
- 某些情况下支持 Oracle ASM。 注释中列出了详细信息
- 非 Unicode SAP 系统仅在运行 Windows 来宾操作系统的应用程序服务器上受支持。 DBMS 的来宾操作系统可以是 Oracle Linux 或 Windows。 检查 SAP 产品可用性矩阵（PAM）时，此限制的原因很明显。 对于 Oracle Linux，SAP 从不发布非 Unicode SAP 内核

了解目标 Azure 基础结构支持的 DBMS 版本后，需要检查 SAP 产品可用性矩阵，确定要运行的 SAP 产品版本是否支持所需的操作系统版本和 DBMS。 


## <a name="sap-hana-support"></a>SAP HANA 支持
在 Azure 中，有两个服务可用于运行 HANA 数据库：

- Azure 虚拟机
- [HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

对于运行 SAP HANA，SAP 具有比用于运行 NetWeaver 或其他 SAP 应用程序和 DBMS 更多且更强的条件。 因此，更少数量的 Azure Vm 有资格运行 SAP HANA DBMS。 SAP HANA 支持的支持的 Azure 基础结构的列表，可在所谓的[SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中找到。 

> [!NOTE]
> 以字母 "s" 开头的单位为[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)单位。 

对于 SAP HANA 的使用情况，不同的最低操作系统版本可能适用于一般的 NetWeaver 事例。 需要单独检查每个单元支持的操作系统，因为这些系统可能会有所不同。 为此，请单击每个单元。 将显示更多详细信息。 列出的详细信息之一是此特定单元支持的不同操作系统。

> [!NOTE]
> 与 Azure Vm 相比，azure HANA 大型实例单元对受支持的操作系统的限制更强。 另一方面，Azure Vm 可能会强制实施更高版本的最新版本。 这对于需要更改 Linux 内核的更大 VM 单位尤其如此

了解 Azure 基础结构的受支持的操作系统时，需要查看[SAP 支持说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581) ，以了解所面向的 Azure 单位所支持的准确 SAP HANA 版本和修补程序级别。 

> [!IMPORTANT]
> 检查正确 SAP HANA 版本和修补程序级别的步骤非常重要。 在很多情况下，对特定 OS 版本的支持取决于 SAP HANA 可执行文件的特定修补程序级别。

当你了解可以在目标 Azure 基础结构上运行的特定 HANA 版本时，需要查看 SAP 产品可用性矩阵，以确定 SAP 产品版本是否存在对支持你筛选出的 HANA 版本的限制


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>认证的 Azure Vm 和 HANA 大型实例单元和业务事务吞吐量
除了评估受支持的操作系统版本之外，DBMS 版本和从属支持适用于 Azure 基础结构单元的 SAP 软件版本，你还需要通过业务事务吞吐量（通过SAP. 所有 SAP 大小调整都围绕整个 SAP。 评估现有 SAP 系统，你通常可以在基础结构提供商的帮助下计算单元的 SAP。 对于 DBMS 层和应用程序层也是如此。 在创建新功能的其他情况下，使用 SAP 进行的调整调整会显示应用程序层和 DBMS 层所需的 SAP 编号。 作为基础结构提供商，Microsoft 不必，提供 NetWeaver 和/或 HANA 认证的不同单位的 SAP 吞吐量特征。

对于 Azure Vm， [sap 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中介绍了这些 sap 吞吐量数字。 对于 Azure HANA 大型实例单元， [sap 支持说明](https://launchpad.support.sap.com/#/notes/2316233)中记录了 sap 吞吐量数字 #2316233

查看[SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)，以下备注适用：

- **对于 M 系列 Azure vm 和 Mv2 系列 Azure vm，不同的最低操作系统版本适用于其他 AZURE vm 类型**。 对于更高版本的操作系统版本，要求基于不同的操作系统版本，这些供应商在其操作系统版本中必须提供这些更改，以使其操作系统能够在特定 Azure VM 类型上运行或优化性能和这些 VM 类型上的 SAP 工作负荷吞吐量
- 有两个指定不同 VM 类型的表。 第二个表指定仅支持 Azure 标准存储的 Azure VM 类型的 SAP 吞吐量。 不支持在注释的第二个表中指定的单元上进行 DBMS 部署


## <a name="other-sap-products-supported-on-azure"></a>Azure 上支持的其他 SAP 产品
一般来说，在超大规模云（如 Azure）的状态下，大多数 SAP 软件都应在 Azure 中运行，而不会出现功能问题。 尽管如此，对于私有云可视化，SAP 仍会针对不同的 hyerpscale 云提供程序明确地表达不同 SAP 产品的支持。 因此，有不同的 SAP 支持说明指出了不同 SAP 产品对 Azure 的支持。 

对于业务对象 BI 平台， [SAP 支持说明 #2145537](https://launchpad.support.sap.com/#/notes/2145537)提供 Azure 上支持的 SAP 业务对象产品列表。 如果有疑问，如未列出或不受支持的软件版本和操作系统版本的组件或组合以及比列出的最低版本更晚，则需要针对所查询的组件打开 SAP 支持请求支持。

对于业务对象数据服务， [SAP 支持说明 #22288344](https://launchpad.support.sap.com/#/notes/2288344)说明在 Azure 上运行的 SAP 数据服务的最低支持。 

> [!NOTE]
> 如 SAP 支持说明中所示，你需要签入 SAP PAM 以确定要在 Azure 上支持的正确支持包级别

Sap Datahub/Vora 支持 Azure Kubernetes Services （AKS）在[sap 支持说明 #2464722](https://launchpad.support.sap.com/#/notes/2464722)

[Sap 支持说明](https://launchpad.support.sap.com/#/notes/2451795)中介绍了对 sap BPC 10.1 SP08 的支持 #2451795

[Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud)中详细介绍了 Azure 上的 SAP Hybris Commerce Platform 6.x 和1.x 的支持


## <a name="next-steps"></a>后续步骤
阅读[SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的后续步骤

