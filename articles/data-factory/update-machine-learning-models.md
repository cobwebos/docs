---
title: 使用 Azure 数据工厂更新 Azure 机器学习 Studio (经典) 模型
description: '介绍如何使用 Azure 数据工厂和 Azure 机器学习 Studio (经典创建预测管道) '
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 83950c2d3c5439886ff787b69d9da4d0c214de31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092535"
---
# <a name="update-azure-machine-learning-studio-classic-models-by-using-update-resource-activity"></a>使用 "更新资源" 活动更新 Azure 机器学习 Studio (经典) 模型

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文补充了 Azure 数据工厂的主要 Azure 机器学习工作室 (经典) 集成文章： [使用 Azure 机器学习 Studio (经典) 和 Azure 数据工厂创建预测管道](transform-data-using-machine-learning.md)。 如果尚未执行此操作，请在阅读本文之前查阅此主要文章。

## <a name="overview"></a>概述
作为实现 Azure 机器学习 Studio (经典) 模型的过程的一部分，将对模型进行定型和保存。 然后使用它来创建预测 Web 服务。 然后可以在网站、仪表板和移动应用中使用 Web 服务。

使用 Azure 机器学习 Studio (经典) 创建的模型通常不是静态的。 当新数据变得可用时，或当 API 使用者拥有其自己的数据时，需要重新训练模型。 

重新训练可能会经常发生。 使用 "批处理执行" 活动和 "更新资源" 活动，可以使用数据工厂操作 Azure 机器学习 Studio (经典) 模型重新训练和更新预测 Web 服务。

下图描述了训练与预测性 Web 服务之间的关系。

![Web 服务](./media/update-machine-learning-models/web-services.png)

## <a name="azure-machine-learning-studio-classic-update-resource-activity"></a>Azure 机器学习 Studio (经典) 更新资源活动

下面的 JSON 代码段定义了一个 Azure 机器学习 Studio (经典) 批处理执行活动。

```json
{
    "name": "amlUpdateResource",
    "type": "AzureMLUpdateResource",
    "description": "description",
    "linkedServiceName": {
        "type": "LinkedServiceReference",
        "referenceName": "updatableScoringEndpoint2"
    },
    "typeProperties": {
        "trainedModelName": "ModelName",
        "trainedModelLinkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "StorageLinkedService"
                },
        "trainedModelFilePath": "ilearner file path"
    }
}
```

| 属性                      | 说明                              | 必须 |
| :---------------------------- | :--------------------------------------- | :------- |
| name                          | 管道中活动的名称     | 是      |
| description                   | 描述活动用途的文本。  | 否       |
| type                          | 对于 Azure 机器学习 Studio (经典) 更新资源活动，活动类型为 "  **AzureMLUpdateResource**"。 | 是      |
| linkedServiceName             | Azure 机器学习 Studio (经典) 包含 updateResourceEndpoint 属性的链接服务。 | 是      |
| trainedModelName              | 将在 Web 服务实验中进行更新的“已训练模型”模块的名称 | 是      |
| trainedModelLinkedServiceName | 存储着由更新操作上传的 ilearner 文件的 Azure 存储链接服务的名称 | 是      |
| trainedModelFilePath          | trainedModelLinkedService 中表示由更新操作上传的 ilearner 文件的相对路径 | 是      |

## <a name="end-to-end-workflow"></a>端到端工作流

对模型进行重新训练以及更新预测性 Web 服务的整个操作过程涉及以下步骤：

- 使用 **“批处理执行”活动**调用**训练 Web 服务**。 调用定型 Web 服务与调用 [使用 Azure 机器学习 Studio (经典) 和数据工厂批处理执行活动创建预测管道](transform-data-using-machine-learning.md)中所述的预测 web 服务相同。 定型 Web 服务的输出是一个 iLearner 文件，可用于更新预测 Web 服务。
- 通过使用 **“更新资源”活动**调用**预测性 Web 服务**的**更新资源终结点**来使用新训练的模型更新 Web 服务。

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure 机器学习 Studio (经典) 链接服务

若要运行上面提到的端到端工作流，需要创建两个 Azure 机器学习 Studio (经典) 链接服务：

1. Azure 机器学习 Studio (经典) 链接服务连接到定型 web 服务，批处理执行活动使用此链接服务，其方式与 [使用 Azure 机器学习 Studio (经典) 和数据工厂批处理执行活动创建预测管道](transform-data-using-machine-learning.md)中提到的内容相同。 不同之处在于定型 web 服务的输出是一个 iLearner 文件，更新资源活动将使用该文件更新预测 web 服务。
2. Azure 机器学习 Studio (经典) 将服务链接到预测 web 服务的更新资源终结点。 此链接服务由“更新资源”活动用来使用上一步骤中返回的 iLearner 文件更新预测性 Web 服务。

对于第二个 Azure 机器学习 Studio (经典) 链接服务，当 Azure 机器学习 Studio (经典) Web 服务是经典 Web 服务或新的 Web 服务时，配置将有所不同。 下面的各部分分别讨论了不同之处。

## <a name="web-service-is-new-azure-resource-manager-web-service"></a>Web 服务是新的 Azure 资源管理器 Web 服务时

