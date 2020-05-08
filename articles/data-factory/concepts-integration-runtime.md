---
title: 集成运行时
description: 了解有关 Azure 数据工厂中的集成运行时的信息。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: 214d97822bdb2efbe164c3526939ddbe78777e59
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890734"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure 数据工厂中的集成运行时 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供以下数据集成功能的计算基础结构：

- **数据流：在**托管的 Azure 计算[环境中执行数据流。](concepts-data-flow-overview.md)  
- **数据移动**：在公用网络中的数据存储和专用网络（本地或虚拟专用网络）中的数据存储中复制数据。 它提供对内置连接器、格式转换、列映射以及性能和可扩展数据传输的支持。
- **活动调度**：调度和监视在各种计算服务（例如 Azure Databricks、azure HDInsight、Azure 机器学习、Azure SQL 数据库、SQL Server 等）上运行的转换活动。
- **SSIS 包执行**：在托管的 Azure 计算环境中本机执行 SQL Server 集成服务 (SSIS) 包。

在数据工厂中，活动定义要执行的操作。 链接服务定义目标数据存储或计算服务。 集成运行时提供活动和链接服务之间的桥梁。  它由链接的服务或活动引用，并提供在其中运行活动或从中进行调度的计算环境。 这样一来，可以在最接近目标数据存储的区域中执行活动，或者，以最优性能计算服务的同时满足安全和合规性需求。

## <a name="integration-runtime-types"></a>集成运行时类型

数据工厂提供三种类型的 Integration Runtime （IR），您应该选择最能满足所需的数据集成功能和网络环境要求的类型。  这三种类型是：

- Azure
- 自承载
- Azure-SSIS

下表介绍了针对其中每个集成运行时类型提供的功能和网络支持：

IR 类型 | 公用网络 | 专用网络
------- | -------------- | ---------------
Azure | 数据流<br/>数据移动<br/>活动分派 | &nbsp;
自承载 | 数据移动<br/>活动分派 | 数据移动<br/>活动分派
Azure-SSIS | SSIS 包执行 | SSIS 包执行

下图介绍了如何结合使用不同的集成运行时以提供丰富的数据集成功能和网络支持：

![不同类型的集成运行时](media/concepts-integration-runtime/different-integration-runtimes.png)

## <a name="azure-integration-runtime"></a>Azure 集成运行时

Azure 集成运行时可以：

- 在 Azure 中运行数据流 
- 在云数据存储之间运行复制活动
- 在公用网络中调度以下转换活动： Databricks 笔记本/Jar/Python 活动、HDInsight Hive 活动、HDInsight Pig 活动、HDInsight MapReduce 活动、HDInsight Spark 活动、HDInsight 流式处理活动、机器学习批处理执行活动、机器学习更新资源活动、存储过程活动、Data Lake Analytics U SQL 活动、.NET 自定义活动、Web 活动、查找活动和获取元数据活动。

### <a name="azure-ir-network-environment"></a>Azure IR 网络环境

Azure Integration Runtime 支持连接到数据存储，并计算具有公共可访问终结点的服务。 为 Azure 虚拟网络环境使用自承载集成运行时。

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR 计算资源和缩放
Azure 集成运行时在 Azure 中提供完全托管的无服务器计算。  无需担心基础结构预配、软件安装、修补或容量扩展。  此外，只需为实际使用时间付费。

Azure 集成运行时提供了使用安全、可靠和高性能的方式在云数据存储之间移动数据的本机计算。  可以设置在复制活动上要使用的数据集成单元的数量，相应地，Azure IR 的计算大小弹性地纵向扩展，无需显式调整 Azure 集成运行时的大小。 

活动调度是将活动路由到目标计算服务的一种轻型操作，因此不需要增加此方案的计算大小。

有关创建和配置 Azure IR 的信息，请参阅如何在操作指南中创建和配置 Azure IR。 

> [!NOTE] 
> Azure 集成运行时具有与数据流运行时相关的属性，该运行时定义将用来运行数据流的基础计算基础结构。 

## <a name="self-hosted-integration-runtime"></a>自承载集成运行时

自承载 IR 能够：

- 在专用网络中的云数据存储和数据存储之间运行复制活动。
- 针对本地或 Azure 虚拟网络中的计算资源调度以下转换活动： HDInsight Hive 活动（BYOC-自带群集），HDInsight Pig 活动（BYOC），hdinsight MapReduce 活动（BYOC），HDInsight Spark 活动（BYOC），HDInsight 流式处理活动（BYOC），机器学习批处理执行活动，机器学习更新资源活动，存储过程活动，Data Lake Analytics U SQL 活动，自定义活动（在 Azure Batch 运行）、查找活动和获取元数据活动。

