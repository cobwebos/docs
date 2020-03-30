---
title: 使用 Azure 机器学习分析数据
description: 使用 Azure 机器学习基于 Azure 突触中存储的数据构建预测机器学习模型。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 07570fd456d7f5a75a6b5a3ee635605a5d40072a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350572"
---
# <a name="analyze-data-with-azure-machine-learning"></a>使用 Azure 机器学习分析数据
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure 机器学习](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

本教程使用 Azure 机器学习构建基于 Azure 突触中存储的数据的预测机器学习模型。 具体而言，这就是通过预测客户是否有可能购买自行车，为自行车商店 Adventure Works 打造的有针对性的市场营销活动。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>先决条件
要逐步完成本教程，需要以下各项：

* 预加载了 AdventureWorksDW 示例数据的 SQL 池。 要预配此内容，请参阅[创建 SQL 池](create-data-warehouse-portal.md)并选择加载示例数据。 如果已有数据仓库但没有示例数据，可以[手动加载示例数据](load-data-from-azure-blob-storage-using-polybase.md)。

## <a name="1-get-the-data"></a>1. 获取数据
数据位于 AdventureWorksDW 数据库的 dbo.vTargetMail 视图中。 若要读取此数据：

1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)并单击“我的试验”。
2. 单击屏幕左下角的 **+NEW，** 然后选择 **"空白实验**"。
3. 输入试验名称：目标营销。
4. 将"数据输入"下的 **"导入数据模块"** 从模块窗格**中拖**出到画布中。
5. 在"属性"窗格中指定 SQL 池的详细信息。
6. 指定数据库 **查询** 以读取所需的数据。

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

单击"在实验画布下运行"来**运行**实验。

![运行试验](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

试验成功运行完毕后，单击读取器模块底部的输出端口，并选择“可视化”**** 以查看导入的数据。

![查看导入的数据](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. 清洁数据
清理数据时，请删除与模型无关的某些列。 为此，请按以下步骤操作：

1. 将"数据**转换<操作**下的**数据集模块中的"选择列**"拖动到画布中。 将此模块连接到**导入数据**模块。
2. 单击“属性”窗格中的“启动列选择器”**** 来指定想要删除的列。

   ![投影列](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. 排除两个列：CustomerAlternateKey 和 GeographyKey。

   ![删除不需要的列](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. 构建模型
我们以 80-20 的比例拆分数据：80% 用于训练机器学习模型，20% 用于测试模型。 我们使用“双类”算法处理此二元分类问题。

1. 将“拆分”**** 模块拖放到画布中。
2. 在属性窗格中，为第一个输出数据集中的行分数输入 0.8。

   ![将数据拆分为训练集和测试集](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. 将“双类提升决策树”**** 模块拖放到画布中。
4. 将**训练模型**模块拖动到画布中，并通过将其连接到**双类提升决策树**（ML 算法） 和**拆分**（数据以训练算法）模块来指定输入。 

     ![连接“训练模型”模块](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. 然后在“属性”窗格中单击“启动列选择器”****。 选择 **BikeBuyer** 列作为要预测的列。

   ![选择要预测的列](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. 对模型进行评分
现在，我们将测试模型如何在测试数据上执行。 我们将比较选择的算法和不同的算法，以查看哪一个的执行效果更好。

1. 将**分数模型**模块拖动到画布中，并将其连接到**训练模型**和**拆分数据**模块。

   ![为模型评分](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. 将“双类贝叶斯点机”**** 拖放到试验画布中。 我们将比较此算法和双类提升决策树的执行效果。
3. 复制“训练模型”和“评分模型”模块并将其粘贴在画布中。
4. 将“评估模型”**** 模块拖放到画布以比较两种算法。
5. **运行** 试验。

   ![运行试验](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. 单击“评估模型”模块底部的输出端口，并单击“可视化”。

   ![可视化评估结果](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

提供的指标包括 ROC 曲线、精度召回图和提升曲线。 查看这些度量值，我们可以看到第一个模型的执行效果优于第二个。 要查看第一个模型的预测，请单击分数模型的输出端口，然后单击"可视化"。

![可视化评分结果](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

会看到另外两个列已添加到测试数据集。

* 评分概率：客户购买自行车的可能性。
* 评分标签：模型执行的分类 – 自行车的购买者 (1) 或不是购买者 (0)。 标签的概率阈值设置为 50%，并可以调整。

比较 BikeBuyer（实际）列和评分标签（预测），可以看到模型的执行效果。 接下来，可以使用此模型为新客户进行预测，并将此模型发布为 Web 服务，或将结果写回 Azure Synapse。

## <a name="next-steps"></a>后续步骤
若要深入了解如何构建预测性机器学习模型，请参阅 [Azure 上的机器学习简介](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/)。