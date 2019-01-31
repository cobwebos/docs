---
title: 常见问题解答 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 关于 QnA Maker 服务的常见问题解答特选列表有助于快速利用此服务，并获得更理想的结果。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 11/27/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 95a8653d946e9896a13e7ecc9f05592467734576
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208969"
---
# <a name="frequently-asked-questions-for-qna-maker"></a>QnA Maker 常见问题解答

关于 QnA Maker 服务的常见问题解答特选列表有助于快速利用此服务，并获得更理想的结果。

## <a name="manage-the-knowledge-base"></a>管理知识库

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>我不小心删除了一部分 QnA Maker，我该怎么办？ 

所有删除操作都是永久性的，包括问题和答案对、文件、URL、自定义问题和答案、知识库或 Azure 资源。 在删除知识库的任何部分之前，请务必从“设置”页导出知识库。 

### <a name="why-is-my-urlsfiles-not-extracting-question-answer-pairs"></a>为什么我的 URL/文件没有提取问答对？

QnA Maker 可能无法从有效的常见问题解答 URL 中自动提取某些问答 (QnA) 内容。 在这种情况下，可以将 QnA 内容粘贴到 .txt 文件中，并查看该工具是否可以引入它。 或者，也可以通过 [QnA Maker 门户](https://qnamaker.ai)以编辑方式向知识库添加内容。

### <a name="how-large-a-knowledge-base-can-i-create"></a>我能创建多大的知识库？

知识库的大小取决于在创建 QnA Maker 服务时选择的 Azure 搜索的 SKU。 请参阅[此处](./Tutorials/choosing-capacity-qnamaker-deployment.md)了解详细信息。

### <a name="why-cant-i-see-anything-in-the-drop-down-when-i-try-to-create-a-new-knowledge-base"></a>当我尝试创建新知识库时，为什么在下拉列表中看不到任何内容？

你尚未在 Azure 中创建任何 QnA Maker 服务。 请阅读[此文](./How-To/set-up-qnamaker-service-azure.md)了解如何执行该操作。

### <a name="how-do-i-share-a-knowledge-base-with-others"></a>如何与他人共享知识库？

共享在 QnA Maker 服务级别工作，即将共享服务中的所有知识库。 请参阅[此处](./How-To/collaborate-knowledge-base.md)了解如何在知识库上进行协作。

### <a name="can-you-share-a-knowledge-base-with-a-contributor-that-is-not-in-the-same-aad-tenant-to-modify-a-knowledge-base"></a>是否可以与不在同一个 AAD 租户中的参与者共享知识库，以修改知识库？ 

共享基于 Azure 基于角色的访问控制 (RBAC)。 如果可与其他用户共享 Azure 中的任何资源，则也可以共享 QnA Maker。

### <a name="if-you-have-an-app-service-plan-with-5-qnamaker-knowledge-bases-can-you-assign-readwrite-rights-to-5-different-users-so-each-of-them-can-access-only-1-qnamaker-knowledge-base"></a>如果某个应用服务计划具有 5 个 QnAMaker 知识库， 是否可以向 5 个不同的用户分配读/写权限，使得其中每个用户只能访问 1 个 QnAMaker 知识库？

可以共享整个 QnAMaker 服务，但不能共享个体知识库。

### <a name="how-can-i-change-the-default-message-when-no-good-match-is-found"></a>如果找不到良好匹配，如何更改默认消息？

默认消息是应用服务中设置的一部分。
- 在 Azure 门户中转到自己的应用服务资源

![qnamaker 应用服务](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 单击“设置”选项

![qnamaker 应用服务设置](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 更改 **DefaultAnswer** 设置的值
- 重启应用服务

![qnamaker 应用服务重启](./media/qnamaker-faq/qnamaker-appservice-restart.png)

### <a name="why-is-my-sharepoint-link-not-getting-extracted"></a>为什么我的 SharePoint 链接没有被提取？

有关详细信息，请参阅[数据源位置](./Concepts/data-sources-supported.md#data-source-locations)。

### <a name="the-updates-that-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>我对知识库所做的更新没有反映在发布上， 为什么？

每个编辑操作（无论在表更新、测试还是在设置中）需要先保存才能发布。 每次编辑操作后，请确保单击“保存并训练”按钮 **。** 

### <a name="does-the-knowledge-base-support-rich-data-or-multimedia"></a>知识库是否支持丰富的数据或多媒体？

知识库支持 Markdown。 但是，从 URL 自动提取的 HTML 到 Markdown 转换功能受限制。 如果想使用功能齐全的 Markdown，可以直接在表中修改内容，或上传包含丰富内容的知识库。

目前不支持多媒体，例如图像和视频。

### <a name="does-qna-maker-support-non-english-languages"></a>QnA Maker 是否支持非英语语言？

查看有关[支持的语言](./Overview/languages-supported.md)的更多详细信息。

如果你有多种语言的内容，请务必为每种语言创建单独的服务。

## <a name="manage-service"></a>管理服务

### <a name="when-should-i-restart-my-app-service"></a>我何时应该重启应用服务？ 

当警告图标位于“用户设置”[页](https://www.qnamaker.ai/UserSettings)上“终结点密钥”表中知识库版本值旁边时，请刷新应用服务。

### <a name="when-should-i-refresh-my-endpoint-keys"></a>我应该何时刷新终结点密钥？

如果怀疑终结点密钥已被泄漏，请刷新终结点密钥。

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>是否可以将同一 Azure 搜索资源用于使用多种语言的知识库？

若要使用多种语言和多个知识库，用户必须为每种语言创建一个 QnA Maker 资源。 这会为每种语言创建单独的 Azure 搜索服务。 在单个 Azure 搜索服务中混合使用不同语言的知识库会导致结果相关性下降。

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>如何更改 QnA Maker 使用的 Azure 搜索资源的名称？

Azure 搜索资源的名称是 QnA Maker 资源名称加上在末尾追加的一些随机字母。 这使得 QnA Maker 难以区分多个搜索资源。 请创建一个单独的 Azure 搜索服务（以你喜欢的方式对其进行命名）并将其连接到你的 QnA 服务。 步骤类似于[升级 Azure 搜索](How-To/upgrade-qnamaker-service.md#upgrade-azure-search-service)时需要执行的步骤。

## <a name="integrate-with-other-services-including-bots"></a>与其他服务（包括机器人）集成

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>为了使用 QnA Maker，需要使用 Bot Framework 吗？

否，不需要将 Bot Framework 与 QnA Maker 一起使用。 但是，QnA Maker 作为 Azure 机器人服务中的几个模板之一提供。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

### <a name="how-can-i-create-a-bot-with-qna-maker"></a>如何使用 QnA Maker 创建机器人？

请遵照[此文档](./Tutorials/create-qna-bot.md)中的说明使用 Azure 机器人服务创建机器人。

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>如何将 QnA Maker 服务嵌入网站？

按照以下步骤将 QnA Maker 服务作为 Web 聊天控件嵌入网站：

1. 按照[此处](./Tutorials/create-qna-bot.md)的说明创建常见问题解答机器人。
2. 按照[此处](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天

## <a name="data-storage"></a>数据存储

### <a name="what-data-is-stored-and-where-is-it-stored"></a>存储哪些数据以及存储在何处？ 

创建 QnA Maker 服务时，你选择了 Azure 区域。 知识库和日志文件存储在此区域中。 
