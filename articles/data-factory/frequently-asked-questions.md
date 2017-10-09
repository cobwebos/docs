---
title: "Azure 数据工厂 - 常见问题解答 | Microsoft Docs"
description: "有关 Azure 数据工厂的常见问题解答。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 95b088d2fdc331dc33e973172d32892693fcb648
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure 数据工厂 - 常见问题解答
本文适用于 Azure 数据工厂服务版本 2。 它提供了常见问题 (FAQ) 及其解答的列表。  

## <a name="what-is-azure-data-factory"></a>什么是 Azure 数据工厂？ 
数据工厂是一项完全托管的基于云的数据集成服务，可以自动移动和转换数据。 如同工厂运转设备将原材料转换为成品一样，Azure 数据工厂可协调现有的服务，收集原始数据并将其转换为随时可用的信息。 Azure 数据工厂可用于创建数据驱动的工作流，以同时在本地和云数据存储区之间移动数据，以及使用 Azure HDInsight、Azure Data Lake Analytics 和 SQL Server Integration Services (SSIS) Integration Runtime 等计算服务来处理/转换数据。 数据工厂允许你在基于 Azure 的云服务上或者利用你自己的自托管计算环境（例如 SSIS、SQL Server 和 Oracle）来执行数据处理。 创建管道（用于执行所需操作）后，可以将它计划为定期运行（每小时、每天、每周等），或者在发生某个事件时触发管道。 有关详细信息，请参阅 [Azure 数据工厂简介](introduction.md)。

## <a name="whats-different-in-version-2"></a>版本 2 有何独特之处？
Azure 数据工厂版本 2 基于原始 Azure 数据工厂数据移动和转换服务构建，扩展到更为广泛的一组云优先的数据集成方案。 Azure 数据工厂版本 2 引进了以下功能：

- 控制流和缩放
- 在 Azure 中部署和运行 SSIS 包

随着版本 1 的发布，我们认识到，客户需要设计复杂的混合数据集成方案，该方案需要在云、本地和云虚拟机中进行数据移动和处理。 这些要求需要在受保护的 VNET 环境中转换和处理数据，以及通过按需处理功能进行横向扩展的能力。

由于管道变为业务分析策略的关键部分，我们见证到这些关键数据活动需要灵活的计划，以支持增量数据加载和触发事件的执行。 最后，由于这些业务流程变得越来越复杂，对支持常见工作流范例（包括分支、循环条件处理）的要求也越来越高。

在版本 2 中，也可以将现有 SQL Server Integration Services (SSIS) 包迁移到云，以利用“集成运行时”(IR) 的新功能将 SSIS 提升和切换为在 ADF 中托管的 Azure 服务。 通过在版本 2 中旋转 SSIS IR，可以在云中执行、管理、监视和构建 SSIS 包。

### <a name="control-flow-and-scale"></a>控制流和缩放 
为了支持现代数据仓库中的各种集成流和模式，数据工厂启用了新的灵活的数据管道模型，该模型不再与时序数据绑定。 在此版本中，可以在数据管道的控制流中对条件语句进行建模，设置分支，以及在这些流中（或跨这些流）显式传递参数。

现在可以自由地对数据集成所需的任何流样式进行建模，并可以按需调度或按时钟计划重复调度。 现在已启用的几个常见流（以前无法启用）包括：   

- 控制流：
    - 在管道中将活动按顺序链接起来
    - 在管道中对活动进行分支
    - 参数
        - 可在管道级别定义参数，并且在按需或从触发器调用管道时传递自变量
        - 活动可以使用传递给管道的参数
    - 自定义状态传递
        - 包含状态的活动输出可供管道中的后续活动使用
    - 循环容器
        - For-each 
- 基于触发器的流
    - 可以按需或按时钟时间触发管道
- 增量流
    - 使用参数并定义用于增量复制的高水位标记，同时移动本地或云中的关系存储中的维度或引用表，以将数据载入 Lake 

有关详细信息，请参阅[教程：控制流](tutorial-control-flow.md)。

### <a name="deploy-ssis-packages-to-azure"></a>将 SSIS 包部署到 Azure 
如果想要移动 SSIS 工作负荷，可以创建一个版本 2 数据工厂，并预配 Azure SSIS 集成运行时 (IR)。 Azure-SSIS IR 是由 Azure VM（节点）构成的完全托管群集，专用于在云中运行 SSIS 包。 有关分步说明，请参阅教程：[将 SSIS 包部署到 Azure](tutorial-deploy-ssis-packages-azure.md)。 
 

### <a name="sdks"></a>SDK
如果你是高级用户并在寻找程序设计界面，版本 2 提供了一组丰富的 SDK，可用于使用你最喜欢的 IDE 创作、管理和监视管道。

