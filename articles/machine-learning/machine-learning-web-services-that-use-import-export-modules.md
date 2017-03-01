---
title: "在 Azure 机器学习 Web 服务中使用导入/导出数据 | Microsoft 文档"
description: "了解如何使用导入数据和导出数据模块从 Web 服务发送和接收数据。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: 3a7ac351-ebd3-43a1-8c5d-18223903d08e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: 0dc02034ea1f3c4f8413aca1ff693838ad1e49bd


---
# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>部署使用数据导入和数据导出模块的 Azure 机器学习 Web 服务
创建预测性实验时，通常添加 Web 服务输入和输出。 部署实验时，使用者可通过输入和输出从 Web 服务发送和接收数据。 对于某些应用程序，使用者的数据可能从数据源提供或已经驻留在外部数据源（如 Azure Blob 存储）中。 在这些情况下，它们不需要使用 Web 服务输入和输出读取和写入数据。 它们可以改为使用批处理执行服务 (BES)，使用导入数据模块从数据源读取数据，使用导出数据模块将评分结果写入不同的数据位置。

导入数据和导出数据模块可在大量数据位置（如通过 HTTP 的 Web URL、Hive 查询、Azure SQL 数据库、Azure 表存储、Azure Blob 存储、数据源提供或本地 SQL 数据库）中读取和写入数据。

本主题使用“示例 5：二元分类的训练、测试、评估：成人数据集”示例，并假设数据集已加载到名为 censusdata 的 Azure SQL 表中。

## <a name="create-the-training-experiment"></a>创建训练实验
打开“示例 5：二元分类的训练、测试、评估：成人数据集”示例时，它使用示例成人人口收入二元分类数据集。 画布中的实验看起来将类似于下图。

![实验的初始配置。](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)

若要从 Azure SQL 表读取数据：

1. 删除数据集模块。
2. 在组件搜索框中，键入导入。
3. 从结果列表中，将*导入数据*模块添加到实验画布。
4. 将*导入数据*模块的输出连接到*清除缺少的数据*模块的输入。
5. 在属性窗格中，选择“数据源”下拉列表中的“Azure SQL 数据库”。
6. 在“数据库服务器名称”、“数据库名称”、“用户名”和“密码”字段中，输入数据库的相应信息。
7. 在“数据库查询”字段中，输入以下查询。
   
     select [age],
   
        [workclass],
        [fnlwgt],
        [education],
        [education-num],
        [marital-status],
        [occupation],
        [relationship],
        [race],
        [sex],
        [capital-gain],
        [capital-loss],
        [hours-per-week],
        [native-country],
        [income]
     from dbo.censusdata;
8. 在实验画布的底部，单击“**运行**”。

## <a name="create-the-predictive-experiment"></a>创建预测性实验
接下来，设置将从中部署 Web 服务的预测性实验。

1. 在实验画布的底部，单击“设置 Web 服务”，然后选择“预测 Web 服务 [推荐]”。
2. 从预测性实验中删除 *Web 服务输入*和 *Web 服务输出模块*。 
3. 在组件搜索框中，键入导出。
4. 从结果列表中，将*导出数据*模块添加到实验画布。
5. 将*评分模型*模块的输出连接到*导出数据*模块的输入。 
6. 在属性窗格中，在数据目标下拉列表中选择“Azure SQL 数据库”。
7. 在“数据库服务器名称”、“数据库名称”、“服务器用户帐户名”和“服务器用户帐户密码”字段中，输入数据库的相应信息。
8. 在“要保存的列的逗号分隔列表字段中，键入评分标签。
9. 在“数据表名称”字段中，键入 dbo.ScoredLabels。 如果表不存在，则在运行实验或调用 Web 服务时创建它。
10. 在“数据表列的逗号分隔列表”字段中，键入 ScoredLabels。

编写调用最终 Web 服务的应用程序时，可能要在运行时指定不同的输入查询或目标表。 若要配置这些输入和输出，可使用 Web 服务参数功能设置*导入数据*模块*数据源*属性和*导出数据*模式数据目标属性。  有关 Web 服务参数的详细信息，请参阅 Cortana Intelligence 和机器学习博客上的 [AzureML Web 服务参数条目](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/)。

若要为导入查询和目标表配置 Web 服务参数：

1. 在*导入数据*模块的属性窗格中，单击“数据库查询”字段右上方的图标，并选择“设置为 Web 服务参数”。
2. 在*导出数据*模块的属性窗格中，单击“数据表名称”字段右上方的图标，并选择“设置为 Web 服务参数”。
3. 在*导出数据*模块属性窗格底部，在“Web 服务参数”部分中单击“数据库查询”并将其重命名为“查询”。
4. 单击“数据表名称”并将其重命名为“表”。

完成后，实验应看起来类似于下图。

![实验的最终外观。](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

现在可以将实验部署为 Web 服务。

## <a name="deploy-the-web-service"></a>部署 Web 服务
可部署到经典或新 Web 服务。

### <a name="deploy-a-classic-web-service"></a>部署经典 Web 服务
若要部署为经典 Web 服务并创建一个应用程序来使用它：

1. 在实验画布的底部，单击“运行”。
2. 当运行已完成时，单击“部署 Web 服务”，然后选择“部署 Web 服务 [经典]”。
3. 在 Web 服务仪表板上，找到 API 密钥。 复制并保存它以供以后使用。
4. 在“默认终结点”表中，单击“批处理执行”链接打开 API 帮助页。
5. 在 Visual Studio 中，创建 C# 控制台应用程序。
6. 在 API 帮助页上，找到页面底部的“示例代码”部分。
7. 将 C# 示例代码复制并粘贴到 Program.cs 文件中，然后删除对 blob 存储的所有引用。
8. 使用之前保存的 API 密钥更新 *apiKey* 变量的值。
9. 找到请求声明并更新传递到*导入数据*和*导出数据*模块的值。 在此情况下，将使用原始查询，但定义新的表名称。
   
        var request = new BatchExecutionRequest() 
        {           
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable2" },
            }
        };
10. 运行应用程序。 

完成运行时，将新表添加到包含评分结果的数据库。

### <a name="deploy-a-new-web-service"></a>部署新的 Web 服务
若要部署为新的 Web 服务并创建一个应用程序来使用它：

1. 在实验画布的底部，单击“**运行**”。
2. 当运行已完成时，单击“部署 Web 服务”，然后选择“部署 Web 服务 [新]”。
3. 在“部署实验”页上，输入 Web 服务的名称并选择定价计划，然后单击“部署”。
4. 在“快速启动”页上，单击“使用”。
5. 在“示例代码”部分中，单击“批处理”。
6. 在 Visual Studio 中，创建 C# 控制台应用程序。
7. 将 C# 示例代码复制并粘贴到 Program.cs 文件中。
8. 使用位于“基本使用信息”部分中的**主键**替换 *apiKey* 变量的值。
9. 找到 *scoreRequest* 声明并更新传递到*导入数据*和*导出数据*模块的值。 在此情况下，将使用原始查询，但定义新的表名称。
   
        var scoreRequest = new
        {       
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };
10. 运行应用程序。 




<!--HONumber=Jan17_HO5-->


