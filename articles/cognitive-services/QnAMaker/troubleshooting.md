---
title: 故障排除-QnA Maker
description: 有关 QnA Maker 服务的最常见问题的特选列表将有助于更快地采用服务，并获得更好的结果。
ms.topic: troubleshooting
ms.date: 02/21/2020
ms.author: diberry
ms.openlocfilehash: 37aae8e67ccc84a6e22be8ce8a304fc4884ead52
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273398"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker 疑难解答

有关 QnA Maker 服务的最常见问题的特选列表将有助于更快地采用服务，并获得更好的结果。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

<details>
<summary><b>如何获取 QnAMaker 服务终结点</b></summary>

**答**：当你联系 QnAMaker 支持部门或 UserVoice 时，QnAMaker service 终结点对于调试很有用。 此终结点是如下格式的 URL： https://your-resource-name.azurewebsites.net。

1. 在 [Azure 门户](https://portal.azure.com)中转到 QnAMaker 服务（资源组）

    ![Azure 门户中的 QnAMaker Azure 资源组](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择与 QnA Maker 资源关联的应用服务。 通常，名称是相同的。

     ![选择“QnAMaker 应用服务”](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. "概述" 部分提供了终结点 URL

    ![QnAMaker 终结点](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>管理知识库

<details>
<summary><b>我意外删除了部分 QnA Maker，我该怎么办？</b></summary>

**答**：不要删除与 QnA Maker 资源（如搜索或 Web 应用）一起创建的任何 Azure 服务。 这些是 QnA Maker 工作所必需的，如果删除一个，QnA Maker 将停止正常工作。

所有删除操作都是永久性的，包括问题和答案对、文件、URL、自定义问题和答案、知识库或 Azure 资源。 在删除知识库的任何部分之前，请务必从“设置”页导出知识库。

</details>

<details>
<summary><b>为什么我的 URL/file 未提取问题答案对？</b></summary>

**答**： QnA Maker 无法自动提取有效常见问题 url 中的一些问题和答案（QnA）内容。 在这种情况下，可以将 QnA 内容粘贴到 .txt 文件中，并查看该工具是否可以引入它。 或者，也可以通过 [QnA Maker 门户](https://qnamaker.ai)以编辑方式向知识库添加内容。

</details>

<details>
<summary><b>我可以创建知识库的大小？</b></summary>

**答**：知识库的大小取决于创建 QnA Maker 服务时所选择的 Azure 搜索的 SKU。 请参阅[此处](./Tutorials/choosing-capacity-qnamaker-deployment.md)了解详细信息。

</details>

<details>
<summary><b>尝试创建新知识库时，为什么无法在下拉箭头中看到任何内容？</b></summary>

**答**：尚未在 Azure 中创建任何 QnA Maker 服务。 请阅读[此文](./How-To/set-up-qnamaker-service-azure.md)了解如何执行该操作。

</details>

<details>
<summary><b>如何实现与他人共享知识库？</b></summary>

**答**：共享在 QnA Maker 服务级别工作，即服务中的所有知识库将被共享。 请参阅[此处](./How-To/collaborate-knowledge-base.md)了解如何在知识库上进行协作。

</details>

<details>
<summary><b>是否可以与不在同一 AAD 租户中的参与者共享知识库来修改知识库？</b></summary>

**答案**：共享基于 Azure 基于角色的访问控制（RBAC）。 如果可与其他用户共享 Azure 中的任何资源，则也可以共享 QnA Maker。

</details>

<details>
<summary><b>如果有5个 QnAMaker 知识库的应用服务计划。能否为5个不同的用户分配读/写权限，使每个用户只能访问1个 QnAMaker 知识库？</b></summary>

**答**：可以共享整个 QnAMaker 服务，而不是单独的知识库。

</details>

<details>
<summary><b>如果找不到合适的匹配项，如何更改默认消息？</b></summary>

**答**：默认消息是应用服务中设置的一部分。
- 在 Azure 门户中转到自己的应用服务资源

![qnamaker 应用服务](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- 单击“设置”选项

![qnamaker 应用服务设置](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- 更改 **DefaultAnswer** 设置的值
- 重启应用服务

![qnamaker 应用服务重启](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>为什么我的 SharePoint 链接不提取？</b></summary>

**答案**：有关详细信息，请参阅[数据源位置](./Concepts/knowledge-base.md#data-source-locations)。

</details>

<details>
<summary><b>我对知识库所做的更新不会反映在发布上。为什么不呢？</b></summary>

**答**：需要先保存每个编辑操作（无论是在表更新、测试还是设置中），然后才能将其发布。 请确保在每次编辑操作后单击 "**保存并训练**" 按钮。

</details>

<details>
<summary><b>知识库是否支持丰富的数据或多媒体？</b></summary>

**答**：

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>文件和 Url 的多媒体自动提取

* URL-Markdown 转换功能有限。
* 文件-不支持

#### <a name="answer-text-in-markdown"></a>Markdown 中的应答文本
一旦 QnA 集在知识库中，就可以编辑答案的 markdown 文本，以包含可从公共 Url 获得的媒体的链接。


</details>

<details>
<summary><b>QnA Maker 是否支持非英语语言？</b></summary>

**答案**：查看有关支持的[语言](./Overview/languages-supported.md)的详细信息。

如果你有多种语言的内容，请务必为每种语言创建单独的服务。

</details>

## <a name="manage-service"></a>管理服务

<details>
<summary><b>何时应重启应用服务？</b></summary>

**答**：如果 "**用户设置**"[页](https://www.qnamaker.ai/UserSettings)上的 "**终结点键**" 表中的知识库版本值旁边有警告图标，请刷新应用服务。

</details>

<details>
<summary><b>我删除了现有搜索服务。如何解决此问题？</b></summary>

**答案**：如果删除 Azure 认知搜索索引，则该操作为最终状态，无法恢复索引。

</details>

<details>
<summary><b>我删除了搜索服务中的 `testkb` 索引。如何解决此问题？</b></summary>

**答**：无法恢复你的旧数据。 创建新的 QnA Maker 资源并再次创建知识库。

</details>

<details>
<summary><b>何时应刷新终结点密钥？</b></summary>

**答**：如果您怀疑您的终结点已经泄露，请刷新您的终结点。

</details>

<details>
<summary><b>是否可以将同一 Azure 认知搜索资源用于使用多种语言的知识库？</b></summary>

**答**：若要使用多种语言和多个知识库，用户必须为每种语言创建一个 QnA Maker 资源。 这会为每种语言创建单独的 Azure 搜索服务。 在单个 Azure 搜索服务中混合使用不同语言的知识库会导致结果相关性下降。

</details>

<details>
<summary><b>如何更改 QnA Maker 使用的 Azure 认知搜索资源的名称？</b></summary>

**答**： Azure 认知搜索资源的名称是在末尾追加了一些随机字母的 QnA Maker 资源名称。 这使得 QnA Maker 难以区分多个搜索资源。 创建单独的搜索服务（按你喜欢的方式对其进行命名），并将其连接到你的 QnA 服务。 步骤与[升级 Azure 搜索](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)所需执行的步骤类似。

</details>

<details>
<summary><b>当 QnA Maker 返回 `Runtime core is not initialized,` 如何修复它？</b></summary>

**答**：应用服务的磁盘空间可能已满。 修复磁盘空间的步骤：

1. 在[Azure 门户](https://portal.azure.com)中，选择 QnA Maker 的应用服务，然后停止服务。
1. 仍在应用服务中，选择 "**开发工具**"，然后依次选择 "**高级工具**"、"**开始**"。 这将打开一个新的浏览器窗口。
1. 选择 "**调试控制台**"，然后选择 " **CMD** " 以打开命令行工具。
1. 导航到_site/wwwroot/Data/QnAMaker/_ directory。
1. 删除名称以 `rd`开头的所有文件夹。

    **请勿删除**以下内容：

    * KbIdToRankerMappings 文件
    * EndpointSettings 文件
    * EndpointKeys 文件夹

1. 启动应用服务。
1. 访问知识库，验证它是否正常工作。

</details>

## <a name="integrate-with-other-services-including-bots"></a>与其他服务（包括机器人）集成

<details>
<summary><b>是否需要使用 Bot 框架才能使用 QnA Maker？</b></summary>

**答**：不可以，不需要将[机器人框架](https://github.com/Microsoft/botbuilder-dotnet)用于 QnA Maker。 不过，QnA Maker 是在 [Azure Bot 服务](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)中提供的几个模板之一。 机器人服务通过 Microsoft Bot Framework 实现快速智能机器人开发，并且它在无服务器的环境中运行。

</details>

<details>
<summary><b>如何使用 QnA Maker 创建新的机器人？</b></summary>

**答案**：按照[本](./Tutorials/create-qna-bot.md)文档中的说明，通过 Azure Bot 服务创建机器人。

</details>

<details>
<summary><b>如何实现在现有 Azure bot 服务中使用其他知识库？</b></summary>

**答**：您需要了解有关知识库的下列信息：

* 知识库 ID。
* 发布之后，在 "**设置**" 页上找到知识库的已发布终结点自定义子域名称，称为 `host`。
* 知识库的已发布终结点项-发布后在 "**设置**" 页上找到。

有关此信息，请在 Azure 门户中转到机器人的应用服务。 在**设置-> 配置-> 应用程序设置**中，更改这些值。

知识库的终结点键在 ABS 服务中标记为 `QnAAuthkey`。

</details>

<details>
<summary><b>两个或多个客户端应用程序是否可以共享知识库？</b></summary>

**答**：是的，可以从任意数量的客户端查询知识库。 如果来自知识库的响应显示速度缓慢或超时，请考虑升级与知识库关联的应用服务的服务层。

</details>

<details>
<summary><b>如何实现将 QnA Maker 服务嵌入我的网站中？</b></summary>

**答**：请按照以下步骤将 QnA Maker 服务作为 web 聊天控件嵌入到你的网站中：

1. 按照[此处](./Tutorials/create-qna-bot.md)的说明创建常见问题解答机器人。
2. 按照[此处](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)的步骤启用 Web 聊天

</details>

## <a name="data-storage"></a>数据存储

<details>
<summary><b>存储了哪些数据以及存储在何处？</b></summary>

**答**：

创建 QnA Maker 服务时，你选择了 Azure 区域。 知识库和日志文件存储在此区域中。

</details>