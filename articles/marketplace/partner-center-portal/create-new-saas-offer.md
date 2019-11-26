---
title: 在商业应用商店中创建新的 SaaS 产品/服务
description: 如何创建新的软件即服务（SaaS）产品/服务，以便在 Azure Marketplace、AppSource 或通过云解决方案提供商（CSP）计划的 Microsoft 合作伙伴中心使用商业应用商店门户。
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 12372c1f00e994382338adf9bd078018ba702da1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213344"
---
# <a name="create-a-new-saas-offer"></a>创建新的 SaaS 产品/服务

若要开始创建 "软件即服务" （SaaS）产品/服务，请确保先[创建合作伙伴中心帐户](./create-account.md)，并打开 "概述" 选项[卡，并](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)选择 "**概述**" 选项卡。

![合作伙伴中心的商业应用商店仪表板](./media/new-offer-overview.png)

>[!Note]
> 发布产品/服务后，将仅在系统中更新对合作伙伴中心提供的产品/服务，并在重新发布后存储。 进行更改后，请确保提交产品/服务进行发布。

选择 "+**新产品/服务 ...** " 按钮，然后选择 "**软件即服务**" 菜单项。 

如果选择其他产品/服务类型，则可能会被重定向到较旧的[云合作伙伴门户](https://cloudpartner.azure.com/)。 目前，合作伙伴中心的商用 Marketplace 门户中提供了 SaaS 和 Dynamics 365 产品/服务。

![在合作伙伴中心创建产品/服务窗口](./media/new-offer-click.png)

将显示 "**新建产品/服务**" 对话框。 

!["新建产品/服务" 对话框](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>产品/服务 ID 和别名

- **产品/服务 ID**：帐户中每个产品/服务的唯一标识符。 在 marketplace 产品/服务和 Azure 资源管理器模板（如果适用）的 URL 地址中，客户将可以看到此 ID。 产品/服务 ID 必须是小写、字母数字（包括连字符和下划线，但没有空格）。 这限制为50个字符，并且在你选择 "*创建*" 后将无法更改。  
示例：测试/服务-1
<br>生成 URL： `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **产品/服务别名**：用于引用合作伙伴中心门户中的产品/服务的名称。 此名称不会在 marketplace 中使用，并且不同于将向客户显示的*产品/服务名称*和其他值。 选择 "*创建*" 后，不能更改此值。

<br>示例：测试产品1&#8482;

选择“创建”。  为此产品/服务创建了 "**产品/服务概述**" 页。  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>产品/服务概述

"**产品概述**" 页包括： 

- **发布状态**显示了发布此产品/服务所需步骤的直观表示形式，以及每个步骤要完成的时间。 不完整的发布步骤图标将灰显。 

- "**产品概述**" 菜单包含用于对此产品/服务执行操作的链接的列表。 此操作列表将根据你为产品/服务所做的选择而更改。  
    - 如果产品/服务是草稿版-删除草稿 
    - 如果产品/服务是 live-停止销售优惠 
    - 如果产品/服务为预览版-上线 
    - 如果尚未完成发布服务器注销-请取消发布

## <a name="offer-setup"></a>产品/服务设置

"**产品/服务设置**" 选项卡要求提供以下信息。 完成这些字段后，选择 "**保存**"。

- **是否想要通过 Microsoft 销售？** （是/否）
    - **是**的，你希望通过 microsoft 出售你的产品/服务，microsoft 将代表你托管 marketplace 交易;或 
    - **不**可以，你希望只通过市场来列出你的产品/服务，而独立于 Microsoft 处理任何货币交易。    

### <a name="sell-through-microsoft"></a>通过 Microsoft 进行销售

通过 Microsoft 进行销售可提供更好的客户发现和收购，使 Microsoft 能够代表你托管 marketplace 交易，并利用 Microsoft 的全球可用商业功能。

#### <a name="saas-offer-requirements"></a>SaaS 产品/服务要求

若要在合作伙伴中心列出 "软件即服务" （SaaS）产品/服务，必须满足以下条件：

- 你的产品/服务必须使用[Azure Active Directory （Azure AD）](https://azure.microsoft.com/services/active-directory/)来进行身份管理和身份验证。
- 你的产品/服务必须使用[SaaS 履单 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)与 Azure Marketplace 集成。
- 有关更广泛的要求，请参阅[SaaS 优惠发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)。

#### <a name="saas-pricing-and-billing-options"></a>SaaS 定价和计费选项
使用在发布者的 Azure 订阅中运行的 SaaS 解决方案，客户支付的许可费用包括部署软件所需的基础结构的成本。 Azure 基础结构的使用情况是直接管理和计费的。 客户将无法看到实际的基础结构使用费。 发布者应将 Azure 基础结构使用费用捆绑到其软件许可证定价中。 

SaaS 使用按流量计费服务，根据固定费用、每用户或消耗费用支持每月或每年计费。 Microsoft 的商业市场运营于代理商模型，使出版商设置价格、Microsoft 帐单客户和 Microsoft 将收入支付给出版商，同时为代理商收费。

下表显示了降低成本的示例，并显示了付款以演示代理模型。

|**你的许可证费用**|**每月 $100**|
|:---|:---|
|Azure 使用费用（D1/1 核）|直接向发布者而不是客户收费|
|客户由 Microsoft 计费|每月 $100.00 （发布者必须按许可费考虑任何产生的或通过的基础结构成本）|

|**Microsoft 收费**|**每月 $100**|
|:---|:---|
|Microsoft 将许可证费用的 80% 支付给你 <br>*适用于合格 SaaS 应用的 *，Microsoft 将支付90% 的许可证成本*|80.00 美元/月 <br>每月 *$* 90.00 *|

- 在此示例中，$100.00 Microsoft 向客户收取软件许可证，并向发布者支付 $80.00。
- 符合**Marketplace 服务费用**的合作伙伴将从5月2019到6月5日起，从 5 2020 月5日起，对 SaaS 产品/服务的事务费用将减少。 在此方案中，Microsoft 为你的软件许可证计费 $100.00，并向发布者支付 $90.00。

> [!NOTE]
> **降低 Marketplace 服务费用**：对于在我们的商业市场上发布的某些 SaaS 产品/服务，microsoft 会将其 Marketplace 服务费用从20% 降低到10% （如 Microsoft 发布者协议中所述）。 为了使你的产品/服务符合资格，Microsoft 已将至少一个产品/服务指定为 "IP 共同销售就绪" 或 "IP 共同销售"。  在每个日历月结束之前，必须在每个日历月结束至少五（5）个工作日内获得资格，以便每月获得更少的 Marketplace 服务费用。  降低的 Marketplace 服务费用不适用于 Vm、托管应用或通过我们的商业市场提供的任何其他产品。  降低的 Marketplace 服务费用仅适用于 Microsoft 在5月1日、2019年6月 30 2020 日之间收集的许可费用。  此时间过后，Marketplace 服务费用将恢复为其正常量。 

### <a name="list-through-microsoft"></a>通过 Microsoft 列出

通过创建 marketplace 列表，通过 Microsoft 提升你的业务。 选择仅列出你的产品/服务，而不是通过 Microsoft，这意味着 Microsoft 不会直接参与软件许可证交易。 没有关联的事务费用，发布者将保留从客户那里收集的任何软件许可费用的100%。 但是，发布者负责支持软件许可证事务的所有方面，包括但不限于：订单执行、计量、计费、开票、支付和收集。 

- **你希望潜在客户如何与此列表服务进行交互？**

##### <a name="get-it-now-free"></a>立即获取（免费）
通过提供有效的 URL （以*http*或*https*开头）向客户免费列出你的产品/服务，用户可以在其中访问你的应用程序。  例如： `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>免费试用（列出）
通过提供有效的 URL （以*http*或*https*开头）向客户列出你的产品/服务，用户可以通过[使用 Azure Active Directory （Azure AD）通过一键式身份验证](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)获取试用版。  例如：`https://contoso.com/trial/saas-app`。 产品/服务列表免费试用版由您的服务创建、管理和配置，并且没有 Microsoft 管理的订阅。

> [!NOTE]
> 应用程序通过试用链接接收的令牌只能用于通过 Azure AD 获取用户信息，以便在应用中自动创建帐户。 Microsoft 帐户（MSA）不支持使用此令牌进行身份验证。

##### <a name="contact-me"></a>联系信息
通过连接客户关系管理（CRM）系统来收集客户联系信息。 系统将要求客户提供共享其信息的权限。 这些客户详细信息以及他们找到你的产品/服务的产品/服务名称、ID 和 marketplace 源将发送到已配置的 CRM 系统。 有关配置 CRM 的详细信息，请参阅[连接潜在客户管理](#connect-lead-management)。 

## <a name="example-marketplace-offer-listing"></a>Marketplace 优惠列表示例

![带有说明的 marketplace 产品/服务示例列表](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>启用体验版

通过向客户提供 "在购买前试用" 的选项，测试驱动器是向潜在客户展示你的产品/服务的一种好办法 [了解有关测试驱动器的详细信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **启用测试驱动器**（复选框）

通过启用 "测试驱动器"，系统将要求你配置演示环境，让客户在固定时间段内试用你的产品/服务。 

### <a name="type-of-test-drive"></a>测试驱动器的类型

- **[Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** ：包含构成解决方案的所有 Azure 资源的部署模板。 适用于此方案的产品只使用 Azure 资源。
- **[Dynamics 365 For Business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** ： Microsoft 托管并维护企业中心企业资源规划系统（财务、运营、供应链、CRM 等）的测试驱动器服务（包括预配和部署）。  
- **[Dynamics 365 For Customer engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** ： Microsoft 托管并维护客户参与系统（销售、服务、项目服务、现场服务等）的测试驱动器服务（包括预配和部署）。  
- **[Dynamics 365 For Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** ： Microsoft 托管并维护针对财务和运营企业资源规划系统（财务、运营、制造、供应链等）的测试驱动器服务（包括预配和部署）。 
- **[逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** ：包含所有复杂解决方案体系结构的部署模板。 任何自定义产品都应使用这种类型的测试驱动器。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** ：指向自定义生成的仪表板的嵌入链接。 要演示交互 Power BI 视觉对象的产品应使用此类型的测试驱动器。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他测试驱动器资源
- [测试驱动器技术最佳方案](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [试用版销售最佳实践](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Test Drive 概述 One 寻呼](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>其他潜在客户管理资源
- [潜在顾客管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [潜在客户管理概述 One 寻呼](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

请记住在转到下一节之前**保存**。

## <a name="properties"></a>属性
"**属性**" 选项卡要求你定义用于对你的产品/服务进行分组的类别和行业、支持你的产品/服务的法律合同和你的应用版本。 

完成这些字段后，选择 "**保存**"。 

### <a name="category"></a>类别
最少选择一（1），最多可选择三（3）个类别，将产品/服务分组到适当的 marketplace 搜索区域。 请在产品/服务描述中了解产品/服务如何支持这些类别。 

### <a name="industry"></a>行业

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>应用版本
这是一个可选字段，用于在 AppSource marketplace 中标识产品的版本号。 

### <a name="standard-contract"></a>标准合同

- **使用标准约定？**

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 提供了标准合同模板，以帮助促进 marketplace 中的事务。 

Azure Marketplace 发布者可以选择在标准合同下提供其软件，而客户只需要来审查和接受一次，而不是手工编写自定义条款和条件。 

可在此处找到标准协定： https://go.microsoft.com/fwlink/?linkid=2041178。

#### <a name="terms-of-use"></a>使用条款

如果你的许可条款不同于标准合同，你可以选择在此处输入你自己的法律条款。 这些内容可以是以纯文本形式输入，也可以作为一个 URL 链接到您的许可条款。

客户必须接受这些条款才能试用应用。 

请记住在转到下一节之前**保存**。

## <a name="offer-listing"></a>产品/服务列表

"产品列表" 选项卡显示提供产品/服务的语言（和市场），当前只有英语（美国）是可用的位置。 此外，此页还显示特定于语言的列表的状态以及添加它的日期/时间。 需要为每种语言/市场定义 marketplace 详细信息（产品名称、说明、搜索词等）。

> [!NOTE]
> 如果产品/服务说明以短语开头，则产品/服务列表内容（例如产品/服务、文档、屏幕截图、使用条款和隐私策略）无需使用英语，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个*有用的链接 URL*来提供与产品/服务列表内容中所用语言不同的内容。

### <a name="offer-listings"></a>套餐列表

提供要在 marketplace 中显示的详细信息，包括产品/服务和市场营销资产的说明。

- **名称**（必需）：此处定义的名称将显示为你选择的 marketplace 中的产品/服务列表的标题。 该名称根据你以前的**新产品/服务**条目预填充。  这可能是商标字的。  这不能包含表情符号（除非它们是商标和版权符号），并且必须限制为50个字符。
- **摘要**（必需）：提供产品/服务的简短说明，用于应用商店列表搜索结果。 最多可在此字段中输入100个字符的文本。
- **说明**（必需）：提供产品/服务的说明，以便在 marketplace 列表概述中显示。 考虑包括价值主张、关键优势、任何类别或行业关联、应用内购买机会、任何所需的披露，以及用于了解详细信息的链接。
最多可在此字段中输入3000个字符的文本。 有关更多提示，请参阅文章[编写出色的应用说明](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)。
- **搜索关键字**：最多输入三个搜索关键字，客户可以使用这些关键字在 marketplace 中查找你的产品/服务。
- **入门说明**（必需）：说明如何配置和开始为潜在客户使用应用。  此快速入门可以包含指向更详细的联机文档的链接。 最多可在此字段中输入3000个字符的文本。 

#### <a name="description"></a>**说明**

此为必填字段。 要包括在说明中的项： 

* 在前几句描述中清晰描述产品/服务的价值主张。  
* 请牢记，前几句话可能在搜索引擎结果中显示。  
* 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
* 尽可能多使用行业特定用语或基于优势的措辞。 

核心价值主张应包含以下信息： 

* 产品说明。 
* 受益于该产品的用户类型。 
* 产品的客户需求或困难。 

若要使您的产品/服务描述更具吸引力，可以使用 HTML 标记来设置说明格式。 

1. 若要创建段落，请在文本 begging 添加 `<p>`，并在末尾添加 `</p>`。

    **示例**： 

    `<p>` 这是我的第一个段落。 `</p>` <br>
    `<p>` 这是我的第二个段落。 `</p>` <br>

    上述内容如下所示：

    <p> 这是我的第一个段落。 </p>
    <p> 这是我的第二个段落。 </p>

1. 如果要添加项目的项目**符号列表**，请将文本放在下面 `<li>` 标记中。 可以在 `<ul>` 和 `</ul>` 标记中复制和粘贴更多项目符号项（`<li>` 和 `</li>` 标记之间的项）。 请确保添加 `<ul></ul>`。 

    **示例**：

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    上述内容如下所示：
    <ul> 
        <li>在此添加文本</li> 
        <li> 在此添加文本 </li> 
        <li> 在此添加文本 </li> 
    </ul> 

1. 若要为**粗体**内容，请在要加粗的文本开头添加 `<b>`，并将 `</b>` 添加到要以粗体显示的文本末尾。 

    **示例**： `<b>` 免费试用版 `</b>`
    
    上述内容将导致在店面中的产品/服务说明中，"免费试用" 字样为粗体。 

    **免费试用版**

1. 若要在内容之间添加**换行符**，请将 `<br>` 添加到要在新行上开始的内容之前。 如果要保留一个空格并确保内容从新行开始，请在内容前添加 `<br><br>`。 

    **示例**：

    这是文本行。 `<br>` 这是将在新行中开始的文本行。 `<br><br>` 这是一条将在下方开始两行的行。 

    上述内容如下所示：

    这是文本行。 <br> 这是将在新行中开始的文本行。 <br><br> 这是一条将在下方开始两行的行。 

1. 如果要**增加文本大小**，请首先选择文本的大小。 使用以下示例。 选择文本大小后，将相应的 `<H*></H*>` 标记添加到文本的开头和结尾。 

    **示例**：

    `<h1>`为标题 1`</h1>` <br>
    `<h2>`为标题 2`</h2>` <br>
    `<h3>`为标题 3`</h3>` <br>
    `<h4>`为标题 4`</h4>` <br>
    `<h5>`为标题 5`</h5>` <br>
    `<h6>`标题 6`</h6>` 

    上述内容如下所示：

    ![示例标题](./media/heading.png)

#### <a name="links"></a>链接

- **隐私策略**（必需）：指向你的组织的隐私策略的链接。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略
- **CSP 计划营销材料**（可选）：如果你选择将你的产品/服务扩展到[云解决方案提供商（CSP）](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)计划，则必须提供营销材料的链接。 CSP 通过使 CSP 合作伙伴捆绑、营销和转售你的产品/服务，将你的产品/服务扩展到更广泛的合格客户。 这些分销商需要访问产品/服务的营销材料。 有关详细信息，请参阅[市场推广服务](https://partner.microsoft.com/reach-customers/gtm)。
- **有用的链接**（可选）：通过提供**标题**和**URL**来列出你的应用或相关服务的可选补充联机文档。 单击 " **+ 添加 URL**"，添加其他有用的链接。

#### <a name="contact-information"></a>联系信息

- **联系人**：对于每个客户联系人，请提供员工**姓名**、**电话号码**和**电子邮件**地址。  （这些*将不*会公开显示）。 **支持联系人**组也需要**支持 URL** 。  （*将*公开显示此信息）。

**支持联系人**（必需）：有关一般支持问题。

**工程联系人**（必需）：有关技术问题。

**渠道经理联系人**（必需）：适用于与 CSP 计划相关的经销商问题。

#### <a name="files-and-images"></a>文件和映像

- **文档**（必需）：以 PDF 格式为产品/服务添加相关的营销文档，每个产品/服务最少提供一（1）个文档，最多提供三（3）个文档。
- **图像**（可选）：在多个位置，产品/服务的徽标图像可能会出现在整个市场中，需要以下尺寸：--小尺寸： 48 x 48 像素 _（必需）、_ 中： 90 x 90 像素、大： 216 x 216 像素 _（必需）、_ 宽： 255 x 115 像素和英雄： 815 x 290 像素。 所有映像都必须在中。PNG 格式。
- **屏幕截图**（必需）：添加演示产品/服务的屏幕截图。 最多可添加五（5）个屏幕截图，大小应为 1280 x 720 像素。 所有映像都必须在中。PNG 格式。
- **视频**（可选）：添加指向演示产品/服务的视频的链接。 可以使用 YouTube 和/或 Vimeo 视频链接，这将随产品/服务一起呈现给客户。 还需要输入视频的缩略图，大小为 1280 x 720 像素，格式为 PNG。 每个产品/服务最多可显示四个视频。

请记住在转到下一节之前**保存**。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

- [Marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>预览

使用 "**预览**" 选项卡，可以在将产品/服务发布到更广泛的 marketplace 受众之前，定义用于发布产品/服务的受限**预览版**。

> [!IMPORTANT]
> 在预览版中检查产品/服务后，你必须选择 "**开始**"，然后将产品/服务发布到 marketplace 公共受众。

- **定义预览受众：添加单个 AAD/MSA 帐户每行的电子邮件和可选描述。**

手动添加最多十（10）个电子邮件地址，或者在上传 CSV 文件（对于现有 Microsoft 帐户（MSA）或 Azure Active Directory 帐户），以帮助在发布之前验证产品/服务。 通过添加这些帐户，你将定义在将产品/服务发布到 marketplace 之前，允许其预览版访问权限的用户。 如果你的产品/服务已处于活动阶段，你仍可以定义预览受众来测试产品/服务的任何更改或更新。

> [!NOTE]
> 预览受众不同于一个专用受众。 在市场上发布生活_之前_，允许预览版观众访问你的产品/服务。 你还可以选择创建一个计划并使其仅可供专用用户使用。 在 "**计划列表**" 选项卡中，你可以使用 "**这是一个专用计划**" 复选框来定义专用受众。 然后，你可以使用 Azure 租户 Id 为多达20000的客户定义专用受众。

## <a name="technical-configuration"></a>技术配置

"**技术配置**" 选项卡定义用于连接到产品/服务的技术详细信息（URL 路径、webhook、租户 id 和应用 ID）。 此连接使我们能够为最终用户预配你的产品/服务。 适用于[SaaS 履单 api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)的文档中提供了介绍所收集字段的使用情况的关系图。

- **登陆页面 URL** （必需）：定义客户在从 marketplace 获取产品/服务后将居住在的站点 url。 此 URL 将是在客户路由到页面时接收令牌的终结点。 使用履单完成 Api 中的 resolve，可以交换该令牌以获取预配详细信息。 您收集的这些详细信息以及您收集的任何其他人都可以在您的体验中使用，以完成注册并激活他们的购买体验。

- **连接 webhook** （必需）：对于 Microsoft 需要代表客户发送给你的所有异步事件（例如： SaaS 订阅无效），我们要求你提供连接 webhook。 如果尚未准备好 webhook 系统，最简单的配置是创建一个 HTTP 终结点逻辑应用，该应用将侦听任何发送给它的事件，并相应地处理这些事件（例如，https：\//prod-1westus.logic.azure.com:443/work）。 有关详细信息，请参阅[在逻辑应用中使用 HTTP 终结点调用、触发或嵌套工作流](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。

- **Azure AD 租户 ID** （必需）：在 Azure 门户内部，我们要求你[创建 Azure Active Directory （AD）应用](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)，以便我们可以验证两个服务之间的连接是否在经过身份验证的通信之后。 若要查找[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，请跳到 Azure Active Directory，选择 "**属性**"，然后查找列出的**目录 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用 id** （必需）：还需要[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)和身份验证密钥。 若要获取这些值，请转到 Azure Active Directory 并选择**应用注册**，然后查找列出的**应用程序 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。 若要查找身份验证密钥，请单击 "**设置**" 并选择 "**密钥**"。 你将需要提供描述和持续时间，然后将提供一个数值。

 请注意，Azure 应用程序 ID 与你的发布者 ID 相关联，因此请确保你的所有产品/服务中都使用相同的应用程序 ID。

## <a name="plan-overview"></a>计划概述

利用 "**计划概述**" 选项卡，您可以在同一产品内提供各种计划选项。 这些计划（有时称为 Sku）在版本、盈利或服务层方面可能会有所不同。 至少必须设置一个计划，才能在 marketplace 中销售产品/服务。

创建后，你将看到计划名称、Id、定价模型、可用性（公共或专用）、当前发布状态和任何可用操作。

**计划概述**中的可用**操作**取决于计划的当前状态，并且可能包括：

- 如果计划状态为**草稿**-删除草稿
- 如果计划状态为**Live** -停止销售计划或同步专用受众

**创建新计划**（对于选择通过 Microsoft 进行销售的用户，至少需要一个计划）

- **计划 ID：** 为此产品/服务中的每个计划创建唯一的计划 ID。 此 ID 将对产品 URL 中的客户可见，Azure 资源管理器模板（如果适用）。 仅使用小写字母、字母数字字符、短划线或下划线。 此计划 ID 最多允许50个字符。 请注意，在选择 "创建" 后，不能修改该 ID。
- **计划名称：** 客户在确定要在产品/服务中选择的计划时，将看到此名称。 为此产品/服务中的每个计划创建一个唯一的产品/服务名称。 计划名称用于区分可能属于同一产品/服务的软件计划（例如 产品/服务名称： Windows Server;计划： Windows Server 2016、Windows Server 2019）。

### <a name="plan-listing"></a>计划列表

"**计划列表**" 选项卡显示你的计划可用的语言（和市场），当前只有英语（美国）是可用的位置。 此外，此页还显示特定于语言的列表的状态以及添加它的日期/时间。 需要为每种语言/市场定义 marketplace 详细信息（产品名称、说明、搜索词等）。

#### <a name="plan-listing-details"></a>计划列表详细信息

选择一种计划语言将显示**计划列表**信息，包括**名称**和**说明。**

- **名称**：根据预览**新计划**条目进行预填充，并将显示在 marketplace 中显示的产品/服务 "软件计划" 的标题。
- **说明：** 此说明是一个机会，用于说明此软件计划的独特之处，以及与你的产品/服务中的其他软件计划的不同之处。 最多可包含500个字符。

完成这些字段后，选择 "**保存**"。

#### <a name="plan-pricing-and-availability"></a>规划定价和可用性

使用 "**定价和可用性**" 选项卡，您可以配置此计划将在其中提供的市场，以及所需的盈利模型、价格和计费条款。 此外，您还可以指示是使该计划对每个人都可见，还是仅供特定客户（一个专用受众）使用。

##### <a name="enabling-free-trials"></a>启用免费试用

通过商用 marketplace 提供的 SaaS 产品，可以在通过 Microsoft 销售时提供一个月免费试用版。 对于除计费计划之外的所有计费模型和条款，都支持免费试用版。 此选项允许客户通过一个月的免费访问权限来进入低障碍。  如果你选择对你的产品/服务中的计划启用免费试用版，则在第一个月期限结束之前，客户将无法转换为付费订阅。  在此期间，购买你的产品/服务的客户可以尝试使用启用了免费试用版的任何受支持计划并在它们之间进行转换。  在此期限结束时，将自动转换为付费订阅。

>[!Note]
>如果客户选择转换为无免费试用版的计划，则会发生转换，但免费试用版将立即丢失。  此外，一旦客户开始为某一计划付费，他们就不能再在同一订阅上再次获得免费试用版，即使他们转换为支持免费试用版的 SKU 也是如此。

你的产品/服务中的每个计划都提供了配置免费试用版的功能。 只需导航到每个产品的定价和可用性，并选中该复选框以允许一个月的试用版。

![一个月免费试用复选框](./media/free-trial-enable.png)

>[!Note]
>使用免费试用版发布事务产品/服务后，将无法为该计划禁用该产品/服务。 请确保首次发布时此设置正确，以避免重新创建该计划。

若要获取有关当前参与免费试用的客户订阅的信息，请使用 `isFreeTrial`的新 API 属性，该属性将被标记为 true 或 false。 有关详细信息，请参阅[SaaS 获取订阅 API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) 。

>[!Note]
>利用 marketplace 计量服务的计划不支持免费试用版。

#### <a name="markets"></a>市场

- **编辑市场**（可选）

每个计划都必须至少在一个市场中可用。 对于想要使此计划可用的任何市场位置，选中相应的复选框。 用于选择 "已汇款" 国家/地区的搜索框和按钮，其中包含 Microsoft 汇寄的销售和使用条款，其中包含你的帮助。

如果已将计划的价格设置为美国美元（USD）并添加另一个市场位置，则将根据当前汇率计算新市场的价格。 在发布前，应始终查看每个市场的价格。 保存更改后，可以通过使用 "导出价格（.xlsx）" 链接来查看定价。

#### <a name="pricing"></a>定价

- **定价模型**：平面速率或基于座位

**固定速率：** 使用单个每月或每年低价位价格启用对你的产品/服务的访问。 这有时称为基于站点的定价。 使用此定价模型，你可以选择定义使用 marketplace 计量服务 API 根据非标准单位对客户收费的计费计划。  有关计费计费的详细信息，请参阅[使用 marketplace 计量服务按流量](./saas-metered-billing.md)计费。

**每个用户：** 根据访问产品/服务的用户数量或使用的座位，使用价格启用对产品/服务的访问。 此基于用户的模型使你能够根据价格设置允许的最小和最大用户数量。 通过这种方式，可以通过配置多个计划，根据用户数量来配置不同的价格点。  这些字段是可选的。 如果未选择此值，则将被解释为不具有限制的用户数（最小值为1，最大值为系统可以支持的最大数目）。 这些字段可作为计划更新的一部分进行编辑。

发布后，无法更改计费定价模型选择。 此外，同一产品/服务的所有计划必须共享同一定价模型。

- **计费术语**：每月或每年

选择客户必须支付所列价格的频率。 必须至少提供一个每月或每年的价格，或者两个选项可供客户使用。

- **价格**：每月 usd 或每年美元

使用在安装过程中可用的当前汇率，使用当地货币（USD = 美国美元）将价格设置为所有所选市场的本地货币。 在发布之前通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单个市场中设置自定义价格，请修改并导入定价电子表格。 你负责验证此定价并拥有这些设置。
*\*必须先保存定价更改才能启用定价数据的导出。*

发布之前请仔细查看你的价格，因为发布计划后，有一些限制会发生变化：

- 一旦发布了某个计划，就不能更改定价模型。
- 为计划发布计费字词后，以后不能将其删除。
- 一旦发布了计划中市场的价格，以后就不能再对其进行更改。

### <a name="plan-audience"></a>规划受众

你可以选择将每个计划配置为对每个用户可见，或者仅配置为你选择的特定群体。 你可以使用 Azure AD 租户 Id 分配此受限受众的成员身份。

#### <a name="privacy"></a>隐私

- **这是专用计划**（可选复选框）

选中此框以使您的计划成为私有计划，并且只对所选的受限受众可见。 作为专用计划发布后，您可以更新受众或选择使该计划对所有人都可用。 一旦将某个计划发布为每个人都可见，它就必须对所有人都保持可见。 （计划不能再次配置为专用计划）。

- **受限受众（租户 Id）**

分配将有权访问此私有计划的受众。 使用租户 Id 分配访问权限，其中包含分配给每个租户 ID 的说明。 如果导入 .csv 电子表格文件，则最多可以添加10个租户 Id 或20000个客户租户 Id。

租户是组织的表示形式，ID 表示为 GUID （全局唯一标识符，是用于标识资源的128位整数）。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如注册 Azure、Microsoft Intune 或 Microsoft 365）会收到该实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户。 若要检查租户，请使用要用于管理应用程序的帐户登录到 Azure 门户。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录和租户 ID (GUID) 以及域。 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。 你还可以使用域名 URL 查找你组织的租户 ID，网址为： [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

尽管 SaaS 提供使用租户 Id 来定义专用受众，但其他产品/服务类型可能使用 Azure 订阅 Id （也表示为 Guid）。

> [!NOTE]
> 专用受众（或受限受众）不同于预览版观众。 在 " **[预览](#preview)** " 选项卡中，可以定义预览观众。 在 marketplace 中发布产品/服务*之前*，允许预览版用户访问你的产品/服务。 尽管专用受众称号仅适用于特定计划，但预览版观众可以查看所有计划（私密或不查看），但仅限在测试和验证计划时的有限预览期内。

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Marketplace 产品/服务中的计划示例列表

![带有说明的 marketplace 计划列表示例](./media/marketplace-plan.svg)

## <a name="test-drive"></a>体验版

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="cloud-solution-provider-csp-reseller-audience"></a>云解决方案提供商（CSP）经销商受众

选拔在 CSP 计划中提供产品/服务，云解决方案提供商可以将产品作为捆绑解决方案的一部分销售给客户。 有关详细信息，请参阅[云解决方案提供商](https://go.microsoft.com/fwlink/?linkid=2111109)。

## <a name="publish"></a>发布

完成产品/服务的所有必需部分后，请选择门户右上角的 "**发布**"。 你将被重定向到 "**查看并发布**" 页。

#### <a name="submit-offer-to-preview"></a>将产品/服务提交到预览版

如果这是你首次发布此产品/服务，则可以：

- 请参阅产品/服务的每个部分的完成状态。
    - *未开始*-表示该部分尚未接触，需要完成。
    - *不完整*-表示部分包含需要修复的错误或需要提供更多信息。 请返回到部分并对其进行更新。
    - *Complete* -表示该部分已完成，所有必需的数据都已提供并且没有错误。 产品/服务的所有部分必须处于完整状态，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保正确测试您的应用程序，以及任何有助于了解应用程序的补充说明。
- 通过选择 "**提交**" 提交产品/服务进行发布。 我们将向你发送一封电子邮件，告知你该产品/服务的预览版本何时可供你查看和批准。 你必须返回到合作伙伴中心，并选择 "产品 **/** 服务"，以便将你的产品/服务发布到公共（或专用用户）。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
