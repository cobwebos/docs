<properties
   pageTitle="将 Azure 机器学习与 SQL 数据仓库配合使用 | Microsoft Azure"
   description="有关在开发解决方案时使用 Azure SQL 数据仓库的 Azure 机器学习功能的教程。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/06/2015"
   wacn.date=""/>

# 将 Azure 机器学习与 SQL 数据仓库配合使用

Azure 机器学习是一项完全托管的预测分析服务，可用于在 SQL 数据仓库中针对数据创建预测模型，并将这些模型发布为随时可供使用的 Web 服务。你可以阅读 [Azure 上的机器学习简介][]，了解有关预测分析和机器学习的基本知识。然后，你可以使用[创建试验教程][]了解如何创建、训练、评分和测试机器学习模型。

在本文中，你将了解如何使用 [Azure Machine Learning Studio][] 来执行以下操作：

- 从数据库读取数据以创建、训练和评分预测模型
- 将数据写入数据库 


## 从 SQL 数据仓库读取数据

我们将从 AdventureWorksDW 数据库中的 Product 表读取数据。

### 步骤 1

单击 Machine Learning Studio 窗口底部的“+新建”，选择“试验”，然后选择“空白试验”开始新的试验。选择画布顶部的默认试验名称，然后将它重命名为有意义的名称，例如“自行车价格预测”。

### 步骤 2

在试验画布左侧的数据集和模块控制板中查找“读取器”模块。将此模块拖放到试验画布。![][drag_reader]

### 步骤 3

选择“读取器”模块并填写属性窗格。

1. 选择 Azure SQL 数据库作为数据源。
2. 数据库服务器名称：键入服务器名称。可以使用 [Azure 门户][]查找此信息。

![][server_name]

3. 数据库名称：键入刚刚指定的服务器上的数据库名称。 
4. 服务器用户帐户名：键入具有数据库访问权限的帐户的用户名。 
5. 服务器用户帐户名：提供指定的用户帐户的密码。
6. 接受任何服务器证书：如果要在读取数据之前跳过审查站点证书，请使用此选项（较不安全）。
7. 数据库查询：输入 SQL 语句用于描述你要读取的数据。在本例中，我们将使用以下查询从 Product 表读取数据。


```
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### 步骤 4

1. 单击试验画布下面的“运行”以运行试验。
2. 试验完成时，“读取器”模块将出现绿色复选标记，表示它已成功完成。另请留意右上角的“已完成运行”状态。

![][run]

3. 若要查看导入的数据，请单击汽车数据集底部的输出端口，然后选择“可视化”。


## 创建、训练和评分模型

现在，你可以使用此数据集来执行以下操作：

- 创建模型：处理数据并定义特征
- 训练模型：选择并应用学习算法
- 评分和测试模型：预测新的自行车价格
 

![][model]

若要了解如何创建、训练、评分和测试机器学习模型，然后，请使用[创建试验教程][]。

## 将数据写入到 Azure SQL 数据仓库

我们要将结果集写入到 AdventureWorksDW 数据库中的 ProductPriceForecast 表。

### 步骤 1

在试验画布左侧的数据集和模块控制板中查找“写入器”模块。将此模块拖放到试验画布。

![][drag_writer]

### 步骤 2

选择“写入器”模块并填写属性窗格。

1. 选择 Azure SQL 数据库作为数据目标。
2. 数据库服务器名称：键入服务器名称。可以使用 [Azure 门户][]查找此信息。 
3. 数据库名称：键入刚刚指定的服务器上的数据库名称。 
4. 服务器用户帐户名：键入具有数据库写入访问权限的帐户的用户名。 
5. 服务器用户帐户名：提供指定的用户帐户的密码。
6. 接受任何服务器证书(不安全)：如果你不想要查看证书，请选择此选项。
7. 要保存的列的逗号分隔列表：提供要输出的数据集或结果列的列表。
8. 数据表名称：指定数据表的名称。
9. 数据表列的逗号分隔列表：指定要在新表中使用的列名称。列名可以不同于源数据集中的名称，但在此处列出的列数必须与你为输出表定义的列数相同。
10. 每个 SQL Azure 操作写入的行数：你可以配置在一个操作中写入到 SQL 数据库的行数。

![][writer_properties]

### 步骤 3

1. 单击试验画布下面的“运行”以运行试验。
2. 试验完成时，所有模块将出现绿色复选标记，表示它们已成功完成。 

## 后续步骤

有关集成的概述，请参阅 [SQL 数据仓库集成概述][]。

有关更多开发技巧，请参阅 [SQL 数据仓库开发概述][]。

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[SQL 数据仓库开发概述]: ./sql-data-warehouse-overview-develop/
[SQL 数据仓库集成概述]: ./sql-data-warehouse-overview-integration/
[创建试验教程]: /documentation/articles/machine-learning-create-experiment/
[Azure 上的机器学习简介]: /documentation/articles/machine-learning-what-is-machine-learning/
[Azure Machine Learning Studio]: https://studio.azureml.net/Home
[Azure 门户]: https://manage.windowsazure.cn

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: /documentation/services/machine-learning/

<!---HONumber=Mooncake_1207_2015-->