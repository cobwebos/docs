---
title: Azure 上的 SAP：Azure 上支持哪些 SAP 软件
description: 说明支持在 Azure 上部署哪些 SAP 软件
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
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cfc66789360f78c927dd157305074ed5ea6f32a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768444"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure 部署支持哪些 SAP 软件
本文介绍如何了解 Azure 部署支持哪些 SAP 软件，以及什么是必要的操作系统版本或 DBMS 版本。

评估当前 SAP 软件是否受支持，Azure 中的 SAP 软件支持哪些操作系统和 DBMS 版本，您需要访问：

- SAP 支持说明
- SAP 产品可用性矩阵



## <a name="general-restrictions-for-sap-workload"></a>SAP 工作负载的一般限制
可用于 SAP 工作负载的 Azure IaaS 服务仅限于 x86-64 或 x64 硬件。 没有适用于 SAP 工作负载的基于 Sparc 或 Power CPU 的优惠。 在 IBM 大型机或 AS400 等硬件体系结构专有的操作系统上运行其应用程序的客户，或者在使用 HP-UX、Solaris 或 AIX 的操作系统时，需要将其 SAP 应用程序（包括 DBMS）更改为以下操作系统之一：

- x86-64 平台的 Windows 服务器 64 位
- X86-64 平台的 SUSE linux 64 位
- 红帽Linux 64Bit用于x86-64平台
- 用于 x86-64 平台的 Oracle Linux 64 位

与 SAP 软件结合使用，不支持其他操作系统版本或 Linux 发行版。 有关特定版本和案例的准确详细信息将记录在文档的后面部分。


