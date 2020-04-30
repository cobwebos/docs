---
title: 在合作伙伴中心创建咨询服务产品-Azure Marketplace
description: 了解如何使用合作伙伴中心将咨询服务产品/服务发布到 Azure Marketplace 或 AppSource。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869794"
---
# <a name="create-a-consulting-service-offer"></a>创建咨询服务产品/服务

> [!IMPORTANT]
> 我们正在将你的咨询服务产品/服务的管理从云合作伙伴门户转移到合作伙伴中心。 在迁移你的产品/服务之前，请按照[Azure 和 Dynamics 365 咨询服务提供](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer)的说明进行操作，以便云合作伙伴门户管理你的产品/服务。

本文介绍如何将咨询服务产品/服务发布到[Azure Marketplace](https://azuremarketplace.microsoft.com/)或[AppSource](https://appsource.microsoft.com/)。 基于 AppSource 上的 Microsoft [Dynamics 365](https://dynamics.microsoft.com/)和 Power Platform 列出咨询服务产品/服务。 基于 Azure Marketplace 上的 Microsoft Azure 列出咨询服务产品/服务。

若要在 Azure Marketplace 或 AppSource 咨询服务中创建咨询服务产品/服务，你必须首先[在合作伙伴中心拥有一个发布者帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，并且你的帐户必须注册到 "商业市场" 计划中。 在创建产品/服务之前，请先查看[咨询服务先决条件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)中的先决条件。

## <a name="publishing-benefits"></a>发布的好处

发布到商业应用商店的好处：

- 使用 Microsoft 品牌提升你的公司。
- 通过 Azure Marketplace 200000，可能会超过100000000个 Office 365 和 Dynamics 365 AppSource 多个组织的用户。
- 从这些市场接收高质量的潜在顾客。
- 由 Microsoft 现场和电话销售团队推广你的服务

## <a name="create-a-new-offer"></a>创建新套餐

满足上述要求后，请按照以下步骤创建咨询服务产品/服务。

1. 登录到 "[合作伙伴中心](https://partner.microsoft.com)"，然后从顶部菜单中选择 "**仪表板**"。
2. 在左侧导航栏中，选择 "**商用 Marketplace**"，然后选择 "**概述**"。

    :::image type="content" source="media/cs-menu-overview.png" alt-text="演示适用于商业应用商店的菜单":::

3. 选择 " **+ 新产品**/服务"，然后选择 "**咨询服务**"。

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="说明用于创建新产品/服务的按钮。":::

4. 输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

    - 适用于 marketplace 产品的 web 地址中的客户可看到此 ID。
    - 仅使用小写字母、数字、短划线和下划线，但不要使用空格。 长度限制为50个字符。 例如，如果输入了 "**测试/服务-1**"，则 "产品/ `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`服务" URL 将为。
    - 选择 "**创建**" 后，产品/服务 ID&#39;t 会更改。

5. 输入**提议别名**。 这是用于引用合作伙伴中心中的产品/服务的名称。

    - 此名称在 marketplace 中不&#39;使用。 它&#39;不同于提供给客户的产品/服务名称和其他值。 您可以使用此字段为产品/服务分配一个名称，该名称对您在内部标识该产品/服务更有用。它不会向客户显示。
    - 选择 "**创建**" 后，该产品/服务别名可以&#39;t 更改。

在输入这两个值后，选择 "**创建**" 以继续进入 "**产品/服务设置**" 页。

## <a name="offer-setup"></a>产品/服务设置

输入产品/服务 ID 和产品/服务别名后，合作伙伴中心会创建草稿产品/服务，并显示 "**产品/服务设置**" 页。 请按照以下步骤设置你的产品/服务。

### <a name="connect-lead-management"></a>连接潜在客户管理

使用合作伙伴中心向 marketplace 发布产品/服务时，_必须_将其连接到客户关系管理（CRM）或市场营销自动化系统。 这样一来，用户就可以立即收到客户联系信息。

1. 选择 "**连接**" 以指定要向其发送客户主管的位置。 合作伙伴中心支持以下系统：

    - 适用于客户参与的[Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果 CRM 系统不&#39;以上列出，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户领导数据，然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布产品/服务后，将产品/服务连接到潜在客户。
3. 确认正确配置了与潜在客户目标的连接。 将其发布到合作伙伴中心后，我们&#39;验证连接并向你发送测试线索。 当你在推出产品/服务前预览产品/服务时，你还可以通过尝试在预览环境中自行购买产品/服务来测试你的潜在客户连接。
4. 确保与潜在客户目标的连接保持更新，这样您就不会&#39;会丢失任何潜在顾客。

下面是一些其他潜在客户管理资源：

- [潜在客户管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （确保弹出窗口阻止程序处于关闭状态）

选择 "**保存草稿**"，然后继续下一部分的 "属性"。

### <a name="properties"></a>属性

使用此页，您可以设置咨询服务提供最佳覆盖的主要产品、设置咨询服务类型，并选择适用的产品。

1. 从下拉列表中选择一个**主产品**。
2. 从下拉列表中选择 "**咨询服务类型**"：

    - **评估**：评估客户&#39;的环境，以确定解决方案的适用性，并提供成本和时间估算。
    - **简介**：使用框架、演示和客户示例介绍解决方案或咨询服务，以吸引客户的兴趣。
    - **实现：实现**完全正常工作的解决方案的完整安装。 限制为可在两周或更短时间内实施的解决方案。
    - **概念证明**：用于确定解决方案是否满足客户要求的有限范围实现。
    - **研讨会**：在客户&#39;的本地开展互动。 它可能涉及到在客户&#39;数据或环境上构建的培训、简报、评估或演示。

1. 如果选择了**Azure**的主要产品，最多可选择三个**解决方案区域**。 这使得 Azure Marketplace 中的客户能够更轻松地找到你的产品/服务。 如果未选择 "&#39;"，请跳过此步骤。
2. 如果选择了除_Azure 之外的主要产品，_ 请选择最多三个适用的**产品**。 这使 AppSource 中的客户能够更轻松地找到你的产品/服务。 有关详细信息，请参阅[Microsoft AppSource 咨询服务列表准则](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)（PDF）。
3. 选择最多六个适用于你的产品/服务的**行业**。 这会使客户更容易找到你的产品/服务。
4. 添加最多三项**技能**，使其能够在你的咨询服务产品列表中显示。 至少需要一项能力，Azure 专家 MSP&#39;s 和 Azure 网络 MSP&#39;s 除外。

选择 "**保存草稿**"，然后继续下一部分 "产品/服务"。

## <a name="offer-listing"></a>产品/服务列表

在这里，您&#39;定义在 marketplace 中显示的产品/服务详细信息。 这包括产品名称、说明、图像等。 在配置此产品/服务时，请确保遵循[Microsoft&#39;s 策略页](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)上详细的策略。

> [!NOTE]
> 产品/服务详细信息-如果产品/服务说明以短语开头，则&#39;t 要求&quot;使用英语，此应用程序仅适用于 [非英语语言]。&quot;它&#39;还可以提供一个有用的链接，以便以一种与产品/服务列表详细信息中所使用的语言不同的&#39;语言提供内容。

### <a name="name"></a>名称

您在此处输入的名称将显示为您的产品/服务的标题。 此字段预先填充了你在创建产品/服务时在 "**产品/服务**" 框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字的（可以包括商标或版权符号）。
- &#39;t 的长度不能超过50个字符。
- 可以&#39;t 是否包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明。 此值最多可包含100个字符，并在 marketplace 搜索结果中使用。

### <a name="description"></a>说明

提供产品/服务的更长说明，最多3000个字符。 这会显示给 marketplace 列表概述中的客户。

在说明中包括以下一项或多项：

- 你的产品/服务提供的价值和关键权益
- 类别或行业关联
- 应用内购买机会
- 任何所需的公开

下面是有关编写说明的一些提示：

- 在说明的前几个句子中清楚地说明产品/服务的价值。 包括以下项：
  - 套餐的说明。
  - 从产品/服务中受益的用户类型。
  - 客户需求或颁发提供地址。
- 请记住，前几个句子可能会显示在搜索结果中。
- 别&#39;t 依赖特性和功能来销售产品。 而是专注于你的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于权益的词汇。

若要使说明更具吸引力，请使用富文本编辑器来设置说明格式。 利用富文本编辑器，您可以添加数字、项目符号、粗体、斜体和缩进，使说明更具可读性。

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="说明用于编写产品说明的富文本编辑器。" border="false":::

### <a name="keywords"></a>关键字

输入与你的主要产品和咨询服务相关的最多三个搜索关键字。 这样可以更轻松地找到产品/服务。

### <a name="duration"></a>Duration

设置此参与与客户的预期持续时间。

### <a name="contact-information"></a>联系信息

您必须为**主要****联系人和辅助联系人**提供姓名、电子邮件和电话号码。 此信息不会向客户显示&#39;。 它可用于 Microsoft，并可提供给云解决方案提供商（CSP）合作伙伴。

### <a name="supporting-documents"></a>支持文档

为你的产品/服务添加最多三个（但至少有一个）支持 PDF 文档。

### <a name="marketplace-images"></a>Marketplace 映像

提供要用于产品/服务的徽标和图像。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

>[!Note]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="store-logos"></a>应用商店徽标

在以下每个像素大小中提供产品/服务的 .png 文件&#39;的徽标：

- **小型（48 x 48）**
- **大（216 x 216）**

所有徽标都是必需的，并用于 marketplace 列表中的不同位置。

#### <a name="screenshots-optional"></a>屏幕快照（可选）

最多可添加五个屏幕截图，其中显示了产品/服务的工作方式。 每个大小必须为 1280 x 720 像素大小和 .png 格式。

#### <a name="videos-optional"></a>视频（可选）

最多可添加四个视频来演示产品/服务。 输入视频&#39;的 "名称"、"web 地址（URL）" 和视频的缩略图 1280 x 720 像素大小。

选择 "**保存草稿**"，然后继续下一部分 "定价和可用性"。

## <a name="pricing-and-availability"></a>定价和可用性

在这里，你将定义定价、市场和私钥等元素。

1. **市场**：设置产品/服务的市场。 对于每个产品/服务，只能选择一个市场。
    1. 对于美国或加拿大市场，请选择 "**编辑省/** 市/**自治区**" 以指定产品/服务的可用位置。
2. **预览受众**：配置用于为产品/服务设置专用受众的**隐藏密钥**。
3. **定价**：指定你的产品/服务是否为**免费**或**付费**产品/服务。

    > [!NOTE]
    > 咨询服务优惠仅适用于列表。 所有事务都将直接在商业市场外发生。

4. 对于付费产品/服务，指定**价格和币种**以及价格是**固定**的还是**估计**的。 如果估计，则必须在 "说明" 中指定哪些因素会影响价格。
5. 选择 "**保存草稿**"。

## <a name="review-and-publish"></a>查看和发布

完成产品/服务的所有必需部分后，可以提交产品/服务进行查看和发布。

1. 准备好发布咨询服务产品/服务时，单击 "**查看并发布**"。
2. 查看最终提交页上的详细信息。
3. 如果你认为你的产品/服务的任何详细信息需要说明，如有必要，请向证书团队写信。
4. 准备就绪后，选择 "**提交**"。
5. "**产品概述**" 页显示了产品/服务的发布阶段。

若要详细了解你的产品/服务在每个发布阶段中所需的时间，请参阅[检查你的商业应用商店产品/服务的发布状态](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)。

## <a name="update-your-existing-consulting-service-offers"></a>更新现有的咨询服务优惠

- [更新商业应用商店中的现有产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
