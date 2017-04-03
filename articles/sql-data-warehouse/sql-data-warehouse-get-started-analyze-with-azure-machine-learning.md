---
title: "使用 Azure 机器学习分析数据 | Microsoft Docs"
description: "使用 Azure 机器学习，基于存储在 Azure SQL 数据仓库中的数据生成预测机器学习模型。"
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
ms.author: kevin
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a5befbf1dc1862b3b0803eb4940341d246ec036c
ms.lasthandoff: 03/14/2017


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

本教程使用 Azure 机器学习功能，根据存储在 Azure SQL 数据仓库中的数据，生成预测机器学习模型。 具体而言，这就是通过预测客户是否有可能购买自行车，为自行车商店 Adventure Works 打造的有针对性的市场营销活动。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>先决条件
若要逐步完成本教程，你需要：

* 随 AdventureWorksDW 示例数据预先加载的 SQL 数据仓库。 若要完成此预配，请参阅[创建 SQL 数据仓库][Create a SQL Data Warehouse]，并选择加载示例数据。 如果已有数据仓库但没有示例数据，可以[手动加载示例数据][load sample data manually]。

## <a name="1-get-the-data"></a>1.获取数据
数据位于 AdventureWorksDW 数据库的 dbo.vTargetMail 视图中。 若要读取此数据：

1. 登录到 [Azure 机器学习工作室][Azure Machine Learning studio]并单击“我的试验”。
2. 单击“+新建”并选择“空白试验”。
3. 输入试验名称：目标营销。
4. 将“读取器”模块从模块窗格拖放到画布上。
5. 在“属性”窗格中指定 SQL 数据仓库数据库的详细信息。
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

单击试验画布下面的“运行”以运行试验。
![运行试验][1]

试验成功运行完毕后，单击读取器模块底部的输出端口，然后选择“可视化”以查看导入的数据。
![查看导入的数据][3]

## <a name="2-clean-the-data"></a>2.清理数据
清理数据时，请删除与模型无关的某些列。 为此，请按以下步骤操作：

1. 将“项目列”模块拖放到画布中。
2. 单击“属性”窗格中的“启动列选择器”来指定想要删除的列。
   ![项目列][4]
3. 排除两个列：CustomerAlternateKey 和 GeographyKey。
   ![删除不需要的列][5]

## <a name="3-build-the-model"></a>3.生成模型
我们将以 80-20 的比例拆分数据：80% 用于训练机器学习模型，20% 用于测试模型。 我们将使用“双类”算法处理此二元分类问题。

1. 将“拆分”模块拖放到画布中。
2. 在“属性”窗格中，为“第一个输出集中的行部分”输入 0.8。
   ![将数据拆分为训练集和测试集][6]
3. 将“双类提升决策树”模块拖放到画布中。
4. 将“训练模型”模块拖放到画布中并指定输入。 然后在“属性”窗格中单击“启动列选择器”。
   * 第一个输入：ML 算法。
   * 第二个输入：用于训练算法的数据。
     ![连接“训练模型”模块][7]
5. 选择 **BikeBuyer** 列作为要预测的列。
   ![选择要预测的列][8]

## <a name="4-score-the-model"></a>4.为模型评分
现在，我们将测试模型如何在测试数据上执行。 我们将比较选择的算法和不同的算法，以查看哪一个的执行效果更好。

1. 将“评分模型”拖放到画布中。
    第一个输入：训练模型。第二个输入：测试数据![为模型评分][9]
2. 将“双类贝叶斯点机”拖放到试验画布中。 我们将比较此算法和双类提升决策树的执行效果。
3. 复制“训练模型”和“评分模型”模块并将其粘贴在画布中。
4. 将“评估模型”模块拖放到画布以比较两种算法。
5. **运行** 试验。
   ![运行试验][10]
6. 单击“评估模型”模块底部的输出端口，然后单击“可视化”。
   ![可视化评估结果][11]

提供的度量值包括 ROC 曲线、精度和召回率示意图以及提升曲线。 查看这些度量值，我们可以看到第一个模型的执行效果优于第二个。 若要查看第一个模型的预测内容，请单击“评分模型”的输出端口，然后单击“可视化”。
![可视化评分结果][12]

你将看到另外两个列已添加到测试数据集。

* 评分概率：客户购买自行车的可能性。
* 评分标签：模型执行的分类 – 自行车的购买者 (1) 或不是购买者 (0)。 标签的概率阈值设置为 50%，并可以调整。

比较 BikeBuyer（实际）列和评分标签（预测），可以看到模型的执行效果。 在后面的步骤中，你可以使用此模型预测新的客户，并将其发布为 Web 服务，或将结果写回到 SQL 数据仓库。

## <a name="next-steps"></a>后续步骤
若要深入了解如何构建预测性机器学习模型，请参阅 [Azure 上的机器学习简介][Introduction to Machine Learning on Azure]。

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