## <a name="you-start-here"></a>从这里开始
您的起点是 SAP[支持说明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 从上到下浏览此 SAP 说明时，将显示支持的软件和 VM 的几个领域

第一节列出了 Azure VM 中 SAP 软件普遍支持的操作版本的最低要求。 如果您没有达到这些最低要求并运行这些操作系统的旧版本，则需要将操作系统版本升级到此类最低版本，甚至更新版本。 Azure 通常支持其中一些操作系统的旧版本是正确的。 但列出的限制或最小版本基于已执行的测试和资格，不会进一步扩展。 


> [!NOTE]
>有一些特定的 VM 类型、HANA 大型实例或 SAP 工作负载需要更新的操作系统版本。 在文件中将提及类似案例。 类似案例清楚地记录在 SAP 说明或其他 SAP 出版物中。

以下部分列出了受支持的版本以及受支持的 SAP 内核支持的通用 SAP 平台。 它列出了 NetWeaver/ABAP 或 Java 堆栈受支持，需要最少的内核版本。 Azure 上支持较新的 ABAP 堆栈，但不需要最少的内核版本，因为 Azure 的更改是从开发较新的堆栈开始时实现的

您需要检查：

- 您正在运行的 SAP 应用程序是否包含在所述的最低版本中。 如果没有，则需要定义新的目标版本，签入 SAP 产品可用性矩阵、新目标版本支持哪些操作系统生成和 DBMS 组合。 因此，您可以选择正确的操作系统版本和 DBMS 版本
- 是否需要在迁移到 Azure 时更新 SAP 内核
- 是否需要更新 SAP 支持包。 特别是基础支持包，在需要迁移到较新的 DBMS 版本的情况下可能需要这些包


下一节将介绍 SAP 支持的 Windows 和 Linux Azure 上支持的其他 SAP 产品和 DBMS 版本的详细信息。 

> [!NOTE]
> 不同的 DBMS 的最小版本是经过精心挑选的，可能并不总是反映 DBMS 发布的整个 DBMS 版本，而不同的 DBMS 供应商通常支持 Azure。 许多 SAP 工作负载相关注意事项都考虑在定义这些最小版本。 无需努力测试和鉴定较旧的 DBMS 版本。 

> [!NOTE]
> 列出的最小版本表示旧版本的操作系统和数据库版本。 我们强烈建议使用最新的操作系统版本和数据库版本。 在许多情况下，较新的操作系统和数据库版本考虑了在公共云中运行的使用情况，并调整了代码以优化在公共云或更具体 Azure 中运行

## <a name="oracle-dbms-support"></a>Oracle DBMS 支持
操作系统、Oracle DBMS 版本和 Azure 上支持的 Oracle 功能特别列在[SAP 支持说明#2039619](https://launchpad.support.sap.com/#/notes/2039619)。 注释的精髓可以概括如下：

- 通过 NetWeaver 认证的 Azure VM 上支持的最小 Oracle 版本是 Oracle 11g 版本 2 修补程序集 3 （11.2.0.4）
- 作为来宾操作系统，只有 Windows 和 Oracle Linux 才符合条件。 说明中列出了操作系统和相关最小 DBMS 版本的确切版本
- Oracle Linux 的支持也扩展到 Oracle DBMS 客户端。 这意味着所有 SAP 组件（如 ABAP 或 Java 堆栈的对话框实例）都需要在 Oracle Linux 上运行。 只有此类 SAP 系统中无法连接到 Oracle DBMS 的 SAP 组件才允许运行不同的 Linux 操作系统
- 不支持 Oracle RAC 
- 某些案例支持 Oracle ASM。 详细信息列在注释中
- 只有使用 Windows 来宾操作系统运行的应用程序服务器才支持非 Unicode SAP 系统。 DBMS 的来宾操作系统可以是 Oracle Linux 或 Windows。 检查 SAP 产品可用性矩阵 （PAM） 时，此限制的原因显而易见。 对于 Oracle Linux，SAP 从未发布非 Unicode SAP 内核

了解目标 Azure 基础结构支持的 DBMS 版本，您需要检查 SAP 产品可用性矩阵，了解您打算运行的 SAP 产品版本是否支持所需的操作系统版本和 DBMS。 


## <a name="sap-hana-support"></a>SAP HANA 支持
在 Azure 中有两个服务，可用于运行 HANA 数据库：

- Azure 虚拟机
- [HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

对于运行 SAP HANA，SAP 比运行 NetWeaver 或其他 SAP 应用程序和 DBMS 相比，基础架构需要满足更多、更强的条件。 因此，少数 Azure VM 有资格运行 SAP HANA DBMS。 SAP HANA 支持的支持 Azure 基础结构的列表可以在所谓的[SAP HANA 硬件目录中](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)找到。 

> [!NOTE]
> 以字母"S"开头的单位是[HANA 大型实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)单位。 

> [!NOTE]
> SAP 没有特定的认证依赖于 SAP HANA 主要版本。 与普遍看法相反，该栏目在[HANA认证IaaS平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中的**认证方案**，该列**没有声明HANA主要或次要版本认证**。 您需要假设，只要 HANA 1.0 版本也支持特定单元的经过认证的操作系统版本，就可以为 HANA 1.0 和 HANA 2.0 列出的所有设备。 

对于 SAP HANA 的使用，可能应用与一般 NetWeaver 案例不同的最低操作系统版本。 您需要单独签出每个单元的支持操作系统，因为这些操作系统可能有所不同。 通过单击每个单元来执行此操作。 将显示更多详细信息。 列出的详细信息之一是此特定单元支持的不同操作系统。

> [!NOTE]
> 与 Azure VM 相比，Azure HANA 大型实例单元与受支持的操作系统相比更为严格。 另一方面，Azure VM 可能会强制使用较新的操作版本作为最小版本。 对于一些需要更改 Linux 内核的大型 VM 单元尤其如此

了解 Azure 基础结构受支持的操作系统，需要检查[SAP 支持说明#2235581](https://launchpad.support.sap.com/#/notes/2235581)有关所针对的 Azure 单元支持的确切 SAP HANA 版本和修补程序级别。 

> [!IMPORTANT]
> 检查支持的确切 SAP HANA 版本和修补程序级别的步骤非常重要。 在许多情况下，对特定操作系统版本的支持取决于 SAP HANA 可执行文件的特定修补程序级别。

如果您知道可以在目标 Azure 基础结构上运行的特定 HANA 版本，因此需要签入 SAP 产品可用性矩阵，以了解支持筛选出来的 HANA 版本的 SAP 产品版本是否存在限制


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>认证的 Azure VM 和 HANA 大型实例单元和业务事务吞吐量
除了评估支持的操作系统版本、DBMS 版本和 Azure 基础结构单元的从属支持 SAP 软件版本外，还需要按业务事务吞吐量对这些单元进行限定，SAP 在单元"SAP"中表示。 所有 SAP 大小取决于 SAPS 计算。 评估现有的 SAP 系统时，您通常可以在基础结构提供商的帮助下计算单位的 SAPS。 对于 DBMS 层以及应用程序层。 在创建新功能的其他情况下，使用 SAP 进行大小调整练习可以显示应用程序层和 DBMS 层所需的 SAPS 编号。 作为基础设施提供商，Microsoft 有义务提供 NetWeaver 和/或 HANA 认证的不同单元的 SAP 吞吐量特征。

对于 Azure VM，这些 SAPS 吞吐量编号记录在[SAP 支持说明#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 对于 Azure HANA 大型实例单元，SAPS 吞吐量编号记录在[SAP 支持说明#2316233](https://launchpad.support.sap.com/#/notes/2316233)

查看[SAP 支持说明#1928533，](https://launchpad.support.sap.com/#/notes/1928533)以下注释适用：

- **对于 M 系列 Azure VM 和 Mv2 系列 Azure VM，应用的最小操作系统版本与其他 Azure VM 类型不同**。 对更新的操作系统版本的要求基于不同操作系统供应商在其操作系统版本中必须提供的更改，以便启用在特定 Azure VM 类型上运行的操作系统，或者优化这些 VM 类型的 SAP 工作负载的性能和吞吐量
- 有两个表指定不同的 VM 类型。 第二个表指定仅支持 Azure 标准存储的 Azure VM 类型的 SAPS 吞吐量。 不支持在注释的第二个表中指定的单元上的 DBMS 部署


## <a name="other-sap-products-supported-on-azure"></a>Azure 上支持的其他 SAP 产品
通常，假设在 Azure 等超大规模云的状态下，大多数 SAP 软件应在 Azure 中运行，而不会出现功能问题。 然而，与私有云可视化相反，SAP 仍然明确表达了对不同 hyerpscale 云提供商的不同 SAP 产品的支持。 因此，有不同的 SAP 支持说明，指示对 Azure 的不同 SAP 产品的支持。 

对于业务对象 BI 平台[，SAP 支持说明#2145537](https://launchpad.support.sap.com/#/notes/2145537)提供了 Azure 上支持的 SAP 业务对象产品的列表。 如果有关软件版本和操作系统版本的组件或组合的问题似乎未列出或支持，并且比列出的最小版本更新，则需要针对您询问支持的组件打开 SAP 支持请求。

对于业务对象数据服务[，SAP 支持说明#22288344](https://launchpad.support.sap.com/#/notes/2288344)解释了在 Azure 上运行的 SAP 数据服务的最低支持。 

> [!NOTE]
> 如 SAP 支持说明所示，您需要签入 SAP PAM 以确定要在 Azure 上支持的正确支持包级别

Azure 库伯奈斯服务 （AKS） 中的 SAP Datahub/Vora 支持在[SAP 支持说明#2464722](https://launchpad.support.sap.com/#/notes/2464722)

[SAP 支持说明](https://launchpad.support.sap.com/#/notes/2451795)#2451795介绍了对 SAP BPC 10.1 SP08 的支持

[在 Hybris Wiki](https://cxwiki.sap.com/display/cloudss/Using+the+hybris+Platform+with+the+Cloud)中详细介绍了对 Azure 上的 SAP Hybris 商务平台的支持。 作为 SAP Hybris 商务平台支持的 DBMS，它列出了如下：

- Windows 操作系统平台上的 SQL Server 和 Oracle。 与 SAP NetWeaver 相同的最小版本适用。 有关详细信息，请参阅[SAP 支持说明#1928533](https://launchpad.support.sap.com/#/notes/1928533)
- 在红帽和SUSE Linux上的SAP HANA。 SAP HANA 认证的 VM 类型是必需的，[本文档前面所述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support)。 SAP（Hybris） 商务平台被视为 OLTP 工作负载
- SQL Azure DB 截至 SAP（Hybris） 商务平台版本 1811




## <a name="next-steps"></a>后续步骤
阅读[SAP NetWeaver Azure 虚拟机规划和实现的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)后续步骤

