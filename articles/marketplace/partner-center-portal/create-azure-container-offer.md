---
title: 在合作伙伴中心 - Azure 应用商店中创建 Azure 容器产品/服务
description: 本文介绍如何为 Azure 应用商店创建和发布容器产品/服务。
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266313"
---
# <a name="create-an-azure-container-offer"></a>创建 Azure 容器产品/服务

> [!IMPORTANT]
> 我们将 Azure 容器产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照云合作伙伴门户[容器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer)中的说明来管理您的优惠。

本文介绍如何为 Azure 应用商店创建和发布容器产品/服务。 在开始之前，[在合作伙伴中心创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>创建新产品/服务

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)，然后从顶部菜单中选择**仪表板**。
2. 在左侧菜单中，选择**商业市场**，然后**选择"概述**"。
3. 在 **"概述"** 页上，选择 **+新产品/服务**，然后选择**Azure 容器**。 将显示 **"新建产品/服务**"对话框。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="在合作伙伴中心中说明"概述"页。突出显示"新优惠"按钮和咨询服务优惠。":::

> [!TIP]
> 发布产品/服务后，在合作伙伴中心中对其所做的编辑仅在重新发布产品/服务后显示在网店中。 请确保在进行更改后始终重新发布。

### <a name="offer-id-and-alias"></a>优惠 ID 和别名

