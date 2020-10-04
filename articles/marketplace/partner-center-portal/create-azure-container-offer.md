---
title: 创建 Azure 容器产品/服务 - Azure 市场
description: 了解如何创建容器产品/服务并将它发布到 Azure 市场。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: cbaca0b9d608d7703beec559e5434f52308629ab
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709098"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>在 Azure 市场中创建 Azure 容器套餐

本文介绍如何为 Azure 市场创建和发布容器产品/服务。 开始前，请先[在合作伙伴中心内创建商业市场帐户](create-account.md)（如果尚未创建的话）。 确保帐户已注册加入商业市场计划。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

2. 在左侧导航菜单中，选择“商业市场” > “概述” 。

3. 在“概述”页上，选择“+ 新建产品/服务” > “Azure 容器” 。

   ![左侧导航菜单图示。](./media/new-offer-azure-container.png)

> [!TIP]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅会在重新发布产品/服务后出现在联机商店中。 请确保在进行更改后始终重新发布。

### <a name="offer-id-and-alias"></a>产品/服务 ID 和别名

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 选择“创建”后，便无法更改产品/服务 ID。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称不用于市场，并且与向客户显示的产品/服务名称和其他值不同。
- 选择“创建”后，便无法更改此名称。

选择“创建”，以生成产品/服务，并继续操作。

## <a name="offer-overview"></a>产品/服务概述

“产品/服务概述”页直观地展示了发布此产品/服务所需的步骤（已完成和即将完成），以及每个步骤需要多长时间才能完成。

此页显示基于产品/服务当前状态的不同链接。 例如：

