---
title: 在微软商业市场创建新的 Dynamics 365 商业中心产品
description: 如何创建新的 Dynamics 365 商业中心产品/服务，用于在 Azure 应用商店、Microsoft AppSource 中列出或销售，或者使用 Microsoft 合作伙伴中心的商业市场门户通过云解决方案提供商 （CSP） 计划进行上市或销售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 10fa16767061506eccf212c182e738246dc9fe7b
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867230"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>创建新的 Dynamics 365 商业中心产品/服务

本文介绍如何创建新的 Dynamics 365 业务中心产品/ [Microsoft Dynamics 365 商业中心](https://dynamics.microsoft.com/business-central)是一个企业资源规划 （ERP） 系统，可处理各种业务流程，包括财务、运营、供应链、CRM 以及项目管理和电子商务。 高级软件包还支持经典部署模型和制造。 Dynamics 365 商务中心的所有优惠都必须经过我们的认证流程。

要开始创建 Dynamics 365 商业中心产品/服务，请确保首先[创建合作伙伴中心帐户](./create-account.md)并打开[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，并选择 **"概述"** 页。

![合作伙伴中心上的商业市场仪表板](./media/new-offer-overview.png)

>[!Note]
> 产品/服务发布后，只有在重新发布后，才会在系统和商店前面更新对合作伙伴中心中提供的优惠进行编辑。 请确保在进行更改后提交要发布的要约。

## <a name="create-a-new-offer"></a>创建新套餐

选择 **+ 新优惠**按钮，然后选择**Dynamics 365 商业中心**菜单项。 将显示"**新建产品/服务**"对话框。

### <a name="offer-id-and-alias"></a>优惠 ID 和别名

- **优惠 ID**：帐户中每个产品/服务的唯一标识符。 此 ID 将在市场产品/服务的 URL 地址和 Azure 资源管理器模板（如果适用）中向客户可见。 优惠 ID 必须是小写字母数字字符（包括连字符和下划线，但没有空格），限制为 50 个字符，并且在选择 "**创建**"后无法更改。  例如，如果您在此处输入*测试报价 1，* 则产品/服务 URL 将为`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。

- **优惠别名**：用于指合作伙伴中心内产品/服务的名称。 此名称不会在市场中使用，并且与向客户显示的要约名称和其他值不同。 选择 "**创建**"后，无法更改此值。

输入**优惠 ID**和**优惠别名**后，选择 **"创建**"。 然后，您将能够处理产品/服务的所有不同部分。

## <a name="offer-setup"></a>优惠设置

**"产品/服务设置"** 页要求提供以下信息。 请务必在完成这些字段后选择 **"保存**"。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>您希望潜在客户如何与此上市产品进行交互？

选择要用于此优惠的选项。

#### <a name="get-it-now-free"></a>立即获取（免费）

通过提供有效的 URL（从*http*或*https*开头），客户可以访问你的应用，从而免费向客户列出您的优惠。  例如： `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>免费试用（列表）

通过提供有效的 URL（从*http*或*https*开头），以便客户获得试用版，从而向客户列出您的优惠。"  例如：`https://contoso.com/trial/my-app`。 免费试用版产品列表的优惠由您的服务创建、管理和配置，并且没有由 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序将通过试用链接收到的令牌只能用于通过 Azure 活动目录 （Azure AD） 获取用户信息，以自动在应用中创建帐户。 使用此令牌进行身份验证不支持 Microsoft 帐户。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 （CRM） 系统收集客户联系信息。 将请求客户共享其信息的权限。 这些客户详细信息以及找到产品/服务的产品/服务的产品/地区的产品/服务名称、ID 和市场源将发送到您配置的 CRM 系统。 有关配置 CRM 的详细信息，请参阅[连接潜在顾客管理](#connect-lead-management)。 

### <a name="test-drive"></a>体验版

试驾是向潜在客户展示您的报价的绝佳方式，为他们提供"购买前试用"选项，从而提高转化率并生成高素质潜在客户。 [了解有关测试驱动器的更多详细信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

要启用试驾，请选中 **"启用试驾"** 框。 然后，您需要在[测试驱动器技术配置](#test-drive-technical-configuration)配置中配置演示环境，以便客户在固定时间段内试用您的产品/服务。 

#### <a name="type-of-test-drive"></a>试驾类型

从下列选项中进行选择：

- **[Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**：包含构成解决方案的所有 Azure 资源的部署模板。 适合此方案的产品仅使用 Azure 资源。
- **[面向业务中心的动态 365：Microsoft](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** 托管和维护业务中央企业资源规划系统（财务、运营、供应链、CRM 等）的试驾服务（包括配置和部署）。  
- **[面向客户参与的动态 365：Microsoft](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** 托管和维护客户参与系统的试驾服务（包括配置和部署）（销售、服务、项目服务、现场服务等）。  
- **[操作动态 365：](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** Microsoft 托管和维护财务和运营企业资源规划系统（财务、运营、制造、供应链等）的试驾服务（包括配置和部署）。 
- **[逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**：包含所有复杂解决方案体系结构的部署模板。 任何自定义产品都应使用这种类型的试驾。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**：指向自定义仪表板的嵌入式链接。 想要演示交互式 Power BI 视觉对象的产品应使用这种类型的测试驱动器。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他试驾资源

- [试驾技术最佳实践](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [试驾营销最佳实践](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [试驾概述一个寻呼机](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

在继续下一节之前，请记住**保存**！

## <a name="properties"></a>属性

属性**页面**允许您定义用于在市场对产品/服务进行分组的类别和行业、应用版本和支持产品/服务的法律合同。 完成此页面后选择 **"保存**"。

### <a name="category"></a>类别

选择至少一个且最多三个类别，这些类别将用于将产品/服务放入相应的市场搜索区域。 请务必在产品/服务说明中标注您的产品/服务如何支持这些类别。 

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页面上看到此版本。

### <a name="terms-and-conditions"></a>条款和条件

在条款和条件字段中提供您自己的法律**条款和条件**。 您还可以提供可在其中找到您的条款和条件的 URL。 客户在试用您的优惠之前，必须接受这些条款。

## <a name="offer-listing"></a>优惠列表

"产品/服务"列表页是定义产品/服务的详细信息（如名称、说明、图像等）的位置。

> [!NOTE]
> 您只能提供一种语言的优惠列表详细信息。 它不需要是英文的，只要报价说明以短语开头，"此应用程序仅以[非英语]提供。 提供*帮助链接 URL*以提供产品/服务列表内容中所用语言以外的语言也是可以接受的。

### <a name="name"></a>名称

您在此处输入的名称将作为产品/服务列表的标题显示给客户。 此字段预填充了创建**产品/服务时为"产品/服务"别名**输入的文本，但您可以更改此值。 此名称可以注册商标（您可以包括商标或版权符号）。 名称不能超过 50 个字符，不能包含任何表情符号。

### <a name="short-description"></a>简短说明

提供产品/服务的简短说明（最多 100 个字符），可在市场搜索结果中使用。

### <a name="description"></a>说明

提供更久的报价说明（最多 3，000 个字符）。 此描述将显示在市场列表概述中的客户。 包括您的产品/服务的价值主张、主要优势、类别和/或行业协会、应用内购买机会以及任何必需的披露。 

编写说明的一些提示：  

- 在前几句描述中清晰描述产品/服务的价值主张。 在价值主张中包括以下项目：
  - 该产品的说明
  - 从产品中受益的用户类型
  - 产品解决的客户需求或痛苦
- 请牢记，前几句话可能在搜索引擎结果中显示。  
- 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
- 尽可能多使用行业特定用语或基于优势的措辞。
- 请考虑使用 HTML 标记来格式化您的描述，使其更具吸引力。

为了使您的报价描述更具吸引力，请使用丰富的文本编辑器来设置您的说明格式。

![使用富文本编辑器](./media/text-editor2.png)

使用以下说明使用富文本编辑器：

- 要更改内容的格式，请突出显示要设置格式的文本并选择文本样式，如下所示：

     ![使用富文本编辑器更改文本格式](./media/text-editor3.png)

- 要向文本添加项目符号或编号列表，请使用以下选项：

     ![使用富文本编辑器添加列表](./media/text-editor4.png)

- 要向文本添加或删除缩进，请使用以下选项：

     ![使用富文本编辑器缩进](./media/text-editor5.png)

### <a name="search-keywords"></a>搜索关键字

您可以选择输入最多三个搜索关键字，帮助客户在市场上找到您的产品/服务。 为获得最佳效果，请尝试在描述中使用这些关键字。

### <a name="products-your-app-works-with"></a>可与应用配合使用的产品

如果您想让客户知道你的应用适用于特定产品，请在此处输入最多三个产品名称。

### <a name="helpprivacy-urls"></a>帮助/隐私 URL

本部分允许您提供链接，帮助客户更好地了解您的产品/服务。

#### <a name="help-link"></a>帮助链接

输入客户可以详细了解您的优惠的 URL。 **您的帮助链接**不能与您的**支持 URL**相同（如下所述）。

#### <a name="privacy-policy-link"></a>隐私政策链接

输入组织隐私政策的 URL。 您有责任确保您的应用符合隐私法律和法规，并提供有效的隐私政策。

### <a name="contact-information"></a>联系信息

在本节中，您必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并且可能会提供给 CSP 合作伙伴。

在 **"支持"联系人**部分中，还必须提供**支持 URL，** 其中 CSP 合作伙伴可以找到对您的产品/服务的支持。 您的支持 URL 不能与您的**帮助链接**相同。

### <a name="supporting-documents"></a>证明文件

在此处至少提供一份（最多三份）相关营销文档，如白皮书、小册子、检查表或演示文稿。 这些文件必须采用 .pdf 格式。

### <a name="marketplace-images"></a>市场映像

在本节中，您可以提供向客户展示产品/服务时将使用的徽标和图像。 所有图像必须采用 .png 格式。 上传您的优惠徽标有两种尺寸：**小 （48 x 48）** **和大 （216 x 216）**。

>[!Note]
>如果上传文件出现问题，请确保本地网络不会阻止合作伙伴中心使用https://upload.xboxlive.com的服务。

#### <a name="screenshots"></a>屏幕截图

添加显示优惠工作原理的屏幕截图。 至少需要三个屏幕截图，您最多只能添加五个屏幕截图。 所有屏幕截图必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

您可以选择添加最多五个演示您的优惠的视频。 这些视频应托管在 YouTube 和/或 Vimeo 上。 对于每个视频，输入视频的名称、URL 和视频的缩略图图像（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

- [市场产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>可用性

"**可用性"** 页面为您提供了有关产品/服务在何处以及如何提供选项。

### <a name="markets"></a>市场

本部分允许您指定报价可用的市场。 为此，请选择 **"编辑市场**"，该市场将显示**市场选择**弹出窗口。

默认情况下，不会选择任何市场，但您必须选择至少一个市场才能发布您的报价。 单击 **"选择全部"** 以使您的产品/服务在每个可能的市场都可用，或选择要添加的特定市场。 完成后，选择"**保存**"。

此处的选择仅适用于新的收购;如果有人已经拥有你的应用程序在某一市场，你后来删除该市场，谁已经在这个市场的报价的人可以继续使用它，但在这个市场没有新的客户将能够得到您的报价。

> [!IMPORTANT]
> 您有责任满足任何当地法律要求，即使这些要求未在此处或合作伙伴中心列出。

请记住，即使您选择所有市场、当地法律和限制或其他因素，也可能阻止某些优惠在某些国家和地区上市。

### <a name="preview-audience"></a>预览受众

在将优惠实时发布到更广泛的市场产品/服务之前，您首先需要将其提供给有限的**预览版受众**。 在此处输入**Hide 键**（仅使用小写字母和/或数字的任何字符串）。 预览受众的成员可以使用此隐藏键作为令牌，在市场中查看优惠的预览。

然后，当您准备好使产品/服务可用并删除预览限制时，您需要删除**隐藏键**并再次发布。

## <a name="technical-configuration"></a>技术配置

"**技术配置**"页定义用于连接到产品/服务的技术详细信息。 此连接使我们能够为最终客户提供您的报价，如果他们选择购买。

### <a name="package-type"></a>包类型

选择适用于您的产品/服务的选项：

- **添加**： 附加应用扩展了 Dynamics 365 业务中心的经验和现有功能。 有关详细信息，请参阅[附加应用](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)。
- **连接**：Connect 应用可用于必须在 Dynamics 365 业务中心和第三方解决方案或服务之间建立点对点连接的情况。 有关详细信息，请参阅[连接](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)。

### <a name="file-upload"></a>文件上传

如果选择上面**的"添加"，** 您将上传产品/服务包文件，以及其具有依赖项的任何扩展名的包文件。

#### <a name="extensions-package-file"></a>扩展程序包文件

上传产品/服务扩展包文件 （.app） 文件。

#### <a name="library-package-file"></a>库包文件

如果必须安装您的产品/服务以及另一个不会发布到市场的扩展，则需要这样做。 如果是这样，请在此处上传其 .app 文件。

#### <a name="dependency-package-file"></a>依赖项包文件

如果必须安装您的产品/服务以及已发布到市场的另一个扩展，则需要。 如果是这样，请在此处上传`.app`其`.zip`或文件。

### <a name="url-to-app-installation"></a>应用安装 URL

如果选择上面的 **"连接**"，请在此处提供应用安装的 URL。 对于不需要安装的已连接服务，请为服务登录页或注册页提供 URL。

## <a name="test-drive-technical-configuration"></a>试驾技术配置

如果在["产品/服务设置"](#offer-setup)页中选择 **"启用试驾**"，则需要在此处提供详细信息，以便客户体验您的产品/服务中的试驾。

通过 **"测试驱动器**"页面，您可以设置演示（或"试驾"），使客户能够在承诺购买之前试用您的产品/服务。 在文章"[什么是试驾"中了解更多信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) 如果您不想再为产品/服务提供试驾，请返回**["产品/服务设置](#offer-setup)**"页面并取消选中**启用试驾**。

提供以下类型的测试驱动器，每种驱动器都有自己的技术配置要求。

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [电源 BI（](#technical-configuration-not-required-for-power-bi-test-drives)不需要技术配置）

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 资源管理器测试驱动器的技术配置

包含构成解决方案的所有 Azure 资源的部署模板。 适合此方案的产品仅使用 Azure 资源。 详细了解如何设置 Azure[资源管理器测试驱动器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **区域**（必需）：目前有 26 个 Azure 支持的区域，可以在其中提供您的试驾。 通常，您需要在预期客户数量最多的区域提供试驾，以便他们选择最接近的区域以获得最佳性能。 您需要确保允许订阅部署所选择的每个区域所需的所有资源。

- **实例**：选择类型（热或冷）和可用实例数，这些实例将乘以产品/服务可用的区域数。

**热**：这种类型的实例已部署，并等待每个选定区域的访问。 客户可以立即访问测试驱动器*的热*实例，而无需等待部署。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议至少使用一个*热*实例，因为大多数客户不希望等待完全部署，如果没有*热*实例，则会导致客户使用量下降。

**冷**：这种类型的实例表示每个区域可能部署的实例总数。 冷实例要求在整个测试驱动器资源管理器模板在客户请求试驾时进行部署，因此*冷*实例的加载速度比*热*实例慢得多。 权衡是，您只需要在试驾期间付费，它*并不总是*像*使用热*实例那样在 Azure 订阅上运行。

- **试用 Azure 资源管理器模板**：上载包含 Azure 资源管理器模板的 .zip。  在快速入门文章"[使用 Azure 门户创建和部署 Azure 资源管理器模板"](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)中了解有关创建 Azure 资源管理器模板详细信息。

- **试驾持续时间**（必需）：输入试驾将保持活动状态的时间长度（以小时数计）。 此时间段结束后，体验版会自动终止。 此持续时间只能由整个小时数设置（例如，"2"小时，"1.5"无效）。

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 试驾的技术配置

Microsoft 可以通过托管和维护使用此类测试驱动器的服务预配和部署来消除设置测试驱动器的复杂性。 无论试驾是面向业务中心、客户参与还是运营受众，此类托管试驾的配置都是相同的。

- **最大并发测试驱动器**（必需）：设置一次可以使用试驾的最大客户数。 每个并发用户将在测试驱动器处于活动状态时使用 Dynamics 365 许可证，因此您需要确保有足够的许可证来支持最大限制集。 建议的值为 3-5。

- **试驾持续时间**（必需）：通过定义小时数输入试驾将保持活动状态的时间长度。 在此多小时后，会话将结束，不再使用您的许可证之一。 根据产品/服务的复杂性，我们建议值为 2-24 小时。 此持续时间只能由整个小时数设置（例如，"2"小时，"1.5"无效）。  如果用户时间已用完，并且希望再次访问试驾，用户可以请求新的会话。

- **实例 URL（** 必需）：客户将开始其试驾的 URL。 通常，使用安装示例数据（例如，）`https://testdrive.crm.dynamics.com`运行应用的 Dynamics 365 实例的 URL。

- **实例 Web API URL（** 必需）：通过登录到 Microsoft 365 帐户并导航到 **"设置**\&"gt，检索 Dynamics 365 实例的 Web API URL;**自定义**\&gt;**开发人员资源**\&gt;**实例 Web API（服务根 URL），** 复制此处找到的 URL（`https://testdrive.crm.dynamics.com/api/data/v9.0`例如。

- **角色名称**（必需）：提供您在自定义 Dynamics 365 试驾中定义的安全角色名称，这些角色将在用户试驾期间分配给用户（例如，测试驱动器角色）。

### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用试驾的技术配置

任何自定义产品都应使用包含各种复杂解决方案体系结构的这种类型的测试驱动器部署模板。 有关设置逻辑应用测试驱动器的详细信息，请访问 GitHub 上的[操作](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)[和客户参与度](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **区域**（必需，单选下拉列表）：目前有 26 个 Azure 支持的区域，可以在其中提供您的试驾。 逻辑应用的资源将部署在您选择的区域中。 如果逻辑应用在特定区域中存储了任何自定义资源，请确保在此处选择该区域。 最佳方法是在门户中的 Azure 订阅中本地完全部署逻辑应用，并在进行此选择之前验证其是否正常工作。

- **最大并发测试驱动器**（必需）：设置一次可以使用试驾的最大客户数。 这些测试驱动器已部署，使客户能够立即访问它们，而无需等待部署。

- **试驾持续时间**（必需）：输入试驾将保持活动状态的时间长度（以小时数计）。 此时间段结束后，试驾将自动终止。

- **Azure 资源组名称**（必需）：输入逻辑应用测试驱动器保存的[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名称。

- **Azure 逻辑应用名称**（必需）：输入将测试驱动器分配给用户的逻辑应用的名称。 此逻辑应用必须保存在上面的 Azure 资源组中。

- **取消预配逻辑应用名称**（必需）：输入逻辑应用的名称，该应用在客户完成后取消配置试驾。 此逻辑应用必须保存在上面的 Azure 资源组中。

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 测试驱动器不需要技术配置

想要演示交互式 Power BI 视觉对象的产品可以使用嵌入式链接共享自定义仪表板作为试驾，无需进一步的技术配置。 详细了解如何设置[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)模板应用。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

为了代表您部署测试驱动器，请创建并提供单独且唯一的 Azure 订阅。 （电源 BI 测试驱动器不需要）。

- **Azure 订阅 ID（Azure**资源管理器和逻辑应用需要）：输入订阅 ID 以授予对 Azure 帐户服务的访问权限，以便进行资源使用情况报告和计费。 如果尚未创建，我们建议您考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用于测试驱动器。 您可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 **"订阅"** 选项卡来查找 Azure 订阅 ID。 选择该选项卡将显示您的订阅 ID（例如，"a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租户 ID（** 必需）：输入 Azure 活动目录 （AD）[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧菜单中的"活动目录"选项卡，选择 "属性"，然后查找列出的**目录 ID**号（例如，50c464d3-4930-494c-963c-1e951d15360e）。 您还可以使用域名 URL 在 ：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)查找组织的租户 ID。

- **Azure AD 租户名称**（动态 365 需要）：输入 Azure 活动目录 （AD） 名称。 要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，租户名称将列在您的帐户名称下。

- **Azure AD 应用 ID（** 必需）：输入 Azure 活动目录 （AD）[应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧菜单中的"活动目录"选项卡，选择**应用注册**，然后查找列出的应用程序**ID**号（例如，50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端机密**（必需）：输入 Azure AD 应用程序[客户端密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 要查找此值，请登录到 Azure[门户](https://portal.azure.com/)。 选择左侧菜单中的**Azure 活动目录**选项卡，选择**应用注册**，然后选择试驾应用。 接下来，选择**证书和机密**，选择 **"新建客户端机密**"，输入说明，选择 **"从不在过期**"下，然后选择"**添加**"。 **Never** 请确保向下复制该值。 （执行此操作之前不要离开页面，否则您将无法访问该值。

在继续下一节之前，请记住**保存**！

### <a name="test-drive-marketplace-listings"></a>试驾市场列表

"**测试驱动器**"选项卡下的 **"应用商店列表**"选项是定义试驾体验详细信息的位置。

> [!NOTE]
> 您只能以一种语言提供试驾列表详细信息。 它不需要是英文的，只要报价说明以短语开头，"此应用程序仅以[非英语]提供。 也可以提供*帮助链接 URL*以测试驱动器列表内容中使用的语言以外的语言提供内容。


- **说明**（必需）：描述您的试驾、演示的内容、供用户试验的目标、要浏览的功能以及任何相关信息，以帮助用户确定是否获取您的产品/服务。 此字段中最多可输入 3，000 个字符的文本。 

- **访问信息**（Azure 资源管理器和逻辑测试驱动器所需的信息）：解释客户需要了解哪些内容才能访问和使用此测试驱动器。 演练使用产品/服务的方案，以及客户在整个试驾中访问功能的准确情况。 此字段中最多可输入 10，000 个字符的文本。

- **用户手册**（必需）：对试驾体验的深入演练。 《用户手册》应准确介绍您希望客户从体验试驾中获得什么，并作为他们可能存在的任何问题的参考。 文件必须采用 PDF 格式，并在上载后命名（最多 255 个字符）。

- **视频：添加视频**（可选）：视频可以上传到 YouTube 或 Vimeo，并在此处引用链接和缩略图图像 （533 x 324 像素），以便客户可以查看信息的演练，以帮助他们更好地了解试驾，包括如何成功使用您的产品/服务的功能，并了解突出其优势的方案。
  - **名称**（必需）
  - **网址（仅限 YouTube 或 Vimeo）（** 必填）
  - **缩略图 （533 x 324 px）**： 图像文件必须采用 PNG 格式。

## <a name="supplemental-content"></a>补充内容

此页面允许您提供有关您的优惠的其他信息，以帮助我们验证您的产品/服务。 此信息不会向客户显示或发布到市场。

### <a name="target-release"></a>目标版本

指示解决方案目标为"**当前"、****下一个主要**版本或**下一个次要**版本的 Microsoft 动态业务中心版本。 此信息使我们能够适当地测试您的解决方案。

### <a name="supported-editions"></a>支持的版本

如果您的产品/服务需要 Microsoft 动态 365 商业中心的高级版，请选择"**高级"。** 否则，选择 **"基本"** 和"**高级**"。

### <a name="key-usage-scenario"></a>关键使用方案

您必须上传一个`.pdf`文件，其中列出文档（.pdf 格式）中列出的产品/服务的关键使用方案。 在我们批准您的市场报价之前，我们的验证团队可能会验证此处列出的所有方案。

### <a name="app-tests-automation"></a>应用测试自动化

您可以选择在此处上载**应用测试自动化**文件 （.app）。

### <a name="test-accounts"></a>测试帐户

如果需要测试帐户，我们的认证团队才能正确审核您的产品/服务，请上传包含**测试帐户**信息的 .pdf、.doc 或 .docx 文件。

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

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
