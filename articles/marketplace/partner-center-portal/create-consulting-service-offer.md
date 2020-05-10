---
title: 创建咨询服务产品/服务-Microsoft 商业应用商店
description: 了解如何使用合作伙伴中心将咨询服务产品/服务发布到 Microsoft AppSource 或 Azure Marketplace。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 626dc5a7d1cd52182e68eb0d217b2ac4c653330e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994354"
---
# <a name="create-a-consulting-service-offer"></a>创建咨询服务套餐

本文介绍如何将咨询服务产品/服务发布到[Microsoft AppSource](https://appsource.microsoft.com/)或[Azure Marketplace](https://azuremarketplace.microsoft.com/)。 基于 AppSource 上的 Microsoft [Dynamics 365](https://dynamics.microsoft.com/)和 Power Platform 列出咨询服务产品/服务。 基于 Azure Marketplace 上的 Microsoft Azure 列出咨询服务产品/服务。 在开始之前，请[在合作伙伴中心创建一个商用 marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在 "商用 marketplace 计划" 中注册。

在创建产品/服务之前，请先查看[咨询服务先决条件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)中的先决条件。

## <a name="publishing-benefits"></a>发布的好处

发布到商业应用商店的好处：

- 使用 Microsoft 品牌提升你的公司。
- 通过 Azure Marketplace 200000，可能会超过100000000个 Office 365 和 Dynamics 365 AppSource 多个组织的用户。
- 从这些市场接收高质量的潜在顾客。
- 由 Microsoft 现场和电话销售团队推广你的服务

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新建产品** > /**服务**"。

    ![阐释左侧导航菜单。](./media/new-offer-consulting-service.png)

>[!NOTE]
>发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

- 适用于 marketplace 产品的 web 地址中的客户可看到此 ID。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果输入了 "**测试/服务-1**"，则 "产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`" 网址为。
- 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

- 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
- 选择 "**创建**" 后，不能更改该产品/服务别名。

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

### <a name="connect-lead-management"></a>连接潜在客户管理

使用合作伙伴中心向 marketplace 发布产品/服务时，_必须_将其连接到客户关系管理（CRM）或市场营销自动化系统。 这使你可以在有人向你的产品表达兴趣或使用你的产品时立即收到客户联系信息。

1. 选择 "**连接**" 以指定要向其发送客户主管的位置。 合作伙伴中心支持以下系统：

    - 适用于客户参与的[Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果上面未列出你的 CRM 系统，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户领导数据，然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布产品/服务后，将产品/服务连接到潜在客户。
3. 确认正确配置了与潜在客户目标的连接。 在合作伙伴中心发布后，我们将验证连接并向你发送测试线索。 当你在推出产品/服务前预览产品/服务时，你还可以通过尝试在预览环境中自行购买产品/服务来测试你的潜在客户连接。
4. 请确保与潜在客户目标的连接保持更新，以便不会丢失任何潜在顾客。

下面是一些其他潜在客户管理资源：

- [潜在客户管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （确保弹出窗口阻止程序处于关闭状态）

选择 "**保存草稿**"，然后继续。

### <a name="properties"></a>属性

使用此页，您可以设置咨询服务提供最佳覆盖的主要产品、设置咨询服务类型，并选择适用的产品。

1. 从下拉列表中选择一个**主产品**。
2. 从下拉列表中选择 "**咨询服务类型**"：

    - **评估**：评估客户的环境，以确定解决方案的适用性，并提供成本和时间估算。
    - **简介**：使用框架、演示和客户示例介绍解决方案或咨询服务，以吸引客户的兴趣。
    - **实施：** 进行完整安装，以实施完全正常运行的解决方案。 限制为可在两周或更短时间内实施的解决方案。
    - **概念证明：** 进行有限范围的实施，以确定解决方案是否满足客户的要求。
    - **研讨会**：在客户的本地开展互动。 它可能涉及到基于客户数据或环境构建的培训、简报、评估或演示。

3. 如果选择了**Azure**的主要产品，最多可选择三个**解决方案区域**。 这使得 Azure Marketplace 中的客户能够更轻松地找到你的产品/服务。 如果未选择 "Azure"，请跳过此步骤。
4. 如果选择了除_Azure 之外的主要产品，_ 请选择最多三个适用的**产品**。 这使 AppSource 中的客户能够更轻松地找到你的产品/服务。 有关详细信息，请参阅[Microsoft AppSource 咨询服务列表准则](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)（PDF）。
5. 选择最多六个适用于你的产品/服务的**行业**。 这会使客户更容易找到你的产品/服务。
6. 添加最多三项**技能**，使其能够在你的咨询服务产品列表中显示。 至少需要一项能力，Azure 专家 Msp 和 Azure 网络 Msp 除外。

选择 "**保存草稿**"，然后继续。

## <a name="offer-listing"></a>产品/服务列表

在这里，你将定义显示在 marketplace 中的产品/服务详细信息。 这包括产品名称、说明、图像等。 在配置此产品/服务时，请确保遵循[商业应用商店认证策略页](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)上详细的策略。

> [!NOTE]
> 如果产品/服务说明以短语开头，则产品/服务详细信息不需要&quot;为英语，此应用程序仅在 [非英语语言] 中可用。&quot;还可以提供一个有用的链接来提供与产品/服务列表详细信息中所使用的语言不同的内容。

### <a name="name"></a>名称

您在此处输入的名称将显示为您的产品/服务的标题。 此字段预先填充了你在创建产品/服务时在 "**产品/服务**" 框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字的（可以包括商标或版权符号）。
- 长度不能超过50个字符。
- 不能包含表情符号。

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
- 不要依赖特性和功能来销售产品。 而是专注于你的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于权益的词汇。

若要使您的产品/服务说明更具吸引力，请使用富文本编辑器应用格式设置。

![使用富文本编辑器](./media/rich-text-editor.png)

| <center>更改文本格式 | <center>添加项目符号或编号 | <center>添加或删除文本缩进 |
| --- | --- | --- |
| <center>![使用富文本编辑器更改文本格式](./media/text-editor3.png) |  <center>![使用富文本编辑器添加列表](./media/text-editor4.png) |  <center>![使用富文本编辑器缩进](./media/text-editor5.png) |

### <a name="keywords"></a>关键字

输入与你的主要产品和咨询服务相关的最多三个搜索关键字。 这样可以更轻松地找到产品/服务。

### <a name="duration"></a>持续时间

设置此参与与客户的预期持续时间。

### <a name="contact-information"></a>联系信息

您必须为**主要****联系人和辅助联系人**提供姓名、电子邮件和电话号码。 此信息不会向客户显示。 它可用于 Microsoft，并可提供给云解决方案提供商（CSP）合作伙伴。

### <a name="supporting-documents"></a>支持文档

为你的产品/服务添加最多三个（但至少有一个）支持 PDF 文档。

### <a name="marketplace-images"></a>Marketplace 映像

提供要用于产品/服务的徽标和图像。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

>[!Note]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="store-logos"></a>应用商店徽标

在以下每个像素大小中提供产品/服务徽标的 PNG 文件：

- **小型（48 x 48）**
- **大（216 x 216）**

所有徽标都是必需的，并用于 marketplace 列表中的不同位置。

#### <a name="screenshots-optional"></a>屏幕快照（可选）

最多可添加五个屏幕截图，其中显示了产品/服务的工作方式。 每个必须 1280 x 720 像素大小和 PNG 格式。

#### <a name="videos-optional"></a>视频（可选）

最多可添加四个视频来演示产品/服务。 输入视频的名称、其 web 地址（URL）和视频的缩略图（以 1280 x 720 像素大小表示）。

选择 "**保存草稿**"，然后继续。

## <a name="pricing-and-availability"></a>定价和可用性

在这里，你将定义定价、市场和私钥等元素。

1. **市场**：设置产品/服务的市场。 对于每个产品/服务，只能选择一个市场。
    1. 对于美国或加拿大市场，请选择 "**编辑省/** 市/**自治区**" 以指定产品/服务的可用位置。
2. **预览受众**：配置用于为产品/服务设置专用受众的**隐藏密钥**。
3. **定价**：指定你的产品/服务是否为**免费**或**付费**产品/服务。

    > [!NOTE]
    > 咨询服务优惠仅适用于列表。 所有事务都将直接在商业市场外发生。

4. 对于付费产品/服务，指定**价格和币种**以及价格是**固定**的还是**估计**的。 如果估计，则必须在 "说明" 中指定哪些因素会影响价格。
5. 选择 "**保存草稿**"，然后继续。

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
