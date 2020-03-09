---
title: 快速入门：使用 cURL 和 REST 管理知识库 - QnA Maker
description: 本快速入门介绍如何使用 REST API 创建、发布和查询知识库。
ms.topic: quickstart
ms.date: 12/16/2019
ms.openlocfilehash: d2542a0307387cef1c96ecfb426a8e7c01621ee2
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165376"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>快速入门：使用 cURL 和 REST 管理知识库

本快速入门逐步介绍如何创建、发布和查询知识库。 QnA Maker 自动从[数据源](../Concepts/knowledge-base.md)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* 最新版本的 [cURL](https://curl.haxx.se/)。 快速入门中使用了 [cURL 文档](https://curl.haxx.se/docs/manpage.html)中所述的多个命令行开关。
* 必须已有一项 [QnA Maker 资源](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和资源名称，请在 Azure 门户中选择资源对应的“快速入门”。  资源名称是终结点 URL 的第一个组成部分：

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> 以下 BASH 示例使用 `\` 行继续符。 如果你的控制台或终端使用不同的行继续符，请使用此字符。

## <a name="create-a-knowledge-base"></a>创建知识库

若要使用 REST API 和 cURL 创建知识库，需要准备好以下信息：

|信息|cURL 配置|目的|
|--|--|--|
|QnA Maker 资源名称|代码|用于构造 URL|
|QnA Maker 资源密钥|`Ocp-Apim-Subscription-Key` 标头的 `-h` 参数|对 QnA Maker 服务进行身份验证|
|描述知识库的 JSON|`-d` 参数|JSON [示例](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples)|
|JSON 大小（字节）|`Content-Size` 标头的 `-h` 参数||

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥、JSON 值和 JSON 大小编辑此命令。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

QnA Maker 返回的 cURL 响应包含 `operationId`，[获取操作的状态](#get-status-of-operation)时需要此 ID。

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>获取操作的状态

创建知识库时，响应包含用于确定状态的信息，因为操作是异步的。

|信息|cURL 配置|目的|
|--|--|--|
|QnA Maker 资源名称|代码|用于构造 URL|
|操作 ID|URL 路由|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker 资源密钥|`Ocp-Apim-Subscription-Key` 标头的 `-h` 参数|对 QnA Maker 服务进行身份验证|

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥和操作 ID 编辑此命令。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

cURL 响应包含状态。 如果操作状态为 succeeded，则 `resourceLocation` 包含知识库 ID。

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>发布知识库

在查询知识库之前，需要：
* 发布知识库
* 获取运行时终结点密钥

此任务将发布知识库。 获取运行时终结点密钥是一个[独立的任务](#get-published-knowledge-bases-runtime-endpoint-key)。

|信息|cURL 配置|目的|
|--|--|--|
|QnA Maker 资源名称|代码|用于构造 URL|
|QnA Maker 资源密钥|`Ocp-Apim-Subscription-Key` 标头的 `-h` 参数|对 QnA Maker 服务进行身份验证|
|知识库 ID|URL 路由|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥和知识库 ID 编辑此命令。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

响应状态为 204，不包含结果。 使用 `-v` 命令行参数查看 cURL 命令的详细输出。 这包括 HTTP 状态。

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>获取已发布的知识库的运行时终结点密钥

在查询知识库之前，需要：
* 发布知识库
* 获取运行时终结点密钥

此任务获取运行时终结点密钥。 发布知识库是一个[独立的任务](#publish-knowledge-base)。

使用 QnA Maker 资源的所有知识库的运行时终结点密钥是相同的。

|信息|cURL 配置|目的|
|--|--|--|
|QnA Maker 资源名称|代码|用于构造 URL|
|QnA Maker 资源密钥|`Ocp-Apim-Subscription-Key` 标头的 `-h` 参数|对 QnA Maker 服务进行身份验证|

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称和资源密钥编辑此命令。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


cURL 响应包含运行时终结点密钥。 通过查询从知识库获取答案时，只使用其中一个的密钥。

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>从已发布的知识库查询答案

从知识库获取答案时所在的运行时不同于管理知识库时所在的运行时。 由于它是一个独立的运行时，因此需要使用运行时密钥进行身份验证。

|信息|cURL 配置|目的|
|--|--|--|
|QnA Maker 资源名称|代码|用于构造 URL|
|QnA Maker 运行时密钥|`Authorization` 标头的 `-h` 参数|密钥是包含单词 `Endpointkey ` 的字符串的一部分。 对 QnA Maker 服务进行身份验证|
|知识库 ID|URL 路由|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|描述查询的 JSON|`-d` 参数|[请求正文参数](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body)和 JSON [示例](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|JSON 大小（字节）|`Content-Size` 标头的 `-h` 参数||

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥和知识库 ID 编辑此命令。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

成功的响应包含最相关的答案，以及客户端应用程序（例如聊天机器人）在向用户显示答案时所需的其他信息。

## <a name="delete-knowledge-base"></a>删除知识库

用完知识库后，请将其删除。

|信息|cURL 配置|目的|
|--|--|--|
|QnA Maker 资源名称|代码|用于构造 URL|
|QnA Maker 资源密钥|`Ocp-Apim-Subscription-Key` 标头的 `-h` 参数|对 QnA Maker 服务进行身份验证|
|知识库 ID|URL 路由|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL 命令将从 BASH shell 执行。 请使用自己的资源名称、资源密钥和知识库 ID 编辑此命令。

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

响应状态为 204，不包含结果。 使用 `-v` 命令行参数查看 cURL 命令的详细输出。 这包括 HTTP 状态。

## <a name="additional-resources"></a>其他资源

* [创作](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)参考文档
* [运行时](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/)参考文档
* [使用 cURL 的示例 BASH 脚本](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
