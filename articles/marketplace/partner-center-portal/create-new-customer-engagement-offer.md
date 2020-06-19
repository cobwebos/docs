---
title: 在 Microsoft 商业市场中创建 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务
description: 如何创建新的 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务，以在 Azure 市场、AppSource 中或通过合作伙伴中心的云解决方案提供商 (CSP) 计划列出或销售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e49ed542af140b28e8ebe58e5c8920d0959c9387
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848890"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>创建 Dynamics 365 for Customer Engagement & PowerApps 产品/服务

本主题说明如何创建新的 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务。 Dynamics 365 for Customer Engagement 的所有应用（PowerApps、销售、服务、项目服务和现场服务）都必须通过我们的认证流程并支持试用体验。 认证过程会检查解决方案的标准要求、兼容性和正确的做法。 试用体验允许用户将你的解决方案部署到实时 Dynamics 365 环境中。

开始前，请先[在合作伙伴中心内创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未创建的话）。 确保你的帐户已注册加入商业市场计划。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
3. 在“概述”页上，依次选择“+ 新建产品/服务” > “Dynamics 365 for Customer Engagement 和 PowerApps”。

    ![展示了左侧导航菜单。](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> 在产品/服务发布后，在合作伙伴中心内对它进行的编辑只会在产品/服务重新发布后才显示在店面中。 确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称没有用于市场，与向客户显示的产品/服务名称和其他值不同。
- 在选择“创建”后，就无法更改此名称了。

选择“创建”，以生成产品/服务，并继续操作。

## <a name="offer-setup"></a>产品/服务设置

若要设置产品/服务，请按照以下步骤操作。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>希望潜在客户如何与此产品/服务列表进行交互？

选择要用于此产品/服务的选项。

#### <a name="get-it-now-free"></a>立即获取（免费）

通过提供有效的 URL（以 http 或 https 开头）免费向客户提供产品/服务列表，用户可以通过该 URL 访问你的应用 。  例如，`https://contoso.com/my-app` 。

#### <a name="free-trial-listing"></a>免费试用版（列出）

通过提供有效的 URL（以 http 或 https 开头）使用免费试用版的链接向客户提供产品/服务列表，用户可以通过该 URL 获取试用版 。  例如，`https://contoso.com/trial/my-app` 。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序将通过试用链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户不支持使用此令牌进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

选择“保存草稿”，然后继续操作。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，它为潜在客户提供“在购买前试用”这一选择，从而提高了转化率并开发出优质的潜在顾客。 [详细了解体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。 在本主题后面的[体验版技术配置](#test-drive-technical-configuration)部分中配置体验版环境。

有关其他信息，请参阅[在商业市场中为你的产品/服务提供体验版](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

#### <a name="type-of-test-drive"></a>体验版类型

从下列选项中进行选择：

- **[Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** - 一个部署模板，其中包含了组成解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** - Microsoft 托管并维护 Business Central 企业资源规划系统（财务、运营、供应链、CRM 等）的体验版服务（包括预配和部署）。  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** - Microsoft 托管并维护 Customer Engagement 系统（销售、服务、项目服务、现场服务等）的体验版服务（包括预配和部署）。  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** - Microsoft 托管并维护 Finance and Operations 企业资源规划系统（财务、运营、制造、供应链等）的体验版服务（包括预配和部署）。 
- **[逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** - 包含所有复杂解决方案体系结构的部署模板。 所有自定义产品都应当使用此类型的体验版。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** - 指向某个定制仪表板的嵌入式链接。 希望演示交互式 Power BI 视觉对象的产品都应使用此类型的体验版。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他体验版资源

- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [体验版概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF（确保弹出窗口阻止程序处于关闭状态）

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

选择至少一个且最多三个类别。 这些类别用于将产品/服务放入相应的市场搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>适用的 Dynamics 365 产品

选择此产品/服务适用的所有 Dynamics 365 产品。

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页面上看到此列出的版本。 如果仅由于营销内容/描述内容发生更改而更新版本号，请选中“仅限营销的更改”框。 此选项可让产品/服务绕过认证和预配阶段。

### <a name="terms-and-conditions"></a>条款和条件

在此处提供你自己的法律条款和条件。 还可以提供可在其中找到条款和条件的地址。 客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续。

## <a name="offer-listing"></a>产品/服务列表

此页显示用于列出产品/服务的语言。 当前，“英语(美国)”是唯一可用的选项。

在此处为每种语言/市场定义市场详细信息，例如产品/服务名称、说明和图像。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 只要产品/服务说明使用以下短语开头：“此应用程序仅以[非英语语言]提供”，产品/服务列表内容（例如说明、文档、屏幕截图和使用条款）就不需要是英语。 还可以提供一个有用链接 URL，使用与产品/服务列表内容中所使用的语言不同的语言提供内容。

### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可以更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

### <a name="short-description"></a>简短说明

提供产品/服务的简短说明（最多 100 个字符）。 此说明可在市场搜索结果中使用。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜索关键字

可以选择输入最多三个搜索关键字，以帮助客户在市场中查找你的产品/服务。 为了获得最佳结果，请在说明中也使用这些关键字。

### <a name="products-your-app-works-with"></a>可与应用配合使用的产品

如果你想让客户知道你的应用适用于特定产品，请在此处输入最多三个产品名称。

### <a name="support-urls"></a>支持 URL

可在此部分提供链接，帮助客户详细了解你的产品/服务。

#### <a name="help-link"></a>帮助链接

输入客户可在其中详细了解产品/服务的地址。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的地址。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="contacts"></a>联系人

提供“支持联系人”和“工程联系人”的姓名、电子邮箱和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并且可能会提供给 CSP 合作伙伴。

在“支持联系人”部分中，还必须提供“支持 URL”，CSP 合作伙伴可以在该 URL 中找到对产品/服务的支持 。

### <a name="supporting-documents"></a>支持性文档

在此处提供至少一个（最多三个）相关的 PDF 格式的营销文档，如白皮书、小册子、清单或演示文稿。

### <a name="marketplace-images"></a>市场映像

提供产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。

>[!NOTE]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>应用商店徽标

提供以下三个像素大小的产品/服务徽标：
- **小**（必需；48 x 48）
- **大**（必需；216 x 216）
- **宽**（可选；255 x 115）

#### <a name="hero"></a>英雄

特大图像是可选的。 如果提供一个，则其大小必须为 815 x 290 像素。

#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要一个屏幕截图，最多可添加五个屏幕截图。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可以选择添加最多四个演示产品/服务的视频。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，请输入视频的名称、URL 和视频的缩略图（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

[市场产品/服务列表最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择“保存草稿”，然后继续操作。

## <a name="availability"></a>可用性

可使用此页定义在何处以及如何提供产品/服务。

### <a name="markets"></a>市场

指定应在哪些市场提供你的产品/服务。 为此，请选择“编辑市场”，这会显示“市场选择”弹出窗口 。

默认情况下未选择任何市场，但你必须至少选择一个市场才能发布产品/服务。 选择“全选”可使你的产品/服务在每个可能的市场中提供，也可选择要添加的特定市场。

此处的选择仅适用于新的购置；如果某人在某市场中已经有了你的应用，而你后来删除了该市场，则在该市场中已经有该产品/服务的人可以继续使用，但该市场中的新客户都将无法获取你的产品/服务。

> [!IMPORTANT]
> 你需要负责满足任何本地法律要求，即使这些要求未在此处或合作伙伴中心列出。 即使你选择所有市场，本地法律和限制或其他因素也可能会阻止在某些国家和地区列出某些产品/服务。

### <a name="preview-audience"></a>预览版受众

在将产品/服务发布到更广泛的市场产品/服务前，需要先将其提供给有限的预览版受众。 在此处输入隐藏密钥（任何只使用小写字母和/或数字的字符串）。 预览版受众的成员可以将此隐藏密钥用作令牌，以查看市场中产品/服务的预览版。

然后，当你准备好提供产品/服务并删除预览限制时，需要删除该隐藏密钥并再次发布。

选择“保存草稿”，然后继续操作。

## <a name="technical-configuration"></a>技术配置

此页定义用于连接到产品/服务的技术详细信息。 如果最终客户选择获取你的产品/服务，我们可以通过此连接为其预配你的产品/服务。

### <a name="base-license-model"></a>基本许可模型

基本许可模型确定如何在 CRM 管理中心将客户分配到应用程序。 如果按实例进行许可，则选择“资源”；如果为每个租户分配一个许可证，则选择“用户” 。

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>需要 S2S 出站和 CRM 安全存储访问

选中此框可配置 CRM 安全存储或服务器间 (S2S) 出站访问。 此功能要求遵循 Dynamics 365 团队在认证阶段专业提出的注意事项。 Microsoft 将与你取得联系，以完成附加的步骤来支持此功能。

### <a name="application-configuration-url"></a>应用程序配置 URL

提供配置网页的 URL，客户可以通过该网页配置你的应用。

### <a name="crm-package"></a>CRM 包

在“包位置的 URL”字段中，输入包含已上传的 CRM 包 .zip 文件的 Azure Blob 存储帐户的 URL。 在该 URL 中包含只读 SAS 密钥，以便 Microsoft 能够选取要验证的包。

> [!IMPORTANT]
> 为避免发布受阻，请确保 Blob 存储 URL 中的到期日期尚未到期。 可通过访问策略来修改该日期。 我们建议“到期时间”至少是未来一个月。

选中标记为“我的包文件中有多个 CRM 包”的框（如果适用）。 如果选中该框，请确保将所有包都添加到 .zip 文件中。

有关如何生成包和更新其结构的详细信息，请参阅[步骤 3：为应用创建 AppSource 包](https://docs.microsoft.com/powerapps/developer/common-data-service/create-package-app-appsource)。

### <a name="crm-package-availability"></a>CRM 包的可用性

在此部分中，选择“+ 添加区域”以指定客户可以使用 CRM 包的地理区域。 部署到以下主权区域需要特殊的权限，并在认证过程中进行验证：[德国](https://docs.microsoft.com/azure/germany/)、[美国政府云](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)和 TIP。

默认情况下，你在上面输入的“应用程序配置 URL”将用于每个区域。 如果需要，可以为一个或多个特定区域输入单独的应用程序配置 URL。 

选择“保存草稿”，然后继续操作。

## <a name="test-drive-technical-configuration"></a>体验版技术配置

可使用此页设置演示（“体验版”），让客户可在购买之前试用你的产品/服务。 在[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)一文中了解详细信息。

若要启用体验版，请在[产品/服务设置](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请清除此复选框。

在[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)一文中了解详细信息。

可以使用以下类型的体验版，每种都有其自己的技术配置要求：

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives)（无需进行技术配置）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器体验版的技术配置

一个部署模板，其中包含了组成解决方案的所有 Azure 资源。 适合此方案的产品仅使用 Azure 资源。 详细了解如何设置 [Azure 资源管理器体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **区域**（必需）- 当前存在 26 个 Azure 支持的区域，可在这些区域提供你的体验版。 通常情况下，你希望在预计客户数最多的区域中提供你的体验版，以便他们可以选择最接近的区域以获得最佳性能。 需确保允许订阅在所选的各个区域中部署所需的所有资源。
- **实例** - 选择类型（热或冷）和可用实例的数量，将其乘以提供产品/服务的区域数量。

    **热** - 部署此类型的实例并等待每个所选区域的访问。 客户无需等待部署，就能立即访问体验版的热实例。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议拥有至少一个热实例，因为多数客户不想等待完整部署，因此，如果没有热实例可用，则会导致客户使用量下降 。

    **冷** - 此类型的实例表示每个区域可能部署的实例总数。 冷实例要求在客户请求体验版时部署整个体验版资源管理器模板，因此，冷实例的加载速度比热实例要慢得多 。 弊端在于你只需为体验版的持续时间付费，它并不像热实例一样始终在 Azure 订阅上运行 。

- **体验版 Azure 资源管理器模板** - 上传包含 Azure 资源管理器模板的 .zip。 若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的小时数。 此时间段结束后，体验版会自动终止。 此持续时间只能设置为整时（例如，“2”小时有效；“1.5”无效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 体验版的技术配置

Microsoft 通过使用此类型的体验版来托管和维护服务预配和部署，可简化体验版的设置。 无论体验版面向的是 Business Central、Customer Engagement 还是 Operations 受众，此类型的托管体验版的配置都相同。

- **最大并发体验版数**（必需）- 设置可同时使用体验版的最大客户数。 当体验版处于活动状态时，每个并发用户都将使用一个 Dynamics 365 许可证，因此你需要确保有足够的空闲许可证来支持所设置的最大限制。 建议值为 3-5。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的小时数。 超出此时间后，会话将结束且不再使用你的许可证。 建议根据产品/服务的复杂度将值设为 2-24 小时。 此持续时间只能设置为整时（例如，“2”小时有效；“1.5”无效）。 如果用户已耗尽时间，但想再次访问体验版，该用户可请求新会话。

- **实例 URL**（必需）- 客户将在其中开始使用体验版的 URL。 通常，这是正在运行你的应用且安装了示例数据的 Dynamics 365 实例的 URL（例如 `https://testdrive.crm.dynamics.com`）。

- **实例 Web API URL**（必需）- 通过登录 Microsoft 365 帐户并导航到“设置”\&gt;“自定义”\&gt;“开发人员资源”\&gt;“实例 Web API URL (服务根 URL)”来检索 Dynamics 365 实例的 Web API URL，然后复制在此处找到的 URL（例如，`https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名称**（必需）- 提供在自定义 Dynamics 365 体验版中定义的安全角色名称。 当用户使用体验版时，将向用户分配此安全角色名称（例如，test-drive-role）。

### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用体验版的技术配置

所有自定义产品都应使用此类型的体验版部署模板，该模板包含各种复杂的解决方案体系结构。 有关设置逻辑应用体验版的详细信息，请访问 GitHub 上的 [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 和 [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单选下拉列表）- 当前存在 26 个 Azure 支持的区域，可在这些区域提供你的体验版。 逻辑应用的资源将部署在所选区域中。 如果逻辑应用在特定区域存储了任何自定义资源，请务必在此处选择该区域。 最佳方式是在做出此选择之前，先在门户中将逻辑应用完全部署到 Azure 订阅本地，并验证它是否正常工作。

- **最大并发体验版数**（必需）- 设置可同时使用体验版的最大客户数。 这些体验版已部署完毕，客户无需等待部署便可立即访问它们。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的时间长度，以小时为单位。 此时间段结束后，体验版会自动终止。

- **Azure 资源组名称**（必需）- 输入保存逻辑应用体验版的 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名称。

- **Azure 逻辑应用名称**（必需）- 输入将体验版分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

- **取消预配逻辑应用名称**（必需）- 输入在客户完成体验后取消预配体验版的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 体验版无需进行技术配置

希望演示交互式 Power BI 视觉对象的产品可以使用嵌入式链接来共享定制仪表板作为其体验版，无需进行进一步的技术配置。 详细了解如何设置 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 模板应用。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

若要允许 Microsoft 代表你部署体验版，请创建并提供单独的唯一 Azure 订阅（Power BI 体验版不需要）。

- **Azure 订阅 ID**（Azure 资源管理器和逻辑应用需要）- 输入订阅 ID 以授予对 Azure 帐户服务的访问权限，以便进行资源使用情况报告和计费。 建议考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用于体验版（如果尚未创建）。 登录 [Azure 门户](https://portal.azure.com/)并导航到左侧菜单的“订阅”选项卡，即可找到 Azure 订阅 ID。 选择该选项卡将显示订阅 ID （例如“a83645ac-1234-5ab6-6789-1h234g764ghty”）。

- **Azure AD 租户 ID**（必需）- 输入 Azure Active Directory (AD) [租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要找到此 ID，请登录 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择“Active Directory”选项卡，选择“属性”，然后查找列出的“目录 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。 你还可以在以下位置使用域名 URL 查找组织的租户 ID - [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

- **Azure AD 租户名称**（Dynamic 365 需要）- 输入 Azure Active Directory (AD) 名称。 若要找到此名称，请登录 [Azure 门户](https://portal.azure.com/)，租户名称将在右上角的帐户名称下列出。

- **Azure AD 应用 ID**（必需）- 输入 Azure Active Directory (AD) [应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要找到此 ID，请登录 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择“Active Directory”选项卡，选择“应用注册”，然后查找列出的“应用程序 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端密码**（必需）- 输入 Azure AD 应用程序[客户端密码](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要找到此值，请登录 [Azure 门户](https://portal.azure.com/)。 在左侧菜单中选择“Azure Active Directory”选项卡，选择“应用注册”，然后选择体验版应用。 接下来，选择“证书和密码”，选择“新建客户端密码”，输入说明，在“过期”下选择“从不”，然后选择“添加”。 请务必复制该值。 复制该值之前请不要退出页面，否则你将无法访问该值。

### <a name="test-drive-marketplace-listings"></a>体验版市场列表

“体验版”选项卡下的“市场列表”选项显示了体验版可用的语言。 当前，“英语(美国)”是唯一可用的语言。 选择语言名称以输入描述体验版体验的信息。

- **说明**（必需）- 介绍体验版、要演示的内容、用户要体验的目标、要浏览的功能，以及帮助用户确定是否要获取产品/服务的任何相关信息。 最多可在此字段中输入 3,000 个字符的文本。 

- 访问信息（Azure 资源管理器和逻辑体验版需要）– 说明客户需要了解哪些内容才能访问和使用此体验版。 演练使用产品/服务的方案，以及客户在整个体验版中访问功能时应了解的确切内容。 最多可在此字段中输入 10,000 个字符的文本。

- 用户手册（必需）– 体验版体验的深入演练。 用户手册应确切地涵盖希望客户通过体验体验版而获得的好处，并用作他们可能遇到的任何问题的参考。 该文件必须采用 PDF 格式，并在上传之后命名（最多 255 个字符）。

- **视频**（可选）- 将视频上传到外部托管站点，并在此处引用链接和缩略图（533 x 324 像素）。 这允许客户查看信息演练，帮助他们更好地了解体验版，包括如何成功使用产品/服务的功能并了解突出其优势的方案。
  - **名称**（必需）
  - **地址**（必需；仅限 YouTube 或 Vimeo）
  - **缩略图**（图像文件必须采用 PNG 格式，并且为 533 x 324 像素）

选择“保存草稿”，然后继续操作。

## <a name="supplemental-content"></a>补充内容

可使用此页提供有关产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不显示给客户，也不在市场上发布。

### <a name="key-usage-scenario"></a>主要使用方案

上传一个 PDF 文件，其中列出产品/服务的主要使用方案。 在我们批准你的产品/服务进入市场之前，我们的验证团队可能会验证所有方案。

选择“保存草稿”，然后继续操作。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交产品/服务进行预览

完成产品/服务的所有必需部分后，请选择门户右上角的“查看和发布”。

如果这是第一次发布此产品/服务，则可以：

- 查看产品/服务各个部分的完成状态。
    - **未开始** - 尚未涉及该部分，应完成。
    - **未完成** - 本部分包含需要修复的错误或需要提供详细信息。 返回相应部分并进行更新。
    - **完成** - 该部分已完成，已提供所有必需的数据，并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 在“认证说明”部分，除了有助于理解应用的任何补充说明之外，还可以向认证团队提供测试说明，以确保应用得到正确测试。
- 通过选择“提交”来提交要发布的产品/服务。 我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。 返回合作伙伴中心并为产品/服务选择“上线”，以发布给公众（或者，如果是专用产品/服务，则发布给专用受众）。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
