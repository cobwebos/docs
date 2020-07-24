---
title: 使用合作伙伴中心在 Azure 市场中创建 Azure IoT Edge 模块产品/服务
description: 了解如何使用合作伙伴中心在 Azure Marketplace 中创建、配置和发布 IoT Edge 模块产品。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: anbene
ms.author: mingshen
ms.date: 07/22/2020
ms.openlocfilehash: b2a1e523e2af8c0be9c4211dfc8a0ffd2a889ca0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053668"
---
# <a name="create-an-iot-edge-module-offer"></a>创建 IoT Edge 模块产品/服务

本文介绍了如何创建和发布面向 Azure 市场的物联网 (IoT) Edge 模块产品/服务。 开始前，请先[在合作伙伴中心内创建商业市场帐户](create-account.md)（如果尚未创建的话）。 确保你的帐户已注册加入商业市场计划。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，依次选择“商业市场” > “概述”。
3. 在“概述”页上，依次选择“+ 新建产品/服务” > “IoT Edge 模块”。

    ![展示了左侧导航菜单。](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> 在产品/服务发布后，在合作伙伴中心内对它进行的编辑只会在产品/服务重新发布后才显示在店面中。 请确保在进行更改后始终重新发布。

### <a name="offer-id-and-alias"></a>产品/服务 ID 和别名

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

- 此名称没有用于市场，与向客户显示的产品/服务名称和其他值不同。
- 在选择“创建”后，就无法更改此名称了。

选择“创建”，以生成产品/服务，并继续操作。

## <a name="offer-overview"></a>产品/服务概述

“产品/服务概述”页直观地展示了发布此产品/服务所需的步骤（已完成和即将完成），以及每个步骤需要多长时间才能完成。

此页中包含链接，用于根据你所做的选择对此产品/服务执行操作。 例如：

- 如果产品/服务是草稿版/草稿版[产品](update-existing-offer.md#delete-a-draft-offer)/服务
- 如果产品/服务是实时的，则[停止销售产品](update-existing-offer.md#stop-selling-an-offer-or-plan)/服务）
- 如果[产品/服务是预览版）](publishing-status.md#publisher-approval)
- 如果尚未完成发布服务器注销-请[取消发布。](update-existing-offer.md#cancel-publishing)）

## <a name="offer-setup"></a>产品/服务设置

若要设置产品/服务，请按照以下步骤操作。

### <a name="customer-leads"></a>潜在客户

使用合作伙伴中心将产品/服务发布到市场时，可以视需要选择将它连接到客户关系管理 (CRM) 系统。 这样，只要有人对你的产品表示兴趣或使用你的产品，你就可以收到客户的联系信息。

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

- [潜在客户管理概述](commercial-marketplace-get-customer-leads.md)
- [潜在客户管理常见问题](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)）
- [常见潜在客户配置错误](../lead-management-for-cloud-marketplace.md#publishing-config-errors)）
- [潜在客户管理概述](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF（确保弹出窗口阻止程序处于禁用状态）。

选择“保存草稿”，然后继续操作。

### <a name="properties"></a>属性

通过此页，可以定义用于在市场上对产品/服务进行分组的类别，以及支持产品/服务的法律协定。

#### <a name="category"></a>类别

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别（可选）。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 "**不适用**"。

请参阅[产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。 在 marketplace 中，IoT Edge 模块始终显示在 " **物联网**  >  **IoT Edge" 模块**"   类别下。

#### <a name="legal"></a>合法

必须提供产品/服务的条款和条件。 可以使用两个选项：

- 使用 Microsoft 商业市场的标准协定。
- 提供你自己的条款和条件。

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商业市场的标准协定

我们提供了标准协定模板，以帮助促进商业市场中的交易。 可以选择根据标准协定提供解决方案，这样客户只需要查看并接受一次。 如果你不想编写自定义条款和条件，不妨考虑使用这种方式。

若要详细了解标准协定，请参阅 [Microsoft 商业市场的标准协定](../standard-contract.md)。 还可以下载[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178) PDF（确保弹出窗口阻止程序处于禁用状态）。

若要使用标准协定，请选中“使用 Microsoft 商业市场的标准协定”复选框，然后单击“接受”。

> [!NOTE]
> 使用 Microsoft 商业市场的标准协定发布产品/服务后，你将无法使用自己的自定义条款和条件。 要么根据标准协定提供解决方案，要么根据你自己的条款和条件提供解决方案。

![展示了“使用 Microsoft 商业市场的标准协定”复选框。](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>你自己的条款和条件

若要提供你自己的自定义条款和条件，请在“条款和条件”框中输入它们。 可以在此框中输入不限数量的文本字符。 客户必须接受这些条款，才能试用你的产品/服务。

选择“保存草稿”，然后转到下一部分“产品/服务一览”继续操作。

## <a name="offer-listing"></a>产品/服务列表

在此处将定义在市场中显示的产品/服务详细信息。 其中包括产品/服务名称、说明、图像等。 在配置此产品/服务时，请务必遵循 Microsoft 策略页上详细说明的策略。

> [!NOTE]
> 如果产品/服务说明的开头短语是“此应用只提供[非英语语言]版本”，那么产品/服务详细信息就不要求是英语的。 还可以提供实用链接，以不同于产品/服务一览详细信息中使用的语言提供内容。

### <a name="name"></a>名称

在此处输入的名称显示为产品/服务的标题。 此字段预填充了你在创建产品/服务时在“产品/服务别名”框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字（可以包括商标或版权符号）。
- 长度不得超过 50 个字符。
- 不得包含表情符号。

### <a name="search-results-summary"></a>搜索结果摘要

提供对产品/服务的简短说明。 此说明最多可包含 100 个字符，用于市场搜索结果。

### <a name="long-summary"></a>长摘要

提供对产品/服务的更详细说明。 此说明最多可包含 256 个字符，用于市场搜索结果。

### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge 模块产品/服务必须在说明底部添加最低硬件要求段落，例如：

- 最低硬件要求： Linux x64 和 arm32 OS，1GB RAM，500MB 存储

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的 Web 地址。 你有责任确保你的产品/服务符合隐私法律法规。 你还有责任在你的网站上发布有效的隐私策略。

#### <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 最多可以添加 25 个链接。 若要添加链接，请选择“+ 添加链接”，然后填写以下字段：

- 标题 - 客户将在产品/服务详细信息页上看到标题。
- 链接 (URL) - 输入链接，以供客户查看联机文档。 链接必须以或开头 `http://` `https://` 。

请务必至少添加一个文档链接，以及一个  [Azure IoT 设备目录](https://catalog.azureiotsolutions.com/)中兼容的 IoT Edge 设备的链接。

### <a name="contact-information"></a>联系信息

必须提供支持联系人和工程联系人的姓名、电子邮件地址和电话号码。 此信息不会向客户显示。 它可供 Microsoft 使用，并且可提供给云解决方案提供商 (CSP) 合作伙伴。

- 支持联系人（必需）：用于解决常规支持问题。
- 工程联系人（必填）：用于解决技术问题和认证问题。
- CSP 计划联系人（可选）：用于解决与 CSP 计划相关的经销商问题。

在“支持联系人”部分中，提供支持网站的 Web 地址，合作伙伴可以在此网站中根据产品/服务是在 Azure 全球中还是在 Azure 政府中推出亦或是在两者中推出来寻求产品/服务支持。

在“CSP 计划联系人”部分中，提供 CSP 合作伙伴可以在其中查找产品/服务的市场营销资料的链接（CSP 计划市场营销资料）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场一览资源

若要详细了解如何创建产品/服务一览，请参阅[产品/服务一览最佳做法](../gtm-offer-listing-best-practices.md)。

### <a name="marketplace-images"></a>市场映像

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊的图像将被拒绝。

>[!Note]
>如果在上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>应用商店徽标

按以下四个像素大小提供产品/服务徽标的 PNG 文件：

- 小 (48 x 48)
- 中 (90 x 90)
- 大 (216 x 216)
- 宽 (255 x 115)

所有四个徽标都是必需的，并且在市场一览中的不同位置上使用。

#### <a name="screenshots-optional"></a>屏幕截图（可选）

最多添加五张显示产品/服务工作方式的屏幕截图。 每张屏幕截图大小必须为 1280 x 720 像素并采用 PNG 格式。

#### <a name="videos-optional"></a>视频（可选）

最多添加五个视频来演示产品/服务。 输入视频的名称及其 Web 地址，并提供视频的缩略图 PNG 图像（大小为 1280 x 720 像素）。

#### <a name="marketplace--examples"></a>Marketplace 示例

下面是有关如何在 Azure Marketplace 中显示产品/服务的示例：

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="说明此产品/服务在 Azure Marketplace 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 大徽标
2. 类别
3. 支持地址（链接）
4. 条款和条件
5. 隐私策略地址（链接）
6. 名称
7. 摘要
8. 描述
9. 有用链接
10. 屏幕截图/视频

<br>下面是有关如何在 Azure Marketplace 搜索结果中显示产品/服务的示例：

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="说明此产品/服务在 Azure Marketplace 搜索结果中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 小徽标
2. 产品名称
3. 搜索结果摘要

<br>下面是有关如何在 Azure 门户中显示产品/服务的示例：

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="说明了此产品/服务在 Azure 门户中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 名称
2. 描述
3. 有用链接
4. 屏幕截图

<br>下面是一个示例，说明了产品/服务信息在 "Azure 门户搜索" 结果中的显示方式：

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="说明此产品/服务在 Azure 门户搜索结果中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 小徽标
2. 产品名称
3. 搜索结果摘要

<br>选择“保存草稿”，然后转到下一部分“预览”继续操作。

## <a name="preview"></a>预览

在“预览”选项卡上，可以先选择有限的预览受众来验证产品/服务，再向更广泛的市场受众发布它。

> [!IMPORTANT]
> 在查看处于预览状态的产品/服务后，必须选择“上线”向公众发布产品/服务。

使用 Azure 订阅 ID GUID 指定预览受众，以及每个受众的可选说明。 客户看不到这些字段。

> [!NOTE]
> 可以在 Azure 门户的“订阅”页上找到 Azure 订阅 ID。

添加至少一个 Azure 订阅 ID，可以单独添加（最多 10 个），也可以通过上传 CSV 文件来添加（最多 100 个）。 通过添加这些订阅 ID，可以定义在产品/服务发布上线前谁能预览它。 如果产品/服务已上线，可以定义预览受众来测试产品/服务的更改或更新。

选择“保存草稿”，然后转到下一部分“计划概述”继续操作。

### <a name="plan-overview"></a>计划概述

使用此选项卡，可以在合作伙伴中心内的相同产品/服务中提供不同的计划选项。 计划（以前称为 Sku）的不同之处在于可用的云，如全球云、政府云和计划引用的映像。 若要在市场中列出你的产品/服务，至少必须设置一个计划。

在你创建计划后，“计划概述”选项卡显示：

- 计划名称
- 定价模型
- Azure 区域（全局或政府）
- 当前发布状态
- 任何可用操作

“计划概述”中的可用操作因计划的当前状态而异。 其中包括：

- 删除草稿：如果计划状态为“草稿”。
- 停止销售计划：如果计划状态为“已发布上线”。

#### <a name="create-new-plan"></a>新建计划

选择“新建计划”。 此时，“新建计划”对话框显示。

在“计划 ID”框中，创建此产品/服务中的每个计划的唯一计划 ID。 客户可以在产品 Web 地址中看到此 ID。 只使用小写字母和数字、破折号或下划线字符，最多包含 50 个字符。

在“计划名称”框中，输入此计划的名称。 客户在确定要在产品/服务中选择哪个计划时会看到此名称。 创建此产品/服务中每个计划的唯一名称。 例如，可以将 Windows Server 的产品/服务名称与 Windows Server 2016 和 Windows Server 2019 计划一起使用。

> [!NOTE]
> 在选择“创建”后，就无法更改计划 ID 了。

选择“创建”。

### <a name="plan-setup"></a>计划设置

使用此选项卡，可以配置计划在哪些云中可用。 你在此选项卡上的设置会影响哪些字段显示在其他选项卡上。

#### <a name="azure-regions"></a>Azure 区域

IoT Edge 模块产品/服务的所有计划自动在**Azure Global**中提供。  使用 marketplace 的所有全球 Azure 区域中的客户都可以使用你的计划。 有关详细信息，请参阅[地理区域可用性和货币支持](../marketplace-geo-availability-currencies.md)。

选择[Azure 政府](../../azure-government/documentation-government-welcome.md)版选项，使解决方案显示在此处。 这是政府社区云，会对来自美国联邦、州、地方或部落政府机构的客户和有资格服务于他们的合作伙伴进行访问控制。 作为发布者，你要对此云社区的任何符合性控制、安全措施和最佳做法负责。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。 在[发布](../../azure-government/documentation-government-manage-marketplace-partners.md)到 Azure 政府前，请先在相应区域中测试并确认你的解决方案，因为结果可能会有所不同。 若要暂存和测试你的解决方案，请通过 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)申请试用帐户。

> [!NOTE]
> 在您的计划发布并在特定区域中可用后，您将无法删除该区域。

#### <a name="azure-government-certifications"></a>Azure 政府认证

仅当在 " **azure 区域**" 下选择了 " **azure 政府**" 时，此选项才可见。

Azure 政府服务处理符合特定政府法规和要求的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 为了让受众注意到你在这些计划上获得的认证，可以提供最多 100 个链接来描述你的认证。 这些链接可以直接链接到你在这些计划上获得的认证一览，也可以链接到你自己的网站。 这些链接只对 Azure 政府客户可见。

## <a name="plan-listing"></a>计划一览

此选项卡显示同一个产品/服务内每个不同计划的具体信息。

### <a name="plan-name"></a>计划名称

此字段预填充了你在创建计划时为计划命名的名称。 可以根据需要更改此名称。 最长可包含 50 个字符。 此名称在 Azure Marketplace 和 Azure 门户中显示为此计划的标题。 在计划可供使用后，这一名称就会用作默认模块名称。

### <a name="plan-summary"></a>计划摘要

提供计划（而不是产品/服务）的简短摘要。 此摘要显示在 Azure 市场搜索结果中，最多可包含 100 个字符。

### <a name="plan-description"></a>计划说明

描述此计划的独特之处，以及产品/服务内各个计划的区别。 不要描述产品/服务，只描述计划。 在 Azure 市场和 Azure 门户中，此说明显示在“产品/服务一览”页上。 它可以与计划摘要中提供的内容相同，最多可包含 2,000 个字符。

填写这些字段后，选择“保存草稿”。

#### <a name="plan-examples"></a>计划示例

下面是 Azure Marketplace 计划详细信息的示例（所有列出的价格仅用于示例目的，不打算反映实际成本）：

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="说明 Azure Marketplace 计划详细信息。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 产品名称
2. 计划名称
3. 计划说明

<br>下面是 Azure 门户计划详细信息的示例（所有列出的价格仅用于示例目的，不打算反映实际成本）：

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="说明 Azure 门户计划详细信息。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 产品名称
2. 计划名称
3. 计划说明

## <a name="availability"></a>可用性

若要隐藏已发布的产品/服务，以便客户无法在市场中搜索、浏览或购买它，请选中“可用性”选项卡上的“隐藏计划”复选框。

此字段通常用于以下情况：

- 产品/服务仅供通过其他应用引用来间接使用。
- 产品/服务不得单独购买。
- 计划用于初始测试，已不再相关。
- 计划用于临时或季节性产品/服务，不得再提供。

## <a name="technical-configuration"></a>技术配置

“IoT Edge 模块”产品/服务类型是在 IoT Edge 设备上运行的一种特定类型的容器。 在“技术配置”选项卡上，将提供 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)内的容器映像存储库的引用信息，以及可便于客户轻松使用模块的配置设置。

在产品/服务发布后，IoT Edge 容器映像会复制到 Azure 市场中的特定公共容器注册表。 Azure 用户的所有使用模块的请求都是由 Azure 市场公共容器注册表（而不是专用容器注册表）提供服务。

可以定目标到多个平台，并使用标记提供模块容器映像的多个版本。 若要详细了解标记和版本控制，请参阅[准备 IoT Edge 模块技术资产](create-iot-edge-module-asset.md)。

### <a name="image-repository-details"></a>映像存储库详细信息

将在“映像存储库详细信息”选项卡中提供以下信息。

选择映像源：选择“Azure 容器注册表”选项。

Azure 订阅 ID：提供用于报告资源使用情况，并为包含容器映像的 Azure 容器注册表进行服务计费的订阅 ID。 可以在 Azure 门户的[“订阅”页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此 ID。

Azure 资源组名称：提供包含具有容器映像的 Azure 容器注册表的[资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md)的名称。 资源组必须在上面的订阅 ID 中是可访问的。 可以在 Azure 门户的[“资源组”](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)页上找到此名称。

Azure 容器注册表名称：提供包含容器映像的 [Azure 容器注册表](../../container-registry/container-registry-intro.md)的名称。 容器注册表必须位于你前面提供的 Azure 资源组中。 只提供注册表名称，而不提供完整的登录服务器名称。 请务必从名称中省略 azurecr.io。 可以在 Azure 门户的[“容器注册表”页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到注册表名称。

**Azure 容器注册表的管理员用户名**：提供与具有容器映像的 Azure 容器注册表关联的[管理员用户名](../../container-registry/container-registry-authentication.md#admin-account)）。 用户名和密码是必需的，以确保公司有权访问注册表。 若要获取管理员用户名和密码，请使用 Azure 命令行接口 (CLI) 将“管理员已启用”属性设置为“True”。 在 Azure 门户中，可以视需要选择将“管理员用户”设置为“启用”。

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="展示了“更新容器注册表”对话框。":::

#### <a name="call-out-description"></a>拨出说明

1. 管理员用户

<br>Azure 容器注册表的密码：提供与包含容器映像的 Azure 容器注册表关联的管理员用户名的密码。 用户名和密码是必需的，以确保公司有权访问注册表。 可以从 Azure 门户中获取密码，具体方法为依次转到“容器注册表” > “访问密钥”，或通过 Azure CLI 运行 [show 命令](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)。

:::image type="content" source="media/example-iot-access-keys.png" alt-text="说明 Azure 门户中的访问密钥屏幕。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 访问密钥
2. 用户名
3. 密码

Azure 容器注册表中的存储库名称。 提供包含映像的 Azure 容器注册表存储库的名称。 可以在将映像推送到注册表时指定存储库名称。 若要查找存储库名称，可以依次转到[容器注册表](https://azure.microsoft.com/services/container-registry/) > “存储库”页。 有关详细信息，请参阅[在 Azure 门户中查看容器注册表存储库](../../container-registry/container-registry-repositories.md)。 请注意，在设置了名称之后，就不能更改它了。 请对你帐户中的每个产品/服务使用唯一名称。

### <a name="image-tags-for-new-versions-of-your-offer"></a>新版产品/服务的映像标记

当你发布更新时，客户必须能够从 Azure 市场自动获取更新。 如果客户不想更新，他们必须能够继续使用特定版本的映像。 为此，可以在每次更新映像时添加新的映像标记。

映像标记。 此字段必须包含指向所有受支持平台上最新版映像的最新标记。 它还必须包含版本标记（例如，以 xx.xx.xx 开头，其中 xx 是数字）。 客户应使用[清单标记](https://github.com/estesp/manifest-tool)来将多个平台作为目标。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 所有清单标记（最新标记除外）必须以 X.Y- 或 X.Y.Z- 开头，其中，X、Y 和 Z 为整数。 例如，如果最新标记指向 1.0.1-linux-x64、1.0.1-linux-arm32 和 1.0.1-windows-arm32，必须将这六个标记添加到此字段。 若要详细了解标记和版本控制，请参阅[准备 IoT Edge 模块技术资产](create-iot-edge-module-asset.md)。

### <a name="default-deployment-settings-optional"></a>默认部署设置（可选）

定义最常见的设置来部署 IoT Edge 模块。 通过让客户使用这些默认设置以“开箱即用”的方式启动 IoT Edge 模块来优化客户部署。

默认路由。 IoT Edge 中心管理模块、IoT 中心与设备之间的通信。 可以为模块和 IoT 中心之间的数据输入和输出设置路由，这样就能灵活地将消息发送到需要发送的位置，而无需使用其他服务来处理消息或编写额外的代码。 路由是使用名称/值对构造而成的。 最多可以定义五个默认路由名称，每个名称最长可包含 512 个字符。

请确保在路由值中使用正确的[路由语法](../../iot-edge/module-composition.md#declare-routes)（通常定义为从/消息/* 到 $upstream）。 也就是说，任何模块发送的任何消息都发送到 IoT 中心。 若要引用模块，请使用它的默认模块名称，即不包含空格或特殊字符的产品/服务名称。 若要引用未知的其他模块，请使用 <FROM_MODULE_NAME> 约定来告知客户他们需要更新此信息。 有关 IoT Edge 路由的详细信息，请参阅[声明路由](../../iot-edge/module-composition.md#declare-routes)）。

例如，如果模块 ContosoModule 侦听 ContosoInput 上的输入数据和 ContosoOutput 上的输出数据，最好定义以下两个默认路由：

- 名称 #1：ToContosoModule
- 值 #1：FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- 名称 #2：FromContosoModuleToCloud
- 值 #2：FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

默认模块孪生所需属性。 模块孪生是 IoT 中心内的 JSON 文档，用于存储模块实例状态信息，包括所需属性。 所需属性与报告的属性一起用于同步模块配置或条件。 解决方案后端可以设置所需属性，模块可以读取它们。 模块还可以接收所需属性的更改通知。 所需属性是使用最多五个名称/值对创建的，每个默认值必须小于 512 个字符。 最多可以定义五个名称/值孪生所需属性。 孪生所需属性的值必须是有效的 JSON、未转义且不包含数组，最多可包含四级嵌套层次结构。 如果默认值所需的参数（例如客户服务器的 IP 地址）没有意义，可以添加一个参数作为默认值。 若要详细了解成对出现的所需属性，请参阅[定义或更新所需属性](../../iot-edge/module-composition.md#define-or-update-desired-properties)。

例如，如果某个模块使用孪生所需属性支持可动态配置的刷新频率，最好定义以下默认孪生所需属性：

- 名称 #1：RefreshRate
- 值 #1：60

默认环境变量。 环境变量提供了有助于配置过程的模块的补充信息。 环境变量是使用名称/值对创建的。 每个默认环境变量名称和值都必须小于 512 个字符，并且最多可以定义五个。 如果默认值所需的参数（例如客户服务器的 IP 地址）没有意义，可以添加一个参数作为默认值。

例如，如果某个模块在启动之前需要接受使用条款，则你可以定义以下环境变量：

- 名称 #1：ACCEPT_EULA
- 值 #1：Y

默认容器创建选项。 容器创建选项指导 IoT Edge 模块 Docker 容器的创建。 IoT Edge 支持 Docker 引擎 API 创建容器选项。 有关所有选项，请查看[列出容器](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList)。 “创建选项”字段必须是有效的 JSON、未转义且小于 512 个字符。

例如，如果某个模块需要端口绑定，请定义以下创建选项：

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>审阅和发布

完成产品/服务的所有必需部分后，可以提交产品/服务以供审阅和发布。

在门户的右上角，选择“审阅和发布”。

在“审阅”页上，可以看到发布状态：

- 查看产品/服务的每个部分的完成状态。 除非产品/服务的所有部分都标记为“已完成”，否则无法发布。
    - 未启动 - 部分尚未启动，需要完成。
    - 未完成 - 部分有错误需要修复，或需要你提供更多信息。 有关指南，请参阅本文档前面的部分。
    - 完成 - 部分包含所有必需数据，并且没有错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保产品/服务得到正确测试。 同时，提供有助于了解产品/服务的补充说明。

若要提交产品/服务进行发布，请选择“发布”。

我们会向你发送电子邮件，让你知道何时有预览版产品/服务可供审阅和审核。 若要将产品/服务发布到公众，请前往 "合作伙伴中心" 并选择 "**上线**"。

## <a name="next-steps"></a>后续步骤

- [更新商业市场中的现有产品/服务](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)