- .NET SDK - .NET SDK 在版本 2 中进行了更新。 
- PowerShell - PowerShell cmdlet 在版本 2 中进行了更新。 在版本 2 中，cmdlet 的名称中包含 **DataFactoryV2**。 例如：Get-AzureRmDataFactoryV2。 
- Python SDK - 此 SDK 是版本 2 中新增的。
- REST API - REST API 在版本 2 中进行了更新。  

在版本 2 中进行了更新的 SDK 不能向后兼容版本 1 客户端。 

### <a name="monitoring"></a>监视
当前，版本 2 仅支持使用 SDK 监视数据工厂。 门户尚不支持对版本 2 的数据工厂进行监视。 

## <a name="what-is-integration-runtime"></a>什么是集成运行时？
集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供以下数据集成功能的计算基础结构：

- **数据移动**：在公用网络中的数据存储和专用网络（本地或虚拟专用网络）中的数据存储之间移动数据。 它提供对内置连接器、格式转换、列映射以及性能和可扩展数据传输的支持。
- **调度活动**：调度和监视在各种计算服务（如 Azure HDInsight、Azure 机器学习、Azure SQL 数据库、SQL Server 等等）上运行的转换活动。
- **执行 SSIS 包**：在托管的 Azure 计算环境中本机执行 SQL Server 集成服务 (SSIS) 包。

可以按需部署一个或多个集成运行时实例来移动和转换数据。  集成运行时可以在 Azure 公用网络或专用网络（本地、Azure 虚拟网络或 Amazon Web Services 虚拟专用云 (VPC)）中运行。 

