---
title: 在商业市场中创建 Dynamics 365 for Operations 产品/服务
description: 如何使用 Microsoft 合作伙伴中心内的商业市场门户来新建 Dynamics 365 for Operations 产品/服务，以供在 Azure 市场、AppSource 中或通过云解决方案提供商 (CSP) 计划列出或销售。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 621d657f527e5307ed3a0b583e4c9171005d4f8c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121824"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 产品/服务

本主题介绍了如何新建 Dynamics 365 for Operations 产品/服务。 [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations) 是一项企业资源计划 (ERP) 服务，支持高级财务、运营、制造和供应链管理。 Dynamics 365 for Operations 的所有产品/服务都必须通过我们的认证流程。

开始前，请先[在合作伙伴中心内创建商业市场帐户](create-account.md)（如果尚未创建的话）。 请确保你的帐户已注册加入商业市场计划。

>[!NOTE]
> 在产品/服务发布后，在合作伙伴中心内对它进行的编辑只会在产品/服务重新发布后才在系统和店面中进行更新。 请务必在进行更改后提交产品/服务以供发布。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
3. 在“概述”页上，依次选择“+ 新建产品/服务” > “Dynamics 365 for Operations”。

    ![展示了左侧导航菜单。](./media/new-offer-dynamics-365-operations.png)

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

向客户列出你的产品/服务，并提供有效的 URL （从或开始， `http` `https` 用户可以在其中获取试用版）。 例如，`https://contoso.com/trial/my-app` 。 产品/服务一览免费试用版是由你的服务创建、管理和配置，不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用通过试用版链接收到的令牌只能用于通过 Azure Active Directory (Azure AD) 获取用户信息，从而在应用中自动创建帐户。 不支持使用此令牌对 Microsoft 帐户进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 客户会被要求授权共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 若要详细了解如何配置 CRM，请参阅[潜在客户](#customer-leads)。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，让他们可以选择“先试后买”，从而提高转化率，并开发出高质量的潜在客户。 若要了解详细信息，请从[什么是测试驱动器](../what-is-test-drive.md)开始。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请取消选中此复选框。

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续操作。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别（可选）。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 "**不适用**"。

请参阅[产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本

输入产品/服务的版本号。 客户将在产品/服务的详细信息页上看到此版本列出。

### <a name="terms-and-conditions"></a>条款和条件

在“条款和条件”字段中，提供你自己的法律条款和条件。 还可以提供能够在其中找到条款和条件的 URL。 客户必须接受这些条款，才能试用产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="offer-listing"></a>产品/服务列表

此页显示用于列出产品/服务的语言。 目前，“英语(美国)”是唯一的可用选项。

需要为每种语言/市场定义市场详细信息（产品/服务名称、说明、图像等）。 请选择语言/市场名称，以提供此类信息。

> [!NOTE]
> 只要产品/服务说明的开头短语是“此应用只提供[非英语语言]版本”，那么产品/服务一览内容（如说明、文档、屏幕截图、使用条款等）就不要求是英语的。 提供实用链接 URL 来提供产品/服务一览内容中使用的语言以外的语言内容，也是可以接受的。

下面是如何在 Microsoft AppSource 中显示产品/服务的示例：

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 产品
3. 类别
4. 行业
5. 支持地址（链接）
6. 使用条款
7. 隐私策略
8. 产品名称
9. 屏幕截图/视频
10. 说明

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

如果你想让客户知道你的应用适用于特定产品，请在此处输入最多三个产品名称。

### <a name="support-urls"></a>支持 URL

可在此部分提供链接，帮助客户详细了解你的产品/服务。

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


#### <a name="screenshots"></a>屏幕截图

添加屏幕截图来展示产品/服务的工作方式。 至少需要一个屏幕截图，最多可添加五个屏幕截图。 所有屏幕截图的大小都必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

可以选择添加最多四个演示产品/服务的视频。 这些视频应在 YouTube 和/或 Vimeo 上托管。 对于每个视频，请输入视频的名称、URL 和视频的缩略图（1280 x 720 像素）

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

[市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)

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

在将产品/服务发布到更广泛的市场产品/服务前，需要先将其提供给有限的预览版受众。 在此处输入隐藏密钥（任何只使用小写字母和/或数字的字符串）。 预览版受众的成员可以将此隐藏密钥用作令牌，以查看市场中产品/服务的预览版。

然后，当你准备好提供产品/服务并删除预览限制时，需要删除该隐藏密钥并再次发布。

选择“保存草稿”，然后继续。

## <a name="technical-configuration"></a>技术配置

此页定义用于连接到产品/服务的技术详细信息。 使用此连接，可以为选择获取你的产品/服务的最终客户预配产品/服务。

### <a name="solution-identifier"></a>解决方案标识符

提供解决方案的解决方案标识符 (GUID)。

若要查找解决方案标识符，请执行以下操作：

1. 在 Microsoft Dynamics Lifecycle Services (LCS) 中，选择“解决方案管理”。
2. 选择解决方案，然后在“包概述”中查找“解决方案标识符”。 如果标识符是空白，请选择“编辑”并重新发布包，然后重试。

### <a name="release-version"></a>发行版本

选择此解决方案使用的 Dynamics 365 for Finance and Operations 的版本。

选择“保存草稿”，然后继续。

## <a name="test-drive-technical-configuration"></a>体验版技术配置

可使用此页设置演示（“体验版”），让客户可在购买之前试用你的产品/服务。 有关详细信息[，请参阅什么是测试驱动器](../what-is-test-drive.md)。

若要启用体验版，请在[“产品/服务设置”](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请取消选中此复选框。

完成设置测试驱动器后，请选择 "**保存草稿**"，然后继续。

## <a name="supplemental-content"></a>补充内容

可使用此页提供有关产品/服务的其他信息，以帮助我们验证你的产品/服务。 此信息不显示给客户，也不在市场上发布。

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
- 在“认证说明”部分，除了有助于理解应用的任何补充说明之外，还可以向认证团队提供测试说明，以确保应用得到正确测试。
- 通过选择“提交”来提交要发布的产品/服务。 我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。 返回到 "合作伙伴中心"，并选择 "产品 **/** 服务"，将产品/服务发布到公共产品/服务。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
