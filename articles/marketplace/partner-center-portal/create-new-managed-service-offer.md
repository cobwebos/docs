---
title: 在 Microsoft 商业应用商店中创建托管服务产品/服务
description: 如何使用合作伙伴中心的商业应用商店门户在 Azure Marketplace 中创建新的托管服务产品/服务。
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 41b2e12a94f5751e31f482c913c71b4731b1cf6c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871901"
---
# <a name="create-a-managed-service-offer"></a>创建托管服务产品

托管服务提供了帮助来启用[Azure Lighthouse](../../lighthouse/overview.md)方案。 当客户接受托管服务产品/服务后，他们就可以为[Azure 委托的资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)加入资源。 在开始之前，请[在合作伙伴中心创建一个商用 Marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在 "商用 marketplace 计划" 中注册。

若要发布托管服务产品/服务，你必须拥有[银牌或金牌云平台资格认证级别](https://partner.microsoft.com/membership/cloud-platform-competency)或[Azure 专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp) 。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新建产品** > /**服务**"。

    ![阐释左侧导航菜单。](./media/new-offer-managed-service.png)

>[!NOTE]
>发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

* 适用于 marketplace 产品/服务和 Azure 资源管理器模板的 web 地址中的客户可看到此 ID （如果适用）。
* 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果输入了 "**测试/服务-1**"，则 "产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`" 网址为。
* 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

* 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
* 选择 "**创建**" 后，无法更改第一个产品/服务别名。

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

根据[托管服务认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，需要**潜在客户目标**。 这会在每次客户部署产品/服务时，在 CRM 系统中创建一条记录。

有关详细信息，请参阅[潜在客户管理概述](./commercial-marketplace-get-customer-leads.md)。

选择 "**保存草稿**"，然后继续。

## <a name="properties"></a>属性

在此页上，你可以定义用于在 marketplace 上对产品/服务进行分组的类别，以及支持你的产品/服务的法律合同。

### <a name="category"></a>类别

选择最小值，最多可选择5个类别，用于将产品/服务置于适当的 marketplace 搜索区域。 在产品/服务描述中，请务必了解你的产品/服务如何支持这些类别。

### <a name="terms-and-conditions"></a>条款和条件

在**条款和条件**字段中提供自己的法律条款和条件。 你还可以提供 URL，你可以在其中找到你的条款和条件。 客户需要接受这些条款，然后才能试用你的产品/服务。

选择 "**保存草稿**"，然后继续。

## <a name="offer-listing"></a>产品/服务列表

此页可让你为产品/服务定义 marketplace 详细信息（如产品/服务的名称、描述和图像）。

> [!NOTE]
> 产品/服务列表内容（如描述、文档、屏幕截图和使用条款）不要求使用英语，只要产品/服务说明以短语开头，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个*有用的链接 URL*来提供与产品/服务列表内容中所用语言不同的内容。

### <a name="name"></a>名称

你在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预填充了你在创建产品/服务时为 "**产品/服务别名**" 输入的文本，但你可以更改此值。 此名称可能是商标字（并且你可以包括商标或版权符号）。 名称不能超过50个字符，并且不能包含任何表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明（最多100个字符），可以在 marketplace 搜索结果中使用。

### <a name="long-summary"></a>长摘要

提供产品/服务的更长说明（最多256个字符）。 此长摘要还可在 marketplace 搜索结果中使用。

### <a name="description"></a>说明

提供产品/服务的更长说明（最多3000个字符）。 此说明将在 marketplace 列表概述中向客户显示。 包括产品/服务的价值主张、主要优势、类别和/或行业关联、应用内购买机会，以及任何所需的公开。

编写说明的一些提示：  

- 在前几句描述中清晰描述产品/服务的价值主张。 在价值主张中包括以下各项：
  - 产品/服务说明
  - 从产品/服务中受益的用户类型
  - 产品/服务地址的客户需求或难点
- 请牢记，前几句话可能在搜索引擎结果中显示。  
- 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
- 尽可能多使用行业特定用语或基于优势的措辞。

若要使您的产品/服务说明更具吸引力，请使用富文本编辑器应用格式设置。

![使用富文本编辑器](./media/rich-text-editor.png)

| <center>更改文本格式 | <center>添加项目符号或编号 | <center>添加或删除文本缩进 |
| --- | --- | --- |
| <center>![使用富文本编辑器更改文本格式](./media/text-editor3.png) |  <center>![使用富文本编辑器添加列表](./media/text-editor4.png) |  <center>![使用富文本编辑器缩进](./media/text-editor5.png) |

### <a name="privacy-policy-link"></a>隐私策略链接

输入你的组织的隐私策略的 URL （托管在你的网站上）。 你需要负责确保你的应用符合隐私法律和法规，并提供有效的隐私策略。

### <a name="useful-links"></a>有用链接

提供有关解决方案的可选补充联机文档。 单击 " **+ 添加链接**"，添加其他有用的链接。

### <a name="contact-information"></a>联系信息

在本部分中，你必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但会提供给 Microsoft，并可提供给 CSP 合作伙伴。

### <a name="support-urls"></a>支持 Url

如果你有**Azure 全球客户**和/或**azure 政府客户**的支持网站，请在此处提供这些 url。

### <a name="marketplace-images"></a>Marketplace 映像

在本部分中，可以提供向客户显示产品/服务时要使用的徽标和图像。 所有图像必须采用 .png 格式。

>[!NOTE]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="marketplace-logos"></a>Marketplace 徽标

提供产品/服务的徽标，大小为四个像素：

- **小型**（40 x 40）
- **中**（90 x 90）
- **大**（115 x 115）
- **宽**（255 x 115）

请遵守徽标适用的下述准则：

- Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
- 门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。
- 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
- 徽标的外观应平整，并且应避免渐变。 不要在徽标上使用渐变背景。
- 不要在徽标上放置文本，即使是公司或品牌名称也不可以。
- 确保徽标未被拉伸。

#### <a name="screenshots"></a>屏幕截图

最多可添加五个屏幕截图，其中显示了产品/服务的工作方式。 所有屏幕截图必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

你可以选择性地添加最多5个视频来演示你的产品/服务。 这些视频应托管在 YouTube 和/或 Vimeo 上。 对于每个名称，请输入视频的名称、其 URL 和视频的缩略图（1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

- [Marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择 "**保存草稿**"，然后继续。

## <a name="preview"></a>预览

在将产品/服务实时发布到更广泛的 marketplace 产品/服务之前，首先需要将其提供给有限的预览受众。 这样，你就可以在将其提供给客户之前，确认你的产品/服务在 Azure Marketplace 中的显示方式。 在此预览期间，Microsoft 支持团队和工程团队还将能够查看你的产品/服务。

可以通过在**预览受众**部分输入 Azure 订阅 id 来定义预览受众。 可以手动输入最多10个订阅 Id，或上传最多包含100个订阅 Id 的 .csv 文件。

与这些订阅关联的任何客户都可以在 Azure Marketplace 中查看该产品/服务。 请确保在此处包含你自己的订阅，以便可以预览你的产品/服务。

选择 "**保存草稿**"，然后继续。

## <a name="plan-overview"></a>计划概述

每个产品/服务都必须具有一个或多个计划（有时也称为 SKU）。 可添加多个计划，以不同的价格支持不同的功能集，也可为有限数量的特定客户自定义特定计划。 客户可在父级产品/服务中查看可供其使用的计划。

在 "**计划概述**" 页上，选择 " **+ 创建新计划**"。 然后输入**计划 ID**和**计划名称**。 这两个值只能包含小写字母数字字符、短划线和下划线，最多可包含50个字符。 这些值可能对客户可见，在发布产品/服务后，这些值将无法更改。

输入这些值后，请选择 "**创建**" 以继续处理计划。 需要完成三个部分：**计划列表**、**定价和可用性**以及**技术配置**。

### <a name="plan-listing"></a>计划列表

首先，提供计划的**搜索结果摘要**。 这是计划的简短说明（最多100个字符），可以在 marketplace 搜索结果中使用。

接下来，输入详细**说明**计划。

### <a name="pricing-and-availability"></a>定价和可用性

目前，只有一个可用于托管服务产品/服务的定价模型：**自带许可证（BYOL）**。 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。

"**计划可见性**" 部分用于指示此计划是否应为[私有](../../marketplace/private-offers.md)。 如果保留 "这是未选中的**私有计划**框"，则不会将计划限制为特定客户（或特定数量的客户）。

要使此计划仅对特定客户可用，请选择“是”****。 执行此操作时，需要提供客户的订阅 ID 来确定客户身份。 可以逐个输入（最多10个订阅），也可以上传 .csv 文件（在所有计划中最多可包含10000个订阅）。 请确保在此处包含你自己的订阅，以便测试和验证该产品/服务。

> [!IMPORTANT]
> 一旦某个计划作为公共计划发布，则不能将其更改为 "专用"。 若要控制哪些客户可以接受您的产品/服务和委托资源，请使用私有计划。 使用公用计划，您不能将可用性限制为特定客户或甚至是特定数量的客户（不过，如果您选择这样做，您可以完全停止销售计划）。 仅当你在发布产品/服务时，如果你在将**角色定义**设置为 "[托管服务注册分配](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)" "角色 **"，则**可以在客户接受产品/服务后[删除对委派的访问权限](../../lighthouse/how-to/remove-delegation.md)。 你还可以联系客户并要求他们[删除你的访问权限](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="technical-configuration"></a>技术配置

计划的此部分将创建一个清单，其中包含用于管理客户资源的授权信息。 若要启用[Azure 委托资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)，需要此信息。

务必查看[Azure Lighthouse 方案中的租户、角色和用户](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以了解受支持的角色，以及定义授权的最佳实践。

> [!NOTE]
> 如上所述，“授权”条目中的用户和角色将应用于每个购买了计划的客户****。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

#### <a name="manifest"></a>清单

首先，请提供清单的版本****。 使用 n.n.n 格式（例如 1.2.5）**。

然后，输入租户 ID****。 这是与你的组织的 Azure Active Directory （Azure AD）租户 ID 关联的 GUID;也就是说，将从其访问客户资源的管理租户。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它****。

如果发布产品/服务的新版本并需要创建更新的清单，请选择 " **+ 新建清单**"。 请确保增加之前清单版本的版本号。

#### <a name="authorization"></a>授权

授权定义你的管理租户中的实体，这些实体可以访问购买计划的客户的资源和订阅。 其中每个实体都分配有一个授予特定访问级别的内置角色。

对于每个计划，最多可以创建20个授权。

> [!TIP]
> 在大多数情况下，你需要将角色分配给 Azure AD 的用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 向 Azure AD 组分配角色时，请[确保**组类型**为 "**安全**"，而不是 " **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)"。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../../lighthouse/concepts/tenants-users-roles.md)。

对于每个授权，你需要提供以下信息****。 然后，可以根据需要选择 " **+ 添加授权**次数" 来添加更多用户和角色定义。

- **Azure AD 对象 ID**：用户、用户组或应用程序的 Azure AD 标识符，将向客户的资源授予特定权限（由角色定义定义）。
- **Azure AD 对象显示名称**：一个友好名称，可帮助客户了解此授权的用途。 在委派资源时，客户将看到此名称。
- **角色定义**：从列表中选择一个可用 Azure AD 内置角色。 此角色将确定“Azure AD 对象 ID”字段中的用户对客户资源拥有的权限****。 有关这些角色的说明，请参阅[Azure 委托资源管理](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)的[内置角色](../../role-based-access-control/built-in-roles.md)和角色支持。
  > [!NOTE]
  > 将适用的新内置角色添加到 Azure 时，它们将在此处可用，但可能会出现一些延迟后出现。
- 可**分配角色**：仅当在**角色定义**中为此授权选择了 "用户访问管理员" 时，才会显示此选项。 如果是这样，则必须在此处添加一个或多个可分配角色。 " **Azure AD 对象 ID** " 字段中的用户可以将这些角色分配给[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可修正的策略](../../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 请注意，通常与用户访问管理员角色关联的其他权限均不适用于此用户。

> [!TIP]
> 若要确保在需要时可以[删除对委派的访问权限](../../lighthouse/how-to/remove-delegation.md)，请包括将**角色定义**设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的**授权**。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成计划的所有部分后，可以根据需要选择 " **+ 创建新计划**" 来创建其他计划。 完成后，选择“保存”****。

选择 "**保存草稿**"，然后继续。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>将产品/服务提交到预览版

完成产品/服务的所有必需部分后，请选择门户右上角的 "**查看并发布**"。

如果你是首次发布此产品/服务，则可以：

- 请参阅产品/服务的每个部分的完成状态。
  - **未开始**-部分未被接触，需要完成。
  - **不完整**-部分包含需要修复的错误或需要提供更多信息的信息。 返回到部分并对其进行更新。
  - **完成**-该部分已完成，所有必需的数据都已提供，且没有错误。 产品/服务的所有部分必须处于完整状态，然后才能提交产品/服务。
- 在 "**认证说明**" 部分中，向认证团队提供测试说明，以确保正确测试应用程序，以及任何有助于了解应用程序的补充说明。
- 通过选择 "**提交**" 提交产品/服务进行发布。 当提供预览版时，我们将向你发送一封电子邮件，供你查看和批准。 返回到 "合作伙伴中心"，并选择 "产品 **/** 服务"，以将你的产品/服务发布到公共（如果是专用受众）。

### <a name="customer-experience-and-offer-management"></a>客户体验和产品/服务管理

当客户部署你的产品/服务时，他们将能够为[Azure 委托的资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)委派订阅或资源组。 有关此过程的详细信息，请参阅[客户加入流程](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)。

你可以随时[发布产品/服务](update-existing-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序****](../../lighthouse/how-to/view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都可以查看更改并决定是否要更新到新版本。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
- [了解 Azure Lighthouse](../../lighthouse/overview.md)
