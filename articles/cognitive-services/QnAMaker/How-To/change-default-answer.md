---
title: 获取默认的答案 QnA Maker
description: 如果与问题没有匹配项，将返回默认的答案。 你可能需要更改标准默认值答案的默认应答。
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979958"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>更改 QnA Maker 资源的默认答案

当找不到答案时，将返回知识库的默认回答。 如果你使用的是客户端应用程序，如[Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)，则可能还会有一个单独的默认应答，指示没有符合分数阈值的答案。

## <a name="set-default-answer-when-you-create-knowledge-base"></a>设置创建知识库时的默认答案

创建新的知识库时，默认的答案文本是设置之一。 如果选择不在创建过程中对其进行设置，则可以稍后通过以下过程进行更改。

## <a name="change-default-answer-in-qna-maker-portal"></a>在 QnA Maker 门户中更改默认答案

当 QnA Maker 服务未返回任何答案时，将返回知识库默认值答案。

1. 登录到[QnA Maker 门户](https://www.qnamaker.ai/)，并从列表中选择知识库。
1. 从导航栏中选择 "**设置**"。
1. 更改 "**管理知识库**" 部分的 "**默认回答文本**" 的值。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="突出显示默认答案文本框的 QnA Maker 门户的屏幕截图。":::

1. 选择 "**保存并训练**" 保存更改。

## <a name="next-steps"></a>后续步骤

* [创建知识库](../How-to/manage-knowledge-bases.md)