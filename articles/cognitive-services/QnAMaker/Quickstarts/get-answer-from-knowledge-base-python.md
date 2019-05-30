---
title: 快速入门：从知识库获取答案 - REST、Python - QnA Maker
titlesuffix: Azure Cognitive Services
description: 此基于 Python REST 的快速入门详细介绍如何以编程方式从知识库获取答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/24/2019
ms.author: diberry
ms.openlocfilehash: 0f40ed3d0b77662e23e4042f568b8241cef130c6
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256613"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-python"></a>使用 Python 从知识库获取问题的答案

本快速入门详细介绍如何以编程方式从已发布的 QnA Maker 知识库获取答案。 知识库包含来自[数据源](../Concepts/data-sources-supported.md)的问题和答案，例如常见问题解答。 [问题](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)将发送到 QnA Maker 服务。 [响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含最常见的预测答案。 

## <a name="prerequisites"></a>先决条件

* [Python 3.6 或更高版本](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在适用于 QnA Maker 资源的 Azure 仪表板的“资源管理”下选择“密钥”。   
* “发布”页设置。  如果没有已发布的知识库，请创建一个空的知识库，接着“设置”页上导入一个知识库，然后进行发布。  可以下载并使用[这个基本的知识库](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。 

    发布页设置包括 POST 路由值、Host 值和 EndpointKey 值。 

    ![发布设置](../media/qnamaker-quickstart-get-answer/publish-settings.png)

本快速入门的代码位于 [https://github.com/Azure-Samples/cognitive-services-qnamaker-python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/get-answer) 存储库中。 

## <a name="create-a-python-file"></a>创建 Python 文件

打开 VSCode 并创建名为 `get-answer-3x.py` 的新文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `get-answer-3x.py` 文件顶部，向项目添加必需的依赖项：

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=1-2 "Add the required dependencies")]

主机和路由不同于它们在“发布”页上显示的样子。  这是因为 python 库不允许主机中的任何路由。 在“发布”页上作为主机的一部分显示的路由功能已移至此路由。 

## <a name="add-the-required-constants"></a>添加必需的常量

添加必需的常量来访问 QnA Maker。 发布知识库后，这些值会出现在“发布”页上。  

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=5-25 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>添加一个 POST 请求来发送问题并获取答案

以下代码向 QnA Maker API 发出 HTTPS 请求，以便向知识库发送问题并接收响应：

[!code-python[Add a POST request to send question to knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/get-answer/get-answer-3x.py?range=27-48 "Add a POST request to send question to knowledge base")]

`Authorization` 标头的值包括字符串 `EndpointKey`。 

## <a name="run-the-program"></a>运行程序

通过命令行运行程序。 它会自动向 QnA Maker API 发送请求，然后输出到控制台窗口。

运行文件：

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)] 

详细了解[请求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
