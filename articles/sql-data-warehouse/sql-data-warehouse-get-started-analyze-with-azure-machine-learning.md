---
title: "使用 Azure 机器学习分析数据 |Microsoft 文档"
description: "使用 Azure 机器学习构建预测性机器学习模型基于存储在 Azure SQL 数据仓库的数据。"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 95635460-150f-4a50-be9c-5ddc5797f8a9
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 03/02/2017
ms.author: kevin;barbkess
ms.openlocfilehash: 3197948e32fe5c95b111fe5495a0e5f85966a24b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
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

本教程使用 Azure 机器学习来生成预测性机器学习模型基于存储在 Azure SQL 数据仓库的数据。 具体而言，这针对性的营销活动为生成 Adventure Works，自行车式应用商店，预测客户是否可能购买自行车，或未通过。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>先决条件
若要逐步完成本教程，你需要：

* SQL 数据仓库预先加载了 AdventureWorksDW 示例数据。 若要设置此，请参阅[创建 SQL 数据仓库][ Create a SQL Data Warehouse] ，并选择加载示例数据。 如果你已有数据仓库，但没有示例数据，则可以[手动加载示例数据][load sample data manually]。

## <a name="1-get-the-data"></a>1.获取数据
数据位于 AdventureWorksDW 数据库中的 dbo.vTargetMail 视图中。 读取此数据：

1. 登录到[Azure Machine Learning studio] [ Azure Machine Learning studio]单击我的试验。
2. 单击**+ 新建**和选择**空白试验**。
3. 输入试验名称： 目标营销。
4. 拖动**读取器**从到画布模块窗格中的模块。
5. 在属性窗格中指定 SQL 数据仓库数据库的详细的信息。
6. 指定的数据库**查询**读取感兴趣的数据。

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

通过单击运行此试验**运行**试验画布下面。
![运行此试验][1]

试验成功运行完毕后，单击底部的读取器模块的输出端口并选择**可视化**以查看导入的数据。
![查看导入的数据][3]

## <a name="2-clean-the-data"></a>2.清理数据
若要清理数据，删除不相关的模型的某些列。 为此，请执行以下操作：

1. 拖动**投影列**到画布的模块。
2. 单击**启动列选择器**你想要删除在属性窗格中，以指定哪些列。
   ![项目列][4]
3. 排除两个列： CustomerAlternateKey 和 GeographyKey。
   ![删除不必要的列][5]

## <a name="3-build-the-model"></a>3.生成模型
我们将拆分数据 80-20: 80%用于训练机器学习模型，20%用于测试模型。 我们将使用"双类"算法的此二元分类问题。

1. 拖动**拆分**到画布的模块。
2. 中属性窗格中的第一个输出数据集的行部分输入 0.8。
   ![将数据拆分为训练和测试集][6]
3. 拖动**双类提升决策树**到画布的模块。
4. 拖动**训练模型**到画布的模块和指定的输入。 然后，单击**启动列选择器**在属性窗格中。
   * 第一个输入： ML 算法。
   * 第二个输入： 训练算法的数据。
     ![连接训练模型模块][7]
5. 选择**BikeBuyer**列作为要预测的列。
   ![选择要预测的列][8]

## <a name="4-score-the-model"></a>4.为模型评分
现在，我们将测试模型如何执行测试数据。 我们将比较具有不同的算法，以查看其更好地执行我们的选择的算法。

1. 拖动**评分模型**到画布的模块。
    第一个输入： 训练模型的第二个输入： 测试数据![为模型评分][9]
2. 拖动**双类贝叶斯点机**到试验画布。 我们将比较此算法与双类提升决策树相比的执行方式。
3. 复制并粘贴在画布中的模块训练模型和评分模型。
4. 拖动**评估模型**到画布以比较这两种算法的模块。
5. **运行**试验。
   ![运行此试验][10]
6. 单击底部的评估模型模块的输出端口，然后单击可视化。
   ![可视化评估结果][11]

提供的度量值包括 ROC 曲线、 精度和召回率示意图以及提升曲线。 查看这些度量值，我们可以看到第一个模型的执行效果优于第二个。 要查看什么第一个模型的预测，单击评分模型的输出端口，然后单击可视化。
![可视化评分结果][12]

你将看到两列添加到你的测试数据集。

* 评分概率： 客户是购买自行车的可能性。
* 评分标签： 的分类模型 – 自行车购买者 (1) 或不 (0)。 此标签的概率阈值设置为 50%，并且可以进行调整。

比较 BikeBuyer （实际） 和评分标签 （预测） 的列，你可以看到模型的执行程度。 在后面的步骤，你可以使用此模型进行新的客户的预测和发布此模型作为 web 服务或将结果写回到 SQL 数据仓库。

## <a name="next-steps"></a>后续步骤
若要了解有关构建预测性机器学习模型的详细信息，请参阅[Azure 上的机器学习简介][Introduction to Machine Learning on Azure]。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
