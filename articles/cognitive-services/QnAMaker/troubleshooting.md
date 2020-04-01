---
title: 故障排除 - QnA 制造商
description: 有关 QnA Maker 服务的最常见问题列表将帮助您更快地采用该服务，并取得更好的结果。
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 310cfe0cc8c1f647c09b8e0efb435a1ab326ff62
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474954"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA 制造商的故障排除

有关 QnA Maker 服务的最常见问题列表将帮助您更快地采用该服务，并取得更好的结果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>管理预测

<details>
<summary><b>如何提高查询预测的吞吐量性能？</b></summary>

**答**：吞吐量性能问题表明您需要扩展应用服务和认知搜索。 请考虑向认知搜索添加副本以提高性能。

了解有关[定价层的更多。](Concepts/azure-resources.md)
</details>

<details>
<summary><b>如何获取 QnAMaker 服务终结点</b></summary>

**答**：当您联系 QnAMaker 支持或用户语音时，QnAMaker 服务终结点可用于调试目的。 终结点是此窗体中的 URL： `https://your-resource-name.azurewebsites.net`。

1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![Azure 门户中的 QnAMaker Azure 资源组](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择与 QnA 制造商资源关联的应用服务。 通常，名称相同。

     ![选择“QnAMaker 应用服务”](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. 终结点 URL 在"概述"部分提供

    ![QnAMaker 端点](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>管理知识库

<details>
<summary><b>我不小心删除了一部分 QnA Maker，我该怎么办？</b></summary>

**答**：不要删除与 QnA Maker 资源（如搜索或 Web 应用）一起创建的任何 Azure 服务。 这些是 QnA 制造商工作所必需的，如果您删除一个，QnA 制造商将停止正常工作。

所有删除操作都是永久性的，包括问题和答案对、文件、URL、自定义问题和答案、知识库或 Azure 资源。 在删除知识库的任何部分之前，请务必从“设置”**** 页导出知识库。

</details>

<details>
<summary><b>为什么我的 URL/文件没有提取问答对？</b></summary>

**答**：QnA Maker 可能无法自动从有效的常见问题解答 URL 中提取一些问答 （QnA） 内容。 在这种情况下，可以将 QnA 内容粘贴到 .txt 文件中，并查看该工具是否可以引入它。 或者，也可以通过 [QnA Maker 门户](https://qnamaker.ai)以编辑方式向知识库添加内容。

</details>

<details>
<summary><b>我能创建多大的知识库？</b></summary>

**答**：知识库的大小取决于您在创建 QnA Maker 服务时选择的 Azure 搜索 SKU。 请参阅[此处](./Tutorials/choosing-capacity-qnamaker-deployment.md)了解详细信息。

</details>

<details>
<summary><b>当我尝试创建新知识库时，为什么在下拉列表中看不到任何内容？</b></summary>

**答**：您尚未在 Azure 中创建任何 QnA Maker 服务。 请阅读[此文](./How-To/set-up-qnamaker-service-azure.md)了解如何执行该操作。

</details>

<details>
<summary><b>如何与他人共享知识库？</b></summary>

**答**：在 QnA Maker 服务级别共享工作，即服务中的所有知识库都将共享。 请参阅[此处](./How-To/collaborate-knowledge-base.md)了解如何在知识库上进行协作。

</details>

<details>
<summary><b>是否可以与不在同一个 AAD 租户中的参与者共享知识库，以修改知识库？</b></summary>

**答**：共享基于 Azure 基于角色的访问控制 （RBAC）。 如果可与其他用户共享 Azure 中的任何资源，则也可以共享 QnA Maker。__

</details>

<details>
<summary><b>如果您有一个应用服务计划，有 5 个 QnAMaker 知识库。您能否为 5 个不同的用户分配读取/写入权限，以便每个用户只能访问 1 个 QnAMaker 知识库？</b></summary>

**答**：您可以共享整个 QnAMaker 服务，而不是单个知识库。

</details>

<details>
<summary><b>如果找不到良好匹配，如何更改默认消息？</b></summary>

**答**：默认消息是应用服务中设置的一部分。
- 在 Azure 门户中转到自己的应用服务资源

![qnamaker 应用服务](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 单击“设置”**** 选项

![qnamaker 应用服务设置](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 更改 **DefaultAnswer** 设置的值
- 重启应用服务

![qnamaker 应用服务重启](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>为什么我的 SharePoint 链接没有被提取？</b></summary>

**答案**： 有关详细信息[，请参阅数据源位置](./Concepts/knowledge-base.md#data-source-locations)。

</details>

<details>
<summary><b>我对知识库所做的更新不会反映在发布上。为什么不呢？</b></summary>

**答**：每次编辑操作（无论是在表更新、测试还是设置中）都需要保存才能发布。 请务必在每次编辑操作后单击"**保存"和"训练"** 按钮。

</details>

<details>
<summary><b>知识库是否支持丰富的数据或多媒体？</b></summary>

**答**：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>文件和 URL 的多媒体自动提取

* URLS - 有限的 HTML 到标记转换功能。
* 文件 - 不支持

#### <a name="answer-text-in-markdown"></a>以标记形式回答文本
一旦 QnA 集位于知识库中，您可以编辑答案的标记文本，以包括公共 URL 中可用的媒体链接。


</details>

<details>
<summary><b>QnA Maker 是否支持非英语语言？</b></summary>

**答**：查看有关[受支持语言](./Overview/languages-supported.md)的更多详细信息。

如果你有多种语言的内容，请务必为每种语言创建单独的服务。

</details>

## <a name="manage-service"></a>管理服务

<details>
<summary><b>我何时应该重启应用服务？</b></summary>

**答**：当警告图标位于 **"用户设置"**[页上](https://www.qnamaker.ai/UserSettings)**的"终结点键**"表中的知识库的版本值旁边时，请刷新应用服务。

</details>

<details>
<summary><b>我删除了现有的搜索服务。如何解决此问题？</b></summary>

**答**：如果删除 Azure 认知搜索索引，则操作为最终操作，无法恢复该索引。

</details>

<details>
<summary><b>我在搜索服务中删除`testkb`了索引。如何解决此问题？</b></summary>

**答**：无法恢复您的旧数据。 创建新的 QnA Maker 资源，然后再次创建您的知识库。

</details>

<details>
<summary><b>我应该何时刷新终结点密钥？</b></summary>

**答**：如果您怀疑终结点密钥已泄露，请刷新它们。

</details>

<details>
<summary><b>是否可以使用多种语言对知识库使用相同的 Azure 认知搜索资源？</b></summary>

**答**：要使用多种语言和多种知识库，用户必须为每个语言创建 QnA Maker 资源。 这将创建单独的 Azure 搜索服务每种语言。 在单个 Azure 搜索服务中混合使用不同语言的知识库会导致结果相关性下降。

</details>

<details>
<summary><b>如何更改 QnA Maker 使用的 Azure 认知搜索资源的名称？</b></summary>

**答**：Azure 认知搜索资源的名称是 QnA Maker 资源名称，末尾附加了一些随机字母。 这使得 QnA Maker 难以区分多个搜索资源。 创建单独的搜索服务（按您希望的方式命名该服务），并将其连接到 QnA 服务。 这些步骤与[升级 Azure 搜索](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)所需的步骤类似。

</details>

<details>
<summary><b>当 QnA`Runtime core is not initialized,`制造商返回时，如何修复它？</b></summary>

**答**：应用服务的磁盘空间可能已满。 修复磁盘空间的步骤：

1. 在[Azure 门户](https://portal.azure.com)中，选择 QnA Maker 的应用服务，然后停止该服务。
1. 在应用服务上时，选择**开发工具**，然后选择**高级工具**，然后**转到**。 这将打开一个新的浏览器窗口。
1. 选择**调试控制台**，然后**选择 CMD**以打开命令行工具。
1. 导航到_站点/wwwroot/数据/QnAMaker/_ 目录。
1. 删除其名称以`rd`开头的所有文件夹。

    **不要删除**以下内容：

    * KbIdToRanker 映射.txt 文件
    * 端点设置.json 文件
    * 终结点键文件夹

1. 启动应用服务。
1. 访问您的知识库以验证它现在是否有效。

</details>

## <a name="integrate-with-other-services-including-bots"></a>与其他服务（包括机器人）集成

<details>
<summary><b>为了使用 QnA Maker，需要使用 Bot Framework 吗？</b></summary>

**答**：不，您不需要将[机器人框架](https://github.com/Microsoft/botbuilder-dotnet)与 QnA 制造商一起使用。 但是，QnA Maker 是 [Azure 机器人服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)中的多个模板之一。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

</details>

<details>
<summary><b>如何使用 QnA Maker 创建新的机器人？</b></summary>

**答**：按照[本文档](./Quickstarts/create-publish-knowledge-base.md)中的说明使用 Azure 机器人服务创建自动程序。

</details>

<details>
<summary><b>如何使用现有 Azure 自动程序服务使用不同的知识库？</b></summary>

**答**：您需要获得以下有关知识库的信息：

* 知识库 ID。
* 知识库的已发布终结点自定义子域名（称为`host`）在发布后在 **"设置"** 页上找到。
* 知识库的已发布终结点密钥 - 发布后在 **"设置"** 页上找到。

使用此信息，请转到 Azure 门户中的自动程序应用服务。 在**设置 -> 配置 -> 应用程序设置下**，更改这些值。

知识库的终结点密钥在 ABS 服务`QnAAuthkey`中标记。

</details>

<details>
<summary><b>两个或多个客户端应用程序可以共享知识库吗？</b></summary>

**答**：是的，可以从任意数量的客户端查询知识库。 如果来自知识库的响应似乎很慢或超时，请考虑升级与知识库关联的应用服务的服务层。

</details>

<details>
<summary><b>如何将 QnA Maker 服务嵌入网站？</b></summary>

**答**：按照以下步骤将 QnA Maker 服务嵌入到您的网站中，作为网络聊天控件：

1. 按照[此处](./Quickstarts/create-publish-knowledge-base.md)的说明创建常见问题解答机器人。
2. 按照[此处](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天

</details>

## <a name="data-storage"></a>数据存储

<details>
<summary><b>存储哪些数据以及存储在何处？</b></summary>

**答**：

创建 QnA Maker 服务时，你选择了 Azure 区域。 知识库和日志文件存储在此区域中。

</details>