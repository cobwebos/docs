---
title: 配置审阅工具设置-内容审查器
titleSuffix: Azure Cognitive Services
description: 使用 "查看" 工具可以配置或检索团队、标记、连接器、工作流和凭据，以便内容审查器。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2ba314c814bdc92f62a607e28aefa30372bf297f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757428"
---
# <a name="configure-the-review-tool"></a>配置审阅工具

[查看工具](https://contentmoderator.cognitive.microsoft.com)具有几个可以通过仪表板上的 "**设置**" 菜单访问的重要功能。

![内容审查器查看 "设置" 菜单](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>管理团队和子团队

利用 "**团队**" 选项卡，你可以管理你的团队和子团队 &mdash;groups 用户，这些用户可以在启动某些[人审查](../review-api.md#reviews)时收到通知。 你只能有一个团队（在使用审阅工具注册时创建），但你可以创建多个子组。 团队管理员可以邀请成员、设置其权限，并将其分配给不同的子组。

![查看工具团队设置](images/settings-2-team.png)

子团队适用于创建呈报团队或专门负责审阅特定类别内容的团队。 例如，你可能会向单独的团队发送成人内容，以便进一步查看。

本部分介绍如何创建子团队并快速快速分配评审。 不过，可以使用[工作流](workflows.md)根据特定条件分配审阅。

### <a name="create-a-subteam"></a>创建子团队

请参阅子**团队**部分，并单击 "**添加具有**"。 在对话框中输入你的具有名称，然后单击 "**保存**"。

![子团队名称](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>邀请团队成员

如果某个用户还不是默认团队的成员，则不能将该用户分配到具有，因此你需要首先将审阅者添加到默认团队。 单击 "**团队**" 选项卡上的 "**邀请**"。

![邀请用户](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>将团队成员分配给具有

单击 "**添加成员**" 按钮，将默认团队的成员分配给一个或多个子团队。 只能将现有用户添加到子团队。 若要添加“审阅”工具中没有的新用户，请使用“团队”设置页上的“邀请”按钮邀请用户。

![分配子团队成员](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>将审阅分配给子团队

一旦你创建了子团队并分配了成员，你就可以开始向这些子团队分配内容[评审](../review-api.md#reviews)。 这是从站点的 "**查看**" 选项卡中完成的。
若要将内容分配到具有，请单击右上角的省略号，选择 "**移到**"，然后选择具有。

![向子团队分配图像审阅](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>切换子团队

如果你是多个具有的成员，则可以在这些子团队之间进行切换，以更改显示的内容评审。 在 "**审阅**" 选项卡中，选择标记为 "**默认**" 的下拉菜单，然后选择 "**选择具有**"。 您可以查看不同子团队的内容评审，但只能查看您所属的子团队的内容评审。

![切换子团队](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

"**标记**" 选项卡可用于定义自定义审核标记，以及 &mdash;**isadult** （**a**）和**isracy** （**r**）的两个默认审核标记。 当创建自定义标记时，它将在默认标记旁边的审阅中可用。 可以通过切换其可见性设置来更改中显示的标记。

!["标记" 视图，包括 "显示" 复选框](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>创建自定义标记

若要创建新的标记，必须在相应的字段中输入简短的代码、名称和说明。

- **简短代码**：为标记输入一个双字母代码。 示例： **cb**
- **名称**：输入简短且描述性的标记名称，不含空格。 例如： **isbullying**。
- **说明**：（可选）输入标记所针对的内容种类的说明。 示例：**描述或网络 bullying 的实例**。

单击 "**添加**" 以添加标记，并在创建完标记后单击 "**保存**"。

![查看工具 "创建新标记" 对话框](images/settings-3-tags.png)

### <a name="delete-tags"></a>删除标记

您可以通过在 "标记" 列表中的条目旁边选择 "垃圾桶" 图标来删除自定义标记，但是不能删除默认标记。

## <a name="connectors"></a>连接器

利用 "**连接器**" 选项卡，你可以管理连接器，这是特定于服务的插件，可以采用不同的方式处理内容[工作流](../review-api.md#workflows)的内容。

创建工作流时的默认连接器是内容审查器连接器，它可以将内容标记为**成人**或**猥亵**，查找猥亵，等等。 不过，你可以使用这里列出的其他连接器，只要你拥有各自服务的凭据（例如，要使用人脸 API 连接器，你将需要获取[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)订阅密钥）。

[查看工具](./human-in-the-loop.md)包括以下连接器：

- 情感 API
- 面部 API
- PhotoDNA 云服务
- 文本分析 API

### <a name="add-a-connector"></a>添加连接器

若要添加连接器（并使其可用于内容[工作流](../review-api.md#workflows)），请选择相应的 "**连接**" 按钮。 在下一个对话框中，输入该服务的订阅密钥。 完成后，新的连接器应显示在页面顶部。

![内容审查器“连接器”设置](images/settings-4-connectors.png)

## <a name="workflows"></a>工作流

"**工作流**" 选项卡可用于管理[工作流](../review-api.md#workflows)。 工作流是针对内容的基于云的筛选器，它们使用连接器来以不同方式对内容进行排序，并采取适当的措施。 可在此处定义、编辑和测试工作流。 有关如何执行此操作的指导，请参阅[定义和使用工作流](Workflows.md)。

![内容审查器“工作流”设置](images/settings-5-workflows.png)

## <a name="credentials"></a>凭据

"**凭据**" 选项卡可用于快速访问你的内容审查器订阅密钥，你将需要从 REST 调用或客户端 SDK 访问任何审核服务。

![内容审查器“凭据”设置](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>使用工作流的外部凭据

当你注册时，[查看工具](https://contentmoderator.cognitive.microsoft.com)会生成 azure 内容审查器服务的免费试用密钥，但你也可以将其配置为使用 azure 帐户中的现有密钥。 对于大型方案，建议使用此方法，因为免费试用密钥具有严格的使用限制（[定价和限制](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)）。

如果已在 Azure 中创建[内容审查器资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)，请在 Azure 门户中导航到该资源，然后选择 "**密钥**" 边栏选项卡。 复制其中一个密钥。

![Azure 门户中的内容审查器密钥](images/credentials-azure-portal-keys.PNG)

在[审阅工具](https://contentmoderator.cognitive.microsoft.com)的 "**凭据**" 选项卡中，依次单击 "**工作流设置**" 窗格、"**编辑**"，然后将你的密钥粘贴到 " **Apim-订阅-密钥**" 字段。 现在，调用裁决 Api 的工作流将使用你的 Azure 凭据。

> [!NOTE]
> "**工作流设置**" 窗格中的其他两个字段用于自定义术语和图像列表。 有关详细信息，请参阅[自定义术语](../try-terms-list-api.md)或[自定义图像](../try-image-list-api.md)指南。

### <a name="use-your-azure-account-with-the-review-apis"></a>将你的 Azure 帐户用于查看 Api

若要将你的 Azure 密钥与审查 Api 一起使用，你需要检索资源 ID。 在 Azure 门户中转到内容审查器资源，然后选择 "**属性**" 边栏选项卡。 复制 "资源 ID" 值，并将其粘贴到查看工具的 "**凭据**" 选项卡的 "**允许列表资源 ID** " 字段中。

![Azure 门户中的内容审查器资源 ID](images/credentials-azure-portal-resourceid.PNG)

如果在这两个位置输入了订阅密钥，则不会使用你的审阅工具帐户附带的试用密钥，但仍可使用。

## <a name="next-steps"></a>后续步骤

遵循[查看工具快速入门](../quick-start.md)，开始使用内容审核方案中的审阅工具。