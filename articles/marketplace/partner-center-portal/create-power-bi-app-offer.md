---
title: 在 Microsoft 商业市场中创建 Power BI 应用产品/服务
description: 了解如何创建 Power BI 应用产品/服务并发布到 Microsoft AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 16b44b3a4496e98f8b6b643bf76aa5aa7ce593c6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849087"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>为 Microsoft AppSource 创建 Power BI 应用

本文介绍如何创建 Power BI 应用程序产品/服务并发布到 Microsoft [AppSource](https://appsource.microsoft.com/)。

在开始之前，[在合作伙伴中心创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在商业市场计划中注册。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择“商业市场” > “概述”。
3. 在“概述”页上，选择“+ 新建产品/服务” > “Power BI 服务应用”。

   ![阐释左侧导航菜单。](./media/new-offer-pbi-app.png)

> [!NOTE]
> 发布产品/服务后，在合作伙伴中心对其进行的编辑仅在重新发布该产品/服务后才会显示在店面中。 确保在进行更改后始终重新发布。

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

- 如果产品/服务是草稿 - [删除草稿产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果产品/服务已上线 - [停止销售产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果产品/服务为预览版 - [投入使用](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果尚未完成发布服务器注销 - [取消发布](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>产品/服务设置

### <a name="customer-leads"></a>潜在顾客

将产品/服务发布到合作伙伴中心的市场时，必须将其连接到客户关系管理 (CRM) 系统。 这样，只要某人表示有兴趣购买产品或使用产品，就会收到该客户联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) for Customer Engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果前面未列出你的 CRM 系统，请使用 [Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或 [Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)存储潜在顾客数据。 然后将数据导出到你的 CRM 系统。

2. 在合作伙伴中心发布时，将产品/服务连接到潜在顾客目标。
3. 确认正确配置了到潜在顾客目标的连接。 在合作伙伴中心发布后，我们将验证连接，并向你发送一个测试潜在顾客。 如果要在投入使用前预览产品/服务，还可以通过亲自尝试在预览环境中购买产品/服务来测试潜在顾客连接。
4. 请确保与顾客连接目标的连接保持最新状态，以免丢失任何顾客连接。

下面是一些其他顾客连接管理资源：

- [顾客连接管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [顾客连接管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF（确保弹出窗口阻止程序处于关闭状态）

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

可使用此页定义用于对市场上的产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

最少选择一个类别，最多选择三个类别。 这些类别用于将产品/服务置于相应的市场搜索区域中，并显示在“产品/服务详细信息”页上。 在产品/服务说明中，介绍产品/服务如何支持这些类别。

### <a name="industry"></a>行业

（可选）在每个行业下最多选择两个行业和两个纵向。 类别用于显示你的产品/服务，而行业和纵向在搜索筛选器中使用，并应用于店面。 如果产品/服务面向特定行业和/或纵向，请使用产品/服务说明来解释产品/服务如何支持所选行业或纵向。 如果产品/服务不特定于行业，请将此部分留空。

> [!NOTE]
> 因为我们努力引入新的行业和纵向来改善产品/服务发现体验，所以某些行业或纵向可能尚未显示在店面中。 标有 (*) 的行业和纵向将在某个将来日期可用。 所有发布的产品/服务都可以通过关键字搜索来发现。
<p>&nbsp;

| 行业 | 子行业 |
| --- | --- |
| *汽车 | *汽车 |
| 农业 | *其他 - 未划分 |
| 分发 | *批发<br>包裹和包装运送 |
| 教育 | 高等教育<br>中小学教育/K-12<br>*图书馆和博物馆 |
| 金融服务 | 银行和资本市场<br>保险 |
| Government | 国防和情报（过去称为国家和公共安全）<br>公共安全和司法<br>*平民政府 |
| 医疗保健（过去称为健康） | 健康支付者<br>健康提供方<br>*制药 |
| 制造和资源（过去称为制造） | 化学制品和农药<br>离散制造<br>*能源 |
| 零售和消费品（过去称为零售） | 消费品<br>零售商 |
| *媒体和通信（过去称为媒体和娱乐） | 媒体和娱乐<br>电信 |
| 专业服务 | 法律<br>合作伙伴专业服务 |
| *建筑和施工（过去称为建筑工程） | *其他 - 未划分 |
| *酒店餐饮和旅游 | 酒店和休闲<br>旅游和运输<br>*餐馆和餐饮服务 |
| *其他公共部门行业 | 林业和渔业<br>非营利组织 |
| *房地产 | *其他 - 未划分 |

### <a name="legal"></a>合法

#### <a name="terms-and-conditions"></a>条款和条件

若要提供你自己的自定义条款和条件，请在“条款和条件”框中输入最多 10,000 个字符。 如果你的条款和条件需要更长的说明，请输入指向它们所在位置的单个 Web 链接。 它将作为活动链接向客户显示。

客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续进行到下一部分“产品/服务列表”。

## <a name="offer-listing"></a>产品/服务列表

在此处将定义显示在市场中的产品/服务详细信息。 其中包括产品/服务名称、说明、图像等。

### <a name="language"></a>语言

选择用于列出产品/服务的语言。 当前，“英语(美国)”是唯一的可用选项。

为每种语言/市场定义市场详细信息（例如产品/服务名称、说明和图像）。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 如果产品/服务说明的开头是“此应用程序仅以 [非英语语言] 推出”，则产品/服务详细信息不必是英语。 还可以提供一个有用的链接，来使用与产品/服务列表中所使用的语言不同的语言提供内容。

### <a name="name"></a>名称

在此处输入的名称将显示为你的产品/服务的标题。 此字段预填充了在创建产品/服务时在“产品/服务别名”框中输入的文本。 你以后可以更改此名称。

名称：

- 可以是商标字（可以包括商标或版权符号）。
- 长度不能超过 50 个字符。
- 不能包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明。 此说明最多可包含 100 个字符，并在市场搜索结果中使用。

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

- 支持联系人（必需）：解决常规支持问题。
- 工程联系人（必需）：解决技术问题和认证问题。
- CSP 计划联系人（可选）：解决与 CSP 计划相关的经销商问题。

在“支持联系人”部分中，提供“支持网站”的 Web 地址，合作伙伴可以在该网站中找到对产品/服务的支持 。

### <a name="supporting-documents"></a>支持性文档

提供至少一个并且最多三个相关市场营销文档（PDF 格式）。 例如，白皮书、手册、清单或演示文稿。

### <a name="marketplace-images"></a>市场映像

提供要用于产品/服务的徽标和图像。 所有图像都必须为 .png 格式。 模糊图像将被拒绝。

>[!NOTE]
>如果上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>Microsoft Store 徽标

按以下两个像素大小提供产品/服务徽标的 .png 文件：
- 小图 (48 x 48)
- 大图 (216 x 216)

两种徽标都是必需的，并且在市场列表的不同位置上使用。

#### <a name="screenshots"></a>屏幕截图

添加至少一个并且最多五个显示产品/服务的工作方式的屏幕截图。 每个屏幕截图大小必须为 1280 x 720 像素并采用 .png 格式。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个演示产品/服务的视频。 输入视频的名称、其 Web 地址和视频的缩略图 .png 图像（大小为 1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

若要详细了解如何创建产品/服务列表，请参阅[产品/服务列表最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

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

- 查看产品/服务的每个部分的完成状态。 在产品/服务的所有部分标记为“已完成”之前，无法发布。
  - 未开始 - 该部分尚未开始，需要完成。
  - 未完成 - 该部分包含需要修复的错误或需要提供详细信息。 有关指南，请参阅本文档前面的部分。
  - 完成 - 该部分包含所有必需的数据，并且没有错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保应用已正确测试。 同时，提供有助于了解产品/服务的补充说明。

若要提交产品/服务进行发布，请选择“发布”。

我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。 若要将你的产品/服务发布给公众（如果是专用产品/服务，则发布给专用受众），请访问合作伙伴中心，并选择“投入使用”。
