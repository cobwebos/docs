---
title: 获取默认的答案 QnA Maker
description: 如果与问题没有匹配项，将返回默认的答案。 你可能需要更改标准默认值答案的默认应答。
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: d37e63d84be58e6ccd2f1e23a1344961d39ffa01
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054163"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>更改 QnA Maker 资源的默认答案

当找不到答案时，将返回知识库的默认回答。 如果你使用的是客户端应用程序，如[Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)，则可能还会有一个单独的默认应答，指示没有符合分数阈值的答案。

## <a name="types-of-default-answer"></a>默认答案的类型

知识库中有两种类型的默认答案。 务必了解每个在预测查询中返回的方式和时间：


|问题类型|答案说明|
|--|--|
|未确定答案时的 KB 答案|`No good match found in KB.`-当[GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)找不到问题的匹配答案时，将 `DefaultAnswer` 返回应用服务的设置。 同一 QnA Maker 资源中的所有知识库共享相同的默认答案文本。<br>可以通过应用服务管理 Azure 门户中的设置，也可以通过 REST Api 来[获取](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings)或[更新](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings)设置。|
|跟进提示说明文本|在会话流中使用跟进提示符时，您可能不需要在 QnA 对中进行应答，因为您希望用户从后续提示中进行选择。 在这种情况下，设置特定的文本，方法是设置默认的应答文本，并在每次进行预测后提示时返回。 该文本旨在作为说明文本显示给所选的后续提示。 此默认回答文本的一个示例是 `Please select from the following choices` 。 本文档的后面几节将对此配置进行说明。 还可以设置为使用 REST API 的知识库定义的 `defaultAnswerUsedForExtraction` 一部分[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。|

### <a name="client-application-integration"></a>客户端应用程序集成

对于客户端应用程序（如使用**Azure 机器人服务**的机器人），可以从以下常见方案中进行选择：

* 使用知识库的设置
* 使用客户端应用程序中的不同文本来区分返回答案但不满足分数阈值的时间。 此文本可以是存储在代码中的静态文本，也可以存储在客户端应用程序的 "设置" 列表中。

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>创建知识库时设置跟进提示的默认答案

创建新的知识库时，默认的答案文本是设置之一。 如果选择不在创建过程中对其进行设置，则可以稍后通过以下过程进行更改。

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>在 QnA Maker 门户中更改跟进提示的默认答案

当 QnA Maker 服务未返回任何答案时，将返回知识库默认值答案。

1. 登录到[QnA Maker 门户](https://www.qnamaker.ai/)，并从列表中选择知识库。
1. 从导航栏中选择 "**设置**"。
1. 更改 "**管理知识库**" 部分的 "**默认回答文本**" 的值。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="突出显示默认答案文本框的 QnA Maker 门户的屏幕截图。":::

1. 选择 "**保存并训练**" 保存更改。

## <a name="next-steps"></a>后续步骤

* [创建知识库](../How-to/manage-knowledge-bases.md)
