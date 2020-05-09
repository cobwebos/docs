---
title: 为 Microsoft 商业应用商店创建新的 SaaS 产品/服务
description: 如何创建新的软件即服务（SaaS）产品/服务，以便使用 Microsoft 合作伙伴中心的 Microsoft 商业市场计划在 Microsoft AppSource、Azure Marketplace 或通过云解决方案提供商（CSP）计划进行列表或销售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: b85846419ce5f6cd4093e198d83240817c95754a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983051"
---
# <a name="create-a-new-saas-offer-in-the-commercial-marketplace"></a>在商业应用商店中创建新的 SaaS 产品/服务

若要开始在商业应用商店中创建软件即服务（SaaS）产品/服务，请确保先[创建合作伙伴中心帐户](./create-account.md)，并[打开 "](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)概述" 选项卡，并选择 "**概述**" 选项卡。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新建产品** > /服务" "**软件即服务**"。

   ![阐释左侧导航菜单。](./media/new-offer-saas.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

- 适用于 marketplace 产品/服务和 Azure 资源管理器模板的 web 地址中的客户可看到此 ID （如果适用）。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果在此处输入了 "**测试/服务-1** "，则 "产品/ `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`服务" 网址为。
- 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

- 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
- 选择 "**创建**" 后，不能更改该产品/服务别名。

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-overview"></a>产品/服务概述

**发布状态**显示发布此产品/服务所需步骤的直观表示形式，以及完成每个步骤所需的时间。 不完整的发布步骤图标将灰显。

"**产品概述**" 菜单包含用于对此产品/服务执行操作的链接的列表。 此操作列表将根据你为产品/服务所做的选择而更改。  

- 如果产品/服务是草稿版-删除草稿
- 如果产品/服务是 live-停止销售优惠
- 如果产品/服务为预览版-上线
- 如果尚未完成发布服务器注销-请取消发布

## <a name="offer-setup"></a>产品/服务设置

此页要求提供以下信息。

- **是否想要通过 Microsoft 销售？** （是/否）
    - **是**，我想要通过 microsoft 销售并代表我的 microsoft 主机交易
    - **不**，我不希望只通过市场和单独处理事务来列出我的产品/服务。

### <a name="sell-through-microsoft"></a>通过 Microsoft 进行销售

通过 Microsoft 进行销售可提供更好的客户发现和收购，使 Microsoft 能够代表你托管 marketplace 交易，并利用 Microsoft 的全球可用商业功能。

#### <a name="saas-offer-requirements"></a>SaaS 产品/服务要求

若要在合作伙伴中心列出 "软件即服务" （SaaS）产品/服务，必须满足以下条件：

- 你的产品/服务必须使用[Azure Active Directory （Azure AD）](https://azure.microsoft.com/services/active-directory/)来进行身份管理和身份验证。
- 你的产品/服务必须使用[SaaS 履单 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)与 Azure Marketplace 集成。

#### <a name="saas-pricing-and-billing-options"></a>SaaS 定价和计费选项

使用在发布者的 Azure 订阅中运行的 SaaS 解决方案，客户支付的许可费用包括部署软件所需的基础结构的成本。 Azure 基础结构的使用情况是直接管理和计费的。 客户将无法看到实际的基础结构使用费。 发布者应将 Azure 基础结构使用费用捆绑到其软件许可证定价中。 

SaaS 使用按流量计费服务，根据固定费用、每用户或消耗费用支持每月或每年计费。 Microsoft 的商业市场运营于代理商模型，使出版商设置价格、Microsoft 帐单客户和 Microsoft 将收入支付给出版商，同时为代理商收费。

这是一个示例，用于演示代理模型：

|**你的许可证费用**|**每月 $100**|
|:---|:---|
|Azure 使用费用（D1/1 核）|直接向发布者而不是客户收费|
|由 Microsoft 向客户收费|每月 $100.00 （发布者必须按许可费考虑任何产生的或通过的基础结构成本）|

|**Microsoft 收费**|**每月 $100**|
|:---|:---|
|Microsoft 将许可证费用的 80% 支付给你 <br>**对于合格的 SaaS 应用，Microsoft 将支付90% 的许可证成本*|80.00 美元/月 <br>*$* 每月 90.00 *|

- 在此示例中，$100.00 Microsoft 向客户收取软件许可证，并向发布者支付 $80.00。
- 符合**Marketplace 服务费用**的合作伙伴将从5月2019到6月5日起，从 5 2020 月5日起，对 SaaS 产品/服务的事务费用将减少。 在此方案中，Microsoft 为你的软件许可证计费 $100.00，并向发布者支付 $90.00。

> [!NOTE]
> **降低 Marketplace 服务费用**–对于在我们的商业市场上发布的某些 SaaS 产品/服务，microsoft 会将其 Marketplace 服务费用从20% 降低到10% （如 Microsoft 发布者协议中所述）。 为了使你的产品/服务符合资格，Microsoft 已将至少一个产品/服务指定为 "IP 共同销售就绪" 或 "IP 共同销售"。 在每个日历月结束之前，必须在每个日历月结束至少五（5）个工作日内获得资格，以便每月获得更少的 Marketplace 服务费用。  降低的 Marketplace 服务费用不适用于 Vm、托管应用或通过我们的商业市场提供的任何其他产品。 降低的 Marketplace 服务费用仅适用于 Microsoft 在5月1日、2019年6月 30 2020 日之间收集的许可费用。 此时间过后，Marketplace 服务费用将恢复为其正常量。

### <a name="list-through-microsoft"></a>通过 Microsoft 列出

通过创建 marketplace 列表，通过 Microsoft 提升你的业务。 选择仅列出你的产品/服务，而不是通过 Microsoft，这意味着 Microsoft 不会直接参与软件许可证交易。 没有关联的事务费用，发布者将保留从客户那里收集的任何软件许可费用的100%。 但是，发布者负责支持软件许可证事务的所有方面，包括但不限于：订单执行、计量、计费、开票、支付和收集。

<!-- - **How do you want potential customers to interact with this listing offer?** -->

#### <a name="get-it-now-free"></a>立即获取（免费）

通过提供有效的地址（以*http*或*https*开头）向客户免费列出你的产品/服务，用户可以通过[使用 Azure Active Directory （Azure AD）通过一键式身份验证](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)获得试用。 例如 `https://contoso.com/saas-app`。

#### <a name="free-trial-listing"></a>免费试用（列出）

通过提供有效的地址（以*http*或*https*开头）向客户列出你的产品/服务，用户可以通过[使用 Azure Active Directory （Azure AD）通过一键式身份验证](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)获得试用版。 例如 `https://contoso.com/trial/saas-app`。 产品/服务列表免费试用版由您的服务创建、管理和配置，并且没有 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序通过试用链接接收的令牌只能用于通过 Azure AD 获取用户信息，以便在应用中自动创建帐户。 Microsoft 帐户（MSA）不支持使用此令牌进行身份验证。

#### <a name="contact-me"></a>与我联系

通过连接客户关系管理（CRM）系统来收集客户联系信息。 系统将要求客户提供共享其信息的权限。 这些客户详细信息以及他们找到你的产品/服务的产品/服务名称、ID 和 marketplace 源将发送到已配置的 CRM 系统。 有关配置 CRM 的详细信息，请参阅[连接潜在客户管理](#connect-lead-management)。

#### <a name="example-marketplace-offer-listing"></a>市场套餐商品示例

![带有说明的 marketplace 产品/服务示例列表](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>启用体验版

通过向客户提供 "在购买前试用" 的选项，测试驱动器是向潜在客户展示你的产品/服务的一种很好的方法，导致增加转换和产生高度合格的潜在顾客。 [了解有关测试驱动器的详细信息](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用测试驱动器，请选中 "**启用测试驱动器**" 复选框。 若要从产品/服务中删除测试驱动器，请清除此复选框。

有关其他信息，请参阅[在商业应用商店中试用产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

### <a name="test-drive-resources"></a>测试驱动器资源

- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF; 请确保弹出窗口阻止程序处于关闭状态）

### <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>其他潜在客户管理资源
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述 One 寻呼](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

选择 "**保存草稿**"，然后继续。

## <a name="properties"></a>属性

本页面要求你定义用于对你的产品/服务、支持你的产品/服务和应用程序版本进行分组的类别和行业。

### <a name="category"></a>类别

选择至少一个和三个类别，将产品/服务分组到适当的 marketplace 搜索区域。 在产品/服务描述中介绍产品/服务如何支持这些类别。

### <a name="industries"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本

此字段是可选的，在 AppSource marketplace 中用于标识产品的版本号。

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商用 marketplace 的标准协定

Microsoft 提供了标准合同模板。

- **使用 Microsoft 商业应用商店的标准约定？**

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 为 Microsoft 商用 marketplace 提供了标准合同，有助于促进 marketplace 中的交易。 商业 marketplace 发布者可以选择以标准合同提供其软件，而客户只需来审查并接受一次，而不是手工编写自定义条款和条件。 可以在https://go.microsoft.com/fwlink/?linkid=2041178中找到标准协定。

您可以选择使用标准合同，而不是通过选择 "为商业应用商店使用标准协定" 复选框来提供自己的自定义条款和条件。

![使用 "标准协定" 复选框](./media/use-standard-contract.png)

> [!NOTE]
> 使用 Microsoft 商业应用商店的标准合同发布产品/服务后，将无法使用自己的自定义条款和条件。 这是一个 "或" 方案。 你可以在标准合同**或**你自己的条款和条件下提供解决方案。 如果要修改标准协定的条款，可以通过标准协定改正来实现。

#### <a name="standard-contract-amendments"></a>标准协定改正

标准协定改正允许发布者为简单起见选择标准协定术语，并自定义其产品或企业的条款。 如果客户已查看并接受 Microsoft 标准合同，则他们只需要查看对约定的修正。

对于商业 marketplace 发布者，可以使用两种改正：

- 通用修正：这些修正适用于所有客户的标准协定。 通用修正在购买流程中的每个产品/服务的客户上显示。 客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。
- 自定义修正：这些修正是仅通过 Azure 租户 Id 面向特定客户的标准协定的特殊修正。 发布者可以选择要以其为目标的租户。 只有租户中的客户会看到产品/服务购买流程中的自定义修订条款。  客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。

>[!NOTE]
> 这两种类型的改正堆栈彼此之上。 以自定义修正为目标的客户也会在购买过程中获得标准合同的通用修正。

**适用于 Microsoft 商用 Marketplace 标准合同的通用修正条款**–在此框中输入通用修订条款。 可以为每个产品/服务提供单个通用修订。 您可以在此框中输入不限数量的字符。 在发现和购买流程期间，会向 AppSource、Azure Marketplace 和/或 Azure 门户中的客户显示这些条款。

**适用于 Microsoft 商用 Marketplace 标准合同的自定义修订条款**–首先选择 "**添加自定义修订条款**"。 最多可为每个产品提供10个自定义修订条款。

- **自定义修订条款**–在自定义修订条款框中输入自定义修订条款。 您可以在此框中输入不限数量的字符。 只有你为这些自定义术语指定的租户 Id 的客户才会在 Azure 门户的产品/服务购买流程中提供自定义修订条款。  
- **租户 id** （必需）–每个自定义修正最多可针对20个租户 id。 如果添加自定义修正，则必须至少提供一个租户 ID。 租户 ID 用于标识 Azure 中的客户。 可以通过导航到 portal.azure.com > Azure Active Directory > 属性来向客户提供此 ID。 目录 ID 值是租户 ID （例如，50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用其域名 URL 查找你的客户的租户 ID，"[我的 Microsoft Azure 和 Office 365 租户 id 是什么？"](https://www.whatismytenantid.com)。
- **描述**（可选）-（可选）-根据需要提供适用于租户 ID 的友好描述，以帮助你确定要使用修正的客户。

#### <a name="terms-and-conditions"></a>条款和条件

如果要提供自己的自定义条款和条件，则可以选择在 "条款和条件" 字段中输入它们。 最多可在此字段中输入10000个字符的文本。 如果你的条款和条件需要较长的说明，请在此字段中输入一个 URL 链接，你可以在其中找到你的条款和条件。 它将向客户显示为活动链接。

客户必须接受这些条款，然后才能试用你的产品/服务。

选择 "**保存草稿**"，然后继续。

## <a name="offer-listing"></a>产品/服务列表

此页面显示提供产品/服务的语言（和市场），当前只有英语（美国）是可用的位置。 此外，此页还显示特定于语言的列表的状态以及添加它的日期/时间。 需要为每种语言/市场定义 marketplace 详细信息（产品名称、说明、搜索词等）。

> [!NOTE]
> 如果产品/服务说明以短语开头，则产品/服务列表内容（例如产品/服务、文档、屏幕截图、使用条款和隐私策略）无需使用英语，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个*有用的链接 URL*来提供与产品/服务列表内容中所用语言不同的内容。

### <a name="offer-listings"></a>套餐列表

提供要在 marketplace 中显示的详细信息，包括产品/服务和市场营销资产的说明。

- **名称**（必填）–在此处定义的名称将显示为你选择的 marketplace 中的产品/服务列表的标题。 该名称根据你以前的**新产品/服务**条目预填充。 名称可以是商标字。 它不能包含表情符号（除非它们是商标和版权符号），并且必须限制为50个字符。
- **摘要**（必需）–提供要在 marketplace 列表搜索结果中使用的产品/服务的简短说明。 最多可在此字段中输入100个字符的文本。
- **说明**（必需）–提供产品/服务的说明，以便在 marketplace 列表概述中显示。 考虑包括价值主张、关键优势、任何类别或行业关联、应用内购买机会、任何所需的披露，以及用于了解详细信息的链接。 在此字段中最多可以输入3000个字符（包括标记）。 有关其他提示，请参阅[编写卓越的应用说明](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)。
- **搜索关键字**–输入最多三个搜索关键字，客户可以使用这些关键字在 marketplace 中查找你的产品/服务。
- **入门说明**（必需）-介绍如何配置和开始使用应用来实现潜在的客户。  此快速入门可以包含指向更详细的联机文档的链接。 最多可在此字段中输入3000个字符的文本。

#### <a name="description"></a>**说明**

此字段是必需的。 要包括在**说明**中的项：

* 在前几句描述中清晰描述产品/服务的价值主张。  
* 请牢记，前几句话可能在搜索引擎结果中显示。  
* 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
* 尽可能多使用行业特定用语或基于优势的措辞。

值主张的核心组件应包括：

* 该产品的说明
* 受益于该产品的用户类型
* 产品地址的客户需求或难点

若要使您的产品/服务说明更具吸引力，请使用富文本编辑器应用格式设置。

![使用富文本编辑器](./media/rich-text-editor.png)

| <center>更改文本格式 | <center>添加项目符号或编号 | <center>添加或删除文本缩进 |
| --- | --- | --- |
| <center>![使用富文本编辑器更改文本格式](./media/text-editor3.png) |  <center>![使用富文本编辑器添加列表](./media/text-editor4.png) |  <center>![使用富文本编辑器缩进](./media/text-editor5.png) |

#### <a name="links"></a>链接

- **隐私策略**（必需）–链接到你的组织的隐私策略。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略
- **CSP 计划营销材料**（可选）–如果你选择将你的产品/服务扩展到[云解决方案提供商（CSP）](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)计划，则提供营销材料的链接。 CSP 通过使 CSP 合作伙伴捆绑、营销和转售你的产品/服务，将你的产品/服务扩展到更广泛的合格客户。 这些分销商需要访问产品/服务的营销材料。 有关详细信息，请参阅[市场推广服务](https://partner.microsoft.com/reach-customers/gtm)。
- **有用的链接**（可选）–提供**标题**和**URL**时所列的有关应用或相关服务的可选补充联机文档。 单击 " **+ 添加 URL**"，添加其他有用的链接。

#### <a name="contact-information"></a>联系信息

- **联系人**–对于每个客户联系人，提供员工**姓名**、**电话号码**和**电子邮件**地址（*不*会公开显示）。 **支持联系人**组需要一个**支持 URL** （这*将*公开显示）。

    - **支持联系人**（必填）–获取一般支持问题。
    - **工程联系人**（必填）–了解技术问题。
    - **渠道经理联系**（必需）-适用于与 CSP 计划相关的经销商问题。

#### <a name="files-and-images"></a>文件和映像

- **文档**（必需）-为产品/服务添加相关营销文档（PDF 格式），每个产品/服务最少提供一（1）个和三个（3）个文档。
- **图像**（可选）–在市场上，你的产品/服务的徽标图像可能出现在多个位置，并要求以下像素大小为 PNG 格式：

    - **Small** （48 x 48，必需）
    - **中**（90 x 90，必需）
    - **大**（216 x 216，必需）
    - **宽**（255 x 115）
    - **英雄**（815 x 290）

- **屏幕截图**（必需）–添加演示产品/服务的屏幕截图。 最多可添加五（5）个屏幕截图，大小应为 1280 x 720 像素。 所有映像都必须在中。PNG 格式。
- **视频**（可选）–添加指向演示产品/服务的视频的链接。 可以使用 YouTube 和/或 Vimeo 视频链接，这将随产品/服务一起呈现给客户。 还需要输入视频的缩略图，大小为 1280 x 720 像素，格式为 PNG。 每个产品/服务最多可显示四个视频。

>[!NOTE]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

- [Marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择 "**保存草稿**"，然后继续。

## <a name="preview-audience"></a>预览受众

在将产品/服务发布到更广泛的 marketplace 受众之前，你可以在此页上定义用于发布产品/服务的受限**预览版**。

> [!IMPORTANT]
> 在预览版中检查您的产品/服务后 **，选择 "上线"** 以便您的产品/服务可以实时发布到 marketplace 公共受众。

添加单个 AAD/MSA 帐户每行的电子邮件和可选描述。

手动添加最多10个电子邮件地址，或20个（如果上传 CSV 文件，为现有 Microsoft 帐户（MSA）或 Azure Active Directory 帐户），以帮助在发布之前验证产品。 通过添加这些帐户，你将定义在将产品/服务发布到 marketplace 之前，允许其预览版访问权限的用户。 如果你的产品/服务已处于活动阶段，你仍可以定义预览受众来测试产品/服务的任何更改或更新。

> [!NOTE]
> 预览受众不同于一个专用受众。 在市场上发布生活_之前_，允许预览版观众访问你的产品/服务。 你还可以选择创建一个计划并使其仅可供专用用户使用。 在 "**计划列表**" 选项卡中，你可以使用 "**这是一个专用计划**" 复选框来定义专用受众。 然后，你可以使用 Azure 租户 Id 为多达20000的客户定义专用受众。

选择 "**保存草稿**"，然后继续。

## <a name="technical-configuration"></a>技术配置

本页定义用于连接到产品/服务的技术详细信息（URL 路径、webhook、租户 ID 和应用 ID）。 此连接使我们能够为最终用户预配你的产品/服务。 适用于[SaaS 履单 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)的文档中提供了介绍所收集字段的使用情况的关系图。

- **登陆页面 URL** （必填）–定义客户在从 marketplace 获取产品/服务后将居住在的站点 URL。 此 URL 将是在客户路由到页面时接收令牌的终结点。 使用履单完成 Api 中的 resolve，可以交换该令牌以获取预配详细信息。 您收集的这些详细信息以及您收集的任何其他人都可以在您的体验中使用，以完成注册并激活他们的购买体验。

- **连接 webhook** （必需）–对于 Microsoft 需要代表客户发送给你的所有异步事件（例如，SaaS 订阅无效），我们要求你提供连接 webhook。 如果尚未准备好 webhook 系统，最简单的配置是创建一个 HTTP 终结点逻辑应用，该应用将侦听任何发送给它的事件，并相应地处理这些事件（例如，https：\//prod-1westus.logic.azure.com:443/work）。 有关详细信息，请参阅[通过逻辑应用中的 HTTP 终结点调用、触发或嵌套工作流](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。

- **Azure AD 租户 ID** （必需）–在 Azure 门户内部，我们要求你[创建 Azure Active Directory （AD）应用](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)，以便我们可以验证两个服务之间的连接是否在经过身份验证的通信之后。 若要查找[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，请在 Azure Active Directory 中选择 "**属性**"，然后查找列出的**目录 ID**号（例如，50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用 id** （必需）–还需要[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)和身份验证密钥。 若要获取这些值，请转到 Azure Active Directory 并选择**应用注册**，然后查找列出的**应用程序 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。 若要查找身份验证密钥，请单击 "**设置**" 并选择 "**密钥**"。 你将需要提供描述和持续时间，然后将提供一个数值。

>[!Note]
>Azure 应用程序 ID 与你的发布者 ID 相关联，因此请确保你的所有产品/服务中都使用相同的应用程序 ID。

选择 "**保存草稿**"，然后继续。

## <a name="plan-overview"></a>计划概述

使用此页可以在相同的产品/服务中提供各种计划选项。 这些计划（有时称为 Sku）在版本、盈利或服务层方面可能会有所不同。 至少必须设置一个计划，才能在 marketplace 中销售产品/服务。

创建后，你将看到计划名称、Id、定价模型、可用性（公共或专用）、当前发布状态和任何可用操作。

**计划概述**中的可用**操作**取决于计划的当前状态，并且可能包括：

- 如果计划状态为**草稿**-删除草稿
- 如果计划状态为**Live** -停止销售计划或同步专用受众

**创建新计划**（对于选择通过 Microsoft 进行销售的用户，至少需要一个计划）

- **计划 ID：** 为此产品/服务中的每个计划创建唯一的计划 ID。 此 ID 将对产品 URL 中的客户可见，Azure 资源管理器模板（如果适用）。 仅使用小写字母、字母数字字符、短划线或下划线。 此计划 ID 最多允许50个字符。 选择 "创建" 后，不能修改该 ID。
- **计划名称：** 客户在确定要在产品/服务中选择的计划时，将看到此名称。 为此产品/服务中的每个计划创建一个唯一的产品/服务名称。 计划名称用于区分可能属于同一产品/服务（例如，产品/服务名称： Windows Server; 计划： Windows Server 2016，Windows Server 2019）的软件计划。

### <a name="plan-listing"></a>计划列表

此页可用于定义计划名称和说明。 <!-- displays the languages (and markets) where your plan is available, currently English (United States) is the only location available. Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the marketplace details (offer name, description, search terms, etc.) for each language / market.-->

<!--#### Plan listing details-->

<!--Selecting one of the plan languages will display the **plan listing** information, including **Name** and **Description.** -->

- **名称**–根据预览**新计划**条目进行预填充，并将显示为应用商店中显示的产品/服务的标题。
- **说明**–此描述是一种机会，用于说明此软件计划的独特之处，以及与产品/服务内的其他软件计划的任何差异。 最多可包含500个字符。

选择 "**保存草稿**"，然后继续。

#### <a name="pricing-and-availability"></a>定价和可用性

使用此页，您可以配置此计划将在中可用的市场，以及所需的盈利模型、价格和计费术语。 此外，您还可以指示是使该计划对每个人都可见，还是仅供特定客户（一个专用受众）使用。

#### <a name="markets-optional"></a>市场（可选）

每个计划都必须至少在一个市场中可用。 选择 "**编辑市场**"，然后选中要使此计划可用的任何市场位置对应的复选框。 此页面包含一个搜索框和选项，用于选择 "已汇款的国家/地区"，其中 Microsoft 汇寄的销售和使用条款代表您。

如果已将计划的价格设置为美国美元（USD）并添加另一个市场位置，则将根据当前汇率计算新市场的价格。 在发布前查看每个市场的价格。 保存更改后，使用 "导出价格（.xlsx）" 链接查看定价。

选择 "**保存**"，然后继续。

#### <a name="pricing"></a>定价

##### <a name="pricing-model"></a>定价模型

**单层**–启用对你的产品/服务的每月或每年低价位价格的访问。 这有时称为基于站点的定价。 使用此定价模型，你可以选择定义使用 marketplace 计量服务 API 根据非标准单位对客户收费的计费计划。  有关计费计费的详细信息，请参阅[使用 marketplace 计量服务按流量](./saas-metered-billing.md)计费。

**每用户**–根据访问产品/服务的用户数或使用的座位数量，启用对产品/服务的访问。 此基于用户的模型使你能够根据价格设置允许的最小和最大用户数量。 通过这种方式，可以通过配置多个计划，根据用户数量来配置不同的价格点。  这些字段是可选的。 如果未选择此值，则将被解释为不具有限制的用户数（最小值为1，最大值为系统可以支持的最大数目）。 这些字段可作为计划更新的一部分进行编辑。

发布后，无法更改计费定价模型选择。 此外，同一产品/服务的所有计划必须共享同一定价模型。

##### <a name="user-limits"></a>用户限制

如果适用，请选择并设置最小和最大用户限制。

##### <a name="billing-term-and-price"></a>计费术语和价格

选择客户必须支付所列价格的**条款**和**价格**。 必须至少提供一个每月或每年的价格，或者两个选项可供客户使用。

以 USD （USD = 美国美元）为单位的价格将在保存时使用当前汇率转换为所有选定市场的本地货币。 在发布之前通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单个市场中设置自定义价格，请修改并导入定价电子表格。 你负责验证此定价并拥有这些设置。
*\*必须先保存定价更改，才能导出定价数据。*

发布之前请仔细查看你的价格，因为发布计划后，有一些限制会发生变化：

- 一旦发布了某个计划，就不能更改定价模型。
- 为计划发布计费字词后，以后不能将其删除。
- 一旦发布了计划中市场的价格，以后就不能再对其进行更改。

#### <a name="free-trial"></a>免费试用版

通过商用 marketplace 提供的 SaaS 产品，可以在通过 Microsoft 销售时提供一个月免费试用版。 对于除计费计划之外的所有计费模型和条款，都支持免费试用版。 此选项允许客户通过一个月的免费访问权限来进入低障碍。  如果你选择对你的产品/服务中的计划启用免费试用版，则在第一个月期限结束之前，客户将无法转换为付费订阅。  在此期间，购买你的产品/服务的客户可以尝试使用启用了免费试用版的任何受支持计划并在它们之间进行转换。  在此期限结束时，将自动转换为付费订阅。

>[!NOTE]
>如果客户选择转换为无免费试用版的计划，则会发生转换，但免费试用版将立即丢失。 此外，一旦客户开始为某一计划付费，他们就不能再在同一订阅上再次获得免费试用版，即使他们转换为支持免费试用版的 SKU 也是如此。

你可以在此处为你的产品/服务中的每个计划配置免费试用版。 选中此复选框以允许一个月的试用版。

![一个月免费试用复选框](./media/free-trial-enable.png)

>[!NOTE]
>使用免费试用版发布事务产品/服务后，将无法为该计划禁用该产品/服务。 请确保首次发布时此设置正确，以避免重新创建该计划。

若要获取有关当前参与免费试用的客户订阅的信息，请使用新的`isFreeTrial`API 属性，该属性将标记为 true 或 false。 有关详细信息，请参阅[SaaS 获取订阅 API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription)。

>[!NOTE]
>利用 marketplace 计量服务的计划不支持免费试用版。

#### <a name="plan-visibility"></a>计划可见性

你可以将每个计划配置为对每个用户可见，或者仅针对你选择的特定受众进行配置。 你可以使用 Azure AD 租户 Id 分配此受限受众的成员身份。

##### <a name="privacy"></a>隐私

选择 "**这是专用计划**"，使你的计划成为私有计划，并且仅对你选择的受限受众可见。 作为专用计划发布后，您可以更新受众或选择使该计划对所有人都可用。 一旦将某个计划发布为每个人都可见，它就必须对所有人都保持可见（不能再次配置为私有计划）。

##### <a name="restricted-audience-tenant-ids"></a>**受限受众（租户 Id）**

分配将有权访问此私有计划的受众。 使用租户 Id 分配访问权限，其中包含分配给每个租户 ID 的说明。 如果导入 .csv 电子表格文件，则最多可以添加10个租户 Id 或20000个客户租户 Id。

租户是组织的表示形式，ID 表示为 GUID （全局唯一标识符，是用于标识资源的128位整数）。 它是组织或应用开发人员在与 Microsoft 建立关系时（例如，在注册 Azure、Microsoft Intune 或 Microsoft 365 时）收到的 Azure AD 的专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户。 要检查租户，请使用要用于管理应用程序的帐户登录 Azure 门户。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录/租户 ID (GUID) 以及域。 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。 你还可以使用中[https://www.whatismytenantid.com](https://www.whatismytenantid.com)的域名 URL 查找你的组织的租户 ID。

尽管 SaaS 提供使用租户 Id 来定义专用受众，但其他产品/服务类型可能使用 Azure 订阅 Id （也表示为 Guid）。

> [!NOTE]
> 专用受众（或受限受众）不同于预览版观众。 在 "**[预览](#preview-audience)**" 页上，可以定义预览观众。 在 marketplace 中发布产品/服务*之前*，允许预览版用户访问你的产品/服务。 尽管专用受众称号仅适用于特定计划，但预览版观众可以查看所有计划（私密或不查看），但仅限在测试和验证计划时的有限预览期内。

选择 "**保存草稿**"，然后继续。

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Marketplace 产品/服务中的计划示例列表

![带有说明的 marketplace 计划列表示例](./media/marketplace-plan.svg)

## <a name="cloud-solution-provider-csp-reseller-audience"></a>云解决方案提供商（CSP）经销商受众

选拔在 CSP 计划中提供产品/服务，云解决方案提供商可以将产品作为捆绑解决方案的一部分销售给客户。 有关详细信息，请参阅[云解决方案提供商](https://go.microsoft.com/fwlink/?linkid=2111109)。

## <a name="publish"></a>发布

完成产品/服务的所有必需部分后，请选择门户右上角的 "**查看并发布**"。

### <a name="submit-offer-to-preview"></a>将产品/服务提交到预览版

如果这是你首次发布此产品/服务，则可以：

- 请参阅产品/服务的每个部分的完成状态。
    - **未启动**–此部分尚未接触，需要完成。
    - **不完整**–此部分包含需要修复的错误或需要提供详细信息。 需要返回并更新该部分。
    - **Complete** –此部分已完成，所有必需的数据都已提供并且没有错误。 产品/服务的所有部分必须处于完整状态，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保正确测试您的应用程序，以及任何有助于了解应用程序的补充说明。
- 通过选择 "**提交**" 提交产品/服务进行发布。 我们将向你发送一封电子邮件，告知你该产品/服务的预览版本何时可供你查看和批准。 你必须返回到合作伙伴中心，并选择 "产品 **/** 服务"，以便将你的产品/服务发布到公共（或专用用户）。

## <a name="next-step"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
