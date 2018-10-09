---
title: 资源和密钥管理 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 服务处理两种密钥：订阅密钥和终结点密钥。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: f48b4b7da4990de0efb9b7de5d97731d748f0e7f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040442"
---
# <a name="key-management"></a>密钥管理

QnA Maker 服务处理两种密钥：**订阅密钥**和**终结点密钥**。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

1. **订阅密钥**：这些密钥用来访问 [QnA Maker 管理服务 API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)。 可以使用这些 API 对知识库执行各种 CRUD 操作。  

2. **终结点密钥**：这些密钥用来访问知识库终结点以获取对用户问题的响应。 通常，你将在使用 QnA Maker 服务的聊天机器人/应用代码中使用此终结点。
 
## <a name="subscription-keys"></a>订阅密钥
可以从你在其中创建了 QnA Maker 资源的 Azure 门户中查看和重置订阅密钥。 
1. 在 Azure 门户中转到 QnA Maker 资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 转到“密钥”。

    ![订阅密钥](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>终结点密钥

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录到 [QnA Maker 门户](https://qnamaker.ai)，然后转到“管理密钥”。

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置你的密钥。

    ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为密钥已泄露，请刷新你的密钥。 这可能需要相应地更改应用/机器人代码。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [以另一种语言创建知识库](./language-knowledge-base.md)
