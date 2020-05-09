---
title: 为新数据触发 ML 管道
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 逻辑应用触发 ML 管道的运行。
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: contperfq4
ms.openlocfilehash: 32b3e153a98ca7de91e0dd05258414780b39dec4
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857972"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>从逻辑应用触发机器学习管道的运行

在新数据出现时触发 Azure 机器学习管道的运行。 例如，你可能想要在 blob 存储帐户中出现新数据时触发管道来训练新模型。 使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)设置触发器。

## <a name="prerequisites"></a>必备条件

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

现在创建一个[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)实例。 如果需要，请[使用 integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)并[设置客户管理的密钥](../logic-apps/customer-managed-keys-integration-service-environment.md)，以供逻辑应用使用。

预配逻辑应用后，请使用以下步骤为管道配置触发器：

1. [创建系统分配的托管标识](../logic-apps/create-managed-service-identity.md)，使应用能够访问你的 Azure 机器学习工作区。

1. 导航到逻辑应用设计器视图，并选择 "空白逻辑应用" 模板。 
    > [!div class="mx-imgBorder"]
    > ![空白模板](media/how-to-trigger-published-pipeline/blank-template.png)

1. 在设计器中，搜索 " **blob**"。 选择 "**添加或修改 Blob 时（仅属性）** " 触发器，并将此触发器添加到逻辑应用。
    > [!div class="mx-imgBorder"]
    > ![添加触发器](media/how-to-trigger-published-pipeline/add-trigger.png)

1. 填写要监视其 blob 添加或修改的 Blob 存储帐户的连接信息。 选择要监视的容器。 
 
    选择要轮询的更新的**间隔**和**频率**。  

    > [!NOTE]
    > 此触发器将监视所选容器，但不会监视子文件夹。

1. 添加一个将在检测到新的或已修改的 blob 时运行的 HTTP 操作。 选择 " **+ 新步骤**"，搜索并选择 HTTP 操作。

  > [!div class="mx-imgBorder"]
  > ![搜索 HTTP 操作](media/how-to-trigger-published-pipeline/search-http.png)

  使用以下设置来配置操作：

  | 设置 | 值 | 
  |---|---|
  | HTTP 操作 | POST |
  | URI |作为[必备组件](#prerequisites)找到的已发布管道的终结点 |
  | 身份验证模式 | 托管标识 |

1. 设置你的计划以设置你可能具有的任何[数据路径 pipelineparameters.json](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb)的值：

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

    使用作为`DataStoreName` [先决条件](#prerequisites)添加到工作区中的。
     
    > [!div class="mx-imgBorder"]
    > HTTP 设置![](media/how-to-trigger-published-pipeline/http-settings.png)

1. 选择 "**保存**"，你的计划现已准备就绪。
