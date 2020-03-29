---
title: 获取默认答案 - QnA 制造商
description: 当与问题不匹配时，将返回默认答案。 您可能希望从标准默认答案更改默认答案。
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843271"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>为知识库设置默认答案

当与问题不匹配时，将返回默认答案。 您可能希望从标准默认答案更改默认答案。

## <a name="change-default-answer"></a>更改默认答案

1. 转到 [Azure 门户](https://portal.azure.com)并导航到表示所创建的 QnA Maker 服务的资源组。

2. 单击以打开“应用服务”****。

    ![在 Azure 门户中访问 QnA Maker 的应用服务](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. 单击“应用程序设置”****，然后将 **DefaultAnswer** 字段编辑为所需的默认响应。 单击“保存”。****

    ![选择“应用程序设置”，然后编辑 QnA Maker 的 DefaultAnswer](../media/qnamaker-concepts-confidencescore/change-response.png)

4. 重启应用服务

    ![更改 DefaultAnswer 后，重启 QnA Maker 应用服务](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>后续步骤

* [使用 QnA 制造商和 LUIS 创建机器人](../tutorials/integrate-qnamaker-luis.md)