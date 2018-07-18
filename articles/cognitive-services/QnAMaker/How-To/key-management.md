---
title: 密钥管理 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何管理 QnA Maker 密钥
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366293"
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
