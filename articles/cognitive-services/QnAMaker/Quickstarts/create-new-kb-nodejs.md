---
title: 快速入门：创建知识库 - REST、Node.js - QnA Maker
description: 此基于 REST 的快速入门详细介绍如何以编程方式创建一个示例 QnA Maker 知识库，该知识库会显示在认知服务 API 帐户的 Azure 仪表板中。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: a9a6470e8dc7b9f82ae8db586fcbd6629d8ed757
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235558"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-nodejs"></a>快速入门：通过 Node.js 在 QnA Maker 中创建知识库

本快速入门将指导你完成以编程方式创建和发布示例 QnA Maker 知识库的过程。 QnA Maker 自动从[数据源](../Concepts/data-sources-supported.md)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。 

本快速入门调用了 QnA Maker API：
* [创建知识库](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [获取操作详细信息](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>先决条件

* [Node.js 6+](https://nodejs.org/en/download/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在仪表板的“资源管理”下选择“密钥”。 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-nodejs-repo-note.md)]

## <a name="create-a-knowledge-base-nodejs-file"></a>创建知识库 Node.js 文件

创建名为 `create-new-knowledge-base.js` 的文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `create-new-knowledge-base.js` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=1-4 "Add the dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量
在上述必需的依赖项后，添加访问 QnA Maker 所必需的常量。 将 `subscriptionKey` 变量的值替换为你自己的 QnA Maker 密钥。

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=10-19 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>添加知识库模型定义

在常量后，添加以下知识库模型定义。 模型将在定义后转换为字符串。

[!code-nodejs[Add the KB model definition](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=21-51 "Add the KB model definition")]

## <a name="add-supporting-functions"></a>添加支持函数

接下来，添加以下支持函数。

1. 添加以下函数，用于以可读格式输出 JSON：

   [!code-nodejs[Add supporting functions, step 1](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=53-56 "Add supporting functions, step 1")]

2. 添加以下函数来管理 HTTP 响应：

   [!code-nodejs[Add supporting functions, step 2](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=58-80 "Add supporting functions, step 2")]

## <a name="add-functions-to-create-kb"></a>添加函数以创建知识库

添加以下函数以发出用于创建知识库的 HTTP POST 请求。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。 

[!code-nodejs[POST Request to API](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=82-109 "POST Request to API")]

此 API 调用将返回包括操作 ID 的 JSON 响应。 使用操作 ID 来确定知识库是否已成功创建。 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-functions-to-determine-creation-status"></a>添加函数以确定创建状态

添加以下函数以发出用于检查操作状态的 HTTP GET 请求。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。 

[!code-nodejs[Determine creation status](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=112-135 "Determine creation status")]

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


## <a name="add-create-kb-function"></a>添加创建知识库函数

以下函数是主函数，用于创建知识库并重复检查状态。 _create_ **Operation ID** 在 POST 响应标头字段 **Location** 中返回，然后在 GET 请求中用作路由的一部分。 因为知识库创建可能要花费一些时间，所以你需要重复用来检查状态的调用，直到状态为成功或失败。

[!code-nodejs[Add create-kb function](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base.js?range=137-167 "Add create-kb function")]

## <a name="run-the-program"></a>运行程序

在命令行中输入以下命令以运行程序。 它向 QnA Maker API 发送创建知识库的请求，然后每隔 30 秒轮询一次结果。 每个响应都将输出到控制台窗口中。

```bash
node create-new-knowledge-base.js
```

创建知识库以后，即可在 QnA Maker 门户 - [My knowledge bases](https://www.qnamaker.ai/Home/MyServices)（我的知识库）页中查看它。 

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