有关详细信息，请参阅 [Azure 数据工厂中的集成运行时](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>对集成运行时的数目有何限制？
对于数据工厂中可以有多少个集成运行时实例，没有硬性限制。 不过，对于集成运行时在每个订阅中可以用于执行 SSIS 包的虚拟机 (VM) 核心数具有限制。 有关详细信息，请参阅[数据工厂限制](../azure-subscription-service-limits.md#data-factory-limits)。

## <a name="when-to-use-version-2-rather-than-version-1"></a>何时使用版本 2 而非版本 1？ 
对于 Azure 数据工厂的新用户而言，可以直接从版本 2 开始。 如果已在使用版本 1，可以在版本 2 上重新生成数据工厂。

> [!WARNING]
> 数据工厂版本 2 目前为预览版，尚未正式发布 (GA)。 因此，它目前不提供与正式版的数据工厂版本 1 相同的 Azure 服务级别协议 (SLA) 承诺。 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>版本 2 的顶层概念有哪些？
一个 Azure 订阅可能有多个数据工厂，或者有一个或多个 Azure 数据工厂实例。 Azure 数据工厂由四个关键组件组成，这些组件组合起来提供一个平台，供你在上面编写数据驱动型工作流（其中包含用来移动和转换数据的步骤）。

### <a name="pipeline"></a>管道
数据工厂可以包含一个或多个管道。 管道是执行任务单元的活动的逻辑分组。 管道中的活动可以共同执行一项任务。 例如，一个管道可能包含一组活动，这些活动从 Azure Blob 引入数据，并在 HDInsight 群集上运行 Hive 查询，以便对数据分区。 这样做的好处是，可以通过管道以集而非单独的形式管理活动。 管道中的活动可以链接在一起来按顺序执行，也可以独立并行执行

### <a name="activity"></a>活动
活动表示管道中的处理步骤。 例如，可以使用“复制”活动将数据从一个数据存储复制到另一个数据存储。 同样，可能使用在 Azure HDInsight 群集上运行 Hive 查询的“Hive”活动来转换或分析数据。 数据工厂支持三种类型的活动：数据移动活动、数据转换活动和控制活动

### <a name="datasets"></a>数据集
数据集代表数据存储中的数据结构，这些结构直接指向需要在活动中使用的数据，或者将其作为输入或输出引用。 

### <a name="linked-services"></a>链接服务
链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：链接服务定义到数据源的连接，而数据集则代表数据的结构。 例如，Azure 存储链接服务指定连接到 Azure 存储帐户所需的连接字符串。 Azure Blob 数据集指定 Blob 容器以及包含数据的文件夹。

数据工厂中的链接服务有两个用途：

- 代表**数据存储**，包括但不限于本地 SQL Server、Oracle 数据库、文件共享或 Azure Blob 存储帐户。 有关支持的数据存储的列表，请参阅[复制活动](copy-activity-overview.md)一文。
- 代表可托管活动执行的**计算资源**。 例如，HDInsightHive 活动在 HDInsight Hadoop 群集上运行。 有关转换活动列表和支持的计算环境，请参阅[转换数据](transform-data.md)一文。

### <a name="triggers"></a>触发器
触发器代表处理单元，用于确定何时需要启动管道执行。 针对不同的事件类型提供了不同的触发器类型；对于预览版，我们支持时钟计划程序触发器。 


### <a name="pipeline-runs"></a>管道运行
管道运行是管道执行实例。 管道运行通常是通过将自变量传递给管道中定义的参数来实例化的。 自变量可手动传递，也可在触发器定义中传递

### <a name="parameters"></a>参数
参数是只读配置的键值对。  参数是在管道中定义的，所定义的参数的自变量是在执行期间通过由触发器创建的运行上下文传递的或通过手动执行的管道传递的。 管道中的活动使用参数值。
数据集是强类型参数和可重用/可引用的实体。 活动可以引用数据集并且可以使用数据集定义中所定义的属性

链接服务也是强类型参数，其中包括数据存储或计算环境的连接信息。 它也是可重用/可引用的实体。

### <a name="control-flow"></a>控制流
管道活动的业务流程，包括将活动按顺序链接起来、设置分支。可以在管道级别定义参数，在按需或者通过触发器调用管道时传递自变量。 还包括自定义状态传递和循环容器，即 For-each 迭代器。


有关数据工厂概念的详细信息，请参阅以下文章：

- [数据集和链接服务](concepts-datasets-linked-services.md)
- [管道和活动](concepts-pipelines-activities.md)
- [集成运行时](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>数据工厂的定价模型是怎样的？
有关 Azure 数据工厂的定价详细信息，请参阅[数据工厂定价详细信息](https://azure.microsoft.com/pricing/details/data-factory/)页。

## <a name="what-regions-support-azure-data-factory-version-2"></a>哪些区域支持 Azure 数据工厂版本 2？
当前，可以在美国东部和美国东部 2 区域中创建版本 2 的数据工厂。 不过，在其他区域中，数据工厂可以使用集成运行时在数据存储之间移动数据、调度针对计算服务的活动，或者调度 SSIS 包。  有关详细信息，请参阅[数据工厂位置](concepts-integration-runtime.md#integration-runtime-location)。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何及时了解有关数据工厂的信息？
可以使用以下站点来及时了解有关 Azure 数据工厂的信息：

- [博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文档主页](/azure/data-factory)
- [产品主页](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>技术深入了解 

### <a name="can-i-have-version-1-and-version-2-pipelines-run-side-by-side"></a>是否可以使版本 1 和版本 2 管道并存运行？
不能。 版本 2 或版本 1 数据工厂不能具有其他版本的实体（链接服务、数据集、管道，等等）。   

### <a name="do-i-still-need-to-define-datasets-in-version-2"></a>在版本 2 中是否仍然需要定义数据集？
对于大多数活动而言，数据集不再是必需的实体。 对于使用数据集中的架构和其他元数据信息进行转换的复制、机器学习、查找、验证和自定义活动，数据集还是必需的。 其余活动不再需要数据集。

### <a name="can-i-chain-two-activities-without-a-dataset-in-version-2"></a>在版本 2 中，在没有数据集的情况下，是否可以将两个活动链接在一起？
可以。 在版本 2 中，不需要使用数据集便可将活动链接在一起。 可以在管道的 JSON 定义中使用 **dependsOn** 属性来链接活动。 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>版本 2 是否支持版本 1 中的所有活动？ 
是的，它支持版本 1 中的所有活动。

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>如何调度版本 2 管道？ 
可以使用计划程序触发器来调度版本 2 管道。 计划程序触发器使用时钟日历调度，允许用户定期或使用基于日历的重复模式（例如，在每周的周一下午 6 点和周四晚上 9 点）。 有关详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>在版本 2 中，是否可以向管道传递参数？
可以，在版本 2 中，参数是头等的顶层概念。 可以在管道级别定义参数，并且在按需或使用触发器执行管道运行时传递自变量。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>是否可以为管道参数定义默认值？ 
可以。 可以为管道中的参数定义默认值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-passed-to-a-pipeline-run"></a>管道中的活动是否可以使用传递给管道运行的自变量？ 
可以。 管道中的每个活动都可以使用通过 `@parameter` 构造传递给管道运行的参数值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>活动输出属性是否可以在其他活动中使用？ 
可以。 在后续活动中可以通过 @activity 构造来使用活动输出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何得体地处理活动输出中的 NULL 值？ 
可以在表达式中使用 `@coalesce` 构造来得体地处理 NULL 值。 

### <a name="can-i-use-retries-timeouts-at-the-activity-level-in-version-2"></a>在版本 2 中，是否可以在活动级别使用重试和超时？
可以。 与在版本 1 中一样，在版本 2 中，可以在活动级别配置重试和超时来控制活动的执行。 

## <a name="next-steps"></a>后续步骤
有关创建版本 2 的数据工厂的分步说明，请参阅以下教程：

- [快速入门：创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [教程：在云中复制数据](tutorial-copy-data-dot-net.md)


