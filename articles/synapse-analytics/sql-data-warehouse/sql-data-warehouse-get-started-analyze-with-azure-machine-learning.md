---
title: 使用 Azure 机器学习分析数据
description: 使用 Azure 机器学习基于存储在 Azure Synapse 中的数据生成预测机器学习模型。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 9cf65b2fdeb7faa03b950593db86dd32a4ef91a7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495691"
---
# <a name="analyze-data-with-azure-machine-learning"></a>使用 Azure 机器学习分析数据

本教程使用[Azure 机器学习设计器](https://docs.microsoft.com/azure/machine-learning/concept-designer)来构建预测性机器学习模型。 该模型基于 Azure Synapse 中存储的数据。 本教程的方案是预测某个客户是否有可能购买自行车，而不是通过自行车店来构建目标市场营销活动。

## <a name="prerequisites"></a>先决条件

要逐步完成本教程，需要以下各项：

* 使用 AdventureWorksDW 示例数据预先加载的 SQL 池。 若要设置此 SQL 池，请参阅[创建 sql 池](create-data-warehouse-portal.md)并选择加载示例数据。 如果已有数据仓库但没有示例数据，可以[手动加载示例数据](load-data-from-azure-blob-storage-using-polybase.md)。
* Azure 机器学习工作区。 按照[本教程](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)创建新的教程。

## <a name="get-the-data"></a>获取数据

所使用的数据位于 AdventureWorksDW 的 vTargetMail 视图中。 若要在本教程中使用数据存储，请先将数据导出到 Azure Data Lake Storage 帐户，因为 Azure Synapse 当前不支持数据集。 Azure 数据工厂可用于将数据从数据仓库导出到使用[复制活动](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)Azure Data Lake Storage。 使用以下查询进行导入：

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Azure Data Lake Storage 中提供数据后，Azure 机器学习中的数据存储用于[连接到 Azure 存储服务](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)。 按照以下步骤创建数据存储和相应的数据集：

1. 从 Azure 门户或在[Azure 机器学习 studio](https://ml.azure.com/)中登录，启动 Azure 机器学习工作室。

1. 在 "**管理**" 部分的左窗格中单击 "**数据存储**"，然后单击 "**新建数据存储**"。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/datastores-tab.png" alt-text="Azure 机器学习界面的左窗格的屏幕截图":::

1. 提供数据存储的名称，选择类型为 "Azure Blob 存储"，提供位置和凭据。 然后单击“创建” 。

1. 接下来，在 "**资产**" 部分的左窗格中单击 "**数据集**"。 选择 "**创建数据集**"，其中包含**数据存储中**的选项。

1. 指定数据集的名称，并选择要进行**表格**的类型。 然后，单击 "**下一步**" 继续。

1. 在 "**选择或创建数据存储" 部分**中，选择**以前创建的数据存储**的选项。 选择之前创建的数据存储。 单击 "下一步" 并指定路径和文件设置。 如果文件包含列标题，请确保指定列标题。

1. 最后，单击 "**创建**" 创建数据集。

## <a name="configure-designer-experiment"></a>配置设计器试验

接下来，按照以下步骤进行设计器配置：

1. 在 "**作者**" 部分的左窗格中，单击 "**设计器**" 选项卡。

1. 选择**易于使用的预生成模块**以生成新的管道。

1. 在右侧的 "设置" 窗格中，指定管道的名称。

1. 同时，为之前设置的群集选择 "设置中的整个试验" 按钮的目标计算群集。 关闭“设置”窗格。

## <a name="import-the-data"></a>导入数据

1. 在 "搜索" 框下的左窗格中选择 "**数据集**" 子标签。

1. 将之前创建的数据集拖动到画布中。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/import-dataset.png" alt-text="画布上数据集模块的屏幕截图。":::

## <a name="clean-the-data"></a>清理数据

若要清除数据，请删除与模型无关的列。 请遵循以下步骤进行配置：

1. 在左窗格中选择 "**模块**" 子标签。

1. 将“数据转换 < 操作”下的“选择数据集中的列”模块拖放到画布中 。 将此模块连接到**数据集**模块。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-in.png" alt-text="画布上列选择模块的屏幕截图。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/select-columns-zoomed-out.png":::

1. 单击该模块以打开 "属性" 窗格。 单击 "编辑列"，指定要删除的列。

1. 排除两个列：CustomerAlternateKey 和 GeographyKey。 单击“保存”

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/drop-columns.png" alt-text="显示删除的列的屏幕截图。":::

## <a name="build-the-model"></a>构建模型

数据拆分为 80-20: 80%，以训练机器学习模型，20% 用于测试模型。 此二元分类问题中使用了 "双类" 算法。

1. 将 "**拆分数据**" 模块拖放到画布中。

1. 在 "属性" 窗格中，为**第一个输出数据集中的部分行**输入0.8。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/split-data.png" alt-text="显示0.8 的拆分比例的屏幕截图。":::

1. 将“双类提升决策树”模块拖放到画布中。

1. 将 "**训练模型**" 模块拖放到画布中。 指定输入，方法是将其连接到**双类提升决策树**（ML 算法），并**拆分数据**（用于训练算法的数据）模块。

1. 对于 "训练模型模型"，在 "属性" 窗格中的 "**标签列**" 选项中，选择 "编辑列"。 选择**BikeBuyer**列作为要预测的列，然后选择 "**保存**"。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/label-column.png" alt-text="显示标签列 BikeBuyer 选定的屏幕截图。":::

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/train-model.png" alt-text="显示连接到双类提升决策树和拆分数据模块的定型模型模块的屏幕截图。":::

## <a name="score-the-model"></a>为模型评分

现在，测试模型如何在测试数据上执行。 将比较两个不同的算法，以查看哪一个算法的性能更佳。 请遵循以下步骤进行配置：

1. 将“评分模型”模块拖放到画布中，并将其连接到“训练模型”和“拆分模型”模块  。

1. 将**平均感知器的双类 Bayes**拖动到试验画布中。 与双类提升决策树相比，将比较此算法的执行方式。

1. 复制并粘贴 "模块**训练模型**" 和 "**评分模型**"。

1. 将“评估模型”模块拖放到画布以比较两种算法。

1. 单击 "**提交**" 以设置管道运行。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-in.png" alt-text="画布上所有剩余模块的屏幕截图。" lightbox="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/algo-comparison-zoomed-out.png":::

1. 运行完成后，右键单击 "**评估模型**" 模块，并单击 "**可视化计算结果**"。

    :::image type="content" source="./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/result-visualize-zoomed-out.png" alt-text="结果的屏幕截图。":::

提供的指标包括 ROC 曲线、精度和召回率示意图以及提升曲线。 查看这些指标，查看第一个模型的执行效果优于第二个模型。 若要查看第一个模型的预测内容，请右键单击 "评分模型" 模块，并单击 "可视化评分的数据集" 查看预测的结果。

你将看到添加到测试数据集的两个列。

* 评分概率：客户购买自行车的可能性。
* 评分标签：模型执行的分类 – 自行车的购买者 (1) 或不是购买者 (0)。 标签的概率阈值设置为 50%，并可以调整。

将列 BikeBuyer （实际）与评分标签（预测）进行比较，以查看模型的执行情况。 接下来，您可以使用此模型来预测新客户。 可以[将此模型发布为 web 服务](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy)，或将结果写回 Azure Synapse。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 机器学习，请参阅[Azure 上的机器学习简介](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)。

在[此处](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)了解数据仓库的内置评分。