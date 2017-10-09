---
title: "Azure 数据工厂中的集成运行时 | Microsoft Docs"
description: "了解有关 Azure 数据工厂中的集成运行时的信息。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/15/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf05a28c0fe40839e26cef1af51f3f0317e1e154
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="integration-runtime-in-azure-data-factory"></a>Azure 数据工厂中的集成运行时
集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供以下数据集成功能的计算基础结构：

- **数据移动**：在公用网络中的数据存储和专用网络中的数据存储之间移动数据（本地或虚拟专用网络）。 它提供对内置连接器、格式转换、列映射以及性能和可扩展数据传输的支持。
- **活动分派**：分派和监视在各种计算服务（如 Azure HDInsight、Azure 机器学习、Azure SQL 数据库、SQL Server 等等）上运行的转换活动。
- **SSIS 包执行**：在托管的 Azure 计算环境中本机执行 SQL Server 集成服务 (SSIS) 包。


> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 的数据工厂。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [数据工厂版本 1 文档](v1/data-factory-introduction.md)。

在数据工厂中，活动定义要执行的操作。 链接服务定义目标数据存储或计算服务。 集成运行时提供活动和链接服务之间的桥梁。  它被链接服务引用，并提供运行或分派活动的计算环境。  这样一来，可以在最接近目标数据存储的区域中执行活动，或者，以最优性能计算服务的同时满足安全和合规性需求。

## <a name="integration-runtime-types"></a>集成运行时类型
数据工厂提供三种类型的集成运行时，应选择最能满足你所寻求的数据集成功能和网络环境需求的类型。  这三种类型是：

- Azure
- 自承载
- Azure-SSIS

下表介绍了针对其中每个集成运行时类型提供的功能和网络支持：

IR 类型 | 公用网络 | 专用网络
------- | -------------- | ---------------
Azure | 数据移动<br/>活动分派 | &nbsp;
自承载 | 数据移动<br/>活动分派 | 数据移动<br/>活动分派
Azure-SSIS | SSIS 包执行 | SSIS 包执行

下图介绍了如何结合使用不同的集成运行时以提供丰富的数据集成功能和网络支持：

![不同类型的集成运行时](media\concepts-integration-runtime\different-integration-runtimes.png)


## <a name="azure-integration-runtime"></a>Azure 集成运行时
Azure 集成运行时能够：

- 在云数据存储之间运行复制活动
- 在公用网络中分派以下转换活动：HDInsight Hive 活动、HDInsight Pig 活动、HDInsight MapReduce 活动、HDInsight Spark 活动、HDInsight Streaming 活动、Machine Learning Batch Execution 活动、Machine Learning Update Resource 活动、Stored Procedure 活动、Data Lake Analytics U-SQL 活动、Net 自定义活动、Web 活动、Lookup 活动和 Get Metadata 活动。

### <a name="network-environment"></a>网络环境
Azure 集成运行时支持使用可公开访问的终结点连接到公用网络中的数据存储和计算服务。 为 Azure 虚拟网络环境使用自承载集成运行时。

### <a name="compute-resource-and-scaling"></a>计算资源和缩放
Azure 集成运行时在 Azure 中提供完全托管的无服务器计算。  无需担心基础结构配置、软件安装、修补或功能扩展。  此外，只需为实际使用时间付费。

Azure 集成运行时提供了使用安全、可靠和高性能的方式在云数据存储之间移动数据的本机计算。  可以设置在复制活动上要使用的数据移动单位的数量，相应的，Azure IR 的计算大小弹性地纵向扩展，无需显式调整 Azure 集成运行时的大小。

活动分派是将活动路由到目标计算服务的轻型操作，因此，无需纵向扩展此方案的计算大小。

有关创建和配置 Azure IR 的信息，请参阅操作方法指南下的如何创建和配置 Azure IR。 

## <a name="self-hosted-integration-runtime"></a>自承载集成运行时
自承载 IR 能够：

- 在专用网络中的云数据存储和数据存储之间运行复制活动。
- 对本地或 Azure 虚拟网络中的计算资源分派以下转换活动：HDInsight Hive 活动 (BYOC)、HDInsight Pig 活动 (BYOC)、HDInsight MapReduce 活动 (BYOC)、HDInsight Spark 活动 (BYOC)、HDInsight Streaming 活动 (BYOC)、Machine Learning Batch Execution 活动、Machine Learning Update Resource 活动、Stored Procedure 活动、Data Lake Analytics U-SQL 活动、.Net 自定义活动、Lookup 活动和 Get Metadata 活动。