> [!NOTE] 
> 使用自承载集成运行时支持需要自带驱动程序的数据存储，如 SAP Hana、MySQL 等。 有关详细信息，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!NOTE] 
> Java Runtime Environment (JRE) 是自承载 IR 的依赖项。 请确保将 JRE 安装在同一主机上。

### <a name="self-hosted-ir-network-environment"></a>自承载 IR 网络环境

如果你想要在专用网络环境中安全地执行数据集成，而该环境没有从公有云环境直接看到的线路，则可以在企业防火墙后面或虚拟专用网络内安装自承载的 IR 本地环境。  自承载集成运行时仅进行基于出站 HTTP 的连接，以打开 Internet。

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>自承载 IR 计算资源和缩放

在本地计算机或专用网络中的虚拟机上安装自承载 IR。 目前，仅支持在 Windows 操作系统上运行自承载 IR。  

为了获得高可用性和可伸缩性，可以通过在主动-主动模式中将逻辑实例与多个本地计算机相关联来向外扩展自承载 IR。  有关详细信息，请参阅如何[创建和配置自承载 IR](create-self-hosted-integration-runtime.md)文章。

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 集成运行时

若要提升和切换现有 SSIS 工作负荷，可以创建 Azure-SSIS IR 以本机执行 SSIS 包。

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR 网络环境

可以在公用网络或专用网络中配置 Azure-SSIS IR。  通过将 Azure-SSIS IR 加入连接到本地网络的虚拟网络来支持本地数据访问。  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR 计算资源和缩放

Azure-SSIS IR 是完全托管的 Azure VM 群集，专用于运行 SSIS 包。 可使用自己的 Azure SQL 数据库或托管实例服务器托管要附加到该服务器的 SSIS 项目/包 (SSISDB) 的目录。 可以通过指定节点大小纵向扩展计算能力并通过指定群集中的节点数对其进行横向扩展。 可以在认为合适时停止和启动 Azure-SSIS 集成运行时以管理运行的成本。

有关详细信息，请参阅操作方法指南下的“如何创建和配置 Azure-SSIS IR”一文。  创建后，即可使用熟悉的工具（如 SQL Server 数据工具 (SSDT) 和 SQL Server Management Studio (SSMS)）部署和管理现有 SSIS 包，无需对其更改或仅做少量更改。

有关 Azure-SSIS 运行时的详细信息，请参阅以下文章： 

- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文提供了创建 Azure-SSIS IR 的分步说明，并使用 Azure SQL 数据库来托管 SSIS 目录。 
- [如何：创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 本文是教程的拓展延伸，介绍了如何使用 Azure SQL 数据库托管实例以及如何将 IR 加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过在 Azure-SSIS IR 中添加更多节点来扩展 IR。 
- [将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。 此文提供有关将 Azure-SSIS IR 加入 Azure 虚拟网络的概念性信息。 此外，还介绍可以执行哪些步骤来使用 Azure 门户配置虚拟网络，以便 Azure-SSIS IR 能够加入虚拟网络。 

## <a name="integration-runtime-location"></a>集成运行时位置

数据工厂位置是存储数据工厂元数据和启动管道触发所在的位置。 同时，数据工厂可以访问其他 Azure 区域的数据存储和计算数据，在数据存储之间移动数据或使用计算服务处理数据。 此行为通过[全局可用 IR 来实现](https://azure.microsoft.com/global-infrastructure/services/)，以确保数据的符合性、有效性并减少网络对外费用。

IR 位置定义其后端计算的位置，尤其是执行数据移动、活动分派和 SSIS 包执行的位置。 IR 位置可能与数据工厂所属的位置不同。 

### <a name="azure-ir-location"></a>Azure IR 位置

您可以设置某个 Azure IR 的某个位置，在这种情况下，活动执行或调度将在该特定区域中发生。

如果选择使用自动解析 Azure IR （默认值），则

- 对于复制活动，ADF 会尽力自动检测接收器数据存储的位置，然后在同一区域中使用 IR （如果可用），或者在同一地理区域中使用最接近的 IR;如果无法检测接收器数据存储区的区域，则会使用数据工厂区域中的 IR 作为替代项。

  例如，你的工厂是在美国东部创建的， 
  
  - 将数据复制到美国西部的 Azure Blob 时，如果 ADF 成功检测到 Blob 在美国西部，则会在美国西部的 IR 上执行复制活动;如果区域检测失败，将在美国东部的 IR 上执行复制活动。
  - 将数据复制到未检测到其区域的 Salesforce 时，将在美国东部的 IR 上执行复制活动。

  >[!TIP] 
  >如果有严格的数据符合性要求，并需确保数据不离开特定的地域，则可在特定区域显式创建一个 Azure IR，然后使用 ConnectVia 属性将链接服务指向该 IR。 例如，若需将数据从英国南部的 Blob 复制到英国南部的 SQL DW，并且需确保数据不离开英国，请在英国南部创建一个 Azure IR，然后将两个链接服务均链接到该 IR。

- 对于查找/GetMetadata/删除活动执行（也称为管道活动）、转换活动调度（也称为外部活动）和创作操作（测试连接、浏览文件夹列表和表列表、预览数据），ADF 会在数据工厂区域使用 IR。

- 对于数据流，ADF 使用数据工厂区域中的 IR。 

  > [!TIP] 
  > 好的做法是确保数据流与相应的数据存储在同一区域中运行（如果可能）。 可以通过自动解析 Azure IR 来实现此目的（如果数据存储位置与数据工厂位置相同），或在与数据存储相同的区域中创建新的 Azure IR 实例，然后在数据存储区中执行数据流。 

可以在 UI 或活动监视有效负载的管道活动监视视图中监视哪个 IR 位置在活动执行期间生效。

### <a name="self-hosted-ir-location"></a>自承载 IR 位置

自承载 IR 逻辑上注册到数据工厂，用于支持其功能的计算由你提供。 因此，没有适用于自承载 IR 的显式位置属性。 

用于执行数据移动时，自承载 IR 从源提取数据并写入到目标。

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR 位置

为你的 Azure-SSIS IR 选择正确的位置对在提取-转换-加载 (ETL) 工作流中实现高性能至关重要。

- Azure-SSIS IR 的位置不需要与数据工厂的位置相同，但它应该与你自己的 Azure SQL 数据库的位置或要托管 SSISDB 的托管实例服务器的位置相同。 这样一来，Azure-SSIS 集成运行时可以轻松地访问 SSISDB，且不会在不同位置之间产生过多的流量。
- 如果没有现有的 Azure SQL 数据库或托管实例服务器来托管 SSISDB，但有本地数据源/目标，应在连接到本地网络的虚拟网络的同一位置中创建新的 Azure SQL 数据库或托管实例服务器。  这样，你便可以使用新的 Azure SQL 数据库或托管实例服务器创建 Azure-SSIS IR，并将该虚拟网络加入同一位置，从而有效地最大限度地减少了跨不同位置的数据移动。
- 如果 Azure SQL 数据库的现有 Azure SQL 数据库或托管实例服务器的位置与连接到本地网络的虚拟网络的位置不同，请首先使用现有的 Azure SQL 数据库或托管实例服务器创建 Azure-SSIS IR，并在同一位置中加入另一个虚拟网络，然后将虚拟网络配置为不同位置之间的虚拟网络连接。

下图显示了数据工厂及其集成运行时的位置设置：

![集成运行时位置](media/concepts-integration-runtime/integration-runtime-location.png)

## <a name="determining-which-ir-to-use"></a>确定要使用哪个 IR

### <a name="copy-activity"></a>复制活动

对于复制活动，它需要使用源和接收器链接服务，以定义数据流的方向。 以下逻辑用于确定执行复制所使用的集成运行时实例的类型： 

- 在**两个云数据源之间复制**：源和接收器链接服务都在使用 Azure IR 时，如果你选择了 "自动解析 IR" （默认值），则 ADF 使用区域 Azure IR; 如果选择 "[集成运行时位置](#integration-runtime-location)" 部分中所述，则自动确定 Azure IR 位置。
- **在云数据源和专用网络中的数据源之间复制**：如果源或接收器链接服务指向自承载 IR，则在该自承载集成运行时上执行复制活动。
- **在专用网络中的两个数据源之间复制**：源和接收器链接服务必须指向集成运行时的同一个实例，而集成运行时用于执行复制活动。

### <a name="lookup-and-getmetadata-activity"></a>查找和 GetMetadata 活动

查找和 GetMetadata 活动在关联到数据存储链接服务的集成运行时上执行。

### <a name="external-transformation-activity"></a>外部转换活动

利用外部计算引擎的每个外部转换活动都具有指向集成运行时的目标计算链接服务。 此集成运行时实例确定从中调度外部手动编码转换活动的位置。

### <a name="data-flow-activity"></a>数据流活动

数据流活动在与其关联的 Azure 集成运行时上执行。 数据流所利用的 Spark 计算由 Azure Integration Runtime 中的数据流属性决定，由 ADF 完全管理。

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [创建 Azure 集成运行时](create-azure-integration-runtime.md)
- [创建自承载集成运行时](create-self-hosted-integration-runtime.md)
- [创建 AZURE SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 本文是教程的拓展延伸，介绍了如何使用 Azure SQL 数据库托管实例以及如何将 IR 加入虚拟网络。 
