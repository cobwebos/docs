---
title: 在 Microsoft AppSource 中创建 Power BI 应用服务
description: 了解如何创建 Power BI 应用产品/服务并发布到 Microsoft AppSource。
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: e0dcd5db36e41093bf1cd75779770b04df2fc26a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710645"
---
# <a name="create-a-power-bi-app-offer"></a>创建 Power BI 应用套餐

本文介绍如何创建 Power BI 应用服务并将其发布到 [Microsoft AppSource](https://appsource.microsoft.com/)。

在开始之前，[在合作伙伴中心创建商业市场帐户](create-account.md)（如果尚未这样做）。 确保你的帐户已在商业市场计划中注册。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择“商业市场” > “概述”。
3. 在“概述”页上，选择“+ 新建产品/服务” > “Power BI 服务应用”。

   ![阐释左侧导航菜单。](./media/new-offer-power-bi-app.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅会在重新发布产品/服务后出现在联机商店中。 确保在进行更改后始终重新发布。

> [!IMPORTANT]
> 如果未显示或未启用“Power BI 服务应用”，则帐户无权创建此产品/服务类型。 请检查是否已满足此产品/服务类型的所有[要求](create-power-bi-app-overview.md)，包括开发人员帐户注册。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你的帐户中每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（如果适用）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线，但不能包含空格，并且不得超过 50 个字符。 例如，如果在此处输入“test-offer-1”，则产品/服务 Web 地址将为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 选择“创建”后，无法更改产品/服务 ID。

输入“产品/服务别名”。 这是用于合作伙伴中心的产品/服务的名称。

- 此名称未用于市场，并且与向客户显示的产品/服务名称和其他值不同。
- 选择“创建”后，无法更改产品/服务别名。

选择“创建”以生成产品/服务并继续。

## <a name="offer-overview"></a>产品/服务概述

此页显示了发布此产品/服务（已完成和即将推出）所需步骤的直观表示形式以及每个步骤需要多长时间才能完成。

它包含用于根据你所做的选择对此产品/服务执行操作的链接。 例如：

- 如果产品/服务是草稿 - [删除草稿产品/服务](update-existing-offer.md#delete-a-draft-offer)
- 如果产品/服务已上线 - [停止销售产品/服务](update-existing-offer.md#stop-selling-an-offer-or-plan)
- 如果产品/服务为预览版 - [投入使用](publishing-status.md#publisher-approval)
- 如果尚未完成发布- [取消发布](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>产品/服务设置

### <a name="customer-leads"></a>潜在顾客

将产品/服务发布到合作伙伴中心的市场时，必须将其连接到客户关系管理 (CRM) 系统。 这样，只要某人表示有兴趣购买产品或使用产品，就会收到该客户联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > 如果 CRM 系统不在此列表中，请使用 [Azure 表](commercial-marketplace-lead-management-instructions-azure-table.md) 或 [HTTPS 终结点](commercial-marketplace-lead-management-instructions-https.md) 来存储客户的潜在客户数据。 然后将数据导出到你的 CRM 系统。

2. 在合作伙伴中心发布时，将产品/服务连接到潜在顾客目标。
3. 确认正确配置了到潜在顾客目标的连接。 在合作伙伴中心发布后，我们将验证连接，并向你发送一个测试潜在顾客。 如果要在投入使用前预览产品/服务，还可以通过亲自尝试在预览环境中购买产品/服务来测试潜在顾客连接。
4. 请确保与顾客连接目标的连接保持最新状态，以免丢失任何顾客连接。

下面是一些其他顾客连接管理资源：

- [商业市场产品/服务的潜在客户](commercial-marketplace-get-customer-leads.md)
- [有关潜在客户管理的常见问题](../lead-management-faq.md#common-questions-about-lead-management)
- [潜在客户配置错误疑难解答](../lead-management-faq.md#publishing-config-errors)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF（确保弹出窗口阻止程序处于禁用状态）。

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别 (可选) 。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 " **不适用**"。

请参阅 [产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>合法

#### <a name="terms-and-conditions"></a>条款和条件

若要提供你自己的自定义条款和条件，请在“条款和条件”框中输入最多 10,000 个字符。 如果你的条款和条件需要更长的说明，请输入指向它们所在位置的单个 Web 链接。 它将作为活动链接向客户显示。

客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后转到下一部分“产品/服务一览”继续操作。

## <a name="offer-listing"></a>产品/服务列表

在此处将定义在市场中显示的产品/服务详细信息。 其中包括产品/服务名称、说明、图像等。

### <a name="language"></a>语言

选择用于列出产品/服务的语言。 当前，“英语(美国)”是唯一的可用选项。

为每种语言/市场定义市场详细信息（例如产品/服务名称、说明和图像）。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 如果产品/服务说明的开头是“此应用程序仅以 [非英语语言] 推出”，则产品/服务详细信息不必是英语。 还可以提供一个有用的链接，来使用与产品/服务列表中所使用的语言不同的语言提供内容。

以下示例显示了产品/服务信息在 Microsoft AppSource 中的显示方式， (所有的价格仅用于举例目的，而不用于反映实际成本) ：

:::image type="content" source="media/example-power-bi-app.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 产品
3. 类别
4. 行业
5. 支持地址 (链接) 
6. 使用条款
7. 隐私策略
8. 产品名称
9. 总结
10. 说明
11. 屏幕截图/视频

### <a name="name"></a>名称

在此处输入的名称显示为产品/服务的标题。 此字段预填充了你在创建产品/服务时在“产品/服务别名”框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字（可以包括商标或版权符号）。
- 长度不得超过 50 个字符。
- 不得包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供对产品/服务的简短说明。 此说明最多可包含 100 个字符，并在市场搜索结果中使用。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>搜索关键字

输入最多三个可选的搜索关键字，以帮助客户在市场中查找你的产品/服务。 为了获得最佳结果，请在说明中也使用这些关键字。

### <a name="helpprivacy-web-addresses"></a>帮助/隐私 Web 地址

提供可帮助客户更好地了解产品/服务的链接。

#### <a name="help-link"></a>“帮助”链接

输入客户可在其中详细了解产品/服务的 Web 地址。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的 Web 地址。 你负责确保你的产品/服务符合隐私法律和法规。 你还负责在你的网站上发布有效的隐私策略。

### <a name="contact-information"></a>联系信息

必须提供支持联系人和工程联系人的姓名、电子邮件和电话号码 。 此信息不会向客户显示。 它可供 Microsoft 使用，并且可提供给云解决方案提供商 (CSP) 合作伙伴。

- 支持联系人（必需）：用于解决常规支持问题。
- 工程联系人（必填）：用于解决技术问题和认证问题。
- CSP 计划联系人（可选）：解决与 CSP 计划相关的经销商问题。

在“支持联系人”部分中，提供“支持网站”的 Web 地址，合作伙伴可以在该网站中找到对产品/服务的支持 。

### <a name="supporting-documents"></a>支持性文档

提供至少一个并且最多三个相关市场营销文档（PDF 格式）。 例如，白皮书、手册、清单或演示文稿。

### <a name="marketplace-images"></a>市场映像

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊的图像将被拒绝。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 `https://upload.xboxlive.com` 服务。

#### <a name="store-logos"></a>应用商店徽标

提供 **大** 徽标徽标的 PNG 文件。 合作伙伴中心将使用此来创建 **小** 徽标。 稍后，可以选择将此替换为其他映像。

- **大型** (从 216 x 216 到 350 x 350 px，必需) 
- **小写** (48 x 48 px，可选) 

在列表中的不同位置使用这些徽标：

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>屏幕截图

添加至少一个并且最多五个显示产品/服务的工作方式的屏幕截图。 每张屏幕截图大小必须为 1280 x 720 像素并采用 PNG 格式。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个视频来演示产品/服务。 以 1280 x 720 像素的大小输入视频的名称、其 web 地址和缩略图 PNG 图像。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

若要详细了解如何创建产品/服务列表，请参阅[产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)。

## <a name="technical-configuration"></a>技术配置

将 Power BI 服务中的应用提升到生产环境，并提供使客户可以安装应用的 Power BI 应用安装程序链接。 有关详细信息，请参阅[在 Power BI 中发布使用仪表板和报表的应用](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。

## <a name="supplemental-content"></a>补充内容

提供有关你的产品/服务的其他信息，以帮助我们进行验证。 此信息不会向客户显示，也不会发布到市场。

### <a name="validation-assets"></a>验证资产

（可选）添加说明（最多 3,000 个字符），以帮助 Microsoft 验证团队配置、连接和测试你的应用。 包括典型配置设置、帐户、参数或可用于测试“连接数据”选项的其他信息。 此信息仅对验证团队可见，并且仅用于验证。

## <a name="review-and-publish"></a>查看和发布

完成产品/服务的所有必填部分后，可以提交产品/服务，以供查看和发布。

在门户的右上角，选择“查看和发布”。

在查看页上，你可以：

- 查看产品/服务的每个部分的完成状态。 除非产品/服务的所有部分都标记为“已完成”，否则无法发布。
  - 未启动 - 部分尚未启动，需要完成。
  - 未完成 - 部分有错误需要修复，或需要你提供更多信息。 有关指南，请参阅本文档前面的部分。
  - 完成 - 部分包含所有必需数据，并且没有错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保应用已正确测试。 同时，提供有助于了解产品/服务的补充说明。

若要提交产品/服务进行发布，请选择“发布”。

我们会向你发送电子邮件，让你知道何时有预览版产品/服务可供审阅和审核。 若要将产品/服务发布到公众，请前往 "合作伙伴中心" 并选择 " **上线**"。
