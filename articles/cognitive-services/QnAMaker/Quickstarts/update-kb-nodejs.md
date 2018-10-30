---
title: 快速入门：更新知识库 - REST、Node.js - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本快速入门将指导你完成以编程方式更新示例 QnA Maker 知识库 (KB) 的过程。  使用此 JSON，可以通过添加新数据源、更改数据源或删除数据源来更新知识库。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 1b92589fe01f171b732c04057dc290f0f32cb31a
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647810"
---
# <a name="quickstart-update-a-knowledge-base-in-qna-maker-using-nodejs"></a>快速入门：通过 Node.js 更新 QnA Maker 中的知识库

本快速入门将指导你完成以编程方式更新示例 QnA Maker 知识库 (KB) 的过程。  使用此 JSON，可以通过添加新数据源、更改数据源或删除数据源来更新知识库。

此 API 等效于进行编辑然后使用 QnA Maker 门户中的“保存并训练”按钮。

本快速入门调用了 QnA Maker API：
* [更新](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) - 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。 
* [获取操作详细信息](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>先决条件

* [Node.js 6+](https://nodejs.org/en/download/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在仪表板的“资源管理”下选择“密钥”。 
* 在 kbid 查询字符串参数中的 URL 中找到的 QnA Maker 知识库 (KB) ID，如下所示。

    ![QnA Maker 知识库 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

如果还没有知识库，可以创建一个用于此快速入门的示例知识库：[创建新的知识库](create-new-kb-nodejs.md)。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>创建知识库 Node.js 文件

创建名为 `update-knowledge-base.js` 的文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `update-knowledge-base.js` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-required-constants"></a>添加必需的常量
在上述必需的依赖项后，添加访问 QnA Maker 所必需的常量。 将 `subscriptionKey` 变量的值替换为你自己的 QnA Maker 密钥。 

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=10-17 "Add required constants")]

## <a name="add-knowledge-base-id"></a>添加知识库 ID

在上述常量的后面，添加知识库 ID 并将其添加到路径：

[!code-nodejs[Add knowledge base ID](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=19-23 "Add knowledge base ID")]

## <a name="add-the-kb-update-model-definition"></a>添加知识库更新模型定义

在常量后面，添加以下知识库更新定义。 更新定义包含三个节：

* 添加
* update
* delete

各个节可以在发送到 API 的同一请求中使用。 

[!code-nodejs[Add the KB update definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=25-53 "Add the KB update definition")]


## <a name="add-supporting-functions"></a>添加支持函数

接下来，添加以下支持函数。

1. 添加以下函数，用于以可读格式输出 JSON：

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=55-58 "Add supporting functions, step 1")]

2. 添加以下函数来管理 HTTP 响应以获取创建操作状态：

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=60-82 "Add supporting functions, step 2")]

## <a name="add-patch-request-to-update-kb"></a>添加 PATCH 请求来更新知识库

添加以下函数来发出更新知识库的 HTTP PATCH 请求。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。

[!code-nodejs[Add PATCH request to update KB](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=84-111 "Add PATCH request to update KB")]

此 API 调用返回包含操作 ID 的 JSON 响应。 如果该操作未完成，则必须使用操作 ID 来请求状态。

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-10-02T01:23:00Z",
  "lastActionTimestamp": "2018-10-02T01:23:00Z",
  "userId": "335c3841df0b42cdb00f53a49d51a89c",
  "operationId": "e7be3897-88ff-44e5-a06c-01df0e05b78c"
}
```

## <a name="add-get-request-to-determine-operation-status"></a>添加 GET 请求来确定操作状态

检查操作的状态。
    
[!code-nodejs[Add GET request to determine operation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=113-137 "Add GET request to determine operation status")]

此 API 调用返回包含操作状态的 JSON 响应： 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

重复调用，直到成功或失败： 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>添加 update_kb 方法

以下方法更新知识库并重复检查状态。 因为知识库创建可能要花费一些时间，所以你需要重复用来检查状态的调用，直到状态为成功或失败。

[!code-nodejs[Add update_kb method](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/update-knowledge-base/update-knowledge-base.js?range=139-169 "Add update_kb method")]

## <a name="run-the-program"></a>运行程序

在命令行中输入以下命令以运行程序。 此命令向 QnA Maker API 发送更新知识库的请求，然后每隔 30 秒轮询结果。 每个响应将列显到控制台窗口。

```bash
node update-knowledge-base.js
```

更新知识库以后，可以在 QnA Maker 门户中的[我的知识库](https://www.qnamaker.ai/Home/MyServices)页中查看它。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)