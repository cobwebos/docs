---
title: 配置审阅工具设置-内容审查器
titlesuffix: Azure Cognitive Services
description: 使用审阅工具配置或检索内容审查器团队、 标记、 连接器、 工作流和凭据。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756518"
---
# <a name="configure-the-review-tool"></a>配置审阅工具

[审阅工具](https://contentmoderator.cognitive.microsoft.com)具有几个重要功能，您可以通过访问**设置**仪表板上的菜单。

![内容审查器评审过设置菜单](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理团队和子团队

**团队**选项卡可以管理你的团队和子团队&mdash;当某些可以收到通知的用户组[人工审阅](../review-api.md#reviews)是否已启动。 只能有一个团队 （它创建使用审阅工具注册时），但您可以创建多个子团队。 团队管理员可以邀请成员、 设置其权限，并将其分配到不同子团队。

![评审工具团队设置](images/settings-2-team.png)

子团队适用于创建呈报团队或专门负责审阅特定类别内容的团队。 例如，可能会将有成人内容发送到单独的团队以便进一步查看。

本部分介绍如何创建子团队并快速分配动态评审。 不过，可以使用[工作流](workflows.md)根据特定条件分配审阅。

### <a name="create-a-subteam"></a>创建子团队

转到**子团队**部分，然后单击**添加子组**。 在对话框中输入你的子组名称，然后单击**保存**。

![子团队名称](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀请团队成员

您不能将人分配给子组，如果它们尚不属于默认团队，因此您需要先将审阅者添加到默认团队。 单击**邀请**上**团队**选项卡。

![邀请用户](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>分配团队成员要 subteam

单击**添加成员**按钮，将从默认团队的成员分配到一个或多个子团队。 只能将现有用户添加到子团队。 若要添加“审阅”工具中没有的新用户，请使用“团队”设置页上的“邀请”按钮邀请用户。

![分配子团队成员](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>将评审作业分配到子团队

一旦您已创建你的子团队和分配成员，才能开始分配内容[评审](../review-api.md#reviews)到这些子团队。 这是从**评审**站点的选项卡。
若要将内容分配到子组，请单击右上角选择省略号**移动到**，并选择子组。

![向子团队分配图像审阅](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切换子团队

如果你是多个子组的成员，您可以更改为您显示哪些内容审查这些子团队之间进行切换。 在中**评审**选项卡上，选择标记为下拉列表菜单**默认**，然后选择**选择子组**。 您可以查看不同子团队，但仅剩的内容审阅你是其成员。

![切换子团队](images/3-review-image-subteam-2.png)

## <a name="tags"></a>标记

**标记**选项卡可以定义自定义审查标记除了两个默认审查标记&mdash;**isadult** () 和**isracy** (**r**)。 创建自定义标记，可用在默认标记和评论。 您可以更改哪些标记显示在评审切换其可见性设置。

![查看标记，包括"是可见"复选框](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>创建自定义标记

若要创建一个新的标记，必须在相应字段中输入短代码、 名称和说明。

- **简短的代码**:输入你的标记的双字母代码。 示例： **cb**
- **名称**：以不含空格的小写形式输入简短且具有说明性的标记名称。 示例： **isbullying**。
- **说明**: （可选） 输入的内容类型的说明，标记目标。 示例：**描述或实例的网络门**。

单击**外**以添加标记，并单击**保存**完后创建标记。

![审阅工具创建新标记对话框](images/settings-3-tags.png)

### <a name="delete-tags"></a>删除标记

可以通过选择标记列表中，在其条目旁边的垃圾桶图标来删除自定义标记，但不能删除默认标记。

## <a name="connectors"></a>连接器

**连接器**选项卡可以管理你的连接器，它是可以作为内容的一部分以不同方式处理内容的特定于服务的插件[工作流](../review-api.md#workflows)。

创建工作流时的默认连接器是内容审查器连接器，可以将标记作为内容**成人**或**挑逗性**，查找不雅内容，等等。 但是，可以使用其他连接器，在此处列出，只要您具有针对其各自的服务的凭据 (若要使用人脸 API 连接器，例如，你需要获取[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)订阅密钥)。

[审阅工具](./human-in-the-loop.md)包括以下连接器：

- 情感 API
- 人脸 API
- PhotoDNA 云服务
- 文本分析 API

### <a name="add-a-connector"></a>添加连接器

若要添加的连接器 (并使其可供使用的内容[工作流](../review-api.md#workflows))，选择相应**Connect**按钮。 在下一步的对话框中，输入该服务的订阅密钥。 完成后，新的连接器应出现在页面顶部。

![内容审查器“连接器”设置](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流

**工作流**选项卡可以管理你[工作流](../review-api.md#workflows)。 工作流是基于云的筛选器有关的内容，并且它们使用连接器来对内容进行排序以不同的方式并采取适当措施。 在这里，可以定义、 编辑和测试你的工作流。 请参阅[定义和使用工作流](Workflows.md)有关如何执行此操作的指导。

![内容审查器“工作流”设置](images/settings-5-workflows.png)

## <a name="credentials"></a>凭据

**凭据**选项卡提供了快速访问你内容审查器的订阅密钥，将需要访问任何审查服务从 REST 调用或客户端 SDK。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>为工作流中使用外部凭据

[审阅工具](https://contentmoderator.cognitive.microsoft.com)为来自你的 Azure 帐户密钥时您注册，但还可以配置它以使用现有的 Azure 内容审查器服务生成一个免费试用版密钥。 这建议对于大规模的方案，因为免费试用版密钥严格使用限制 ([定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/))。

如果已创建[内容审查器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)在 Azure 中，导航到它在 Azure 门户并选择**密钥**边栏选项卡。 其中一个密钥的副本。

![Azure 门户中的内容审查器密钥](images/credentials-azure-portal-keys.PNG)

在中[审阅工具](https://contentmoderator.cognitive.microsoft.com)的**凭据**选项卡上，转到**工作流设置**窗格中，选择**编辑**，并将你的密钥粘贴到**Ocp Apim 订阅密钥**字段。 现在，调用审查 Api 的工作流将使用 Azure 凭据。

> [!NOTE]
> 中的其他两个字段**工作流设置**窗格进行自定义的术语和图像列表。 请参阅[自定义条款](../try-terms-list-api.md)或[自定义映像](../try-image-list-api.md)指南，以了解有关这些信息。

### <a name="use-your-azure-account-with-the-review-apis"></a>查看 Api 使用 Azure 帐户

若要查看 Api 使用 Azure 密钥，需要检索资源 id。 转到门户并选择在 Azure 中的内容审查器资源**属性**边栏选项卡。 复制资源 ID 值，并将其粘贴到**列入允许列表资源 id**审阅工具字段**凭据**选项卡。

![Azure 门户中的内容审查器资源 ID](images/credentials-azure-portal-resourceid.PNG)

如果已在这两个位置中输入你的订阅密钥，附带了你评审工具的帐户的试用版密钥将不使用，但将保持可用。

## <a name="next-steps"></a>后续步骤

请按照[评审工具快速入门](../quick-start.md)若要开始在内容审查方案中使用审阅工具。