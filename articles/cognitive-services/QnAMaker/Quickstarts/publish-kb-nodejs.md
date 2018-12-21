---
title: 发布知识库，REST，Node.js
titleSuffix: QnA Maker - Azure Cognitive Services
description: 本 Node.js 快速入门将指导你完成以编程方式发布知识库 (KB) 的过程。 发布操作会将知识库的最新版本推送到一个专用 Azure 搜索引擎，并创建一个可以在应用程序或聊天机器人中调用的终结点。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: diberry
ms.openlocfilehash: 5970cb0b4f5e38862d41fc0db84847b1bb92c173
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164171"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-nodejs"></a>快速入门：在 QnA Maker 中使用 Node.js 发布知识库

此基于 REST 的快速入门将指导你完成以编程方式发布知识库 (KB) 的过程。 发布操作会将知识库的最新版本推送到一个专用 Azure 搜索引擎，并创建一个可以在应用程序或聊天机器人中调用的终结点。

本快速入门调用了 QnA Maker API：
* [发布](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 此 API 不需要请求正文中的任何信息。

## <a name="prerequisites"></a>先决条件

* [Node.js 6+](https://nodejs.org/en/download/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在仪表板的“资源管理”下选择“密钥”。 
* 在 kbid 查询字符串参数中的 URL 中找到的 QnA Maker 知识库 (KB) ID，如下所示。

    ![QnA Maker 知识库 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果还没有知识库，可以创建一个用于本快速入门的示例知识库：[创建新的知识库](create-new-kb-nodejs.md)。


> [!NOTE] 
> 完整的解决方案文件可从 [Azure-Samples/cognitive-services-qnamaker-nodejs GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/publish-knowledge-base-short)获得。

## <a name="create-a-knowledge-base-nodejs-file"></a>创建知识库 Node.js 文件

创建名为 `publish-knowledge-base.js` 的文件。

## <a name="add-required-dependencies"></a>添加必需的依赖项

在 `publish-knowledge-base.js` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-nodejs[Add the dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=1-3 "Add the dependencies")]

## <a name="add-required-constants"></a>添加必需的常量

在上述必需的依赖项后，添加访问 QnA Maker 所必需的常量。 将值替换成自己的值。

[!code-nodejs[Add required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=11-14 "Add required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>添加 POST 请求来发布知识库

在所需常量后添加以下代码，以便向 QnA Maker API 发出 HTTPS 请求，目的是发布知识库并接收响应：

[!code-nodejs[Add a POST request to publish knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/publish-knowledge-base-short/publish-knowledge-base.js?range=16-47 "Add a POST request to publish knowledge base")]

对于成功的发布，该 API 调用会返回一个 204 状态，并且响应正文中没有任何内容。 该代码为 204 响应添加了内容。

对于任何其他响应，该响应将原样返回，不做任何更改。

## <a name="run-the-program"></a>运行程序

生成并运行程序。 它将自动向 QnA Maker API 发送发布知识库的请求，然后，响应将输出到控制台窗口。

发布知识库后，可以使用客户端应用程序或聊天机器人从终结点对其进行查询。 

```bash
node publish-knowledge-base.js
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

在发布知识库后，需要[生成答案所需的终结点 URL](../Tutorials/create-publish-answer.md#generating-an-answer)。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
