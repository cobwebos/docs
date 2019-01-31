---
title: 创建知识库 - REST、C#
titlesuffix: QnA Maker- Azure Cognitive Services
description: 此 C# 基于 REST 的快速入门详细介绍如何以编程方式创建一个示例 QnA Maker 知识库，该知识库会显示在认知服务 API 帐户的 Azure 仪表板中。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: diberry
ms.openlocfilehash: bf02247008c79c48eedd09e68c81db1aa7634146
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225176"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>快速入门：通过 C# 在 QnA Maker 中创建知识库

本快速入门将指导你完成以编程方式创建和发布示例 QnA Maker 知识库的过程。 QnA Maker 自动从[数据源](../Concepts/data-sources-supported.md)中从半结构化内容（例如常见问题解答）中自动提取问题和解答。 用于知识库的模型是在 API 请求的正文中发送的 JSON 中定义的。 

本快速入门调用了 QnA Maker API：
* [创建知识库](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [获取操作详细信息](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)
* [发布](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) 

## <a name="prerequisites"></a>先决条件

* 最新的 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)。
* 必须已有一个 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)。 若要检索密钥，请在仪表板的“资源管理”下选择“密钥”。 

> [!NOTE] 
> 完整的解决方案文件可从 [Azure-Samples/cognitive-services-qnamaker-csharp GitHub 存储库](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)获得。

## <a name="create-a-knowledge-base-project"></a>创建知识库项目

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

在 Program.cs 的顶部，将单个 using 语句替换为以下行，以向项目添加必要的依赖项：

[!code-csharp[Add the required dependencies](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=1-11 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>添加必需的常量

在 Program 类的顶部，添加以下常量来访问 QnA Maker：

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=17-24 "Add the required constants")]

## <a name="add-the-kb-definition"></a>添加知识库定义

在常量后，添加以下知识库定义：

[!code-csharp[Add the required constants](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=32-57 "Add the required constants")]

## <a name="add-supporting-functions-and-structures"></a>添加支持函数和结构
在 Program 类中添加以下代码块：

[!code-csharp[Add supporting functions and structures](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=62-82 "Add supporting functions and structures")]

## <a name="add-a-post-request-to-create-kb"></a>添加一个 POST 请求来创建知识库

以下代码向 QnA Maker API 发出 HTTPS 请求来创建知识库并接收响应：

[!code-csharp[Add a POST request to create KB](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=91-105 "Add a POST request to create KB")]

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

## <a name="add-get-request-to-determine-creation-status"></a>添加 GET 请求来确定创建状态

检查操作的状态。

[!code-csharp[Add GET request to determine creation status](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=159-170 "Add GET request to determine creation status")]

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

## <a name="add-createkb-method"></a>添加 CreateKB 方法

以下方法创建知识库并重复检查状态。  _create_ **Operation ID** 在 POST 响应标头字段 **Location** 中返回，然后在 GET 请求中用作路由的一部分。 因为知识库创建可能要花费一些时间，所以你需要重复用来检查状态的调用，直到状态为成功或失败。 操作成功后，会在 **resourceLocation** 中返回 KB ID。 

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=176-237 "Add CreateKB method")]

## <a name="add-the-createkb-method-to-main"></a>将 CreateKB 方法添加到 Main

更改 Main 方法来调用 CreateKB 方法：

[!code-csharp[Add CreateKB method](~/samples-qnamaker-csharp/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs?range=239-248 "Add CreateKB method")]

## <a name="build-and-run-the-program"></a>生成并运行程序

生成并运行程序。 它将自动向 QnA Maker API 发送请求来创建知识库，然后它每隔 30 秒轮询一次结果。 每个响应都将输出到控制台窗口中。

创建知识库以后，即可在 QnA Maker 门户 - [My knowledge bases](https://www.qnamaker.ai/Home/MyServices)（我的知识库）页中查看它。 


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
