---
title: 创建 Power BI 应用产品 /服务 - Azure 应用商店
description: 了解如何创建和发布 Power BI 应用产品/服务到 Microsoft AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867599"
---
# <a name="create-a-power-bi-app"></a>创建 Power BI 应用

> [!IMPORTANT]
> 我们将 Power BI 应用产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照为云合作伙伴门户[创建 Power BI 应用产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer)以管理您的优惠的说明进行操作。

本文介绍如何创建和发布 Power BI 应用产品/服务到 Microsoft [AppSource](https://appsource.microsoft.com/)。

在创建 Power BI 应用产品/服务之前，必须在合作伙伴中心中具有商业市场帐户。 如果您尚未创建，请参阅[在合作伙伴中心帐户中创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择**商业市场** > **概述**。

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="商业市场菜单概述" border="false":::

3. 选择 **= 新优惠** > **电源 BI 应用程序**。 将显示 **"新建产品/服务**"对话框。

> [!IMPORTANT]
> 如果**power BI 应用**选项&#39;未显示或未&#39;未启用，则您的帐户&#39;无权创建此产品/服务类型。 请检查您是否&#39;满足此产品/服务类型的所有[要求](create-power-bi-app-overview.md)，包括注册开发人员帐户

### <a name="offer-id-and-alias"></a>优惠 ID 和别名

输入**优惠 ID**。 这是您帐户中每个产品/服务的唯一标识符。

- 此 ID 在市场产品/服务的网站地址和 Azure 资源管理器模板（如果适用）中可见。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但没有空格，并且限制为 50 个字符。 例如，如果输入**测试-产品/服务 -1，** 则产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`Web 地址将为 。

- 选择 "**创建**"后，无法更改优惠 ID。

输入**产品/服务别名**。 这是用于指合作伙伴中心产品/服务的名称。

- 此名称不在市场上使用，并且不同于向客户显示的要约名称和其他值。
- 选择 后无法更改此 **。**

输入这两个值后，选择 **"创建**"以继续到"产品/服务概览"页。

## <a name="offer-overview"></a>产品/服务概述

**"产品/服务概述"** 页显示发布此产品/服务所需的步骤（已完成和即将推出）以及每个步骤应完成多长时间的可视化表示形式。

它包括基于您做出的选择对此产品/服务执行操作的链接。 例如：

- 如果要约是草稿 -[删除草稿要约](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果优惠是实时的 -[停止销售优惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果优惠处于预览版 -[上线](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成发布者注销 -[取消发布](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>优惠设置

按照以下步骤设置您的产品/服务。

### <a name="connect-lead-management"></a>连接潜在客户管理

使用合作伙伴中心将产品/服务发布到市场时，必须将其连接到客户关系管理 （CRM） 系统。 这样，一旦有人对您的产品表示兴趣或使用，您就可以立即接收客户联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

    - [面向客户互动的动态 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果上面未列出 CRM 系统，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户潜在顾客数据。 然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布时，请将您的产品/服务连接到潜在顾客目标。
3. 确认与潜在顾客目标的连接配置正确。 在合作伙伴中心发布后，我们将验证连接并向您发送测试线索。 在产品/服务上线之前预览产品/服务时，还可以尝试在预览环境中自行购买产品/服务，以测试潜在顾客连接。
4. 确保与潜在顾客目标的连接保持更新状态，这样您就不会丢失任何潜在顾客。

下面是一些额外的潜在客户管理资源：

- [潜在客户管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF（确保弹出窗口阻止程序已关闭）

选择 **"保存"草稿**，然后再继续下一节"属性"。

## <a name="properties"></a>属性

此页面允许您定义用于在市场对产品/服务进行分组的类别和行业、应用版本和支持您的产品/服务的法律合同。

### <a name="category"></a>类别

选择至少一个，最多选择三个类别。 这些类别用于将您的产品/服务放入相应的市场搜索区域，并显示在您的优惠详细信息页面上。 在产品/服务说明中，说明您的优惠如何支持这些类别。

### <a name="industry"></a>行业

可以选择每个行业下最多选择两个行业和两个垂直行业。 虽然类别用于显示您的产品/服务，但行业和垂直在搜索筛选器中使用，并应用于网店。 如果您的优惠针对特定行业和/或垂直行业，请使用产品/产品/服务说明来解释您的产品/服务如何支持所选行业或垂直行业。 如果您的报价不是特定于行业&#39;，请保留此部分空白。

> [!NOTE]
> 当我们努力引入新的行业和垂直，以改善产品供应发现体验时，某些行业或垂直行业可能尚未在店面中可见。 标有 （*） 的行业和垂直行业将在未来日期提供。 所有已发布的优惠均可通过关键字搜索发现。
<p>&nbsp;

| **行业** | **子行业** |
| --- | --- |
| *汽车 | *汽车 |
| 农业 | *其他 - 未分段 |
| 分发 | *批发<br>包裹和包裹运输 |
| 教育 | *高等教育<br>* 中小学教育 / K-12<br>*图书馆和博物馆 |
| 金融服务 | *银行和资本市场<br>* 保险 |
| Government | *国防和情报（过去称为国家与公共安全）<br> *公共安全与正义<br>*文官政府 |
| 医疗保健（过去称为健康） | *健康付款人<br>* 健康提供者<br>*制药 |
| 制造与资源（过去称为制造） | *化学和农用化学<br>* 离散制造<br>*能源 |
| 零售和消费品（过去称为零售） | *消费品<br>* 零售商 |
| *媒体与通讯（过去称为媒体和娱乐） | *媒体和娱乐<br>* 电信 |
| 专业服务 | *法律<br>* 合作伙伴专业服务 |
| *建筑和建筑（过去称为建筑工程） | *其他 - 未分段 |
| *酒店和旅行 | *酒店及休闲<br>* 旅行与交通<br>*餐厅和餐饮服务 |
| *其他公共部门行业 | *林业和渔业<br>* 非营利组织 |
| *房地产 | *其他 - 未分段 |

### <a name="legal"></a>合法

#### <a name="terms-and-conditions"></a>条款和条件

要提供您自己的自定义条款和条件，请在 **"条款和条件**"框中输入最多 10，000 个字符。 如果您的条款和条件需要较长的描述，请输入单个 Web 链接，指向可以找到它们的位置。 它将作为活动链接向客户显示。

客户必须先接受这些条款，然后才能试用您的优惠。

选择 **"保存"草稿**，然后再继续下一节"优惠列表"。

## <a name="offer-listing"></a>优惠列表

在这里，您将定义市场上显示的报价详细信息。 这包括产品/服务名称、说明、图像等。

### <a name="language"></a>语言

选择您的优惠将在其中列出的语言。 目前，**英语（美国）** 是唯一可用的选项。

定义每种语言/市场的市场详细信息（如产品/服务名称、描述和图像）。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 如果要约说明以短语开头，则优惠详细信息不需要以英文提供，&quot;此应用程序仅提供 [非英语语言]。&quot;&#39;提供有用的链接，以&#39;与产品/服务清单中使用的语言不同的语言提供内容。

### <a name="name"></a>名称

您在此处输入的名称显示为产品/服务的标题。 此字段预先填充您在创建产品/服务时在 **"产品/服务"别名**框中输入的文本。 可以稍后更改此名称。

名称：

- 可以注册商标（您可以包括商标或版权符号）。
- 不能超过50个字符长。
- 不能包括表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供您的优惠的简短说明。 这最多可达 100 个字符，并且用于市场搜索结果。

### <a name="description"></a>说明

提供较长范围的报价说明，最多 3，000 个字符。 这在市场列表概述中向客户显示。

在描述中包括以下一项或多项：

- 您的产品提供的价值和关键优势。
- 类别或行业协会，或两者。
- 应用内购买机会。
- 任何必需的披露。

以下是编写说明的一些提示：

- 清楚地描述你描述前几句的报价的价值。 包括以下项目：
  - 套餐的说明。
  - 从产品/服务中受益的用户类型。
  - 客户需求或发出产品/服务地址。
- 请记住，搜索结果中可能会显示前几个句子。
- 不要依赖特性和功能来销售您的产品。 相反，关注您的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于利益的措辞。

为了使您的产品/**服务更具**吸引力，请使用丰富的文本编辑器来设置说明的格式。 富文本编辑器允许您添加数字、项目符号、粗体、斜体和缩进，以使描述更具可读性。

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="说明富文本编辑器" border="false":::

### <a name="search-keywords"></a>搜索关键字

最多输入三个可选搜索关键字，帮助客户在市场上找到您的产品/服务。 为获得最佳效果，还应在说明中使用这些关键字。

### <a name="helpprivacy-web-addresses"></a>帮助/隐私 Web 地址

提供链接，帮助客户更好地了解您的产品/服务。

#### <a name="help-link"></a>帮助链接

输入网址，客户可以在其中了解有关您的优惠的更多内容。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私政策的网址。 您有责任确保您的报价符合隐私法律和法规。 您还负责在您的网站上发布有效的隐私政策。

### <a name="contact-information"></a>联系信息

您必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示&#39;。 它可供 Microsoft 使用，并可能提供给云解决方案提供商 （CSP） 合作伙伴。

- 支持联系人（必需）：有关一般支持问题。
- 工程联系人（必需）：有关技术问题和认证问题。
- CSP 计划联系人（可选）：对于与 CSP 计划相关的经销商问题。

在 **"支持"联系人**部分中，提供**支持网站的**网址，合作伙伴可以在其中找到对您的优惠的支持。

### <a name="supporting-documents"></a>证明文件

以 PDF 格式提供至少一个和最多三个相关的营销文档。 例如，白皮书、小册子、检查表或演示文稿。

### <a name="marketplace-images"></a>市场映像

提供徽标和图像，以便与您的产品配合使用。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

>[!Note]
>如果上传文件出现问题，请确保本地网络不会阻止合作伙伴中心使用https://upload.xboxlive.com的服务。

#### <a name="store-logos"></a>应用商店徽标

提供您的优惠的 .png 文件&#39;徽标，两种大小：**小**（48 x 48 像素）**和大**（216 x 216 像素）。

这两个徽标是必需的，并且在市场列表中的不同位置使用。

#### <a name="screenshots"></a>屏幕截图

添加至少一个最多五个屏幕截图，显示您的优惠如何工作。 每个像素的大小和 .png 格式必须为 1280 x 720 像素。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个视频，演示您的优惠。 输入视频&#39;名称、Web 地址和缩略图 .png 视频图像，大小为 1280 x 720 像素。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

要了解有关创建优惠列表的更多信息，请参阅[产品/服务列表最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

## <a name="technical-configuration"></a>技术配置

将 Power BI 服务中的应用提升为生产，并提供 Power BI 应用安装程序链接，使客户能够安装你的应用。 有关详细信息，请参阅在[Power BI 中发布具有仪表板和报表的应用](https://docs.microsoft.com/power-bi/service-create-distribute-apps)。

## <a name="supplemental-content"></a>补充内容

提供有关您的优惠的其他信息，以帮助我们验证它。 此信息不会向客户显示&#39;，也不会发布到市场。

### <a name="validation-assets"></a>验证资产

或者，添加说明（最多 3，000 个字符），以帮助 Microsoft 验证团队配置、连接和测试你的应用。 包括可用于测试"连接数据"选项的典型配置设置、帐户、参数或其他信息。 此信息仅对验证团队可见，仅用于验证目的。

## <a name="review-and-publish"></a>审核和发布

&#39;完成产品/服务的所有必需部分后，您可以提交报价以供审核和发布。

在门户的右上角，选择 **"审阅"并发布**。

在评论页面上，您可以：

- 请参阅产品/服务的每个部分的完成状态。 在产品/服务的所有部分都标记为完整之前，您&#39;发布。
  - **未启动**- 部分尚未启动，需要完成。
  - **不完整**- 该部分有需要修复或要求您提供详细信息的错误。 有关指导，请参阅本文档前面的部分。
  - **完成**- 该部分具有所有必需的数据，并且没有错误。 优惠的所有部分必须完成，然后才能提交报价。
- 向认证团队提供测试说明，以确保应用测试正确。 此外，提供有助于了解您的报价的任何补充说明。

要提交发布要约，请选择 **"发布**"。

我们将向您发送一封电子邮件，告知您产品/服务预览版本何时可供审核和批准。 要向公众发布您的优惠（或者如果私人优惠，则向私人受众），请转到合作伙伴中心并选择 **"Go-live**"。
