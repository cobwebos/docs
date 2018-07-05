---
title: Azure 数据工厂：常见问题解答 | Microsoft Docs
description: 获取有关 Azure 数据工厂的常见问题的解答。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: shlo
ms.openlocfilehash: ebe8745db06113d0508d86554bf031a4235c8e44
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045943"
---
# <a name="azure-data-factory-faq"></a>Azure 数据工厂常见问题解答
本文提供有关 Azure 数据工厂的常见问题解答。  

## <a name="what-is-azure-data-factory"></a>什么是 Azure 数据工厂？ 
数据工厂是一项完全托管的基于云的数据集成服务，可以自动移动和转换数据。 如同工厂运转设备将原材料转换为成品一样，Azure 数据工厂可协调现有的服务，收集原始数据并将其转换为随时可用的信息。 

使用 Azure 数据工厂可以创建数据驱动的工作流，用于在本地与云数据存储之间移动数据。 可以使用 Azure HDInsight、Azure Data Lake Analytics 和 SQL Server Integration Services (SSIS) 集成运行时等计算服务来处理和转换数据。 

使用数据工厂，可在基于 Azure 的云服务或自己的自承载计算环境（例如 SSIS、SQL Server 或 Oracle）中执行数据处理。 创建用于执行所需操作的管道后，可将它计划为定期运行（例如每小时、每天或每周）、按时间范围运行或者在发生某个事件时触发。 有关详细信息，请参阅 [Azure 数据工厂简介](introduction.md)。

### <a name="control-flows-and-scale"></a>控制流和缩放 
为了支持现代数据仓库中不同的集成流和模式，数据工厂启用了灵活的数据管道建模，其中包含完全控制流编程范例，包括条件执行、数据管道中的分支操作，以及在这些流中和流之间显式传递参数。 控制流还包含通过活动调度将数据转换到外部执行引擎以及数据流功能，包括通过复制活动大规模移动数据。

使用数据工厂可自由地对数据集成所需的任何流样式进行建模，可按需调度或按计划重复调度这些样式。 此模型支持的几个常见流有：   

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

### <a name="transform-your-data-at-scale-with-code-free-pipelines"></a>使用无代码管道大规模地转换数据
使用基于浏览器的新工具，可进行无代码管道创作和部署，获得现代化的、基于 Web 的交互式体验。

对于可视数据开发人员和数据工程师，ADF Web UI 是可用于生成管道的无代码设计环境。 它与 Visual Studio Online Git 完全集成，并提供 CI/CD 集成以及含有调试选项的迭代开发。

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>为高级用户提供丰富的跨平台 SDK
对于正在寻求程序设计界面的高级用户，ADF V2 提供了一组丰富的 SDK，可使用偏好的 IDE 来创作、管理和监视管道
1.  Python SDK
2.  Powershell CLI
3.  C# SDK 用户还能利用已记录的 REST API 来与 ADF V2 交互

### <a name="iterative-development-and-debugging-using-visual-tools"></a>使用可视化工具进行迭代开发和调试
使用 Azure 数据工厂 (ADF) 可视化工具可进行迭代开发和调试。 可使用管道画布中的“调试”功能创建管道并测试运行情况，无需编写任何代码。 可在管道画布的“输出”窗口中查看测试运行的结果。 在测试运行成功后，可向管道中添加更多活动并继续以迭代方式进行调试。 还可以“取消”正在进行的测试运行。 在单击“调试”之前，不需要将所做的更改发布至数据工厂服务。 在开发、测试或生产环境中更新数据工厂工作流之前，如果想确保新添加的内容或更改能按预期工作，这一点就很有帮助。 

### <a name="deploy-ssis-packages-to-azure"></a>将 SSIS 包部署到 Azure 
如果想要移动 SSIS 工作负荷，可以创建一个数据工厂，并预配 Azure-SSIS 集成运行时。 Azure-SSIS 集成运行时是由 Azure VM（节点）构成的完全托管群集，专用于在云中运行 SSIS 包。 有关分步说明，请参阅[将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md) 教程。 
 
