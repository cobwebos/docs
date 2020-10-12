---
title: 创建 Dynamics 365 Business Central 产品/服务 - Microsoft 商业市场
description: 了解在合作伙伴中心的 Microsoft 商业 marketplace 中创建新的 Dynamics 365 Business Central 产品/服务的步骤和注意事项。 可在 Azure 市场或通过云解决方案提供商 (CSP) 计划列出或销售你的产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: bc34d2044c3a91fe18e900b21d589dde855754d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91774572"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Dynamics 365 Business Central 产品/服务

本文介绍如何创建新的 Dynamics 365 Business Central 产品/服务。 [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) 是一个企业资源规划 (ERP) 系统，可处理财务、运营、供应链、客户关系管理 (CRM)、项目管理和电子商务等各种业务流程。 高级包还支持经典部署模型和制造。 Dynamics 365 Business Central 的所有产品/服务都必须完成我们的认证过程。

开始前，请先[在合作伙伴中心内创建商业市场帐户](create-account.md)（如果尚未创建）。 确保你的帐户已注册加入商业市场计划。

## <a name="create-a-new-offer"></a>创建新产品/服务

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述” 。
3. 在“概述”页面上，依次选择“+新建产品/服务” > “Dynamics 365 Business Central” 。

    ![左侧导航菜单图示。](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅会在重新发布产品/服务后出现在联机商店中。 确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 与发布者 ID 组合的产品 ID 的长度必须为40个字符。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格。 例如，如果你的发布者 ID 是 "testpublisherid"，并且你在此处输入了 " **测试/服务-1** "，则 "产品/服务" 网址为 `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` 。
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

免费向客户列出你的产品/服务。

#### <a name="free-trial-listing"></a>免费试用版（列出）

向客户列出你的产品/服务，其中包含免费试用版的链接。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序将通过试用链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户不支持使用此令牌进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

### <a name="test-drive"></a>体验版

此时，Dynamics 365 Business Central 产品/服务不支持测试驱动器。 若要从产品/服务中删除测试驱动器，请清除 " **启用测试驱动器** " 复选框。

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

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页上看到此版本列出。

### <a name="terms-and-conditions"></a>条款和条件

在“条款和条件”字段中，提供你自己的法律条款和条件。 还可以提供能够在其中找到条款和条件的 URL。 客户必须接受这些条款，才能试用产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="offer-listing"></a>产品/服务列表

可在此处定义产品/服务的详细信息，例如名称、描述和图像。

> [!NOTE]
> 仅可用一种语言提供产品/服务列表的详细信息。 如果不要求用英语，则只要产品/服务描述的开头是短语“此应用程序只提供[非英语语言]版本”就可以。 还可提供一个帮助链接 URL，使用与产品/服务列表内容中所用语言以外的语言提供内容。

以下示例显示了产品/服务信息在 Microsoft AppSource 中的显示方式， (所有的价格仅用于举例目的，而不用于反映实际成本) ：

:::image type="content" source="media/example-d365-business-central.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 产品
3. 类别
4. 支持地址 (链接) 
5. 使用条款
6. 隐私策略
7. 产品名称
8. 总结
9. 说明
10. 屏幕截图/视频

### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

### <a name="short-description"></a>简短说明

提供产品/服务的简短说明（最多 100 个字符）。 此说明可在市场搜索结果中使用。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜索关键字

可以视需要选择输入最多三个搜索关键字，以帮助客户在市场中查找你的产品/服务。 为了达到最佳效果，请尝试也在说明中使用这些关键字。

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

提供 **大** 徽标徽标的 PNG 文件。 合作伙伴中心将使用此来创建 **小** 徽标。 稍后，可以选择将此替换为其他映像。

- **大型** (从 216 x 216 到 350 x 350 px，必需) 
- **小写** (48 x 48 px，可选) 

在列表中的不同位置使用这些徽标：

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要 3 个屏幕截图，最多可添加 5 个。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可选择添加最多 5 个视频来演示产品/服务。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，输入视频的名称、URL 和视频的缩略图（1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

[市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)

选择“保存草稿”，然后继续操作。

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

在将产品/服务发布到更广泛的市场产品/服务前，需要先将其提供给有限的预览版受众。 在此处输入隐藏密钥（任何只使用小写字母和/或数字的字符串）。 预览版受众的成员可以将此隐藏密钥用作令牌，以查看市场中产品/服务的预览版。

然后，当你准备好提供产品/服务并删除预览限制时，需要删除该隐藏密钥并再次发布。

选择“保存草稿”，然后继续操作。

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

>[!NOTE]
>不再使用该依赖项包文件。 请改为上载库包文件。

### <a name="url-to-app-installation"></a>应用安装的 URL

如果在上面选择了“Connect”，则请在此处提供应用安装的地址。 对于不需要安装的已连接服务，请提供服务登陆页面或注册页面的地址。

选择“保存草稿”，然后继续。

## <a name="test-drive-technical-configuration"></a>体验版技术配置

可使用此页设置演示（“体验版”），让客户可在购买之前试用你的产品/服务。 有关详细信息 [，请参阅什么是测试驱动器](../what-is-test-drive.md)。

若要启用体验版，请在[“产品/服务设置”](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请取消选中此复选框。

完成设置测试驱动器后，请选择 " **保存草稿** "，然后继续。

## <a name="supplemental-content"></a>补充内容

可使用此页提供有关产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不显示给客户，也不在市场上发布。

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
- 通过选择“提交”来提交要发布的产品/服务。 当产品/服务的预览版本可供评审和审批时，我们将向你发送一封电子邮件。 返回到 "合作伙伴中心"，并选择 "产品 **/** 服务"，将产品/服务发布到公共产品/服务。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
