---
title: 快速入门：从知识库获取答案 - REST、C# - QnA Maker
titleSuffix: Azure Cognitive Services
description: 此基于 C# REST 的快速入门详细介绍如何以编程方式从知识库获取答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: bb4d3c37d0f6612b4b149bde213b972014dd91d3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447549"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-c"></a>快速入门：使用 C# 从知识库获取问题的答案

本快速入门详细介绍如何以编程方式从已发布的 QnA Maker 知识库获取答案。 知识库包含来自[数据源](../Concepts/data-sources-supported.md)的问题和答案，例如常见问题解答。 [问题](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)将发送到 QnA Maker 服务。 [响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含最常见的预测答案。

[参考文档](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs)

## <a name="prerequisites"></a>必备条件

* 最新的 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)。
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在适用于 QnA Maker 资源的 Azure 仪表板的“资源管理”下选择“密钥”。  
* “发布”页设置。  如果没有已发布的知识库，请创建一个空的知识库，接着“设置”页上导入一个知识库，然后进行发布。  可以下载并使用[这个基本的知识库](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    发布页设置包括 POST 路由值、Host 值和 EndpointKey 值。

    ![发布设置](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-knowledge-base-project"></a>创建知识库项目

1. 打开 Visual Studio 2019 Community Edition。
1. 创建一个新的“控制台应用(.NET Core)”项目并将项目命名为 QnaMakerQuickstart。 接受其余设置的默认值。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 Program.cs 文件的顶部，将单个 using 语句替换为以下行，以向项目添加必要的依赖项：

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=1-3 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量

在 `Program` 类顶部的 `Main` 中，添加必需的常量来访问 QnA Maker。 发布知识库后，这些值会出现在“发布”页上。 

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=9-41 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>添加一个 POST 请求来发送问题并获取答案

以下代码向 QnA Maker API 发出 HTTPS 请求，以便向知识库发送问题并接收响应：

[!code-csharp[Add a POST request to send question to knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/get-answer/QnAMakerAnswerQuestion/Program.cs?range=43-76 "Add a POST request to send question to knowledge base")]

`Authorization` 标头的值包括字符串 `EndpointKey`。

详细了解[请求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

## <a name="build-and-run-the-program"></a>生成并运行程序

通过 Visual Studio 生成并运行程序。 它会自动向 QnA Maker API 发送请求，然后输出到控制台窗口。

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
