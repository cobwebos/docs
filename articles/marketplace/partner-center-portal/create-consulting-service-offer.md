---
title: 创建咨询服务产品/服务 - Microsoft 商业市场
description: 了解如何使用合作伙伴中心将咨询服务产品/服务发布到 Microsoft AppSource 或 Azure 市场。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: 464e75e55bc67ce619134be01ba00f2606a271a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709064"
---
# <a name="create-a-consulting-service-offer"></a>创建咨询服务套餐

本文介绍如何将咨询服务产品/服务发布到 [Microsoft AppSource](https://appsource.microsoft.com/) 或 [Azure 市场](https://azuremarketplace.microsoft.com/)。 基于 AppSource 上的 Microsoft [Dynamics 365](https://dynamics.microsoft.com/) 和 Power Platform 列出咨询服务产品/服务。 基于 Azure 市场上的 Microsoft Azure 列出咨询服务产品/服务。 在开始之前，[在合作伙伴中心创建商业市场帐户](create-account.md)（如果尚未这样做）。 确保你的帐户已在商业市场计划中注册。

在创建产品/服务之前，请先查看[咨询服务先决条件](consulting-service-prerequisites.md)中的先决条件。

## <a name="publishing-benefits"></a>发布的好处

发布到商业市场的好处：

- 使用 Microsoft 品牌推广你的公司。
- 通过 Azure Marketplace，可能会超过 100000000 Microsoft 365 和 Dynamics 365 用户，AppSource 和200000多家组织。
- 从这些市场获得优质的潜在客户。
- 通过 Microsoft 现场和远程销售团队推广你的服务

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择“商业市场” > “概述”。
3. 在“概述”页上，选择“+ 新建产品/服务” > “咨询服务”。

    ![阐释左侧导航菜单。](./media/new-offer-consulting-service.png)

>[!NOTE]
>发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅会在重新发布产品/服务后出现在联机商店中。 确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你的帐户中每个产品/服务的唯一标识符。

- 市场产品/服务的 Web 地址中，此 ID 对客户可见。
- 只使用小写字母和数字。 其中可以包含连字符和下划线，但不能包含空格，并且不得超过 50 个字符。 例如，如果输入“test-offer-1”，则产品/服务 Web 地址将为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 选择“创建”后，无法更改产品/服务 ID。

输入“产品/服务别名”。 这是用于合作伙伴中心的产品/服务的名称。

- 此名称未用于市场，并且与向客户显示的产品/服务名称和其他值不同。
- 选择“创建”后，无法更改产品/服务别名。

选择“创建”以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

### <a name="connect-lead-management"></a>连接潜在客户管理

将产品/服务发布到合作伙伴中心的市场时，必须将其连接到客户关系管理 (CRM) 或市场营销自动化系统。 这样，只要某人表示有兴趣购买产品或使用产品，就会收到该客户联系信息。

1. 选择“连接”以指定希望我们将潜在客户发送到的位置。 合作伙伴中心支持以下系统：

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > 如果前面未列出 CRM 系统，请使用 [Azure 表](commercial-marketplace-lead-management-instructions-azure-table.md)或 [Https 终结点](commercial-marketplace-lead-management-instructions-https.md)存储潜在客户数据，然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布时，将产品/服务连接到潜在客户目标。
3. 确认正确配置了到潜在客户目标的连接。 在合作伙伴中心发布后，我们将验证连接，并向你发送一个测试潜在客户。 如果要在投入使用前预览产品/服务，还可以通过亲自尝试在预览环境中购买产品/服务来测试潜在客户连接。
4. 请确保与潜在客户目标的连接保持最新状态，以免丢失任何潜在客户。

下面是一些其他潜在客户管理资源：

- [商业市场产品/服务的潜在客户](commercial-marketplace-get-customer-leads.md)
- [有关潜在客户管理的常见问题](../lead-management-faq.md#common-questions-about-lead-management)
- [潜在客户配置错误疑难解答](../lead-management-faq.md#publishing-config-errors)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF（确保弹出窗口阻止程序处于禁用状态）。

选择“保存草稿”，然后继续。

### <a name="properties"></a>属性

可以使用此页设置咨询服务产品/服务包含的主产品，设置咨询服务类型，并选择适用的产品。

1. 从下拉列表中选择“主产品”。
2. 从下拉列表中选择“咨询服务类型”：

    - **评估**：评估客户的环境，以确定解决方案的适用性，并提供成本和时间估算。
    - **简报**：使用框架、演示和客户示例介绍解决方案或咨询服务，以吸引客户的兴趣。
    - **实施**：进行完整安装，以实施完全正常运行的解决方案。 限制为可在两周或更短时间内实施的解决方案。
    - **概念证明**：进行有限范围的实施，以确定解决方案是否满足客户的要求。
    - **研讨会**：在客户场地开展的交互式活动。 可以涉及到培训、简报、评估，或根据客户数据或环境制作的演示。

3. 如果选择了 " **Azure** 作为主要产品"，请选择最多三个 **解决方案区域**。 这样会使 Azure 市场中的客户更轻松地找到你的产品/服务。 如果未选择 Azure，请跳过此步骤。

    - Analytics
    - 应用现代化
    - 存档
    - AI + 机器学习
    - 备份
    - 大数据
    - 数据平台
    - 数据中心管理
    - DevOps
    - 灾难恢复
    - 标识
    - 物联网
    - 迁移
    - 网络
    - 安全性
    - 存储

1. 如果选择了 " **Azure** 作为主要产品"，则可以选择最多六个 **行业**。 这样会使 Azure 市场中的客户更轻松地找到你的产品/服务。 请参阅 [产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的行业完整列表。 如果未选择 "Azure"，请跳过此步骤。
1. 如果选择了除 Azure 之外的主产品，则最多选择三个适用的产品。 这样会使 AppSource 中的客户更轻松地找到你的产品/服务。 有关详细信息，请参阅 [Microsoft AppSource 咨询服务列出准则](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF)。
1. 如果选择了除*Azure 之外的主要产品，* 可以选择最多选择**两个行业和两****个行业**。 这样会使 AppSource 中的客户更轻松地找到你的产品/服务。 请参阅 [产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的行业和纵向完整列表。
1. 最多添加贵公司已赢得的三个资格，以显示在咨询服务产品列表中。 至少需要一个资格，Azure 专家 MSP 和 Azure 网络 MSP 除外。

选择“保存草稿”，然后继续。

## <a name="offer-listing"></a>产品/服务列表

在此处将定义在市场中显示的产品/服务详细信息。 其中包括产品/服务名称、说明、图像等。 配置此产品/服务时，请务必遵循[商业市场认证策略页](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)上详细说明的策略。

> [!NOTE]
> 如果产品/服务说明的开头是“此应用程序仅以 [非英语语言] 推出”，则产品/服务详细信息不必是英语。&quot;&quot;还可以提供一个有用的链接，来使用与产品/服务列表详细信息中所使用的语言不同的语言提供内容。

下面是一个示例，说明如何在 Azure Marketplace 中显示产品/服务 (任何列出的价格仅用于举例目的，而不用于反映实际成本) ：

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="说明此产品/服务在 Azure Marketplace 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 价格
3. 解决方案领域
4. 行业
5. 产品名称
6. 总结
7. 说明
8. 屏幕截图/视频

<br>以下示例显示了产品/服务信息在 Microsoft AppSource 中的显示方式， (所有的价格仅用于举例目的，而不用于反映实际成本) ：

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="说明此产品/服务在 Azure Marketplace 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 价格
3. 产品
4. 行业
5. 产品名称
6. 总结
7. 说明
8. 屏幕截图/视频
9. 文档

### <a name="name"></a>名称

在此处输入的名称将显示为你的产品/服务的标题。 此字段预填充了在创建产品/服务时在“产品/服务别名”框中输入的文本。 可以稍后更改此名称。

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

### <a name="keywords"></a>Keywords

最多输入三个与主产品和咨询服务相关的搜索关键字。 这可以方便查找你的产品/服务。

### <a name="duration"></a>Duration

设置客户参与的预期持续时间。

### <a name="contact-information"></a>联系信息

必须为主要联系人和次要联系人提供名称、电子邮件和电话号码 。 此信息不会向客户显示。 它可供 Microsoft 使用，并且可提供给云解决方案提供商 (CSP) 合作伙伴。

### <a name="supporting-documents"></a>支持性文档

为你的产品/服务最多添加三个（但至少一个）支持性 PDF 文档。

### <a name="marketplace-images"></a>市场映像

提供要用于产品/服务的徽标和图像。 所有图像都必须为 .png 格式。 模糊图像将被拒绝。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>应用商店徽标

提供 **大** 徽标徽标的 PNG 文件。 合作伙伴中心将使用此来创建 **小** 徽标。 稍后，可以选择将此替换为其他映像。

- **大型** (从 216 x 216 到 350 x 350 px，必需) 
- **小写** (48 x 48 px，可选) 

这些徽标用在列表中的不同位置。

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>屏幕截图（可选）

最多添加五张显示产品/服务工作方式的屏幕截图。 每张屏幕截图大小必须为 1280 x 720 像素并采用 PNG 格式。

#### <a name="videos-optional"></a>视频（可选）

最多添加四个演示产品/服务的视频。 输入视频的名称及其 Web 地址 (URL) 和视频的缩略图 PNG 图像（大小为 1280 x 720 像素）。

选择“保存草稿”，然后继续。

## <a name="pricing-and-availability"></a>定价和可用性

在此处将定义定价、市场和私钥等元素。

1. 市场：设置将提供你的产品/服务的市场。 对于每个产品/服务，只能选择一个市场。
    1. 对于美国或加拿大市场，选择“编辑州”（或“省”）以指定将提供产品/服务的位置 。
2. 预览受众：配置用于为你的产品/服务设置私人受众的“隐藏密钥”。
3. **定价**：指定你的产品/服务是“免费”还是“付费” 。

    > [!NOTE]
    > 咨询服务产品/服务仅适用于列表。 所有事务都将直接发生在商业市场外。

4. 对于付费产品/服务，指定“价格和货币”以及价格是“固定”还是“估计”  。 如果为“估计”，则必须在“说明”中指定哪些因素会影响价格。
5. 选择“保存草稿”，然后继续。

## <a name="review-and-publish"></a>查看和发布

完成产品/服务的所有必填部分后，可以提交产品/服务，以供查看和发布。

1. 准备好发布咨询服务产品/服务时，请选择 " **查看并发布**"。
2. 查看最终提交页上的详细信息。
3. 如果你认为你的产品/服务的任何详细信息需要说明，请向证书团队编写注释（如有必要）。
4. 准备就绪后，请选择“提交”。
5. “产品/服务概述”页显示了产品/服务所处的发布阶段。

若要详细了解你的产品/服务在每个发布阶段中所需的预计时间，请参阅[检查商业市场产品/服务的发布状态](publishing-status.md)。

## <a name="update-your-existing-consulting-service-offers"></a>更新现有的咨询服务产品/服务

- [在商业市场中更新现有的产品/服务](update-existing-offer.md)
