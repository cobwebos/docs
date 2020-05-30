---
title: Azure 窗体识别器的灾难恢复指南
titleSuffix: Azure Cognitive Services
description: 了解如何使用复制模型 API 来备份窗体识别器资源。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221841"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>备份和恢复窗体识别器模型

在 Azure 门户中创建窗体识别器资源时，请指定一个区域。 从那开始，你的资源及其所有操作与该特定的 Azure 服务器区域保持关联。 这种情况很少出现，但并不是不可能遇到的网络问题。 如果你的解决方案需要始终可用，则应将其设计为故障转移到另一个区域，或在两个或多个区域之间拆分工作负荷。 这两种方法在不同区域中至少需要两个窗体识别器资源，并且能够跨区域同步[自定义模型](./quickstarts/curl-train-extract.md)。

复制 API 允许您将自定义模型从一个窗体识别器帐户或其他人（可以存在于任何受支持的地理区域）复制到其他模型，从而实现了这种方案。 本指南说明如何将复制 REST API 与卷曲一起使用。 你还可以使用 HTTP 请求服务（如 Postman）发出请求。

## <a name="business-scenarios"></a>业务方案

如果你的应用程序或企业依赖于窗体识别器自定义模型的使用，我们建议你将模型复制到另一个区域中的另一个窗体识别器帐户。 如果发生区域性中断，你可以在复制模型的区域中访问模型。

##  <a name="prerequisites"></a>先决条件

1. 两个窗体识别不同 Azure 区域中的 Azure 资源。 如果没有这些资源，请参阅 Azure 门户，并 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" 创建新的窗体识别器资源 " target="_blank"> 创建新的窗体识别器资源 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 。
1. 你的窗体识别器资源的订阅密钥、终结点 URL 和订阅 ID。 可以在 Azure 门户上资源的 "**概述**" 选项卡中找到这些值。


## <a name="copy-api-overview"></a>复制 API 概述

复制自定义模型的过程包括以下步骤：

1. 首先向目标资源 &mdash; （即将接收复制的模型的资源）发出对目标资源的复制授权请求。 返回新创建的目标模型的 URL，它将接收复制的数据。
1. 接下来，将复制请求发送到源资源， &mdash; 其中包含要复制的模型的资源。 您将获得一个 URL，您可以查询该 URL 以跟踪操作的进度。
1. 你将使用源资源凭据来查询进度 URL，直到操作成功为止。 您还可以在目标资源中查询新的模型 ID 以获取新模型的状态。

## <a name="generate-copy-authorization-request"></a>生成复制授权请求

以下 HTTP 请求从目标资源获取复制授权。 需要以标头的形式输入目标资源的终结点和密钥。

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

你将 `201\Created` 在正文中收到一个值为的响应 `modelId` 。 此字符串是新创建的（空白）模型的 ID。 `accessToken`API 需要将数据复制到此资源， `expirationDateTimeTicks` 值为令牌的过期时间。 将所有这三个值都保存到一个安全位置。

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>开始复制操作

以下 HTTP 请求将对源资源启动复制操作。 需要以标头形式输入源资源的终结点和密钥。 请注意，请求 URL 包含要复制的源模型的模型 ID。

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

请求正文需要采用以下格式。 需要输入目标资源的资源 ID 和区域名称。 你还需要从上一步骤中复制的 "模型 ID"、"访问令牌" 和 "过期" 值。

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

你将收到 `202\Accepted` 包含操作位置标头的响应。 此值是用于跟踪操作进度的 URL。 将其复制到临时位置，以供下一步使用。

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>跟踪复制进度

通过根据源资源终结点查询**获取副本模型结果**API 来跟踪进度。

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

响应会因操作的状态而有所不同。 查找 `"status"` JSON 正文中的字段。 如果要在脚本中自动执行此 API 调用，建议每秒查询一次此操作。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>可有可无跟踪目标模型 ID 

你还可以使用**获取自定义模型**API 来跟踪操作的状态，方法是查询目标模型。 使用在第一步中复制的目标模型 ID 调用此 API。

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

在响应正文中，你将看到有关模型的信息。 检查该 `"status"` 模型的状态字段。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>卷曲示例代码

以下代码片段使用卷来执行上述步骤中所述的 API 调用。 你仍需要填写特定于你自己的资源的模型 Id 和订阅信息。

### <a name="generate-copy-authorization-request"></a>生成复制授权请求

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>开始复制操作

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>跟踪复制进度

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>后续步骤

在本指南中，已学习如何使用复制 API 将自定义模型备份到辅助窗体识别器资源。 接下来，浏览 API 参考文档，以了解可以对窗体识别器执行的其他操作。
* [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)