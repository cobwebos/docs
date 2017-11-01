---
title: "Azure 数据工厂：常见问题解答 | Microsoft Docs"
description: "获取有关 Azure 数据工厂的常见问题的解答。"
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
ms.openlocfilehash: b394b78b1d1a45a725c438d2cb978bb367c24894
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="azure-data-factory-faq"></a>Azure 数据工厂常见问题解答
本文适用于 Azure 数据工厂服务版本 2。 其中提供了有关数据工厂的常见问题的解答。  

## <a name="what-is-azure-data-factory"></a>什么是 Azure 数据工厂？ 
数据工厂是一项完全托管的基于云的数据集成服务，可以自动移动和转换数据。 如同工厂运转设备将原材料转换为成品一样，Azure 数据工厂可协调现有的服务，收集原始数据并将其转换为随时可用的信息。 

使用 Azure 数据工厂可以创建数据驱动的工作流，用于在本地与云数据存储之间移动数据。 可以使用 Azure HDInsight、Azure Data Lake Analytics 和 SQL Server Integration Services (SSIS) 集成运行时等计算服务来处理和转换数据。 

使用数据工厂，可在基于 Azure 的云服务或自己的自承载计算环境（例如 SSIS、SQL Server 或 Oracle）中执行数据处理。 创建用于执行所需操作的管道后，可将它计划为定期运行（例如每小时、每天或每周），或者在发生某个事件时触发该管道。 有关详细信息，请参阅 [Azure 数据工厂简介](introduction.md)。

## <a name="whats-different-in-version-2"></a>版本 2 有何独特之处？
Azure 数据工厂版本 2 基于原始 Azure 数据工厂数据移动和转换服务构建，扩展到更为广泛的一组云优先的数据集成方案。 Azure 数据工厂版本 2 提供以下功能：

- 控制流和缩放
- 在 Azure 中部署和运行 SSIS 包

随着版本 1 的发布，我们认识到，客户需要设计复杂的混合数据集成方案，该方案需要在云、本地和云虚拟机 (VM) 中进行数据移动和处理。 这些要求需要在受保护的虚拟网络环境中转换和处理数据，以及通过按需处理功能进行横向扩展的能力。

由于数据管道日渐成为业务分析策略的较为关键组成部分，我们发现，这些活动需要灵活的计划才能支持不断增大的数据负载和事件触发的执行。 随着复杂性的不断提高，支持常见工作流模式（包括分支、循环和条件处理）的服务的要求也越来越高。

使用版本 2 时，还可将现有的 SSIS 包迁移到云中。 此操作会将 SSIS 作为数据工厂中管理的、利用新的集成运行时功能的 Azure 服务即时转移。 通过运转版本 2 中的 SSIS 集成运行时，可在云中执行、管理、监视和生成 SSIS 包。

### <a name="control-flows-and-scale"></a>控制流和缩放 
为了支持现代数据仓库中的各种集成流和模式，数据工厂启用了新的灵活的数据管道模型，该模型不再与时序数据绑定。 在此版本中，可以在数据管道的控制流中对条件语句进行建模，设置分支，以及在这些流中（或跨这些流）显式传递参数。

现在可以自由地对数据集成所需的任何流样式进行建模，并可以按需调度或按计划重复调度。 现在已启用的几个常见流（以前无法启用）包括：   

- 控制流：
    - 在管道中将活动按顺序链接起来。
    - 在管道中对活动进行分支。
    - parameters
        - 在管道级别定义参数，在按需或通过触发器调用管道时传递自变量。
        - 活动可以使用传递给管道的自变量。
    - 自定义状态传递
        - 包含状态的活动输出可供管道中的后续活动使用。
    - 循环容器
        - For-each 
- 基于触发器的流：
    - 可以按需或按时钟时间触发管道。
- 增量流：
    - 使用参数并定义用于增量复制的高水位标记，同时移动本地或云中的关系存储中的维度或引用表，以将数据载入 Lake。 

有关详细信息，请参阅[教程：控制流](tutorial-control-flow.md)。

### <a name="deploy-ssis-packages-to-azure"></a>将 SSIS 包部署到 Azure 
如果想要移动 SSIS 工作负荷，可以创建一个版本 2 数据工厂，并预配 Azure-SSIS 集成运行时。 Azure-SSIS 集成运行时是由 Azure VM（节点）构成的完全托管群集，专用于在云中运行 SSIS 包。 有关分步说明，请参阅[将 SSIS 包部署到 Azure](tutorial-deploy-ssis-packages-azure.md) 教程。 
 

### <a name="sdks"></a>SDK
对于正在寻求编程接口的高级用户，版本 2 提供了一组丰富的 SDK，让他们使用偏好的 IDE 创作、管理和监视管道。

