---
title: 快速入门：使用 cURL 从知识库获取答案 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 此快速入门详细介绍如何使用 cURL 从知识库获取答案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: ad4b40d649d4e4cbc17d6aec5d8bc7308012b927
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60913732"
---
# <a name="quickstart-get-answer-from-knowledge-base-using-curl"></a>快速入门：使用 cURL 从知识库获取答案

此基于 cURL 的快速入门详细介绍如何从知识库获取答案。

## <a name="prerequisites"></a>必备组件

* 最新的 [cURL](https://curl.haxx.se/)。
* 必须拥有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)，并拥有[包含问题和答案的知识库](../Tutorials/create-publish-query-in-portal.md)。

## <a name="publish-to-get-endpoint"></a>通过发布来获取终结点

准备好从知识库中生成问题的答案后，请[发布](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)知识库。

## <a name="use-production-endpoint-with-curl"></a>通过 cURL 使用生产终结点

发布知识库后，“发布”页将显示 HTTP 请求设置以生成答案。 “CURL”选项卡显示了从命令行工具 [CURL](https://www.getpostman.com) 生成答案所需的设置。

[![发布结果](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png)](../media/qnamaker-use-to-generate-answer/curl-command-on-publish-page.png#lightbox)

若要使用 CURL 生成答案，请完成以下步骤：

1. 复制“CURL”选项卡中的文本。 
1. 打开一个命令行或终端，并粘贴文本。
1. 编辑为与知识库相关的问题。 注意不要删除围绕此问题的包含 JSON。
1. 输入命令。 
1. 响应包括有关答案的相关信息。 

    ```bash
    > curl -X POST https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/1111f8c-d01b-4698-a2de-85b0dbf3358c/generateAnswer -H "Authorization: EndpointKey 111841fb-c208-4a72-9412-03b6f3e55ca1" -H "Content-type: application/json" -d "{'question':'How do I programmatically update my Knowledge Base?'}"
    {
      "answers": [
        {
          "questions": [
            "How do I programmatically update my Knowledge Base?"
          ],
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600",
          "score": 100.0,
          "id": 18,
          "source": "Custom Editorial",
          "metadata": [
            {
              "name": "category",
              "value": "api"
            }
          ]
        }
      ]
    }
    ```

## <a name="use-staging-endpoint-with-curl"></a>与 cURL 配合使用临时终结点

如果你想要从过渡终结点中获得答案，使用`isTest`正文属性。

```json
isTest:true
```

## <a name="next-steps"></a>后续步骤

发布页还提供了使用 Postman [生成答案](get-answer-from-kb-using-postman.md)的信息。 

> [!div class="nextstepaction"]
> [生成答案时使用元数据](../How-to/metadata-generateanswer-usage.md)
