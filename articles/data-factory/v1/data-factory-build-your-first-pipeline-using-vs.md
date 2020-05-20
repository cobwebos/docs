---
title: 构建第一个数据工厂 (Visual Studio)
description: 本教程使用 Visual Studio 创建一个示例 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: vs-azure
ms.date: 01/22/2018
ms.openlocfilehash: eb9c21bf1972304da688586da9ccabe5063fa112
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75438976"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>教程：使用 Visual Studio 创建数据工厂
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [概述与先决条件](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [资源管理器模板](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[快速入门：使用 Azure 数据工厂创建数据工厂](../quickstart-create-data-factory-dot-net.md)。

本教程演示如何使用 Visual Studio 创建 Azure 数据工厂。 你可以使用数据工厂项目模板创建 Visual Studio 项目，以 JSON 格式定义数据工厂实体（链接服务、数据集和管道），并将这些实体发布/部署到云。 

本教程中的管道有一个活动：**HDInsight Hive 活动**。 该活动在 Azure HDInsight 群集上运行 Hive 脚本，通过转换输入数据来生成输出数据。 管道在指定的开始时间和结束时间范围内每月按计划运行一次。 

> [!NOTE]
> 本教程不介绍如何通过 Azure 数据工厂复制数据。 有关如何使用 Azure 数据工厂复制数据的教程，请参阅[教程：将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
> 
> 一个管道可以有多个活动。 而且，可以通过将一个活动的输出数据集设置为另一个活动的输入数据集，链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[在数据工厂中计划和执行](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)。


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>演练：创建和发布数据工厂实体
下面是本演练中要执行的步骤：

1. 创建两个链接服务：**AzureStorageLinkedService1** 和 **HDInsightOnDemandLinkedService1**。 
   
    在本教程中，Hive 活动的输入和输出数据均位于同一 Azure Blob 存储中。 可以使用按需 HDInsight 群集处理现有的输入数据，以便生成输出数据。 当输入数据已准备就绪，可以进行处理时，Azure 数据工厂就会在运行时自动创建按需 HDInsight 群集。 需将数据存储或计算链接到数据工厂，以便数据工厂服务能够在运行时连接到它们。 因此，请使用 AzureStorageLinkedService1 将 Azure 存储帐户链接到数据工厂，并使用 HDInsightOnDemandLinkedService1 链接按需 HDInsight 群集。 发布时，请为要创建的数据工厂指定一个名称，或者指定现有数据工厂的名称。  
2. 创建两个数据集：**InputDataset** 和 **OutputDataset**，分别代表存储在 Azure Blob 存储中的输入/输出数据。 
   
    这两个数据集定义是指在上一步创建的 Azure 存储链接服务。 对于 InputDataset，请指定 Blob 容器 (adfgetstarted) 和文件夹 (inptutdata)，后者包含 Blob 和输入数据。 对于 OutputDataset，请指定 Blob 容器 (adfgetstarted) 和文件夹 (partitioneddata)，后者包含输出数据。 还可指定其他属性，例如结构、可用性和策略。
3. 创建名为 **MyFirstPipeline** 的管道。 
  
    在本演练中，该管道只有一个活动：**HDInsight Hive 活动**。 该活动在按需 HDInsight 群集上运行 Hive 脚本，通过转换输入数据来生成输出数据。 若要详细了解 Hive 活动，请参阅 [Hive 活动](data-factory-hive-activity.md) 
4. 创建名为 **DataFactoryUsingVS** 的数据工厂。 部署数据工厂和所有数据工厂实体（链接服务、表和管道）。
5. 发布后，请使用 Azure 门户边栏选项卡以及监视和管理应用来监视管道。 
  
### <a name="prerequisites"></a>必备条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. 阅读 [教程概述](data-factory-build-your-first-pipeline.md) ，完成 **先决条件** 步骤。 也可在顶部的下拉列表中选择“概述与先决条件”选项，以便切换到该文章。  完成先决条件以后，在下拉列表中选择“Visual Studio”选项即可切换回本文。 
2. 只有订阅/资源组级别的 [数据工厂参与者](../../role-based-access-control/built-in-roles.md#data-factory-contributor) 角色成员才能创建数据工厂实例。  
3. 必须在计算机上安装了以下软件：
   * Visual Studio 2013 或 Visual Studio 2015
   * 下载用于 Visual Studio 2013 或 Visual Studio 2015 的 Azure SDK。 导航到 [Azure 下载页](https://azure.microsoft.com/downloads/)，在“.NET”部分中单击“VS 2013”或“VS 2015”。   
   * 下载用于 Visual Studio 的最新 Azure 数据工厂插件：[VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) 或 [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。 还可通过执行以下步骤更新插件：在菜单上，依次单击“工具” -> “扩展和更新” -> “联机” -> “Visual Studio 库” -> “适用于 Visual Studio 的 Microsoft Azure 数据工厂工具” -> “更新”。

现在，使用 Visual Studio 创建 Azure 数据工厂。

### <a name="create-visual-studio-project"></a>创建 Visual Studio 项目
1. 启动 **Visual Studio 2013** 或 **Visual Studio 2015**。 单击“文件”，指向“新建”并单击“项目”。    将显示“新建项目”  对话框。  
2. 在“新建项目”对话框中，选择“DataFactory”模板，并单击“空数据工厂项目”。      

    ![“新建项目”对话框](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. 输入项目的**名称**、**位置**以及**解决方案**的名称，并单击“确定”。 

    ![解决方案资源管理器](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>创建链接服务
在此步骤中，将创建两项链接服务：**Azure 存储**和**按需 HDInsight**。 

Azure 存储链接服务通过提供连接信息将 Azure 存储帐户链接到数据工厂。 数据工厂在运行时使用链接服务设置中的连接字符串连接到 Azure 存储。 该存储保存管道的输入和输出数据，以及 Hive 活动所使用的 Hive 脚本文件。 

有了按需 HDInsight 链接服务，则当输入数据已准备就绪，可以进行处理时，就会在运行时自动创建 HDInsight 群集。 该群集在处理完毕并空闲指定的时间后删除。 

> [!NOTE]
> 可以在发布数据工厂解决方案时，通过指定名称和设置来创建数据工厂。

#### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务
1. 在解决方案资源管理器中，右键单击“链接服务”，指向“添加”，并单击“新建项”。         
2. 在“添加新项”对话框中，从列表中选择“Azure 存储链接服务”，并单击“添加”。   
    ![Azure 存储链接服务](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. 将 `<accountname>` 和 `<accountkey>` 替换为 Azure 存储帐户的名称和密钥。 若要了解如何获取存储访问密钥，请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。
    ![Azure 存储链接服务](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. 保存 **AzureStorageLinkedService1.json** 文件。

#### <a name="create-azure-hdinsight-linked-service"></a>创建 Azure HDInsight 链接服务
1. 在“解决方案资源管理器”中，右键单击“链接服务”，指向“添加”，并单击“新建项”。    
2. 选择“HDInsight 按需链接服务”，并单击“添加”。  
3. 将 **JSON** 替换为以下 JSON：

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    下表提供了代码片段中使用的 JSON 属性的描述：

    properties | 说明
    -------- | ----------- 
    clusterSize | 指定 HDInsight Hadoop 群集的大小。
    timeToLive | 指定 HDInsight 群集在被删除之前的空闲时间。
    linkedServiceName | 指定一个存储帐户，用于存储 HDInsight Hadoop 群集生成的日志。 

    > [!IMPORTANT]
    > HDInsight 群集在 Blob 存储 (linkedServiceName) 中创建**默认容器**，该存储是你在 JSON 中指定的。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非存在现有的实时群集 (timeToLive)，否则每次处理切片时，都会创建 HDInsight 群集。 处理完成后会自动删除该群集。
    > 
    > 随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循 `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp` 模式。 使用 [Microsoft 存储资源管理器](https://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。

    有关 JSON 属性的详细信息，请参阅[计算链接服务](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)一文。 
4. 保存 **HDInsightOnDemandLinkedService1.json** 文件。

### <a name="create-datasets"></a>创建数据集
此步骤创建数据集来代表 Hive 处理的输入和输出数据。 这些数据集引用前面在本教程中创建的 **AzureStorageLinkedService1** 。 链接服务指向 Azure 存储帐户，数据集指定用于保存输入和输出数据的存储中的容器、文件夹和文件名。   

#### <a name="create-input-dataset"></a>创建输入数据集
1. 在“解决方案资源管理器”中，右键单击“表”，指向“添加”，并单击“新建项”。    
2. 从列表中选择“Azure Blob”，将文件名更改为 **InputDataSet.json**，并单击“添加”。
3. 在编辑器中将 **JSON** 替换为以下 JSON 代码片段：

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    该 JSON 代码片段定义名为 **AzureBlobInput** 的数据集，表示管道中 Hive 活动的输入数据。 请指定将输入数据放在名为 `adfgetstarted` 的 Blob 容器以及名为 `inputdata` 的文件夹中。

    下表提供了代码片段中使用的 JSON 属性的描述：

    properties | 说明 |
    -------- | ----------- |
    type |type 属性设置为 **AzureBlob**，因为数据驻留在 Azure Blob 存储中。
    linkedServiceName | 表示前面创建的 AzureStorageLinkedService1。
    fileName |此属性是可选的。 如果省略此属性，将选择 folderPath 中的所有文件。 在这种情况下，只处理 input.log。
    type | 日志文件采用文本格式，因此这里使用 TextFormat。 |
    columnDelimiter | 日志文件中的列以逗号 (`,`) 分隔
    frequency/interval | frequency 设置为 Month，interval 为 1，表示每月获取输入切片。
    external | 如果活动的输入数据不是由管道生成的，此属性设置为 true。 仅在输入数据集上指定此属性。 对于第一个活动的输入数据集，请始终将其设置为 true。
4. 保存 **InputDataset.json** 文件。

#### <a name="create-output-dataset"></a>创建输出数据集
现在，请创建一个输出数据集，用来表示 Azure Blob 存储中存储的输出数据。

1. 在“解决方案资源管理器”中，右键单击“表”，指向“添加”，并单击“新建项”。    
2. 从列表中选择“Azure Blob”，将文件名更改为 **OutputDataset.json**，并单击“添加”。
3. 在编辑器中将 **JSON** 替换为以下 JSON：
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    该 JSON 代码片段定义名为 **AzureBlobOutput** 的数据集，表示管道中 Hive 活动生成的输出数据。 可指定将 Hive 活动生成的输出数据置于名为 `adfgetstarted` 的 Blob 容器以及名为 `partitioneddata` 的文件夹中。 
    
    **availability** 节指定每月生成输出数据集一次。 输出数据集驱动管道的计划。 管道每月在开始和结束时间范围内运行。 

    有关这些属性的描述，请参阅 **创建输入数据集** 部分。 由于数据集是由管道生成的，因此未在输出数据集上设置外部属性。
4. 保存 **OutputDataset.json** 文件。

### <a name="create-pipeline"></a>创建管道
目前已创建 Azure 存储链接服务，以及输入和输出数据集。 现在，请创建 **HDInsightHive** 活动的管道。 Hive 活动的“输入”  设置为 **AzureBlobInput**，“输出”  设置为 **AzureBlobOutput**。 输入数据集的切片每月都会提供（频率：每月，间隔：1），输出切片也是每月都会生成。 

1. 在“解决方案资源管理器”中，右键单击“管道”，指向“添加”，并单击“新建项”。    
2. 从列表中选择“Hive 转换管道”，并单击“添加”。  
3. 将 **JSON** 替换为以下代码片段：

    > [!IMPORTANT]
    > 将 `<storageaccountname>` 替换为存储帐户的名称。

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
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
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > 将 `<storageaccountname>` 替换为存储帐户的名称。

    JSON 代码片段定义的管道包含单个活动（Hive 活动）。 该活动运行一个 Hive 脚本，用于在按需 HDInsight 群集上处理输入数据，以便生成输出数据。 在管道 JSON 的“活动”部分，只在数组中看到一个活动，其类型设置为 **HDInsightHive**。 

    在特定于 HDInsight Hive 活动的类型属性中，请指定包含 Hive 脚本文件的 Azure 存储链接服务、脚本文件的路径，以及脚本文件的参数。 

    Hive 脚本文件 **partitionweblogs.hql** 存储在 Azure 存储帐户（由 scriptLinkedService 指定）中，以及 `adfgetstarted` 容器的 `script` 文件夹中。

    `defines` 节用于指定运行时设置，这些设置将作为 Hive 配置值（例如 `${hiveconf:inputtable}`、`${hiveconf:partitionedtable})`）传递给 Hive 脚本。

    管道的 **start** 和 **end** 属性指定管道的活动期限。 已将数据集配置为按月生成，因此，只能通过管道生成切片一次（因为月的开始和结束日期相同）。

    在活动 JSON 中，指定 Hive 脚本要在通过 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的计算资源上运行。
4. 保存 **HiveActivity1.json** 文件。

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>将 partitionweblogs.hql 和 input.log 添加为依赖项
1. 在“解决方案资源管理器”窗口中右键单击“依赖项”，指向“添加”，并单击“现有项”。      
2. 导航到 **C:\ADFGettingStarted**，选择 **partitionweblogs.hql** 和 **input.log** 文件，并单击“添加”。  已根据[教程概述](data-factory-build-your-first-pipeline.md)的部分先决条件创建上述两个文件。

在下一步骤中发布解决方案时，请将 **partitionweblogs.hql** 文件上传到 `adfgetstarted` Blob 容器中的 **script** 文件夹。   

### <a name="publishdeploy-data-factory-entities"></a>发布/部署数据工厂实体
在此步骤中，请将项目中的数据工厂实体（链接服务、数据集和管道）发布到 Azure 数据工厂服务。 在发布过程中，请指定数据工厂的名称。 

1. 在“解决方案资源管理器”中，右键单击该项目，并单击“发布”  。
2. 如果显示“登录到 Microsoft 帐户”对话框，请输入拥有 Azure 订阅的帐户凭据，并单击“登录”。  
3. 应该会看到以下对话框：

   ![“发布”对话框](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. 在“配置数据工厂”  页，按照以下步骤操作：

    ![发布 - 新数据工厂设置](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. 选择“新建数据工厂”  选项。
   2. 输入数据工厂的唯一 **名称** 。 例如：**DataFactoryUsingVS09152016**。 该名称必须全局唯一。
   3. 为“订阅”  字段选择正确的订阅。 
        > [!IMPORTANT]
        > 如果未看到任何订阅，请确保使用属于订阅管理员或共同管理员的帐户登录。
   4. 为要创建的数据工厂选择 **资源组** 。
   5. 为数据工厂选择 **区域** 。
   6. 单击“下一步”切换到“发布项”页。   （如果“下一步”按钮已禁用，请按 **TAB** 移出“名称”字段）。 

      > [!IMPORTANT]
      > 如果在发布时收到错误：“数据工厂名称 ‘DataFactoryUsingVS’ 不可用”，请更改名称（例如 yournameDataFactoryUsingVS）。  有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。   
1. 在“发布项”页上，确保已选择所有数据工厂实体，并单击“下一步”切换到“摘要”页。   

    ![发布项页](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. 查看摘要，单击“下一步”，启动部署过程并查看“部署状态”。  

    ![“摘要”页](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. 在“部署状态”  页上，应看到部署过程的状态。 部署完成后，单击“完成”。

重要注意事项：

- 如果收到错误：“该订阅未注册，无法使用命名空间 Microsoft.DataFactory”，请执行下列操作之一，尝试再次发布： 
    - 在 Azure PowerShell 中运行以下命令，注册数据工厂提供程序。
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        可通过运行以下命令来确认数据工厂提供程序是否已注册。

        ```powershell
        Get-AzResourceProvider
        ```
    - 使用 Azure 订阅登录到 [Azure 门户](https://portal.azure.com) ，并导航到“数据工厂”边栏选项卡，或在 Azure 门户中创建数据工厂。 此操作会自动注册提供程序。
- 数据工厂名称可能在将来被注册为 DNS 名称，因此将公开可见。
- 只有 Azure 订阅的管理员或共同管理员才可以创建数据工厂实例

### <a name="monitor-pipeline"></a>监视管道
此步骤使用数据工厂的“图示视图”来监视管道。 

#### <a name="monitor-pipeline-using-diagram-view"></a>使用图示视图监视管道
1. 登录到 [Azure 门户](https://portal.azure.com/)，执行以下步骤：
   1. 单击“更多服务”，并单击“数据工厂”。  
       
        ![浏览数据工厂](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. 从数据工厂列表中选择数据工厂的名称（例如： **DataFactoryUsingVS09152016**）。
   
       ![选择数据工厂](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. 在数据工厂的主页中单击“图示”。 

    ![图示磁贴](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. 在“图示视图”中，可以看到管道的概述，以及本教程中使用的数据集。

    ![图示视图](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. 要查看管道中的所有活动，请右键单击图示中的管道，并单击“打开管道”。

    ![“打开管道”菜单](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. 确认管道中显示了 HDInsightHive 活动。

    ![“打开管道”视图](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    若要导航回到上一个视图，请单击顶部痕迹导航菜单中的“数据工厂”。 
6. 在“图示视图”中，双击数据集 **AzureBlobInput**。  确认切片处于“就绪”状态。 可能需要几分钟时间，切片才显示为“就绪”状态。 如果一段时间后未显示此状态，请检查是否已将输入文件 (input.log) 放置在正确的容器 (`adfgetstarted`) 和文件夹 (`inputdata`) 中。 另外，请确保将输入数据集的 **external** 属性设置为 **true**。 

   ![输入切片处于就绪状态](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. 单击“X”关闭“AzureBlobInput”边栏选项卡。  
8. 在“图示视图”中，双击数据集 **AzureBlobOutput**。 此时会显示当前正在处理的切片。

   ![数据集](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. 处理完成后，可以看到切片处于“就绪”状态。 

   > [!IMPORTANT]
   > 创建按需 HDInsight 群集通常需要一段时间（大约 20 分钟）。 因此，预期管道需要花费 **大约 30 分钟** 来处理切片。  
   
    ![数据集](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. 当切片处于“就绪”状态时，检查 Blob 存储中 `adfgetstarted` 容器内 `partitioneddata` 文件夹的输出数据。  

    ![输出数据](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. 单击切片可在“数据切片”边栏选项卡中查看其详细信息。 

    ![数据切片详细信息](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. 单击“活动运行列表”中的某个活动运行可在“活动运行详细信息”窗口中查看有关该活动运行（在本例中为 Hive 活动）的详细信息。   
  
    ![活动运行详细信息](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    在日志文件中，可以看到已执行的 Hive 查询和状态信息。 可以使用这些日志来排查任何问题。  

有关如何使用 Azure 门户监视本教程中所创建管道和数据集的说明，请参阅 [Monitor datasets and pipeline](data-factory-monitor-manage-pipelines.md) （监视数据集和管道）。

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>使用“监视和管理”应用来监视管道
还可以使用“监视和管理”应用程序来监视管道。 有关使用此应用程序的详细信息，请参阅 [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App](data-factory-monitor-manage-app.md)（使用监视和管理应用程序来监视和管理 Azure 数据工厂管道）。

1. 单击“监视和管理”磁贴。

    ![“监视和管理”磁贴](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. 此时应出现“监视和管理应用程序”。 更改“开始时间”和“结束时间”，使之与管道的开始时间 (04-01-2016 12:00 AM) 和结束时间 (04-02-2016 12:00 AM) 匹配，并单击“应用”。   

    ![“监视和管理”应用](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. 要查看某个活动窗口的详细信息，请在“活动窗口”列表中选择该窗口，即可查看其详细信息。 
    ![活动窗口详细信息](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> 成功处理切片后，会删除输入文件。 因此，如果想要重新运行切片或重新学习本教程，请将输入文件 (input.log) 上传到 `adfgetstarted` 容器的 `inputdata` 文件夹中。

### <a name="additional-notes"></a>附加说明
- 数据工厂可以包含一个或多个数据管道。 管道可以包含一个或多个活动。 例如，将数据从源复制到目标数据存储的复制活动，以及运行 Hive 脚本来转换输入数据的 HDInsight Hive 活动。 有关复制活动支持的所有源和接收器，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 有关数据工厂支持的计算服务列表，请参阅[计算链接的服务](data-factory-compute-linked-services.md)。
- 链接服务将数据存储区或计算服务链接到 Azure 数据工厂。 有关复制活动支持的所有源和接收器，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)。 请参阅[计算链接服务](data-factory-compute-linked-services.md)，了解数据工厂支持的计算服务的列表，以及可在这些服务上运行的[转换活动](data-factory-data-transformation-activities.md)。
- 请参阅[将数据移出/移入 Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service)，详细了解在 Azure 存储链接服务定义中使用的 JSON 属性。
- 可以使用自己的 HDInsight 群集，而不使用按需 HDInsight 群集。 有关详细信息，请参阅 [Compute Linked Services](data-factory-compute-linked-services.md) （计算链接服务）。
-  数据工厂使用前面的 JSON 创建**基于 Linux** 的 HDInsight 群集。 有关详细信息，请参阅 [On-demand HDInsight Linked Service](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) （按需 HDInsight 链接服务）。
- HDInsight 群集在 Blob 存储 (linkedServiceName) 中创建**默认容器**，该存储是你在 JSON 中指定的。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非存在现有的实时群集 (timeToLive)，否则每次处理切片时，都会创建 HDInsight 群集。 处理完成后会自动删除该群集。
    
    随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循 `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 模式。 使用 [Microsoft 存储资源管理器](https://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。
- 当前，输出数据集驱动计划，因此即使活动并未生成任何输出，也必须创建输出数据集。 如果活动没有任何输入，可以跳过创建输入数据集。 
- 本教程不介绍如何通过 Azure 数据工厂复制数据。 有关如何使用 Azure 数据工厂复制数据的教程，请参阅[教程：将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="use-server-explorer-to-view-data-factories"></a>使用“服务器资源管理器”查看数据工厂
1. 在 **Visual Studio** 中，在菜单上单击“视图”，并单击“服务器资源管理器”。  
2. 在“服务器资源管理器”窗口中，依次展开“Azure”和“数据工厂”。   如果看到“登录到 Visual Studio”，请输入与 Azure 订阅关联的**帐户**，并单击“继续”。 输入**密码**，并单击“登录”。  Visual Studio 尝试获取有关订阅中所有 Azure 数据工厂的信息。 可在“数据工厂任务列表”窗口中查看此操作的状态。 

    ![服务器资源管理器](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. 可右键单击数据工厂，并选择“将数据工厂导出到新项目”，创建基于现有数据工厂的 Visual Studio 项目。 

    ![导出数据工厂](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>更新适用于 Visual Studio 的数据工厂工具
若要更新适用于 Visual Studio 的 Azure 数据工厂工具，请执行以下步骤：

1. 在菜单中单击“工具”，并选择“扩展和更新”。  
2. 在左窗格中选择“更新”，并选择“Visual Studio 库”。  
3. 选择“用于 Visual Studio 的 Azure 数据工厂工具”，并单击“更新”。   如果未看到此项，说明已有此工具的最新版本。

## <a name="use-configuration-files"></a>使用配置文件
可以在 Visual Studio 中使用配置文件，以不同的方式为每个环境配置链接服务/表/管道的属性。

针对 Azure 存储链接服务，请考虑以下 JSON 定义。 根据部署数据工厂实体的环境（开发/测试/生产），为 accountname 和 accountkey 指定具有不同值的 **connectionString** 。 可以针对每个环境使用不同的配置文件来实现此行为。

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>添加配置文件
执行以下步骤，为每个环境添加配置文件：   

1. 在 Visual Studio 解决方案中右键单击数据工厂项目，指向“添加”，并单击“添加项”。  
2. 在左侧的已安装模板列表中选择“配置”，选择“配置文件”，输入配置文件的**名称**，并单击“添加”。

    ![添加配置文件](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. 使用以下格式添加配置参数及其值：

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure sql server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    此示例配置 Azure 存储链接服务和 Azure SQL 链接服务的 connectionString 属性。 请注意，指定名称的语法是 [JsonPath](https://goessner.net/articles/JsonPath/)。   

    如果 JSON 具有属性，该属性将包含以下代码所示的值数组：  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    配置如以下配置文件中所示的属性（使用从零开始的索引）：

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>包含空格的属性名称
如果属性名称包含空格，请按以下示例中所示使用方括号（数据库服务器名称）：

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>使用配置部署解决方案
在 VS 中发布 Azure 数据工厂实体时，可以指定要用于该发布操作的配置。

使用配置文件在 Azure 数据工厂项目中发布实体：   

1. 右键单击数据工厂项目，并单击“发布”查看“发布项”对话框。  
2. 选择现有的数据工厂，或者在“配置数据工厂”页上指定用于创建数据工厂的值，并单击“下一步”。     
3. 在“发布项”页上，可以看到一个下拉列表，其中包含“选择部署配置”字段的可用配置。  

    ![选择配置文件](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. 选择要使用的**配置文件**，并单击“下一步”。 
5. 确认在“摘要”页上显示了 JSON 文件的名称，并单击“下一步”。  
6. 部署操作完成后，请单击“完成”。 

如果进行部署，则在将实体部署到 Azure 数据工厂服务之前，请使用配置文件中的值设置 JSON 文件中的属性。   

## <a name="use-azure-key-vault"></a>使用 Azure 密钥保管库
不建议将敏感数据（例如连接字符串）提交到代码存储库，这样做违反安全策略。 请参阅 GitHub 上的 [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) 示例，了解在发布数据工厂实体时，如何将敏感信息存储在 Azure Key Vault 中并进行使用。 使用适用于 Visual Studio 的 Secure Publish 扩展，可以将机密存储在 Key Vault 中，仅在链接的服务/部署配置中指定这些机密的引用。 向 Azure 发布数据工厂实体时，会对这些引用进行解析。 然后即可将这些文件提交到源存储库，不会公开任何机密。

## <a name="summary"></a>总结
本教程通过在 HDInsight hadoop 群集上运行 Hive 脚本，创建了一个 Azure 数据工厂来处理数据。 在 Azure 门户中使用数据工厂编辑器执行了以下步骤：  

1. 创建了 Azure **数据工厂**。
2. 创建了两个 **链接服务**：
   1. **Azure 存储** 链接服务，用于将保存输入/输出文件的 Azure Blob 存储链接到数据工厂。
   2. **Azure HDInsight** 按需链接服务，用于将 HDInsight Hadoop 按需群集链接到数据工厂。 Azure 数据工厂实时创建 HDInsight Hadoop 群集来处理输入数据以及生成输出数据。
3. 创建了两个 **数据集**，描述管道中 HDInsight Hive 活动的输入和输出数据。
4. 创建了包含 **HDInsight Hive** 活动的**管道**。  

## <a name="next-steps"></a>后续步骤
本文创建了可在按需 HDInsight 群集上运行 Hive 脚本、包含转换活动（HDInsight 活动）的管道。 要了解如何使用复制活动将数据从 Azure Blob 复制到 Azure SQL，请参阅 [Tutorial: Copy data from an Azure blob to Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)（教程：将数据从 Azure Blob 复制到 Azure SQL）。

通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[数据工厂中的计划和执行情况](data-factory-scheduling-and-execution.md)。 


## <a name="see-also"></a>另请参阅

| 主题 | 说明 |
|:--- |:--- |
| [管道](data-factory-create-pipelines.md) |帮助你了解 Azure 数据工厂中的管道和活动，以及如何利用它们为方案或业务构造数据驱动工作流。 |
| [数据集](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [Data Transformation Activities](data-factory-data-transformation-activities.md) |此文提供 Azure 数据工厂支持的数据转换活动列表（例如本教程中使用的 HDInsight Hive 转换）。 |
| [Scheduling and execution](data-factory-scheduling-and-execution.md) |本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
| [使用监视应用监视和管理管道](data-factory-monitor-manage-app.md) |本文介绍如何使用监视和管理应用来监视、管理和调试管道。 |