输入**优惠 ID**。 这是您帐户中每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务的网站地址和 Azure 资源管理器模板（如果适用）中看到此 ID。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但没有空格，并且限制为 50 个字符。 例如，如果输入**测试-产品/服务 -1，** 则产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`Web 地址将为 。
- 选择 "**创建**"后，无法更改优惠 ID。

**输入****产品/服务别名**。 这是用于指合作伙伴中心产品/服务的名称。

- 此名称不在市场上使用，并且不同于向客户显示的要约名称和其他值。
- 选择 后无法更改此 **。**

选择 **"在**继续之前创建"。

## <a name="offer-overview"></a>产品/服务概述

**"产品/服务概述"** 页显示发布此产品/服务所需的步骤（已完成和即将推出）以及每个步骤应完成多长时间的可视化表示形式。

此页面根据产品/服务的状态显示不同的链接。 例如：

- 如果要约是草稿 -[删除草稿要约](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果优惠是实时的 -[停止销售优惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果优惠处于预览版 -[上线](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成发布者注销 -[取消发布](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>优惠设置

按照以下步骤设置您的产品/服务。

### <a name="connect-lead-management--optional"></a>连接潜在客户管理 = 可选

使用合作伙伴中心将产品/服务发布到市场时，您可以将其连接到客户关系管理 （CRM） 系统。 这样，一旦有人对您的产品表示兴趣或使用，您就可以立即接收客户联系信息。

1. **选择您希望我们发送客户线索的潜在客户目的地**。 合作伙伴中心支持以下 CRM 系统：

- [面向客户互动的动态 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> 如果上面未列出 CRM 系统，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户潜在顾客数据，然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布时，请将您的产品/服务连接到潜在顾客目标。
3. 确认与潜在顾客目标的连接配置正确。 在合作伙伴中心发布后，我们将验证连接并向您发送测试线索。 在产品/服务上线之前预览产品/服务时，还可以尝试在预览环境中自行购买产品/服务，以测试潜在顾客连接。
4. 确保与潜在顾客目标的连接保持更新状态，这样您就不会丢失任何潜在顾客。

下面是一些额外的潜在客户管理资源：

- [潜在客户管理概述](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF（确保弹出窗口阻止程序已关闭）

选择 **"保存"草稿**，然后再继续下一节"属性"。

### <a name="properties"></a>属性

此页面允许您定义用于在市场上对产品/服务进行分组的类别以及支持您的优惠的法律合同。

#### <a name="category"></a>类别

选择至少 1 个类别，最多选择五个类别。 这些类别用于将产品/服务放置在相应的市场搜索区域，并显示在您的优惠详细信息页面上。 在产品/服务说明中，说明您的优惠如何支持这些类别。 容器显示在 **"容器**"下，然后是 **"容器图像**"类别。

#### <a name="legal"></a>合法

您必须为优惠提供条款和条件。 存在两个选项：

- 使用 Microsoft 商业市场的标准合约。
- 提供您自己的条款和条件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微软商业市场的标准合同

我们提供标准合同模板，以帮助促进商业市场的交易。 您可以选择根据标准合同提供解决方案，客户只需检查和接受一次。 如果您不想创建自定义条款和条件，这是一个不错的选择。

要了解有关标准合同的更多信息，请参阅[Microsoft 商业市场的标准合约](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您还可以下载[标准合约](https://go.microsoft.com/fwlink/?linkid=2041178)PDF（确保弹出窗口阻止程序已关闭）。

要使用标准合约，请选择"**使用 Microsoft 商业市场的标准合约**"复选框，然后单击"**接受**"。

> [!NOTE]
> 使用 Microsoft 商业市场的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 在标准合同下或根据您自己的条款和条件提供您的解决方案。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="演示了"使用标准合同进行微软商业市场"复选框。":::

##### <a name="your-own-terms-and-conditions"></a>您自己的条款和条件

要提供您自己的自定义条款和条件，请在 **"条款和条件"** 框中输入这些条款和条件。 您可以在此框中输入无限量的文本字符。 客户必须先接受这些条款，然后才能试用您的优惠。

选择 **"保存"草稿**，然后再继续下一节"优惠列表"。

## <a name="offer-listing"></a>优惠列表

此页面允许您定义在商业市场中显示的产品/服务详细信息。 这包括产品/服务名称、说明和图像。

> [!NOTE]
> 如果优惠说明以短语开头，则优惠详细信息不需要以英文表示："此应用程序仅提供 [非英语语言]。 提供有用链接以不同于产品/服务列表详细信息中使用的语言提供内容也没关系。

### <a name="name"></a>名称

您在此处输入的名称显示为产品/服务的标题。 此字段预先填充您在创建产品/服务时在 **"产品/服务"别名**框中输入的文本。 可以稍后更改此名称。

名称：

- 可以注册商标（可能包括商标和版权符号）。
- 不能超过50个字符长。
- 不能包括表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

您的报价的简短说明。 这最多可达 100 个字符，并且用于市场搜索结果。

### <a name="long-summary"></a>长摘要

更详细说明您的优惠。 这最多可达 256 个字符，并且用于市场搜索结果。

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
  - 从产品/服务中受益的用户类型
  - 客户需求或发出产品/服务地址。
- 请记住，搜索结果中可能会显示前几个句子。
- 不要依赖特性和功能来销售您的产品。 相反，关注您的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于利益的措辞。

为了使您的报价**描述**更具吸引力，请使用丰富的文本编辑器来设置说明的格式。 具有编号、项目符号、粗体、斜体和缩进，使描述更具可读性。

:::image type="content" source="media/text-editor2.png" alt-text="说明富文本编辑器。" border="false" :::

- 使用此下拉列表将段落样式应用于文本。

    :::image type="content" source="media/text-editor3.png" alt-text="说明富文本编辑器中的文本样式控件。" border="false":::

- 使用这些图标将编号或项目符号应用于文本。

     :::image type="content" source="media/text-editor4.png" alt-text="在富文本编辑器中说明项目符号和数字列表控件。" border="false":::

- 使用这些图标向文本添加或删除缩进或从文本中删除缩进。

    :::image type="content" source="media/text-editor5.png" alt-text="说明富文本编辑器中的缩进控件。" border="false":::

#### <a name="privacy-policy-link"></a>隐私政策链接

输入组织的隐私政策的网址。 您有责任确保您的报价符合隐私法律和法规。 您还负责在您的网站上发布有效的隐私政策。

#### <a name="useful-links"></a>有用链接

提供有关您的优惠的补充在线文档。 您最多可以添加 25 个链接。 要添加链接，请选择 **" 添加链接"，** 然后完成以下字段：

- **标题**– 客户将在您产品/服务的详细信息页面上看到此内容。
- **链接 （URL）** – 输入客户查看联机文档的链接。 链接必须以http://或https://开头。

### <a name="contact-information"></a>联系信息

您必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但 Microsoft 可以获取此信息。 它还可能提供给云解决方案提供商 （CSP） 合作伙伴。

- 支持联系人（必需）：有关一般支持问题。
- 工程联系人（必需）：有关技术问题和认证问题。
- CSP 计划联系人（可选）：对于与 CSP 计划相关的经销商问题。

在 **"支持"联系人**部分中，提供**支持网站**，合作伙伴可以根据产品/服务是全局 Azure、Azure 政府版还是两者提供，查找对产品/服务的支持。

在**CSP 计划联系**部分，提供链接 （ **CSP 计划营销材料**）， 其中 CSP 合作伙伴可以找到您的报价的营销材料.

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

要了解有关创建产品/服务列表详细信息，请参阅[产品/服务列表最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>市场映像

提供徽标和图像，以便与您的产品配合使用。 所有图像必须采用 PNG 格式。 模糊图像将被拒绝。

#### <a name="store-logos"></a>应用商店徽标

 在以下四个像素大小中提供产品徽标的 PNG 文件：

- **小**（48 x 48）
- **中等**（90 X 90）
- **大**（216 x 216）
- **宽**（255 X 115）

所有四个徽标都是必需的，并且在市场列表中的不同位置使用。

#### <a name="screenshots-optional"></a>屏幕截图（可选）

最多添加五个屏幕截图，显示您的优惠如何工作。 每个必须为 1280 x 720 像素的大小和 PNG 格式。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个视频，演示您的优惠。 输入视频的名称、Web 地址以及视频的缩略图 PNG 图像，大小为 1280 x 720 像素。

#### <a name="offer-examples"></a>优惠示例

以下示例显示了要约列表字段如何显示在要约的不同位置。

这显示了 Azure 应用商店中的 **"产品/服务"列表**页：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="在 Azure 应用商店中说明产品/服务列表页。" :::

这显示了 Azure 应用商店中的搜索结果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="在 Azure 应用商店中说明搜索结果。":::

这显示了 Azure 门户中的 **"产品/服务"列表**页：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="在 Azure 门户中说明产品/服务列表页。":::

这显示了 Azure 门户中的搜索结果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="在 Azure 门户中说明搜索结果。":::

## <a name="preview"></a>预览

在"预览"选项卡上，您可以选择有限的**预览受众**，以在实时发布产品/服务之前验证产品/服务。

> [!IMPORTANT]
> 在**预览**版中查看优惠后，必须选择 **"上线"** 以向公众发布您的优惠。

使用 Azure 订阅 ID GUID 指定预览受众，以及每个受众的可选说明。 客户看不到这两个字段。

> [!NOTE]
> 您可以在 Azure 门户中的"订阅"页上找到 Azure 订阅 ID。

添加至少一个 Azure 订阅 ID，分别（最多 10 个）或通过上传 CSV 文件（最多 100 个）。 通过添加这些订阅 IT，您可以确定谁可以在产品/服务实时发布之前预览产品/服务。 如果您的优惠已实时，您可以选择预览受众以测试产品/服务的变化或更新。

> [!NOTE]
> 预览观众与私人观众不同。 **预览**受众可以在所有优惠计划在市场上直播之前查看和确认这些计划，包括仅向**私人**受众发布的计划（在"可用性"选项卡上设置）。

选择 **"保存"草稿**，然后再继续。

### <a name="plan-overview"></a>计划概述

此选项卡允许您在同一产品/服务中提供不同的计划选项。 这些计划以前称为 SKU 或库存单位。 计划在可用云方面可能有所不同，例如全局云、政府云和计划引用的图像。 要在商业市场列出您的优惠，您必须至少设置一个计划。

创建计划后，"**计划概览**"选项卡显示：

- 计划名称
- 定价模型
- 云可用性（全球或政府）
- 当前发布状态
- 任何可用操作

计划概述中可用的操作因计划的当前状态而异。 其中包括：

- **删除草稿**= 如果计划状态为草稿。
- **停止销售计划**– 如果计划状态实时发布。

#### <a name="create-new-plan"></a>创建新计划

选择 **"创建新计划**"。 将显示"**新建计划**"对话框。

在 **"计划 ID"** 框中，为此产品/服务中的每个计划创建一个唯一的计划标识符。 此 ID 将在产品的 Web 地址中对客户可见。 仅使用小写字母和数字、破折号或下划线，最多 50 个字符。

> [!NOTE]
> 选择 "**创建**"后，无法更改计划 ID。

在 **"计划名称**"框中，输入此计划的名称。 客户在决定在您的产品/服务中选择哪个计划时，会看到此名称。 为此产品/服务中的每个计划创建唯一名称。 例如，您可以使用**Windows 服务器**的报价名称，并计划 Windows**服务器 2016**和**Windows 服务器 2019**。

### <a name="plan-setup"></a>计划设置

此选项卡允许您选择计划可用的云。 此选项卡上的答案会影响其他选项卡上显示的字段。

#### <a name="cloud-availability"></a>云可用性

您的计划必须在至少一个云中可用。

选择**Azure 全局**选项，以便使用商业市场的所有全局 Azure 区域的客户可以使用您的计划。 有关详细信息，请参阅[地理可用性和货币支持](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

选择[**Azure 政府云**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)选项以使解决方案在此处显示。 这是一个政府社区云，由来自美国联邦、州、地方或部落政府机构的客户以及有资格为其提供服务的合作伙伴进行受控访问。 作为发布者，您负责此云社区的任何合规性控制、安全措施和最佳实践。 Azure 政府使用物理隔离的数据中心和网络（仅适用于美国）。

在[发布到](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)Azure 政府之前，请测试并确认解决方案，因为结果可能会有所不同。 要创建和测试解决方案，请从[Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)请求试用帐户。

> [!NOTE]
> 计划发布并在特定云中可用后，无法删除该云。

#### <a name="azure-government-cloud-certifications"></a>Azure 政府云认证

仅当 Azure**政府云**在**云可用性**下被选中时，才能看到此选项。

Azure 政府服务处理受某些政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。

要显示这些计划的认证，您最多可以提供 100 个描述这些程序的链接。 这些可以是直接链接到您的程序列表或您自己的网站。 这些链接仅对 Azure 政府客户可见。

## <a name="plan-listing"></a>计划列表

此选项卡显示当前产品/服务中每个不同计划的特定信息。

### <a name="plan-name"></a>计划名称

这预先填充了您在创建计划时给出的名称。 您可以根据需要更改此名称。 它可以长达 50 个字符。 此名称在 Azure 应用商店和 Azure 门户中显示为此计划的标题。 在计划准备好使用后，它用作默认模块名称。

### <a name="plan-summary"></a>计划摘要

软件计划的简短摘要（不是产品/服务）。 此摘要显示在 Azure 应用商店搜索结果中，最多可以包含 100 个字符。

### <a name="plan-description"></a>计划描述

描述是什么让此软件计划独一无二，以及您产品/服务中计划之间的差异。 不要描述这个提议，只是计划。 此说明将显示在 Azure 应用商店和 **"产品/服务"列表**页上的 Azure 门户中。 它可以与您在计划摘要中提供的内容相同，并包含最多 2，000 个字符。

完成这些字段后选择 **"保存**"。

#### <a name="plan-examples"></a>计划示例

以下示例显示了计划列表字段如何出现在不同的视图中。

查看计划详细信息时，Azure 应用商店中的字段：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="说明在 Azure 应用商店中查看计划详细信息时看到的字段。":::

以下是 Azure 门户上的计划详细信息：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="在 Azure 门户上说明计划详细信息。":::

## <a name="plan-availability"></a>计划可用性

如果要隐藏已发布的产品/服务，以便客户无法在市场上搜索、浏览或购买，请在 **"可用性"** 选项卡上选中"**隐藏计划**"复选框。

此字段用于：

- 当通过另一个应用程序引用此要约时，该产品/服务将间接使用。
- 报价不应单独购买。
- 该计划用于初始测试，不再相关。
- 该计划用于临时或季节性优惠，不应再提供。

## <a name="technical-configuration"></a>技术配置

容器映像必须托管在私有的[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)中。 在 **"技术配置"** 选项卡上，为 Azure 容器注册表中的容器映像存储库提供参考信息。

发布产品/服务后，容器映像将复制到特定公共容器注册表中的 Azure 应用商店。 使用容器映像的所有请求都来自 Azure 应用商店公共容器注册表，而不是私有注册表。 有关详细信息，请参阅[准备 Azure 容器技术资产](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

### <a name="image-repository-details"></a>图像存储库详细信息

在 **"映像存储库详细信息**"选项卡上提供以下信息。

**Azure 订阅 ID** – 提供订阅 ID，其中报告使用情况，并为包含容器映像的 Azure 容器注册表计费服务。 您可以在 Azure 门户中的["订阅"页上](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)找到此 ID。

**Azure 资源组名称**– 提供包含容器映像的 Azure 容器注册表[的资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)名称。 必须在订阅 ID（上图）中访问资源组。 您可以在 Azure 门户中的["资源组"](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)页上找到该名称。

**Azure 容器注册表名称**– 提供具有容器映像的[Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-intro)的名称。 容器注册表必须位于之前提供的 Azure 资源组中。 仅包括注册表项，不包括完整的登录服务器名称。 请务必省略名称**中azurecr.io。** 您可以在 Azure 门户中的[容器注册表页上](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)找到注册表项。

**Azure 容器注册表的管理员用户名**– 提供与具有容器映像的 Azure 容器注册表链接的[管理员用户名](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account)。 需要用户名和密码来确保您的公司有权访问注册表。 要获取管理员用户名和密码，请使用 Azure 命令行接口 （CLI） 将**启用管理员**的属性设置为**True。** 可以选择将**管理员用户**设置为在 Azure 门户中**启用**。

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="演示"更新容器注册表"对话框。":::

**Azure 容器注册表的密码**– 提供与 Azure 容器注册表关联的管理员用户名的密码，并具有容器映像。 需要用户名和密码来确保您的公司有权访问注册表。 您可以通过使用[show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)访问**容器注册表** > **访问密钥**或使用 Azure CLI 从 Azure 门户获取密码。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="说明访问键菜单。":::

**Azure 容器注册表中的存储库名称**。 提供具有映像的 Azure 容器注册表存储库的名称。 将映像推送到注册表时，包括存储库的名称。 您可以通过访问[容器注册表](https://azure.microsoft.com/services/container-registry/) > **存储库**页来查找存储库的名称。 有关详细信息，请参阅在[Azure 门户中查看容器注册表存储库](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)。

> [!NOTE]
> 设置名称后，无法更改它。 对帐户中的每个产品/服务使用唯一名称。

### <a name="image-tags-for-new-versions-of-your-offer"></a>产品/服务新版本的图像标签

发布更新时，客户必须能够自动从 Azure 应用商店获取更新。 如果他们不想更新，他们必须能够保留您的映像的特定版本。 通过在每次更新图像时添加新图像标记来执行此操作。

### <a name="image-tag"></a>映像标记

此字段必须包含指向所有受支持平台上映像的最新版本**的最新**标记。 它还必须包含一个版本标记（例如，从 xx.xx.xx 开始，其中 xx 是数字）。 客户应使用[清单标记](https://github.com/estesp/manifest-tool)来定位多个平台。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。

所有清单标记（最新标记除外）必须以 X.Y**-** 或 X.Y.Z 开头，其中 X、Y 和 Z 是整数。 例如，如果**最新的**标记指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32，则需要将这六个标记添加到此字段中。 有关详细信息，请参阅[准备 Azure 容器技术资产](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 请记得向你的映像添加一个测试标记，以便在测试期间可以标识该映像。

## <a name="review-and-publish"></a>审核和发布

完成产品/服务所需的所有部分后，可以将其提交以供审核和发布。

在门户的右上角，选择 **"审阅"并****发布**。

在评论页面上，您可以：

- 请参阅产品/服务的每个部分的完成状态。 在产品/服务的所有部分都标记为完整之前，您无法发布。
  - **尚未启动**= 尚未启动，需要完成。
  - **不完整**– 有需要修复或要求您提供详细信息的错误。 有关帮助，请参阅本文档前面的部分。
  - **完成**= 包括所有必需的数据，没有错误。 优惠的所有部分必须完成，然后才能提交报价。
- 向认证团队提供测试说明，以确保您的产品/服务测试正确。 此外，提供有助于了解您的报价的任何补充说明。

要提交发布要约，请选择 **"发布**"。

我们将向您发送一封电子邮件，告知您产品/服务预览版本何时可供审核和批准。

要向公众发布您的优惠（或者如果私人优惠，则向私人受众），请转到合作伙伴中心并选择 **"Go-live**"。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
