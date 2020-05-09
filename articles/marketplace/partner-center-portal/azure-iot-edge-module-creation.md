---
title: 在 Azure Marketplace 中使用合作伙伴中心创建 Azure IoT Edge 模块产品
description: 了解如何使用合作伙伴中心在 Azure Marketplace 中创建、配置和发布 IoT Edge 模块产品
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: d69090eb07159c2c188c54499a167f127269df24
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857664"
---
# <a name="create-configure-and-publish-an-iot-edge-module-offer-in-azure-marketplace"></a>在 Azure Marketplace 中创建、配置和发布 IoT Edge 模块产品

本文介绍如何创建和发布适用于 Azure Marketplace 的物联网（IoT）边缘模块产品。 在开始之前，请[在合作伙伴中心创建一个商用 Marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在 "商用 marketplace 计划" 中注册。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新产品/服务** > **IoT Edge 模块**。

    ![阐释左侧导航菜单。](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

### <a name="offer-id-and-alias"></a>产品/服务 ID 和别名

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

- 适用于 marketplace 产品/服务和 Azure 资源管理器模板的 web 地址中的客户可看到此 ID （如果适用）。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果输入了 "**测试/服务-1**"，则 "产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`" 网址为。
- 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

- 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
- 选择 "**创建**" 后无法更改此。

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-overview"></a>产品/服务概述

"**产品概述**" 页显示了发布此产品/服务所需步骤的直观表示形式（已完成和即将推出）以及每个步骤的完成时间。

此页包含基于你所做的选择对此服务执行操作的链接。 例如：

- 如果产品/服务是草稿版/草稿版[产品](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)/服务
- 如果产品/服务是实时的，则[停止销售产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果[产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)为预览版-上线
- 如果尚未完成发布服务器注销-请[取消发布。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>产品/服务设置

请按照以下步骤设置你的产品/服务。

### <a name="connect-lead-management"></a>连接潜在客户管理

在合作伙伴中心向 marketplace 发布产品/服务时，可以选择将其连接到客户关系管理（CRM）系统。 这使你可以在有人向你的产品表达兴趣或使用你的产品时立即收到客户联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

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
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF （请确保弹出窗口阻止程序处于关闭状态）。

选择 "**保存草稿**"，然后继续。

### <a name="properties"></a>属性

使用此页，可以定义用于在 marketplace 上对产品/服务进行分组的类别，以及支持产品/服务的法律合同。

#### <a name="category"></a>类别

选择最小值，最多五个类别。 这些类别用于将产品/服务放入相应的 marketplace 搜索区域，并显示在 "产品/服务详细信息" 页上。 在产品/服务说明中，说明产品/服务对这些类别的支持。 在 "浏览" 页中，所有 IoT Edge 模块都显示在 " **物联网 > IoT Edge" 模块** "类别下。

#### <a name="legal"></a>合法

必须提供产品/服务的条款和条件。 可以使用两个选项：

- 使用 Microsoft 商业应用商店的标准约定。
- 提供您自己的条款和条件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商用 Marketplace 的标准协定

我们提供了标准合同模板来帮助促进商业应用商店中的交易。 您可以选择在标准合同下提供您的解决方案，客户只需检查并接受一次。 如果不想创建自定义条款和条件，则这是一个不错的选择。

若要了解有关标准协定的详细信息，请参阅[Microsoft 商用 Marketplace 的标准约定](https://docs.microsoft.com/azure/marketplace/standard-contract)。 你还可以下载[标准合同](https://go.microsoft.com/fwlink/?linkid=2041178)PDF （确保弹出窗口阻止程序处于关闭状态）。

若要使用标准协定，请选中 "**使用 Microsoft 商业市场的标准合同**" 复选框，然后单击 "**接受**"。

> [!NOTE]
> 使用 Microsoft 商业应用商店的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 根据标准合同或您自己的条款和条件提供您的解决方案。

![介绍如何使用 Microsoft 商业 marketplace 标准合同复选框。](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>你自己的条款和条件

若要提供自己的自定义条款和条件，请在 "**条款和条件**" 框中输入。 您可以在此框中输入不限数量的字符。 客户必须接受这些条款，然后才能试用你的产品/服务。

选择 "**保存草稿**"，然后继续下一部分 "产品/服务"。

## <a name="offer-listing"></a>产品/服务列表

在这里，你将定义显示在 marketplace 中的产品/服务详细信息。 这包括产品名称、说明、图像等。 在配置此产品/服务时，请确保遵循 Microsoft 策略页面上详细的策略。

> [!NOTE]
> 如果产品/服务说明以短语开头，则产品/服务详细信息不需要为英语，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个有用的链接来提供与产品/服务列表详细信息中所使用的语言不同的内容。

### <a name="name"></a>名称

此处输入的名称将显示为产品/服务的标题。 此字段预先填充了你在创建产品/服务时在 "**产品/服务**" 框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字的（可以包括商标或版权符号）。
- 长度不能超过50个字符。
- 不能包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明。 此值最多可包含100个字符，并在 marketplace 搜索结果中使用。

### <a name="long-summary"></a>长摘要

提供产品/服务的更详细说明。 此值最多可包含256个字符，并在 marketplace 搜索结果中使用。

### <a name="description"></a>说明

提供产品/服务的更长说明，最多3000个字符。 这会显示给 marketplace 列表概述中的客户。

在说明中包括以下一项或多项：

- 你的产品/服务提供的价值和关键权益
- 类别或行业关联
- 应用内购买机会
- 任何所需的公开

IoT Edge 模块产品/服务必须包括说明底部的最低硬件要求段落。 例如：

*最低硬件要求： Linux x64 和 arm32 OS、1 GB RAM、500 Mb 的存储空间*

下面是有关编写说明的一些提示：

- 在说明的前几个句子中清楚地说明产品/服务的价值。 包括以下项：
    - 套餐的说明。
    - 从产品/服务中受益的用户类型。
    - 客户需求或颁发提供地址。
- 请记住，前几个句子可能会显示在搜索结果中。
- 不要依赖特性和功能来销售产品。 而是专注于你的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于权益的词汇。

若要使您的产品/服务**说明**更具吸引力，请使用富文本编辑器来设置说明格式。 利用富文本编辑器，您可以添加数字、项目符号、粗体、斜体和缩进，使说明更具可读性。

:::image type="content" source="media/text-editor2.png" alt-text="展示了丰富文本编辑器。" border="false":::

- 若要更改内容的格式，请突出显示要设置格式的文本并选择文本样式，如以下屏幕截图所示：

     :::image type="content" source="media/text-editor3.png" alt-text="阐释了丰富文本编辑器中的文本样式控件。" border="false":::

- 若要向文本添加项目符号列表或编号列表，请使用此屏幕截图中显示的选项：
  
    :::image type="content" source="media/text-editor4.png" alt-text="在富文本编辑器中说明项目符号和编号列表控件。" border="false":::

- 若要在文本中添加或删除缩进，请使用此屏幕截图中显示的选项：

    :::image type="content" source="media/text-editor5.png" alt-text="阐释了丰富文本编辑器中的缩进控件。" border="false":::

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的 web 地址。 你需要负责确保你的产品/服务符合隐私法律和法规。 你还负责在你的网站上发布有效的隐私策略。

#### <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 最多可添加25个链接。 若要添加链接，请选择 " **+ 添加链接**"，然后完成以下字段：

- **标题**-客户可以在产品/服务的详细信息页上看到标题。
- **链接（URL）** -输入客户的链接以查看你的联机文档。 链接必须以 http://或 https://开头。

请确保至少添加一个指向文档的链接，以及一个从 [Azure IoT 设备目录](https://catalog.azureiotsolutions.com/)到兼容 IoT Edge 设备的链接。

### <a name="contact-information"></a>联系信息

您必须为**支持联系人**和**工程联系人**提供姓名、电子邮件和电话号码。 此信息不会向客户显示。 它可用于 Microsoft，并可提供给云解决方案提供商（CSP）合作伙伴。

- 支持联系人（必需）：有关一般支持问题。
- 工程联系人（必需）：用于技术问题和认证问题。
- CSP 计划联系人（可选）：适用于与 CSP 计划相关的经销商问题。

在 "**支持联系人**" 部分中，根据产品/服务是否在全球 azure 和/或 azure 政府版中可用，提供合作伙伴可在其中找到对你的产品/服务的支持**的网站的**web 地址。

在**Csp program contact**部分，提供链接（**Csp 计划营销材料**），CSP 合作伙伴可在其中找到产品/服务的营销材料。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

若要了解有关创建产品/服务列表的详细信息，请参阅[产品/服务列表最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

### <a name="marketplace-images"></a>Marketplace 映像

提供要用于产品/服务的徽标和图像。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

>[!Note]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="store-logos"></a>应用商店徽标

在以下四个像素大小中，为产品/服务的徽标提供 .png 文件：

- **小型（48 x 48）**
- **中（90 x 90）**
- **大（216 x 216）**
- **宽（255 x 115）**

所有这四种徽标都是必需的，并用于 marketplace 列表中的不同位置。

#### <a name="screenshots-optional"></a>屏幕快照（可选）

最多可添加五个屏幕截图，其中显示了产品/服务的工作方式。 每个大小必须为 1280 x 720 像素大小和 .png 格式。

#### <a name="videos-optional"></a>视频（可选）

添加最多5个视频来演示产品/服务。 输入视频的名称、其 web 地址以及以 1280 x 720 像素大小显示的视频的缩略图。

#### <a name="offer-examples"></a>优惠示例

以下示例显示了产品/服务列表字段在产品/服务的不同位置中的显示方式。

此屏幕截图显示 Azure Marketplace 中的**产品/服务列表**页。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="介绍 Azure Marketplace 中的产品/服务列表页。":::

此屏幕截图显示 Azure Marketplace 中的搜索结果：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="说明 Azure Marketplace 中的搜索结果。":::

此屏幕截图显示了 "Azure 门户中的**产品/服务列表**页。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="说明 Azure 门户中的产品/服务列表页。":::

此屏幕截图显示 Azure 门户中的搜索结果。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="说明 Azure 门户中的产品/服务列表页。":::

选择 "**保存草稿**"，然后继续阅读下一节 "预览"。

## <a name="preview"></a>预览

在 "**预览" 选项卡**上，可以选择在将产品/服务发布到更广泛的 marketplace 群体之前验证产品/服务的受限制**预览受众**。

> [!IMPORTANT]
> 在预览版中查看产品/服务后，必须选择 "上线" **，将产品**/服务发布到公共产品/服务。

使用 Azure 订阅 ID Guid 指定预览受众，并为每个用户指定可选描述。 这些字段均不能由客户查看。

> [!NOTE]
> 你可以在 "订阅" 页上的 "Azure 门户" 中找到你的 Azure 订阅 ID。

至少添加一个 Azure 订阅 ID （最多10个）或上传一个 CSV 文件（最大为100）。 通过添加这些订阅 Id，你可以定义在 live 发布之前可以预览你的产品/服务的人员。 如果产品/服务已处于活动阶段，则可以定义预览受众来测试产品/服务的更改或更新。

> [!NOTE]
> 预览受众不同于一个专用受众。 **预览**观众可以在市场上生活之前查看和确认所有产品/服务计划，包括仅将发布给**专用**受众的产品/服务（在 "可用性" 选项卡上设置）。

在转到下一节 "计划概述" 之前，请选择 "**保存草稿**"。

### <a name="plan-overview"></a>计划概述

利用此选项卡，你可以在合作伙伴中心的相同产品内提供不同的计划选项。 这些计划以前称为 Sku，也称为 "库存单位"。 计划可能不同于可用的云，如全球云、政府云和计划引用的映像。 若要列出 marketplace 中的产品/服务，必须至少设置一个计划。

创建计划后，"**计划概述**" 选项卡将显示：

- 计划名称
- 定价模型
- 云可用性（全局或政府）
- 当前发布状态
- 任何可用的操作

计划概述中的可用操作取决于计划的当前状态。 其中包括：

- **删除草稿**：如果计划状态为草稿。
- **停止销售计划**：如果计划状态为 "实时"。

#### <a name="create-new-plan"></a>创建新计划

选择 "**创建新计划**"。 此时将显示 "**新建计划**" 对话框。

在 "**计划 id** " 框中，为此产品/服务中的每个计划创建唯一的计划 id。 此 ID 将对产品 web 地址中的客户可见。 仅使用小写字母和数字、短划线或下划线，最多使用50个字符。

在 "**计划名称**" 框中，输入此计划的名称。 客户在确定要在产品中选择的计划时，将看到此名称。 为此产品/服务中的每个计划创建唯一名称。 例如，你可以将**Windows server**的产品/服务名称用于计划**windows Server 2016**和**windows server 2019**。

> [!NOTE]
> 选择 "**创建**" 后，不能更改计划 ID。

选择“创建”。 

### <a name="plan-setup"></a>计划设置

此选项卡允许你配置计划中可用的云。 此选项卡上的答案会影响在其他选项卡上显示的字段。

#### <a name="cloud-availability"></a>云可用性

你的计划必须至少在一个使用 Azure IoT 中心的云中可用。

选择**Azure 全局**选项，以使计划可供使用 marketplace 的所有全球 Azure 区域中的客户使用。 有关详细信息，请参阅[地理可用性和货币支持](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

选择 " [Azure 政府云](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)" 选项，使解决方案显示在此处。 这是一个政府社区云，具有对美国联邦、州、地方或部落政府机构的客户的受控访问权限，以及可为他们提供服务的合作伙伴。 作为发布者，你需要负责此云社区的任何符合性控制、安全措施和最佳实践。 Azure 政府版使用物理上隔离的数据中心和网络（仅限美国）。 在[发布](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)到 Azure 政府版之前，请在该区域中测试并确认你的解决方案，因为结果可能不同。 若要暂存和测试解决方案，请从[Microsoft Azure 政府试用](https://azure.microsoft.com/global-infrastructure/government/request/)版请求试用帐户。

> [!NOTE]
> 在计划发布并在特定云中可用后，无法删除该云。

#### <a name="azure-government-cloud-certifications"></a>Azure 政府云认证

仅当在 "**云可用性**" 下选择了 " **Azure 政府云**" 时，此选项才可见。

Azure 政府服务负责处理特定政府法规和要求的数据。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。 若要对这些程序进行了解，可以提供多达100的链接来描述证书。 这些链接可以直接链接到程序列表，也可以链接到自己的网站。 这些链接仅对 Azure 政府客户可见。

## <a name="plan-listing"></a>计划列表

此选项卡显示同一个产品/服务内每个不同计划的特定信息。

### <a name="plan-name"></a>计划名称

此名称预先填充了创建计划时为其提供的名称。 您可以根据需要更改此名称。 长度最多可为50个字符。 此名称在 Azure Marketplace 中显示为此计划的标题，并 Azure 门户。 在计划准备就绪后，将其用作默认的模块名称。

### <a name="plan-summary"></a>计划摘要

提供计划的简短摘要（而不是产品/服务）。 此摘要显示在 Azure Marketplace 搜索结果中，最多可包含100个字符。

### <a name="plan-description"></a>计划描述

描述此计划的独特之处，以及产品之间的计划之间的差异。 不要描述该产品/服务，而只是计划。 此说明将显示在 Azure Marketplace 中，并显示在 "产品/服务列表" 页上的 "Azure 门户中。 它可以与你在计划摘要中提供的内容相同，并且最多包含2000个字符。

完成这些字段后，选择 "**保存草稿**"。

#### <a name="plan-examples"></a>计划示例

下面的示例说明计划列表字段在不同视图中的显示方式。

查看计划详细信息时，Azure Marketplace 中的字段如下：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="说明在 Azure Marketplace 中查看计划详细信息时看到的字段。":::

下面是有关 Azure 门户的计划详细信息：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="说明 Azure 门户上的计划详细信息。":::

## <a name="availability"></a>可用性

如果要隐藏已发布的产品/服务，使客户无法在 marketplace 中搜索、浏览或购买该产品/服务，请在 "可用性" 选项卡上选中 "**隐藏计划**" 复选框。

此字段通常用于以下情况：

- 此产品/服务仅适用于引用其他应用程序时间接使用。
- 不得单独购买产品/服务。
- 该计划用于初始测试，不再相关。
- 该计划用于临时或季节性产品/服务，不应再提供。

## <a name="technical-configuration"></a>技术配置

**IoT Edge 模块**产品类型是在 IoT Edge 设备上运行的特定类型的容器。 在 "**技术配置**" 选项卡上，你将提供[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)中的容器映像存储库的参考信息，以及允许客户轻松使用该模块的配置设置。

发布产品/服务后，会将 IoT Edge 容器映像复制到特定公共容器注册表中的 Azure Marketplace。 来自 Azure 用户使用模块的所有请求都是从 Azure Marketplace 公共容器注册表提供的，而不是专用容器注册表。

您可以面向多个平台，并使用标记提供模块容器映像的多个版本。 若要了解有关标记和版本的详细信息，请参阅[准备 IoT Edge 模块技术资产](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset)。

### <a name="image-repository-details"></a>映像存储库详细信息

你将在 "**映像存储库详细信息**" 选项卡上提供以下信息。

**选择图像源**：选择 " **Azure 容器注册表**" 选项。

**Azure 订阅 id**：提供报告资源使用情况的订阅 id，并为包含容器映像的 Azure 容器注册表支付服务费用。 可以在 "[订阅" 页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上的 "Azure 门户" 中找到此 ID。

**Azure 资源组名称**：提供包含 Azure 容器注册表和容器映像的[资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)名称。 资源组必须可在订阅 ID （如上所示）中访问。 可以在 "[资源组](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)" 页上的 "Azure 门户" 中找到该名称。

**Azure 容器注册表名称**：提供具有容器映像的[azure 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-intro)的名称。 容器注册表必须存在于你之前提供的 Azure 资源组中。 仅提供注册表名称，而不提供完整的登录服务器名称。 请确保省略名称中的**azurecr.io** 。 可以在 Azure 门户的 "[容器注册表" 页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到注册表名称。

**Azure 容器注册表的管理员用户名**：提供与具有容器映像的 Azure 容器注册表关联的[管理员用户名](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)。 必须提供用户名和密码才能确保公司有权访问注册表。 若要获取管理员用户名和密码，请使用 Azure 命令行接口（CLI）将**启用了管理员**的属性设置为**True** 。 您可以选择将 "**管理员用户**" 设置为在 Azure 门户中**启用**。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="说明 "更新容器注册表" 对话框。":::

**Azure 容器注册表的密码**：提供与 Azure 容器注册表关联的管理员用户名的密码，并具有容器映像。 必须提供用户名和密码才能确保公司有权访问注册表。 可以通过转到**容器注册表** > **访问密钥**或使用[show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)Azure CLI 来从 Azure 门户获取密码。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="说明 Azure 门户中的访问密钥屏幕。":::

**Azure 容器注册表中的存储库名称**。 提供包含映像的 Azure 容器注册表存储库的名称。 将映像推送到注册表时，可以指定存储库的名称。 可以通过转到 "[容器注册表](https://azure.microsoft.com/services/container-registry/) > **存储库" 页**来查找存储库的名称。 有关详细信息，请参阅[在 Azure 门户中查看容器注册表存储库](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)。 请注意，设置名称后，将无法更改该名称。 为帐户中的每个提议使用唯一名称。

### <a name="image-tags-for-new-versions-of-your-offer"></a>新版本的产品/服务的图像标记

发布更新时，客户必须能够在 Azure Marketplace 中自动获取更新。 如果不想更新，则它们必须能够保留在特定版本的映像中。 可以通过在每次对映像进行更新时添加新的图像标记来实现此目的。

**Image 标记**。 此字段必须包含一个**最新**的标记，该标记指向所有受支持平台上的最新版本的映像。 它还必须包含版本标记（例如，从 xx. xx 开始，其中 xx 是数字）。 客户应使用[清单标记](https://github.com/estesp/manifest-tool)以面向多个平台。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 所有清单标记（最新标记除外）必须以 X. Y 或 X. Z 开头（其中 X、Y 和 Z 是整数）。 例如，如果最新的标记指向 arm32，则需要将这六个标记添加到此字段，并将其添加到 arm32 中。 有关标记和版本控制的详细信息，请参阅[准备 IoT Edge 模块技术资产。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)

### <a name="default-deployment-settings-optional"></a>默认部署设置（可选）

定义最常见的设置来部署 IoT Edge 模块。 通过让用户使用这些默认设置来全新启动 IoT Edge 模块，从而优化客户部署。

**默认路由**。 IoT Edge 中心管理模块、IoT 中心和设备之间的通信。 你可以设置模块与 IoT 中心之间的数据输入和输出的路由，这使你可以灵活地在需要时发送消息，而无需其他服务来处理消息或编写其他代码。 路由使用名称/值对进行构造。 最多可以定义五个默认路由名称，每个名称最多可包含512个字符。

请确保在路由值中使用正确的[路由语法](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)（通常定义为从/消息/* 到 $upstream）。 这意味着任何模块发送的任何消息都将发送到 IoT 中心。 若要引用模块，请使用其默认模块名称，它将是你的**产品/服务名称**，不包含空格或特殊字符。 若要引用尚不知道的其他模块，请使用 <FROM_MODULE_NAME> 约定，让客户知道他们需要更新此信息。 有关 IoT Edge 路由的详细信息，请参阅[声明路由](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)。

例如，如果 module ContosoModule 侦听 ContosoInput 上的输入和 ContosoOutput 的输出数据，则定义以下两个默认路由是有意义的：

- 名称 #1： ToContosoModule
- 值 #1：从/messages/modules/<FROM_MODULE_NAME>/outputs/* 转换为 BrokeredEndpoint （"/modules/ContosoModule/inputs/ContosoInput"）
- 名称 #2： FromContosoModuleToCloud
- 值 #2：从/messages/modules/ContonsoModule/outputs/ContosoOutput 到 $upstream

**默认模块克隆所需属性**。 模块克隆是 IoT 中心中存储模块实例的状态信息的 JSON 文档，包括所需属性。 将所需的属性与报告的属性结合使用，可以同步模块配置或条件。 解决方案后端可以设置所需的属性，模块可以读取它们。 该模块还可以接收所需属性中的更改通知。 所需属性是使用最多五个名称/值对创建的，并且每个默认值必须少于512个字符。 最多可以定义五个名称/值为成对出现的属性。 所需的非嵌套属性的值必须是有效的 JSON，无转义，不包含具有四个级别的最大嵌套层次结构的数组。 在默认值所需的参数没有意义的情况下（例如，客户服务器的 IP 地址），可以将参数添加为默认值。 若要详细了解成对出现的所需属性，请参阅[定义或更新所需属性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

例如，如果模块支持使用成对使用的所需属性进行动态配置的刷新速率，则定义以下默认的、需要的默认值的属性是有意义的：

- 名称 #1： RefreshRate
- 值 #1：60

**默认环境变量**。 环境变量为帮助配置过程的模块提供补充信息。 使用名称/值对创建环境变量。 每个默认环境变量名称和值都必须少于512个字符，并且最多可以定义五个字符。 当默认值所需的参数没有意义时（例如，客户服务器的 IP 地址），可以将参数添加为默认值。

例如，如果某个模块在启动之前需要接受使用条款，则你可以定义以下环境变量：

- 名称 #1： ACCEPT_EULA
- 值 #1： Y

**默认容器创建选项**。 容器创建选项用于直接创建 IoT Edge 模块 Docker 容器。 IoT Edge 支持 Docker 引擎 API 创建容器选项。 查看[列表容器](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)中的所有选项。 "创建选项" 字段必须为有效的 JSON、非转义且小于512个字符。

例如，如果模块需要端口绑定，请定义以下 create 选项：

"HostConfig"： {"PortBindings"： {"5012/tcp"： [{"HostPort"： "5012"}]}

## <a name="review-and-publish"></a>查看和发布

完成该产品/服务的所有必需部分后，可以提交它来查看和发布。

在门户的右上角，选择 "**查看并发布**"。

在 "查看" 页上，可以看到发布状态：

- 请参阅产品/服务的每个部分的完成状态。 在产品/服务的所有部分标记为 "已完成" 之前，无法发布。
    - **未启动**-此部分尚未启动，需要完成。
    - **不完整**-部分包含需要修复的错误或需要提供详细信息。 有关指南，请参阅本文档前面的部分。
    - **完成**-部分包含所有必需的数据，并且没有错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保正确测试产品/服务。 同时，提供有助于了解产品/服务的补充说明。

若要提交产品/服务进行发布，请选择 "**发布**"。

我们将向你发送一封电子邮件，告知你该产品/服务的预览版本可供查看和批准。 若要将产品/服务发布到公共产品/服务（如果是专用用户），请访问合作伙伴中心，并选择 "**上线**"。

## <a name="next-steps"></a>后续步骤

- [更新商业应用商店中的现有产品/服务](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)