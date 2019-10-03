---
title: 快速入门：从知识库获取答案 - REST、Go - QnA Maker
titleSuffix: Azure Cognitive Services
description: 此基于 Go REST 的快速入门详细介绍如何以编程方式从知识库获取答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 7fa0a747965f946198c59aa521c6269fa159c702
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802959"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>快速入门：使用 Go 从知识库获取问题的答案

本快速入门详细介绍如何以编程方式从已发布的 QnA Maker 知识库获取答案。 知识库包含来自[数据源](../Concepts/data-sources-supported.md)的问题和答案，例如常见问题解答。 [问题](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)将发送到 QnA Maker 服务。 [响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含最常见的预测答案。 

## <a name="prerequisites"></a>先决条件

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在适用于 QnA Maker 资源的 Azure 仪表板的“资源管理”下选择“密钥”。   
* “发布”页设置。  如果没有已发布的知识库，请创建一个空的知识库，接着“设置”页上导入一个知识库，然后进行发布。  可以下载并使用[这个基本的知识库](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。 

    发布页设置包括 POST 路由值、Host 值和 EndpointKey 值。 

    ![发布设置](../media/qnamaker-quickstart-get-answer/publish-settings.png)

本快速入门的代码位于 [https://github.com/Azure-Samples/cognitive-services-qnamaker-Go](https://github.com/Azure-Samples/cognitive-services-qnamaker-Go/tree/master/documentation-samples/quickstarts/get-answer) 存储库中。 

## <a name="create-a-go-file"></a>创建 Go 文件

打开 VSCode，创建名为 `get-answer.go` 的新文件，然后添加以下类：

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `main` 函数上方，`get-answer.go` 文件顶部，向项目添加必需的依赖项：

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量

在 `main` 函数的顶部，添加必需的常量来访问 QnA Maker。 发布知识库后，这些值会出现在“发布”页上。  

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>添加一个 POST 请求来发送问题并获取答案

以下代码向 QnA Maker API 发出 HTTPS 请求，以便向知识库发送问题并接收响应：

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

`Authorization` 标头的值包括字符串 `EndpointKey`。 

详细了解[请求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

## <a name="build-and-run-the-program"></a>生成并运行程序

通过命令行生成并运行程序。 它会自动向 QnA Maker API 发送请求，然后输出到控制台窗口。

1. 生成文件：

    ```bash
    go build get-answer.go
    ```

1. 运行文件：

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
