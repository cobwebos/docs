---
title: 快速入门：从知识库获取答案 - REST、Node.js - QnA Maker
description: 此基于 Node.js REST 的快速入门详细介绍如何以编程方式从知识库获取答案。
ms.topic: quickstart
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: e8077235852e8776c4e52403cbac3e4bccc6c4f1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109781"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>快速入门：使用 Node.js 从知识库获取问题的答案

本快速入门详细介绍如何以编程方式从已发布的 QnA Maker 知识库获取答案。 知识库包含来自[数据源](../Concepts/knowledge-base.md)的问题和答案，例如常见问题解答。 [问题](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)将发送到 QnA Maker 服务。 [响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)包含最常见的预测答案。

[参考文档](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [示例](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>必备条件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在适用于 QnA Maker 资源的 Azure 仪表板的“资源管理”下选择“密钥”。  
* “发布”页设置。  如果没有已发布的知识库，请创建一个空的知识库，接着“设置”页上导入一个知识库，然后进行发布。  可以下载并使用[这个基本的知识库](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)。

    发布页设置包括 POST 路由值、Host 值和 EndpointKey 值。

    ![发布设置](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>创建 Node.js 文件

打开 VSCode 并创建名为 `get-answer.js` 的新文件。

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 `get-answer.js` 文件顶部，向项目添加必需的依赖项：

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量

接下来，添加必需的常量来访问 QnA Maker。 发布知识库后，这些值会出现在“发布”页上。 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>添加一个 POST 请求来发送问题并获取答案

以下代码向 QnA Maker API 发出 HTTPS 请求，以便向知识库发送问题并接收响应：

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

`Authorization` 标头的值包括字符串 `EndpointKey`。

## <a name="install-the-dependencies"></a>安装依赖项

从命令行安装依赖项：

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>运行程序

通过命令行运行程序。 它会自动向 QnA Maker API 发送请求，然后输出到控制台窗口。

运行文件：

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

详细了解[请求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)和[响应](../how-to/metadata-generateanswer-usage.md#generateanswer-response)。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://go.microsoft.com/fwlink/?linkid=2092179)
