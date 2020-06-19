---
title: 在商业市场中创建 Dynamics 365 for Operations 产品/服务
description: 如何使用 Microsoft 合作伙伴中心内的商业市场门户来新建 Dynamics 365 for Operations 产品/服务，以供在 Azure 市场、AppSource 中或通过云解决方案提供商 (CSP) 计划列出或销售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 13e05a8771be162ebe37cc79fc93cfa404183d1d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846829"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 产品/服务

本主题介绍了如何新建 Dynamics 365 for Operations 产品/服务。 [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) 是一项企业资源计划 (ERP) 服务，支持高级财务、运营、制造和供应链管理。 Dynamics 365 for Operations 的所有产品/服务都必须通过我们的认证流程。

开始前，请先[在合作伙伴中心内创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未创建的话）。 请确保你的帐户已注册加入商业市场计划。

>[!NOTE]
> 在产品/服务发布后，在合作伙伴中心内对它进行的编辑只会在产品/服务重新发布后才在系统和店面中进行更新。 请务必在进行更改后提交产品/服务以供发布。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
3. 在“概述”页上，依次选择“+ 新建产品/服务” > “Dynamics 365 for Operations”。

    ![展示了左侧导航菜单。](./media/new-offer-dynamics-365-ops.png)

> [!NOTE]
> 在产品/服务发布后，在合作伙伴中心内对它进行的编辑只会在产品/服务重新发布后才显示在店面中。 请务必在进行更改后始终进行重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你在此处输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称没有用于市场，与向客户显示的产品/服务名称和其他值不同。

选择“创建”，以生成产品/服务，并继续操作。

## <a name="offer-setup"></a>产品/服务设置

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>你希望潜在客户如何与此产品/服务一览进行交互？

选择要用于此产品/服务的选项。

#### <a name="get-it-now-free"></a>立即获取(免费)

通过提供可供用户访问应用的有效 URL（以 http 或 https 开头），免费向客户提供产品/服务一览。  例如： `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>免费试用版(一览)

通过提供可供用户获取试用版的有效 URL（以 http 或 https 开头），使用免费试用版链接向客户提供产品/服务一览。 例如，`https://contoso.com/trial/my-app` 。 产品/服务一览免费试用版是由你的服务创建、管理和配置，不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用通过试用版链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 不支持使用此令牌对 Microsoft 帐户进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 客户会被要求授权共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 若要详细了解如何配置 CRM，请参阅[潜在客户](#customer-leads)。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，让他们可以选择“先试后买”，从而提高转化率，并开发出高质量的潜在客户。 [详细了解体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请取消选中此复选框。 在本主题后面的[体验版技术配置](#test-drive-technical-configuration)部分中配置体验版环境。

有关其他信息，请参阅[在商业市场中为你的产品/服务提供体验版](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

#### <a name="type-of-test-drive"></a>体验版类型

从下列选项中进行选择：

- [Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive) - 部署模板，其中包含组成解决方案的所有 Azure 资源。 适合此方案的产品只使用 Azure 资源。
- [Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer) - Microsoft 为 Business Central 企业资源计划系统（财务、运营、供应链、CRM 等）托管并维护体验版服务（包括预配和部署）。  
- [Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer) - Microsoft 为 Customer Engagement 系统（销售、服务、项目服务、现场服务等）托管并维护体验版服务（包括预配和部署）。  
- [Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer) - Microsoft 为 Finance and Operations 企业资源计划系统（财务、运营、制造、供应链等）托管并维护体验版服务（包括预配和部署）。
- [逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive) - 部署模板，其中包含所有复杂的解决方案体系结构。 所有自定义产品都应使用这种类型的体验版。
- [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) - 指向定制仪表板的嵌入链接。 要演示交互式 Power BI 视觉对象的产品应使用这种类型的体验版。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他体验版资源

- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于禁用状态）

### <a name="customer-leads"></a>潜在客户

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在客户管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续操作。

## <a name="properties"></a>属性

通过此页，可以定义用于在市场上对产品/服务进行分组的类别和行业、应用版本，以及支持产品/服务的法律协定。

### <a name="category"></a>类别

最少选择一个类别，最多选择三个类别。 这些类别用于将产品/服务放入相应的市场搜索区域中。 请务必在产品/服务说明中说明产品/服务是如何支持这些类别的。

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页上看到此版本列出。

### <a name="terms-and-conditions"></a>条款和条件

在“条款和条件”字段中，提供你自己的法律条款和条件。 还可以提供能够在其中找到条款和条件的 URL。 客户必须接受这些条款，才能试用产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="offer-listing"></a>产品/服务一览

此页显示产品/服务一览的语言。 目前，“英语(美国)”是唯一的可用选项。

需要为每种语言/市场定义市场详细信息（产品/服务名称、说明、图像等）。 请选择语言/市场名称，以提供此类信息。

> [!NOTE]
> 只要产品/服务说明的开头短语是“此应用只提供[非英语语言]版本”，那么产品/服务一览内容（如说明、文档、屏幕截图、使用条款等）就不要求是英语的。 提供实用链接 URL 来提供产品/服务一览内容中使用的语言以外的语言内容，也是可以接受的。

### <a name="name"></a>名称

在此处输入的名称作为产品/服务一览的标题向客户显示。 此字段预填充了你在创建产品/服务时为“产品/服务别名”输入的文本，但你可以更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称的长度不得超过 50 个字符，且不得包含任何表情符号。

### <a name="short-description"></a>简短说明

提供对产品/服务的简短说明（最多 100 个字符）。 此说明可用于市场搜索结果。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜索关键字

可以视需要选择输入最多三个搜索关键字，以帮助客户在市场中查找你的产品/服务。 为了达到最佳效果，请尝试也在说明中使用这些关键字。

### <a name="products-your-app-works-with"></a>可与应用配合使用的产品

若要让客户知道你的应用可以与特定产品配合使用，请在此处输入最多三个产品名称。

### <a name="support-urls"></a>支持 URL

使用此部分，可以提供链接来帮助客户详细了解你的产品/服务。

#### <a name="help-link"></a>帮助链接

输入客户可以在其中详细了解你的产品/服务的 URL。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的 URL。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="contacts"></a>联系人

在此部分中，提供“支持联系人”和“工程联系人”的姓名、电子邮件地址和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并可能会提供给 CSP 合作伙伴。

在“支持联系人”部分中的“支持 URL”内，提供 CSP 合作伙伴可以在其中找到你产品/服务的支持的 URL。

### <a name="supporting-documents"></a>支持性文档

在此处提供至少一个（最多三个）相关的市场营销文档，如白皮书、小册子、清单或演示文稿。 这些文档必须采用 PDF 格式。

### <a name="marketplace-images"></a>市场映像

在此部分中，可以提供在向客户展示你的产品/服务时使用的徽标和图像。 所有图像都必须采用 .png 格式。

>[!Note]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>应用商店徽标

提供以下两个像素大小的产品/服务徽标：

- 小 (48 x 48)
- 大 (216 x 216)

#### <a name="hero"></a>英雄

特大图像是可选的。 如果你提供一个，它的大小必须为 815 x 290 像素。

#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要一个屏幕截图，最多可添加五个屏幕截图。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可以视需要选择添加最多四个视频来演示产品/服务。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，请输入视频名称、URL 和视频缩略图（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他市场一览资源

[市场产品/服务一览最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择“保存草稿”，然后继续操作。

## <a name="availability"></a>可用性

通过此页，可以设置在哪里以及如何提供产品/服务。

### <a name="markets"></a>市场

使用此部分，可以指定应在其中提供产品/服务的市场。 为此，请选择“编辑市场”，这会显示“市场选择”弹出窗口。

默认情况下，没有选中任何市场。 请选中至少一个市场来发布产品/服务。 单击“全选”以在每个可能的市场中都提供产品/服务，或选中要添加的特定市场。 完成后，选择“保存”。

你在此处所做的选择仅适用于新的获取；如果某人已在特定市场中获取了你的应用，而你后来又删除了此市场，那么已经在此市场中获取了你的产品/服务的人可以继续使用它，但此市场中的任何新客户都无法获取你的产品/服务。

> [!IMPORTANT]
> 你有责任满足所有本地法律要求，即使这些要求并没有在此处或合作伙伴中心内列出。

请注意，即使你选中了所有市场，当地的法律和限制或其他因素也可能会阻止特定产品/服务在一些国家和地区列出。

### <a name="preview-audience"></a>预览受众

必须先将产品/服务提供给有限的预览受众，然后才能向更广泛的市场受众发布它。 在此处输入隐藏密钥（任何只使用小写字母和/或数字的字符串）。 预览受众的成员可以将此隐藏密钥用作令牌，以在市场中查看产品/服务的预览。

然后，当你准备好提供产品/服务并删除预览限制时，需要删除并重新发布隐藏密钥。

选择“保存草稿”，然后继续操作。

## <a name="technical-configuration"></a>技术配置

此页定义了用于连接到产品/服务的技术详细信息。 使用此连接，可以为选择获取你的产品/服务的最终客户预配产品/服务。

### <a name="solution-identifier"></a>解决方案标识符

提供解决方案的解决方案标识符 (GUID)。

若要查找解决方案标识符，请执行以下操作：
1. 在 Microsoft Dynamics Lifecycle Services (LCS) 中，选择“解决方案管理”。
2. 选择解决方案，然后在“包概述”中查找“解决方案标识符”。 如果标识符是空白，请选择“编辑”并重新发布包，然后重试。

### <a name="release-version"></a>发行版本

选择此解决方案使用的 Dynamics 365 for Finance and Operations 的版本。

选择“保存草稿”，然后继续操作。

## <a name="test-drive-technical-configuration"></a>体验版技术配置

通过此页，可以设置演示（“体验版”），这样客户能够在购买前试用你的产品/服务。 若要了解详细信息，请参阅[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)一文。

若要启用体验版，请在[“产品/服务设置”](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请取消选中此复选框。

可以使用以下类型的体验版，每种类型都有自己的技术配置要求。

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives)（不需要技术配置）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器体验版的技术配置

部署模板，其中包含组成解决方案的所有 Azure 资源。 适合此方案的产品只使用 Azure 资源。 详细了解如何设置 [Azure 资源管理器体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- 区域（必需）- 目前有 26 个 Azure 支持的区域，可以在其中提供体验版。 通常，不妨在预计客户数最多的区域中提供体验版，这样他们可以选择最接近的区域来获取最佳性能。 你需要确保允许订阅在所选的各个区域中部署所需的全部资源。

- 实例 - 选择类型（热或冷）和可用实例数（将乘以你在其中提供产品/服务的区域数）。

    热 - 这种类型的实例在每个所选区域中部署并等待访问权限。 客户可以立即访问体验版的热实例，而不必等待部署。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议至少有一个热实例，因为大多数客户不想等待完整部署，所以如果没有热实例，则会导致客户使用量下降。

    冷 - 这种类型的实例表示每个区域中可能部署的实例总数。 如果客户请求获取的是体验版，冷实例需要部署整个体验版资源管理器模板，所以冷实例的加载速度比热实例要慢得多。 需要做的权衡是，只需要为体验版的持续时间付费，它并不像热实例一样始终在 Azure 订阅上运行。

- 体验版 Azure 资源管理器模板 - 上传包含 Azure 资源管理器模板的 .zip 文件。  若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- 体验版持续时间（必需）- 输入体验版保持活动状态的时间长度（以小时为单位）。 此时间段结束后，体验版会自动终止。 此持续时间只能设置为整小时数（例如，“2”小时有效，而“1.5”小时则无效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 体验版技术配置

通过使用这种类型的体验版来托管和维护服务预配和部署，Microsoft 可以简化体验版的设置。 无论体验版面向的是 Business Central 受众、Customer Engagement 受众还是 Operations 受众，这种类型的托管体验版的配置都相同。

- 最大并发体验版（必需）- 设置一次可以使用体验版的最大客户数。 当体验版处于活动状态时，每个并发用户都将使用一个 Dynamics 365 许可证，因此你需要确保有足够的许可证来支持所设置的上限。 建议的值为 3-5。

- 体验版持续时间（必需）- 通过定义小时数，输入体验版保持活动状态的时间长度。 超出此小时数后，会话将结束，且不再使用你的许可证。 建议将值设为 2-24 小时，具体视产品/服务的复杂度而定。 此持续时间只能设置为整小时数（例如，“2”小时有效，而“1.5”小时则无效）。  如果用户已耗尽时间，但想再次访问体验版，可以请求新会话。

- 实例 URL（必需）- 客户将在其中开始体验版的 URL。 通常，这是运行已安装示例数据的应用的 Dynamics 365 实例的 URL（例如 `https://testdrive.crm.dynamics.com`）。

- 实例 Web API URL（必需）- 登录 Microsoft 365 帐户，并依次转到“设置”\&gt;“自定义”\&gt;“开发人员资源”\&gt;“实例 Web API (服务根 URL)”来检索 Dynamics 365 实例的 Web API URL，然后复制在此处找到的 URL（例如 `https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- 角色名称（必需）- 提供在自定义 Dynamics 365 体验版中定义的安全角色名称。 这会在体验版期间分配给用户（例如，test-drive-role）。

### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用体验版技术配置

所有自定义产品都应使用这种类型的体验版部署模板，其中包含各种复杂的解决方案体系结构。 若要详细了解如何设置逻辑应用体验版，请访问 GitHub 上的 [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和 [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- 区域（必需，单选下拉列表）- 目前有 26 个 Azure 支持的区域，可以在其中提供体验版。 逻辑应用的资源将在你选择的区域中部署。 如果逻辑应用在特定区域中存储了任何自定义资源，请务必在此处选中相应区域。 若要确保你拥有区域可用的自定义资源，最佳方式是在做出此选择之前，先在门户中的 Azure 订阅上本地完全部署逻辑应用，并验证它能否正常运行。

- 最大并发体验版（必需）- 设置一次可以使用体验版的最大客户数。 这些体验版已部署，这样客户就可以立即访问它们，而不必等待部署。

- 体验版持续时间（必需）- 输入体验版保持活动状态的时间长度（以小时为单位）。 此时间段结束后，体验版会自动终止。

- Azure 资源组名称（必需）- 输入保存逻辑应用体验版的 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)的名称。

- Azure 逻辑应用名称（必需）- 输入将体验版分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

- 取消预配逻辑应用名称（必需）- 输入在客户完成后取消预配体验版的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 体验版不需要技术配置

要演示交互式 Power BI 视觉对象的产品可使用嵌入链接来共享定制仪表板作为其体验版，无需进一步的技术配置。 详细了解如何设置 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 模板应用。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

为了代表你部署体验版，请创建和提供单独的唯一 Azure 订阅。 （Power BI 体验版无需此操作）。

- Azure 订阅 ID（Azure 资源管理器和逻辑应用需要）- 输入订阅 ID 以向 Azure 帐户服务授予访问权限，以便进行资源使用情况报告和计费。 建议考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用于体验版（如果尚未创建的话）。 若要查找 Azure 订阅 ID，请登录 [Azure 门户](https://portal.azure.com/)，并转到左侧菜单的“订阅”选项卡。 选择此选项卡会显示订阅 ID（例如“a83645ac-1234-5ab6-6789-1h234g764ghty”）。

- Azure AD 租户 ID（必需）- 输入 Azure Active Directory (AD) [租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录 [Azure 门户](https://portal.azure.com/)，依次选择左侧菜单中的“Active Directory”选项卡和“属性”，然后查找列出的“目录 ID”编号（例如，50c464d3-4930-494c-963c-1e951d15360e）。 还可以使用 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 的域名地址来查找组织的租户 ID。

- Azure AD 租户名称（Dynamic 365 需要）- 输入 Azure Active Directory (AD) 名称。 若要查找此名称，请登录 [Azure 门户](https://portal.azure.com/)，租户名称在右上角的帐户名下列出。

- Azure AD 应用 ID（必需）- 输入 Azure Active Directory (AD) [应用 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录 [Azure 门户](https://portal.azure.com/)，依次选择左侧菜单中的“Active Directory”选项卡和“应用注册”，然后查找列出的“应用 ID”编号（例如，“50c464d3-4930-494c-963c-1e951d15360e”）。

- Azure AD 应用客户端密码（必需）- 输入 Azure AD 应用[客户端密码](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要查找此值，请登录 [Azure 门户](https://portal.azure.com/)。 依次选择左侧菜单中的“Azure Active Directory”选项卡、“应用注册”和体验版应用。 接下来，依次选择“证书和密码”和“新建客户端密码”，输入说明，在“过期”下选择“从不”，然后选择“添加”。 请务必复制此值。 （在记下此值之前，请不要离开页面，否则将无法访问此值。）

选择“保存草稿”，然后继续操作。

### <a name="test-drive-marketplace-listings"></a>体验版市场一览

“体验版”选项卡下的“市场一览”选项显示体验版的可用语言。 目前，“英语(美国)”是唯一的可用位置。 请选择语言名称，以输入描述体验版体验的信息。

- 说明（必需）- 介绍体验版、要演示的内容、用户要体验的目标、要浏览的功能，以及帮助用户确定是否要获取产品/服务的任何相关信息。 最多可在此字段中输入 3,000 个字符的文本。

- 访问信息（Azure 资源管理器和逻辑应用体验版需要）- 说明客户需要了解哪些内容才能访问和使用此体验版。 演练产品/服务的使用方案，以及客户在整个体验版中访问功能时应了解的确切内容。 最多可在此字段中输入 10,000 个字符的文本。

- 用户手册（必需）- 体验版体验的深入演练。 用户手册应确切地涵盖你希望客户通过体验体验版而获得的好处，并用作他们可能遇到的任何问题的参考。 此文件必须采用 PDF 格式，并在上传之后命名（最多 255 个字符）。

- 视频（可选）- 可以将视频上传到 YouTube 或 Vimeo，并在此处使用链接和缩略图（533 x 324 像素）进行引用，这样客户就能查看信息演练，以帮助他们更好地了解体验版，包括如何成功使用产品/服务的功能，并了解突出其优势的方案。
  - 名称（必需）
  - URL（必需；仅限 YouTube 或 Vimeo）
  - 缩略图（图像必须采用 PNG 格式，且大小必须为 533 x 324 像素）

选择“保存草稿”，然后继续操作。

## <a name="supplemental-content"></a>补充内容

通过此页，可以提供产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不显示给客户，也不在市场上发布。

### <a name="validation-assets"></a>验证资产

在此部分中，上传[自定义分析报告 (CAR)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report)。 此报告是根据一组预定义的最佳做法规则，通过分析你的自定义和扩展模型而生成的。

此文件必须采用 .xls 或 .xlsx 格式。 如果有多个报告，则可以上传包含所有报告的 .zip 文件。

### <a name="does-solution-include-localizations"></a>解决方案是否包含本地化内容？

如果解决方案允许使用本地标准和策略（例如，如果它适应不同国家/地区所需的不同工资规则），请选择“是”。 否则请选择“否”。

### <a name="does-solution-enable-translations"></a>解决方案是否支持翻译？

如果解决方案中的文本可以翻译为其他语言，请回答“是”。 否则请选择“否”。

选择“保存草稿”，然后继续操作。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交产品/服务以供预览

完成产品/服务的所有必需部分后，请选择门户右上角的“审阅和发布”。

如果你是第一次发布此产品/服务，则可以：

- 查看产品/服务各个部分的完成状态。
    - 未开始 - 尚未涉及此部分，应完成。
    - 未完成 - 此部分包含需要修复的错误或需要提供详细信息。 返回到一个或多个相应部分并进行更新。
    - 完成 - 此部分已完成，已提供所有必需的数据，并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 在“认证说明”部分中，除了有助于理解应用的任何补充说明之外，还可以向认证团队提供测试说明，以确保应用得到正确测试。
- 通过选择“提交”来提交要发布的产品/服务。 我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。 返回到合作伙伴中心，并为产品/服务选择“上线”，以将产品/服务发布给公众（或者，如果是专用产品/服务，则发布给专用受众）。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
