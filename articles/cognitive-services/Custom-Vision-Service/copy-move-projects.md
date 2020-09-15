---
title: 复制并移动自定义视觉项目
titleSuffix: Azure Cognitive Services
description: 了解如何使用 ExportProject 和 ImportProject Api 来复制和移动自定义视觉项目。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 5285dfb23476662a13162788b2ec497b4fe49228
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532586"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>复制并移动自定义视觉项目

创建并训练自定义视觉项目后，你可能想要将项目复制到另一个资源。 例如，你可能想要将项目从开发环境转移到生产环境，或者将项目备份到不同 Azure 区域中的帐户，以提高数据安全性。

**[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3])** 和**[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** api 允许将项目从一个自定义视觉帐户复制到其他帐户，从而实现了这种方案。 本指南演示如何通过卷使用这些 REST Api。 你还可以使用 HTTP 请求服务（如 Postman）发出请求。

## <a name="business-scenarios"></a>业务方案

如果你的应用或企业依赖于自定义视觉项目，则建议你将模型复制到另一个区域中的另一个自定义视觉帐户。 然后，如果发生区域性中断，你可以在复制项目的区域中访问该项目。

##  <a name="prerequisites"></a>先决条件

- 两个 Azure 自定义视觉资源。 如果没有这些资源，请参阅 "Azure 门户"，并 [创建新自定义视觉资源](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。
- 自定义视觉资源的定型密钥和终结点 Url。 可以在 Azure 门户上资源的 " **概述** " 选项卡中找到这些值。
- 创建自定义视觉项目。 有关如何执行此操作的说明，请参阅 [生成分类器](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/getting-started-build-a-classifier) 。

## <a name="process-overview"></a>过程概述

复制项目的过程包括以下步骤：

1. 首先，获取你想要复制的源帐户中的项目 ID。
1. 然后，使用源帐户的项目 ID 和定型密钥调用 **ExportProject** API。 你将获得一个临时令牌字符串。
1. 然后，使用令牌字符串和目标帐户的训练密钥调用 **ImportProject** API。 然后，该项目将在目标帐户下列出。

## <a name="get-the-project-id"></a>获取项目 ID

首先调用 **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** 以查看现有自定义视觉项目及其 id 的列表。 使用源帐户的训练密钥和终结点。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

你将收到一 `200\OK` 项响应，其中包含项目的列表及其在正文中的元数据。 `"id"`值是要为后续步骤复制的字符串。

```json
[
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
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>导出项目

使用项目 ID 和源定型密钥和终结点调用 **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

你将收到 `200/OK` 有关导出的项目和引用字符串的元数据的响应 `"token"` 。 复制令牌的值。

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>导入项目

使用目标定型密钥和终结点调用 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** ，以及引用令牌。 你还可以在新帐户中为你的项目命名。

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects/import?token={token}?name={name}"
-H "Training-key: {training key}"
```

你将收到 `200/OK` 有关新导入项目的元数据的响应。

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
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何在自定义视觉资源之间复制和移动项目。 接下来，浏览 API 参考文档，以了解可以对自定义视觉执行的其他操作。
* [REST API 参考文档](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
