---
title: 使用 Azure 数据工厂更新机器学习模型
description: 介绍如何使用 Azure 数据工厂和 Azure 机器学习创建预测管道
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 83cb62efd98615b7eda7f52ebafe95dedc282355
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930448"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>使用更新资源活动更新 Azure 机器学习模型

> [!div class="op_single_selector" title1="转换活动"]
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


> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[在数据工厂中更新机器学习模型](../update-machine-learning-models.md)。

本文是 Azure 数据工厂和 Azure 机器学习集成主要文章[使用 Azure 机器学习和 Azure 数据工厂创建预测管道](data-factory-azure-ml-batch-execution-activity.md)的补充。 如果尚未执行此操作，请在阅读本文之前查阅此主要文章。 

## <a name="overview"></a>概述
随着时间推移，需要使用新的输入数据集重新定型 Azure 机器学习评分实验中的预测模型。 完成重新定型后，需使用重新定型的机器学习模型更新评分 Web 服务。 通过 Web 服务重新定型和更新 Azure 机器学习模型的常规步骤如下：

1. 在[Azure 机器学习 Studio （经典）](https://studio.azureml.net)中创建试验。
2. 如果对模型感到满意，请使用 Azure 机器学习 Studio （经典）发布 web 服务，以进行**训练试验**和评分/预测性**实验**。

下表介绍了本示例所用的 Web 服务。  有关详细信息，请参阅[以编程方式重新训练机器学习 Studio （经典）模型](../../machine-learning/studio/retrain-machine-learning-model.md)。

- **定型 Web 服务** - 接收定型数据和生成定型模型。 重新定型的输出是 Azure Blob 存储中的 .ilearner 文件。 将训练实验作为 Web 服务发布时，会自动创建**默认终结点**。 还可创建更多终结点，但本示例仅使用默认终结点。
- **评分 Web 服务** - 接收未标记的数据示例并进行预测。 预测输出可能采用多种形式，例如 .csv 文件或 Azure SQL 数据库中的行，具体取决于实验的配置。 将预测实验作为 Web 服务发布时，会自动创建默认终结点。 

下图描述了 Azure 机器学习中定型和评分终结点之间的关系。

![Web 服务](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

使用**Azure 机器学习批处理执行活动**可调用**定型 Web 服务**。 对于进行数据评分，调用定型 Web 服务与调用 Azure 机器学习 Web 服务（评分 Web 服务）相同。 前面几部分详细介绍了如何从 Azure 数据工厂管道调用 Azure 机器学习 Web 服务。 

使用 **Azure 机器学习更新资源活动**可调用**评分 Web 服务**，以通过新的定型模型更新 Web 服务。 以下示例提供了链接服务定义： 

## <a name="scoring-web-service-is-a-classic-web-service"></a>评分 Web 服务是经典 Web 服务
如果评分 web 服务是**经典 web 服务**，请使用 Azure 门户创建第二个**非默认且可更新的终结点**。 请参阅[创建终结点](../../machine-learning/machine-learning-create-endpoint.md)一文以了解相关步骤。 创建非默认的可更新终结点之后，执行以下步骤：

* 单击“批处理执行”**** 获取 **mlEndpoint** JSON 属性的 URI 值。
* 单击“更新资源”**** 链接以获取 **updateResourceEndpoint** JSON 属性的 URI 值。 API 密钥就在终结点页面上（位于右下角）。

![可更新终结点](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

以下示例为 AzureML 链接服务提供示例 JSON 定义。 此链接服务使用 apiKey 进行身份验证。  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>Web 评分服务是 Azure 资源管理器 Web 服务 
如果 Web 服务是公开 Azure 资源管理器终结点的新类型 Web 服务，则无需添加第二个**非默认**终结点。 链接服务中的 **updateResourceEndpoint** 的格式如下： 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

在 [Azure 机器学习 Web 服务门户](https://services.azureml.net/)上查询 Web 服务时可获取 URL 中占位符的值。 新类型的更新资源终结点要求 AAD (Azure Active Directory) 令牌。 在 Azure 机器学习链接的服务中指定**servicePrincipalId**和**servicePrincipalKey** 。 请参阅[如何创建服务主体和分配权限来管理 Azure 资源](../../active-directory/develop/howto-create-service-principal-portal.md)。 此处为一示例 AzureML 链接服务定义： 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

以下方案提供更多详细信息。 它包含有关重新定型和更新 Azure 数据工厂管道中 Azure 机器学习模型的示例。

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>方案：重新定型和更新 Azure 机器学习模型
本部分提供使用 **Azure 机器学习批处理执行活动**来重新定型模型的示例管道。 该管道还使用 **Azure 机器学习更新资源活动**更新评分 Web 服务中的模型。 此外，本部分为示例中的所有链接服务、数据集和管道提供 JSON 片段。

下面是示例管道的图示视图。 如你所见，Azure 机器学习批处理执行活动采用定型输入并生成定型输出（iLearner 文件）。 Azure 机器学习更新资源活动采用此定型输出并更新评分 Web 服务终结点中的模型。 更新资源活动不会生成任何输出。 placeholderBlob 只是 Azure 数据工厂服务运行管道所需的虚拟输出数据集。

![管道关系图](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob 存储链接服务：
Azure 存储保留以下数据：

* 定型数据。 Azure 机器学习定型 Web 服务的输入数据。  
* iLearner 文件。 来自 Azure 机器学习定型 Web 服务的输出。 此文件也是更新资源活动的输入。  

下面是链接服务的示例 JSON 定义：

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>定型输入数据集：
以下数据集表示 Azure 机器学习定型 web 服务的输入定型数据。 Azure 机器学习批处理执行活动采用此数据集作为输入。

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>定型输出数据集：
以下数据集表示 Azure 机器学习定型 Web 服务中的输出 iLearner 文件。 Azure 机器学习批处理执行活动生成此数据集。 此数据集也是 Azure 机器学习更新资源活动的输入。

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Azure 机器学习培训端点的链接服务
以下 JSON 片段定义指向定型 Web 服务默认终结点的 Azure 机器学习链接服务。

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

在**Azure 机器学习 Studio （经典）** 中，执行以下操作以获取**mlEndpoint**和**apiKey**的值：

1. 在左侧菜单上，单击“Web 服务”****。
2. 在 Web 服务列表中，单击“定型 Web 服务”****。
3. 单击“API 密钥”**** 文本框旁的“复制”。 将剪贴板中的密钥粘贴到数据工厂 JSON 编辑器。
4. 在**Azure 机器学习 Studio （经典）** 中，单击 "**批处理执行**" 链接。
5. 从“请求”**** 分区复制“请求 URI”****，然后将其粘贴到数据工厂 JSON 编辑器。   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure 机器学习可更新评分终结点的链接服务：
以下 JSON 片段定义指向评分 Web 服务的非默认可更新终结点的 Azure 机器学习链接服务。  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>占位符输出数据集：
Azure 机器学习更新资源活动不会生成任何输出。 但是，Azure 数据工厂需要输出数据集来推动管道计划。 因此，本示例使用虚拟/占位符数据集。  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>管道
管道具有两个活动：**AzureMLBatchExecution** 和 **AzureMLUpdateResource**。 Azure 机器学习批处理执行活动采用定型数据作为输入，并生成 iLearner 文件作为输出。 活动会通过输入定型数据调用定型 Web 服务（作为 Web 服务公开的训练实验），并从 Web 服务接收 ilearner 文件。 placeholderBlob 只是 Azure 数据工厂服务运行管道所需的虚拟输出数据集。

![管道关系图](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
