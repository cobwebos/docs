---
title: "构建第一个数据工厂（Azure 门户）| Microsoft Docs"
description: "本教程使用 Azure 门户中的数据工厂编辑器创建一个示例 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 19f9686de9face1e53fc84eac23381eadc9fb5cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-portal"></a>教程：使用 Azure 门户构建第一个 Azure 数据工厂
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-build-your-first-pipeline.md)
> * [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


本文章介绍如何使用 [Azure 门户](https://portal.azure.com/)创建第一个 Azure 数据工厂。 若要使用其他工具/SDK 来完成教程，请从下拉列表中选择一个选项。 

本教程中的管道有一个活动：**HDInsight Hive 活动**。 该活动在 Azure HDInsight 群集上运行 Hive 脚本，通过转换输入数据来生成输出数据。 管道在指定的开始时间和结束时间范围内每月按计划运行一次。 

> [!NOTE]
> 本教程中的数据管道可以转换输入数据，以便生成输出数据。 有关如何使用 Azure 数据工厂复制数据的教程，请参阅[教程：将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
> 
> 一个管道可以有多个活动。 而且，可以通过将一个活动的输出数据集设置为另一个活动的输入数据集，链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[在数据工厂中计划和执行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。

## <a name="prerequisites"></a>先决条件
1. 阅读 [教程概述](data-factory-build-your-first-pipeline.md) ，完成 **先决条件** 步骤。
2. 本文不提供 Azure 数据工厂服务的概念性概述。 有关该服务的详细概述，建议通读 [Introduction to Azure Data Factory](data-factory-introduction.md) （Azure 数据工厂简介）一文。  

## <a name="create-data-factory"></a>创建数据工厂
数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 例如，将数据从源复制到目标数据存储的复制活动，以及运行 Hive 脚本来将输入数据转换为产品输出数据的 HDInsight Hive 活动。 首先，在此步骤中创建数据工厂。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。

   ![“创建”边栏选项卡](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)
3. 在“新建数据工厂”边栏选项卡中，输入 **GetStartedDF** 作为名称。

   ![“新建数据工厂”边栏选项卡](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误：“数据工厂名称 ‘GetStartedDF’ 不可用。” 请更改数据工厂名称（例如 yournameGetStartedDF），并尝试重新创建。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。
   >
   > 数据工厂名称可能在将来被注册为 **DNS** 名称，因此将变成公开可见。
   >
   >
4. 选择要在其中创建数据工厂的 **Azure 订阅** 。
5. 选择现有的 **资源组** ，或创建资源组。 本教程创建名为 **ADFGetStartedRG**的资源组。
6. 选择数据工厂的**位置**。 下拉列表中只显示数据工厂服务支持的区域。
7. 选择“固定到仪表板”。 
8. 在“新建数据工厂”边栏选项卡中单击“创建”。

   > [!IMPORTANT]
   > 只有订阅/资源组级别的 [数据工厂参与者](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) 角色成员才能创建数据工厂实例。
   >
   >
7. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。    

   ![正在创建数据工厂状态](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
8. 祝贺你！ 现已成功创建第一个数据工厂。 成功创建数据工厂后，将看到数据工厂页，其中显示了数据工厂的内容。     

    ![“数据工厂”边栏选项卡](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

创建管道之前，需要创建一些数据工厂项。 首先创建链接服务用于将数据存储/计算机链接到数据存储，然后定义输入和输出数据集来表示链接的数据存储中的输入/输出数据，接下来创建管道，其中的某个活动使用这些数据集。

## <a name="create-linked-services"></a>创建链接服务
在此步骤中，将 Azure 存储帐户和按需 Azure HDInsight 群集链接到数据工厂。 Azure 存储帐户保留本示例中管道的输入和输出数据。 HDInsight 链接服务用于运行本示例中管道活动指定的 Hive 脚本。 识别方案中使用的[数据存储](data-factory-data-movement-activities.md)/[计算服务](data-factory-compute-linked-services.md)，创建链接的服务将这些服务链接到数据工厂。  

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
在此步骤中，将 Azure 存储帐户链接到数据工厂。 本教程使用相同的 Azure 存储帐户来存储输入/输出数据和 HQL 脚本文件。

1. 在 **GetStartedDF** 的“数据工厂”边栏选项卡中，单击“编写和部署”。 此时会显示“数据工厂编辑器”。

   ![“编写和部署”磁贴](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2. 单击“新建数据存储”并选择“Azure 存储”。

   ![新建数据存储 - Azure 存储 - 菜单](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)
3. 在编辑器中，应会看到用于创建 Azure 存储链接服务的 JSON 脚本。

   ![Azure 存储链接服务](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. 将**帐户名**替换为 Azure 存储帐户名，将**帐户密钥**替换为 Azure 存储帐户的访问密钥。 要了解如何获取存储访问密钥，请在[管理存储帐户](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)中查看有关如何查看、复制和重新生成存储访问密钥的信息。
5. 单击命令栏上的“部署”，部署链接服务。

    ![部署按钮](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   成功部署链接服务后，“草稿 1”窗口应会消失，左侧树视图中会显示“AzureStorageLinkedService”。

    ![菜单中的 Azure 存储链接服务](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-azure-hdinsight-linked-service"></a>创建 Azure HDInsight 链接服务
在此步骤中，将按需 HDInsight 群集链接到数据工厂。 HDInsight 群集在运行时自动创建，在处理完成之后删除，并且会空闲指定的一段时间。

1. 在“数据工厂编辑器”中，单击“...更多”，单击“新建计算”，并选择“按需 HDInsight 群集”。

    ![新建计算](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. 将以下代码片段复制并粘贴到“草稿 1”窗口。 该 JSON 代码片段描述用于创建按需 HDInsight 群集的属性。

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    下表提供了代码片段中使用的 JSON 属性的描述：

   | 属性 | 说明 |
   |:--- |:--- |
   | ClusterSize |指定 HDInsight 群集的大小。 |
   | TimeToLive | 指定 HDInsight 群集在被删除之前的空闲时间。 |
   | linkedServiceName | 指定用于存储 HDInsight 生成的日志的存储帐户。 |

    请注意以下几点：

   * 数据工厂使用 JSON 创建**基于 Linux** 的 HDInsight 群集。 有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。
   * 可以使用 **自己的 HDInsight 群集** ，而不使用按需 HDInsight 群集。 有关详细信息，请参阅 [HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) （HDInsight 链接服务）。
   * HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时，都会创建 HDInsight 群集。 处理完成后会自动删除该群集。

       随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。 使用 [Microsoft 存储资源管理器](http://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。

     有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。
3. 单击命令栏上的“部署”，部署链接服务。

    ![部署按需 HDInsight 链接服务](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)
4. 确认左侧树视图中出现了 **AzureStorageLinkedService** 和 **HDInsightOnDemandLinkedService**。

    ![包含链接服务的树视图](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>创建数据集
此步骤创建数据集来代表 Hive 处理的输入和输出数据。 这些数据集引用前面在本教程中创建的 **AzureStorageLinkedService** 。 链接服务指向 Azure 存储帐户，数据集指定用于保存输入和输出数据的存储中的容器、文件夹和文件名。   

### <a name="create-input-dataset"></a>创建输入数据集
1. 在“数据工厂编辑器”中，单击“...更多”（在命令栏上），单击“新建数据集”，并选择“Azure Blob 存储”。

    ![新建数据集](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. 将以下代码片段复制并粘贴到“草稿 1”窗口。 在 JSON 代码片段中，创建名为 **AzureBlobInput** 的数据集，表示管道中活动的输入数据。 此外，指定将输入数据放在名为 **adfgetstarted** 的 Blob 容器及名为 **inputdata** 的文件夹中

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    下表提供了代码片段中使用的 JSON 属性的描述：

   | 属性 | 说明 |
   |:--- |:--- |
   | type |type 属性设置为 **AzureBlob**，因为数据驻留在 Azure Blob 存储中。 |
   | linkedServiceName |引用前面创建的 **AzureStorageLinkedService**。 |
   | folderPath | 指定 Blob **容器**以及包含输入 Blob 的**文件夹**。 | 
   | fileName |此属性是可选的。 如果省略此属性，将选择 folderPath 中的所有文件。 在本教程中，只处理 **input.log**。 |
   | type |日志文件采用文本格式，因此这里使用 **TextFormat**。 |
   | columnDelimiter |日志文件中的列以**逗号 (`,`)** 分隔 |
   | frequency/interval |frequency 设置为 **Month**，interval 为 **1**，表示每月获取一次输入切片。 |
   | external | 如果输入数据不是由该管道生成的，此属性设置为 **true**。 在本教程中，input.log 文件不是由该管道生成的，因此将此属性设置为 true。 |

    有关这些 JSON 属性的详细信息，请参阅 [Azure Blob 连接器](data-factory-azure-blob-connector.md#dataset-properties)一文。
3. 单击命令栏上的“部署”，部署新建的数据集。 左侧树视图中应会出现该数据集。

### <a name="create-output-dataset"></a>创建输出数据集
现在，创建输出数据集来表示 Azure Blob 存储中存储的输出数据。

1. 在“数据工厂编辑器”中，单击“...更多”（在命令栏上），单击“新建数据集”，并选择“Azure Blob 存储”。  
2. 将以下代码片段复制并粘贴到“草稿 1”窗口。 在 JSON 代码片段中，创建名为 **AzureBlobOutput**的数据集，指定 Hive 脚本生成的数据结构。 此外，指定将结果存储在名为 **adfgetstarted** 的 Blob 容器及名为 **partitioneddata** 的文件夹中。 **availability** 节指定每月生成输出数据集一次。

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    有关这些属性的描述，请参阅 **创建输入数据集** 部分。 由于数据集是由数据工厂服务生成的，因此未在输出数据集上设置外部属性。
3. 单击命令栏上的“部署”，部署新建的数据集。
4. 验证是否已成功创建数据集。

    ![包含链接服务的树视图](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-pipeline"></a>创建管道
此步骤创建第一个具有 **HDInsightHive** 活动的管道。 每月获取输入切片（frequency：Month；interval：1），每月生成输出切片，活动的计划程序属性也设置为每月。 输出数据集的设置必须与活动计划程序匹配。 当前，输出数据集驱动计划，因此即使活动并未生成任何输出，也必须创建输出数据集。 如果活动没有任何输入，可以跳过创建输入数据集。 本部分结尾说明以下 JSON 中使用的属性。

1. 在“数据工厂编辑器”中，单击**省略号** (…)（更多命令），并单击“新建管道”。

    ![新建管道按钮](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. 将以下代码片段复制并粘贴到“草稿 1”窗口。

   > [!IMPORTANT]
   > 在 JSON 中，将 **storageaccountname** 替换为存储帐户名。
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    在 JSON 代码片段中创建一个管道，其中包括在 HDInsight 群集上使用 Hive 处理数据的单个活动。

    Hive 脚本文件 **partitionweblogs.hql** 存储在 Azure 存储帐户（由 scriptLinkedService 指定，名为 **AzureStorageLinkedService**）中，以及 **adfgetstarted** 容器的 **script** 文件夹中。

    **defines** 节用于指定运行时设置，这些设置将作为 Hive 配置值（例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable}）传递到 Hive 脚本。

    管道的 **start** 和 **end** 属性指定管道的活动期限。

    在活动 JSON 中，指定 Hive 脚本要在通过 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的计算资源上运行。

   > [!NOTE]
   > 有关本示例中使用的 JSON 属性的详细信息，请参阅 [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md)（Azure 数据工厂中的管道和活动）中的“Pipeline JSON”（管道 JSON）。
   >
   >
3. 确认符合以下条件：

   1. **input.log** 文件在 Azure Blob 存储中 **adfgetstarted** 容器的 **inputdata** 文件夹中
   2. **partitionweblogs.hql** 文件在 Azure Blob 存储中 **adfgetstarted** 容器的 **script** 文件夹中。 如果没有看到这些文件，请完成[教程概述](data-factory-build-your-first-pipeline.md)中的先决条件步骤。
   3. 确认已在管道 JSON 中将 **storageaccountname** 替换为存储帐户名。
4. 单击命令栏上的“部署”来部署管道。 由于 **start** 和 **end** 时间设置为过去的时间，**isPaused** 设置为 false，因此管道（管道中的活动）在部署后立即运行。
5. 确认树视图中显示了该管道。

    ![包含管道的树视图](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
6. 恭喜，现已成功创建第一个管道！

## <a name="monitor-pipeline"></a>监视管道
### <a name="monitor-pipeline-using-diagram-view"></a>使用图示视图监视管道
1. 单击“X”关闭“数据工厂编辑器”边栏选项卡，导航回到“数据工厂”边栏选项卡，并单击“图示”。

    ![图示磁贴](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
2. 在“图示视图”中，可以看到管道的概述，以及本教程中使用的数据集。

    ![图示视图](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)
3. 要查看管道中的所有活动，请右键单击图示中的管道，并单击“打开管道”。

    ![“打开管道”菜单](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
4. 确认管道中显示了 HDInsightHive 活动。

    ![“打开管道”视图](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    若要导航回到上一个视图，请单击顶部痕迹导航菜单中的“数据工厂”。
5. 在“图示视图”中，双击数据集 **AzureBlobInput**。 确认切片处于“就绪”状态。 可能需要几分钟时间，切片才显示为“就绪”状态。 如果一段时间后未显示此状态，请检查是否已将输入文件 (input.log) 放置在正确的容器 (adfgetstarted) 和文件夹 (inputdata) 中。

   ![输入切片处于就绪状态](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
6. 单击“X”关闭“AzureBlobInput”边栏选项卡。
7. 在“图示视图”中，双击数据集 **AzureBlobOutput**。 此时会显示当前正在处理的切片。

   ![数据集](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
8. 处理完成后，可以看到切片处于“就绪”状态。

   ![数据集](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > 创建按需 HDInsight 群集通常需要一段时间（大约 20 分钟）。 因此，预期管道需要花费**大约 30 分钟**来处理切片。
   >
   >

9. 当切片处于“就绪”状态时，检查 Blob 存储中 **adfgetstarted** 容器内 **partitioneddata** 文件夹的输出数据。  

   ![输出数据](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)
10. 单击切片可在“数据切片”边栏选项卡中查看其详细信息。

   ![数据切片详细信息](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)  
11. 单击“活动运行列表”中的某个活动运行可在“活动运行详细信息”窗口中查看有关该活动运行（在本例中为 Hive 活动）的详细信息。   

   ![活动运行详细信息](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   在日志文件中，可以看到已执行的 Hive 查询和状态信息。 可以使用这些日志来排查任何问题。
   有关详细信息，请参阅 [Monitor and manage pipelines using Azure portal blades](data-factory-monitor-manage-pipelines.md) （使用 Azure 门户边栏选项卡监视和管理管道）。

> [!IMPORTANT]
> 成功处理切片后，会删除输入文件。 因此，如果想要重新运行切片或重新学习本教程，请将输入文件 (input.log) 上传到 adfgetstarted 容器的 inputdata 文件夹中。
>
>

### <a name="monitor-pipeline-using-monitor--manage-app"></a>使用“监视和管理”应用来监视管道
还可以使用“监视和管理”应用程序来监视管道。 有关使用此应用程序的详细信息，请参阅 [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md)（使用监视和管理应用程序来监视和管理 Azure 数据工厂管道）。

1. 在数据工厂的主页上单击“监视和管理”磁贴。

    ![“监视和管理”磁贴](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)
2. 此时应出现“监视和管理应用程序”。 更改“开始时间”和“结束时间”，使之与管道的开始时间和结束时间匹配，然后单击“应用”。

    ![“监视和管理”应用](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)
3. 在“活动窗口”列表中选择一个活动窗口查看其详细信息。

    ![活动窗口详细信息](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>摘要
本教程通过在 HDInsight hadoop 群集上运行 Hive 脚本，创建了一个 Azure 数据工厂来处理数据。 在 Azure 门户中使用数据工厂编辑器执行了以下步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建了两个 **链接服务**：
   1. **Azure 存储** 链接服务，用于将保存输入/输出文件的 Azure Blob 存储链接到数据工厂。
   2. **Azure HDInsight** 按需链接服务，用于将 HDInsight Hadoop 按需群集链接到数据工厂。 Azure 数据工厂实时创建 HDInsight Hadoop 群集来处理输入数据以及生成输出数据。
3. 创建了两个 **数据集**，描述管道中 HDInsight Hive 活动的输入和输出数据。
4. 创建了包含 **HDInsight Hive** 活动的**管道**。

## <a name="next-steps"></a>后续步骤
本文创建了可在按需 HDInsight 群集上运行 Hive 脚本、包含转换活动（HDInsight 活动）的管道。 若要了解如何使用复制活动将数据从 Azure Blob 复制到 Azure SQL，请参阅 [Tutorial: Copy data from an Azure blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（教程：将数据从 Azure Blob 复制到 Azure SQL）。

## <a name="see-also"></a>另请参阅
| 主题 | 说明 |
|:--- |:--- |
| [管道](data-factory-create-pipelines.md) |帮助你了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造端对端数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [计划和执行](data-factory-scheduling-and-execution.md) |本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
| [使用监视应用监视和管理管道](data-factory-monitor-manage-app.md) |本文介绍如何使用监视和管理应用来监视、管理和调试管道。 |
