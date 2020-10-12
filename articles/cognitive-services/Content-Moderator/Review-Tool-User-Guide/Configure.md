---
title: 配置审阅工具设置 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用审阅工具为内容审查器配置或检索团队、标记、连接器、工作流和凭据。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84689837"
---
# <a name="configure-the-review-tool"></a>配置审阅工具

[审阅工具](https://contentmoderator.cognitive.microsoft.com)具有几个重要功能，可以通过仪表板上的“设置”**** 菜单进行访问。

![内容审查器审阅工具设置菜单](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理团队和子团队

通过“团队”**** 选项卡，可以管理自己的团队和子团队，即可以在某些[人工审阅](../review-api.md#reviews)开始时收到通知的用户组。&mdash; 只能有一个团队（注册审阅工具时创建），但可以创建多个子团队。 团队管理员可以邀请成员、设置其权限，并将他们分配到不同的子团队。

![审阅工具团队设置](images/settings-2-team.png)

子团队适用于创建呈报团队或专门负责审阅特定类别内容的团队。 例如，可能会将成人内容发送给单独的团队以进行进一步审阅。

本部分介绍如何创建子团队并动态分配审阅。 不过，可以使用[工作流](workflows.md)根据特定条件分配审阅。

### <a name="create-a-subteam"></a>创建子团队

转到“子团队”**** 部分，然后单击“添加子团队”****。 在对话框中输入子团队名称，然后单击“保存”****。

![子团队名称](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀请团队成员

无法将尚不是默认团队成员的审阅者分配到子团队，因此，需要先将审阅者添加到默认团队。 单击“团队”**** 选项卡上的“邀请”****。

![邀请用户](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>将团队成员分配到子团队

单击“添加成员”**** 按钮可以将默认团队中的成员分配到一个或多个子团队。 只能将现有用户添加到子团队。 若要添加“审阅”工具中没有的新用户，请使用“团队”设置页上的“邀请”按钮邀请用户。

![分配子团队成员](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>将审阅分配到子团队

创建子团队并分配成员后，便能开始向这些子团队分配内容[审阅](../review-api.md#reviews)。 此操作通过站点的“审阅”**** 选项卡完成。
若要向子团队分配内容，请单击右上角的省略号，再依次选择“移至”**** 和子团队。

![向子团队分配图像审阅](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切换子团队

如果你是多个子团队的成员，则可以在这些子团队之间切换，以更改为你显示的内容审阅。 在“审阅”**** 选项卡中，选择标记为“默认”**** 的下拉菜单，然后选择“选择子团队”****。 你可以查看不同子团队的内容审阅，但前提是你是这些子团队的成员。

![切换子团队](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

除两个默认审核标记 isadult**** (a****) 和 isracy**** (r****) 外，还可通过“标记”**** 选项卡定义自定义审核标记。&mdash; 如果创建自定义标记，该标记会与默认标记一起出现在审阅中。 可以通过切换可见性设置来更改审阅中显示的标记。

![标记视图，包括“可见”复选框](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>创建自定义标记

若要创建新标记，必须在相应字段中输入短代码、名称和描述。

- **短代码**：为标记输入两个字母的代码。 示例：cb****
- **名称**：以小写字母输入简短的描述性标记名称，不要使用空格。 示例：isbullying****。
- **描述**：（可选）输入标记所针对的内容类型的描述。 示例：**网络欺凌的描述或实例**。

单击“添加”**** 以添加标记，并在完成标记创建后单击“保存”****。

![审阅工具“创建新标记”对话框](images/settings-3-tags.png)

### <a name="delete-tags"></a>删除标记

可以通过选择“标记”列表中的条目旁边的垃圾桶图标来删除自定义标记，但无法删除默认标记。

## <a name="connectors"></a>连接器

可通过“连接器”**** 选项卡管理连接器，这些连接器是服务专用插件，可以在内容[工作流](../review-api.md#workflows)中通过不同方式处理内容。

创建工作流时的默认连接器是内容审查器连接器，该连接器可以将内容标记为“adult”**** 或“racy”****、发现亵渎内容，等等。 但是，只要具有其各自服务的凭据，就可以使用此处列出的其他连接器（例如，若要使用人脸连接器，将需要获得[人脸](https://docs.microsoft.com/azure/cognitive-services/face/overview)订阅密钥）。

[审阅工具](./human-in-the-loop.md)包括以下连接器：

- 情感
- 人脸
- PhotoDNA 云服务
- 文本分析

### <a name="add-a-connector"></a>添加连接器

若要添加连接器（并使其可用于内容[工作流](../review-api.md#workflows)），请选择相应的“连接”**** 按钮。 在下一个对话框中，输入该服务的订阅密钥。 完成后，新的连接器应在页面顶部显示。

![内容审查器“连接器”设置](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流

“工作流”**** 选项卡可让你管理[工作流](../review-api.md#workflows)。 工作流是基于云的内容筛选器，它们与连接器配合使用，以不同的方式对内容进行排序并采取适当的措施。 在这里，你可以定义、编辑和测试工作流。 有关如何执行此操作的指导，请参阅[定义和使用工作流](Workflows.md)。

![内容审查器“工作流”设置](images/settings-5-workflows.png)

## <a name="credentials"></a>凭据

“凭据”**** 选项卡提供对内容审查器订阅密钥的快速访问，从 REST 调用或客户端 SDK 访问所有审核服务时都需要此订阅密钥。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>将外部凭据用于工作流

注册时，[审阅工具](https://contentmoderator.cognitive.microsoft.com)会为 Azure 内容审查器服务生成一个免费试用密钥，但也可以将其配置为使用 Azure 帐户中的现有密钥。 对于大型方案，建议使用此方法，因为免费试用密钥具有严格的使用限制（[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)）。

如果在 Azure 中创建了[内容审查器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)，请在 Azure 门户中导航到该资源并选择“密钥”**** 边栏选项卡。 复制其中一个密钥。

![Azure 门户中的内容审查器密钥](images/credentials-azure-portal-keys.PNG)

在[审阅工具](https://contentmoderator.cognitive.microsoft.com)的“凭据”**** 选项卡中，转到“工作流设置”**** 窗格，选择“编辑”****，然后将密钥粘贴到“Ocp-Apim-Subscription-Key”**** 字段。 现在，调用审核 API 的工作流将使用你的 Azure 凭据。

> [!NOTE]
> “工作流设置”**** 窗格中的其他两个字段用于自定义术语和图像列表。 请参阅[自定义术语](../try-terms-list-api.md)或[自定义图像](../try-image-list-api.md)指南了解相关信息。

### <a name="use-your-azure-account-with-the-review-apis"></a>将 Azure 帐户与审阅 API 配合使用

若要将 Azure 密钥与审阅 API 配合使用，需要检索你的资源 ID。 转到 Azure 门户中的内容审查器资源，然后选择“属性”**** 边栏选项卡。 复制资源 ID 值并将其粘贴到审阅工具的“凭据”**** 选项卡的“列入允许列表的资源 ID”**** 字段中。

![Azure 门户中的内容审查器资源 ID](images/credentials-azure-portal-resourceid.PNG)

如果在两个位置都输入了订阅密钥，则不会使用审阅工具帐户随附的试用密钥，但该试用密钥仍然可用。

## <a name="next-steps"></a>后续步骤

请按照[审阅工具快速入门](../quick-start.md)，开始在内容审核方案中使用审阅工具。