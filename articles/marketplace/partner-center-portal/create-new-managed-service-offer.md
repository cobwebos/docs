---
title: 在 Microsoft 商业市场中创建托管服务产品/服务
description: 如何使用合作伙伴中心的商业市场门户创建新的托管服务产品/服务，以在 Azure 市场中列出。
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 3099c73849a63ebf490d026db95cedfd1278d735
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957573"
---
# <a name="create-a-managed-service-offer"></a>创建托管服务产品

托管服务产品/服务有助于实现 [Azure Lighthouse](../../lighthouse/overview.md) 方案。 如果客户接受托管服务产品/服务，则可以为 [Azure 委派资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)载入资源。 在开始之前，[在合作伙伴中心创建商业市场帐户](create-account.md)（如果尚未这样做）。 确保你的帐户已在商业市场计划中注册。

若要发布托管服务产品/服务，你必须具有[白银级或黄金级云平台资格级别](https://partner.microsoft.com/membership/cloud-platform-competency)或 [Azure 专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp)。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择“商业市场” > “概述”。
3. 在“概述”页，选择“+ 新建产品/服务” > “托管服务”。

    ![左侧导航菜单图示。](./media/new-offer-managed-service.png)

>[!NOTE]
>发布产品/服务后，在合作伙伴中心对其进行的编辑仅在重新发布该产品/服务后才会显示在店面中。 确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你的帐户中每个产品/服务的唯一标识符。

* 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（如果适用）。
* 只使用小写字母和数字。 其中可以包含连字符和下划线，但不能包含空格，并且不得超过 50 个字符。 例如，如果输入“test-offer-1”，则产品/服务 Web 地址将为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
* 选择“创建”后，无法更改产品/服务 ID。

输入“产品/服务别名”。 这是用于合作伙伴中心的产品/服务的名称。

* 此名称不用于市场，并且与向客户显示的产品/服务名称和其他值不同。
* 选择“创建”后，无法更改产品/服务别名。

选择“创建”以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

根据[托管服务认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，需要潜在顾客目标。 每次客户部署你的产品/服务时，这将在 CRM 系统中创建一条记录。

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

借助此页，可以定义用于在市场上对产品/服务进行分组的类别，以及支持你的产品/服务的法律合同。

### <a name="category"></a>类别

选择至少一个且最多五个类别，用于将产品/服务放入适当的市场搜索区域。 确保在产品/服务说明中强调产品/服务如何支持这些类别。

### <a name="terms-and-conditions"></a>条款和条件

在“条款和条件”字段中提供自己的法律条款和条件。 还可以提供可在其中找到条款和条件的 URL。 客户将需要接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续。

## <a name="offer-listing"></a>产品/服务列表

借助此页，可以定义产品/服务的市场详细信息（例如产品/服务名称、说明和图像）。

> [!NOTE]
> 只要产品/服务说明使用以下短语开头：“此应用程序仅以[非英语语言]提供”，产品/服务列表内容（例如说明、文档、屏幕截图和使用条款）就不需要是英语。 还可以提供一个有用链接 URL，使用与产品/服务列表内容中所使用的语言不同的语言提供内容。

下面是有关如何在 Azure 门户中显示产品/服务的示例：

:::image type="content" source="media/example-managed-services.png" alt-text="说明了此产品/服务在 Azure 门户中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. Title
2. 描述
3. 有用链接
4. 屏幕截图

### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供可能会在市场搜索结果中使用的产品/服务的简短说明（最多 100 个字符）。

### <a name="long-summary"></a>长摘要

提供产品/服务的较长说明（最多 256 个字符）。 此长摘要也可能会用于市场搜索结果。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 URL（在网站上托管）。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="useful-links"></a>有用链接

提供有关你的解决方案的可选补充联机文档。 通过单击“+ 添加链接”添加其他有用链接。

### <a name="contact-information"></a>联系信息

在本部分中，必须提供支持联系人和工程联系人的姓名、电子邮箱和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并可能会提供给 CSP 合作伙伴。

### <a name="support-urls"></a>支持 URL

如果你有面向 Azure 全球客户和/或 Azure 政府客户的支持网站，请在此处提供这些 URL。

### <a name="marketplace-images"></a>市场映像

在此部分中，可以提供在向客户展示你的产品/服务时使用的徽标和图像。 所有图像都必须采用 .png 格式。

>[!NOTE]
>如果上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="marketplace-logos"></a>市场徽标

提供以下四个像素大小的产品/服务徽标：

- 小图 (48 x 48)
- 中图 (90 x 90)
- 大图 (216 x 216)
- 宽图 (255 x 115)

请遵守徽标适用的下述准则：

- Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
- 门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。
- 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
- 徽标的外观应平整，并且应避免渐变。 不要在徽标上使用渐变背景。
- 不要在徽标上放置文本，即使是公司或品牌名称也不可以。
- 确保徽标未被拉伸。

#### <a name="screenshots"></a>屏幕截图

最多添加五个显示产品/服务工作方式的屏幕截图。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可选择添加最多 5 个视频来演示产品/服务。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，输入视频的名称、URL 和视频的缩略图（1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

- [市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)

选择“保存草稿”，然后继续操作。

## <a name="preview"></a>预览

在将产品/服务发布到更广泛的市场产品/服务前，需要先将其提供给有限的预览版受众。 这样一来，你可以在向客户提供产品/服务之前，确认产品/服务在 Azure 市场中显示方式。 在此预览期间，Microsoft 支持和工程团队也将能够查看你的产品/服务。

可通过在“预览版受众”部分中输入 Azure 订阅 ID 来定义预览版受众。 最多可以手动输入 10 个订阅 ID，或上传包含最多 100 个订阅 ID 的 .csv 文件。

在产品/服务上线之前，与这些订阅关联的所有客户都将能够在 Azure 市场中查看该产品/服务。 请确保在此处包含你自己的订阅，以便可以预览产品/服务。

选择“保存草稿”，然后继续。

## <a name="plan-overview"></a>计划概述

每个产品/服务都必须具有一个或多个计划（有时也称为 SKU）。 可添加多个计划，以不同的价格支持不同的功能集，也可为有限数量的特定客户自定义特定计划。 客户可在父级产品/服务中查看可供其使用的计划。

在“计划概述”页，选择“+ 创建新计划”。 然后输入“计划 ID”和“计划名称”。 这两个值只能包含小写的字母数字字符、短划线和下划线，且长度不得超过 50 个字符。 这些值可能会对客户可见，在发布产品/服务后，无法更改这些值。

输入这些值后，请选择“创建”以继续处理计划。 需要完成三个部分：计划列表、定价和可用性以及技术配置。

### <a name="plan-listing"></a>计划列表

首先，为计划提供“搜索结果摘要”。 这是可能会在市场搜索结果中使用的计划的简短说明（最多 100 个字符）。

接下来，输入更详细地介绍计划的说明。

### <a name="pricing-and-availability"></a>定价和可用性

目前，只有一种定价模型可用于托管服务产品/服务：**自带许可 (BYOL)** 。 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。

“计划可见性”部分让你可以指示此计划是否应为[专用](../../marketplace/private-offers.md)。 如果未选中“这是专用计划”复选框，则计划将不限于特定客户（或特定数量的客户）。

要使此计划仅对特定客户可用，请选择“是”。 执行此操作时，需要提供客户的订阅 ID 来确定客户身份。 可逐一输入（最多 10 个订阅），也可上传一个 .csv 文件（所有计划最多 10,000 个订阅）。 请确保在此处包含你自己的订阅，以便测试和验证该产品/服务。

> [!IMPORTANT]
> 一旦某个计划已发布为公用，就无法将其更改为专用。 若要控制哪些客户可以接受产品/服务并委派资源，请使用专用计划。 使用公用计划时，不能将其可用性限制为特定客户，甚至不能限制特定数量的客户（不过，如果选择这样做，可完全停止销售计划）。 仅当在发布产品/服务时，包含将“角色定义”设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”的情况下，才能在客户接受产品/服务后[删除对委派的访问权限](../../lighthouse/how-to/remove-delegation.md)。 你还可以与客户联系，要求他们[删除你的访问权限](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="technical-configuration"></a>技术配置

计划的此部分会创建一个清单，其中包含用于管理客户资源的授权信息。 若要启用 [Azure 委派资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)，必须使用此信息。

确保查看 [Azure Lighthouse 方案中的租户、角色和用户](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以了解支持的角色以及定义授权的最佳做法。

> [!NOTE]
> 如上所述，“授权”条目中的用户和角色将应用于每个购买了计划的客户。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

#### <a name="manifest"></a>清单

首先，请提供清单的版本。 使用 n.n.n 格式（例如 1.2.5）。

然后，输入租户 ID。 这是与组织的 Azure Active Directory (Azure AD) 租户 ID 关联的 GUID；即你将在其中访问客户资源的管理租户。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它。

如果发布产品/服务的新版本并需要创建更新的清单，请选择“+ 新建清单”。 确保增加之前清单版本的版本号。

#### <a name="authorization"></a>授权

授权定义了管理租户中的哪些实体可访问购买计划的客户的资源和订阅。 其中每个实体都分配有一个授予特定访问级别的内置角色。

最多可以为每个计划创建二十个授权。

> [!TIP]
> 在大多数情况下，需要将角色分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 将角色分配给 Azure AD 组时，[请确保“组类型”是“安全性”，而不是“Office 365”](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../../lighthouse/concepts/tenants-users-roles.md)。

对于每个授权，你需要提供以下信息。 然后，可根据需要多次选择“+ 添加授权”，以添加更多用户和角色定义。

- **Azure AD 对象 ID**：要向其授予客户资源特定权限（如角色定义所定义）的用户、用户组或应用程序的 Azure AD 标识符。
- **Azure AD 对象显示名称**：一个易记名称，可帮助客户了解此授权的目的。 在委派资源时，客户将看到此名称。
- **角色定义**：从列表中选择一个可用的 Azure AD 内置角色。 此角色将确定“Azure AD 对象 ID”字段中的用户对客户资源拥有的权限。 有关这些角色的说明，请参阅[内置角色](../../role-based-access-control/built-in-roles.md)和 [Azure 委派资源管理的角色支持](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)。
  > [!NOTE]
  > 将适用的新内置角色添加到 Azure 后，它们将在此处可用，但在它们显示前可能会有一些延迟。
- **可分配角色**：仅当在“角色定义”中为此授权选择了“用户访问管理员”时，此选项才会显示。 如果是这样，则必须在此处添加一个或多个可分配角色。 “Azure AD 对象 ID”字段中的用户能够将这些角色分配给[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可修正的策略](../../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 请注意，通常与用户访问管理员角色关联的其他权限均不适用于此用户。

> [!TIP]
> 若要确保可以根据需要[删除对委派的访问权限](../../lighthouse/how-to/remove-delegation.md)，请包括“角色定义”设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成计划的所有部分后，可以根据需要多次选择“+ 创建新计划”来创建其他计划。 完成后，选择“保存”。

选择“保存草稿”，然后继续。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交产品/服务进行预览

完成产品/服务的所有必需部分后，请选择门户右上角的“查看和发布”。

如果这是第一次发布此产品/服务，则可以：

- 查看产品/服务各个部分的完成状态。
  - **未开始** - 尚未涉及该部分，需要完成。
  - **未完成** - 本部分包含需要修复的错误或需要提供详细信息。 返回相应部分并进行更新。
  - **完成** - 该部分已完成，已提供所有必需的数据，并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 在“认证说明”部分，除了有助于理解应用的任何补充说明之外，还可以向认证团队提供测试说明，以确保应用得到正确测试。
- 通过选择“提交”来提交要发布的产品/服务。 当产品/服务的预览版本可供评审和审批时，我们将向你发送一封电子邮件。 返回合作伙伴中心并为产品/服务选择“上线”，以将产品/服务发布给公众（或者，如果是专用产品/服务，则发布给专用受众）。

### <a name="customer-experience-and-offer-management"></a>客户体验和产品/服务管理

当客户部署你的产品/服务时，他们将能够委派订阅或资源组，以进行 [Azure 委派资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 有关此过程的详细信息，请参阅[客户加入过程](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)。

你可以随时[发布产品/服务](update-existing-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序](../../lighthouse/how-to/view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都将能够查看更改并决定是否要更新到新版本。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
- [了解 Azure Lighthouse](../../lighthouse/overview.md)
