---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424178"
---
此基于 Postman 的快速入门详细介绍如何从知识库获取答案。

## <a name="prerequisites"></a>先决条件

* 最新 [Postman](https://www.getpostman.com/)  。
* 必须拥有 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)，并拥有[包含问题和答案的知识库](../Tutorials/create-publish-query-in-portal.md)。 

## <a name="publish-to-get-endpoint"></a>通过发布来获取终结点

准备好从知识库中生成问题的答案后，请[发布](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)知识库。

## <a name="use-production-endpoint-with-postman"></a>通过 Postman 使用生产终结点

发布知识库后，“发布”页将显示 HTTP 请求设置以生成答案  。 默认视图显示从 [Postman](https://www.getpostman.com) 生成答案所需的设置。

下图中的黄色数字表示以下步骤中要使用的名称/值对。

[![发布结果](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

若要使用 Postman 生成答案，请完成以下步骤：

1. 打开 Postman。 如果系统提示选择“构建基块”，请选择“基本请求”构建基块  。 将“请求名称”设置为 `Generate QnA Maker answer`，将“集合”设置为 `Generate QnA Maker answers`   。 如果不想保存到集合中，请选择“取消”按钮  。
1. 在工作区中，选择“POST”的 HTTP 方法  。

    [![在 Postman 中，设置 POST 方法](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. 对于 URL，连接 HOST 值（图中的 #2）和 Post 值（图中的 #1）以创建完整的 URL。 完整示例 URL 如下所示： 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![在 Postman 中，设置完整的 URL](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. 选择 URL 下的“标头”选项卡，然后选择“批量编辑”   。 

1. 将标头（图中的 #3 和 #4）复制到文本区域。

    [![在 Postman 中，设置标头](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. 选择“正文”选项卡  。
1. 选择“原始”格式并输入表示问题的 JSON（图中的 #5）  。

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![在 Postman 中，设置正文 JSON 值](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. 选择“发送”按钮  。
1. 答复包含答案以及可能对客户端应用程序很重要的其他信息。 

    [![在 Postman 中，设置正文 JSON 值](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>使用暂存终结点

如果想要从暂存终结点获取答案，请使用 `isTest` 正文属性追加​​ URL。
