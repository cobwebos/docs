---
title: 在 Microsoft 商业市场中创建 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务
description: 如何创建新的 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务，以在 Azure 市场、AppSource 中或通过合作伙伴中心的云解决方案提供商 (CSP) 计划列出或销售。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 159cdef6b2c831e8c20d7249334bebac4f2061c5
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606124"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>创建 Dynamics 365 for Customer Engagement & PowerApps 产品/服务

本主题说明如何创建新的 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务。 Dynamics 365 for Customer Engagement 的所有应用（PowerApps、销售、服务、项目服务和现场服务）都必须通过我们的认证流程并支持试用体验。 认证过程会检查解决方案的标准要求、兼容性和正确的做法。 试用体验允许用户将你的解决方案部署到实时 Dynamics 365 环境中。

开始前，请先[在合作伙伴中心内创建商业市场帐户](create-account.md)（如果尚未创建的话）。 确保你的帐户已注册加入商业市场计划。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
3. 在“概述”页上，依次选择“+ 新建产品/服务” > “Dynamics 365 for Customer Engagement 和 PowerApps”。

    ![展示了左侧导航菜单。](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅会在重新发布产品/服务后出现在联机商店中。 确保在进行更改后始终重新发布。

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

向客户列出你的产品/服务，其中包含免费试用版的链接，方法是提供有效的 URL， (始于 `http` 或 `https`) 可以获取试用版。  例如，`https://contoso.com/trial/my-app` 。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序将通过试用链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户不支持使用此令牌进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

选择“保存草稿”，然后继续操作。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，它为潜在客户提供“在购买前试用”这一选择，从而提高了转化率并开发出优质的潜在顾客。 若要了解详细信息，请从 [什么是测试驱动器](../what-is-test-drive.md)开始。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续操作。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别 (可选) 。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 " **不适用**"。

请参阅 [产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>适用的 Dynamics 365 产品

选择此产品/服务适用的所有 Dynamics 365 产品。

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页面上看到此列出的版本。 如果仅由于营销内容/描述内容发生更改而更新版本号，请选中“仅限营销的更改”框。 此选项可让产品/服务绕过认证和预配阶段。

### <a name="terms-and-conditions"></a>条款和条件

在此处提供你自己的法律条款和条件。 还可以提供可在其中找到条款和条件的地址。 客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="offer-listing"></a>产品/服务列表

此页显示用于列出产品/服务的语言。 当前，“英语(美国)”是唯一可用的选项。

在此处为每种语言/市场定义市场详细信息，例如产品/服务名称、说明和图像。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 只要产品/服务说明使用以下短语开头：“此应用程序仅以[非英语语言]提供”，产品/服务列表内容（例如说明、文档、屏幕截图和使用条款）就不需要是英语。 还可以提供一个有用链接 URL，使用与产品/服务列表内容中所使用的语言不同的语言提供内容。

以下示例显示了产品/服务信息在 Microsoft AppSource 中的显示方式， (所有的价格仅用于举例目的，而不用于反映实际成本) ：

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 产品
3. 类别
4. 支持地址 (链接) 
5. 使用条款地址 (链接) 
6. 产品名称
7. 说明
8. 屏幕截图/视频

### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

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

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>应用商店徽标

提供以下三个像素大小的产品/服务徽标：
- **小**（必需；48 x 48）
- **大**（必需；216 x 216）
- **宽**（可选；255 x 115）

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要一个屏幕截图，最多可添加五个屏幕截图。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可以选择添加最多四个演示产品/服务的视频。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，请输入视频的名称、URL 和视频的缩略图（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

[市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)

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

在此部分中，选择“+ 添加区域”以指定客户可以使用 CRM 包的地理区域。 部署到以下主权区域需要特殊的权限，并在认证过程中进行验证：[德国](../../germany/index.yml)、[美国政府云](../../azure-government/documentation-government-welcome.md)和 TIP。

默认情况下，你在上面输入的“应用程序配置 URL”将用于每个区域。 如果需要，可以为一个或多个特定区域输入单独的应用程序配置 URL。 

选择“保存草稿”，然后继续操作。

## <a name="test-drive-technical-configuration"></a>体验版技术配置

可使用此页设置演示（“体验版”），让客户可在购买之前试用你的产品/服务。 有关详细信息 [，请参阅什么是测试驱动器](../what-is-test-drive.md)。

若要启用体验版，请在[“产品/服务设置”](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请取消选中此复选框。

完成设置测试驱动器后，请选择 " **保存草稿** "，然后继续。

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
- 通过选择“提交”来提交要发布的产品/服务。 我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。 返回到合作伙伴中心，并选择 "产品 **/** 服务" 以发布到公共产品/服务。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