### <a name="sdks"></a>SDK
对于正在寻求程序设计界面的高级用户，ADF 提供了一组丰富的 SDK，用户可使用偏好的 IDE 创作、管理和监视管道。 语言支持包括 .NET、PowerShell、Python 以及 REST。

### <a name="monitoring"></a>监视
可以通过 PowerShell、SDK 或浏览器用户界面中的可视化监视工具监视数据工厂。 可高效监视并管理基于需求、基于触发器的或由时钟驱动的自定义流。 在一个面板取消现有任务、速览故障信息、向下钻取以获取详细的错误消息并调试问题，无需进行上下文切换或来回转换界面。 

### <a name="new-features-for-ssis-in-adf"></a>ADF 中的 SSIS 新功能
从 2017 年首次发布公共预览版以来，数据工厂添加了以下 SSIS 功能：

-   增加了对三种 Azure SQL 数据库 (DB) 配置/变体的支持，可托管项目/包 (SSISDB) 的 SSIS 目录：
-   具备 VNET 服务终结点的 Azure SQL DB
-   托管实例 (MI)
-   弹性池
-   除将要弃用的经典 VNet 外，还支持 Azure 资源管理器虚拟网络 (VNet)。这样用户便可将 Azure-SSIS 集成运行时 (IR) 注入/联接到针对 Azure SQL 数据库配置了 VNet 服务终结点/MI/本地数据访问权限的 VNet，请参阅：https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network 
-   在 SQL 身份验证的基础上，增加了 Azure Active Directory (AAD) 身份验证支持，以便连接到 SSISDB。这样用户便可将 AAD 身份验证于 ADF 托管服务标识 (MSI) 结合使用
-   支持自带本地 SQL Server 许可证，大量节约 Azure 混合权益 (AHB) 选项的成本
-   支持 Azure-SSIS IR 企业版，从而能使用先进/高级功能、自定义附加组件/扩展的安装设置以及使用第三方生态系统，请参阅：https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/ 
-   在 ADF 中更深入地与 SSIS 进行了集成，让用户能在 ADF 管道中调用/触发一流的执行 SSIS 包活动并通过 SSMS 对它们进行计划，请参阅：https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/ 


## <a name="what-is-integration-runtime"></a>什么是集成运行时？
集成运行时是 Azure 数据工厂用于在各种网络环境之间提供以下数据集成功能的计算基础结构：

- **数据移动**：就数据移动而言，集成运行时在源和目标数据存储之间移动数据，同时为内置连接器、格式转换、列映射和高性能可伸缩数据传输提供支持。
- **调动活动**：就转换而言，集成运行时提供本机执行 SSIS 包的能力。
- **执行 SSIS 包**：在托管的 Azure 计算环境中本机执行 SSIS 包。 集成运行时还支持调度和监视各种计算服务（如 Azure HDInsight、Azure Machine Learning、Azure SQL Database、SQL Server 等等）上运行的转换活动。

可以按需部署一个或多个集成运行时实例来移动和转换数据。 集成运行时可以在 Azure 公用网络或专用网络（本地、Azure 虚拟网络或 Amazon Web Services 虚拟私有云 [VPC]）中运行。 