- **.NET SDK**：.NET SDK 在版本 2 中有所更新。 
- **PowerShell**：PowerShell cmdlet 在版本 2 中有所更新。 在版本 2 中，cmdlet 的名称中包含 *DataFactoryV2*。 例如：*Get-AzureRmDataFactoryV2*。 
- **Python SDK**：此 SDK 是版本 2 中新增的。
- **REST API**：REST API 在版本 2 中有所更新。  

在版本 2 中进行了更新的 SDK 不能向后兼容版本 1 客户端。 

### <a name="monitoring"></a>监视
当前，版本 2 仅支持使用 SDK 监视数据工厂。 门户尚不支持对版本 2 的数据工厂进行监视。 

## <a name="what-is-integration-runtime"></a>什么是集成运行时？
集成运行时是 Azure 数据工厂用于在各种网络环境之间提供以下数据集成功能的计算基础结构：

- **数据移动**：在公用网络中的数据存储和专用网络（本地或虚拟专用网络）中的数据存储之间移动数据。 它提供对内置连接器、格式转换、列映射以及性能和可扩展数据传输的支持。
- **调度活动**：调度和监视在各种计算服务（如 Azure HDInsight、Azure 机器学习、Azure SQL 数据库、SQL Server 等等）上运行的转换活动。
- **执行 SSIS 包**：在托管的 Azure 计算环境中本机执行 SSIS 包。

可以按需部署一个或多个集成运行时实例来移动和转换数据。 集成运行时可以在 Azure 公用网络或专用网络（本地、Azure 虚拟网络或 Amazon Web Services 虚拟私有云 [VPC]）中运行。 

