---
title: "从 Azure 数据工厂调用 Spark 程序 | Microsoft Docs"
description: "了解如何使用 MapReduce 活动从 Azure 数据工厂调用 Spark 程序。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 124f04eb39296899af501d506476ad966442fc3d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/13/2017


---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>从 Azure 数据工厂管道调用 Spark 程序

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活动](data-factory-hive-activity.md)
> * [Pig 活动](data-factory-pig-activity.md)
> * [MapReduce 活动](data-factory-map-reduce.md)
> * [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
> * [Spark 活动](data-factory-spark.md)
> * [机器学习批处理执行活动](data-factory-azure-ml-batch-execution-activity.md)
> * [机器学习更新资源活动](data-factory-azure-ml-update-resource-activity.md)
> * [存储过程活动](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)
> * [.NET 自定义活动](data-factory-use-custom-activities.md)

## <a name="introduction"></a>介绍
Spark 活动是 Azure 数据工厂支持的[数据转换活动](data-factory-data-transformation-activities.md)之一。 此活动在 Azure HDInsight 中你的 Apache Spark 群集上运行指定的 Spark 程序。    

> [!IMPORTANT]
> - Spark 活动不支持那些使用 Azure Data Lake Store 作为主存储的 HDInsight Spark 群集。
> - Spark 活动仅支持现有的（你自己的）HDInsight Spark 群集。 它不支持按需 HDInsight 链接的服务。

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>演练：创建包含 Spark 活动的管道
下面是创建包含 Spark 活动的数据工厂管道的典型步骤。  

1. 创建数据工厂。
2. 创建一个 Azure 存储链接的服务来将与你的 HDInsight Spark 群集关联的 Azure 存储链接到该数据工厂。     
2. 创建一个 Azure HDInsight 链接的服务来将 Azure HDInsight 中你的 Apache Spark 群集链接到该数据工厂。
3. 创建引用 Azure 存储链接服务的数据集。 目前，即使不生成任何输出，也必须为活动指定输出数据集。  
4. 创建一个包含 Spark 活动的管道，使该活动引用在步骤 2 中创建的 HDInsight 链接的服务。 该活动配置为使用上一步骤中创建的数据集作为输出数据集。 输出数据集驱动计划（每小时、每日等）。 因此，即使该活动实际上不生成任何输出，也必须指定输出数据集。

### <a name="prerequisites"></a>先决条件
1. 遵循[创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)演练中的说明创建一个**通用 Azure 存储帐户**。  
2. 遵循[在 Azure HDInsight 中创建 Apache Spark 群集](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)教程中的说明**在 Azure HDInsight 中创建一个 Apache Spark 群集**。 将你在步骤 1 中创建的 Azure 存储帐户与此群集相关联。  
3. 下载并查看位于以下网址的 python 脚本文件 **test.py**：[https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)。  
3.  将 **test.py** 上载到你的 Azure Blob 存储中 **adfspark** 容器中的 **pyFiles** 文件夹。 如果该容器和文件夹不存在，则创建它们。

### <a name="create-data-factory"></a>创建数据工厂
首先，在此步骤中创建数据工厂。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中单击“新建”，然后依次单击“数据 + 分析”、“数据工厂”。
3. 在“新建数据工厂”边栏选项卡中，输入 **SparkDF** 作为名称。

   > [!IMPORTANT]
   > Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误：“数据工厂名称 ‘SparkDF’ 不可用。” 请更改数据工厂名称（例如 yournameSparkDFdate），然后尝试重新创建。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。   
4. 选择要在其中创建数据工厂的 **Azure 订阅** 。
5. 选择一个现有**资源组**或创建一个 Azure 资源组。
6. 选择“固定到仪表板”选项。  
6. 在“新建数据工厂”边栏选项卡中单击“创建”。

   > [!IMPORTANT]
   > 只有订阅/资源组级别的 [数据工厂参与者](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成员才能创建数据工厂实例。
7. 此时可在 Azure 门户的**仪表板**中看到所创建的数据工厂，如下所示：   
8. 成功创建数据工厂后，将看到数据工厂页，其中显示了数据工厂的内容。 如果没有看到数据工厂页面，请在仪表板上单击你的数据工厂的磁贴。

    ![“数据工厂”边栏选项卡](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>创建链接服务
在此步骤中，你将创建两个链接的服务，一个将你的 Spark 群集链接到你的数据工厂，另一个将你的 Azure 存储链接到你的数据工厂。  

#### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
在此步骤中，将 Azure 存储帐户链接到数据工厂。 本演练中你在后面的一个步骤中创建的数据集将引用此链接的服务。 你在下一步骤中定义的 HDInsight 链接的服务也将引用此链接的服务。  

1. 在你的数据工厂的“数据工厂”边栏选项卡中，单击“编写和部署”。 此时将显示“数据工厂编辑器”。
2. 单击“新建数据存储”并选择“Azure 存储”。

   ![新建数据存储 - Azure 存储 - 菜单](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. 你应当会在编辑器中看到用于创建 Azure 存储链接的服务的 **JSON 脚本**。

   ![Azure 存储链接服务](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. 将**帐户名称**和**帐户密钥**替换为你的 Azure 存储帐户的名称和访问密钥。 若要了解如何获取存储访问密钥，请在[管理你的存储帐户](../storage/storage-create-storage-account.md#manage-your-storage-account)中查看有关如何查看、复制和重新生成存储访问密钥的信息。
5. 若要部署链接服务，请单击命令栏上的“部署”。 成功部署链接服务后，“草稿 1”窗口应会消失，左侧树视图中会显示“AzureStorageLinkedService”。

#### <a name="create-hdinsight-linked-service"></a>创建 HDInsight 链接的服务
在此步骤中，你将创建 Azure HDInsight 链接的服务来将你的 HDInsight Spark 群集链接到数据工厂。 HDInsight 群集用来运行本示例中在管道的 Spark 活动中指定的 Spark 程序。  

1. 在工具栏上单击“...更多”，单击“新计算”，然后单击“HDInsight 群集”。

    ![创建 HDInsight 链接的服务](media/data-factory-spark/new-hdinsight-linked-service.png)
2. 将以下代码片段复制并粘贴到“草稿 1”窗口。 在 JSON 编辑器中，执行以下步骤：
    1. 指定 HDInsight Spark 群集的 **URI**。 例如： `https://<sparkclustername>.azurehdinsight.net/`。
    2. 指定有权访问 Spark 群集的**用户**的名称。
    3. 指定该用户的**密码**。
    4. 指定与 HDInsight Spark 群集关联的 **Azure 存储链接的服务**。 在此示例中，该链接的服务为 **AzureStorageLinkedService**。

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark 活动不支持那些使用 Azure Data Lake Store 作为主存储的 HDInsight Spark 群集。
    > - Spark 活动仅支持现有的（你自己的）HDInsight Spark 群集。 它不支持按需 HDInsight 链接的服务。

    有关 HDInsight 链接的服务的详细信息，请参阅 [HDInsight 链接的服务](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)。
3.  若要部署链接服务，请单击命令栏上的“部署”。  

### <a name="create-output-dataset"></a>创建输出数据集
输出数据集驱动计划（每小时、每日等）。 因此，必须为管道中的 Spark 活动指定输出数据集，即使该活动实际上不生成任何输出。 为该活动指定输入数据集是可选的。

1. 在“数据工厂编辑器”中，单击“...更多”（在命令栏上），单击“新建数据集”，然后选择“Azure Blob 存储”。  
2. 将以下代码片段复制并粘贴到“草稿 1”窗口。 此 JSON 片段定义了一个名为 **OutputDataset** 的数据集。 此外，你将指定将结果存储在名为 **adfspark** 的 Blob 容器和名为 **pyFiles/output** 的文件夹中。 如前所述，此数据集是一个虚拟数据集。 此示例中的 Spark 程序不生成任何输出。 **availability** 节指定输出数据集每日生成一次。  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. 若要部署数据集，请单击命令栏上的“部署”。


### <a name="create-pipeline"></a>创建管道
在此步骤中，你将创建一个包含**HDInsightSpark** 活动的管道。 当前，输出数据集驱动计划，因此即使活动并未生成任何输出，也必须创建输出数据集。 如果活动没有任何输入，可以跳过创建输入数据集。 因此，此示例中未指定输入数据集。

1. 在**数据工厂编辑器**中，单击命令栏上的“… 更多”，然后单击“新建管道”。
2. 将 Draft-1 窗口中的脚本替换为以下脚本：

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    请注意以下几点：
    - **type** 属性设置为 **HDInsightSpark**。
    - **rootPath** 设置为 **adfspark\\pyFiles**，其中，adfspark 是 Azure Blob 容器，pyFiles 是该容器中的文件夹。 在此示例中，Azure Blob 存储是与 Spark 群集关联的存储。 可将文件上载到不同的 Azure 存储。 如果这样做，请创建 Azure 存储链接服务，将该存储帐户链接到数据工厂。 然后，将该链接的服务的名称指定为 **sparkJobLinkedService** 属性的值。 请参阅 [Spark 活动属性](#spark-activity-properties)，了解有关 Spark 活动支持的此属性及其他属性的详细信息。  
    - **entryFilePath** 设置为 **test.py**，这是 python 文件。
    - **getDebugInfo** 属性设置为 **Always**，表示始终生成日志文件（无论成功还是失败）。

        > [!IMPORTANT]
        > 在生产环境中，除非你要排查问题，否则我们不建议将此属性设置为 `Always`。
    - **outputs** 节包含一个输出数据集。 必须指定一个输出数据集，即使 spark 程序不会生成任何输出。 输出数据集驱动管道的计划（每小时、每日，等等）。  

        有关 Spark 活动支持的属性的详细信息，请参阅 [Spark 活动属性](#spark-activity-properties)。
3. 若要部署管道，请在命令栏上单击“部署”。

### <a name="monitor-pipeline"></a>监视管道
1. 单击 **X** 关闭“数据工厂编辑器”边栏选项卡以导航回到“数据工厂”主页。 单击“监视和管理”以在另一个选项卡中启动监视应用程序。

    ![“监视和管理”磁贴](media/data-factory-spark/monitor-and-manage-tile.png)
2. 在顶部将“开始时间”筛选器更改为“2/1/2017”，然后单击“应用”。
3. 你应当只会看到一个活动时段，因为在管道的开始时间 (2017-02-01) 与结束时间 (2017-02-02) 之间只有一天。 确认数据切片处于“就绪”状态。

    ![监视管道](media/data-factory-spark/monitor-and-manage-app.png)    
4. 选择该**活动时段**来查看有关活动运行的详细信息。 如果存在错误，则会在右侧的窗格中看到关于该错误的详细信息。

### <a name="verify-the-results"></a>验证结果

1. 通过导航到 https://CLUSTERNAME.azurehdinsight.net/jupyter 为你的 HDInsight Spark 群集启动 **Jupyter notebook**。 还可以启动你的 HDInsight Spark 群集的群集仪表板，然后启动 **Jupyter Notebook**。
2. 单击“新建” -> “PySpark”以启动一个新的 notebook。

    ![Jupyter 新建 notebook](media/data-factory-spark/jupyter-new-book.png)
3. 通过复制/粘贴以下文本并在第二个语句结束时按 **SHIFT + ENTER** 运行以下命令。  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. 确认你看到了来自 hvac 表的数据：  

    ![Jupyter 查询结果](media/data-factory-spark/jupyter-notebook-results.png)

有关详细说明，请参阅[运行 Spark SQL 查询](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md#run-an-interactive-spark-sql-query)部分。 

### <a name="troubleshooting"></a>故障排除
因为你将 **getDebugInfo** 设置为 **Always**，所以你会在 Azure Blob 容器的 **pyFiles** 文件夹中看到一个 **log** 子文件夹。 日志文件夹中的日志文件提供了更多详细信息。 当发生错误时，此日志文件尤其有用。 在生产环境中，你可能希望将其设置为 **Failure**。

要进一步进行故障排除，请执行以下步骤：


1. 导航到 `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`。

    ![YARN UI 应用程序](media/data-factory-spark/yarnui-application.png)  
2. 针对其中的一个运行尝试单击“日志”。

    ![应用程序页面](media/data-factory-spark/yarn-applications.png)
3. 你应当在日志页面中查看更多的错误信息。

    ![日志错误](media/data-factory-spark/yarnui-application-error.png)

以下各节提供有关要在数据工厂中使用 Apache Spark 群集和 Spark 活动的数据工厂实体的信息。

## <a name="spark-activity-properties"></a>Spark 活动属性
下面是包含 Spark 活动的管道的示例 JSON 定义：    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

下表描述了 JSON 定义中使用的 JSON 属性：

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| name | 管道中活动的名称。 | 是 |
| description | 描述活动用途的文本。 | 否 |
| type | 此属性必须设置为 HDInsightSpark。 | 是 |
| linkedServiceName | 运行 Spark 程序的 HDInsight 链接服务的名称。 | 是 |
| rootPath | 包含 Spark 文件的 Azure Blob 容器和文件夹。 文件名称需区分大小写。 | 是 |
| entryFilePath | Spark 代码/包的根文件夹的相对路径 | 是 |
| className | 应用程序的 Java/Spark main 类 | 否 |
| arguments | Spark 程序的命令行参数列表。 | 否 |
| proxyUser | 用于模拟执行 Spark 程序的用户帐户 | 否 |
| sparkConfig | Spark 配置属性。 | 否 |
| getDebugInfo | 指定何时将 Spark 日志文件复制到 HDInsight 群集使用的（或者）sparkJobLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：None。 | 否 |
| sparkJobLinkedService | 用于保存 Spark 作业文件、依赖项和日志的 Azure 存储链接服务。  如果未指定此属性的值，将使用与 HDInsight 群集关联的存储。 | 否 |

## <a name="folder-structure"></a>文件夹结构
与 Pig 和 Hive 活动不同，Spark 活动不支持内联脚本。 与 Pig/Hive 作业相比，Spark 作业的可扩展性更高。 对于 Spark 作业，可以提供多个依赖项，例如 jar 包（放在 java CLASSPATH 中）、python 文件（放在 PYTHONPATH 中）和其他任何文件。

在 HDInsight 链接服务引用的 Azure Blob 存储中创建以下文件夹结构。 然后，将依赖文件上载到 **entryFilePath** 表示的根文件夹中的相应子文件夹。 例如，将 python 文件上载到根文件夹的 pyFiles 子文件夹，将 jar 文件上载到根文件夹的 jars 子文件夹。 在运行时，数据工厂服务需要 Azure Blob 存储中的以下文件夹结构：     

| 路径 | 说明 | 必选 | 类型 |
| ---- | ----------- | -------- | ---- |
| 。    | Spark 作业在存储链接服务中的根路径    | 是 | 文件夹 |
| &lt;用户定义&gt; | 指向 Spark 作业入口文件的路径 | 是 | 文件 |
| ./jars | 此文件夹下的所有文件将上载并放置在群集的 java 类路径中 | 否 | 文件夹 |
| ./pyFiles | 此文件夹下的所有文件将上载并放置在群集的 PYTHONPATH 中 | 否 | 文件夹 |
| ./files | 此文件夹下的所有文件将上载并放置在执行器工作目录中 | 否 | 文件夹 |
| ./archives | 此文件夹下的所有文件未经压缩 | 否 | 文件夹 |
| ./logs | 来自 Spark 群集的日志所存储到的文件夹。| 否 | 文件夹 |

以下示例显示了一个在 HDInsight 链接服务引用的 Azure Blob 存储中包含两个 Spark 作业文件的存储。

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```

