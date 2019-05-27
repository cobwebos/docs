---
title: 发布知识库、REST 和 Python
titleSuffix: QnA Maker - Azure Cognitive Services
description: 此基于 Python REST 的快速入门将指导你完成发布知识库的过程，发布操作会将经过测试的知识库的最新版本推送至代表“已发布”知识库的专用 Azure 搜索索引。 还会创建一个终结点，可在应用程序或聊天机器人中调用此终结点。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 60c5e24baf9062f6d7da3bf6f477c2b64101670e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787879"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>快速入门：在 QnA Maker 中通过 Python 发布知识库

此基于 REST 的快速入门将指导你完成以编程方式发布知识库 (KB) 的过程。 发布操作会将知识库的最新版本推送到一个专用 Azure 搜索引擎，并创建一个可以在应用程序或聊天机器人中调用的终结点。

本快速入门调用了 QnA Maker API：
* [发布](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要请求正文中的任何信息。

## <a name="prerequisites"></a>先决条件

* [Python 3.7](https://www.python.org/downloads/)
* 必须已有一个 QnA Maker 服务。 若要检索密钥，请在仪表板的“资源管理”下选择“密钥”。
* 在 kbid 查询字符串参数中的 URL 中找到的 QnA Maker 知识库 (KB) ID，如下所示。

    ![QnA Maker 知识库 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果还没有知识库，可以创建一个用于本快速入门的示例知识库：[创建新的知识库](create-new-kb-nodejs.md)。

> [!NOTE] 
> 完整的解决方案文件可从 [Azure-Samples/cognitive-services-qnamaker-python GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)获得。

## <a name="create-a-knowledge-base-python-file"></a>创建知识库 Python 文件

创建名为 `publish-kb-3x.py` 的文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `publish-kb-3x.py` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>添加必需的常量

在上述必需的依赖项后，添加访问 QnA Maker 所必需的常量。 将值替换成自己的值。

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>添加 POST 请求来发布知识库

在所需常量后添加以下代码，以便向 QnA Maker API 发出 HTTPS 请求，目的是发布知识库并接收响应：

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

对于成功的发布，该 API 调用会返回一个 204 状态，并且响应正文中没有任何内容。 该代码为 204 响应添加了内容。

对于任何其他响应，该响应将原样返回，不做任何更改。

## <a name="build-and-run-the-program"></a>生成并运行程序

在命令行中输入以下命令以运行程序。 它向 QnA Maker API 发送发布知识库的请求，然后输出 204，表示成功或错误。

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

在发布知识库后，需要[生成答案所需的终结点 URL](../Tutorials/create-publish-answer.md#generating-an-answer)。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)

[QnA Maker 概述](../Overview/overview.md)