如果 Web 服务是公开 Azure 资源管理器终结点的新类型 Web 服务，则无需添加第二个**非默认**终结点。 链接服务中的 **updateResourceEndpoint** 的格式如下：

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview
```

在 [Azure 机器学习 Studio (经典) Web 服务门户](https://services.azureml.net/)上查询 web 服务时，可以在 URL 中获取占位符的值。

新类型的更新资源终结点要求执行服务主体身份验证。 若要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册应用程序实体，并向其授予 Web 服务所属的订阅或资源组的“参与者”或“所有者”角色。******** 请参阅[如何创建服务主体和分配权限来管理 Azure 资源](../active-directory/develop/howto-create-service-principal-portal.md)。 记下下面的值，这些值用于定义链接服务：

- 应用程序 ID
- 应用程序密钥
- 租户 ID

下面是一个示例链接服务定义：

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000  000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": {
                "type": "SecureString",
                "value": "APIKeyOfEndpoint1"
            },
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "servicePrincipalKey"
            },
            "tenant": "mycompany.com"
        }
    }
}
```

以下方案提供更多详细信息。 它有一个从 Azure 数据工厂管道重新训练和更新 Azure 机器学习 Studio (经典) 模型的示例。


## <a name="sample-retraining-and-updating-an-azure-machine-learning-studio-classic-model"></a>示例：重新训练和更新 Azure 机器学习 Studio (经典) 模型

本部分提供的示例管道使用 **Azure 机器学习 Studio (经典) 批处理执行活动** 来重新训练模型。 管道还使用 **Azure 机器学习 Studio (经典) 更新资源活动** 更新计分 web 服务中的模型。 此外，本部分为示例中的所有链接服务、数据集和管道提供 JSON 片段。

### <a name="azure-blob-storage-linked-service"></a>Azure Blob 存储链接服务：
Azure 存储保留以下数据：

* 定型数据。 Azure 机器学习 Studio (经典) 定型 web 服务的输入数据。
* iLearner 文件。 Azure 机器学习 Studio 的输出 (经典) 定型 web 服务。 此文件也是更新资源活动的输入。

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

### <a name="linked-service-for-azure-machine-learning-studio-classic-training-endpoint"></a>Azure 机器学习 Studio (经典) 定型终结点的链接服务
下面的 JSON 代码段定义了一个 Azure 机器学习 Studio (经典) 链接服务，该服务指向定型 web 服务的默认终结点。

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

在 **Azure 机器学习 Studio (经典) **中，执行以下操作以获取 **mlEndpoint** 和 **apiKey**的值：

1. 在左侧菜单上，单击“Web 服务”****。
2. 在 Web 服务列表中，单击“定型 Web 服务”****。
3. 单击“API 密钥”**** 文本框旁的“复制”。 将剪贴板中的密钥粘贴到数据工厂 JSON 编辑器。
4. 在 **Azure 机器学习 Studio (经典) **中，单击 " **批处理执行** " 链接。
5. 从“请求”**** 分区复制“请求 URI”****，然后将其粘贴到数据工厂 JSON 编辑器。

### <a name="linked-service-for-azure-machine-learning-studio-classic-updatable-scoring-endpoint"></a>Azure 机器学习 Studio (经典) 可更新评分终结点的链接服务：
下面的 JSON 代码段定义了一个 Azure 机器学习 Studio (经典) 链接服务，该服务指向评分 web 服务的可更新终结点。

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

### <a name="pipeline"></a>管道
管道具有两个活动：**AzureMLBatchExecution** 和 **AzureMLUpdateResource**。 “批处理执行”活动采用训练数据作为输入，并生成 iLearner 文件作为输出。 然后，“更新资源”活动采用此 iLearner 文件并使用它来更新预测性 Web 服务。

```JSON
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "amlBEGetilearner",
                "description": "Use AML BES to get the ileaner file from training web service",
                "type": "AzureMLBatchExecution",
                "linkedServiceName": {
                    "referenceName": "trainingEndpoint",
                    "type": "LinkedServiceReference"
                },
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        },
                        "input2": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/input"
                        }
                    },
                    "webServiceOutputs": {
                        "output1": {
                            "LinkedServiceName":{
                                "referenceName": "StorageLinkedService",
                                "type": "LinkedServiceReference"
                            },
                            "FilePath":"azuremltesting/output"
                        }
                    }
                }
            },
            {
                "name": "amlUpdateResource",
                "type": "AzureMLUpdateResource",
                "description": "Use AML Update Resource to update the predict web service",
                "linkedServiceName": {
                    "type": "LinkedServiceReference",
                    "referenceName": "updatableScoringEndpoint2"
                },
                "typeProperties": {
                    "trainedModelName": "ADFV2Sample Model [trained model]",
                    "trainedModelLinkedServiceName": {
                        "type": "LinkedServiceReference",
                        "referenceName": "StorageLinkedService"
                    },
                    "trainedModelFilePath": "azuremltesting/output/newModelForArm.ilearner"
                },
                "dependsOn": [
                    {
                        "activity": "amlbeGetilearner",
                        "dependencyConditions": [ "Succeeded" ]
                    }
                ]
            }
        ]
    }
}
```
## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据：

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