有关详细信息，请参阅 [Azure 数据工厂中的集成运行时](concepts-integration-runtime.md)。

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>对集成运行时的数目有何限制？
对于可在数据工厂中使用多少个集成运行时实例，没有硬性限制。 不过，对于集成运行时在每个订阅中可用于执行 SSIS 包的 VM 核心数有限制。 有关详细信息，请参阅[数据工厂限制](../azure-subscription-service-limits.md#data-factory-limits)。

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Azure 数据工厂的顶层概念有哪些？
一个 Azure 订阅可以包含一个或多个 Azure 数据工厂实例（或数据工厂）。 Azure 数据工厂包含四个关键组件，这些组件组合成为一个平台，可在其中编写数据驱动型工作流，以及用来移动和转换数据的步骤。

### <a name="pipelines"></a>管道
数据工厂可以包含一个或多个数据管道。 管道是执行任务单元的活动的逻辑分组。 管道中的活动可以共同执行一项任务。 例如，一个管道可以包含一组活动，这些活动从 Azure Blob 引入数据，并在 HDInsight 群集上运行 Hive 查询，以便对数据分区。 优点在于，可以使用管道以集的形式管理活动，而无需单独管理每个活动。 管道中的活动可以链接在一起来按顺序运行，也可以独立并行运行。

### <a name="activity"></a>活动
活动表示管道中的处理步骤。 例如，可以使用“复制”活动将数据从一个数据存储复制到另一个数据存储。 同样，可以使用在 Azure HDInsight 群集上运行 Hive 查询的 Hive 活动来转换或分析数据。 数据工厂支持三种类型的活动：数据移动活动、数据转换活动和控制活动。

### <a name="datasets"></a>数据集
数据集代表数据存储中的数据结构，这些结构直接指向需要在活动中使用的数据，或者将其作为输入或输出引用。 

### <a name="linked-services"></a>链接服务
链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：链接服务定义到数据源的连接，而数据集则代表数据的结构。 例如，Azure 存储链接服务指定连接到 Azure 存储帐户所需的连接字符串。 Azure Blob 数据集指定 Blob 容器以及包含数据的文件夹。

数据工厂中的链接服务有两个用途：

- 代表数据存储，包括但不限于本地 SQL Server 实例、Oracle 数据库实例、文件共享或 Azure Blob 存储帐户。 有关支持的数据存储列表，请参阅 [Azure 数据工厂中的复制活动](copy-activity-overview.md)。
- 代表可托管活动执行的*计算资源*。 例如，HDInsight Hive 活动在 HDInsight Hadoop 群集上运行。 有关转换活动列表和支持的计算环境，请参阅[在 Azure 数据工厂中转换数据](transform-data.md)。

### <a name="triggers"></a>触发器
触发器表示处理单元，确定何时启动管道执行。 不同类型的事件有不同类型的触发器类型。 

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

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>如何及时了解有关数据工厂的最新信息？
有关 Azure 数据工厂的最新信息，请访问以下站点：

- [博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [文档主页](/azure/data-factory)
- [产品主页](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>技术深入了解 

### <a name="how-can-i-schedule-a-pipeline"></a>如何计划管道？ 
可以采用计划程序触发器或时间范围触发器来计划管道。 该触发器使用时钟日历计划，你可以使用该计划定期或通过基于日历的重复模式（例如，在每周的周一下午 6 点和周四晚上 9 点）来安排管道。 有关详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md)。

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>是否可以向管道传递参数？
可以，在 ADF 中，参数是最重要的顶层概念。 可以在管道级别定义参数，并在按需或使用触发器执行管道运行时传递自变量。  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>是否可以为管道参数定义默认值？ 
是的。 可以为管道中的参数定义默认值。 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>管道中的活动是否可以使用传递给管道运行的自变量？ 
是的。 管道中的每个活动都可以使用通过 `@parameter` 构造传递给管道运行的参数值。 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>活动输出属性是否可以在其他活动中使用？ 
是的。 在后续活动中可以通过 `@activity` 构造来使用活动输出。
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>如何得体地处理活动输出中的 NULL 值？ 
可以在表达式中使用 `@coalesce` 构造来得体地处理 NULL 值。 

## <a name="next-steps"></a>后续步骤
有关创建数据工厂的分步说明，请参阅以下教程：

- [快速入门：创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [教程：在云中复制数据](tutorial-copy-data-dot-net.md)