> [!NOTE] 
> 使用自承载集成运行时支持需要自带驱动程序（如 SAP Hana、MySQL 等）的数据存储。有关详细信息，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

### <a name="network-environment"></a>网络环境
如果想要在专用网络环境中安全地执行数据集成（不在公有云环境中建立直通连接），可以在企业防火墙后的本地环境中或虚拟专用网络内安装自承载 IR。  自承载集成运行时仅进行基于出站 HTTP 的连接，以打开 Internet。

### <a name="compute-resource-and-scaling"></a>计算资源和缩放
需要在本地计算机或专用网络中的虚拟机上安装自承载 IR。 目前，仅支持在 Windows 操作系统上运行自承载 IR。  

为了获得高可用性和可伸缩性，可以通过在主动-主动模式中将逻辑实例与多个本地计算机相关联来向外扩展自承载 IR。  有关详细信息，请参阅操作方法指南下的创建和配置自承载 IR 主题，以了解详细信息。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 集成运行时
若要提升和切换现有 SSIS 工作负荷，可以创建 Azure-SSIS IR 以本机执行 SSIS 包。

### <a name="network-environment"></a>网络环境
可以在公用网络或专用网络中配置 Azure-SSIS IR。  通过将 Azure-SSIS IR 加入连接到你本地网络的虚拟网络 (VNet) 支持本地数据访问。 目前，仅支持经典 VNet。 

### <a name="compute-resource-and-scaling"></a>计算资源和缩放
Azure-SSIS IR 是完全托管的 Azure VM 群集，专用于运行 SSIS 包。 可以使用自己的 Azure SQL 数据库或托管实例（个人预览版）服务器托管附加到 SSIS 项目/包 (SSISDB) 的目录。 可以通过指定节点大小纵向扩展计算能力并通过指定群集中的节点数对其进行横向扩展。 可以在认为合适时停止和启动 Azure-SSIS 集成运行时以管理运行的成本。

有关详细信息，请参阅操作方法指南下的创建和配置 Azure-SSIS IR 主题。  创建后，即可使用熟悉的工具（如 SQL Server 数据工具 (SSDT) 和 SQL Server Management Studio (SSMS)）部署和管理现有 SSIS 包，无需对其更改或仅做少量更改。

## <a name="determining-which-ir-to-use"></a>确定要使用哪个 IR
每个转换活动都有一个目标计算链接服务，该服务指向集成运行时。 该集成运行时实例是分派转换活动的实例。

对于复制活动，它需要使用源和接收器链接服务，以定义数据流的方向。 以下逻辑用于确定执行复制所使用的集成运行时实例的类型： 

- **在两个云数据源之间复制**：当源和接收器链接服务都使用 Azure IR 时，接收器链接服务所使用的集成运行时用于执行复制活动。
- **在云数据源和专用网络中的数据源之间复制**：如果源或接收器链接服务指向自承载 IR，则在该自承载集成运行时上执行复制活动。
- **在专用网络中的两个数据源之间复制**：源和接收器链接服务必须同时指向同一集成运行时实例，且该集成运行时用于执行复制活动。

下图介绍两个复制活动示例：

- 对于复制活动 1，其源是引用自承载 IR A 的 SQL Server 链接服务，且其接收器是引用 Azure IR B 的 Azure 存储链接服务。当复制活动运行时，它在自承载 IR A 上执行。
- 对于复制活动 2，其源是引用 Azure IR C 的 Azure SQL 数据库链接服务，且其接收器是引用 Azure IR B 的 Azure 存储链接服务。复制活动运行时，它在 Azure IR B 上执行，因为它是供接收器链接服务使用的集成运行时。

![要使用哪个 IR](media/concepts-integration-runtime/which-integration-runtime-to-use.png)

## <a name="integration-runtime-location"></a>集成运行时位置
数据工厂位置是存储数据工厂元数据和启动管道触发所在的位置。 目前，支持的数据工厂位置有：美国东部、美国东部 2。 但是，数据工厂可以访问其他 Azure 区域的数据存储和计算数据，在数据存储之间移动数据或使用计算服务处理数据。 此行为通过多个区域中全局可用的 IR 来实现，以确保数据的合规性、有效性并减少网络对外费用。

IR 位置定义其后端计算的位置，尤其是执行数据移动、活动分派和 SSIS 包执行的位置。 IR 位置可能与数据工厂所属的位置不同。 下图显示了数据工厂及其集成运行时的位置设置：

![集成运行时位置](media/concepts-integration-runtime/integration-runtime-location.png)

