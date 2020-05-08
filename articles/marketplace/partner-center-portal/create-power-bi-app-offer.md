---
title: 在 Microsoft 商业应用商店中创建 Power BI 应用产品/服务
description: 了解如何创建 Power BI 应用服务并将其发布到 Microsoft AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 17a6b7e46341ee0231ac88b849495cfc7e46ad88
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839141"
---
# <a name="create-a-power-bi-app-for-microsoft-appsource"></a>为 Microsoft AppSource 创建 Power BI 应用

本文介绍如何创建 Power BI 应用程序产品并将其发布到 Microsoft [AppSource](https://appsource.microsoft.com/)。

在开始之前，请[在合作伙伴中心创建一个商用 Marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在 "商用 marketplace 计划" 中注册。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新产品** > /**服务 Power BI 服务应用**"。

   ![阐释左侧导航菜单。](./media/new-offer-pbi-app.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

> [!IMPORTANT]
> 如果未显示或未启用**Power BI 服务应用**，则帐户无权创建此产品/服务类型。 请检查是否满足了此产品/服务类型的所有[要求](create-power-bi-app-overview.md)，包括注册开发人员帐户。

## <a name="new-offer"></a>新产品/服务

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

- 适用于 marketplace 产品/服务和 Azure 资源管理器模板的 web 地址中的客户可看到此 ID （如果适用）。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果在此处输入了 "**测试/服务-1** "，则 "产品/ `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`服务" 网址为。
- 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

- 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
- 选择 "**创建**" 后，不能更改该产品/服务别名。

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-overview"></a>产品/服务概述

此页面显示了发布此产品/服务所需步骤的直观表示形式（已完成和即将推出）以及每个步骤需要多长时间才能完成。

它包含基于你所做的选择对此服务执行操作的链接。 例如：

- 如果产品/服务是草稿版/草稿版[产品](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)/服务
- 如果产品/服务是实时的，则[停止销售产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果[产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)为预览版-上线
- 如果尚未完成发布服务器注销-请[取消发布](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>产品/服务设置

### <a name="connect-lead-management"></a>连接潜在客户管理

在合作伙伴中心向 marketplace 发布产品/服务时，必须将其连接到客户关系管理（CRM）系统。 这使你可以在有人向你的产品表达兴趣或使用你的产品时立即收到客户联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

    - 适用于客户参与的[Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果上面未列出你的 CRM 系统，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户的潜在客户数据。 然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布产品/服务后，将产品/服务连接到潜在客户。
3. 确认正确配置了与潜在客户目标的连接。 在合作伙伴中心发布后，我们将验证连接并向你发送测试线索。 当你在推出产品/服务前预览产品/服务时，你还可以通过尝试在预览环境中自行购买产品/服务来测试你的潜在客户连接。
4. 请确保与潜在客户目标的连接保持更新，以便不会丢失任何潜在顾客。

下面是一些其他潜在客户管理资源：

- [潜在客户管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （确保弹出窗口阻止程序处于关闭状态）

选择 "**保存草稿**"，然后继续。

## <a name="properties"></a>“属性”

此页面允许你定义用于在 marketplace 上对产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="category"></a>类别

选择最小值，最多三个类别。 这些类别用于将产品/服务放入相应的 marketplace 搜索区域，并显示在 "产品/服务详细信息" 页上。 在产品/服务说明中，说明产品/服务对这些类别的支持。

### <a name="industry"></a>行业

（可选）在每个行业下最多选择两个行业和两个纵向。 虽然类别用于显示您的产品/服务，但在搜索筛选器中使用的是行业和纵向，并在店面中应用。 如果产品/服务面向特定的行业和/或垂直，请使用产品/服务说明来说明产品/服务如何支持所选行业或纵向。 如果产品/服务不特定于行业，请将本部分留空。

> [!NOTE]
> 随着我们努力引入新的行业和纵向来改善产品发现体验，某些行业或纵向可能还不能在店面上看到。 标有（*）的行业和纵向将在将来的某个日期提供。 所有发布的产品/服务都可以通过关键字搜索来发现。
<p>&nbsp;

| **行业** | **Subindustry** |
| --- | --- |
| * 汽车 | * 汽车 |
| 农业 | * 其他-Unsegmented |
| 分布 | * 批发<br>包裹和包裹寄送 |
| 教育水平 | *高等教育<br> *主要和次要教育/K-12<br>* 库和博物馆 |
| 金融服务 | *银行和资本市场<br> *等 |
| Government | *防御和智能（称为国家和公共安全）<br> *公共安全和司法<br>* 民用政府 |
| 医疗保健（用于称为运行状况） | *运行状况<br> Payor*运行状况提供程序<br>* 药物 |
| 制造和资源（用于称为制造） | *化学和 Agrochemical<br> *离散制造业<br>* 能量 |
| 零售和消费品（用于称为零售） | *消费品<br> *经销商 |
| * 媒体和通信（用于称为媒体和娱乐） | *媒体和娱乐<br> *通讯 |
| 专业服务 | *法律<br> *合作伙伴专业服务 |
| * 体系结构和构造（用于称为体系结构工程） | * 其他-Unsegmented |
| * 招待和旅游 | *宾馆和休闲<br> *旅行和运输<br>* 餐厅和食物服务 |
| * 其他公共部门行业 | *林和钓鱼<br> *非营利组织 |
| * 房地产 | * 其他-Unsegmented |

### <a name="legal"></a>合法

#### <a name="terms-and-conditions"></a>条款和条件

若要提供自己的自定义条款和条件，请在 "**条款和条件**" 框中输入最多10000个字符。 如果你的条款和条件需要较长的说明，请输入单个 web 链接，以便找到可找到的位置。 它将向客户显示为活动链接。

客户必须接受这些条款，然后才能试用你的产品/服务。

选择 "**保存草稿**"，然后继续下一部分 "产品/服务"。

## <a name="offer-listing"></a>产品/服务列表

在这里，你将定义显示在 marketplace 中的产品/服务详细信息。 这包括产品名称、说明、图像等。

### <a name="language"></a>语言

选择将在其中列出产品/服务的语言。 目前，只有**英语（美国）** 是可用的选项。

为每种语言/市场定义 marketplace 详细信息，例如产品/服务的名称、描述和图像。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 如果产品/服务说明以短语开头，则产品/服务详细信息不需要为英语，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个有用的链接来提供与产品/服务列表中使用的语言不同的内容。

### <a name="name"></a>名称

此处输入的名称将显示为产品/服务的标题。 此字段预先填充了你在创建产品/服务时在 "**产品/服务**" 框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字的（可以包括商标或版权符号）。
- 长度不能超过50个字符。
- 不能包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明。 此值最多可包含100个字符，并在 marketplace 搜索结果中使用。

### <a name="description"></a>说明

提供产品/服务的更长说明，最多3000个字符。 这会显示给 marketplace 列表概述中的客户。

在说明中包括以下一项或多项：

- 提供的价值和重要优势。
- 类别或行业协会，或两者。
- 应用内购买机会。
- 任何所需的公开。

下面是有关编写说明的一些提示：

- 在说明的前几个句子中清楚地说明产品/服务的价值。 包括以下项：
  - 套餐的说明。
  - 从产品/服务中受益的用户类型。
  - 客户需求或颁发提供地址。
- 请记住，前几个句子可能会显示在搜索结果中。
- 不要依赖特性和功能来销售产品。 而是专注于你的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于权益的词汇。

若要使您的产品/服务说明更具吸引力，请使用富文本编辑器应用格式设置。

![使用富文本编辑器](./media/rich-text-editor.png)

| <center>更改文本格式 | <center>添加项目符号或编号 | <center>添加或删除文本缩进 |
| --- | --- | --- |
| <center>![使用富文本编辑器更改文本格式](./media/text-editor3.png) |  <center>![使用富文本编辑器添加列表](./media/text-editor4.png) |  <center>![使用富文本编辑器缩进](./media/text-editor5.png) |

### <a name="search-keywords"></a>搜索关键字

输入最多三个可选搜索关键字，帮助客户在 marketplace 中找到你的产品/服务。 为了获得最佳结果，请在说明中使用这些关键字。

### <a name="helpprivacy-web-addresses"></a>帮助/隐私 Web 地址

提供可帮助客户更好地了解产品/服务的链接。

#### <a name="help-link"></a>帮助链接

输入客户可在其中了解你的产品/服务的详细信息。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的 web 地址。 你需要负责确保你的产品/服务符合隐私法律和法规。 你还负责在你的网站上发布有效的隐私策略。

### <a name="contact-information"></a>联系信息

您必须为**支持联系人**和**工程联系人**提供姓名、电子邮件和电话号码。 此信息不会向客户显示。 它可用于 Microsoft，并可提供给云解决方案提供商（CSP）合作伙伴。

- 支持联系人（必需）：有关一般支持问题。
- 工程联系人（必需）：用于技术问题和认证问题。
- CSP 计划联系人（可选）：适用于与 CSP 计划相关的经销商问题。

在 "**支持联系人**" 部分中，提供**支持网站**的 web 地址，合作伙伴可在其中找到对你的产品/服务的支持。

### <a name="supporting-documents"></a>支持文档

提供至少一个与三个以上的相关营销文档（PDF 格式）。 例如，白皮书、小册子、清单或演示文稿。

### <a name="marketplace-images"></a>Marketplace 映像

提供要用于产品/服务的徽标和图像。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

>[!NOTE]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="store-logos"></a>应用商店徽标

以两个像素大小提供产品/服务的徽标的 .png 文件：
- **小型**（48 x 48）
- **大**（216 x 216）

这两种徽标都是必需的，并用于 marketplace 列表中的不同位置。

#### <a name="screenshots"></a>屏幕截图

添加至少一个最多五个屏幕截图，其中显示了产品/服务的工作原理。 每个大小必须为 1280 x 720 像素大小和 .png 格式。

#### <a name="videos-optional"></a>视频（可选）

添加最多5个视频来演示产品/服务。 以 1280 x 720 像素的大小输入视频的名称、其 web 地址和缩略图。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

若要了解有关创建产品/服务列表的详细信息，请参阅[产品/服务列表最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

## <a name="technical-configuration"></a>技术配置

将 Power BI 服务中的应用提升为生产环境，并提供可让客户安装应用的 Power BI 应用安装程序链接。 有关详细信息，请参阅[在 Power BI 中将应用发布到仪表板和报表](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。

## <a name="supplemental-content"></a>补充内容

提供有关你的产品/服务的其他信息，以帮助我们进行验证。 此信息不会向客户显示，也不会发布到 marketplace。

### <a name="validation-assets"></a>验证资产

（可选）添加说明（最多3000个字符），以帮助 Microsoft 验证团队配置、连接和测试你的应用。 包括典型的配置设置、帐户、参数或可用于测试 "连接数据" 选项的其他信息。 此信息仅对验证团队可见，并仅用于验证目的。

## <a name="review-and-publish"></a>查看和发布

完成产品/服务的所有必需部分后，可以提交产品/服务进行查看和发布。

在门户的右上角，选择 "**查看并发布**"。

在 "查看" 页上，您可以：

- 请参阅产品/服务的每个部分的完成状态。 在产品/服务的所有部分标记为 "已完成" 之前，无法发布。
  - **未启动**-此部分尚未启动，需要完成。
  - **不完整**-部分包含需要修复的错误或需要提供详细信息。 有关指南，请参阅本文档前面的部分。
  - **完成**-部分包含所有必需的数据，并且没有错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保正确测试您的应用程序。 同时，提供有助于了解产品/服务的补充说明。

若要提交产品/服务进行发布，请选择 "**发布**"。

我们将向你发送一封电子邮件，告知你该产品/服务的预览版本可供查看和批准。 若要将产品/服务发布到公共产品/服务（如果是专用用户），请访问合作伙伴中心，并选择 "**上线**"。
