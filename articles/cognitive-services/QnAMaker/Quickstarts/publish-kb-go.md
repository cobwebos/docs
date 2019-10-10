---
title: 快速入门：发布知识库，REST、Go - QnA Maker
titleSuffix: Azure Cognitive Services
description: 此基于 Go REST 的快速入门将指导你完成发布知识库的过程，发布操作会将经过测试的知识库的最新版本推送至代表“已发布”知识库的专用 Azure 搜索索引。 还会创建一个终结点，可在应用程序或聊天机器人中调用此终结点。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 20e48d0c16281d6b1399d46f5ca9a7781cbaeea7
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840211"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>快速入门：在 QnA Maker 中通过 Go 发布知识库

此基于 REST 的快速入门将指导你完成以编程方式发布知识库 (KB) 的过程。 发布操作会将知识库的最新版本推送到一个专用 Azure 搜索引擎，并创建一个可以在应用程序或聊天机器人中调用的终结点。

本快速入门调用了 QnA Maker API：
* [发布](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 此 API 不需要请求正文中的任何信息。

## <a name="prerequisites"></a>先决条件

* [Go 1.10.1](https://golang.org/dl/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥和终结点（包括资源名称），请在 Azure 门户中为资源选择“快速入门”  。

* 在 kbid 查询字符串参数中的 URL 中找到的 QnA Maker 知识库 (KB) ID，如下所示。

    ![QnA Maker 知识库 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    如果还没有知识库，可以创建一个用于本快速入门的示例知识库：[创建新的知识库](create-new-kb-csharp.md)。

> [!NOTE] 
> 完整的解决方案文件可从 [Azure-Samples/cognitive-services-qnamaker-go GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)获得  。

## <a name="create-a-go-file"></a>创建 Go 文件

打开 VSCode 并创建名为 `publish-kb.go` 的新文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `publish-kb.go` 的顶部，添加以下行来向项目添加必需的依赖项：

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>创建 main 函数

在所需依赖项之后添加以下类：

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>添加必需的常量

在 **main**


 函数中，添加必需的常量来访问 QnA Maker。 将值替换成自己的值。

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>添加 POST 请求来发布知识库

在所需常量后添加以下代码，以便向 QnA Maker API 发出 HTTPS 请求，目的是发布知识库并接收响应：

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

对于成功的发布，该 API 调用会返回一个 204 状态，并且响应正文中没有任何内容。 该代码为 204 响应添加了内容。

对于任何其他响应，该响应将原样返回，不做任何更改。

## <a name="build-and-run-the-program"></a>生成并运行程序

输入以下命令以编译该文件。 对于成功的生成，命令提示符不会返回任何信息。

```bash
go build publish-kb.go
```

在命令行中输入以下命令以运行程序。 它向 QnA Maker API 发送发布知识库的请求，然后输出 204，表示成功或错误。

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

在发布知识库后，需要[生成答案所需的终结点 URL](../Tutorials/create-publish-answer.md#generating-an-answer)。 

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
