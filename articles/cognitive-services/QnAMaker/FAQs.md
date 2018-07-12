---
title: 常见问题解答 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 常见问题
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: a6bf32549715d0357771b3f3b0ff72f64788ec20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366294"
---
# <a name="frequently-asked-questions"></a>常见问题

## <a name="why-is-my-urlsfiles-is-not-extracting-question-answer-pairs"></a>为什么我的 URL/文件没有提取问答对？

QnA Maker 可能无法从有效的常见问题解答 URL 中自动提取某些问答 (QnA) 内容。 在这种情况下，可以将 QnA 内容粘贴到 .txt 文件中，并查看该工具是否可以引入它。 或者，也可以通过编辑方式向知识库添加内容。

## <a name="how-large-a-knowledge-base-can-i-create"></a>我能创建多大的知识库？

知识库的大小取决于在创建 QnA Maker 服务时选择的 Azure 搜索的 SKU。 请参阅[此处](./Tutorials/choosing-capacity-qnamaker-deployment.md)了解详细信息。

## <a name="why-do-i-not-see-anything-in-the-drop-down-for-when-i-try-to-create-a-new-knowledge-base"></a>当我尝试创建新知识库时，为什么我在下拉列表中看不到任何内容？

你尚未在 Azure 中创建任何 QnA Maker 服务。 请参阅[此处](./How-To/set-up-qnamaker-service-azure.md)了解如何执行该操作。

## <a name="how-do-i-share-a-knowledge-base-with-other"></a>如何与他人共享知识库？

共享在 QnA Maker 服务级别工作，即将共享服务中的所有知识库。 请参阅[此处](./How-To/collaborate-knowledge-base.md)了解如何在知识库上进行协作。

## <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>如果找不到良好匹配，如何更改默认消息？

默认消息是应用服务中设置的一部分。
- 转到 Azure 门户中的应用服务资源

![qnamaker 应用服务](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 单击“设置”选项

![qnamaker 应用服务设置](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 更改 **DefaultAnswer** 设置的值
- 重启应用服务

![qnamaker 应用服务重启](./media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>为什么我的 SharePoint 链接没有被提取？

该工具目前仅解析公共 URL，不支持经过身份验证的数据源。 或者，你可以下载该文件并使用文件上传选项来提取问题和答案。


## <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>我对知识库所做的更新没有反映在发布上， 为什么？

每个编辑操作（无论在表更新、测试还是在设置中）需要先保存才能发布。 每次编辑操作后，请确保单击“保存并定型”按钮。

## <a name="when-should-i-refresh-my-endpoint-keys"></a>我应该何时刷新终结点密钥？

如果怀疑终结点密钥已被泄漏，应刷新终结点密钥。

## <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>知识库是否支持丰富的数据或多媒体？

知识库支持 Markdown。 但是，从 URL 自动提取的 HTML 到 Markdown 转换功能受限制。 如果想使用功能齐全的 Markdown，可以直接在表中修改内容，或上传包含丰富内容的知识库。

目前不支持多媒体，例如图像和视频。

## <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker 是否支持非英语语言？

查看有关[支持的语言](./Overview/languages-supported.md)的更多详细信息。

如果你有多种语言的内容，请务必为每种语言创建单独的服务。

## <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>为了使用 QnA Maker，需要使用 Bot Framework 吗？

否，不需要将 Bot Framework 与 QnA Maker 一起使用。 但是，QnA Maker 作为 Azure 机器人服务中的几个模板之一提供。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

## <a name="how-can-i-create-a-bot-with-qna-maker"></a>如何使用 QnA Maker 创建机器人？

按照[此](./Tutorials/create-qna-bot.md)文档中的说明使用 Azure 机器人创建机器人。

## <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>如何将 QnA Maker 服务嵌入网站？

按照以下步骤将 QnA Maker 服务作为 Web 聊天控件嵌入网站：

1. 按照[此处](./Tutorials/create-qna-bot.md)的说明创建常见问题解答机器人。
2. 按照[此处](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天


