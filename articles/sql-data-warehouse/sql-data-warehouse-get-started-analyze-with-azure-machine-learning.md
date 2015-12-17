<properties
   pageTitle="使用 Azure 机器学习分析数据 | Microsoft Azure"
   description="有关在开发解决方案时使用 Azure SQL 数据仓库的 Azure 机器学习功能的教程。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="10/21/2015"
   wacn.date=""/>

# 使用 Azure 机器学习分析数据
本教程介绍如何通过使用 Azure SQL 数据仓库数据的 Azure 机器学习构建预测性机器学习模型。在本教程中，我们将为 Adventure Work 构建有针对性的营销活动，预测某个客户是否有可能购买自行车。


## 先决条件
若要逐步完成本教程中，你需要：

- 包含 AdventureWorksDW 示例数据库的 SQL 数据仓库。

[创建 SQL 数据仓库][]演示如何预配包含示例数据的数据库。如果你已有 SQL 数据仓库数据库但没有示例数据，可以[手动加载示例数据][]。


## 步骤 1：获取数据 
我们将从 AdventureWorksDW 数据库中的 dbo.vTargetMail 视图读取数据。

1. 登录到 [Azure Machine Learning Studio][] 并单击我的试验。
2. 单击“+新建”并选择“空白试验”。
3. 输入试验名称：目标营销。
4. 将“读取器”模块从模块窗格拖放到画布上。
5. 在“属性”窗格中指定 SQL 数据仓库数据库的详细信息。 
6. 指定数据库**查询**以读取所需的数据。
   
   ```
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





## 步骤 2：清理数据
我们将要删除与模型无关的某些列。

1. 将“项目列”模块拖放到画布中。
2. 单击“属性”窗格中的“启动列选择器”来指定想要删除的列。
![项目列][4]

3. 排除两个列：CustomerAlternateKey 和 GeographyKey。
![删除不需要的列][5]




## 步骤 3：构建模型
我们将以 80-20 的比例拆分数据：80% 用于训练机器学习模型，20% 用于测试模型。我们将使用“双类”算法处理此二元分类问题。

1. 将“拆分”模块拖放到画布中。
2. 在“属性”窗格中，为“第一个输出集中的行部分”输入 0.8。![将数据拆分为训练集和测试集][6]
3. 将“双类提升决策树”模块拖放到画布中。
4. 将“训练模型”模块拖放到画布中并指定输入。然后单击“属性”窗格中的“启动列选择器”。
      - 第一个输入：ML 算法。
      - 第二个输入：用于训练算法的数据。![连接“训练模型”模块][7]
5. 选择 **BikeBuyer** 列作为要预测的列。![选择要预测的列][8]





## 步骤 4：评分模型
现在，我们将测试模型如何在测试数据上执行。我们将比较选择的算法和不同的算法，以查看哪一个的执行效果更好。

1. 将“评分模型”拖放到画布中。第一个输入：训练模型。第二个输入：测试数据 ![为模型评分][9]
2. 将“双类贝叶斯点机”拖放到试验画布中。我们将比较此算法和双类提升决策树的执行效果。
3. 复制“训练模型”和“评分模型”模块并将其粘贴在画布中。
4. 将“评估模型”模块拖放到画布以比较两种算法。
5. **运行**试验。![运行试验][10]
6. 单击“评估模型”模块底部的输出端口，然后单击“可视化”。![可视化评估结果][11]



提供的度量值包括 ROC 曲线、精度和召回率示意图以及提升曲线。查看这些度量值，我们可以看到第一个模型的执行效果优于第二个。若要查看第一个模型的预测内容，请单击“评分模型”的输出端口，然后单击“可视化”。
![可视化评分结果][12]

你将看到另外两个列已添加到测试数据集。

- 评分概率：客户购买自行车的可能性。
- 评分标签：模型执行的分类 – 自行车的购买者 (1) 或不是购买者 (0)。标签的概率阈值设置为 50%，并可以调整。

比较 BikeBuyer（实际）列和评分标签（预测），可以看到模型的执行效果。在后面的步骤中，你可以使用此模型预测新的客户，并将其发布为 Web 服务，或将结果写回到 SQL 数据仓库。

若要深入了解如何构建预测性机器学习模型，请参阅 [Azure 上的机器学习简介][]。



<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: ./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]: https://studio.azureml.net/
[Azure 上的机器学习简介]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[手动加载示例数据]: sql-data-warehouse-get-started-manually-load-samples.md
[创建 SQL 数据仓库]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Mooncake_1207_2015-->