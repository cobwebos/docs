---
title: 将自己的 ML 引入 Azure Sentinel |Microsoft Docs
description: 本文介绍如何创建和使用自己的机器学习算法，以便在 Azure Sentinel 中进行数据分析。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 17c0ba7306ab4cc51fe8bbe3709d5b6bc85fa487
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342688"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>自带机器学习 (ML) 到 Azure Sentinel

机器学习 (ML) 是 Azure Sentinel 的主要 connect-through 之一，它是设置它的主要属性之一。 Azure Sentinel 在几个体验中提供 ML：内置于 [合成](fusion.md) 相关引擎和 Jupyter 笔记本，并 (BYO ml) 平台。 

ML 检测模型可适应单个环境和用户行为变化，从而减少误报并识别使用传统方法无法找到的威胁。 许多安全组织都理解了 ML 的安全性价值，尽管不是很多，都有很多专业人员在安全和 ML 方面都具有专业知识。 我们在此处为安全组织和专业人员设计了一个框架，以便在其 ML 旅程中与我们一起增长。 对于 ML 或没有必要的专业技能的组织而言，可以从 Azure Sentinel 的内置 ML 功能中获得重大保护价值。

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="机器学习框架":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>什么是自带机器学习 (BYO) 平台？

对于具有 ML 资源并想要为其独特的业务需求构建自定义 ML 模型的组织，我们提供了 **BYO ml 平台**。 该平台利用[Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/)环境和 Jupyter 笔记本来生成 ML 环境。 它提供以下组件：

- 一个 BYO ML 包，其中包含的库可帮助你访问数据并将结果推送到 Log Analytics (LA) ，因此你可以将结果与检测、调查和搜索集成。 

- ML 算法模板，你可以自定义这些模板，以适应组织中的特定安全问题。 

- 用于训练模型和计划模型评分的示例笔记本。 

除此之外，你还可以将自己的 ML 模型和/或自己的 Spark 环境与 Azure Sentinel 集成。

使用 BYO ML 平台，可以快速开始构建自己的机器学习模型： 

- 带有示例数据的笔记本可帮助你获得端到端的动手体验，而无需担心如何处理生产数据。

- 与 Spark 环境集成的包减少了管理基础结构的难题，并摩擦。

- 库支持数据移动。 笔记本培训和评分演示了端到端体验，并可用作模板以适应环境。

### <a name="use-cases"></a>用例
 
BYO ML 平台和包可显著减少构建你自己的 ML 检测所需的时间和精力，并利用这些功能来解决 Azure Sentinel 中的特定安全问题。 平台支持以下用例：

**训练 ML 算法以获取自定义模型：** 你可以 (Microsoft 或用户社区共享的现有 ML 算法) 并轻松地对其进行培训，以获取更适合你的数据和环境的自定义 ML 模型。

