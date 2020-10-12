---
title: 集成 Azure 存储以提供通知和模型备份
titleSuffix: Azure Cognitive Services
description: 了解如何集成 Azure 存储，以便在训练或导出自定义视觉模型时接收推送通知。 还可以保存导出模型的备份。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f4d9cc4c02ab062c73e9dbd977d9ea9e6ccdb60d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532624"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>集成 Azure 存储以提供通知和备份

可以将自定义视觉项目与 Azure blob 存储队列集成，以获取项目训练/导出活动的推送通知和已发布模型的备份副本。 运行长时间的操作时，此功能有助于避免不断轮询服务来获取结果。 你可以改为将存储队列通知集成到工作流中。

本指南演示如何通过卷使用这些 REST Api。 你还可以使用 HTTP 请求服务（如 Postman）发出请求。

> [!NOTE]
> 推送通知依赖于**CreateProject** API 中的可选_notificationQueueUri_参数，而模型备份则需要使用可选的_exportModelContainerUri_参数。 本指南将同时使用这两个功能。

## <a name="prerequisites"></a>必备条件

- Azure 中的自定义视觉资源。 如果没有，请参阅 "Azure 门户"，并 [创建新自定义视觉资源](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。 此功能当前不支持认知服务资源 (都) 。
- 具有 blob 容器的 Azure 存储帐户。 如果需要此步骤的帮助，请遵循 [Azure 存储实验室的练习 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) 。

## <a name="set-up-azure-storage-integration"></a>设置 Azure 存储集成

前往 Azure 门户上的自定义视觉培训资源，选择 " **标识** " 页，并启用系统分配的托管标识。

接下来，请在 Azure 门户中找到存储资源。 请 **访问 (IAM) ** 页上的访问控制，并为每个集成功能添加角色分配：
* 如果计划使用模型备份功能，请选择自定义视觉定型资源并分配 **存储 Blob 数据参与者** 角色。 
* 如果计划使用通知队列功能，请选择自定义视觉定型资源并分配 **存储队列数据参与者** 。

> [!div class="mx-imgBorder"]
> !["存储帐户添加角色分配" 页](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>获取集成 Url

接下来，你将获得允许自定义视觉资源访问这些终结点的 Url。

对于通知队列集成 URL，请参阅存储帐户的 " **队列** " 页，添加新队列，并将其 URL 保存到临时位置。

> [!div class="mx-imgBorder"]
> ![Azure 存储队列页](./media/storage-integration/queue-url.png) 

对于模型备份集成 URL，请参阅存储帐户的 " **容器** " 页，并创建新的容器。 然后选择它，并跳到 " **属性** " 页。 将 URL 复制到临时位置。
 
> [!div class="mx-imgBorder"]
> ![Azure 存储容器属性页](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>集成自定义视觉项目

现在，你已有集成 Url，你可以创建一个集成 Azure 存储功能的新自定义视觉项目。 还可以更新现有项目以添加功能。

### <a name="create-new-project"></a>创建新项目

调用 [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API 时，请添加可选参数 _exportModelContainerUri_ 和 _notificationQueueUri_。 分配你在上一节中获取的 URL 值。 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

如果收到 `200/OK` 响应，则表示已成功设置 url。 还应在 JSON 响应中看到 URL 值：

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>更新现有项目

若要使用 Azure 存储功能集成更新现有项目，请使用要更新的项目的 ID 调用 [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API。 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

将请求正文 (`body`) 设置为以下 JSON 格式，并填充 _ExportModelContainerUri_ 和 _notificationQueueUri_的适当值：

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

如果收到 `200/OK` 响应，则表示已成功设置 url。

## <a name="verify-the-connection"></a>验证连接 

之前部分中的 API 调用应该已在 Azure 存储帐户中触发了新的信息。 

在指定容器中， **CustomVision-TestPermission** 文件夹中应有一个测试 blob。 此 blob 仅暂时存在。

在通知队列中，应会看到以下格式的测试通知：
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>获取事件通知

准备就绪后，在项目上调用 [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API，以执行普通的培训操作。

在存储通知队列中，你将在训练结束后收到通知：

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`字段可以是 `"TrainingCompleted"` 或 `"TrainingFailed"` 。 该 `"iterationId"` 字段是定型模型的 ID。

## <a name="get-model-export-backups"></a>获取模型导出备份

准备就绪后，调用 [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API 将定型模型导出到指定的平台。

在指定的存储容器中，将显示导出模型的备份副本。 Blob 名称将采用以下格式：

```
{projectId} - {iterationId}.{platformType}
```

此外，当导出完成时，你会在队列中收到通知。 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`字段可以是 `"ExportCompleted"` 或 `"ExportFailed"` 。 此 `"modelUri"` 字段将包含容器中存储的备份模型的 URL，假设你在开始时集成了队列通知。 如果没有，该 `"modelUri"` 字段将显示自定义视觉模型 blob 的 SAS URL。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何在自定义视觉资源之间复制和移动项目。 接下来，浏览 API 参考文档，以了解可以对自定义视觉执行的其他操作。
* [REST API 参考文档](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
