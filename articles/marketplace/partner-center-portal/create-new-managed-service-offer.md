---
title: 在商业市场中创建新的托管服务产品/服务
description: 如何使用合作伙伴中心中的商业应用商店门户创建新的托管服务产品/服务，以便在 Azure 应用商店中列出。
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: de6e08534722deccb8be11248dd8af3ca2e66739
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983206"
---
# <a name="create-a-new-managed-service-offer"></a>创建新的托管服务产品/服务

> [!IMPORTANT]
> 我们将托管服务产品的管理从云合作伙伴门户转移到合作伙伴中心。 在迁移产品/服务之前，请按照[向 Azure 应用商店发布托管服务产品/服务以](../../lighthouse/how-to/publish-managed-services-offers.md)管理产品/服务的说明进行操作。

托管服务提供了启用[Azure 灯塔](../../lighthouse/overview.md)方案的帮助。 当客户接受托管服务产品/服务时，他们将能够将资源用于 Azure[委派的资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。

要开始创建托管服务产品/服务，请确保首先[创建合作伙伴中心帐户](./create-account.md)并打开["商业市场"仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，并选择 **"概述"** 页。 您必须具有[银牌或金云平台能力级别](https://partner.microsoft.com/membership/cloud-platform-competency)，或必须是 Azure[专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp)才能发布托管服务产品/服务。

![合作伙伴中心上的商业市场仪表板](./media/new-offer-overview.png)

>[!Note]
> 产品/服务发布后，只有在重新发布后，才会在系统和商店前面更新对合作伙伴中心中提供的优惠进行编辑。 请确保在进行更改后提交要发布的要约。

## <a name="create-a-new-offer"></a>创建新产品/服务

选择 **"+ 新产品/服务**"按钮，然后选择 **"托管服务**"菜单项。 将显示"**新建产品/服务**"对话框。

### <a name="offer-id-and-alias"></a>优惠 ID 和别名

- **优惠 ID**：帐户中每个产品/服务的唯一标识符。 此 ID 将在市场产品/服务 URL 地址中对客户可见。 此 ID 只能包含小写字母数字字符（包括连字符和下划线，但没有空格），限制为 50 个字符，在选择 "**创建**"后无法更改。  例如，如果您在此处输入*测试报价 1，* 则产品/服务 URL 将为`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。

- **优惠别名**：用于指合作伙伴中心内产品/服务的名称。 此名称不会在市场中使用，并且与向客户显示的要约名称和其他值不同。 选择 "**创建**"后，无法更改此值。

输入**优惠 ID**和**优惠别名**后，选择 **"创建**"。 然后，您将能够处理产品/服务的所有不同部分。

## <a name="offer-setup"></a>优惠设置

**"产品/服务设置"** 页要求提供以下信息。 请务必在完成这些字段后选择 **"保存**"。

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

请注意，根据[托管服务认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，需要**潜在顾客目标**。 这将在每次客户部署您的产品/服务时在 CRM 系统中创建记录。

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

在继续下一节之前，请记住**保存**页面。

## <a name="properties"></a>属性

属性**页面**允许您定义用于在市场上对产品/服务进行分组的类别以及支持您的优惠的法律合同。 完成此页面后选择 **"保存**"。

### <a name="category"></a>类别

选择至少一个且最多五个类别，这些类别将用于将您的产品/服务放入相应的市场搜索区域。 请务必在产品/服务说明中标注您的产品/服务如何支持这些类别。

### <a name="terms-and-conditions"></a>条款和条件

在条款和条件字段中提供您自己的法律**条款和条件**。 您还可以提供可在其中找到您的条款和条件的 URL。 客户在试用您的优惠之前，必须接受这些条款。

## <a name="offer-listing"></a>优惠列表

"**产品/服务"列表**页允许您为您的产品/服务定义市场详细信息（产品/服务名称、说明、图像等）。

> [!NOTE]
> 报价列表内容（如说明、文档、屏幕截图、使用条款等）不需要以英文表示，只要要约说明以短语开头，"此应用程序仅以 [非英语语言]提供。 提供*有用的链接 URL*以提供产品/服务列表内容中所用语言以外的语言提供内容也是可接受的。

### <a name="name"></a>名称

您在此处输入的名称将作为产品/服务列表的标题显示给客户。 此字段预填充了创建产品/服务时为 **"产品/服务"别名**输入的文本，但您可以更改此值。 此名称可以注册商标（您可以包括商标或版权符号）。 名称不能超过 50 个字符，不能包含任何表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明（最多 100 个字符），可在市场搜索结果中使用。

### <a name="long-summary"></a>长摘要

提供更久的报价说明（最多 256 个字符）。 此长摘要也可用于市场搜索结果。

### <a name="description"></a>说明

提供更久的报价说明（最多 3，000 个字符）。 此描述将显示在市场列表概述中的客户。 包括您的产品/服务的价值主张、主要优势、类别和/或行业协会、应用内购买机会以及任何必需的披露。

编写说明的一些提示：  

- 在前几句描述中清晰描述产品/服务的价值主张。 在价值主张中包括以下项目：
  - 优惠说明
  - 从产品/服务中受益的用户类型
  - 产品/服务解决的客户需求或痛苦
- 请牢记，前几句话可能在搜索引擎结果中显示。  
- 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
- 尽可能多使用行业特定用语或基于优势的措辞。

为了使您的报价描述更具吸引力，请使用丰富的文本编辑器来设置您的说明格式。

![使用富文本编辑器](./media/text-editor2.png)

使用以下说明使用富文本编辑器：

- 要更改内容的格式，请突出显示要设置格式的文本并选择文本样式，如下所示：

     ![使用富文本编辑器更改文本格式](./media/text-editor3.png)

- 要向文本添加项目符号或编号列表，请使用以下选项：

     ![使用富文本编辑器添加列表](./media/text-editor4.png)

- 要向文本添加或删除缩进，请使用以下选项：

     ![使用富文本编辑器缩进](./media/text-editor5.png)

### <a name="privacy-policy-link"></a>隐私政策链接

输入组织隐私政策的 URL（托管在您的网站上）。 您有责任确保您的应用符合隐私法律和法规，并提供有效的隐私政策。

### <a name="useful-links"></a>有用链接

提供有关解决方案的可选补充联机文档。 通过单击 **" 添加链接**"添加其他有用的链接。

### <a name="contact-information"></a>联系信息

在本节中，您必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并且可能会提供给 CSP 合作伙伴。

### <a name="support-urls"></a>支持 URL

如果有 Azure**全局客户**和/或 Azure**政府客户**的支持网站，请在此处提供这些 URL。

### <a name="marketplace-images"></a>市场映像

在本节中，您可以提供向客户展示产品/服务时将使用的徽标和图像。 所有图像必须采用 .png 格式。

#### <a name="marketplace-logos"></a>市场徽标

需要四个徽标大小：**小 （40x40）**、**中等 （90x90）**、**大 （115x115）** 和**宽 （255x115）**。 请遵守徽标适用的下述准则：

- Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
- 门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。
- 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
- 徽标的外观应平整，并且应避免渐变。 不要在徽标上使用渐变背景。
- 不要在徽标上放置文本，即使是公司或品牌名称也不可以。
- 确保徽标未被拉伸。

#### <a name="screenshots"></a>屏幕截图

最多添加五个屏幕截图，显示您的优惠如何工作。 所有屏幕截图必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

您可以选择添加最多五个演示您的优惠的视频。 这些视频应托管在 YouTube 和/或 Vimeo 上。 对于每个视频，输入视频的名称、URL 和视频的缩略图图像（1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

- [市场产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>预览

在将优惠实时发布到更广泛的市场产品/服务之前，首先需要将其提供给有限的预览受众。 这样，您就可以在向客户提供产品之前确认所提供的服务在 Azure 应用商店中显示的方式。 Microsoft 支持和工程团队还可以在此预览期内查看您的产品/服务。

您可以通过在 **"预览受众"** 部分中输入 Azure 订阅标识来定义预览受众。 您可以手动输入多达 10 个订阅 I，或上载最多包含 100 个订阅的 ID 的 .csv 文件。

与这些订阅关联的任何客户都可以在 Azure 应用商店中查看产品/服务。 请务必在此处包含您自己的订阅，以便预览产品/服务。

## <a name="plan-overview"></a>计划概述

每个产品/服务都必须具有一个或多个计划（有时也称为 SKU）。 可添加多个计划，以不同的价格支持不同的功能集，也可为有限数量的特定客户自定义特定计划。 客户可在父级产品/服务中查看可供其使用的计划。

在 **"计划概述"** 页上，选择 **"创建新计划**"。 然后输入**计划 ID**和**计划名称**。 这两个值只能包含小写字母数字字符、破折号和下划线，最多 50 个字符。 这些值可能向客户可见，并且在发布产品/服务后无法更改这些值。

选择 **"在**输入这些值后创建"以继续执行计划。 有三个部分要完成：**计划列表**、**定价和可用性**以及**技术配置**。

### <a name="plan-listing"></a>计划列表

首先，提供计划的**搜索结果摘要**。 这是您的计划（最多 100 个字符）的简短说明，可用于市场搜索结果。

接下来，输入一个描述，该**说明**提供计划的更详细说明。

### <a name="pricing-and-availability"></a>定价和可用性

目前，只有一种定价模型可用于托管服务产品/服务：**自带许可证 （BYOL）。** 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。

"**计划可见性**"部分允许您指示此计划是否应为[私有](../../marketplace/private-offers.md)。 如果取消选中"**这是私有计划**"框，则您的计划将不限于特定客户（或特定数量的客户）。

要使此计划仅对特定客户可用，请选择“是”****。 执行此操作时，需要提供客户的订阅 ID 来确定客户身份。 可以逐个输入（最多 10 个订阅）或通过上载 .csv 文件（在所有计划中最多 10，000 个订阅）来输入这些文件。 请确保在此处包含你自己的订阅，以便测试和验证该产品/服务。

> [!IMPORTANT]
> 计划一旦公开发布，您就无法将其更改为私有。 要控制哪些客户可以接受您的报价并委派资源，请使用私人计划。 使用公共计划时，您无法将可用性限制为特定客户，甚至限制特定数量的客户（尽管如果您选择这样做，您可以完全停止销售计划）。 只有在发布产品/服务时，在**角色定义**中包含"**授权"** 设置为["托管服务注册分配删除角色"时](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，才能在客户接受产品/服务后取消对[委派的访问权限](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation)。 您也可以联系客户，要求他们[删除您的访问权限](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="technical-configuration"></a>技术配置

计划的此部分创建一个清单，其中提供了用于管理客户资源的授权信息。 为了启用[Azure 委派的资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)，需要此信息。

请务必查看[Azure 灯塔方案中的租户、角色和用户](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以了解支持哪些角色以及定义授权的最佳做法。

> [!NOTE]
> 如上所述，“授权”条目中的用户和角色将应用于每个购买了计划的客户****。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

#### <a name="manifest"></a>清单

首先，请提供清单的版本****。 使用 n.n.n 格式（例如 1.2.5）**。

然后，输入租户 ID****。 这是与组织的 Azure 活动目录 （Azure AD） 租户 ID 关联的 GUID;也就是说，您将从中访问客户的资源的管理租户。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它****。

如果您发布新版本的报价，并且需要创建更新的清单，请选择 **"新清单**"。 请确保从以前的清单版本增加版本号。

#### <a name="authorization"></a>授权

授权定义管理租户中的实体，这些实体可以访问购买计划的客户的资源和订阅。 每个实体都分配了一个内置角色，该角色授予特定级别的访问。

您可以为每个计划创建多达 20 个授权。

> [!TIP]
> 在大多数情况下，您需要将角色分配给 Azure AD 用户组或服务主体，而不是一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 将角色分配给 Azure AD 组时[，请确保**组类型**为 **"安全"，** 而不是**Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../../lighthouse/concepts/tenants-users-roles.md)。

对于每个授权，你需要提供以下信息****。 然后，您可以根据需要多次选择 **+ 添加授权**，以添加更多用户和角色定义。

- **Azure AD 对象 ID**：用户、用户组或应用程序的 Azure AD 标识符，这些权限将被授予客户资源的特定权限（由角色定义定义）。
- **Azure AD 对象显示名称**：一个友好名称，用于帮助客户了解此授权的目的。 在委派资源时，客户将看到此名称。
- **角色定义**：从列表中选择一个可用的 Azure AD 内置角色。 此角色将确定“Azure AD 对象 ID”字段中的用户对客户资源拥有的权限****。 有关这些角色的说明，请参阅[Azure 委派资源管理的](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)[内置角色](../../role-based-access-control/built-in-roles.md)和角色支持。
  > [!NOTE]
  > 由于适用的新内置角色已添加到 Azure 中，它们将在此处可用，尽管在出现之前可能会有一些延迟。
- **可分配角色**：仅当已在此授权**的角色定义**中选择"用户访问管理员"时，才会显示此选项。 如果是这样，则必须在此处添加一个或多个可分配角色。 Azure AD**对象 ID**字段中的用户将能够将这些角色分配给[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可以修复的策略](../../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 请注意，通常与用户访问管理员角色关联的其他权限均不适用于此用户。

> [!TIP]
> 为确保在需要时可以[删除对委派的访问](../../lighthouse/how-to/onboard-customer.md#remove-access-to-a-delegation)，请包括一个**授权，** 其中**的角色定义**设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成计划的所有部分后，可以选择 **"创建新计划**"的次数，以创建其他计划。 完成后，选择“保存”****。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交优惠以预览

完成产品/服务的所有必需部分后，选择门户右上角的**发布**。 您将被重定向到 **"审阅"和"发布"** 页。

如果您是首次发布此产品/服务，您可以：

- 请参阅产品/服务的每个部分的完成状态。
  - *未启动*- 表示该部分尚未触摸且需要完成。
  - *不完整*- 表示该节有需要修复的错误或需要提供更多信息。 返回部分并更新它。
  - *完成*- 表示部分已完成，提供了所有必需的数据，并且没有错误。 报价的所有部分都必须处于完全状态，然后才能提交报价。
- 在 **"认证备注"** 部分中，除了任何有助于了解应用的补充说明外，请向认证团队提供测试说明，以确保正确测试应用。
- 通过选择 **"提交**"提交要发布的产品/ 当产品/服务预览版本可供您审核和批准时，我们将向您发送一封电子邮件。 返回合作伙伴中心，选择 **"Go-live"** 以优惠形式向公众发布您的优惠（如果是私人优惠，则向私人受众发布）。

### <a name="customer-experience-and-offer-management"></a>客户体验和产品管理

当客户部署产品/服务时，他们将能够委派订阅或资源组以进行[Azure 委派的资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 有关此过程的更多，请参阅[客户入职流程](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)。

你可以随时[发布产品/服务](update-existing-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序****](../../lighthouse/how-to/view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都可以查看更改并决定是否要更新到新版本。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
- [了解 Azure 灯塔](../../lighthouse/overview.md)