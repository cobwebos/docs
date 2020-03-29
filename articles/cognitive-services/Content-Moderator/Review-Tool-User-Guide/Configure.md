---
title: 配置审阅工具设置 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 使用"审阅"工具配置或检索内容审阅人的团队、标记、连接器、工作流和凭据。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220123"
---
# <a name="configure-the-review-tool"></a>配置审阅工具

["审阅"工具](https://contentmoderator.cognitive.microsoft.com)具有几个重要功能，您可以通过仪表板上的 **"设置"** 菜单访问这些功能。

![内容审阅人 查看设置菜单](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理团队和子团队

"**团队"** 选项卡允许您管理团队和子团队&mdash;组的用户组，这些用户可在开始[某些人工评论](../review-api.md#reviews)时收到通知。 您只能有一个团队（在使用"审阅"工具注册时创建），但您可以创建多个子团队。 团队管理员可以邀请成员、设置其权限并将他们分配给不同的子团队。

![查看工具团队设置](images/settings-2-team.png)

子团队适用于创建呈报团队或专门负责审阅特定类别内容的团队。 例如，您可以将成人内容发送给单独的团队进行进一步审阅。

本节介绍如何创建子团队并快速动态分配评论。 不过，可以使用[工作流](workflows.md)根据特定条件分配审阅。

### <a name="create-a-subteam"></a>创建子团队

转到**子团队**部分，然后单击"**添加子团队**"。 在对话框中输入子团队名称，然后单击"**保存**"。

![子团队名称](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀请队友

如果某人不是默认团队的成员，则无法将某人分配给子团队，因此您需要首先将审阅者添加到默认团队。 单击"**团队**"选项卡上的 **"邀请**"。

![邀请用户](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>将队友分配到子团队

单击"**添加成员"** 按钮可将默认团队的成员分配给一个或多个子团队。 只能将现有用户添加到子团队。 若要添加“审阅”工具中没有的新用户，请使用“团队”设置页上的“邀请”按钮邀请用户。

![分配子团队成员](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>将审核分配给子团队

创建子团队和分配成员后，即可开始向这些子团队分配内容[审核](../review-api.md#reviews)。 这是从网站的 **"审阅**"选项卡完成的。
要将内容分配给子团队，请单击右上角的省略号，选择 **"移动到**"并选择子组。

![向子团队分配图像审阅](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切换子团队

如果您是多个子团队的成员，则可以在这些子团队之间切换，以更改为您显示的内容评论。 在 **"审阅**"选项卡中，选择标记为 **"默认"** 的下拉菜单，然后**选择"子团队**"。 您可以查看不同子团队的内容评论，但只能查看您属于其成员的内容评论。

![切换子团队](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

"**标记"** 选项卡允许您定义自定义审核标记，除了两个默认的审核标记&mdash;**是成人**（**a**） 和**isracy** （**r**） 创建自定义标记时，它与默认标记一起在评论中可用。 您可以通过切换这些标记的可见性设置来更改评论中显示的标记。

![标记视图，包括"可见"复选框](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>创建自定义标记

要创建新标记，必须在相应的字段中输入短代码、名称和说明。

- **短代码**：输入标记的两个字母代码。 示例： **cb**
- **名称**：在不带空格小写的情况下输入简短且描述性标记名称。 示例：**是欺凌**。
- **说明**：（可选） 输入标记目标内容类型的说明。 示例：**网络欺凌的描述或实例**。

单击"**添加"** 以添加标记，并在创建标记后单击"**保存**"。

![查看工具创建新标记对话框](images/settings-3-tags.png)

### <a name="delete-tags"></a>删除标签

您可以通过在"标记"列表中选择其条目旁边的垃圾标记图标来删除自定义标记，但不能删除默认标记。

## <a name="connectors"></a>连接器

连接器**选项卡**允许您管理连接器，连接器是特定于服务的插件，可以作为内容[工作流](../review-api.md#workflows)的一部分以不同的方式处理内容。

创建工作流时的默认连接器是内容审阅者连接器，它可以将内容标记为**成人**或**流质**、查找亵渎等。 但是，您可以使用此处列出的其他连接器，只要您具有其各自服务的凭据（例如，要使用 Face 连接器，则需要获取[Face](https://docs.microsoft.com/azure/cognitive-services/face/overview)订阅密钥）。

["审阅"工具](./human-in-the-loop.md)包括以下连接器：

- 情感
- 人脸
- PhotoDNA 云服务
- 文本分析

### <a name="add-a-connector"></a>添加连接器

要添加连接器（并使它可用于内容[工作流](../review-api.md#workflows)），请选择相应的**连接**按钮。 在下一个对话框中，输入该服务的订阅密钥。 完成后，新连接器应显示在页面顶部。

![内容审查器“连接器”设置](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流

**工作流**选项卡允许您管理[工作流](../review-api.md#workflows)。 工作流是内容的基于云的筛选器，它们使用连接器以不同方式对内容进行排序并采取适当的操作。 在这里，您可以定义、编辑和测试工作流。 有关如何执行此操作的指导[，请参阅定义和使用工作流](Workflows.md)。

![内容审查器“工作流”设置](images/settings-5-workflows.png)

## <a name="credentials"></a>凭据

"**凭据"** 选项卡提供对内容审阅人订阅密钥的快速访问，您需要从 REST 调用或客户端 SDK 访问任何审核服务。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>将外部凭据用于工作流

["审阅"工具](https://contentmoderator.cognitive.microsoft.com)会在注册时为 Azure 内容审阅人服务生成免费试用密钥，但也可以将其配置为使用 Azure 帐户中的现有密钥。 对于大型方案，建议这样做，因为免费试用密钥具有严格的使用限制（[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)）。

如果在 Azure 中创建了[内容审阅人资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)，请在 Azure 门户中导航到该资源，然后选择 **"密钥"** 边栏选项卡。 复制您的一个密钥。

![Azure 门户中的内容审查器密钥](images/credentials-azure-portal-keys.PNG)

在["审阅"工具](https://contentmoderator.cognitive.microsoft.com)的 **"凭据"** 选项卡中，**转到"工作流设置"** 窗格，选择 **"编辑**"，并将密钥粘贴到**Ocp-Apim-订阅键**字段中。 现在，调用审核 API 的工作流将使用 Azure 凭据。

> [!NOTE]
> **"工作流设置"** 窗格中的其他两个字段用于自定义术语和图像列表。 请参阅[自定义术语](../try-terms-list-api.md)或[自定义图像](../try-image-list-api.md)指南以了解这些。

### <a name="use-your-azure-account-with-the-review-apis"></a>将 Azure 帐户与审核 API 一起使用

若要将 Azure 密钥与审核 API 一起使用，需要检索资源 ID。 转到 Azure 门户中的内容审阅人资源，然后选择 **"属性**"边栏选项卡。 复制资源 ID 值并将其粘贴到"审阅"工具的**凭据**选项卡的 **"白名单资源 ID"** 字段中。

![Azure 门户中的内容审查器资源 ID](images/credentials-azure-portal-resourceid.PNG)

如果您在两个位置输入了订阅密钥，则不会使用"审阅"工具帐户附带的试用密钥，但将保持可用状态。

## <a name="next-steps"></a>后续步骤

按照["审阅"工具快速入门](../quick-start.md)开始在内容审核方案中使用"审阅"工具。