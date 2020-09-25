---
title: 触发用于新数据的 ML 管道
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 逻辑应用触发 ML 管道的运行。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 2e3544bee5158a855467f8cb142f176df2187ef5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318290"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>从逻辑应用触发机器学习管道的运行

在新数据出现时触发 Azure 机器学习管道的运行。 例如，你可能想要在 blob 存储帐户中出现新数据时触发管道来训练新模型。 使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)设置触发器。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 已发布机器学习管道的 REST 终结点。 [创建并发布管道](how-to-create-your-first-pipeline.md)。 然后，使用管道 ID 查找已发布管道的 REST 终结点：
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* 用来存储数据的 [Azure blob 存储](../storage/blobs/storage-blobs-overview.md)。
* 工作区中包含你的 blob 存储帐户详细信息的[数据存储](how-to-access-data.md)。

## <a name="create-a-logic-app"></a>创建逻辑应用

现在创建一个 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)实例。 如果需要，[使用集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 并[设置客户管理的密钥](../logic-apps/customer-managed-keys-integration-service-environment.md)供逻辑应用使用。

预配逻辑应用后，使用以下步骤为管道配置触发器：

1. [创建系统分配的托管标识](../logic-apps/create-managed-service-identity.md)以授予应用对你的 Azure 机器学习工作区的访问权限。

1. 导航到逻辑应用设计器视图，并选择“空白逻辑应用”模板。 
    > [!div class="mx-imgBorder"]
    > ![空白模板](media/how-to-trigger-published-pipeline/blank-template.png)

1. 在设计器中，搜索 **blob**。 选择“添加或修改 blob 时(仅属性)”触发器并将此触发器添加到你的逻辑应用。
    > [!div class="mx-imgBorder"]
    > ![添加触发器](media/how-to-trigger-published-pipeline/add-trigger.png)

1. 填写你要监视其 blob 添加或修改的 Blob 存储帐户的连接信息。 选择要监视的容器。 
 
    选择轮询适合你的更新时要使用的“间隔”和“频率”。   

    > [!NOTE]
    > 此触发器将监视所选容器，但不会监视子文件夹。

1. 添加当检测到新的或修改的 blob 时要运行的 HTTP 操作。 选择“+ 新建步骤”，然后搜索并选择该 HTTP 操作。

  > [!div class="mx-imgBorder"]
  > ![搜索 HTTP 操作](media/how-to-trigger-published-pipeline/search-http.png)

  使用以下设置来配置你的操作：

  | 设置 | Value | 
  |---|---|
  | HTTP 操作 | POST |
  | URI |作为[先决条件](#prerequisites)找到的已发布管道的终结点 |
  | 身份验证模式 | 托管标识 |

1. 设置你的计划以设置你可能具有的任何 [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 的值：

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    将已添加到工作区的 `DataStoreName` 用作[先决条件](#prerequisites)。
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 设置](media/how-to-trigger-published-pipeline/http-settings.png)

1. 选择“保存”，你的计划现已准备就绪。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

> [!div class="nextstepaction"]
> [使用 Azure 机器学习管道进行批量评分](tutorial-pipeline-batch-scoring-classification.md)

* 详细了解[管道](concept-ml-pipelines.md)
* 详细了解如何[通过 Jupyter 探索 Azure 机器学习](samples-notebooks.md)

