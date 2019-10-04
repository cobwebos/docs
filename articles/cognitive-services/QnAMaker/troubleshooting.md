---
title: 故障排除-QnA Maker
titleSuffix: Azure Cognitive Services
description: 有关 QnA Maker 服务的最常见问题的特选列表将有助于更快地采用服务, 并获得更好的结果。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 00d3d63ff1a5b4d5dab0534e039145b97091af87
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802156"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker 疑难解答

有关 QnA Maker 服务的最常见问题的特选列表将有助于更快地采用服务, 并获得更好的结果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="how-to-get-the-qnamaker-service-endpoint"></a>如何获取 QnAMaker 服务终结点

当你联系 QnAMaker 支持部门或 UserVoice 时，QnAMaker service 终结点对于调试很有用。 此终结点是如下格式的 URL： https://your-resource-name.azurewebsites.net 。
    
1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![Azure 门户中的 QnAMaker Azure 资源组](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择与 QnA Maker 资源关联的应用服务。 通常，名称是相同的。

     ![选择“QnAMaker 应用服务”](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. "概述" 部分提供了终结点 URL

    ![QnAMaker 终结点](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="use-the-help-bot-in-the-qna-maker-portal"></a>在 QnA Maker 门户中使用帮助机器人

QnA Maker 在 QnA Maker 门户中提供**帮助**机器人来帮助你。 每个网页上都提供了帮助机器人。 机器人使用 QnA Maker 来提供答案, 并向机器人提供[ C#机器人框架代码项目](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support), 以便你可以快速启动并运行你自己的应答机器人。 

![![QnA Maker 提供了 * * 帮助 * * 智能机器人应用程序以帮助您在 QnA Maker 门户中的。](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png)](./media/qnamaker-faq/use-qna-maker-help-bot-to-learn-qna-maker-service.png#lightbox)

## <a name="manage-the-knowledge-base"></a>管理知识库

### <a name="i-accidentally-deleted-a-part-of-my-qna-maker-what-should-i-do"></a>我不小心删除了一部分 QnA Maker，我该怎么办？ 

请勿删除随 QnA Maker 资源（如搜索或 Web 应用）一起创建的任何 Azure 服务。 这些是 QnA Maker 工作所必需的，如果删除一个，QnA Maker 将停止正常工作。

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

### <a name="i-deleted-my-existing-search-service-how-can-i-fix-this"></a>我删除了现有搜索服务。 如何解决此问题？

如果删除 Azure 搜索索引，则该操作是最终的，无法恢复索引。 

### <a name="i-deleted-my-testkb-index-in-my-search-service-how-can-i-fix-this"></a>我删除`testkb`了搜索服务中的索引。 如何解决此问题？ 

无法恢复你的旧数据。 创建新的 QnA Maker 资源并再次创建知识库。

### <a name="when-should-i-refresh-my-endpoint-keys"></a>我应该何时刷新终结点密钥？

如果怀疑终结点密钥已被泄漏，请刷新终结点密钥。

### <a name="can-i-use-the-same-azure-search-resource-for-knowledge-bases-using-multiple-languages"></a>是否可以将同一 Azure 搜索资源用于使用多种语言的知识库？

若要使用多种语言和多个知识库，用户必须为每种语言创建一个 QnA Maker 资源。 这会为每种语言创建单独的 Azure 搜索服务。 在单个 Azure 搜索服务中混合使用不同语言的知识库会导致结果相关性下降。

### <a name="how-can-i-change-the-name-of-the-azure-search-resource-used-by-qna-maker"></a>如何更改 QnA Maker 使用的 Azure 搜索资源的名称？

Azure 搜索资源的名称是 QnA Maker 资源名称加上在末尾追加的一些随机字母。 这使得 QnA Maker 难以区分多个搜索资源。 请创建一个单独的 Azure 搜索服务（以你喜欢的方式对其进行命名）并将其连接到你的 QnA 服务。 步骤与[升级 Azure 搜索](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-search-service)所需执行的步骤类似。

### <a name="when-qna-maker-returns-runtime-core-is-not-initialized-how-do-i-fix-it"></a>当 QnA Maker 返回`Runtime core is not initialized,`如何修复它？

应用服务的磁盘空间可能已满。 修复磁盘空间的步骤:

1. 在[Azure 门户](https://portal.azure.com)中, 选择 QnA Maker 的应用服务, 然后停止服务。
1. 仍在应用服务中, 选择 "**开发工具**", 然后依次选择 "**高级工具**"、"**开始**"。 这将打开一个新的浏览器窗口。
1. 选择 "**调试控制台**", 然后选择 " **CMD** " 以打开命令行工具。 
1. 导航到_site/wwwroot/Data/QnAMaker/_ directory。
1. 删除名称以开头`rd`的所有文件夹。 

    **请勿删除**以下内容:

    * KbIdToRankerMappings 文件
    * EndpointSettings 文件
    * EndpointKeys 文件夹 

1. 启动应用服务。
1. 访问知识库, 验证它是否正常工作。 


## <a name="integrate-with-other-services-including-bots"></a>与其他服务（包括机器人）集成

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>为了使用 QnA Maker，需要使用 Bot Framework 吗？

不需要, 不需要将[机器人框架](https://github.com/Microsoft/botbuilder-dotnet)与 QnA Maker 一起使用。 不过, QnA Maker 是在 [Azure Bot 服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)中提供的几个模板之一。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

### <a name="how-can-i-create-a-new-bot-with-qna-maker"></a>如何使用 QnA Maker 创建新的机器人？

请遵照[此文档](./Tutorials/create-qna-bot.md)中的说明使用 Azure 机器人服务创建机器人。

### <a name="how-do-i-use-a-different-knowledge-base-with-an-existing-azure-bot-service"></a>如何实现在现有 Azure bot 服务中使用其他知识库？

你需要了解有关知识库的以下信息：

* 知识库 ID。
* 发布后在 "设置" 页上的 "**设置**" 页上，知识库的已发布终结点自定义子域名称称为 `host`。
* 知识库的已发布终结点项-发布后在 "**设置**" 页上找到。 

有关此信息，请在 Azure 门户中转到机器人的应用服务。 在**设置-> 配置-> 应用程序设置**中，更改这些值。  

知识库的终结点键在 ABS 服务`QnAAuthkey`中进行了标记。 

### <a name="can-two-or-more-client-applications-share-a-knowledge-base"></a>两个或多个客户端应用程序是否可以共享知识库？ 

是的，可以从任意数量的客户端查询知识库。 如果来自知识库的响应显示速度缓慢或超时，请考虑升级与知识库关联的应用服务的服务层。

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>如何将 QnA Maker 服务嵌入网站？

按照以下步骤将 QnA Maker 服务作为 Web 聊天控件嵌入网站：

1. 按照[此处](./Tutorials/create-qna-bot.md)的说明创建常见问题解答机器人。
2. 按照[此处](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天

## <a name="data-storage"></a>数据存储

### <a name="what-data-is-stored-and-where-is-it-stored"></a>存储哪些数据以及存储在何处？ 

创建 QnA Maker 服务时，你选择了 Azure 区域。 知识库和日志文件存储在此区域中。 
