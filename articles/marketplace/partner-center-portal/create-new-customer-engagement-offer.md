---
title: 在 Microsoft 商业应用商店中，为客户参与 & PowerApps 提议创建 Dynamics 365
description: 如何在 Azure Marketplace、AppSource 或合作伙伴中心的云解决方案提供商（CSP）计划中，为客户参与 & PowerApps 产品/服务创建新的 Dynamics 365。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: cfec905d61daf961d9ca71026e9b7ec82f852bd5
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734767"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>创建 Dynamics 365 for Customer Engagement & PowerApps 产品/服务

本主题介绍如何创建新的 Dynamics 365 for Customer Engagement & PowerApps 产品/服务。 适用于 Dynamics 365 for Customer Engagement 的所有应用（PowerApps、销售、服务、项目服务和现场服务）都必须经过我们的认证过程，并支持试用体验。 认证过程会检查解决方案的标准要求、兼容性和正确的做法。 试用体验允许用户将你的解决方案部署到实时 Dynamics 365 环境中。

在开始之前，请[在合作伙伴中心创建一个商用 Marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在 "商用 marketplace 计划" 中注册。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新产品** > /**服务 Dynamics 365 for Customer Engagement & PowerApps**"。

    ![阐释左侧导航菜单。](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

- 适用于 marketplace 产品/服务和 Azure 资源管理器模板的 web 地址中的客户可看到此 ID （如果适用）。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果输入了 "**测试/服务-1**"，则 "产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`" 网址为。
- 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

- 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
- 选择 "**创建**" 后无法更改此。

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

请按照以下步骤设置你的产品/服务。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>你希望潜在客户如何与此列表服务进行交互？

选择要用于此产品/服务的选项。

#### <a name="get-it-now-free"></a>立即获取（免费）

通过提供有效的 URL （以*http*或*https*开头）向客户免费列出你的产品/服务，用户可以在其中访问你的应用程序。  例如 `https://contoso.com/my-app`。

#### <a name="free-trial-listing"></a>免费试用（列出）

向客户列出你的产品/服务，其中包含免费试用版的链接，方法是提供有效的 URL （以*http*或*https*开头），用户可在其中获取试用版。  例如 `https://contoso.com/trial/my-app`。 产品/服务列表免费试用版由您的服务创建、管理和配置，并且没有 Microsoft 管理的订阅。

> [!NOTE]
> 您的应用程序将通过您的试用链接接收的令牌只能用于通过 Azure Active Directory （Azure AD）获取用户信息以在应用程序中自动创建帐户。 使用此令牌进行身份验证不支持 Microsoft 帐户。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理（CRM）系统来收集客户联系信息。 系统将要求客户提供共享其信息的权限。 这些客户详细信息以及他们找到你的产品/服务的产品/服务名称、ID 和 marketplace 源将发送到已配置的 CRM 系统。 有关配置 CRM 的详细信息，请参阅[连接潜在客户管理](#connect-lead-management)。 

选择 "**保存草稿**"，然后继续。

### <a name="test-drive"></a>体验版

通过向客户提供 "在购买前试用" 的选项，测试驱动器是向潜在客户展示你的产品/服务的一种很好的方法，导致增加转换和产生高度合格的潜在顾客。 [了解有关测试驱动器的详细信息](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用测试驱动器，请选中 "**启用测试驱动器**" 复选框。 若要从产品/服务中删除测试驱动器，请清除此复选框。 请在本主题后面的 "[测试驱动器技术配置](#test-drive-technical-configuration)" 部分中配置测试驱动器环境。

有关其他信息，请参阅[在商业应用商店中试用产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

#### <a name="type-of-test-drive"></a>测试驱动器的类型

选择从以下选项：

- **[Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**–包含构成解决方案的所有 Azure 资源的部署模板。 适用于此方案的产品只使用 Azure 资源。
- **[Dynamics 365 For Business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft 托管并维护企业中心企业资源规划系统（财务、运营、供应链、CRM 等）的测试驱动器服务（包括预配和部署）。  
- **[Dynamics 365 For Customer engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft 托管并维护客户参与系统（销售、服务、项目服务、现场服务等）的测试驱动器服务（包括预配和部署）。  
- **[适用于操作的 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft 托管并维护针对财务和运营企业资源规划系统（财务、运营、制造、供应链等）的测试驱动器服务（包括预配和部署）。 
- **[逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**–包含所有复杂解决方案体系结构的部署模板。 任何自定义产品都应使用这种类型的测试驱动器。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** –指向自定义生成的仪表板的嵌入链接。 要演示交互 Power BI 视觉对象的产品应使用此类型的测试驱动器。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他测试驱动器资源

- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [测试驱动器概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)PDF （确保弹出窗口阻止程序处于关闭状态）

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在客户管理概述](./commercial-marketplace-get-customer-leads.md)。

选择 "**保存草稿**"，然后继续。

## <a name="properties"></a>属性

此页面允许你定义用于在 marketplace 上对产品/服务进行分组的类别和行业、你的应用版本以及支持你的产品/服务的法律合同。

### <a name="category"></a>类别

选择至少一个和三个类别。 它们用于将产品/服务放入相应的 marketplace 搜索区域。 请确保在产品/服务描述中说明产品/服务如何支持这些类别。

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>适用的 Dynamics 365 产品

选择 "此产品/服务适用于的所有 Dynamics 365 产品"。

### <a name="app-version"></a>应用版本

输入产品的版本号。 客户将在产品/服务的详细信息页上列出此版本。 如果只是由于市场营销/描述性更改而更新版本号，请选中 "**仅市场营销更改**" 框。 此选项允许产品/服务跳过认证和预配阶段。

### <a name="terms-and-conditions"></a>条款和条件

在此处提供自己的法律条款和条件。 你还可以提供可找到你的条款和条件的地址。 客户需要接受这些条款，然后才能试用你的产品/服务。

选择 "**保存草稿**"，然后继续。

## <a name="offer-listing"></a>产品/服务列表

此页显示你的产品/服务将列出的语言。 目前，只有**英语（美国）** 是可用的选项。

在此处为每种语言/市场定义 marketplace 详细信息，例如产品/服务名称、说明和图像。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 产品/服务列表内容（如描述、文档、屏幕截图和使用条款）不要求使用英语，只要产品/服务说明以短语开头，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个*有用的链接 URL*来提供与产品/服务列表内容中所用语言不同的内容。

### <a name="name"></a>名称

你在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预填充了你在创建产品/服务时为 "**产品/服务别名**" 输入的文本，但你可以更改此值。 此名称可能是商标字（并且你可以包括商标或版权符号）。 名称不能超过50个字符，并且不能包含任何表情符号。

### <a name="short-description"></a>简短说明

提供产品/服务的简短描述，最多100个字符。 此说明可用于 marketplace 搜索结果中。

### <a name="description"></a>说明

提供产品/服务的更长说明，最多3000个字符。 此说明将在 marketplace 列表概述中向客户显示。 包括产品/服务的价值主张、主要优势、类别和/或行业关联、应用内购买机会，以及任何所需的公开。

编写说明的一些提示：

- 在前几句描述中清晰描述产品/服务的价值主张。 在价值主张中包含以下信息：
  - 该产品的说明
  - 从产品中受益的用户类型
  - 产品地址的客户需求或难点
- 请牢记，前几句话可能在搜索引擎结果中显示。  
- 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
- 尽可能多使用行业特定用语或基于优势的措辞。

若要使您的产品/服务说明更具吸引力，请使用富文本编辑器应用格式设置。

![使用富文本编辑器](./media/rich-text-editor.png)

| <center>更改文本格式 | <center>添加项目符号或编号 | <center>添加或删除文本缩进 |
| --- | --- | --- |
| <center>![使用富文本编辑器更改文本格式](./media/text-editor3.png) |  <center>![使用富文本编辑器添加列表](./media/text-editor4.png) |  <center>![使用富文本编辑器缩进](./media/text-editor5.png) |


### <a name="search-keywords"></a>搜索关键字

你可以根据需要输入最多三个搜索关键字，帮助客户在 marketplace 中找到你的产品/服务。 为了获得最佳结果，请在说明中使用这些关键字。

### <a name="products-your-app-works-with"></a>可与应用配合使用的产品

如果你想让客户知道你的应用程序适用于特定产品，请在此处输入最多三个产品名称。

### <a name="support-urls"></a>支持 Url

本部分允许你提供链接，以帮助客户了解你的产品/服务的详细信息。

#### <a name="help-link"></a>帮助链接

输入客户可在其中了解产品/服务的详细信息。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略地址。 你需要负责确保你的应用符合隐私法律和法规，并提供有效的隐私策略。

### <a name="contacts"></a>联系人

提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但会提供给 Microsoft，并可提供给 CSP 合作伙伴。

在 "**支持联系人**" 部分中，你还必须提供**支持 URL** ，CSP 合作伙伴可在其中找到对你的产品/服务的支持。

### <a name="supporting-documents"></a>支持文档

在此处提供至少一个（及最多三个）相关营销文档，如 PDF 格式的白皮书、小册子、清单或演示文稿。

### <a name="marketplace-images"></a>Marketplace 映像

提供产品/服务的徽标和图像。 所有图像必须为 PNG 格式。

>[!NOTE]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="store-logos"></a>应用商店徽标

提供产品/服务的徽标，大小为三个像素：
- **Small** （必需; 48 x 48）
- **大**（必需; 216 x 216）
- **宽**（可选; 255 x 115）

#### <a name="hero"></a>英雄

英雄映像是可选的。 如果提供了一个，则必须度量 815 x 290 像素。

#### <a name="screenshots"></a>屏幕截图

添加显示产品/服务工作方式的屏幕截图。 至少需要一个屏幕截图，最多可添加五个屏幕快照。 所有屏幕截图必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

您可以根据需要添加最多四个视频来演示您的产品/服务。 这些视频应托管在 YouTube 和/或 Vimeo 上。 对于每个名称，请输入视频的名称、其 URL 和视频缩略图（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

[Marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择 "**保存草稿**"，然后继续。

## <a name="availability"></a>可用性

使用此页，可以定义提供产品/服务的位置和方式。

### <a name="markets"></a>市场

指定提供产品/服务的市场。 为此，请选择 "**编辑市场**"，这将显示**市场选择**弹出窗口。

默认情况下，未选择市场，但你必须至少选择一个市场才能发布产品/服务。 选择 "全**选**" 以使你的产品/服务可在每个市场中使用，或者选择想要添加的特定市场。

此处的选择仅适用于新的收购;如果有人在某个市场上已经有了您的应用程序，后来又删除了该市场，则该市场上已有该产品/服务的人员可以继续使用该产品/服务，但该市场上的新客户将无法获得您的产品/服务。

> [!IMPORTANT]
> 你需要负责满足任何本地法律要求，即使这些要求未在此处或合作伙伴中心列出。 即使选择了所有市场，本地法律和限制或其他因素也可能会阻止某些国家和地区列出某些产品/服务。

### <a name="preview-audience"></a>预览受众

在将产品/服务实时发布到更广泛的 marketplace 产品/服务之前，首先需要将其提供给有限的**预览受众**。 在此处输入一个**Hide 键**（任何只使用小写字母和/或数字的字符串）。 预览版观众的成员可以将此隐藏密钥用作令牌，以查看 marketplace 中产品/服务的预览。

然后，当你准备好提供产品/服务并删除预览限制时，你将需要删除**隐藏密钥**并再次发布。

选择 "**保存草稿**"，然后继续。

## <a name="technical-configuration"></a>技术配置

本页定义用于连接产品/服务的技术详细信息。 此连接使我们能够为最终用户预配你的产品/服务。

### <a name="base-license-model"></a>基本许可模型

基本许可证模型确定如何将应用程序分配到 CRM 管理中心。 如果为每个租户分配了许可证，请选择 "基于实例的许可的**资源**" 或 "**用户**"。

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>需要 S2S 出站和 CRM 安全存储区访问

选中此框可启用 CRM 安全存储或服务器到服务器（S2S）出站访问的配置。 此功能要求遵循 Dynamics 365 团队在认证阶段专业提出的注意事项。 Microsoft 将与你取得联系，以完成附加的步骤来支持此功能。

### <a name="application-configuration-url"></a>应用程序配置 URL

提供配置网页的 URL，该 URL 允许客户配置应用。

### <a name="crm-package"></a>CRM 包

在 "**包位置的 url** " 字段中，输入包含已上传的 CRM 包 .zip 文件的 Azure 存储帐户的 url。 此 URL 应包含一个只读 SAS 密钥，以允许 Microsoft 提取你的包进行验证。

选中标签**中有多个 CRM 包**的框（如果适用）。 如果是这样，请确保在 .zip 文件中包含所有包。

### <a name="crm-package-availability"></a>CRM 包可用性

在本部分中，选择 " **+ 添加区域**"，以指定将向客户提供 CRM 包的地理区域。 在认证过程中，部署到以下主权区域需要特殊的权限和验证：[德国](https://docs.microsoft.com/azure/germany/)、[美国政府云和](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)技巧。

默认情况下，在上面输入的**应用程序配置 URL**将用于每个区域。 如果需要，可以为一个或多个特定区域输入单独的应用程序配置 URL。 

选择 "**保存草稿**"，然后继续。

## <a name="test-drive-technical-configuration"></a>测试驱动器技术配置

在此页上，你可以设置一个演示（"测试驱动器"），让客户在购买产品之前试用你的产品/服务。 有关详细信息，请参阅[什么是测试驱动器？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要启用某个测试驱动器，请选中 "[产品/服务设置](#test-drive)" 选项卡上的 "启用测试驱动器" 复选框。若要从产品/服务中删除测试驱动器，请清除此复选框。

有关详细信息，请参阅[什么是测试驱动器？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

可以使用以下类型的测试驱动器，每种都有其自己的技术配置要求：

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) （不需要技术配置）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器的技术配置

一个部署模板，其中包含组成解决方案的所有 Azure 资源。 适用于此方案的产品只使用 Azure 资源。 详细了解如何设置[Azure 资源管理器试用版驱动器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **区域**（必填）–当前存在26个支持 Azure 的区域，可在其中提供测试驱动器。 通常情况下，你将希望使你的测试驱动器在你预计的客户数最多的区域中可用，以便他们可以选择最接近的区域以获得最佳性能。 你将需要确保你的订阅可以在你选择的每个区域中部署所需的所有资源。
- **实例**–选择 "类型" （"热" 或 "冷"）和 "可用实例数"，将乘以提供的可用区域数。

    **热**–此类型的实例部署并等待每个所选区域的访问权限。 客户可以即时访问测试驱动器的*热*实例，而无需等待部署。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议至少具有一个*热*实例，因为大多数客户不希望等待完全部署，因此，如果没有可用的*热*实例，则会导致客户使用中出现下拉。

    **冷**–此类型的实例表示每个区域可能部署的实例总数。 冷实例需要整个测试驱动器资源管理器模板在客户请求测试驱动器时进行部署，因此，*冷*实例的加载速度慢于*热*实例。 缺点是，你只需支付测试驱动器的持续时间，就*不*会像使用*热*实例那样始终在 Azure 订阅上运行它。

- **测试驱动器 Azure 资源管理器模板**–上传包含 Azure 资源管理器模板的 .zip。 若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- "**测试驱动器持续时间**（必需）" –输入测试驱动器将保持活动状态的小时数。 此时间段结束后，testdDrive 会自动终止。 此持续时间可能只需整小时设置（例如，"2" 小时有效;"1.5" 不是）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 的技术配置测试驱动器

Microsoft 可以通过使用这种类型的测试驱动器来托管和维护服务预配和部署，消除设置测试驱动器的复杂性。 无论测试驱动器是面向业务中心、客户接洽还是操作受众，此类型的托管测试驱动器的配置都是相同的。

- **最大并发测试驱动器**数（必需）–设置一次可以使用您的测试驱动器的最大客户数。 在测试驱动器处于活动状态时，每个并发用户将使用 Dynamics 365 许可证，因此你将需要确保有足够的可用许可证来支持最大限制集。 建议值为3-5。

- "**测试驱动器持续时间**（必需）" –输入测试驱动器将保持活动状态的小时数。 在此之后，该会话将结束，并且不再使用您的一个许可证。 建议使用2-24 小时的值，具体取决于产品/服务的复杂性。 此持续时间可能只需整小时设置（例如，"2" 小时有效;"1.5" 不是）。 如果用户运行时间不足，并且想要再次访问测试驱动器，则用户可以请求新的会话。

- **实例 url** （必需）–客户将从其开始测试驱动器的 url。 通常是运行应用并安装示例数据的 Dynamics 365 实例的 URL （例如`https://testdrive.crm.dynamics.com`）。

- **实例 Web API URL** （必需）-通过登录到 Microsoft 365 帐户并导航到 "**设置** \&" gt; 来检索 Dynamics 365 实例的 Web API url**自定义** \&g t;**开发人员资源** \&g t;**实例 WEB API （服务根 url）**，复制此处找到的 URL （例如`https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名称**（必需）–提供在自定义 Dynamics 365 测试驱动器中定义的安全角色名称。 此安全角色名称将在其测试驱动器（例如，测试驱动器角色）期间分配给用户。

### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用的技术配置测试驱动器

任何自定义产品都应使用这种类型的测试驱动器部署模板，其中包含各种复杂的解决方案体系结构。 有关设置逻辑应用测试驱动器的详细信息，请访问 GitHub 上的[操作](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和[客户参与](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单项选择下拉列表）-当前有26个支持 Azure 的区域可供使用。 逻辑应用的资源将部署在所选的区域中。 如果逻辑应用包含存储在特定区域中的任何自定义资源，请确保在此处选择 "区域"。 实现此目的的最佳方式是在门户中将逻辑应用完全部署在 Azure 订阅上，并在做出此选择之前验证它是否正常工作。

- **最大并发测试驱动器**数（必需）–设置一次可以使用您的测试驱动器的最大客户数。 已部署这些测试驱动器，从而使客户无需等待部署即可立即访问它们。

- "**测试驱动器持续时间**（必需）" –输入测试驱动器将保持活动状态的时间长度（以小时为单位）。 此时间段结束后，测试驱动器自动终止。

- **Azure 资源组名称**（必需）–输入逻辑应用测试驱动器保存到的[azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名称。

- **Azure 逻辑应用名称**（必需）–输入向用户分配测试驱动器的逻辑应用的名称。 此逻辑应用必须保存在上述 Azure 资源组中。

- 取消预**配逻辑应用名称**（必需）–输入完成客户后取消设置该测试驱动器的逻辑应用的名称。 此逻辑应用必须保存在上述 Azure 资源组中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 测试驱动器无需技术配置

希望演示交互 Power BI 视觉对象的产品可以使用嵌入的链接，将自定义的仪表板共享为其测试驱动器，无需进一步的技术配置。 了解有关设置[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)模板应用的详细信息。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

若要允许 Microsoft 代表你部署测试驱动器，请创建并提供单独的唯一 Azure 订阅（Power BI 测试驱动器不需要此订阅）。

- **Azure 订阅 id** （azure 资源管理器和逻辑应用需要）–输入订阅 id 可授予对 azure 帐户服务的访问权限，以便进行资源使用报告和计费。 如果还没有 Azure 订阅，请考虑[创建一个单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)用于测试驱动器。 你可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 "**订阅**" 选项卡来查找你的 Azure 订阅 ID。 选择该选项卡将显示你的订阅 ID （例如 "a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租户 id** （必需）–输入 AZURE ACTIVE DIRECTORY （AD）[租户 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**属性**"，然后查找列出的**目录 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用域名 URL 查找你的组织的租户 ID [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

- **Azure AD 租户名称**（动态365需要）–输入 AZURE ACTIVE DIRECTORY （AD）名称。 若要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，你的租户名称将在你的帐户名称下面列出。

- **Azure AD 应用 id** （必需）–输入 AZURE ACTIVE DIRECTORY （AD）[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**应用注册**"，然后查找列出的**应用程序 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端机密**（必需）–输入 Azure AD 应用程序[客户端机密](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要查找此值，请登录到[Azure 门户](https://portal.azure.com/)。 选择左侧菜单中的 " **Azure Active Directory** " 选项卡，选择 "**应用注册**"，然后选择你的测试驱动器应用。 接下来，选择 "**证书和密码**"，选择 "**新建客户端密码**"，输入描述，选择 "**永不****过期**"，然后选择 "**添加**"。 请确保复制值。 请在复制值之前不要离开页面，否则将无法访问值。）

### <a name="test-drive-marketplace-listings"></a>测试驱动器 marketplace 列表

"**测试驱动器**" 选项卡下的**Marketplace 列表**选项显示了可用的测试驱动器的语言。 目前，只有**英语（美国）** 是可用的位置。 选择语言名称以输入描述测试驱动器体验的信息。

- **说明**（必需）–介绍你的测试驱动器、将演示的内容、要浏览的功能，以及可帮助用户确定是否获取产品/服务的任何相关信息。 最多可在此字段中输入3000个字符的文本。 

- **访问信息**（对于 Azure 资源管理器和逻辑测试驱动器是必需的）–说明客户需要知道哪些内容才能访问和使用此测试驱动器。 逐步完成使用产品/服务的方案，并确切了解客户在整个测试过程中访问功能应知道的情况。 最多可在此字段中输入10000个字符的文本。

- **用户手册**（必需）-深入了解你的测试驱动器体验。 用户手册应确切地说明您希望客户从遇到该测试驱动器中获得的内容，并为他们可能遇到的任何问题提供参考。 文件必须采用 PDF 格式，并在上传后命名为（最多255个字符）。

- **视频**（可选）–将视频上传到外部宿主网站引用此处的链接和缩略图（533 x 324 像素）。 这允许客户查看信息的指导，以帮助他们更好地了解测试驱动器，包括如何成功使用产品/服务的功能，并了解突出显示其优势的方案。
  - **名称**（必需）
  - **Address** （必需;仅限 YouTube 或 Vimeo
  - **缩略图**（图像文件必须采用 PNG 格式，533 x 324 像素）

选择 "**保存草稿**"，然后继续。

## <a name="supplemental-content"></a>补充内容

此页面允许你提供有关产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不会向客户显示，也不会发布到 marketplace。

### <a name="key-usage-scenario"></a>密钥使用方案

上传一个 PDF 文件，其中列出了产品/服务的关键使用方案。 在为 marketplace 批准产品/服务之前，验证团队可能会验证所有方案。

选择 "**保存草稿**"，然后继续。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>将产品/服务提交到预览版

完成产品/服务的所有必需部分后，请选择门户右上角的 "**查看并发布**"。

如果你是首次发布此产品/服务，则可以：

- 请参阅产品/服务的每个部分的完成状态。
    - **未开始**-部分未被触摸并且应完成。
    - **不完整**-部分包含需要修复的错误或需要提供更多信息的信息。 返回到部分并对其进行更新。
    - **完成**-该部分已完成，所有必需的数据都已提供，且没有错误。 产品/服务的所有部分必须处于完整状态，然后才能提交产品/服务。
- 在 "**认证说明**" 部分中，向认证团队提供测试说明，以确保正确测试应用程序，以及任何有助于了解应用程序的补充说明。
- 通过选择 "**提交**" 提交产品/服务进行发布。 我们将向你发送一封电子邮件，告知你该产品/服务的预览版本何时可供你查看和批准。 返回到 "合作伙伴中心"，并选择 "产品 **/** 服务" 以发布到公共（如果是专用受众）。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
