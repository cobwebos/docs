---
title: "Azure 数据工厂 - 示例"
description: "提供 Azure 数据工厂服务相关示例的详细信息。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c0538b90-2695-4c4c-a6c8-82f59111f4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 3137773458ca5b50b1bf42972d4cfd8e9b8d5cba


---
# <a name="azure-data-factory---samples"></a>Azure 数据工厂 - 示例
## <a name="samples-on-github"></a>GitHub 上的示例
[GitHub Azure-DataFactory 存储库](https://github.com/azure/azure-datafactory)中的几个示例可帮助快速熟悉 Azure 数据工厂服务或修改脚本并将其用于自己的应用程序。 Samples\JSON 文件夹包含用于常见方案的 JSON 片段。

| 示例 | 说明 |
|:--- |:--- |
| [ADF 演练](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |此示例提供端到端演练，以使用 Azure 数据工厂处理日志文件，从而将日志文件中的数据转换为见解。 <br/><br/>在本演练中，数据工厂管道收集示例日志、处理和扩充日志中的数据（包含引用数据）并转换数据，以评估最近启动的市场营销活动的有效性。 |
| [JSON 示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |此示例提供常见方案的 JSON 示例。 |
| [Http 数据下载程序示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |此示例展示如何使用自定义 .NET 活动将数据从 HTTP 终结点下载到 Azure Blob 存储。 |
| [跨 AppDomain Dot Net 活动示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |借助此示例，可创建自定义 .NET 活动，该活动不受 ADF 启动器使用的程序集版本限制（例如，WindowsAzure.Storage v4.3.0、 Newtonsoft.Json v6.0.x 等）。 |
| [运行 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |此示例包括可用来调用 RScript.exe 的数据工厂自定义活动。 此示例仅适用于已安装有 R 的用户自己（非按需）的 HDInsight 群集。 |
| [调用 HDInsight Hadoop 群集上的 Spark 作业](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |此示例演示如何使用 MapReduce 活动调用 Spark 程序。 Spark 程序只是将数据从一个 Azure Blob 容器复制到其他的 Azure Blob容器。 |
| [使用 Azure 机器学习批处理评分活动的 Twitter 分析](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |此示例演示如何使用 AzureMLBatchScoringActivity 调用执行 Twitter 情绪分析、评分、预测等的 Azure 机器学习模型。 |
| [使用自定义活动的 Twitter 分析](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |此示例演示如何使用自定义 .NET 活动调用执行 Twitter 情绪分析、评分、预测等的 Azure 机器学习模型。 |
| [Azure 机器学习的参数化管道](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |此示例提供端到端 C# 代码来部署 N 个管道进行评分和重新训练，每个管道都具有不同的区域参数，其中区域列表来自 parameters.txt 文件，该文件包含在此示例中。 |
| [Azure 流分析作业的引用数据刷新](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |此示例演示如何将 Azure 数据工厂和 Azure 流分析配合使用以运行引用数据的查询并将引用数据设置为定期刷新。 |
| [本地 Hortonworks Hadoop 的混合管道](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |此示例将本地 Hadoop 群集用作在数据工厂中运行作业的计算目标，就好像要在云中添加基于 HDInsight 的 Hadoop 群集等其他计算目标一样。 |
| [JSON 转换工具](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |借助此工具，可将 JSON 从 2015-07-01-preview 之前的版本转换到最新版本或 2015-07-01-preview 版本（默认）。 |
| [U-SQL 示例输入文件](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |此文件是 U-SQL 活动使用的示例文件。 |

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager 模板
可在 Github 上找到数据工厂的以下 Azure Resource Manager 模板。

| 模板 | 说明 |
| --- | --- |
| [从 Azure Blob 存储复制到 Azure SQL 数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) |部署此模板将创建 Azure 数据工厂，其中包含可将数据从指定的 Azure Blob 存储复制到 Azure SQL 数据库的管道 |
| [从 Salesforce 复制到 Azure Blob 存储](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) |部署此模板将创建 Azure 数据工厂，其中包含可将数据从指定的 Salesforce 帐户复制到 Azure Blob 存储的管道。 |
| [在 Azure HDInsight 群集上通过运行 Hive 脚本来转换数据](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) |部署此模板将创建包含管道的 Azure 数据工厂，该管道可以通过在 Azure HDInsight Hadoop 群集上运行示例 Hive 脚本来转换数据。 |

## <a name="samples-in-azure-portal"></a>Azure 门户中的示例
可以使用数据工厂主页上的“示例管道”磁贴来将示例管道和与其相关联的实体（数据集和链接服务）部署到数据工厂。

1. 创建数据工厂或打开现有数据工厂。 有关创建数据工厂的步骤，请参阅 [使用数据工厂将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
2. 在数据工厂的“数据工厂”边栏选项卡中，单击“示例管道”磁贴。

    ![“示例管道”磁贴](./media/data-factory-samples/SamplePipelinesTile.png)
3. 在“示例管道”边栏选项卡中，单击要部署的“示例”。

    ![“示例管道”边栏选项卡](./media/data-factory-samples/SampleTile.png)
4. 指定此示例的配置设置。 例如，Azure 存储帐户名和帐户密钥、Azure SQL server 名称、数据库、用户 ID 和密码等。

    ![“示例”边栏选项卡](./media/data-factory-samples/SampleBlade.png)
5. 指定配置设置后，单击“创建”可以创建/部署示例管道和此管道使用的链接服务/表格。
6. 可在之前在“示例管道”边栏选项卡上单击的“示例”磁贴上看到部署状态。

    ![部署状态](./media/data-factory-samples/DeploymentStatus.png)
7. 在此示例的磁贴上看到“已成功部署”消息时，关闭“示例管道”边栏选项卡。  
8. 在“数据工厂”边栏选项卡上，将看到链接服务、数据集和管道已添加到数据工厂。  

    ![“数据工厂”边栏选项卡](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="samples-in-visual-studio"></a>Visual Studio 中的示例
### <a name="prerequisites"></a>先决条件
必须在计算机上安装以下软件：

* Visual Studio 2013 或 Visual Studio 2015
* 下载用于 Visual Studio 2013 或 Visual Studio 2015 的 Azure SDK。 导航到 [Azure 下载页](https://azure.microsoft.com/downloads/)，在“.NET”部分中单击“VS 2013”或“VS 2015”。
* 下载用于 Visual Studio 的最新 Azure 数据工厂插件：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。 如果使用的是 Visual Studio 2013，还可通过执行以下步骤更新插件：在菜单上，依次单击“工具” -> “扩展和更新” -> “联机” -> “Visual Studio 库” -> “适用于 Visual Studio 的 Microsoft Azure 数据工厂工具” -> “更新”。

### <a name="use-data-factory-templates"></a>使用数据工厂模板
1. 在菜单上单击“文件”，指向“新建”并单击“项目”。
2. 在“新建项目”对话框中，执行以下步骤：

   1. 在“模板”下选择“DataFactory”。
   2. 在右窗格中选择“数据工厂模板”。
   3. 输入项目的**名称**。
   4. 选择项目的**位置**。
   5. 单击 **“确定”**。

      ![“新建项目”对话框](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. 在“数据工厂模板”对话框中，从“用例模板”部分选择示例模板，然后单击“下一步”。 以下步骤将引导用户完成“客户分析”模板的使用。 步骤与其他示例的步骤类似。

    ![“数据工厂模板”对话框](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. 在“数据工厂配置”对话框的“数据工厂基本”上，单击“下一步”。
5. 在“配置数据工厂”页上，请按以下步骤操作：
   1. 选择“新建数据工厂”。 还可选择“使用现有数据工厂”。
   2. 输入数据工厂的**名称**。
   3. 选择要在其中创建数据工厂的 **Azure 订阅**。
   4. 为数据工厂选择**资源组**。
   5. 在“区域”中选择“美国西部”、“美国东部”或“北欧”。
   6. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“配置数据存储”页上，指定现有的“Azure SQL 数据库”和“Azure 存储帐户”或创建数据库/存储，并单击“下一步”。
7. 在“配置计算”页上，选择“默认”，然后单击“下一步”。
8. 在“摘要”页上，查看所有设置，然后单击“下一步”。
9. 在“部署状态”页上，等待部署完成后，单击“完成”。
10. 在“解决方案资源管理器”中，右键单击该项目，并单击“发布” 。
11. 如果显示“登录到 Microsoft 帐户”对话框，请输入拥有 Azure 订阅的帐户凭据，然后单击“登录”。
12. 应该会看到以下对话框：

    ![“发布”对话框](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. 在“配置数据工厂”页，按照以下步骤操作：

    1. 确认“使用现有数据工厂”选项。
    2. 选择使用模板时选中的**数据工厂**。
    3. 单击“下一步”切换到“发布项”页。 （如果“下一步”按钮已禁用，请按 **TAB** 移出“名称”字段）。
14. 在“发布项”页上，确保已选择所有数据工厂实体，然后单击“下一步”切换到“摘要”页。     
15. 查看摘要，单击“下一步”，启动部署过程并查看“部署状态”。
16. 在“部署状态”  页上，应看到部署过程的状态。 部署完成后，单击“完成”。

若要深入了解如何使用 Visual Studio 来创作数据工厂实体并将其发布到 Azure，请参阅[生成第一个数据工厂 (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md)。          



<!--HONumber=Nov16_HO3-->


