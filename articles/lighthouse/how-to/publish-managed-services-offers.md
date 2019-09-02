---
title: 将托管服务产品发布到 Azure 市场
description: 了解如何发布将客户载入到 Azure 委派资源管理的托管服务产品。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: f9d3fad2a98647bcd10d54c03a76e95bc3e05227
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011867"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>将托管服务产品发布到 Azure 市场

在本文中，你将学习如何使用[云合作伙伴门户](https://cloudpartner.azure.com/)将公共或专用托管服务产品发布到 [Azure 市场](https://azuremarketplace.microsoft.com)，从而能针对 Azure 委派资源管理载入购买此产品/服务的客户。

> [!NOTE]
> 若要创建和发布这些产品/服务，需要拥有有效的[合作伙伴中心帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。 如果还没有帐户，[注册过程](https://aka.ms/joinmarketplace)将引导你按照步骤在合作伙伴中心创建帐户并注册商业市场计划。 你的 Microsoft 合作伙伴网络 (MPN) ID 将[自动关联](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started)你发布的产品/服务，以跟踪你在客户参与中的影响。
>
> 如果不想将产品/服务发布到 Azure 市场，可使用 Azure 资源管理器模板手动载入客户。 有关详细信息，请参阅[将客户载入到 Azure 委派资源管理](onboard-customer.md)。

与将其他任何类型的产品/服务发布到 Azure 市场相比，发布托管服务产品的过程很类似。 要了解此过程，请参阅 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)和[管理 Azure 和 AppSource 市场产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers)。 还应查看[商业市场认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies)，特别是[托管服务](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)部分。

> [!IMPORTANT]
> 托管服务产品中的每个计划都有一个“清单详细信息”部分，你在此处定义租户中将有权访问委派资源组和/或购买该计划的客户的订阅的 Azure Active Directory (Azure AD) 实体  。 请注意，此处包括的任何组（或用户/服务主体）将对购买此计划的每位客户具有相同的权限。 要分配不同的组来应对每位客户，需要发布单独的专门针对每位用户的计划。

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>在云合作伙伴门户中创建产品/服务

1. 登录[云合作伙伴平台](https://cloudpartner.azure.com/)。
2. 从左侧导航菜单中，选择“新建产品/服务”，然后选择“托管服务”   。
3. 你将看到产品/服务的“编辑”部分，其中有 4 个部分需要填写  ：“产品/服务设置”、“计划”、“市场”和“支持”     。 请继续阅读，了解如何填写这些部分。

## <a name="enter-offer-settings"></a>输入产品/服务设置

在“产品/服务设置”部分中，提供以下信息  ：

|字段  |说明  |
|---------|---------|
|**产品/服务 ID**     | （发布者配置文件内）产品/服务的唯一标识符。 此 ID 只能包含小写字母数字字符、短划线和下划线，且不得超过 50 个字符。 请记住，客户可能会在产品 URL 和计费报表等位置看到产品/服务 ID。 一旦发布产品/服务，即无法更改此值。        |
|**发布者 ID**     | 将与产品/服务关联的发布者 ID。 如果拥有多个发布者 ID，可选择要用于此产品/服务的 ID。       |
|**名称**     | 你的产品/服务将在 Azure 市场和 Azure 门户中向客户显示的名称（不超过 50 个字符）。 请使用客户能理解的可识别的品牌名称 - 如果正在通过自己的网站推广此产品/服务，请确保在此处使用完全相同的名称。        |

完成后，选择“保存”  。 现在，可继续转到“计划”部分  。

## <a name="create-plans"></a>创建计划

每个产品/服务都必须具有一个或多个计划（有时也称为 SKU）。 可添加多个计划，以不同的价格支持不同的功能集，也可为有限数量的特定客户自定义特定计划。 客户可在父级产品/服务中查看可供其使用的计划。

在“计划”部分中，针对要创建的每个计划选择“新建计划”  。 然后，输入计划 ID  。 此 ID 只能包含小写字母数字字符、短划线和下划线，且不得超过 50 个字符。 客户可能会在产品 URL 和计费报表等位置看到计划 ID。 一旦发布产品/服务，即无法更改此值。

接下来，完成“计划详细信息”部分中的以下部分  ：

|字段  |说明  |
|---------|---------|
|**标题**     | 要显示的计划的易记名称。 最大长度为 50 个字符。        |
|**摘要**     | 标题下显示的计划的简洁说明。 最大长度为 100 个字符。        |
|**说明**     | 更详细地介绍计划的说明文本。         |
|**计费模式**     | 此处显示两种计费模式，但必须为托管服务产品选择“自带许可”  。 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。   |
|**这是专用计划吗？**     | 指示该 SKU 是专用还是公共 SKU。 默认值为“否”（即公共 SKU）  。 如果保留此选择，则你的计划将不仅限于特定客户（或特定数量的客户）；发布公用计划后，无法再将其更改为专用计划。 要使此计划仅对特定客户可用，请选择“是”  。 执行此操作时，需要提供客户的订阅 ID 来确定客户身份。 可逐一输入（最多 10 个订阅），也可上传一个 .csv 文件（最多 20,000 个订阅）。 请确保在此处包含你自己的订阅，以便测试和验证该产品/服务。 有关详细信息，请参阅[专用 SKU 和计划](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus)。  |

最后，完成“清单详细信息”部分  。 这会创建一个清单，其中包含用于管理客户资源的授权信息。 要针对 Azure 委托资源管理载入客户，必须使用你在此处提供的信息。 如上所述，这些权限将应用于购买了计划的每位客户，因此如果想要仅限特定客户访问，需要发布专用计划供其专门使用。

- 首先，请提供清单的版本  。 使用 n.n.n 格式（例如 1.2.5）  。
- 然后，输入租户 ID  。 这是与你组织的 Azure Active Directory 租户 ID 关联的 GUID（即你将在其中管理客户资源的租户）。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它  。 
- 最后，将一个或多个授权条目添加到计划中  。 授权定义了哪些实体可访问购买计划的客户的资源和订阅。 必须提供此信息才能通过 Azure 委派资源管理代表客户访问资源。
  对于每个授权，请提供以下信息。 然后，可根据需要多次选择“新授权”，以添加更多用户/角色定义  。
  - **Azure AD 对象 ID**：要将客户资源特定权限（如角色定义所述）授予到的用户、用户组或应用程序的 Azure AD 标识符。
  - **Azure AD 对象显示名称**：一个易记名称，可帮助客户了解此授权的目的。 在委派资源时，客户将看到此名称。
  - **角色定义**：从列表中选择一个可用的 Azure AD 内置角色。 此角色将确定“Azure AD 对象 ID”字段中的用户对客户资源拥有的权限  。 有关这些角色的详细信息，请参阅[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。
  - **可分配角色**：如果已在“角色定义”中针对此授权选择了“用户访问管理员”，则可在此添加一个或多个可分配角色  。 “Azure AD 对象 ID”字段中的用户能将这些可分配角色分配给[托管标识](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity)   。 请注意，通常与用户访问管理员角色关联的其他权限均不适用于此用户。 （如果未为此用户的角色定义选择用户访问管理员，则此字段无效。）

> [!TIP]
> 在大多数情况下，需要将权限分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。

添加计划后，选择“保存”，然后继续到“市场”部分   。

## <a name="provide-marketplace-text-and-images"></a>提供市场文本和图像

可在“市场”部分提供客户要在 Azure 市场和 Azure 门户看到的文本和图像  。

在“概述”部分中提供以下字段的信息  ：

|字段  |说明  |
|---------|---------|
|**标题**     |  套餐的标题，通常是较长的正式名称。 此标题将在市场中突出显示。 最大长度为 50 个字符。 大多数情况下，此名称应与你在“产品/服务设置”部分输入的名称相同   。       |
|**摘要**     | 产品/服务用途或功能的简要说明。 通常显示在标题下方。 最大长度为 100 个字符。        |
|**长摘要**     | 产品/服务用途或功能的详细摘要。 最大长度为 256 个字符。        |
|**说明**     | 有关产品/服务的详细信息。 此字段不得超过 3000 个字符，且支持简单的 HTML 格式。 必须在说明中的某个位置包含“托管服务”字样。       |
|**营销标识符**     | 唯一的 URL 友好标识符。 它将在此产品/服务的市场 URL 中使用。 例如，如果发布者 ID 为 contoso，营销标识符为 sampleApp，则产品/服务在 Azure 市场中的 URL 为 https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp    。        |
|**预览订阅 ID**     | 添加 1 到 100 个订阅标识符。 在产品/服务上线之前，与这些订阅关联的客户将可在 Azure 市场中查看此产品/服务。 建议在此处包含你自己的订阅，以便在向客户提供产品/服务之前，可预览其在 Azure 市场中的显示方式。  （在此预览期间，Microsoft 支持和工程团队也将能够查看你的产品/服务。）   |
|**有用链接**     | 与你的产品/服务相关的 URL，例如文档、发行说明、常见问题解答等等。        |
|**建议的类别（最多 5 个）**     | 适用于你的产品/服务的一个或多个类别（最多 5 个）。 这些类别可帮助客户在 Azure 市场和 Azure 门户中发现你的产品/服务。        |

在“营销项目”部分，可上传徽标和其他资产，使其与产品/服务一起显示  。 可选择性地上传屏幕截图或视频链接，帮助客户了解你的产品/服务。

需要 4 种徽标大小：小 (40x40)、中 (90x90)、大 (115x115) 和特大 (255x155)     。 请遵守徽标适用的下述准则：

- Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
- 门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。
- 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
- 徽标的外观应平整，并且应避免渐变。 不要在徽标上使用渐变背景。
- 不要在徽标上放置文本，即使是公司或品牌名称也不可以。
- 确保徽标未被拉伸。

特大 (815x290) 徽标可选，但建议使用它  。 如果包含了特大徽标，请遵循以下准则：

- 特大徽标中不得包含任何文本，且务必在徽标右侧留出 415 像素的空白空间。 目的是有空间以编程方式嵌入发布者显示名称、计划标题和产品/服务详细摘要等文本元素。
- 特大徽标的背景不得为黑色、白色或透明。 请确保背景色较深，因为嵌入的文本将显示为白色。
- 一旦使用特大图标发布产品/服务，便不能将其删除（但如果需要，可使用其他版本进行更新）。

可在“潜在顾客管理”部分中，选择将存储潜在顾客的 CRM 系统  。 请注意，根据[托管服务认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，需要**潜在顾客目标**。

最后，在“法律”部分提供隐私策略 URL 和使用条款    。 还可在此处指定是否对此产品/服务使用[标准协定](https://docs.microsoft.com/azure/marketplace/standard-contract)。

转到“支持”部分之前，请务必保存更改  。

## <a name="add-support-info"></a>添加支持信息

在“支持”部分中，提供工程联系人和客户支持联系人的姓名、电子邮件和电话号码  。 还需要提供支持 URL。 当我们需要就业务和支持问题与你联系时，Microsoft 可能会使用此信息。

添加此信息后，请选择“保存”  。

## <a name="publish-your-offer"></a>发布产品/服务

如果你对自己提供的所有信息感到满意，则接下来就是将产品/服务发布到 Azure 市场。 选择“发布”按钮启动产品/服务上线过程  。 有关此过程的更多信息，请参阅[发布 Azure 市场和 AppSource 产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer)。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 访问 Azure 门户中的“我的客户”，[查看和管理客户](view-manage-customers.md)  。
