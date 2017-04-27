---
title: "Azure 数据工厂 - 常见问题解答"
description: "有关 Azure 数据工厂的常见问题解答。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/24/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 14b7900a8b4fbf86e8a814def6fa8c7915832376
ms.lasthandoff: 04/12/2017


---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure 数据工厂 - 常见问题解答
## <a name="general-questions"></a>一般问题
### <a name="what-is-azure-data-factory"></a>什么是 Azure 数据工厂？
数据工厂是一项基于云的数据集成服务，可以**自动移动和转换数据**。 如同工厂运转设备将原材料转换为成品一样，数据工厂可协调现有的服务，收集原始数据并将其转换为随时可用的信息。

数据工厂可用于创建数据驱动的工作流，以同时在本地和云数据存储区之间移动数据，以及使用 Azure HDInsight 和 Azure Data Lake Analytics 等计算服务处理/转换数据。 创建管道（用于执行所需操作）后，可以安排它定期（每小时、每天、每周等）运行。   

有关详细信息，请参阅[概述和关键概念](data-factory-introduction.md)。

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>在哪里可以找到 Azure 数据工厂的定价详细信息？
有关 Azure 数据工厂的定价详细信息，请参阅[数据工厂定价详细信息页][adf-pricing-details]。  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>如何使用 Azure 数据工厂？
* 有关 Azure 数据工厂的概述，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)。
* 有关如何使用复制活动“复制/移动数据”的教程，请参阅[将数据从 Azure Blob 存储复制到 Azure SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
* 有关如何使用 HDInsight Hive 活动“转换数据”的教程。 请参阅[通过在 Hadoop 群集上运行 Hive 脚本处理数据](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>数据工厂的区域可用性是什么？
数据工厂可在**美国西部**和**北欧**使用。 数据工厂使用的计算和存储服务可以位于其他区域。 请参阅[支持的区域](data-factory-introduction.md#supported-regions)。

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>数据工厂/管道/活动/数据集的数量限制有哪些？
请参阅 [Azure 订阅和服务限制、配额与约束条件](../azure-subscription-service-limits.md#data-factory-limits)一文的 **Azure 数据工厂限制**部分。

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Azure 数据工厂服务的创作/开发人员体验是什么？
可以使用下列工具/SDK 之一创作/创建数据工厂：

* **Azure 门户**
    Azure 门户中的“数据工厂”边栏选项卡提供丰富的用户界面，可供你创建数据工厂和链接服务。 “数据工厂编辑器”也是该门户的一部分，可用于创建链接服务、表、数据集和管道并且操作简单，通过指定这些项目的 JSON 定义即可实现。 有关使用门户/编辑器创建和部署数据工厂的示例，请参阅[使用 Azure 门户构建第一个数据管道](data-factory-build-your-first-pipeline-using-editor.md)。
* **Visual Studio**
    可以使用 Visual Studio 创建 Azure 数据工厂。 有关详细信息，请参阅[使用 Visual Studio 构建第一个数据管道](data-factory-build-your-first-pipeline-using-vs.md)。
* **Azure PowerShell**
    有关使用 PowerShell 创建数据工厂的教程/演练，请参阅[使用 Azure PowerShell 创建和监视 Azure 数据工厂](data-factory-build-your-first-pipeline-using-powershell.md)。 有关数据工厂 cmdlet 的综合文档，请参阅 MSDN 库上的[数据工厂 Cmdlet 参考][adf-powershell-reference]内容。
* **.NET 类库**
    可以通过使用数据工厂 .NET SDK 以编程方式创建数据工厂。 有关使用 .NET SDK 创建数据工厂的演练，请参阅[使用 .NET SDK 创建、监视和管理数据工厂](data-factory-create-data-factories-programmatically.md)。 有关数据工厂 .NET SDK 的综合文档，请参阅[数据工厂类库参考][msdn-class-library-reference]。
* **REST API**
    还可以使用 Azure 数据工厂服务所公开的 REST API 创建和部署数据工厂。 有关数据工厂 REST API 的综合文档，请参阅[数据工厂 REST API 参考][msdn-rest-api-reference]。
* **Azure Resource Manager 模板**
    有关详细信息，请参阅[教程：使用 Azure Resource Manager 模板构建第一个 Azure 数据工厂](data-factory-build-your-first-pipeline-using-arm.md)。

### <a name="can-i-rename-a-data-factory"></a>是否可以重命名数据工厂？
否。 和其他 Azure 资源一样，不可以更改 Azure 数据工厂的名称。

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>是否可以将数据工厂从一个 Azure 订阅移动到另一个？
是的。 使用数据工厂边栏选项卡上的“移动”按钮，如下面的关系图所示：

![移动数据工厂](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>数据工厂支持的计算环境有哪些？
下表提供了数据工厂所支持的计算环境以及可以在其上运行的活动的列表。

| 计算环境 | 活动 |
| --- | --- |
| [按需 HDInsight 群集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)或[自己的 HDInsight 群集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop 流式处理](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure 机器学习](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[机器学习活动：批处理执行和更新资源](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service)、[Azure SQL 数据仓库](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service)、[SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[存储过程](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Azure 数据工厂与 SQL Server Integration Services (SSIS) 相比如何？ 
请参阅 [Azure 数据工厂与 SSIS](http://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) 演示文稿，来自我们的 MVP（最有价值的专家）之一 Reza Rad。 幻灯片中可能未列出数据工厂中的一些最新更改。 我们将不断向 Azure 数据工厂添加更多的功能。 我们将不断向 Azure 数据工厂添加更多的功能。 我们会在今年晚些时候，将这些更新纳入 Microsoft 的数据集成技术的比较中。   

## <a name="activities---faq"></a>活动 - 常见问题解答
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>在数据工厂管道中可以使用哪些不同的活动类型？
* 用于移动数据的[数据移动活动](data-factory-data-movement-activities.md)。
* 用于处理/转换数据的[数据转换活动](data-factory-data-transformation-activities.md)。

### <a name="when-does-an-activity-run"></a>活动何时运行？
运行活动的时间由输出数据表中的“可用性”配置设置确定。 如果指定了输入数据集，则在开始运行之前，活动会检查是否满足所有输入数据依赖项（即**就绪**状态）。

## <a name="copy-activity---faq"></a>复制活动 - 常见问题解答
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>一个管道具有多个活动好还是每个活动都有单独的管道好？
假设管道要捆绑所有相关的活动。 如果管道之外的任何其他活动都未使用连接它们的数据集，则可以将活动保留在一个管道中。 这样，就不需要链接管道有效期间，使其彼此对齐。 此外，更新管道时，也会更好地保留管道内部表中的数据完整性。 实际上，管道更新会停止、删除并重新创建管道内的所有活动。 从创作角度来看，可能也比较容易看出管道的某个 JSON 文件中相关活动内的数据流。

### <a name="what-are-the-supported-data-stores"></a>支持的数据存储有哪些？
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

### <a name="what-are-the-supported-file-formats"></a>支持的文件格式有哪些？
[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>在哪里执行复制操作？
请参阅[全局可用的数据移动](data-factory-data-movement-activities.md#global)部分了解详细信息。 简单地说，当涉及一个本地数据存储时，复制操作由“数据管理网关”在本地环境中执行。 而在两个云存储之间移动数据时，复制操作是在最靠近相同地理位置内接收位置的区域中执行。

## <a name="hdinsight-activity---faq"></a>HDInsight 活动 - 常见问题解答
### <a name="what-regions-are-supported-by-hdinsight"></a>HDInsight 支持哪些区域？
请参阅以下文章中的“上市地区”部分：或 [HDInsight 定价详细信息][hdinsight-supported-regions]。

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>按需 HDInsight 群集使用哪个区域？
按需 HDInsight 群集的创建位置与你指定用于群集的存储位于同一区域。    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>如何将其他存储帐户关联到 HDInsight 群集？
如果你使用的是自己的 HDInsight 群集（BYOC - 自带群集），请参阅以下主题：

* [将 HDInsight 群集与备用存储帐户和元存储配合使用][hdinsight-alternate-storage]
* [将其他存储帐户与 HDInsight Hive 配合使用][hdinsight-alternate-storage-2]

如果你使用的是由数据工厂服务创建的按需群集，请为 HDInsight 链接服务指定其他存储帐户，以便数据工厂服务可以代表你注册它们。 在按需链接服务的 JSON 定义中，请使用 **additionalLinkedServiceNames** 属性指定备用存储帐户，如以下 JSON 代码段所示：

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "clusterSize": 1,
            "timeToLive": "00:01:00",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
在上面的示例中，otherLinkedServiceName1 和 otherLinkedServiceName2 表示链接服务，其定义包含 HDInsight 群集访问备用存储帐户所需的凭据。

## <a name="slices---faq"></a>切片 - 常见问题解答
### <a name="why-are-my-input-slices-not-in-ready-state"></a>为何我的输入切片不是处于“就绪”状态？
一个常见错误是，当输入数据是数据工厂的外部数据（不是由数据工厂生成）时，未将输入数据集上的“external”属性设置为 **true**。

在下面的示例中，只需将 **dataset1** 上的“external”属性设置为 true 即可。  

**DataFactory1** Pipeline 1: dataset1 -> activity1 -> dataset2 -> activity2 -> dataset3 Pipeline 2: dataset3-> activity3 -> dataset4

如果你有另一个数据工厂的管道采用 dataset4（由数据工厂 1 中的管道 2 生成），请将 dataset4 标记为外部数据集，因为该数据集是由另一个数据工厂（DataFactory1，而非 DataFactory2）生成。  

**DataFactory2**    
Pipeline 1: dataset4->activity4->dataset5

如果 external 属性设置正确，请验证输入数据是否存在于输入数据集定义中指定的位置。

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>当每天生成切片时，如何在午夜以外的其他时间运行切片？
请使用“offset”属性来指定要生成切片的时间。 如需此属性的详细信息，请参阅[数据集可用性](data-factory-create-datasets.md#Availability)部分。 下面是一个简短的示例：

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
每日切片在**上午 6 点**而不是默认的午夜开始。     

### <a name="how-can-i-rerun-a-slice"></a>如何重新运行切片？
可通过以下方式之一重新运行切片：

* 使用“监视和管理应用”重新运行活动窗口或切片。 有关说明，请参阅[重新运行所选活动窗口](data-factory-monitor-manage-app.md#perform-batch-actions)。   
* 在 Azure 门户中，在切片的“数据切片”边栏选项卡上，单击命令栏中的“运行”。
* 对切片运行 **Set-AzureRmDataFactorySliceStatus** cmdlet，同时将状态设置为“等待”。   

    ```PowerShell
    Set-AzureRmDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
有关该 cmdlet 的详细信息，请参阅 [Set-AzureRmDataFactorySliceStatus][set-azure-datafactory-slice-status]。

### <a name="how-long-did-it-take-to-process-a-slice"></a>处理一个切片需要多长时间？
请使用“监视和管理应用”中的活动窗口资源管理器来了解处理数据切片所花费的时间。 有关详细信息，请参阅[活动窗口资源管理器](data-factory-monitor-manage-app.md#activity-window-explorer)。

在 Azure 门户中，还可以执行以下操作：  

1. 在数据工厂的“数据工厂”边栏选项卡中，单击“数据集”磁贴。
2. 在“数据集”边栏选项卡上，单击特定数据集。
3. 在“表”边栏选项卡上的“最新的切片”列表中，选择你感兴趣的切片。
4. 在“数据切片”边栏选项卡上的“活动运行”列表中，单击运行的活动。
5. 在“活动运行详细信息”边栏选项卡上，单击“属性”磁贴。
6. 你应该会看到“持续时间”字段包含一个值。 该值就是处理切片所花费的时间。   

### <a name="how-to-stop-a-running-slice"></a>如何停止正在运行的切片？
如需停止执行管道，可以使用 [Suspend-AzureRmDataFactoryPipeline](https://msdn.microsoft.com/library/mt603721.aspx) cmdlet。 目前，挂起管道不会停止正在进行中的切片执行。 一旦完成正在进行中的执行，就不会再选取任何额外的切片。

如果确实想要立即停止所有执行，唯一的方法就是删除管道，然后再重新创建。 如果选择删除管道，“不”需要删除该管道所使用的表和链接服务。

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/azurerm.datafactories
[azure-portal]: http://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/resourcemanager/azurerm.datafactories/v2.3.0/set-azurermdatafactoryslicestatus

[adf-pricing-details]: http://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: http://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: http://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx

