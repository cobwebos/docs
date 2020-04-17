---
title: 使用合作伙伴中心创建 Azure IoT 边缘模块产品 /Azure 应用商店
description: 了解如何使用合作伙伴中心在 Azure 应用商店中创建 IoT 边缘模块产品
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6b56b748ef31bcfd33893e55d3ea5f8d9851a3ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674470"
---
# <a name="create-an-iot-edge-module-offer"></a>创建 IoT Edge 模块产品/服务

> [!IMPORTANT]
> 我们将 IoT Edge 模块产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照 IoT Edge 模块中的说明操作，为云合作伙伴门户[提供发布概述](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts)来管理您的产品/服务。

本文介绍如何为 Azure 应用商店创建和发布物联网 （IoT） 边缘模块产品。

在创建 IoT Edge 模块产品产品之前，必须在合作伙伴中心中具有商业市场帐户。 如果您尚未创建一个帐户，请参阅[在合作伙伴中心创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到合作伙伴中心。
2. 在左侧导航菜单中，选择**商业市场** > **概述**。

    ![说明左导航菜单。](./media/cs-menu-overview.png)

3. 选择 **= 新产品 IoT** > **边缘模块**。 将显示 **"新建产品/服务**"对话框。

> [!IMPORTANT]
> 发布产品/服务后，在合作伙伴中心中对其所做的编辑仅在重新发布产品/服务后显示在网店中。 请确保在进行更改后始终重新发布。

### <a name="offer-id-and-alias"></a>优惠 ID 和别名

输入**优惠 ID**。 这是您帐户中每个产品/服务的唯一标识符。

- 此 ID 在市场产品/服务的网站地址和 Azure 资源管理器模板（如果适用）中可见。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但没有空格，并且限制为 50 个字符。 例如，如果输入**测试-产品/服务 -1，** 则产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`Web 地址将为 。
- 选择"创建"后，无法更改优惠 ID。

输入**产品/服务别名**。 这是用于指合作伙伴中心产品/服务的名称。

- 此名称不在市场上使用，并且不同于向客户显示的要约名称和其他值。
- 选择 后无法更改此 **。**

输入这两个值后，选择 **"先创建"，** 然后再继续下一页"产品/服务概览"。

## <a name="offer-overview"></a>产品/服务概述

**"产品/服务概述"** 页显示发布此产品/服务所需的步骤（已完成和即将推出）以及每个步骤应完成多长时间的可视化表示形式。

此页包含基于您做出的选择对此产品/服务执行操作的链接。 例如：

- 如果要约是草稿 -[删除草稿要约](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果优惠是实时的 -[停止销售优惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果优惠处于预览版 -[上线](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成发布者注销 -[取消发布。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>优惠设置

按照以下步骤设置您的产品/服务。

### <a name="connect-lead-management"></a>连接潜在客户管理

使用合作伙伴中心将产品/服务发布到市场时，您可以选择将其连接到客户关系管理 （CRM） 系统。 这样，一旦有人对您的产品表示兴趣或使用，您就可以立即接收客户联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

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
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)PDF（确保弹出窗口阻止程序已关闭）。

选择 **"保存"草稿**，然后再继续下一节"属性"。

### <a name="properties"></a>属性

此页面允许您定义用于在市场上对产品/服务进行分组的类别以及支持您的优惠的法律合同。

#### <a name="category"></a>类别

选择至少 1 个类别，最多选择五个类别。 这些类别用于将您的产品/服务放入相应的市场搜索区域，并显示在您的优惠详细信息页面上。 在产品/服务说明中，说明您的优惠如何支持这些类别。 在浏览页面中，所有 IoT Edge 模块都显示在 **物联网> IoT Edge 模块** 类别下。

#### <a name="legal"></a>合法

您必须为优惠提供条款和条件。 可以使用两个选项：

- 使用 Microsoft 商业市场的标准合约。
- 提供您自己的条款和条件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微软商业市场的标准合同

我们提供标准合同模板，以帮助促进商业市场的交易。 您可以选择根据标准合同提供解决方案，客户只需检查和接受一次。 如果您不想制作自定义条款和条件，这是一个不错的选择。

要了解有关标准合同的信息，请参阅[Microsoft 商业市场的标准合同](https://docs.microsoft.com/azure/marketplace/standard-contract)。 您还可以下载[标准合约](https://go.microsoft.com/fwlink/?linkid=2041178)PDF（确保弹出窗口阻止程序已关闭）。

要使用标准合约，请选择"**使用 Microsoft 商业市场的标准合约**"复选框，然后单击"**接受**"。

> [!NOTE]
> 使用 Microsoft 商业市场的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 在标准合同下或根据您自己的条款和条件提供您的解决方案。

![使用 Microsoft 商业市场的标准合同复选框进行说明。](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>您自己的条款和条件

要提供您自己的自定义条款和条件，请在 **"条款和条件"** 框中输入这些条款和条件。 您可以在此框中输入无限量的文本字符。 客户必须先接受这些条款，然后才能试用您的优惠。

选择 **"保存"草稿**，然后再继续下一节"优惠列表"。

## <a name="offer-listing"></a>优惠列表

在这里，您将定义市场上显示的报价详细信息。 这包括产品/服务名称、说明、图像等。 在配置此产品/服务时，请务必遵循 Microsoft 策略页上详述的策略。

> [!NOTE]
> 如果要约说明以短语开头，"此应用程序仅以[非英语语言]提供，则优惠详细信息不需要以英文表示。 提供有用链接以不同于产品/服务列表详细信息中使用的语言提供内容也没关系。

### <a name="name"></a>名称

您在此处输入的名称显示为产品/服务的标题。 此字段预先填充您在创建产品/服务时在 **"产品/服务"别名**框中输入的文本。 可以稍后更改此名称。

名称：

- 可以注册商标（您可以包括商标或版权符号）。
- 不能超过50个字符长。
- 不能包括表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供您的优惠的简短说明。 这最多可达 100 个字符，并且用于市场搜索结果。

### <a name="long-summary"></a>长摘要

提供更详细说明您的优惠。 这最多可达 256 个字符，并且用于市场搜索结果。

### <a name="description"></a>说明

提供较长范围的报价说明，最多 3，000 个字符。 这在市场列表概述中向客户显示。

在描述中包括以下一项或多项：

- 您的产品提供的价值和关键优势
- 类别或行业协会，或两者
- 应用内购买机会
- 任何必需的披露

IoT Edge 模块产品必须在说明底部包含最低硬件要求段落。 例如：

*最低硬件要求：Linux x64 和 arm32 操作系统、1 GB RAM、500 Mb 存储*

以下是编写说明的一些提示：

- 清楚地描述你描述前几句的报价的价值。 包括以下项目：
    - 套餐的说明。
    - 从产品/服务中受益的用户类型。
    - 客户需求或发出产品/服务地址。
- 请记住，搜索结果中可能会显示前几个句子。
- 不要依赖特性和功能来销售您的产品。 相反，关注您的产品/服务提供的价值。
- 尝试使用行业特定的词汇或基于利益的措辞。

为了使您的产品/**服务更具**吸引力，请使用丰富的文本编辑器来设置说明的格式。 富文本编辑器允许您添加数字、项目符号、粗体、斜体和缩进，以使描述更具可读性。

:::image type="content" source="media/text-editor2.png" alt-text="说明富文本编辑器。" border="false":::

- 要更改内容的格式，请突出显示要设置格式的文本并选择文本样式，如以下屏幕截图所示：

     :::image type="content" source="media/text-editor3.png" alt-text="说明富文本编辑器中的文本样式控件。" border="false":::

- 要向文本添加项目符号或编号列表，请使用以下屏幕截图中显示的选项：
  
    :::image type="content" source="media/text-editor4.png" alt-text="在富文本编辑器中说明项目符号和数字列表控件。" border="false":::

- 要向文本添加或删除缩进，请使用以下屏幕截图中显示的选项：

    :::image type="content" source="media/text-editor5.png" alt-text="说明富文本编辑器中的缩进控件。" border="false":::

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私政策的网址。 您有责任确保您的报价符合隐私法律和法规。 您还负责在您的网站上发布有效的隐私政策。

#### <a name="useful-links"></a>有用链接

提供有关您的优惠的补充在线文档。 您最多可以添加 25 个链接。 要添加链接，请选择 **" 添加链接"，** 然后完成以下字段：

- **标题**- 客户将在优惠的详细信息页面上看到标题。
- **链接 （URL）** - 输入客户查看联机文档的链接。 链接必须以http://或https://开头。

请确保至少向文档添加一个链接，并从 [Azure IoT 设备目录中](https://catalog.azureiotsolutions.com/)添加一个指向兼容 IoT Edge 设备的链接。

### <a name="contact-information"></a>联系信息

您必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示。 它可供 Microsoft 使用，并可能提供给云解决方案提供商 （CSP） 合作伙伴。

- 支持联系人（必需）：有关一般支持问题。
- 工程联系人（必需）：有关技术问题和认证问题。
- CSP 计划联系人（可选）：对于与 CSP 计划相关的经销商问题。

在 **"支持"联系人**部分中，提供**支持网站的**Web 地址，合作伙伴可以根据产品/服务在全球 Azure、Azure 政府版或两者中是否可用来查找产品/服务的支持。

在**CSP 计划联系**部分，提供链接 （**CSP 计划营销材料**）， 其中 CSP 合作伙伴可以找到您的报价的营销材料.

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

要了解有关创建优惠列表的更多信息，请参阅[产品/服务列表最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

### <a name="marketplace-images"></a>市场映像

提供徽标和图像，以便与您的产品配合使用。 所有图像必须采用 .png 格式。 模糊图像将被拒绝。

#### <a name="store-logos"></a>应用商店徽标

在以下四个像素大小中提供产品徽标的 .png 文件：

- **小 （48 x 48）**
- **中等 （90 x 90）**
- **大 （216 x 216）**
- **宽 （255 x 115）**

所有四个徽标都是必需的，并且在市场列表中的不同位置使用。

#### <a name="screenshots-optional"></a>屏幕截图（可选）

最多添加五个屏幕截图，显示您的优惠如何工作。 每个像素的大小和 .png 格式必须为 1280 x 720 像素。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个视频，演示您的优惠。 输入视频的名称、Web 地址和视频的缩略图 .png 图像，大小为 1280 x 720 像素。

#### <a name="offer-examples"></a>优惠示例

以下示例显示了要约列表字段如何显示在要约的不同位置。

此屏幕截图显示 Azure 应用商店中的 **"产品/服务"列表**页。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="在 Azure 应用商店中说明产品/服务列表页。":::

此屏幕截图显示了 Azure 应用商店中的搜索结果：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="在 Azure 应用商店中说明搜索结果。":::

此屏幕截图显示 Azure 门户中的 **"产品/服务"列表**页。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="在 Azure 门户中说明产品/服务列表页。":::

此屏幕截图在 Azure 门户中显示搜索结果。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="在 Azure 门户中说明产品/服务列表页。":::

在继续下一节"预览"之前选择 **"保存草稿**"。

## <a name="preview"></a>预览

在 **"预览"选项卡**上，您可以选择有限的**预览受众**以验证您的优惠，然后再将其实时发布到更广泛的市场受众。

> [!IMPORTANT]
> 在预览版中查看优惠后，必须选择 **"上线"** 以向公众发布您的优惠。

使用 Azure 订阅 ID GUID 指定预览受众，以及每个受众的可选说明。 客户看不到这两个字段。

> [!NOTE]
> 您可以在 Azure 门户中的"订阅"页上找到 Azure 订阅 ID。

添加至少一个 Azure 订阅 ID，分别（最多 10 个）或通过上传 CSV 文件（最多 100 个）。 通过添加这些订阅 IT，您可以定义谁可以在产品/服务实时发布之前预览产品/服务。 如果您的优惠已是实时的，则可以定义预览受众以测试产品/服务的变化或更新。

> [!NOTE]
> 预览观众与私人观众不同。 **预览**受众可以在所有优惠计划在市场上直播之前查看和确认这些计划，包括仅向**私人**受众发布的计划（在"可用性"选项卡上设置）。

在继续下一节"计划概述"之前选择 **"保存草稿**"。

### <a name="plan-overview"></a>计划概述

此选项卡允许您在合作伙伴中心的同一优惠中提供不同的计划选项。 这些计划以前称为 SKU 或库存单位。 计划在可用云方面可能有所不同，例如全局云、政府云和计划引用的图像。 要在市场上列出您的产品/服务，您必须至少设置一个计划。

创建计划后，"**计划概览**"选项卡显示：

- 计划名称
- 定价模型
- 云可用性（全球或政府）
- 当前发布状态
- 任何可用操作

计划概述中可用的操作因计划的当前状态而异。 其中包括：

- **删除草稿**：如果计划状态为草稿。
- **停止销售计划**：如果计划状态实时发布。

#### <a name="create-new-plan"></a>创建新计划

选择 **"创建新计划**"。 将显示"**新建计划**"对话框。

在 **"计划 ID"** 框中，为此产品/服务中的每个计划创建唯一的计划 ID。 此 ID 将在产品 Web 地址中对客户可见。 仅使用小写字母和数字、破折号或下划线，最多 50 个字符。

在 **"计划名称**"框中，输入此计划的名称。 客户在决定在您的产品/服务中选择哪个计划时，会看到此名称。 为此产品/服务中的每个计划创建唯一名称。 例如，您可以使用**Windows 服务器**的报价名称，并计划 Windows**服务器 2016**和**Windows 服务器 2019**。

> [!NOTE]
> 选择 "**创建**"后，无法更改计划 ID。

选择“创建”  。

### <a name="plan-setup"></a>计划设置

此选项卡允许您配置计划可用的云。 此选项卡上的答案会影响其他选项卡上显示的字段。

#### <a name="cloud-availability"></a>云可用性

使用 Azure IoT 中心，您的计划必须在至少一个云中可用。

选择**Azure 全局**选项，以便使用市场的所有全局 Azure 区域的客户可以使用计划。 有关详细信息，请参阅[地理可用性和货币支持](https://aka.ms/AzureGovCurrencies)。

选择[Azure 政府云](https://aka.ms/WhatIsAzureGovernment)选项以使解决方案在此处显示。 这是一个政府社区云，由来自美国联邦、州、地方或部落政府机构的客户以及有资格为其提供服务的合作伙伴进行受控访问。 作为发布者，您负责此云社区的任何合规性控制、安全措施和最佳实践。 Azure 政府使用物理隔离的数据中心和网络（仅适用于美国）。 在[发布到](https://aka.ms/azuregovpublish)Azure 政府之前，请测试并确认解决方案，因为结果可能会有所不同。 要暂示和测试解决方案，请从 Microsoft [Azure 政府试用版](https://aka.ms/AzureGovernmentTrial)请求试用帐户。

> [!NOTE]
> 计划发布并在特定云中可用后，无法删除该云。

#### <a name="azure-government-cloud-certifications"></a>Azure 政府云认证

仅当在**云可用性**下选择**Azure 政府云**时，此选项才可见。

Azure 政府服务处理受某些政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。 为了提高您对这些计划的认证的认识，您最多可以提供 100 个描述您的认证的链接。 这些可以是直接链接到您的程序列表或您自己的网站。 这些链接仅对 Azure 政府客户可见。

## <a name="plan-listing"></a>计划列表

此选项卡显示同一产品/服务内每个不同计划的特定信息。

### <a name="plan-name"></a>计划名称

这预先填充了您在创建计划时给出的名称。 您可以根据需要更改此名称。 它可以长达 50 个字符。 此名称在 Azure 应用商店和 Azure 门户中显示为此计划的标题。 在计划准备好使用后，它用作默认模块名称。

### <a name="plan-summary"></a>计划摘要

提供您的计划（而不是报价）的简短摘要。 此摘要显示在 Azure 应用商店搜索结果中，最多可以包含 100 个字符。

### <a name="plan-description"></a>计划描述

描述此计划的独特之处，以及优惠内计划之间的差异。 不要描述这个提议，只是计划。 此说明将显示在 Azure 应用商店和"产品/服务"列表页上的 Azure 门户中。 它可以与您在计划摘要中提供的内容相同，并包含最多 2，000 个字符。

完成这些字段后选择 **"保存草稿**"。

#### <a name="plan-examples"></a>计划示例

以下示例显示了计划列表字段如何出现在不同的视图中。

查看计划详细信息时，Azure 应用商店中的字段：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="说明在 Azure 应用商店中查看计划详细信息时看到的字段。":::

以下是 Azure 门户上的计划详细信息：

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="在 Azure 门户上说明计划详细信息。":::

## <a name="availability"></a>可用性

如果要隐藏已发布的产品/服务，以便客户无法在市场上搜索、浏览或购买，请在"可用性"选项卡上选中"**隐藏计划**"复选框。

此字段通常用于：

- 此优惠仅在另一个应用程序引用时间接使用。
- 报价不应单独购买。
- 该计划用于初始测试，不再相关。
- 该计划用于临时或季节性优惠，不应再提供。

## <a name="technical-configuration"></a>技术配置

**IoT Edge 模块**提供类型是在 IoT Edge 设备上运行的特定类型的容器。 在 **"技术配置**"选项卡上，您将在[Azure 容器注册表](https://aka.ms/ContainerRegistry)内为容器映像存储库提供参考信息，以及允许客户轻松使用模块的配置设置。

发布产品/服务后，IoT Edge 容器映像将复制到特定公共容器注册表中的 Azure 应用商店。 Azure 用户使用模块的所有请求都来自 Azure 应用商店公共容器注册表，而不是专用容器注册表。

您可以使用标记定位多个平台并提供多个版本的模块容器映像。 要了解有关标记和版本控制的更多信息，请参阅[准备 IoT Edge 模块技术资产](https://aka.ms/AzureIoTTechAsset)。

### <a name="image-repository-details"></a>图像存储库详细信息

您将在 **"映像存储库详细信息**"选项卡上提供以下信息。

**选择映像源**：选择**Azure 容器注册表**选项。

**Azure 订阅 ID**：提供订阅 ID，其中报告资源使用情况，并为包含容器映像的 Azure 容器注册表计费服务。 您可以在 Azure 门户中的["订阅"页上](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)找到此 ID。

**Azure 资源组名称**：提供包含容器映像的 Azure 容器注册表[的资源组](https://aka.ms/ResourceManagerAzurePortal)名称。 必须在订阅 ID（上图）中访问资源组。 您可以在 Azure 门户中的["资源组"](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)页上找到该名称。

**Azure 容器注册表名称**：提供具有容器映像的[Azure 容器注册表](https://aka.ms/DockerContainerRegistriesAzure)的名称。 容器注册表必须存在于您之前提供的 Azure 资源组中。 仅提供注册表项名称，不提供完整的登录服务器名称。 请务必省略名称**中azurecr.io。** 您可以在 Azure 门户中的[容器注册表页上](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)找到注册表项。

**Azure 容器注册表的管理员用户名**：提供与具有容器映像的 Azure 容器注册表关联的[管理员用户名](https://aka.ms/AdminAccountContainerRegistry)。 需要用户名和密码来确保您的公司有权访问注册表。 要获取管理员用户名和密码，请使用 Azure 命令行接口 （CLI） 将**启用管理员**的属性设置为**True。** 可以选择将**管理员用户**设置为在 Azure 门户中**启用**。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="演示更新容器注册表 对话框。":::

**Azure 容器注册表的密码**：为与 Azure 容器注册表关联的管理员用户名提供密码，并具有容器映像。 需要用户名和密码来确保您的公司有权访问注册表。 您可以通过使用[show 命令](https://aka.ms/azacrcredentialshow)访问**容器注册表** > **访问密钥**或使用 Azure CLI 从 Azure 门户获取密码。

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="说明 Azure 门户中的访问键屏幕。":::

**Azure 容器注册表中的存储库名称**。 提供具有映像的 Azure 容器注册表存储库的名称。 将映像推送到注册表时，可以指定存储库的名称。 您可以通过访问[容器注册表](https://aka.ms/ContainerRegistry) > **存储库页**来查找存储库的名称。 有关详细信息，请参阅在[Azure 门户中查看容器注册表存储库](https://aka.ms/ContainerRegistryRepositoriesAzure)。 请注意，设置名称后，无法更改它。 对帐户中的每个产品/服务使用唯一名称。

### <a name="image-tags-for-new-versions-of-your-offer"></a>产品/服务新版本的图像标签

发布更新时，客户必须能够自动从 Azure 应用商店获取更新。 如果他们不想更新，他们必须能够保留您的映像的特定版本。 通过在每次更新图像时添加新图像标记来执行此操作。

**图像标记**。 此字段必须包含指向所有受支持平台上映像的最新版本**的最新**标记。 它还必须包含一个版本标记（例如，从 xx.xx.xx 开始，其中 xx 是数字）。 客户应使用[清单标记](https://aka.ms/GitHubmanifest-tool)来定位多个平台。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 所有清单标记（最新标记除外）必须以 X.Y 或 X.Y.Z- 开头，其中 X、Y 和 Z 是整数。 例如，如果最新的标记指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32，则需要将这六个标记添加到此字段中。 有关标记和版本控制的详细信息，请参阅[准备 IoT Edge 模块技术资产。](https://aka.ms/PrepareIoTEdgeModTechAssets)

### <a name="default-deployment-settings-optional"></a>默认部署设置（可选）

定义最常见的设置来部署 IoT Edge 模块。 通过允许他们使用这些默认设置启动开箱即用的 IoT Edge 模块来优化客户部署。

**默认路由**。 IoT 边缘中心管理模块、IoT 中心和设备之间的通信。 您可以在模块和 IoT 中心之间设置数据输入和输出的路由，从而使您能够灵活地在需要的消息位置发送消息，而无需其他服务来处理消息或编写其他代码。 路由使用名称/值对构造。 您最多可以定义五个默认路由名称，每个路由名称最多为 512 个字符。

请务必在路由值中使用正确的[路由语法](https://aka.ms/DeclareRoutesAzureIoT)（通常定义为 FROM/消息/* into $upstream）。 这意味着任何模块发送的任何消息都发送到 IoT 中心。 要参考您的模块，请使用其默认模块名称，该名称将是您的**优惠名称**，没有空格或特殊字符。 要参考其他尚不为人所知的模块，请使用<FROM_MODULE_NAME>约定，让您的客户知道他们需要更新此信息。 有关 IoT 边缘路由的详细信息，请参阅[声明路由](https://aka.ms/DeclareRoutesAzureIoT)。

例如，如果模块 ContosoModule 侦听 ContosoInput 上的输入和 ContosoOutput 的输出数据，则定义以下两个默认路由是有意义的：

- 名称#1：toContoso模块
- 值#1：从 /消息/模块/<FROM_MODULE_NAME>/输出/* 到中转端点（"/模块/Contoso 模块/输入/Contosoinput"）
- 名称#2：从康托索模块到云
- 值#2：从 /消息/模块/Contonso 模块/输出/contoso 输出到$upstream

**默认模块孪生所需属性**。 模块孪生是 IoT 中心中的 JSON 文档，用于存储模块实例的状态信息，包括所需的属性。 将所需的属性与报告的属性结合使用，可以同步模块配置或条件。 解决方案后端可以设置所需的属性，模块可以读取它们。 模块还可以接收所需属性中的更改通知。 使用最多五个名称/值对创建所需的属性，每个默认值必须小于 512 个字符。 您最多可以定义五个名称/值孪生所需属性。 双所需属性的值必须为有效的 JSON，非转义，没有最大嵌套层次结构为四个级别的数组。 在默认值所需的参数没有意义（例如，客户服务器的 IP 地址）的情况下，可以添加参数作为默认值。 要了解有关双所需属性的详细信息，请参阅[定义或更新所需属性](https://aka.ms/DefineUpdateProperties)。

例如，如果模块使用双所需属性支持动态可配置的刷新率，则定义以下默认孪生所需属性是有意义的：

- 名称#1：刷新率
- 价值#1： 60

**默认环境变量**。 环境变量为帮助配置过程的模块提供补充信息。 使用名称/值对创建环境变量。 每个默认环境变量名称和值必须小于 512 个字符，并且最多可以定义 5 个字符。 当默认值所需的参数没有意义（例如，客户服务器的 IP 地址）时，可以添加参数作为默认值。

例如，如果某个模块在启动之前需要接受使用条款，则你可以定义以下环境变量：

- 名称#1：ACCEPT_EULA
- 值#1：Y

**默认容器创建选项**。 容器创建选项直接用于创建 IoT 边缘模块 Docker 容器。 IoT 边缘支持 Docker 引擎 API 创建容器选项。 查看[List 容器](https://aka.ms/ContainerList)中的所有选项。 创建选项字段必须有效 JSON、非转义字符和少于 512 个字符。

例如，如果模块需要端口绑定，请定义以下创建选项：

"主机配置"：{"端口绑定"：{"5012/tcp"：{"主机端口"："5012"|*

## <a name="review-and-publish"></a>审核和发布

完成产品/服务所需的所有部分后，可以将其提交以供审核和发布。

在门户的右上角，选择 **"审阅"并发布**。

在审阅页上，您可以看到发布状态：

- 请参阅产品/服务的每个部分的完成状态。 在产品/服务的所有部分都标记为完整之前，您无法发布。
    - **未启动**- 部分尚未启动，需要完成。
    - **不完整**- 该部分有需要修复或要求您提供详细信息的错误。 有关指导，请参阅本文档前面的部分。
    - **完成**- 该部分具有所有必需的数据，并且没有错误。 优惠的所有部分必须完成，然后才能提交报价。
- 向认证团队提供测试说明，以确保您的产品/服务测试正确。 此外，提供有助于了解您的报价的任何补充说明。

要提交发布要约，请选择 **"发布**"。

我们将向您发送一封电子邮件，告知您产品/服务预览版本何时可供审核和批准。 要向公众发布您的优惠（或者如果私人优惠，则向私人受众），请转到合作伙伴中心并选择 **"Go-live**"。

## <a name="next-steps"></a>后续步骤

- [更新商业市场中的现有产品/服务](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)