### <a name="azure-ir"></a>Azure IR
数据工厂使用最接近同一地域的接收器的区域中的 Azure IR 来移动数据。 请参照下表进行映射：

接收器数据存储的地理位置 | 接收器数据存储的位置 | Azure 集成运行时使用的位置
-------------------------------| ----------------| ------------------
美国 | 美国东部 | 美国东部
&nbsp; | 美国东部 2 | 美国东部 2
&nbsp; | 美国中部 | 美国中部
&nbsp; | 美国中北部 | 美国中北部
&nbsp; | 美国中南部 | 美国中南部
&nbsp; | 美国中西部 | 美国中西部
&nbsp; | 美国西部 | 美国西部
&nbsp; | 美国西部 2 | 美国西部
加拿大 | 加拿大东部 | 加拿大中部
&nbsp; | 加拿大中部 | 加拿大中部
巴西 | 巴西南部 | 巴西南部
欧洲 | 北欧 | 欧洲北部
&nbsp; | 欧洲西部 | 西欧
英国 | 英国西部 | 英国南部
&nbsp; | 英国南部 | 英国南部
亚太区 | 东南亚 | 东南亚
&nbsp; | 东亚 | 东南亚
澳大利亚 | 澳大利亚东部 | 澳大利亚东部
&nbsp; | 澳大利亚东南部 | 澳大利亚东南部
日本 | 日本东部 | 日本东部
&nbsp; | 日本西部 | 日本东部
韩国 | 韩国中部 | 韩国南部
&nbsp; | 韩国南部 | 韩国南部
印度 | 印度中部 | 印度中部
&nbsp; | 印度西部 | 印度中部
&nbsp; | 印度南部 | 印度中部

也可将 Azure IR 的位置设置为自动解决，这意味着数据工厂在基于链接服务定义自动检测要使用的最佳位置时尽最大努力。

> [!NOTE] 
> 如果目标数据存储的区域不在列表中或未找到该区域，出于合规性原因，活动会失败，而不会通过其他区域完成。 在这种情况下，显式指示用于执行复制的其他位置。
 
下图显示了 Azure IR 的位置被设置为自动解决时的有效位置示例。 复制活动执行时，它检测数据目标的位置，在本示例中是“日本西部”。  基于该表，“日本东部”中的 Azure IR 用于执行实际数据复制。 使用同一 IR 连接到 Spark 活动的 HDInsight 时，Spark 应用程序提交从数据工厂位置进行（在该示例中，是美国东部），而 Spark 应用程序的实际执行从 HDInsight 服务器位置进行。 

![有效位置](media/concepts-integration-runtime/effective-location.png)

### <a name="self-hosted-ir"></a>自承载 IR
自承载 IR 逻辑上注册到数据工厂，用于支持其功能的计算由你提供。 因此，没有适用于自承载 IR 的显式位置属性。 

用于执行数据移动时，自承载 IR 从源提取数据并写入到目标。

### <a name="azure-ssis-ir"></a>Azure-SSIS IR
为你的 Azure-SSIS IR 选择正确的位置对在提取-转换-加载 (ETL) 工作流中实现高性能至关重要。  预览版最初提供两个位置（美国东部和北欧）。

- Azure-SSIS IR 的位置无需与数据工厂的位置相同，但它应与你自己要托管 SSISDB 的 Azure SQL 数据库/托管实例（个人预览版）服务器的位置相同。 这样一来，Azure-SSIS 集成运行时可以轻松地访问 SSISDB，且不会在不同位置之间产生过多的流量。
- 如果没有托管 SSISDB 的现有 Azure SQL 数据库/托管实例（个人预览版）服务器，但有本地数据源/目标，应在连接到你本地网络的 VNet 的同一位置中创建新的 Azure SQL 数据库/托管实例（个人预览版）服务器。  这样一来，可以使用新的 Azure SQL 数据库/托管实例（个人预览版）服务器创建你的 Azure-SSIS IR 并连接该 VNet，全部在同一位置进行，从而有效地最大程度减少跨不同位置的数据移动。
- 如果托管 SSISDB 所在的现有 Azure SQL 数据库/托管实例（个人预览版）服务器的位置与连接到你本地网络的 VNet 的位置不同，首先使用现有 Azure SQL 数据库/托管实例（个人预览版）服务器创建你的 Azure-SSIS IR，并在同一位置连接其他 VNet，然后在不同的位置之间配置 Vnet 到 Vnet 连接。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [创建自承载集成运行时](create-self-hosted-integration-runtime.md)

