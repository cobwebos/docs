---
title: 资源和密钥管理 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 服务处理两种密钥：订阅密钥和终结点密钥。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c1d8104f94d3f215108000b904848f3b968492ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372798"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>如何管理 QnA Maker 中的密钥

QnA Maker 服务处理两种密钥：**订阅密钥**和**终结点密钥**。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

1. **订阅密钥**：这些密钥用来访问 [QnA Maker 管理服务 API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)。 这些 API 允许你执行知识库编辑。  

2. **终结点密钥**：这些密钥用来访问知识库终结点以获取对用户问题的响应。 通常，你将在使用 QnA Maker 服务的聊天机器人或客户端应用程序代码中使用此终结点。
 
## <a name="subscription-keys"></a>订阅密钥
可以从你在其中创建了 QnA Maker 资源的 Azure 门户中查看和重置订阅密钥。 
1. 在 Azure 门户中转到 QnA Maker 资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 转到“密钥”。

    ![订阅密钥](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>终结点密钥

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录 [QnA Maker 门户](https://qnamaker.ai)，转到你的个人资料，然后单击“服务设置”。

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置你的密钥。

    ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为密钥已泄露，请刷新你的密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [以另一种语言创建知识库](./language-knowledge-base.md)