**修改 ML 算法模板以获取自定义模型：** 你可以修改由 Microsoft 或用户社区) 共享的 ML 算法模板 (，并对你自己的数据定型修改后的算法，以根据你的特定问题来派生自定义模型。

**创建自己的模型：** 使用 Azure Sentinel 的 BYO 平台和实用工具从头开始创建自己的模型。

**集成 Databricks/Spark 环境：** 将现有的 Databricks/Spark 环境集成到 Azure Sentinel，并使用 BYO ML 库和模板为其独特的情况构建 ML 模型。

**导入自己的 ML 模型：** 可以导入自己的 ML 模型，并使用 BYO ML 平台和实用工具将它们与 Azure Sentinel 集成。

**共享 ML 算法：** 共享用于社区的 ML 算法以采用和改编。

**使用 ML 来实现 SecOps：** 使用自己的自定义 ML 模型和结果进行搜索、检测、调查和响应。

本文介绍了 BYO ML 平台的组件，以及如何利用平台和异常资源访问算法，通过 Azure Sentinel 提供自定义 ML 检测。

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark 环境

[Apache Spark™](http://spark.apache.org/) 通过提供用于构建数据管道的统一框架，进一步简化了大数据。 Azure Databricks 提供了一个围绕 Spark 构建的零管理云平台，从而进一步了解这一点。 建议你将 Databricks 用于 BYO ML 平台，这样你就可以专注于查找直接影响你的业务的答案，而不是处理数据管道和平台问题。
如果你已有 Databricks 或任何其他 Spark 环境，并希望使用现有的设置，则 BYO ML 包也会正常运行。 

## <a name="byo-ml-package"></a>BYO ML 包

BYO ML 包包含 Microsoft 在 ML 的前端的最佳实践和研究，以实现安全性。 在此包中，我们提供了以下实用程序、笔记本和算法模板的列表，以确保安全问题。

| 文件名 | 说明 |
| --------- | ----------- |
| azure_sentinel_utilities. whl | 包含用于从 Azure 读取 blob 并写入 Log Analytics 的实用程序。 |
| AnomalousRASampleData | 笔记本演示如何在 Sentinel 中使用异常资源访问模型以及生成的定型和测试示例数据。 |
| AnomalousRATraining.ipynb | 用于训练算法、生成并保存模型的笔记本。 |
| AnomalousRAScoring.ipynb | 用于计划要运行的模型的笔记本，直观显示结果，并将评分写回到 Azure Sentinel。 |
|

我们提供的第一个 ML 算法模板用于 [异常资源访问检测](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)。 它基于协作筛选算法，并使用 Windows 文件共享访问日志定型 (事件 ID 为 5140) 的安全事件。 日志中此模型所需的关键信息是访问的用户和资源配对。 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>示例演练：异常文件共享访问检测 

现在，你已经熟悉了 BYO ML 平台的关键组件，下面提供了一个示例，演示如何使用平台和组件来提供自定义 ML 检测。

### <a name="setup-the-databricksspark-environment"></a>设置 Databricks/Spark 环境

如果还没有 Databricks 环境，需要设置自己的环境。 有关说明，请参阅 [Databricks 快速入门](https://docs.microsoft.com/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) 文档。

### <a name="auto-export-instruction"></a>自动导出指令

若要基于 Sentinel 中自己的数据构建自定义 ML 模型，需要将数据从 Log Analytics 导出到 Blob 存储或事件中心资源，以便 ML 模型可以从 Databricks 访问该模型。 了解如何将 [数据引入 Azure Sentinel](connect-data-sources.md)。

在此示例中，需要在 Azure blob 存储中为文件共享访问日志提供训练数据。 笔记本和库中记录了数据的格式。

可以使用 [Azure 命令行接口 (CLI) ](https://docs.microsoft.com/cli/azure/monitor/log-analytics)，从 Log Analytics 中自动导出数据。 

若要运行这些命令，必须在 Log Analytics 工作区、存储帐户和 EventHub 资源中分配 " **参与者** " 角色。 

下面是用于设置自动导出的一组示例命令：

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>导出自定义数据

对于 Log Analytics 自动导出不支持的自定义数据，可以使用逻辑应用或其他解决方案来移动数据。 可以参考将 [Log Analytics 数据导出到 Blob 存储](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) 博客和脚本。

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>与 Azure Sentinel 之外的数据关联

你还可以将 Azure Sentinel 之外的数据引入 blob 存储或事件中心，并将其与 Sentinel 数据相关联，以生成 ML 模型。 
 
### <a name="copy-and-install-the-related-packages"></a>复制并安装相关程序包

将上面提到的 Azure Sentinel GitHub 存储库中的 BYO ML 包复制到 Databricks 环境。 然后打开笔记本并按照笔记本中的说明在群集上安装所需的库。

### <a name="model-training-and-scoring"></a>模型定型和评分

按照两个笔记本中的说明操作，根据自己的环境和资源更改配置，按照以下步骤训练和生成模型，然后将模型计划为传入文件共享访问日志评分。

### <a name="write-results-to-log-analytics"></a>将结果写入 Log Analytics

一旦你按计划获得评分，你就可以使用评分笔记本中的模块将评分结果写入与你的 Azure Sentinel 实例相关联的 Log Analytics 工作区。

### <a name="check-results-in-azure-sentinel"></a>在 Azure Sentinel 中检查结果

若要查看评分结果以及相关日志详细信息，请返回到 Azure Sentinel 门户。 在 **日志** > 自定义日志中，你将看到 **AnomalousResourceAccessResult_CL** 表中的结果 (或你自己的自定义表名称) 。 你可以使用这些结果来增强调查和搜索体验。

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="机器学习框架":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>用 ML 结果构建自定义分析规则

一旦您确认了 ML 结果位于自定义日志表中，并且您对该分数的保真度感到满意，就可以根据结果创建检测。 从 Azure Sentinel 门户中转到 " **分析** "，并 [创建新的检测规则](tutorial-detect-threats-custom.md)。 下面是一个示例，演示了用于创建检测的查询。

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="机器学习框架":::

### <a name="view-and-respond-to-incidents"></a>查看和响应事件
基于 ML 结果设置分析规则后，如果结果超出了查询中设置的阈值，则会生成事件并将其显示在 Azure Sentinel 上的 " **事件** " 页上。 

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Sentinel 的 BYO ML 平台来创建或导入自己的机器学习算法，以便分析数据和检测威胁。

- 请参阅 [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)中有关机器学习和众多其他相关主题的文章。
