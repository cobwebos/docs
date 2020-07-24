---
title: 创建 SaaS 产品/服务、Azure Marketplace 和 Microsoft AppSource
description: 如何创建 "软件即服务" （SaaS）产品/服务（SaaS）产品/服务，以便使用 Microsoft 合作伙伴中心的 Microsoft 商业市场计划在 Microsoft AppSource、Azure Marketplace 或通过云解决方案提供商（CSP）计划进行列出或销售。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 2c5394dce503a6fa00e2a3e6ff73a683d3d2e76f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012087"
---
# <a name="create-a-saas-offer"></a>创建 SaaS 产品/服务

若要开始在商业市场中创建软件即服务 (SaaS) 产品/服务，请确保先[创建合作伙伴中心帐户](./create-account.md)，然后打开[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，并选择“概述”选项卡。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择“商业市场” > “概述”。
3. 在“概述”页，选择“+ 新建产品/服务” > “软件即服务”。

   ![左侧导航菜单图示。](./media/new-offer-saas.png)

> [!NOTE]
> 发布产品/服务后，在合作伙伴中心对其进行的编辑仅在重新发布该产品/服务后才会显示在店面中。 确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你的帐户中每个产品/服务的唯一标识符。

- 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（如果适用）。
- 只使用小写字母和数字。 其中可以包含连字符和下划线，但不能包含空格，并且不得超过 50 个字符。 例如，如果在此处输入“test-offer-1”，则产品/服务 Web 地址将为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 选择“创建”后，无法更改产品/服务 ID。

输入“产品/服务别名”。 这是用于合作伙伴中心的产品/服务的名称。

- 此名称不用于市场，并且与向客户显示的产品/服务名称和其他值不同。
- 选择“创建”后，无法更改产品/服务别名。

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

选择“创建”以生成产品/服务并继续。

## <a name="offer-overview"></a>产品/服务概述

发布状态显示发布此产品/服务所需执行的步骤以及完成每个步骤所需花费的时间的可视化表示形式。 未完成发布步骤的图标将显示为灰色。

“产品/服务概述”菜单包含用于对此产品/服务执行操作的链接的列表。 此操作列表将根据为产品/服务做出的选择而改变。  

- 如果产品/服务是草稿 - 删除草稿
- 如果产品/服务已上线 - 停止销售产品/服务
- 如果产品/服务为预览版 - 上线
- 如果尚未完成发布服务器注销-请取消发布

## <a name="offer-setup"></a>产品/服务设置

此页要求提供以下信息。

- **是否想要通过 Microsoft 销售？** （是/否）
    - 是，我想通过 Microsoft 进行销售并让 Microsoft 代表我主持交易
    - 否，我希望仅通过市场列出我的产品/服务并独立处理交易。

### <a name="sell-through-microsoft"></a>通过 Microsoft 进行销售

通过 Microsoft 进行销售可以更好地发现和获取客户，使 Microsoft 可以代表你主持市场交易，并能利用 Microsoft 遍布全球的商务能力。

#### <a name="saas-offer-requirements"></a>SaaS 产品/服务要求

若要在合作伙伴中心的商业市场上列出软件即服务 (SaaS) 产品/服务，必须满足以下条件：

- 你的产品/服务必须使用 [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) 进行标识管理和身份验证。
- 你的产品/服务必须使用 [SaaS 履行 API](pc-saas-fulfillment-api-v2.md) 与 Azure 市场集成。

#### <a name="saas-pricing-and-billing-options"></a>SaaS 定价和计费选项

使用在发布者的 Azure 订阅中运行的 SaaS 解决方案时，客户支付的许可证费用包括在其上部署软件的基础结构的费用。 Azure 基础结构使用情况由合作伙伴直接管理并向你收费。 客户将无法看到实际的基础结构使用费。 发布者应将 Azure 基础结构使用费捆绑到其软件许可证定价中。

SaaS 产品/服务支持根据固定费用、每位用户按月或按年计费，或使用按流量计费服务收取使用费。 Microsoft 商业市场基于代理模型运行，即发布者设定价格、Microsoft 向客户收费以及 Microsoft 向发布者支付收入并预扣代理费。

这是一个用于演示机构模型的成本和付款的示例细分（任何列出的价格仅用于举例目的，不打算反映实际成本）：

|**许可证费用**|**$100 美元/月**|
|:---|:---|
|Azure 使用费用（D1/1 核）|直接向发布者而不是客户收费|
|由 Microsoft 向客户收费|$100.00 美元/月（发布者必须将所有产生的或者转嫁的基础结构费用纳入许可证费用中）|

|**Microsoft 收费**|**$100 美元/月**|
|:---|:---|
|Microsoft 将许可证费用的 80% 支付给你 <br>*对于符合资格的 SaaS 应用，Microsoft 支付许可证费用的 90%|80.00 美元/月 <br>每月 $90.00 美元*|

- 在此示例中，Microsoft 向客户收取 $100.00 美元的软件许可证费用，并向发布者支付 $80.00 美元。

> [!NOTE]
> **降低 Marketplace 服务费用**–对于在商业市场上发布的某些 SaaS 产品/服务，microsoft 会将其 Marketplace 服务费用从20% 降低到10% （如 Microsoft 发布者协议中所述）。 对于你的产品/服务，你的产品/服务必须已由 Microsoft 指定为 Azure IP 共同销售 incentivized。 在每个日历月结束之前，资格必须至少满足五（5）个工作日，才能获得该月降低的 Marketplace 服务费用。 降低的 Marketplace 服务费用还适用于 Azure IP 共同销售 incentivized Vm、托管应用以及通过商业市场提供的任何其他合格事务 IaaS 产品/服务。

### <a name="list-through-microsoft"></a>通过 Microsoft 列出

借助创建市场列表，通过 Microsoft 促进业务发展。 选择仅列出产品/服务而不通过 Microsoft 进行交易意味着 Microsoft 不会直接参与软件许可证交易。 因此，不存在相关交易费用，发布者 100% 保留向客户收取的所有软件许可费用。 但是，发布者负责支持软件许可证交易的各个方面，包括但不限于：订单履行、计量、计费、开具发票、付款和费用收取。

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>立即获取（免费）

通过提供有效的地址（以*http*或*https*开头）向客户免费列出你的产品/服务，用户可以通过[使用 Azure Active Directory （Azure AD））通过一键式身份验证](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)获得试用。 例如，`https://contoso.com/saas-app`。

#### <a name="free-trial-listing"></a>免费试用版（列出）

通过提供有效的地址（以*http*或*https*开头），向客户列出你的产品/服务，用户可以通过[使用 Azure Active Directory （Azure AD），通过一次单击身份验证](../marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)获得试用版。 例如，`https://contoso.com/trial/saas-app` 。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序将通过试用链接收到的令牌只能用于通过 Azure AD 获取用户信息，从而在应用中自动创建帐户。 Microsoft 帐户 (MSA) 不支持使用此令牌进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关如何配置 CRM 的详细信息，请参阅[潜在顾客](#customer-leads)。

#### <a name="example-marketplace-offer-listing"></a>市场产品/服务列表示例

<!-- ![Example marketplace offer listing with notes](./media/marketplace-offer.svg) -->

下面是如何在 Microsoft AppSource 中显示产品/服务的示例：

:::image type="content" source="media/example-appsource-saas.png" alt-text="说明了此产品/服务在 Microsoft AppSource 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 大徽标
2. 类别
3. 行业
4. 支持地址（链接）
5. 使用条款
6. 隐私策略
7. 产品名称
8. 摘要
9. 描述
10. 屏幕截图/视频
11. 文档

<br>下面是有关如何在 Azure 门户中显示产品/服务的示例：

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="说明了此产品/服务在 Azure 门户中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. Title
2. 描述
3. 有用链接
4. 屏幕截图

## <a name="enable-a-test-drive"></a>启用体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，它为潜在客户提供“在购买前试用”这一选择，从而提高了转化率并开发出优质的潜在顾客。 [详细了解体验版](../what-is-test-drive.md)。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。

有关其他信息，请参阅[在商业市场中为你的产品/服务提供体验版](test-drive.md)。

### <a name="test-drive-resources"></a>体验版资源

- [什么是体验版？](../what-is-test-drive.md)
- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF；确保弹出窗口阻止程序处于禁用状态）

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>其他潜在顾客管理资源
- [潜在客户管理常见问题](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)）
- [常见潜在客户配置错误](../lead-management-for-cloud-marketplace.md#publishing-config-errors)）
- [潜在顾客管理概述单页广告](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

此页要求定义用于在市场上对产品/服务进行分组的类别和行业、支持产品/服务的法律合同以及应用版本。

### <a name="category"></a>类别

你的产品/服务将发布到 AppSource 或 Azure Marketplace，具体取决于与产品/服务关联的事务功能和你的类别选择。 有关详细信息，请参阅[比较 Microsoft AppSource 和 Azure Marketplace](../comparing-appsource-azure-marketplace.md) 。 选择最适合你的产品/服务和目标受众的类别和子类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别（可选）。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 "**不适用**"。

请参阅[产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中适用于每个店面的类别和子类别的完整列表。

### <a name="industries"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

行业选择仅适用于发布到 AppSource 的产品/服务。

### <a name="app-version"></a>应用版本

此字段是可选字段，在 AppSource 市场中用于标识产品/服务的版本号。

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商业市场标准协定

Microsoft 提供标准协定模板。

- **使用 Microsoft 商业市场标准协定？**

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供 Microsoft 商业市场标准协定，以帮助促进市场中的交易。 商业市场发布者可以选择根据标准协定提供软件，而不制定自定义条款和条件，客户只需要审查并接受一次即可。 可在 https://go.microsoft.com/fwlink/?linkid=2041178 找到标准协定。

你可以通过选中“使用商业市场标准协定”复选框来选择使用标准协定，而不提供自己的自定义条款和条件。

![使用标准协定复选框](./media/use-standard-contract.png)

> [!NOTE]
> 使用 Microsoft 商业市场标准协定发布产品/服务后，你将无法使用自己的自定义条款和条件。 这是一个“二选其一”方案。 你要么根据标准协定提供解决方案，要么根据自己的条款和条件提供解决方案。 如果想修改标准协定的条款，可以通过标准协定修订进行。

#### <a name="standard-contract-amendments"></a>标准协定修订

标准协定修订让发布者能够出于简单起见选择标准协定条款，同时为其产品或业务自定义条款。 如果客户已查看并接受了 Microsoft 标准协定，则只需要查看协定的修订部分。

有两种修正可供商业市场发行商使用：

- 通用修正：这些修正内容适用于所有客户的标准协定。 在购买流程中，将向产品/服务的所有客户显示通用修正。 客户在使用产品/服务之前，必须接受标准协定的条款和修正内容。
- 自定义修正：这些修正是对标准协定的特殊修正内容，仅通过 Azure 租户 ID 面向特定客户提供。 发行商可以选择要面向的租户。 只有租户中的客户才能在产品/服务的购买流程中看到自定义修正条款。  客户在使用产品/服务之前，必须接受标准协定的条款和修正。

>[!NOTE]
> 这两种修正相互叠加。 在购买期间，自定义修订所面向的客户还将看到标准协定的通用修订。

**Microsoft 商业市场标准协定通用修订条款** - 在此框中输入通用修订条款。 你可以为每个产品/服务提供一个通用修订。 可以在此框中输入无限数量的字符。 在发现和购买流程中，这些条款会在 AppSource、Azure 市场和/或 Azure 门户中向客户显示。

**Microsoft 商业市场标准协定自定义修订条款** - 首先选择“添加自定义修订条款”。 每个产品/服务最多可以提供 10 个自定义修订条款。

- **自定义修订条款** - 在“自定义修订条款”框中输入自定义修订条款。 你可以在此框中输入无限数量的字符。 在 Azure 门户的产品/服务购买流程中，只会向为这些自定义条款指定的租户 ID 中的客户显示自定义修订条款。  
- **租户 ID**（必需）- 每个自定义修订最多可以面向 20 个租户 ID。 如果添加自定义修订，则必须至少提供一个租户 ID。 租户 ID 标识 Azure 中的客户。 你可以要求客户提供此 ID，他们可通过导航到 portal.azure.com >“Azure Active Directory”>“属性”来找到它。 目录 ID 值即为租户 ID（例如，50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用其域名 URL 查找你的客户的租户 ID [，"我的 Microsoft Azure 和 Office 365 租户 id 是什么？](https://www.whatismytenantid.com)
- **说明**（可选）- 可以选择为租户 ID 提供易记说明，以帮助确定修订所面向的客户。

#### <a name="terms-and-conditions"></a>条款和条件

如果要提供自己的自定义条款和条件，可以选择在条款和条件字段中输入它们。 最多可在此字段中输入 10,000 个字符的文本。 如果条款和条件需要更长的说明，请在此字段中输入指向可找到你的条款和条件的位置的单个 URL 链接。 它将作为活动链接向客户显示。

客户必须接受这些条款才能试用产品/服务。

选择“保存草稿”，然后继续操作。

## <a name="offer-listing"></a>产品/服务列表

此页显示提供产品/服务的语言（和市场），当前仅英语（美国）可用。 此外，此页还显示特定于语言的列表的状态及其添加日期/时间。 你将需要为每种语言/市场定义市场详细信息（产品/服务名称、说明、搜索词等）。

> [!NOTE]
> 只要产品/服务说明使用以下短语开头：“此应用程序仅以[非英语语言]提供”，产品/服务列表内容（例如产品/服务说明、文档、屏幕截图、使用条款和隐私策略）就不需要是英语。 还可以提供一个有用链接 URL，使用与产品/服务列表内容中所使用的语言不同的语言提供内容。

### <a name="offer-listings"></a>套餐列表

提供要在市场中显示的详细信息，包括对产品/服务和市场营销资产的说明。

- **名称**（必需）- 此处定义的名称将作为产品/服务列表的标题在所选市场上显示。 该名称根据之前的“新产品/服务”条目预先填充。 该名称可以是商标字。 它不能包含表情符号（除非它们是商标和版权符号），并且长度必须限制为 50 个字符。
- **摘要**（必需）- 提供要在市场列表搜索结果中使用的产品/服务的简短说明。 最多可在此字段中输入 100 个字符的文本。
- **说明**（必需）- 提供要在市场列表概述中显示的产品/服务的说明。 考虑包括价值主张、关键优势、任何类别或行业关联、应用内购买机会、任何所需的披露以及用于了解详细信息的链接。 最多可在此字段中输入 3,000 个字符的文本，包括标记。 有关其他提示，请参阅[编写出色的应用说明](/windows/uwp/publish/write-a-great-app-description)。
- **搜索关键字** - 最多可输入三个搜索关键字，客户可以使用它们在市场中查找你的产品/服务。
- **入门说明**（必需）- 向潜在客户说明如何配置和开始使用你的应用。  此快速入门可以包含指向更详细的联机文档的链接。 最多可在此字段中输入 3,000 个字符的文本。

#### <a name="description"></a>说明

此字段是必填的。

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="links"></a>链接

- **隐私策略**（必需）- 链接到你的组织的隐私策略。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略
- **CSP 计划市场营销材料**（可选）- 如果你选择将产品/服务扩展到[云解决方案提供商 (CSP)](../cloud-solution-providers.md) 计划，请提供市场营销材料的链接。 CSP 通过使 CSP 合作伙伴能够捆绑、营销和转售你的产品/服务，将你的产品/服务扩展到更广泛的合格客户。 这些经销商将需要能够访问用于营销你的产品/服务的材料。 有关详细信息，请参阅[进入市场服务](https://partner.microsoft.com/reach-customers/gtm)。
- **有用链接**（可选）- 通过提供“标题”和“URL”，列出有关你的应用或相关服务的可选补充联机文档。 通过单击“+ 添加 URL”添加其他有用链接。

#### <a name="contact-information"></a>联系信息

- **联系人**：对于每个客户联系人，提供员工姓名、电话号码和电子邮件地址（这些信息不会公开显示）。 “支持联系人”组需要提供“支持 URL”（将公开显示此信息）。

    - **支持联系人**（必需）- 用于一般支持问题。
    - **工程联系人**（必需）- 用于技术问题。
    - **渠道经理联系人**（必需）- 用于与 CSP 计划相关的经销商问题。

#### <a name="files-and-images"></a>文件和图像

- **文档**（必需）-为产品/服务添加相关营销文档（PDF 格式），其中至少有一个和多达三个文档。
- **图像**（可选）- 市场上的多个位置可能会显示你的产品/服务的徽标图像，这要求以 PNG 格式提供以下像素大小：

    - **小**（48 x 48，必需）
    - **中**（90 x 90，必需）
    - **大**（216 x 216，必需）
    - **宽** (255 x 115)

- **屏幕截图**（必需）–添加最多5个屏幕截图，其中演示了产品/服务，大小为 1280 x 720 像素。 所有图像都必须为 .PNG 格式。
- **视频**（可选）- 添加演示产品/服务的视频的链接。 可以使用 YouTube 和/或 Vimeo 视频链接，这将随产品/服务一起呈现给客户。 还需要以 PNG 格式输入视频的缩略图，大小为 1280 x 720 像素。 每个产品/服务最多可以显示四个视频。

>[!NOTE]
>如果上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

- [市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)

选择“保存草稿”，然后继续操作。

## <a name="preview-audience"></a>预览版受众

借助此页，可以定义有限的预览版受众，以便在向更广泛的市场受众发布产品/服务之前发布产品/服务。

> [!IMPORTANT]
> 检查预览版的产品/服务后，选择“上线”，以便向市场公共受众发布产品/服务。

每行添加一个 AAD/MSA 帐户电子邮件，以及可选的说明。

为现有 Microsoft 帐户 (MSA) 或 Azure Active Directory 帐户手动添加最多 10 个电子邮件地址（如果上传 CSV 文件，则最多添加 20 个电子邮件地址），以帮助在发布之前验证产品/服务。 通过添加这些帐户，你可定义在将产品/服务发布到市场之前，允许对其进行预览访问的用户。 如果产品/服务已上线，仍然可以定义预览版受众，以测试对产品/服务的任何更改或更新。

> [!NOTE]
> 预览版受众与专用受众不同。 预览版受众可以在发布到市场之前访问你的产品/服务。 你也可以选择创建一个计划并仅面向专用受众提供。 在“计划列表”选项卡中，你可以通过“这是专用计划”复选框来定义专用受众。 然后，你可以使用 Azure 租户 ID 定义最多包含 20,000 个客户的专用受众。

选择“保存草稿”，然后继续。

## <a name="technical-configuration"></a>技术配置

"**技术配置**" 选项卡定义 marketplace 用于与 SaaS 服务通信的技术详细信息。 通过此连接，我们可以为最终用户预配你的产品/服务，前提是他们选择获取和管理产品。 

>[!Note]
>在产品/服务的详细信息中配置这些详细信息之前，必须实现与[SaaS 履单 api](./pc-saas-fulfillment-api-v2.md)的集成。

适用于[api](./pc-saas-fulfillment-api-v2.md)的文档中提供了介绍所收集字段的用法的关系图和详细说明。

- **登陆页面 url** （必需）–定义 `https://contoso.com/signup` 最终客户从 marketplace 获取产品/服务并从新创建的 SaaS 订阅触发配置过程后，最终客户将居住在其上的 SaaS 网站 url （例如：）。  此 URL 将与 marketplace 购买标识令牌参数一起调用，此参数唯一标识特定最终客户的 SaaS 购买。  必须使用[解析](./pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)API 为相应的 SaaS 订阅详细信息交换此标记。  应将这些详细信息以及要收集的任何其他内容用作客户交互式网页的一部分，以完成最终客户注册并激活其购买。  在此页上，用户应使用 Azure Active Directory （Azure AD）注册一次单击身份验证。 <br> <br> 当最终用户从 Azure 门户或 M365 管理中心启动托管 SaaS 体验时，还将调用具有 marketplace 购买标识令牌参数的 URL。 你应在为新客户购买后首次提供令牌时处理两个流，并为现有客户提供该令牌，以便管理其 SaaS。 <br> <br> 你在此处配置的登录页面应已启动并运行24/7。 这是你在 marketplace 中新购买的 SaaS 产品或产品/服务的活动订阅的配置请求时，你将收到的通知。

- **连接 webhook** （必需）–对于 Microsoft 需要发送给你的所有异步事件（例如，已取消 SaaS 订阅），我们要求你提供连接 webhook URL。 我们将调用此 URL 来通知你事件。 <br> <br> 提供的 webhook 应启动并运行24/7，因为这是你将会收到有关通过 marketplace 购买的客户 SaaS 订阅的更新的通知。 如果尚未准备好 webhook 系统，最简单的配置是创建一个 HTTP 终结点逻辑应用，该应用将侦听任何发送给它的事件，并相应地处理这些事件（例如 `https://prod-1westus.logic.azure.com:443/work` ）。 有关详细信息，请参阅[在逻辑应用中使用 HTTP 终结点调用、触发或嵌套工作流](../../logic-apps/logic-apps-http-endpoint.md)。

- **Azure AD 租户 ID** （必需）–在 Azure 门户内部，我们要求你[创建 Azure Active Directory （AD）应用](../../active-directory/develop/howto-create-service-principal-portal.md)，以便我们可以验证两个服务之间的连接是否在经过身份验证的通信之后。 若要查找[租户 ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)，请前往 Azure Active Directory，选择 "**属性**"，然后查找列出的**目录 ID**号（如50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用 id** （必需）–还需要[应用程序 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 若要获取其值，请转到 Azure Active Directory 并选择**应用注册**，然后查找列出的**应用程序 ID**号（例如 `50c464d3-4930-494c-963c-1e951d15360e` ）。

>[!Note]
>Azure AD 应用 ID 关联到合作伙伴中心帐户中的发布者 ID。 请确保在所有产品/服务中使用相同的应用程序 ID。

>[!Note]
>如果发布者在合作伙伴中心有两个或更多不同的帐户，则应使用两个或多个不同的 Azure AD 应用 Id，每个帐户对应于一个帐户。 对于通过此帐户发布的所有 SaaS 产品/服务，伙伴中心中的每个合作伙伴帐户都应使用唯一 Azure AD 应用 ID。

选择“保存草稿”，然后继续。

## <a name="plan-overview"></a>计划概述

借助此页，可以在同一产品/服务中提供各种计划选项。 这些计划（以前称为 Sku）在版本、盈利或服务层方面可能会有所不同。 你至少必须设置一个计划，以在 marketplace 中销售你的产品/服务。

创建后，你将看到计划名称、ID、定价模型，可用性（公用或专用）、当前发布状态以及任何可用的操作。

“计划概述”中可用的“操作”根据计划的当前状态而有所不同，其中可能包括：

- 如果计划状态为“草稿”- 删除草稿
- 如果计划状态为“上线”- 停止销售计划或同步专用受众

**创建新计划**（对于选择通过 Microsoft 进行销售的发布者而言，至少要有一个计划）

- **计划 ID：** 为此产品/服务中的每个计划创建唯一的计划 ID。 客户可以在产品 URL 和 Azure 资源管理器模板（如果适用）中看到此 ID。 仅使用小写的字母数字字符、短划线或下划线。 此计划 ID 最多允许使用 50 个字符。 选择创建后无法修改该 ID。
- **计划名称：** 客户在决定要选择产品/服务中的哪个计划时会看到此名称。 为此产品/服务中的每个计划创建唯一的产品/服务名称。 计划名称用于区分可能属于同一产品/服务的软件计划（例如，产品/服务名称：Windows Server；计划：Windows Server 2016、Windows Server 2019）。

### <a name="plan-listing"></a>计划列表

借助此页，可以定义计划名称和说明。 <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **名称** - 根据预览版“新计划”条目预先填充，并且将作为产品/服务的“软件计划”的标题在市场中显示。
- **说明** - 此说明可用于说明此软件计划的与众不同之处，以及与产品/服务中的其他软件计划的任何区别。 最多可包含 500 个字符。

选择“保存草稿”，然后继续。

#### <a name="pricing-and-availability"></a>定价和可用性

借助此页，可以配置将提供此计划的市场、所需的盈利模型、价格和计费期限。 此外，你还可以指示是让计划对所有人可见，还是仅对特定客户（专用受众）可见。

#### <a name="markets-optional"></a>市场（可选）

每个计划必须至少在一个市场中提供。 选择“编辑市场”，然后选中要在其中提供此计划的任何市场位置的复选框。 此页面包含一个搜索框和选项，用于选择["已汇款的国家/地区](tax-details-paid-transactions.md)"，其中，Microsoft 汇寄的销售额和使用条款。

如果已采用美元 (USD) 为计划设置了价格并添加另一个市场位置，则系统将根据当前汇率计算用于新市场的价格。 发布之前，请查看用于每个市场的价格。 保存更改后，使用“导出价格(xlsx)”链接查看价格。

选择“保存”，然后继续。

#### <a name="pricing"></a>定价

##### <a name="pricing-model"></a>定价模型

**固定费率** - 支持按单月或年度统一费率价格获取产品/服务。 这有时称为基于站点的定价。 借助此定价模型，你可以选择定义按流量计费计划，该计划使用市场按流量计费服务 API 根据非标准单位向客户收费。  有关按流量计费的详细信息，请参阅[使用市场按流量计费服务进行按计量计费](./saas-metered-billing.md)。  如果使用行为在 SaaS 服务中出现突发，还应使用此选项。  我们不建议客户在每天或每小时的基础上频繁切换计划。

**按用户** - 支持按根据访问产品/服务或占用席位的用户数量确定的价格获取产品/服务。 这一基于用户的模型使你可以根据价格设置允许的最小和最大用户数。 这样一来，可以通过配置多个计划，根据用户数量配置不同的价格点。  这些字段是可选的。 如果不选择，则用户数量将被解释为没有限制（最小值为 1，最大值为系统可以支持的数量）。 这些字段可以作为计划更新的一部分进行编辑。

发布后，无法更改计费定价模型选择。 此外，同一产品/服务的所有计划必须共享同一定价模型。

##### <a name="user-limits"></a>用户限制

如果适用，请选择并设置最小和最大用户数限制。

##### <a name="billing-term-and-price"></a>计费期限和价格

选择客户必须支付所列价格的期限和价格。 至少必须提供一个月度或年度价格，也可以同时向客户提供这两个选项。

以 USD（USD = 美元）设置的价格在保存时会使用当前汇率转换为所有选定市场的本地货币。 在发布之前，通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单个市场中设置自定义价格，请修改并导入定价电子表格。 你负责验证此定价并拥有这些设置。
\*必须先保存定价更改，才能启用定价数据导出。

发布之前，请仔细检查价格，因为在发布计划后，对可以更改的内容会有一些限制：

- 计划发布后，定价模型无法更改。
- 一旦发布计划的计费期限，之后无法将其删除。
- 一旦发布计划中某个市场的价格，之后无法更改。

#### <a name="free-trial"></a>免费试用版

通过商业应用商店提供的 SaaS 产品可让你在通过 Microsoft 销售时提供一个月免费试用版。 对于除按流量计费计划外的所有计费模型和期限，均支持免费试用版。 通过此选项，客户可以通过一个月的免费访问权限获得较低的准入门槛。  如果选择为产品/服务中的计划启用免费试用版，则客户将无法在最初的一个月期限结束前转换为付费订阅。  在此期间，购买产品/服务的客户可以试用启用了免费试用版的任何受支持的计划，并在它们之间进行转换。  在该期限结束时，自动完成到付费订阅的转换。

>[!NOTE]
>如果客户选择转换为没有免费试用版的计划，则将进行转换，但免费试用版将立即失效。 同样，一旦客户开始为某个计划付费，即使他们转换为支持免费试用版的 SKU，也不再能够重新获得相同订阅的免费试用版。

可在此处为产品/服务中的每个计划配置免费试用版。 选中复选框以允许一个月试用版。

![一个月免费试用版复选框](./media/free-trial-enable.png)

>[!NOTE]
>一旦可交易的产品/服务发布免费试用版，就不能在该计划中将其禁用。 确保此设置在第一次发布时正确，以免必须重新创建计划。

若要获取有关当前参与免费试用版的客户订阅的信息，请使用新 API 属性 `isFreeTrial`，该属性将标记为 true 或 false。 有关详细信息，请参阅 [SaaS 获取订阅 API](pc-saas-fulfillment-api-v2.md#get-subscription)。

>[!NOTE]
>利用市场按流量计费服务的计划不支持免费试用版。

#### <a name="plan-visibility"></a>计划可见性

可以将每个计划配置为对每个人都可见，或者仅对所选择的特定受众可见。 可以使用 Azure AD 租户 ID 在此受限受众中分配成员身份。

##### <a name="privacy"></a>隐私

选择“这是专用计划”可使计划成为专用计划，并且仅对所选择的受限受众可见。 作为专用计划发布后，可以更新受众或选择向每个人提供该计划。 将计划发布为对每个人都可见后，它便必须保持对每个人都可见（无法重新配置为专用计划）。

##### <a name="restricted-audience-tenant-ids"></a>**受限受众（租户 ID）**

分配将有权访问此专用计划的受众。 使用租户 ID 分配访问权限，并可以选择包括对分配的每个租户 ID 的说明。 最多可以添加 10 个租户 ID；如果导入 .csv 电子表格文件，则最多可以添加 20,000 个客户租户 ID。

租户是组织的表示形式，其 ID 表示为 GUID（全局唯一标识符，用于标识资源的 128 位整数）。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如，注册 Azure、Microsoft Intune 或 Microsoft 365 时）会收到该实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户。 若要检查租户，请使用要用于管理应用程序的帐户登录 Azure 门户。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录/租户 ID (GUID) 以及域。 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。 你也可以在 [https://www.whatismytenantid.com](https://www.whatismytenantid.com) 使用域名 URL 查找组织的租户 ID。

虽然 SaaS 产品/服务使用租户 ID 定义专用受众，但其他产品/服务类型可能会使用 Azure 订阅 ID（也表示为 GUID）。

> [!NOTE]
> 专用受众（又称为受限受众）与预览版受众不同。 在“[预览](#preview-audience)”页，可以定义预览版受众。 允许预览版受众在将产品/服务发布到市场之前访问产品/服务。 专用受众指定仅适用于特定计划，而预览版受众可以查看所有计划（专用或非专用），但只能在测试和验证计划时的有限预览期内查看。

选择“保存草稿”，然后继续。

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>市场产品/服务中的计划列表示例

:::image type="content" source="media/marketplace-plan.png" alt-text="带有说明的 marketplace 计划列表示例。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 计划名称
2. 计划说明

<br>

## <a name="cloud-solution-provider-csp-reseller-audience"></a>云解决方案提供商 (CSP) 经销商受众

选择在 CSP 计划中提供产品/服务使云解决方案提供商可以将你的产品作为捆绑解决方案的一部分出售给其客户。 有关详细信息，请参阅[云解决方案提供商](https://go.microsoft.com/fwlink/?linkid=2111109)。

## <a name="publish"></a>发布

完成产品/服务的所有必需部分后，请选择门户右上角的“查看和发布”。

### <a name="submit-offer-to-preview"></a>提交产品/服务进行预览

如果这是第一次发布此产品/服务，则可以：

- 查看产品/服务各个部分的完成状态。
    - **未开始** - 尚未涉及该部分，需要完成。
    - **未完成** - 本部分包含需要修复的错误或需要提供详细信息。 需要返回该部分并进行更新。
    - **完成** - 该部分已完成，已提供所有必需的数据，并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保正确测试您的应用程序，以及任何有助于了解应用程序的补充说明。
- 通过选择“提交”来提交要发布的产品/服务。 我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。 返回到 "合作伙伴中心" 并选择 "**上线**"，将产品/服务发布到公共用户（或专用用户）。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
