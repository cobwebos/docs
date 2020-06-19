---
title: 创建 Dynamics 365 Business Central 产品/服务 - Microsoft 商业市场
description: 了解在合作伙伴中心的商业市场门户中新建 Dynamics 365 Business Central 产品/服务的步骤和注意事项。 可在 Azure 市场或通过云解决方案提供商 (CSP) 计划列出或销售你的产品/服务。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 04de89624dd0e6857e96327bb408cf8700a1f6a2
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848873"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Dynamics 365 Business Central 产品/服务

本文介绍如何创建新的 Dynamics 365 Business Central 产品/服务。 [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) 是一个企业资源规划 (ERP) 系统，可处理财务、运营、供应链、客户关系管理 (CRM)、项目管理和电子商务等各种业务流程。 高级包还支持经典部署模型和制造。 Dynamics 365 Business Central 的所有产品/服务都必须完成我们的认证过程。

开始前，请先[在合作伙伴中心内创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未创建）。 确保你的帐户已注册加入商业市场计划。

## <a name="create-a-new-offer"></a>创建新产品/服务

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述” 。
3. 在“概述”页面上，依次选择“+新建产品/服务” > “Dynamics 365 Business Central” 。

    ![左侧导航菜单图示。](./media/new-offer-dynamics-365-bc.png)

> [!NOTE]
> 在产品/服务发布后，在合作伙伴中心内对它进行的编辑只会在产品/服务重新发布后才显示在店面中。 确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入 test-offer-1，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称不用于市场，并且与向客户显示的产品/服务名称和其他值不同。
- 选择“创建”后，无法更改产品/服务别名。

选择“创建”以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

若要设置产品/服务，请按照以下步骤操作。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>希望潜在客户如何与此产品/服务列表进行交互？

选择要用于此产品/服务的选项。

#### <a name="get-it-now-free"></a>立即获取（免费）

通过提供有效的 URL（以 http 或 https 开头）免费向客户提供产品/服务列表，用户可以通过 URL 访问你的应用。   例如，`https://contoso.com/my-app` 。

#### <a name="free-trial-listing"></a>免费试用版（列出）

通过提供有效的 URL（以 http 或 https 开头）使用免费试用版的链接向客户提供产品/服务列表，用户可以通过 URL 获取试用版。   例如，`https://contoso.com/trial/my-app` 。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序将通过试用链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户不支持使用此令牌进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，它为潜在客户提供“在购买前试用”这一选择，从而提高了转化率并开发出优质的潜在顾客。 [详细了解体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。 在本主题后面的[体验版技术配置](#test-drive-technical-configuration)部分中配置体验版环境。

有关其他信息，请参阅[在商业市场中为你的产品/服务提供体验版](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

#### <a name="type-of-test-drive"></a>体验版类型

从下列选项中进行选择：

- [Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive) - 一个部署模板，其中包含了组成解决方案的所有 Azure 资源。 适合此方案的产品是仅使用 Azure 资源。
- [Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer) - Microsoft 托管并维护 Business Central 企业资源规划系统（财务、运营、供应链、CRM 等）的体验版服务，包括预配和部署。  
- [Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer) - Microsoft 托管并维护 Customer Engagement 系统（销售、服务、项目服务、现场服务等）的体验版服务（包括预配和部署）。  
- [Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer) - Microsoft 托管并维护 Finance and Operations 企业资源规划系统（财务、运营、制造、供应链等）的体验版服务（包括预配和部署）。 
- [逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive) - 包含所有复杂解决方案体系结构的部署模板。 所有自定义产品都应当使用此类型的体验版。
- [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) - 指向某个定制的仪表板的嵌入式链接。 希望演示交互式 Power BI 视觉对象的产品都应使用此类型的体验版。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他体验版资源

- [体验版技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [体验版营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [体验版概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF（确保弹出窗口阻止程序已关闭）

### <a name="customer-leads"></a>潜在客户

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在客户管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

选择类别（最少 1 个，最多 3 个），用于将产品/服务放入适当的市场搜索区域。 确保在产品/服务说明中强调产品/服务如何支持这些类别。 

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页上看到此列出的版本。

### <a name="terms-and-conditions"></a>条款和条件

在“条款和条件”字段中提供自己的法律条款和条件。 还可以提供可在其中找到条款和条件的 URL。 客户将需要接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续。

## <a name="offer-listing"></a>产品/服务列表

可在此处定义产品/服务的详细信息，例如名称、描述和图像。

> [!NOTE]
> 仅可用一种语言提供产品/服务列表的详细信息。 如果不要求用英语，则只要产品/服务描述的开头是短语“此应用程序只提供[非英语语言]版本”就可以。 还可提供一个帮助链接 URL，使用与产品/服务列表内容中所用语言以外的语言提供内容。

### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

### <a name="short-description"></a>简短说明

提供产品/服务的简短说明（最多 100 个字符）。 此说明可在市场搜索结果中使用。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜索关键字

可选择输入搜索关键字（不超过 3 个），帮助客户在市场中查找你的产品/服务。 为了获得最佳结果，请尝试在说明中也使用这些关键字。

### <a name="products-your-app-works-with"></a>可与应用配合使用的产品

如果你想让客户知道你的应用适用于特定产品，请在此处输入产品名称（最多输入 3 个）。

### <a name="helpprivacy-urls"></a>帮助/隐私 URL

可在本部分提供链接，帮助客户详细了解你的产品/服务。

#### <a name="help-link"></a>帮助链接

输入客户可在其中详细了解你的产品/服务的 URL。 帮助链接不能与支持 URL 相同（解释如下） 。

#### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 URL。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="contact-information"></a>联系信息

在本部分中，必须提供支持联系人和工程联系人的姓名、电子邮箱和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并可能会提供给 CSP 合作伙伴。

在“支持联系人”部分，还必须提供“支持 URL”，CSP 合作伙伴可在该 URL 中找到对产品/服务的支持 。 支持 URL不能与帮助链接相同（解释如下）。

### <a name="supporting-documents"></a>支持性文档

在此处提供至少一个（最多三个）相关的营销文档，如白皮书、小册子、清单或演示文稿。 这些文档必须采用 .pdf 格式。

### <a name="marketplace-images"></a>市场映像

提供适合你的产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 按两种大小上传产品/服务徽标：

* 小（48 x 48 像素）
* 大（216 x 216 像素）

>[!NOTE]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要 3 个屏幕截图，最多可添加 5 个。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可选择添加最多 5 个视频来演示产品/服务。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，请输入视频的名称、URL 和视频的缩略图（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

[市场产品/服务列表最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择“保存草稿”，然后继续。

## <a name="availability"></a>可用性

可在本页上确定在何处、如何提供你的产品/服务。

### <a name="markets"></a>市场

可在此部分指定应在哪些市场提供你的产品/服务。 为此，请选择“编辑市场”，这会显示“市场选择”弹出窗口。 

要发布产品/服务，请选择至少一个市场。 选择“全选”可使你的产品/服务在每个可能的市场中提供，也可选择要添加的特定市场。

此处的选择仅适用于新的购置；如果某人在某市场中已经有了你的应用，而你后来删除了该市场，则在该市场中已经有该产品/服务的人可以继续使用，但该市场中的新客户都将无法获取你的产品/服务。

> [!IMPORTANT]
> 你需要负责满足任何本地法律要求，即使这些要求未在此处或合作伙伴中心列出。

请记住，即使你选择所有市场，本地法律和限制或其他因素也可能会阻止在某些国家和地区列出某些产品/服务。

### <a name="preview-audience"></a>预览版受众

在将产品/服务发布到更广泛的市场产品/服务前，需要先将其提供给有限的预览版受众。 在此处输入隐藏密钥（任何只使用小写字母和/或数字的字符串）。 预览版受众的成员可以将此隐藏密钥用作令牌，以查看市场中产品/服务的预览。

然后，当你准备好提供产品/服务并删除预览限制时，需要删除该隐藏密钥并再次发布。

选择“保存草稿”，然后继续。

## <a name="technical-configuration"></a>技术配置

此页定义用于连接到产品/服务的技术详细信息。 如果最终客户选择获取你的产品/服务，则此连接使我们可以为其预配你的产品/服务。

### <a name="package-type"></a>包类型

选择适用于你的产品/服务的选项：

* **附加产品** - 附加产品应用可扩展 Dynamics 365 Business Central 的体验和现有功能。 有关详细信息，请参阅[附加产品应用](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)。
* **Connect** - 如果必须在 Dynamics 365 Business Central 与第三方解决方案或服务之间建立点到点连接，则可使用 Connect 应用。 有关详细信息，请参阅 [Connect 应用](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)。

### <a name="file-upload"></a>文件上传

如果在上面选择了“附加产品”，则将上传产品/服务的包文件，并上传它在其上有依赖项的任何扩展的包文件。

#### <a name="extensions-package-file"></a>扩展包文件

上传产品/服务的扩展包文件 (.app)。

#### <a name="library-package-file"></a>库包文件

如果你的产品/服务必须与将不在市场中发布的其他扩展一起安装，则需要此文件。 如果是，请在此处上传它的 .app 文件。

#### <a name="dependency-package-file"></a>依赖项包文件

如果你的产品/服务必须与已在市场中发布的其他扩展一起安装，则需要此文件。 如果是，请在此处上传它的 `.app` 或 `.zip` 文件。

### <a name="url-to-app-installation"></a>应用安装的 URL

如果在上面选择了“Connect”，则请在此处提供应用安装的地址。 对于不需要安装的已连接服务，请提供服务登陆页面或注册页面的地址。

选择“保存草稿”，然后继续。

## <a name="test-drive-technical-configuration"></a>体验版技术配置

在此页中可以设置演示（“体验版”），让客户可在购买之前试用你的产品/服务。 在[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)一文中了解详细信息。

若要启用体验版，请在[产品/服务设置](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请清除此复选框。

可以使用以下类型的体验版，每种都有其自己的技术配置要求。

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives)（不需要技术配置）

其他体验版资源：

- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于关闭状态）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器体验版的技术配置

一个部署模板，其中包含了组成解决方案的所有 Azure 资源。 适合此方案的产品是仅使用 Azure 资源。 详细了解如何设置 [Azure 资源管理器体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **区域**（必需）- 当前存在 26 个 Azure 支持的区域，可在其中提供你的体验版。 通常情况下，你希望在预计客户数最多的区域中提供你的体验版，以便他们可以选择最接近的区域以获得最佳性能。 需确保允许订阅在所选的各个区域中部署所有所需的资源。

- **实例** - 选择类型（热或冷）和可用实例的数量，将其乘以提供产品/服务的区域数量。

    **热** - 部署此类型的实例并等待每个所选区域的访问权限。 客户无需等待部署，就能立即访问体验版的热实例。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议拥有至少一个热实例，因为多数客户不想等待完整部署，因此，如果没有热实例可用，则会导致客户使用量下降。 

    **冷** - 此类型的实例表示每个区域可能部署的实例总数。 冷实例要求在客户请求体验版时部署整个体验版资源管理器模板，因此，冷实例的加载速度比热实例要慢得多。  弊端在于你只需为体验版的持续时间付费，因为具有热实例，它并不始终在 Azure 订阅上运行。 

- **体验版 Azure 资源管理器模板** - 上传包含 Azure 资源管理器模板的 .zip。  若要详细了解如何创建 Azure 资源管理器模板，请参阅快速入门文章[使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- **体验版持续时间**（必需）- 输入体验版保持活动状态的时间长度，以小时为单位。 此时间段结束后，体验版会自动终止。 仅使用整数（例如，2 小时有效，1.5 小时无效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 体验版技术配置

Microsoft 通过使用此类型的体验版来托管和维护服务预配和部署，可简化体验版的设置。 无论体验版面向的是 Business Central、Customer Engagement 还是 Operations 受众，此类型的托管体验版的配置都相同。

- **最大并发体验版**（必需）- 设置可同时使用体验版的最大用户数。 体验版处于活动状态时，每个并发用户都将使用一个 Dynamics 365 许可证，因此你需要确保有足够的空闲许可证来支持所设置的最大限制。 建议的值为 3-5。

- **体验版持续时间**（必需）- 通过定义小时数，输入体验版保持活动状态的时间长度。 超出此时间后，会话将结束且不再使用你的许可证。 建议根据产品/服务的复杂度将值设为 2-24 小时。 此持续时间仅可设置为整小时数（例如设为 2 小时，而 1.5 小时无效）。  如果用户已耗尽时间，但想再次访问体验版，该用户可请求新会话。

- **实例 URL**（必需）- 客户将在其中开始体验版的 URL。 通常，这是正在运行你的应用且安装了示例数据的 Dynamics 365 实例的 URL（例如 `https://testdrive.crm.dynamics.com`）。

- **实例 Web API URL**（必需）- 可登录 Microsoft 365 帐户，再导航到“设置”\&gt;“自定义”\&gt;“开发人员资源”\&gt; “实例 Web API (服务根 URL)”来检索 Dynamics 365 实例的 Web API URL，然后复制在此处找到的 URL（例如 `https://testdrive.crm.dynamics.com/api/data/v9.0`）   。

- **角色名称**（必需）- 提供你在自定义 Dynamics 365 体验版中定义的安全角色名称，该名称将在用户体验版分配给用户（例如，名称为 test-drive-role）。

### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用体验版技术配置

所有自定义产品都应使用此类型的体验版部署模板，该模板包含各种复杂的解决方案体系结构。 要详细了解如何设置逻辑应用体验版，请访问 GitHub 上的 [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)和 [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单选下拉列表）- 当前存在 26 个 Azure 支持的区域，可在其中提供你的体验版。 逻辑应用的资源将在你选择的区域中部署。 如果逻辑应用在特定区域中存储了任何自定义资源，请务必在此处选择该区域。 最佳方式是在作出此选择之前，先在门户中将逻辑应用完全部署到 Azure 订阅本地，并验证它是否正常工作。

- **最大并发体验版**（必需）- 设置可同时使用体验版的最大用户数。 这些体验版已部署，客户无需等待部署就可立即访问它们。

- **Azure体验版持续时间**（必需）- 输入体验版保持活动状态的时间长度，以小时为单位。 此时间段结束后，体验版会自动终止。

- **Azure 资源组名称**（必需）- 输入保存逻辑应用体验版的 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名称。

- **Azure 逻辑应用名称**（必需）- 输入将体验版分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

- **取消预配逻辑应用名称**（必需）- 输入在客户完成后取消预配体验版的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 体验版不需要技术配置

希望演示交互式 Power BI 视觉对象的产品可使用嵌入的链接来共享自定义构建的仪表板作为其体验版，无需进一步的技术配置。 详细了解如何设置 [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 模板应用。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

若要代表你部署体验版，请创建和提供单独且唯一的 Azure 订阅。 （Power BI 体验版无需此操作）。

- **Azure 订阅 ID**（是 Azure 资源管理器和逻辑应用的必需项）- 输入订阅 ID 以授予对 Azure 帐户服务的访问权限，从而对资源使用情况进行报告和计费。 建议考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用于体验版（如果尚未创建）。 登录 [Azure 门户](https://portal.azure.com/)并导航到左侧菜单的“订阅”选项卡，即可找到 Azure 订阅 ID。 选择该选项卡将显示订阅 ID（例如“a83645ac-1234-5ab6-6789-1h234g764ghty”）。

- **Azure AD 租户 ID**（必需）- 输入 Azure Active Directory (AD) [租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要找到此 ID，请登录 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择“Active Directory”选项卡，选择**“属性”，然后查找列出的“目录 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。 还可使用域名地址在以下位置查找组织的租户 ID：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

- **Azure AD 租户名称**（是 Dynamic 365 的必需项）- 输入 Azure Active Directory (AD) 名称。 若要找到此名称，请登录 [Azure 门户](https://portal.azure.com/)，租户名称将在右上角的帐户名称下列出。

- **Azure AD 应用 ID**（必需）- 输入 Azure Active Directory (AD) [应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要找到此 ID，请登录 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择“Active Directory”选项卡，选择“应用注册”，然后查找列出的“应用程序 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端密码**（必需）- 输入 Azure AD 应用程序[客户端密码](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要找到此值，请登录 [Azure 门户](https://portal.azure.com/)。 在左侧菜单中选择“Azure Active Directory”选项卡，选择“应用注册”，然后选择体验版应用 。 接下来，选择“证书和密码”，选择“新建客户端密码”，输入说明，在“过期”下选择“从不”，然后选择“添加”。 请务必复制值。 （在执行此操作之前，请不要退出此页面，否则你将无法访问此值。）

### <a name="test-drive-marketplace-listings"></a>体验版市场列表

可通过“体验版”选项卡下的“市场列表”选项定义体验版体验的详细信息 。

> [!NOTE]
> 仅可用一种语言提供体验版列表的详细信息。 如果不要求用英语，则只要产品/服务描述的开头是短语“此应用程序只提供[非英语语言]版本”就可以。 还可提供一个帮助链接 URL，使用与体验版列表内容中所用语言以外的语言提供内容。

- **说明**（必需）- 介绍体验版、要演示的内容、用户要体验的目标、要浏览的功能，以及帮助用户确定是否要获取产品/服务的任何相关信息。 最多可在此字段中输入 3,000 个字符的文本。 

- **访问信息**（Azure 资源管理器和逻辑体验版需要）- 说明客户需要了解哪些内容才能访问和使用此体验版。 演练使用产品/服务的方案，以及客户在整个体验版中访问功能时应了解的确切内容。 最多可在此字段中输入 10,000 个字符的文本。

- **用户手册**（必需）- 体验版体验的深入演练。 用户手册应确切地涵盖希望客户通过体验体验版而获得的好处，并用作他们可能遇到的任何问题的参考。 该文件必须采用 PDF 格式，并在上传之后命名（最多 255 个字符）。

- **视频**（可选）- 可将视频上传到 YouTube 或 Vimeo，并在此处使用链接和缩略图（533 x 324 像素）进行引用，以便客户可查看信息演练，来帮助他们更好地了解体验版，包括如何成功使用产品/服务的功能并了解突出其优势的方案。
  - **名称**（必需）
  - **地址**（仅限 YouTube 或 Vimeo；必需）
  - **缩略图**（图像文件必须采用 PNG 格式，并且为 533 x 324 像素）

选择“保存草稿”，然后继续。

## <a name="supplemental-content"></a>补充内容

本页面要求你提供有关你的产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不显示给客户，也不在市场上发布。

### <a name="target-release"></a>目标版本

指出你的解决方案面向哪个版本的 Microsoft Dynamics Business Central：“当前版本”、“下一主要版本”或“下一次要版本”  。 我们可通过此信息适当地测试你的解决方案。

### <a name="supported-editions"></a>支持的版本

如果你的产品/服务需要 Microsoft Dynamics 365 Business Central 高级版，请仅选择“Premium”。 否则，请同时选择“Essentials”和“Premium” 。

### <a name="key-usage-scenario"></a>主要使用方案

必须上传一个 PDF 文件，其中列出文档（.pdf 格式）中产品/服务的主要使用方案。 在我们批准你的产品/服务加入市场之前，此处列出的所有方案可能都已经过我们的验证团队的验证。

### <a name="app-tests-automation"></a>应用测试自动化

如果你的产品/服务是附加产品应用，则必须上传一个应用测试自动化文件 (.app)。 此文件不适用于 Connect 应用。

### <a name="test-accounts"></a>用户帐户

如果我们的认证团队需要测试帐户才能恰当审阅你的产品/服务，请随附测试帐户信息上传一个 .pdf、.doc 或 .docx 文件。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交产品/服务进行预览

完成产品/服务的所有必需部分后，请选择门户右上角的“发布”。 系统会将你重定向到“审阅和发布”页面。 

如果是第一次发布此产品/服务，则可以：

- 查看产品/服务各个部分的完成状态。
    - 未开始 - 表示该部分尚未涉及，需要完成。
    - 未完成 - 表示该部分包含需要修复的错误或需要提供更多信息。 返回到相应部分并进行更新。
    - 完成 - 表示该部分已完成，所有必需数据都已提供并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 在“认证说明”部分，除了有助于理解应用的任何补充说明之外，还可以向认证团队提供测试说明，以确保应用得到正确测试。
- 通过选择“提交”来提交要发布的产品/服务。 当产品/服务的预览版本可供评审和审批时，我们将向你发送一封电子邮件。 返回合作伙伴中心并为产品/服务选择“上线”，以将产品/服务发布给公众（或者，如果是专用产品/服务，则发布给专用受众）。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
