---
title: 快速入门：创建知识库 - REST、Python - QnA Maker
titleSuffix: Azure Cognitive Services
description: 此 Python 基于 REST 的快速入门详细介绍如何以编程方式创建一个示例 QnA Maker 知识库，该知识库会显示在认知服务 API 帐户的 Azure 仪表板中。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 9114d491be1ae11623264c3beaf7c26f1fa143de
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803148"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>快速入门：在 QnA Maker 中使用 Python 创建知识库

本快速入门将指导你完成以编程方式创建和发布示例 QnA Maker 知识库的过程。 QnA Maker 自动从[数据源](../Concepts/data-sources-supported.md)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。 

本快速入门调用了 QnA Maker API：
* [创建知识库](https://go.microsoft.com/fwlink/?linkid=2092179)
* [获取操作详细信息](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>先决条件

* [Python 3.7](https://www.python.org/downloads/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”  。 

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>创建知识库 Python 文件

创建名为 `create-new-knowledge-base-3x.py` 的文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `create-new-knowledge-base-3x.py` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量
在上述必需的依赖项后，添加访问 QnA Maker 所必需的常量。 将 `<your-qna-maker-subscription-key>` 和 `<your-resource-name>` 的值替换为你自己的 QnA Maker 密钥和资源名称。

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>添加知识库模型定义

在常量后，添加以下知识库模型定义。 模型将在定义后转换为字符串。

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>添加支持函数

添加以下函数，用于以可读格式输出 JSON：

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>添加函数以创建知识库

添加以下函数，以发出用于创建知识库的 HTTP POST 请求。 此 API 调用将在标头字段 **Location** 中返回包括操作 ID 的 JSON 响应。 使用操作 ID 来确定知识库是否已成功创建。 `Ocp-Apim-Subscription-Key` 是用于身份验证的 QnA Maker 服务密钥。 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

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

## <a name="add-function-to-check-creation-status"></a>添加函数以检查创建状态

以下函数检查 URL 路由末尾的操作 ID 中发送的创建状态。 对 `check_status` 的调用位于主 _while_ 循环内。

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

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

## <a name="add-main-code-block"></a>添加主代码块
以下循环定期轮询创建操作状态，直到该操作完成。 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>生成并运行程序

在命令行中输入以下命令以运行程序。 它向 QnA Maker API 发送创建知识库的请求，然后每隔 30 秒轮询一次结果。 每个响应都将输出到控制台窗口中。

```bash
python create-new-knowledge-base-3x.py
```

创建知识库以后，即可在 QnA Maker 门户 - [My knowledge bases](https://www.qnamaker.ai/Home/MyServices)（我的知识库）页中查看它。 选择要查看的知识库名称，例如“QnA Maker 常见问题解答”。

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
