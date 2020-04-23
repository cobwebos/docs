---
title: 在合作伙伴中心 - Azure 应用商店中创建咨询服务产品
description: 了解如何使用合作伙伴中心将咨询服务产品发布到 Azure 应用商店或 AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869794"
---
# <a name="create-a-consulting-service-offer"></a>创建咨询服务产品

> [!IMPORTANT]
> 我们将您的咨询服务服务服务从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照 Azure[和 Dynamics 365 云合作伙伴门户咨询服务产品提供](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer)的说明进行管理产品/服务。

本文介绍如何将咨询服务产品发布到[Azure 应用商店](https://azuremarketplace.microsoft.com/)或[AppSource](https://appsource.microsoft.com/)。 基于微软[动态365](https://dynamics.microsoft.com/)和AppSource上的电源平台提供咨询服务。 基于 Azure 应用商店上的 Microsoft Azure 的咨询服务产品。

要在 Azure 应用商店或 AppSource 咨询服务中创建咨询服务产品，您必须首先[在合作伙伴中心中拥有发布者帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，并且必须在商业市场计划中注册您的帐户。 在创建产品/服务之前，请查看[咨询服务先决条件中的先决条件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)。

## <a name="publishing-benefits"></a>发布的好处

发布到商业市场的好处：

- 使用 Microsoft 品牌推广您的公司。
- AppSource 上可能覆盖超过 1 亿个 Office 365 和 Dynamics 365 用户，并通过 Azure 应用商店覆盖 200，000 多个组织。
- 从这些市场获得高质量的潜在客户。
- 让 Microsoft 现场和电话销售团队推广您的服务

## <a name="create-a-new-offer"></a>创建新套餐

满足上述要求后，请按照以下步骤创建咨询服务产品。

1. 登录到[合作伙伴中心](https://partner.microsoft.com)，然后从顶部菜单中选择 **"仪表板**"。
2. 在左导航栏中，选择 **"商业市场**"，然后选择 **"概述**"。

    :::image type="content" source="media/cs-menu-overview.png" alt-text="说明商业市场的菜单":::

3. 选择 **= 新优惠**，然后选择**咨询服务**。

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="说明创建新产品/服务/服务/服务按钮。":::

4. 输入**优惠 ID**。 这是您帐户中每个产品/服务的唯一标识符。

    - 此 ID 在市场产品/服务的网站地址中对客户可见。
    - 仅使用小写字母、数字、破折号和下划线，但不使用空格。 长度限制为 50 个字符。 例如，如果输入**测试-产品/服务 -1，** 则产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`URL 将为 。
    - 选择 "**创建**"后，&#39;无法更改优惠 ID。

5. 输入**产品/服务别名**。 这是用于指合作伙伴中心产品/服务的名称。

    - 此名称&#39;在市场上不使用。 它&#39;与客户显示的要约名称和其他值不同。 您可以使用此字段为产品/服务分配更有用的名称，该名称对于内部标识产品/服务更有用;它不向客户显示。
    - 选择 "**创建**"后，&#39;无法更改产品/服务别名。

输入这两个值后，选择 **"创建**"以继续到 **"产品/服务设置"** 页。

## <a name="offer-setup"></a>优惠设置

输入优惠 ID 和要约别名后，合作伙伴中心将创建草稿产品/服务并显示 **"优惠设置**"页面。 按照以下步骤设置您的产品/服务。

### <a name="connect-lead-management"></a>连接潜在客户管理

使用合作伙伴中心向市场发布产品/服务时，_必须_将其连接到客户关系管理 （CRM） 或营销自动化系统。 这样，一旦有人对您的产品表示兴趣或使用，您就会立即收到客户联系信息。

1. 选择 **"连接"** 以指定您希望我们发送客户潜在顾客的位置。 合作伙伴中心支持以下系统：

    - [面向客户互动的动态 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果 CRM 系统&#39;上面未列出，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户潜在顾客数据，然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布时，请将您的产品/服务连接到潜在顾客目标。
3. 确认与潜在顾客目标的连接配置正确。 在合作伙伴中心发布后，我们将&#39;验证连接并向您发送测试线索。 在产品/服务上线之前预览产品/服务时，还可以尝试在预览环境中自行购买产品/服务，以测试潜在顾客连接。
4. 确保与潜在顾客目标的连接保持更新，这样您就不会丢失任何潜在顾客&#39;。

下面是一些额外的潜在客户管理资源：

- [潜在客户管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF（确保弹出窗口阻止程序已关闭）

选择 **"保存"草稿**，然后再继续下一节"属性"。

### <a name="properties"></a>属性

此页面允许您设置咨询服务提供的最佳封面的主要产品，设置咨询服务类型，并选择适用的产品。

1. 从下拉列表中选择**主要产品**。
2. 从下拉列表中选择**咨询服务类型**：

    - **评估**：评估客户&#39;环境，以确定解决方案的适用性，并提供成本和时间的估计。
    - **简介**：使用框架、演示和客户示例，介绍解决方案或咨询服务，以吸引客户的兴趣。
    - **实现**：一个完整的安装，导致一个完全工作的解决方案。 限制在两周或更短时间即可实施的解决方案。
    - **概念证明**：确定解决方案是否符合客户需求的有限范围实现。
    - **研讨会**：在客户&#39;的场所进行互动互动。 它可以包括基于客户&#39;的数据或环境的培训、简报、评估或演示。

1. 如果选择了**Azure**的主要产品，请选择最多三个**解决方案区域**。 这些功能使 Azure 应用商店中的客户更容易找到您的产品/服务。 如果未&#39;未选择 Azure，请跳过此步骤。
2. 如果选择了 Azure_以外的_主要产品，请选择最多三个**适用产品**。 这些使 AppSource 中的客户更容易找到您的产品/服务。 有关详细信息，请参阅[Microsoft AppSource 咨询服务列表指南](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)（PDF）。
3. 最多选择您优惠适用于的六**个行业**。 这将使客户更容易找到您的产品/服务。
4. 将贵公司获得的三**项能力**添加到咨询服务产品列表中。 除了 Azure 专家 MSP&#39;和 Azure 网络 MSP&#39;， 至少需要一项能力。

选择 **"保存"草稿**，然后再继续下一节"优惠列表"。

## <a name="offer-listing"></a>优惠列表

在这里&#39;定义市场上显示的报价详细信息。 这包括产品/服务名称、说明、图像等。 在配置此产品/服务时，请务必遵循[Microsoft&#39;策略页上](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)详细说明的策略。

> [!NOTE]
> 如果要约说明以短语开头，则报价详细信息&#39;不需要英文，&quot;此应用程序仅提供 [非英语语言]。&quot;提供有用链接&#39;提供与优惠列表详细信息中所用语言&#39;内容也没关系。

### <a name="name"></a>名称

您在此处输入的名称显示为产品的标题。 此字段预先填充您在创建产品/服务时在 **"产品/服务"别名**框中输入的文本。 可以稍后更改此名称。

名称：

- 可以注册商标（您可以包括商标或版权符号）。
- 不能&#39;不超过50个字符长。
- 不能&#39;不包括表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供您的优惠的简短说明。 这最多可达 100 个字符，并且用于市场搜索结果。

### <a name="description"></a>说明

提供较长范围的报价说明，最多 3，000 个字符。 这在市场列表概述中向客户显示。

在描述中包括以下一项或多项：

- 您的产品提供的价值和关键优势
- 类别或行业协会，或两者
- 应用内购买机会
- 任何必需的披露

以下是编写说明的一些提示：

- 清楚地描述你描述前几句的报价的价值。 包括以下项目：
  - 套餐的说明。
  - 从产品/服务中受益的用户类型。
  - 客户需求或发出产品/服务地址。
- 请记住，搜索结果中可能会显示前几个句子。
- 不要&#39;依赖特性和功能来销售您的产品。 相反，关注您的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于利益的措辞。

为了使您的描述更具吸引力，请使用丰富的文本编辑器来设置描述格式。 富文本编辑器允许您添加数字、项目符号、粗体、斜体和缩进，以使描述更具可读性。

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="说明丰富的文本编辑器以编写要约说明。" border="false":::

### <a name="keywords"></a>Keywords

最多输入三个与您的主要产品和咨询服务相关的搜索关键字。 这些将更容易找到您的报价。

### <a name="duration"></a>Duration

设置与客户的此参与的预期持续时间。

### <a name="contact-information"></a>联系信息

您必须提供**主要**和**次要联系人**的名称、电子邮件和电话号码。 此信息不会向客户显示&#39;。 它可供 Microsoft 使用，并可能提供给云解决方案提供商 （CSP） 合作伙伴。

### <a name="supporting-documents"></a>证明文件

为您的优惠添加最多三个（但至少一个）支持 PDF 文档。

### <a name="marketplace-images"></a>市场映像

提供徽标和图像，以便与您的产品配合使用。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

>[!Note]
>如果上传文件出现问题，请确保本地网络不会阻止合作伙伴中心使用https://upload.xboxlive.com的服务。

#### <a name="store-logos"></a>应用商店徽标

在以下每个像素大小中提供&#39;的 /png 产品/服务文件的 ：png 文件：

- **小 （48 x 48）**
- **大 （216 x 216）**

所有徽标都是必需的，并且在市场列表中的不同位置使用。

#### <a name="screenshots-optional"></a>屏幕截图（可选）

最多添加五个屏幕截图，显示您的优惠如何工作。 每个像素的大小和 .png 格式必须为 1280 x 720 像素。

#### <a name="videos-optional"></a>视频（可选）

最多添加四个视频，演示您的优惠。 输入视频&#39;名称、网址 （URL） 和视频的缩略图 .png 图像，大小为 1280 x 720 像素。

选择 **"保存"草稿**，然后再继续下一节"定价和可用性"。

## <a name="pricing-and-availability"></a>定价和可用性

在这里，您将定义定价、市场和私钥等元素。

1. **市场**： 设置您的报价将可用的市场. 您只能根据报价选择一个市场。
    1. 对于美国或加拿大市场，选择 **"编辑"州**（或**省**）以指定您的优惠可用位置。
2. **预览受众**：配置用于为产品/服务设置私人受众的**隐藏密钥**。
3. **定价**：指定您的优惠是**免费**还是**付费**优惠。

    > [!NOTE]
    > 咨询服务优惠仅适用于上市。 任何交易都将直接发生在商业市场之外。

4. 对于已付要约，请指定**价格和货币**以及价格是**固定**价格还是**估计**。 如果"估计"，则必须在说明中指定哪些因素会影响价格。
5. 选择 **"保存草稿**"。

## <a name="review-and-publish"></a>审核和发布

完成产品/服务所需的所有部分后，您可以提交报价以供审核和发布。

1. 当您准备好发布咨询服务产品时，请单击 **"审阅"并发布**。
2. 查看最终提交页面上的详细信息。
3. 如有必要，如果您认为产品/服务的任何详细信息需要解释，请向认证团队写一份便条。
4. 准备就绪后，选择"**提交**"。
5. **"产品/服务概述"** 页显示您的产品/服务处于哪些发布阶段。

有关您希望产品/服务在每个发布阶段的时间的详细信息，[请参阅检查商业市场产品/服务的状态](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)。

## <a name="update-your-existing-consulting-service-offers"></a>更新您现有的咨询服务优惠

- [更新商业市场中的现有产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