有关详细信息，请参阅 [Azure 数据工厂中的集成运行时](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>对集成运行时的数目有何限制？
对于可在数据工厂中使用多少个集成运行时实例，没有硬性限制。 不过，对于集成运行时在每个订阅中可用于执行 SSIS 包的 VM 核心数有限制。 有关详细信息，请参阅[数据工厂限制](../azure-subscription-service-limits.md#data-factory-limits)。

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>何时应使用版本 2 而不使用版本 1？ 
对于 Azure 数据工厂的新用户而言，可以直接从版本 2 开始。 如果已在使用版本 1，可以在版本 2 上重新生成数据工厂。

> [!WARNING]
> 数据工厂版本 2 目前为预览版，尚未正式发布 (GA)。 因此，它目前不提供与正式版的数据工厂版本 1 相同的 Azure 服务级别协议 (SLA) 承诺。 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>版本 2 的顶层概念有哪些？
一个 Azure 订阅可以包含一个或多个 Azure 数据工厂实例（或数据工厂）。 Azure 数据工厂包含四个关键组件，这些组件组合成为一个平台，可在其中编写数据驱动型工作流，以及用来移动和转换数据的步骤。

### <a name="pipelines"></a>管道
数据工厂可以包含一个或多个数据管道。 管道是执行任务单元的活动的逻辑分组。 管道中的活动可以共同执行一项任务。 例如，一个管道可以包含一组活动，这些活动从 Azure Blob 引入数据，并在 HDInsight 群集上运行 Hive 查询，以便对数据分区。 优点在于，可以使用管道以集的形式管理活动，而无需单独管理每个活动。 管道中的活动可以链接在一起来按顺序运行，也可以独立并行运行。

### <a name="activity"></a>活动
活动表示管道中的处理步骤。 例如，可以使用“复制”活动将数据从一个数据存储复制到另一个数据存储。 同样，可以使用在 Azure HDInsight 群集上运行 Hive 查询的 Hive 活动来转换或分析数据。 数据工厂支持三种类型的活动：数据移动活动、数据转换活动和控制活动。

### <a name="data-sets"></a>数据集
数据集代表数据存储中的数据结构，这些结构直接指向需要在活动中使用的数据，或者将其作为输入或输出引用。 

### <a name="linked-services"></a>链接服务
链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：链接服务定义到数据源的连接，而数据集则代表数据的结构。 例如，Azure 存储链接服务指定连接到 Azure 存储帐户所需的连接字符串。 Azure Blob 数据集指定 Blob 容器以及包含数据的文件夹。

数据工厂中的链接服务有两个用途：

- 代表数据存储，包括但不限于本地 SQL Server 实例、Oracle 数据库实例、文件共享或 Azure Blob 存储帐户。 有关支持的数据存储列表，请参阅 [Azure 数据工厂中的复制活动](copy-activity-overview.md)。
- 代表可托管活动执行的*计算资源*。 例如，HDInsight Hive 活动在 HDInsight Hadoop 群集上运行。 有关转换活动列表和支持的计算环境，请参阅[在 Azure 数据工厂中转换数据](transform-data.md)。

### <a name="triggers"></a>触发器
触发器表示处理单元，确定何时启动管道执行。 不同类型的事件有不同类型的触发器类型。 预览版支持时钟计划程序触发器。 


### <a name="pipeline-runs"></a>管道运行
管道运行是管道执行实例。 我们通常通过将自变量传递给管道中定义的参数来实例化管道运行。 可以手动传递自变量，也可以在触发器定义中传递。

### <a name="parameters"></a>parameters
参数是只读配置中的键值对。 在管道中定义参数，并在执行期间通过运行上下文传递所定义参数的自变量。 运行上下文由触发器创建，或通过手动执行的管道创建。 管道中的活动使用参数值。

数据集是可以重用或引用的强类型参数和实体。 活动可以引用数据集，并可以使用数据集定义中所定义的属性。

链接服务也是强类型参数，其中包含数据存储或计算环境的连接信息。 它也是可重用或引用的实体。

### <a name="control-flows"></a>控制流
控制流协调管道活动。管道活动包含序列中的链接活动、分支、在管道级别定义的参数，以及按需或通过触发器调用管道时传递的自变量。 控制流还包含自定义状态传递和循环容器（即 for-each 迭代器）。


有关数据工厂概念的详细信息，请参阅以下文章：

- [数据集和链接服务](concepts-datasets-linked-services.md)
- [管道和活动](concepts-pipelines-activities.md)
- [集成运行时](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>数据工厂的定价模型是怎样的？
有关 Azure 数据工厂的定价详细信息，请参阅[数据工厂定价详细信息](https://azure.microsoft.com/pricing/details/data-factory/)。

## <a name="what-regions-support-azure-data-factory-version-2"></a>哪些区域支持 Azure 数据工厂版本 2？
目前，可在美国东部和美国东部 2 区域中创建版本 2 数据工厂。 不过，在其他区域中，数据工厂可以使用集成运行时在数据存储之间移动数据、调度针对计算服务的活动，或者调度 SSIS 包。 有关详细信息，请参阅[数据工厂位置](concepts-integration-runtime.md#integration-runtime-location)。

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何及时了解有关数据工厂的信息？
有关 Azure 数据工厂的最新信息，请访问以下站点：

- [博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文档主页](/azure/data-factory)
- [产品主页](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>技术深入了解 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>是否可以同时运行版本 1 和版本 2 管道？
不能。 版本 2 和版本 1 数据工厂不能包含另一版本的实体（例如链接服务、数据集或管道）。   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>在版本 2 中是否仍需定义数据集？
对于大多数活动而言，数据集不再是必需的实体。 对于使用数据集中的架构和其他元数据信息进行转换的复制、机器学习、查找、验证和自定义活动，数据集还是必需的。 其余活动不再需要数据集。

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>在版本 2 中，在没有数据集的情况下，是否可将两个活动链接在一起？
是的。 在版本 2 中，不需要使用数据集即可将活动链接在一起。 可以在管道的 JSON 定义中使用 **dependsOn** 属性来链接活动。 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>版本 2 是否支持版本 1 中的所有活动？ 
是的，版本 2 支持版本 1 中的所有活动。

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>如何调度版本 2 管道？ 
可以使用计划程序触发器来调度版本 2 管道。 该触发器使用时钟日历计划，你可以使用该计划定期或通过基于日历的重复模式（例如，在每周的周一下午 6 点和周四晚上 9 点）来安排管道。 有关详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>在版本 2 中，是否可以向管道传递参数？
可以，在版本 2 中，参数是最重要的顶层概念。 可以在管道级别定义参数，并在按需或使用触发器执行管道运行时传递自变量。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>是否可以为管道参数定义默认值？ 
是的。 可以为管道中的参数定义默认值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管道中的活动是否可以使用传递给管道运行的自变量？ 
是的。 管道中的每个活动都可以使用通过 `@parameter` 构造传递给管道运行的参数值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>活动输出属性是否可以在其他活动中使用？ 
是的。 在后续活动中可以通过 `@activity` 构造来使用活动输出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何得体地处理活动输出中的 NULL 值？ 
可以在表达式中使用 `@coalesce` 构造来得体地处理 NULL 值。 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>在版本 2 中，是否可以在活动级别使用重试和超时？
是的。 与在版本 1 中一样，在版本 2 中，可以在活动级别配置重试和超时来控制活动的执行。 

## <a name="next-steps"></a>后续步骤
有关创建版本 2 的数据工厂的分步说明，请参阅以下教程：

- [快速入门：创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [教程：在云中复制数据](tutorial-copy-data-dot-net.md)