- 如果产品/服务是草稿 - [删除草稿产品/服务](update-existing-offer.md#delete-a-draft-offer)
- 如果产品/服务已上线 - [停止销售产品/服务](update-existing-offer.md#stop-selling-an-offer-or-plan)
- 如果产品/服务为预览版 - [投入使用](publishing-status.md#publisher-approval)
- 如果尚未完成发布服务器注销 - [取消发布](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>产品/服务设置

若要设置产品/服务，请按照以下步骤操作。

### <a name="customer-leads--optional"></a>潜在客户 - 可选

将产品/服务发布到合作伙伴中心的商业市场时，可以将它连接到客户关系管理 (CRM) 系统。 这样，只要有人表示有兴趣购买或使用产品，你就会收到客户的联系信息。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：

   - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > 如果你的 CRM 系统没有在上面列出，请使用 [Azure 表](commercial-marketplace-lead-management-instructions-azure-table.md)或 [Https 终结点](commercial-marketplace-lead-management-instructions-https.md)存储潜在客户数据，然后将数据导出到你的 CRM 系统。

2. 在合作伙伴中心内发布时，将产品/服务连接到潜在客户目标。
3. 确认是否正确配置了与潜在客户目标的连接。 当你在合作伙伴中心内发布它后，我们会验证连接，并向你发送测试潜在客户。 若要在上线前预览产品/服务，也可以通过亲自尝试在预览环境中购买产品/服务来测试潜在客户连接。
4. 请务必不断更新与潜在客户目标的连接，以免失去任何潜在客户。

下面是其他一些潜在客户管理资源：

- [商业市场产品/服务的潜在客户](commercial-marketplace-get-customer-leads.md)
- [有关潜在客户管理的常见问题](../lead-management-faq.md#common-questions-about-lead-management)
- [潜在客户配置错误疑难解答](../lead-management-faq.md#publishing-config-errors)
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF（确保弹出窗口阻止程序处于禁用状态）。

选择“保存草稿”，然后继续。

### <a name="properties"></a>属性

通过此页，可以定义用于在市场上对产品/服务进行分组的类别，以及支持产品/服务的法律协定。

#### <a name="category"></a>类别

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别 (可选) 。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 " **不适用**"。

请参阅 [产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。 容器始终 **显示在 "容器"** 下，然后显示 " **容器映像** " 类别。

#### <a name="legal"></a>合法

必须提供产品/服务的条款和条件。 存在两个选项：

- 使用 Microsoft 商业市场标准协定。
- 提供你自己的条款和条件。

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商业市场标准协定

我们提供了标准协定模板，以帮助促进商业市场中的交易。 可以选择根据标准协定提供解决方案，这样客户只需要查看并接受一次。 如果你不想创建自定义条款和条件，不妨考虑使用这种方式。

若要详细了解标准协定，请参阅 [Microsoft 商业市场的标准协定](../standard-contract.md)。 你还可以下载[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178) PDF（确保弹出窗口阻止程序处于禁用状态）。

若要使用标准合同，请选择 "使用 Microsoft 商业 marketplace 的标准合同" (。/standard-contract.md) 

> [!NOTE]
> 使用 Microsoft 商业市场的标准协定发布产品/服务后，你将无法使用自己的自定义条款和条件。 要么根据标准协定提供解决方案，要么根据你自己的条款和条件提供解决方案。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

##### <a name="your-own-terms-and-conditions"></a>你自己的条款和条件

若要提供你自己的自定义条款和条件，请在“条款和条件”框中输入它们。 您可以在此框中输入不限数量的字符。 客户必须接受这些条款，才能试用你的产品/服务。

选择“保存草稿”，然后转到下一部分“产品/服务一览”继续操作。

## <a name="offer-listing"></a>产品/服务一览

你可以在此页面中定义显示在商业市场中的产品/服务详细信息。 其中包括产品/服务名称、说明和图像。

> [!NOTE]
> 如果产品/服务说明的开头是“此应用程序只提供 [非英语语言] 版本”，则产品/服务详细信息不必是英语。 还可以提供实用链接，以不同于产品/服务一览详细信息中使用的语言提供内容。

### <a name="name"></a>名称

在此处输入的名称显示为产品/服务的标题。 此字段预填充了你在创建产品/服务时在“产品/服务别名”框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字（可以包括商标和版权符号）。
- 长度不得超过 50 个字符。
- 不得包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

对产品/服务的简短说明。 此说明最多可包含 100 个字符，用于市场搜索结果。

### <a name="long-summary"></a>长摘要

对产品/服务的更详细说明。 此说明最多可包含 256 个字符，用于市场搜索结果。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 Web 地址。 你有责任确保你的产品/服务符合隐私法律法规。 你还有责任在你的网站上发布有效的隐私策略。

#### <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 最多可以添加 25 个链接。 若要添加链接，请选择“+ 添加链接”，然后填写以下字段：

- 标题 - 客户将在产品/服务详细信息页上看到此标题。
- 链接 (URL) - 输入链接，以供客户查看联机文档。 链接必须以 http:// 或 https:// 开头。

### <a name="contact-information"></a>联系信息

必须提供支持联系人和工程联系人的姓名、电子邮件和电话号码 。 此信息不会向客户显示，但会向 Microsoft 提供。 这些信息还可能提供给云解决方案提供商 (CSP) 合作伙伴。

- 支持联系人（必填）：用于解决常规支持问题。
- 工程联系人（必填）：用于解决技术问题和认证问题。
- CSP 计划联系人（选填）：用于解决与 CSP 计划相关的经销商问题。

在“支持联系人”部分中，提供支持网站合作伙伴可以在此网站中根据产品/服务是在 Azure 全球中还是在 Azure 政府中推出，还是在两者中推出来寻求产品/服务支持。

在“CSP 计划联系人”部分中，提供 CSP 合作伙伴可以在其中查找产品/服务的市场营销资料的链接（CSP 计划市场营销资料）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场一览资源

若要详细了解如何创建产品/服务一览，请参阅[产品/服务一览最佳做法](../gtm-offer-listing-best-practices.md)。

### <a name="marketplace-images"></a>市场映像

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊的图像将被拒绝。

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>应用商店徽标

提供 **大** 徽标徽标的 PNG 文件。 合作伙伴中心将使用此来创建 **小型** 和 **中型** 徽标。 稍后可以选择将它们替换为不同的图像。

- **大型** (从 216 x 216 到 350 x 350 px，必需) 
- **中型** (90 x 90 px，可选) 
- **小写** (48 x 48 px，可选) 

在列表中的不同位置使用这些徽标：

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>屏幕截图（可选）

最多添加五张显示产品/服务工作方式的屏幕截图。 每张屏幕截图大小必须为 1280 x 720 像素并采用 PNG 格式。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个视频来演示产品/服务。 输入视频的名称及其 Web 地址，并提供视频的缩略图 PNG 图像（大小为 1280 x 720 像素）。

#### <a name="offer-examples"></a>优惠示例

下面几个示例展示了产品/服务一览字段在产品/服务的不同位置上的显示方式。

下图显示 Azure 市场中的“产品/服务一览”页：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。" :::

下图显示 Azure 市场中的搜索结果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

下图显示 Azure 门户中的“产品/服务一览”页：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

下图显示 Azure 门户中的搜索结果：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

## <a name="preview"></a>预览

在“预览”选项卡上，可以先选择有限的预览受众来验证产品/服务，再向更广泛的市场受众发布它。

> [!IMPORTANT]
> 在查看处于预览状态的产品/服务后，必须选择“上线”才能向公众发布产品/服务 。

使用 Azure 订阅 ID GUID 指定预览受众，以及每个受众的可选说明。 客户无法查看所有这些字段。

> [!NOTE]
> 可以在 Azure 门户的“订阅”页上找到 Azure 订阅 ID。

添加至少一个 Azure 订阅 ID，可以单独添加（最多 10 个），也可以通过上传 CSV 文件来添加（最多 100 个）。 通过添加这些订阅 ID，可以确定在产品/服务发布上线前谁能预览它。 如果产品/服务已上线，可以选择预览受众来测试产品/服务的更改或更新。

选择“保存草稿”，然后继续。

## <a name="plan-overview"></a>计划概述

使用此选项卡，可以在相同产品/服务中提供不同的计划选项。 计划 (以前称为 Sku) 的计划可能不同于云的可用性，如全球云、政府云和计划引用的映像。 若要在商业市场中列出你的产品/服务，必须至少设置一个计划。

最多可为每个产品创建100个计划：最多可为45个。 详细了解 [Microsoft 商业应用商店](../private-offers.md)中的专用产品/服务。

创建计划后，“计划概述”选项卡将显示：

- 计划名称
- 定价模型
- Azure 区域 (全局或政府) 
- 当前发布状态
- 任何可用操作

“计划概述”中的可用操作因计划的当前状态而异。 其中包括：

- “删除草稿”- 如果计划状态为“草稿”。
- “停止销售计划”- 如果计划状态为“已发布上线”。

### <a name="create-new-plan"></a>新建计划

选择“新建计划”。 此时将显示“新建计划”对话框。

在“计划 ID”框中，创建此产品/服务中每个计划的唯一计划 ID。 客户可以在产品的 Web 地址中看到此 ID。 仅可使用小写字母和数字、破折号或下划线字符，最多包含 50 个字符。

> [!NOTE]
> 选择“创建”后，便无法更改计划 ID。

在“计划名称”框中，输入此计划的名称。 客户在确定要在产品/服务中选择哪个计划时会看到此名称。 创建此产品/服务中每个计划的唯一名称。 例如，可以将 Windows Server 的产品/服务名称与 Windows Server 2016 和 Windows Server 2019 计划一起使用。

### <a name="plan-setup"></a>计划设置

使用此选项卡可以选择计划在哪些云中可用。 你在此选项卡上的设置会影响在其他选项卡上显示的字段。

#### <a name="azure-regions"></a>Azure 区域

Azure 容器产品/服务的所有计划都将自动提供给 **Azure Global**。  所有使用商业应用商店的全球 Azure 区域的客户都可以使用你的计划。 有关详细信息，请参阅[地理区域可用性和货币支持](../marketplace-geo-availability-currencies.md)。

选择 [Azure 政府](../../azure-government/documentation-government-welcome.md) 版选项，使解决方案显示在此处。 这是政府社区云，会对来自美国联邦、州、地方或部落政府机构的客户和有资格服务于他们的合作伙伴进行访问控制。 作为发布者，你要对此云社区的任何符合性控制、安全措施和最佳做法负责。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。 在[发布](../../azure-government/documentation-government-manage-marketplace-partners.md)到 Azure 政府前，请先在相应区域中测试并确认你的解决方案，因为结果可能会有所不同。 若要创建和测试你的解决方案，请通过 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)申请试用帐户。

> [!NOTE]
> 在您的计划发布并在特定区域中可用后，您将无法删除该区域。

#### <a name="azure-government-certifications"></a>Azure 政府认证

仅当在**azure 区域**下选择了**azure 政府**时，才能显示此选项。

Azure 政府服务处理符合特定政府法规和要求的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。

若要展示你在这些计划上获得的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到你在这些计划上获得的认证一览，也可以链接到你自己的网站。 这些链接只对 Azure 政府客户可见。

### <a name="plan-listing"></a>计划一览

此选项卡显示当前产品/服务中每个不同计划的具体信息。

### <a name="plan-name"></a>计划名称

此字段预填充了你在创建计划时为计划命名的名称。 可以根据需要更改此名称。 最长可包含 50 个字符。 在 Azure 市场和 Azure 门户中，这一名称显示为此计划的标题。 在计划可供使用后，这一名称就会用作默认模块名称。

### <a name="plan-summary"></a>计划摘要

计划（而不是产品/服务）的简短摘要。 此摘要显示在 Azure 市场搜索结果中，最多可包含 100 个字符。

### <a name="plan-description"></a>计划说明

描述此软件计划的独特之处，以及产品/服务内的计划之间的差异。 不要描述产品/服务，请只描述计划。 在 Azure 市场和 Azure 门户中，此说明显示在“产品/服务一览”页上。 它可以与计划摘要中提供的内容相同，最多可包含 2,000 个字符。

填写这些字段后，选择“保存”。

#### <a name="plan-examples"></a>计划示例

下面几个示例展示了计划一览字段在不同视图中的显示方式。

下面是在查看计划详细信息时 Azure 市场中的字段：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

下面是 Azure 门户上的计划详细信息：

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

### <a name="plan-availability"></a>计划可用性

若要隐藏已发布的产品/服务，以便客户无法在市场中搜索、浏览或购买它，请选中“可用性”选项卡上的“隐藏计划”复选框。

此字段用于以下情况：

- 此产品/服务旨在通过其他应用程序引用时间接使用。
- 产品/服务不得单独购买。
- 计划用于初始测试，已不再相关。
- 计划用于临时或季节性产品/服务，不得再提供。

## <a name="technical-configuration"></a>技术配置

容器映像必须托管在私有 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)中。 在“技术配置”选项卡上，提供 Azure 容器注册表内的容器映像存储库的引用信息。

在产品/服务发布后，容器映像会复制到 Azure 市场中的特定公共容器注册表。 所有使用容器映像的请求均由 Azure 市场公共容器注册表（而不是专用容器注册表）提供服务。 有关详细信息，请参阅[准备 Azure 容器技术资产](create-azure-container-technical-assets.md)。

### <a name="image-repository-details"></a>映像存储库详细信息

在“映像存储库详细信息”选项卡中提供以下信息。

Azure 订阅 ID - 提供用于报告使用情况，并为包含容器映像的 Azure 容器注册表进行服务计费的订阅 ID。 可以在 Azure 门户的[“订阅”页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此 ID。

Azure 资源组名称 - 提供包含具有容器映像的 Azure 容器注册表的[资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md)名称。 资源组必须可以通过上面的订阅 ID 访问。 可以在 Azure 门户的[“资源组”](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)页上找到此名称。

Azure 容器注册表名称 - 提供包含容器映像的 [Azure 容器注册表](../../container-registry/container-registry-intro.md)的名称。 容器注册表必须位于你之前提供的 Azure 资源组中。 仅包括注册表名称，而不包括完整的登录服务器名称。 请务必从名称中省略 azurecr.io。 可以在 Azure 门户的[“容器注册表”页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到注册表名称。

**Azure 容器注册表的管理员用户名** –提供 [管理员用户名](../../container-registry/container-registry-authentication.md#admin-account)) 与具有容器映像的 Azure 容器注册表链接。 用户名和密码是必需的，以确保公司有权访问注册表。 若要获取管理员用户名和密码，请使用 Azure 命令行接口 (CLI) 将“管理员已启用”属性设置为“True”。 在 Azure 门户中，可以视需要选择将“管理员用户”设置为“启用”。

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

Azure 容器注册表的密码 - 提供与包含容器映像的 Azure 容器注册表相关联的管理员用户的密码。 用户名和密码是必需的，以确保公司有权访问注册表。 可以从 Azure 门户中获取密码，具体方法为依次转到“容器注册表” > “访问密钥”，或通过 Azure CLI 运行 [show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)。

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="“使用 Microsoft 商业市场的标准协定”复选框图示。":::

Azure 容器注册表中的存储库名称。 提供包含映像的 Azure 容器注册表存储库的名称。 可以在将映像推送到注册表时包含存储库名称。 若要查找存储库名称，可以依次转到[容器注册表](https://azure.microsoft.com/services/container-registry/) > “存储库”页。 有关详细信息，请参阅[在 Azure 门户中查看容器注册表存储库](../../container-registry/container-registry-repositories.md)。

> [!NOTE]
> 请注意，名称一经设置便不可更改。 请对你帐户中的每个产品/服务使用唯一名称。

### <a name="image-tags-for-new-versions-of-your-offer"></a>新版产品/服务的映像标记

当你发布更新时，客户必须能够从 Azure 市场自动获取更新。 如果客户不想更新，他们必须能够继续使用特定版本的映像。 为此，可以在每次更新映像时添加新的映像标记。

### <a name="image-tag"></a>映像标记

此字段必须包含指向所有受支持平台上最新版映像的最新标记。 它还必须包含版本标记（例如，以 xx.xx.xx 开头，其中 xx 是数字）。 客户应使用[清单标记](https://github.com/estesp/manifest-tool)来将多个平台作为目标。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。

所有清单标记（最新标记除外）必须以 X.Y- 或 X.Y.Z- 开头，其中，X、Y 和 Z 为整数。 例如，如果最新标记指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32，必须将这六个标记添加到此字段。 有关详细信息，请参阅[准备 Azure 容器技术资产](create-azure-container-technical-assets.md)。

> [!NOTE]
> 请记得向你的映像添加一个测试标记，以便在测试期间可以标识该映像。

## <a name="review-and-publish"></a>审阅和发布

完成产品/服务的所有必需部分后，可以提交产品/服务以供审阅和发布。

在门户的右上角，选择“审阅和发布” 。

在审阅页上，你可以：

- 查看产品/服务的每个部分的完成状态。 除非产品/服务的所有部分都标记为“已完成”，否则无法发布。
  - 未启动 - 尚未启动，需要完成。
  - 未完成 - 有错误需要修复，或需要你提供更多信息。 请参阅本文档前面的部分以获取帮助。
  - 完成 - 包含所有必需数据并且无错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保产品/服务得到正确测试。 同时，提供有助于了解产品/服务的补充说明。

若要提交产品/服务进行发布，请选择“发布”。

我们会向你发送电子邮件，让你知道何时有预览版产品/服务可供审阅和审核。

若要将产品/服务发布到公众，请前往 "合作伙伴中心" 并选择 " **上线**"。

## <a name="next-step"></a>后续步骤

- [更新商业市场中的现有产品/服务](update-existing-offer.md)
