---
title: 在商业 Marketplace 中创建新的 SaaS 产品/服务
description: 如何创建新的软件即服务 (SaaS) 产品列表或销售 Azure Marketplace、 AppSource 或通过云解决方案提供商 (CSP) 计划在 Microsoft 合作伙伴中心上使用商业 Marketplace 门户。
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: f2787cd74525e7676befb133a6106ce83d9c2a20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072629"
---
# <a name="create-a-new-saas-offer"></a>创建新的 SaaS 产品/服务

若要开始创建与服务 (SaaS) 提供的软件，请确保你第一[创建合作伙伴中心帐户](./create-account.md)，然后打开[商业应用商店仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，与**概述**选定的选项卡。

![合作伙伴中心中的商业应用商店仪表板](./media/new-offer-overview.png)

选择 +**创建一个新...** 按钮，然后选择**软件即服务**菜单项。 

如果你选择的其他产品/服务类型之一，你将定向到较旧[云合作伙伴门户](https://cloudpartner.azure.com/)。  唯一的 SaaS 产品/服务目前在合作伙伴中心上的商业 Marketplace 门户中可用。 

![在合作伙伴中心创建产品/服务窗口](./media/new-offer-click.png)

**新产品/服务**显示对话框。 

![新建产品/服务对话框](./media/new-offer-popup.png)


## <a name="offer-id-and-name"></a>产品/服务 ID 和名称

- **产品/服务 ID**：在帐户中创建每个产品/服务的唯一标识符。 此 ID 将是可见的 marketplace 产品/服务和 Azure 资源管理器模板 （如果适用） 的 URL 地址中的客户。 产品/服务 ID 必须是小写字母、 字母数字 （包括连字符和下划线，但不是含空格）。 这是 50 个字符，在选择后不能更新创建。  
示例： 测试产品/服务 1
<br>导致 URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **套餐名称**：SaaS 应用程序产品/服务，在发布、 播发和 web 站点上一致的官方名称。  此名称可能商标。  产品/服务名称不能包含空格，表情符号 （除非它们是商标或版权符号），并且必须限制为 50 个字符。
<br>示例：测试产品/服务 1&#8482;

选择“创建”  。  **产品/服务概述**页创建此产品/服务。  

![在合作伙伴中心上的产品/服务概述](./media/commercial-marketplace-offer-overview.png)

## <a name="offer-overview"></a>产品/服务概述

**产品/服务概述**页包括： 

- **发布状态**发布此产品/服务和每个步骤需要多长时间完成所需的步骤的直观地显示。 不完整的发布步骤图标将灰显。 

- **产品/服务概述**菜单包含一组用于对此产品/服务执行操作的链接。 此操作的列表将根据产品/服务所做的选择。  
    - 如果产品/服务是一个草稿 – 删除草稿 
    - 如果产品/服务是实时 – 停止销售的产品/服务 
    - 如果产品/服务处于预览阶段-发布 
    - 如果尚未完成发布服务器注销-取消发布

## <a name="offer-setup"></a>产品/服务安装程序

**产品/服务安装程序**选项卡的要求提供的以下信息。 选择**保存**之后完成这些字段。

- **想要通过 Microsoft 销售？** （是/否）
    - **是**，你想要销售产品/服务通过 Microsoft 与 Microsoft 托管 marketplace 事务代表你; 或 
    - **不**，你想要仅列出测试产品/服务市场，通过处理独立于 Microsoft 的任何货币交易。    

### <a name="sell-through-microsoft"></a>通过 Microsoft 进行销售

通过 Microsoft 销售提供更好的客户发现和收购，Microsoft 可以通过你的代表主机 marketplace 事务并充分利用 Microsoft 的全球可用的商务功能。

#### <a name="saas-offer-requirements"></a>SaaS 产品/服务要求

若要列出软件与服务 (SaaS) 提供使用合作伙伴中心上的商业应用商店，必须满足以下条件：

- 产品/服务必须使用[Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/)进行标识管理和身份验证。
- 产品/服务必须使用[SaaS 履行 Api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)将与 Azure Marketplace 进行集成。
- 有关更广泛的要求，请参阅[SaaS 产品/服务发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)。

#### <a name="saas-on-azure-billing-infrastructure-costs"></a>Azure 计费基础结构成本 SaaS
如果在 Azure 中托管的 SaaS 产品/服务，，为发布服务器，必须考虑 Azure 基础结构使用费和软件许可费用为单个成本项。 此成本表示为对客户每月固定费用。 管理并直接付款给您的合作伙伴，azure 基础结构使用情况。 客户将无法看到实际的基础结构使用费。 发布者通常选择将 Azure 基础结构使用费捆绑到其软件许可证定价中。 

软件许可费用显示为每月重复执行基于站点的订阅的统一费率付费，并不按流量计费或基于消耗。

|**你的许可证费用**|**每月 100 美元**|
|:---|:---|
|Azure 使用费用（D1/1 核）|直接向发布者而不是客户收费|
|Microsoft 向客户收取|100\.00 美元 / 月 （发布服务器必须考虑中的许可证费用的任何引起的开销量或传递基础结构成本）|

- 在这种情况下，Microsoft 向你收取 100.00 美元的软件许可证费用，并向发布者支付 80.00 美元。
- 具有限定为合作伙伴**减少 Marketplace 服务费用**将看到从年 6 月 2020年直到 2019 年 5 提供在 SaaS 降低的交易费。 在此方案中，Microsoft 对您的软件许可证 100.00 美元进行收费和到发布服务器的回报 90.00 美元。

> [!NOTE]
> **减少的 Marketplace 服务费**:对于某些 SaaS 提供已在我们的商业应用商店上发布，Microsoft 会减少其 Marketplace 服务费用从 20%（如在 Microsoft 发布方协议中所述） 为 10%。 为了使产品/服务来限定，至少一个产品/服务必须已指定由 Microsoft 不会成为 IP 合作销售就绪或 IP 共同销售设置优先级。  资格必须满足每个日历月结束前的至少五 （5） 个工作日才能月接收此简化的 Marketplace 服务费。  减少了 Marketplace 服务费用不适用于 Vm、 管理的应用程序或通过我们的商业应用商店提供的任何其他产品。  减少了 Marketplace 服务费用将仅可供限定产品/服务为 2019 年 5 月 1 日和于 2020 年 6 月 30 日之间的 Microsoft 收集的许可证费用。  此时间后，Marketplace 服务费用将返回到其正常量。 

|**Microsoft 收费**|**每月 100 美元**|
|:---|:---|
|Microsoft 将许可证费用的 80% 支付给你 <br>**对于限定 SaaS 应用程序，Microsoft 支付许可证费用的 90%*|80\.00 美元/月 <br>*$* 每个月 * 90.00|


#### <a name="csp-program-opt-in"></a>CSP 程序参加
[云解决方案提供商 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)程序，以达到数以百万计的合格的 Microsoft 客户只需极少的软件产品/服务市场营销和销售方面的投资。

- **通道：使我的产品/服务在 CSP 计划中可用**（复选框）

选择在 CSP 计划中提供产品/服务使云解决方案提供商向其客户作为捆绑解决方案的一部分销售您的产品。 

### <a name="list-through-microsoft"></a>通过 Microsoft 的列表

创建 marketplace 列表将提升您的企业提供 Microsoft。 选择列表仅产品/服务和服务并不通过 Microsoft 意味着，Microsoft 不直接参与软件许可证的事务。 没有任何关联的事务费用，发布服务器上保留的任何软件许可费用从客户收集的 100%。 但是，发布服务器是负责支持的软件许可证事务，包括但不是限于所有方面： 订单履行、 计数、 计费、 发票、 付款和集合。 

- **你希望如何潜在客户交互，此列表的产品/服务？**

##### <a name="get-it-now-free"></a>立即获取 （免费）
列出产品/服务向客户免费提供有效的 URL （http 或 https 的开头），他们可以访问您的应用程序。  例如： `https://contoso.com/saas-app`

##### <a name="free-trial"></a>免费试用
列出客户免费试用版产品/服务通过提供有效的 URL （http 或 https 的开头），他们可以访问您的应用程序。  例如： `https://contoso.com/trial/saas-app`

##### <a name="contact-me"></a>与我联系
通过连接您的客户关系管理 (CRM) 系统来收集客户联系人信息。 客户会要求共享其信息的权限。 这些客户的详细信息，以及产品/服务名称、 ID 和 marketplace 源它们在其中找到产品/服务，将发送到已配置的 CRM 系统。 有关配置你的 CRM 的详细信息，请参阅[Connect 潜在客户管理](#connect-lead-management)。 

## <a name="example-marketplace-offer-listing"></a>示例 marketplace 产品/服务列表

![示例 marketplace 产品/服务的说明列表](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>启用体验版

测试驱动器是通过为他们提供的选项在购买前试用，从而导致更高的转换以及高度发掘潜在客户生成展示给潜在的客户的产品/服务的好办法。 [了解有关体验版的详细信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **启用测试驱动器**（复选框） 

通过启用体验版，你将需要配置供客户试用产品/服务存在一段固定的时间在演示环境。 

### <a name="type-of-test-drive"></a>测试驱动器的类型

- **[Azure 资源管理器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** :包含组成解决方案的所有 Azure 资源的部署模板。 适合此方案的产品仅使用 Azure 资源。
- **[Dynamics 365 业务中心](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** :Microsoft 托管和 Business Central 企业资源计划系统维护测试驱动器服务 （包括预配和部署） (财务、 操作、 供应链，CRM，等等。)。  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** :Microsoft 托管和客户参与系统 （销售、 服务、 项目服务、 现场服务等） 维护测试驱动器服务 （包括预配和部署）。  
- **[操作的 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** :Microsoft 托管和 Finance and Operations 企业资源规划 （财务、 操作、 生产、 供应链等） 的系统维护测试驱动器服务 （包括预配和部署）。 
- **[逻辑应用](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** :部署模板，其中包含所有复杂的解决方案体系结构。 任何自定义产品应使用此类测试驱动器。
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** :自定义构建的仪表板嵌入的链接。 要演示交互式 Power BI 视觉对象应使用这种体验版的产品。 此处只需要上传你的嵌入式 Power BI URL。

#### <a name="additional-test-drive-resources"></a>其他测试驱动器资源
- [测试驱动器技术最佳实践](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [体验版市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [测试驱动器概述一个页导航](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>连接潜在客户管理

通过列出产品/服务市场中的并挂接客户关系管理 (CRM) 系统，以便客户表达感兴趣或部署后，立即可以接收客户联系信息直接与客户联系你产品。

- **选择销售线索目标**（下拉菜单）：提供连接到 CRM 系统，你想要向其发送潜在客户的详细信息。 

合作伙伴中心支持以下的 CRM 系统的潜在客户管理。 选择安装程序说明的链接。

- Azure Blob – 提供联系人的电子邮件、 容器名称和存储帐户连接字符串。 
- [Azure 表](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table)– 提供联系人电子邮件和存储帐户连接字符串。 
- [Dynamics CRM Online](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) – 提供联系人电子邮件、 URL 和身份验证模式 （Office 365 或 Azure Active Directory）。
- [Https 终结点](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https)– 提供联系人电子邮件和 HTTPS 终结点 URL。 
- [Marketo](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) – 提供联系人电子邮件、 窗体 ID、 Munchkin 帐户 ID 和服务器 id。
- [Salesforce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) -提供联系人电子邮件和组织 id。 

#### <a name="additional-lead-management-resources"></a>其他潜在客户管理资源
- [潜在客户管理常见问题解答](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [常见的潜在顾客配置错误](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [会导致管理概述一个页导航](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

请记住**保存**才能转到下一部分 ！

## <a name="properties"></a>属性
**属性**选项卡会要求您定义的类别和行业用于分组产品/服务市场，支持产品/服务和你的应用程序版本的法律协定上。 

选择**保存**之后完成这些字段。 

### <a name="category"></a>类别
选择最少的一 （1） 和最多三 （3） 用于分组到相应的 marketplace 搜索领域的产品/服务的类别。 请指出产品/服务如何支持这些类别的产品/服务说明中。 

### <a name="industry"></a>行业
选择用于分组到相应的 marketplace 搜索领域的产品/服务的行业最多两 （2）。 如果产品/服务不是特定于行业的则选择一个。 请指出产品/服务如何支持产品/服务说明中的所选的行业。 

### <a name="app-version"></a>应用版本
这是一个可选字段 AppSource marketplace 中用于标识产品/服务的版本号。 

### <a name="standard-contract"></a>标准合同

- **使用标准约定？**

若要简化客户的采购过程并降低的软件供应商的法律的复杂性，Microsoft 以帮助简化在 marketplace 中的事务提供标准协定模板。 

而不是编写自定义条款和条件，Azure Marketplace 发布者可以选择提供标准的合同，客户只需以审查并接受一次软件。 

标准协定可在此处找到： https://go.microsoft.com/fwlink/?linkid=2041178 。

#### <a name="terms-of-use"></a>使用条款

如果从标准约定不同许可条款，你可以选择输入此处使用自己法律条款。 此外可以在此字段中输入文本的最多 10,000 个字符。 如果你的使用条款的需要更长的说明，请输入此字段可以在其中找到其他许可条款的单个 URL 链接。 它将向客户显示为活动的链接。

客户必须接受这些条款才能试用应用。 

请记住**保存**才能转到下一部分 ！

## <a name="offer-listing"></a>产品/服务列表

列出产品/服务是可用的语言 （和市场） 选项卡上显示的产品/服务，英语 （美国） 目前可用的唯一位置。 此外，此页显示特定于语言的列表和已添加的日期/时间的状态。 需要为每种语言定义 （提供名称、 说明、 搜索词等） 的应用商店详细信息 / 市场。

### <a name="offer-listings"></a>套餐列表

提供要显示在 marketplace 中包括的产品/服务说明和市场营销资产的详细信息。

- **名称**（必需）：此处定义的名称将显示为标题的产品/服务在你已选择的 marketplace(s) 上列出。 名称预先填充在基于以前**新产品/服务**条目。  这可能被商标。  这必须不能包含空格，表情符号 （除非它们是商标和版权符号），并且必须限制为 50 个字符。
- **摘要**（必需）：提供产品/服务在 marketplace 列表搜索结果中使用的简短说明。 可以在此字段中输入最多 100 个字符的文本。
- **说明**（必需）：提供的产品/服务会显示在 marketplace 清单概述的说明。 请考虑包括的价值主张、 优势、 任何类别或行业的关联，应用内购买的机会，所需的任何公开信息，以及若要了解详细信息的链接。
可以在此字段中输入最多 3,000 个字符的文本。 更多技巧，请参阅文章[编写出色的应用程序的说明](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)。
- **搜索关键字**:输入的客户可以使用 marketplace(s) 中查找产品/服务最多三个搜索关键字。
- **入门说明**（必需）：介绍如何配置并开始为潜在客户使用你的应用。  本快速入门中可以包含指向更详细的联机文档。 可以在此字段中输入最多 3,000 个字符的文本。 

#### <a name="links"></a>链接

- **隐私策略**（必需）：链接到你组织的隐私策略。 你负责确保您的应用程序符合隐私法律和法规，并提供有效的隐私策略
- **CSP 计划的市场营销材料**（可选）：必须提供到市场营销材料，如果你选择扩展到产品/服务的链接[云解决方案提供商 (CSP)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers)程序。 CSP 使 CSP 合作伙伴能够捆绑，市场中，然后转售产品/服务扩展到更广泛的合格的客户的产品/服务。 这些分销商将需要访问产品/服务的市场营销活动的材料。 有关详细信息，请参阅[到市场服务](https://partner.microsoft.com/reach-customers/gtm)。
- **有用的链接**（可选）：有关你的应用或通过提供列出的相关的服务的可选补充联机文档**标题**并**URL**。 通过单击来添加其他有用的链接 **+ 添加 URL**。

#### <a name="contact-information"></a>联系信息

- **联系人**:对于每个客户联系，提供某位员工**名称**，**电话号码**，并**电子邮件**地址。  (这些*将不会*公开显示)。 一个**支持 URL**还需要**支持联系人**组。  (此信息*将*公开显示)。

**支持联系人**（必需）：有关常规支持问题。

**工程联系人**（必需）：有关技术问题。

**通道管理器联系**（必需）：为分销商与 CSP 计划相关的问题。

#### <a name="files-and-images"></a>文件和映像

- **文档**（必需）：以 PDF 格式，提供三 （3） 每个的文档的产品/服务的上限和下限的一 （1） 添加相关产品/服务，市场营销文档。
- **映像**（可选）：有多个产品/服务的徽标图像整个 marketplace(s)，需要以下大小-小可能出现的位置的位置：48 x 48 像素 _（必需）、_ 中等：90 x 90 像素，大：216x216 像素 _（必需）、_ 宽：255x115 像素，并且 Hero:815 x 290 像素。 所有映像必须都可在中。PNG 格式。
- **屏幕截图**（必需）：添加屏幕截图演示产品/服务。 最多五 （5） 的屏幕截图可能会添加，应在 1280 x 720 像素调整大小。 所有映像必须都可在中。PNG 格式。
- **视频**（可选）：将链接添加到的视频，演示产品/服务。 可以使用 YouTube 和/或 Vimeo 视频链接，这将随产品/服务一起呈现给客户。 您还需要输入视频的缩略图像的大小调整为 1280 x 720 像素的 PNG 格式。 您可以显示每个产品/服务的四个视频的最大值。

请记住**保存**才能转到下一部分 ！

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出的资源

- [最佳做法的 marketplace 产品/服务列表](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>预览

**预览版**选项卡可以定义有限**预览受众**用于释放之前向更广泛的 marketplace 用户发布产品/服务实时产品/服务。

> [!IMPORTANT]
> 必须选择**投入**产品/服务将发布实时到 marketplace 公共受众后检查中预览产品/服务之前。

- **定义预览受众：添加每行，以及可选的描述单个 AAD/MSA 帐户电子邮件。**

手动添加最多十 （10） 电子邮件地址或 20 (20)，如果上传 CSV 文件中，现有 Microsoft 帐户 (MSA) 或 Azure Active Directory (AAD) 帐户如何进行验证之前发布产品/服务实时。 通过添加这些帐户，可以定义将有权预览产品/服务发布到 marketplace(s) 之前的受众。 如果产品/服务已激活，您仍可以用于测试的任何更改或更新产品/服务定义预览受众。

> [!NOTE]
> 预览受众不同于专用受众。 预览受众允许对产品/服务的访问_以前_到实时在商店中发布。 您还可以选择创建计划并使其可仅供专用的受众。 在中**计划列表**选项卡上，可以定义与专用受众**这是私有计划**复选框。 然后，您可以定义最多 20,000 个客户使用 Azure 租户 Id 的私有访问群体。

## <a name="technical-configuration"></a>技术配置

**技术配置**选项卡将定义用于连接到你的产品/服务的技术细节 （URL 路径、 webhook、 租户 ID 和应用程序 ID）。 此连接使我们能够为客户的 Azure 订阅中的资源设置产品/服务，如果用户选择获取它。

- **登录页 URL** （必需）：定义 URL 将客户定向以登录后获取从 marketplace 产品/服务的站点。 此 URL 也是接收连接 API 以方便与 Microsoft 开展商务活动的终结点。

- **连接 webhook** （必需）：对于 Microsoft 需要代表客户向你发送的所有异步事件（示例：Azure 订阅已无效），我们要求你提供连接 webhook。 如果还没有就地 webhook 系统，最简单的配置是将 HTTP 终结点逻辑应用，它将侦听的任何事件发布到其并适当地处理它们 (例如 https:\//prod-1westus.logic.azure.com:443/work)。 有关详细信息，请参阅[在逻辑应用中使用 HTTP 终结点调用、触发或嵌套工作流](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)。

- **Azure AD 租户 ID** （必需）：在 Azure 门户中，我们要求您[创建 Azure Active Directory (AD) 应用](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)位于经过身份验证的通信，以便我们可以验证我们的两个服务之间的连接。 若要查找[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)，转到 Azure Active Directory，然后选择**属性**，然后查找**Directory ID**数列出 （例如50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 应用 ID** （必需）：您还需要您[应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)和身份验证密钥。 若要获取这些值，请转到 Azure Active Directory，并选择**应用注册**，然后寻找**应用程序 ID**数列出 (例如 50c464d3-4930-494c-963c-1e951d15360e)。 若要查找的身份验证密钥，请转到**设置**，然后选择**密钥**。 需要提供的说明和持续时间，然后将提供一个数字值。

 请注意，Azure 应用程序 ID 是与发布服务器 ID 相关联，因此请确保，所有产品/服务中使用相同的应用程序 ID。

## <a name="plan-overview"></a>计划概述

**计划概述**选项卡可以提供了各种计划中的选项相同的产品/服务。 （有时称为 Sku） 这些计划可以具有不同的版本、 货币化率或服务层。 必须设置至少一个计划，若要在 marketplace 中销售。

创建后，你将看到你计划名称、 Id、 定价模型，可用性 （公共或私有），当前发布状态以及任何可用的操作。

**操作**推出**计划概述**取决于你的计划的当前状态，并且可能包括：

- 如果计划状态为**草稿**– 删除草稿
- 如果计划状态为**Live** – 停止销售计划或同步专用受众

**创建新的计划**（最小的一个计划的用户选择此选项可以通过 Microsoft 销售）

- **计划 ID:** 在此产品/服务中创建每个计划的唯一计划 ID。 此 ID 将可看到客户在产品 URL 和 Azure 资源管理器模板 （如果适用）。 使用仅小写字母、 字母数字字符、 短划线或下划线。 50 个字符最多允许为此计划 id。 请注意选择在创建后无法修改 ID。
- **计划名称：** 确定该计划选择产品/服务中时，客户将看到此名称。 在此产品/服务中创建每个计划的唯一产品/服务名称。 计划名称用于区分可能属于同一个产品/服务 （例如软件计划 产品/服务名称：Windows Server;计划：Windows Server 2016 中，Windows Server 2019）。

### <a name="plan-listing"></a>计划列表

**计划列表**选项卡显示你的计划是可用的语言 （和市场） 英语 （美国） 目前可用的唯一位置。 此外，此页显示特定于语言的列表和已添加的日期/时间的状态。 需要为每种语言定义 （提供名称、 说明、 搜索词等） 的应用商店详细信息 / 市场。

#### <a name="plan-listing-details"></a>计划列表详细信息

选择一个计划语言将显示**计划列表**信息，包括**名称**和**说明。**

- **名称**：预填充基于预览版**新的计划**条目，将显示为产品/服务的"软件计划的"显示在 marketplace 中的标题。
- **说明：** 此说明是解释为什么说此软件计划独一无二的机会和与其他软件计划内的产品/服务的任何差异。 可能包含最多 500 个字符。

选择**保存**之后完成这些字段。

#### <a name="plan-pricing-and-availability"></a>计划定价和可用性

**定价和可用性**选项卡可以配置此计划将在中，提供市场所需的货币化模型、 价格和计费的术语。 此外，您可以指示是否使该计划为每个人或仅为特定客户 （专用受众） 可见。

#### <a name="markets"></a>市场

- **编辑市场**（可选）

必须至少一个市场中可用的每个计划。 选择你想要使此计划可用的任意市场位置旁的复选框。 包括在内，以帮助搜索框和按钮，选择"税务免税"国家/地区，在其中 Microsoft remits 销售和你的代表销项税。 


如果你已为你计划在美国美元 (USD) 设置价格，并添加另一个市场位置，将根据当前的汇率计算新市场的价格。 始终应检查每个市场发布之前的价格。 可以通过将保存所做的更改后，使用"导出价格 (xlsx)"链接查看定价。

#### <a name="pricing"></a>定价

- 定价模型：  固定的费率或基于席位

**固定的费率：** 启用对使用单一的月度或年度价格固定的费率价格产品/服务的访问。 这有时称为基于站点的定价。

**基于席位：** 启用对产品/服务的访问使用基于访问产品/服务或占用的用户数的价格*席位*。 此基于席位的模型使您可以设置所需的最低和最大允许的座位数根据价格。 这样一来，可以通过配置多个计划基于用户数量配置不同的价格点。  这些字段是可选的。 如果留空，将被当做席位数没有限制 （最小值为 1） 和系统可以支持的任意数量的最大值。 作为对你的计划的更新的一部分，可以编辑这些字段。

发布后，无法更改计费的定价模型选择。 此外，相同的产品/服务的所有计划必须都共享相同的定价模式。

- **计费术语**:月度或年度

选择客户必须付出代价列出的频率。 必须提供至少一个月度或年度订阅价格，或这两个选项可提供给客户。

- **价格**:美元每月或每年的美元

价格以当地货币表示的集 (美元 = 美国美元) 转换为使用当前汇率安装过程中提供的所有选定市场的本地货币。 通过导出定价电子表格并查看每个市场中的价格来验证发布前的这些价格。 如果你想要在一个单独的市场中设置自定义价格，修改并导入定价电子表格。 您有责任验证此定价，拥有这些设置。
**必须先保存定价更改以便导出定价数据。*

查看发布之前仔细价格，因为没有什么发布计划后可以更改一些限制：

- 一旦发布计划，无法更改定价模型。
- 后计费的术语发布的一个计划，不能删除它更高版本。
- 一旦发布你的计划中的市场的价格，不能以后更改。

### <a name="plan-audience"></a>计划的受众

可以选择要配置以供所有人或仅所选的特定受众才能看到每个计划。 您可以分配此受限用户是使用 Azure AD 租户 Id 中的成员身份。

#### <a name="privacy"></a>隐私

- **这是私有计划**（可选复选框）

选中此框可使你的计划，专用和仅对所选的受限制的访问群体可见。 一旦发布为私有计划，可以将访问群体更新或选择以使所有人都可使用该计划。 后一个计划发布为对每个人可见，则必须始终保持对每个人都可见。 （该计划不能配置为专用计划再次）。

- **受限访问群体 (租户 Id)**

将分配有权访问此专用计划的受众。 使用租户 Id 的选项包括分配每个租户 ID 的说明来分配访问权限。 可以添加最多 10 个租户 Id，或 20,000 个客户租户 Id，如果导入.csv 电子表格文件。

租户是组织，以 GUID （全局唯一标识符，用来识别资源的 128 位的整数数值） 表示 id 的表示形式。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如注册 Azure、Microsoft Intune 或 Microsoft 365）会收到该实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户。 若要检查租户，登录到 Azure 门户中使用你想要用于管理你的应用程序的帐户。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录和租户 ID (GUID) 以及域。 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。 您还可以查找使用域名 URL 在贵组织的租户 ID: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com)。

虽然 SaaS 产品/服务使用租户 Id 来定义专用的受众，其他产品/服务类型可以使用 Azure 订阅 Id （这也表示为 Guid）。

> [!NOTE]
> 专用的受众 （或受限制的受众） 不同于预览版受众。 在中 **[预览版](#preview)** 选项卡上，可以定义预览受众。 预览受众允许对产品/服务的访问*以前*实时在 marketplace 中发布的产品/服务。 预览受众虽然专用用户指定仅适用于特定计划，可以查看所有计划 (专用与否)，但仅在有限的预览版时测试并验证该计划。

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>计划内的 marketplace 产品/服务的示例列表

![列出与说明示例 marketplace 计划](./media/marketplace-plan.svg)

## <a name="test-drive"></a>体验版

**测试驱动器**选项卡可以设置演示 （或"试用"） 这将使客户试用产品/服务提交之前来购买它。 在本文中了解更多信息[什么是体验版？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。 如果不再想要提供体验版产品/服务，返回到 **[提供了安装程序](#offer-setup)** 页上，取消选中**启用测试驱动器**。

### <a name="technical-configuration"></a>技术配置
以下类型的测试驱动器都可用，每个都有其自己的技术配置要求。

- [Azure 资源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [逻辑应用](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) （不是必需的技术配置）

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>技术配置 Azure 资源管理器测试驱动器

包含组成解决方案的所有 Azure 资源的部署模板。 适合此方案的产品仅使用 Azure 资源。 了解有关设置详细[Azure 资源管理器体验版](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **区域**（必需）：目前有 26 个其中体验版可提供的支持 Azure 的区域。 通常情况下，想要提供体验版希望最大数量的客户，在区域中，以便他们可以选择最靠近的区域为获得最佳性能。 您需要确保你的订阅允许部署所有中每个区域选择所需的资源。

- **Instances**：选择的类型 （热或冷） 和数字的可用实例，将提供产品/服务的区域数的乘积。

**Hot**：这种类型是实例的已部署并且正在等待每个所选区域的访问。 客户可以立即访问*热*测试之用，而无需等待部署的实例。 弊端在于这些实例始终在 Azure 订阅上运行，因此，它们会产生较高的运行时间成本。 强烈建议至少一个*热*实例，因为大多数客户不想等待完整部署，如果没有导致在客户的使用情况下车*热*实例不可用。

**冷**:这种类型的实例表示可能是每个区域进行部署的实例的总数。 冷实例需要整个测试驱动器资源管理器模板部署时客户请求体验版，因此*冷*实例都要慢得多加载比*热*实例。 不利的一面是，只需支付的体验版持续时间，它是*不*始终为与在 Azure 订阅上运行*热*实例。

- **测试驱动器 Azure 资源管理器模板**:上传包含你的 Azure 资源管理器模板.zip。  详细了解如何快速入门文章中创建的 Azure 资源管理器模板[创建和使用 Azure 门户部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。

- **体验版持续时间**（必需）：输入测试驱动器将保持活动状态，在数小时的时间的长度。 此时间段结束后，体验版会自动终止。 此持续时间可能仅打赌集小时数的整数部分 （例如"2"小时"1.5"不是有效）。

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>技术配置 Dynamics 365 测试驱动器

Microsoft 可以消除托管和维护服务的预配和部署使用此类型的体验版的设置体验版的复杂性。 这种类型的托管的体验版的配置是而不考虑是否测试驱动器以目标 Business Central、 客户的参与情况或操作的访问群体相同的。

- **最大并发测试驱动器**（必需）：设置可以一次使用体验版的客户的最大数目。 测试驱动器处于活动状态，因此将需要确保有足够的许可证可用于支持设置的最大限制时，每个并发用户将使用 Dynamics 365 许可证。 建议的值为 3-5。

- **体验版持续时间**（必需）：输入通过定义的小时数的测试驱动器将保持活动状态的时间的长度。 在数小时后该会话将结束并不能再使用一个许可证。 我们建议值为 2 到 24 个小时，具体取决于产品/服务的复杂性。 此持续时间可能仅打赌集小时数的整数部分 （例如"2"小时"1.5"不是有效）。  如果它们会时间不够，并且想要再次访问体验版，用户可以请求一个新的会话。

- **实例 URL** （必需）：客户将在其中开始其体验版的 URL。 通常使用示例数据安装运行您的应用程序在 Dynamics 365 实例的 URL (例如 https://testdrive.crm.dynamics.com) 。

- **实例 Web API URL** （必需）：检索通过登录到你的 Microsoft 365 帐户并导航到 Dynamics 365 实例的 Web API URL**设置** \&g t;**自定义** \&g t;**开发人员资源** \&g t;**实例 Web API (服务根 URL)** ，复制以下网页中找到的 URL (例如 https://testdrive.crm.dynamics.com/api/data/v9.0) 。

- **角色名称**（必需）：提供自定义的 Dynamics 365 测试驱动器中定义的安全角色名称。 这将分配给该用户在其测试驱动器 （例如测试的汽车-角色）。

#### <a name="technical-configuration-for-logic-app-test-drive"></a>逻辑应用体验版的技术配置

任何自定义产品应使用此类型的测试驱动器部署模板，其中包含多种复杂的解决方案体系结构。 测试驱动器的有关设置逻辑应用的详细信息，请访问[Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)并[Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) GitHub 上。

- **区域**（必需、 单选下拉列表中）：目前有 26 个其中体验版可提供的支持 Azure 的区域。 逻辑应用的资源将部署在所选的区域。 如果逻辑应用具有特定区域中存储任何自定义资源，请确保此处选择该区域。 若要执行此操作的最佳方法是完全部署在 Azure 门户中订阅本地逻辑应用并验证它正常进行此选择之前。

- **最大并发测试驱动器**（必需）：设置可以一次使用体验版的客户的最大数目。 已部署的驱动器，从而使客户能够立即访问它们而无需等待部署这些测试。

- **体验版持续时间**（必需）：输入测试驱动器将保持活动状态，在数小时的时间的长度。 此时间段结束后，测试驱动器会自动终止。

- **Azure 资源组名称**（必需）：输入[Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)保存逻辑应用体验版的名称。

- **Azure 逻辑应用名称**（必需）：输入分配给用户的体验版的逻辑应用的名称。 此逻辑应用必须保存在更高版本的 Azure 资源组中。

- **取消预配逻辑应用名称**（必需）：输入客户运行完成后取消设置体验版的逻辑应用的名称。 此逻辑应用必须保存在更高版本的 Azure 资源组中。

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>对于 Power BI 体验版不是必需的技术配置

要演示交互式 Power BI 视觉对象可以使用的嵌入的链接共享自定义构建的仪表板作为其体验版，无需进一步的技术配置所需的产品。 了解有关设置详细[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)模板应用程序。

### <a name="deployment-subscription-details"></a>部署的订阅详细信息

若要部署你的名义体验版，请创建并提供单独的、 唯一的 Azure 订阅。 （非所需的 Power BI 体验版）。

- **Azure 订阅 ID** （需要 Azure 资源管理器和逻辑应用）：输入订阅 ID，授予对资源使用情况报告和计费在 Azure 帐户服务访问权限。 我们建议你考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)如果你还没有用于体验版。 你可以通过登录到找到你的 Azure 订阅 ID [Azure 门户](https://portal.azure.com/)并导航到**订阅**的左侧菜单的选项卡。 选择选项卡会显示你的订阅 ID (例如"a83645ac-1234年-5ab6-6789-1h234g764ghty")。

- **Azure AD 租户 ID** （必需）：输入在 Azure Active Directory (AD)[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 Active Directory 选项卡，选择**属性**，然后查找**Directory ID**数列出 （例如50c464d3-4930-494c-963c-1e951d15360e)。 你还可以查看你组织的使用在你的域名 URL 的租户 id: [ https://www.whatismytenantid.com ](https://www.whatismytenantid.com)。

- **Azure AD 租户名称**（需要动态 365）：输入你的 Azure Active Directory (AD) 名称。 若要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角你的租户名称将列在你的帐户名称。

- **Azure AD 应用 ID** （必需）：输入在 Azure Active Directory (AD)[应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 Active Directory 选项卡，选择**应用注册**，然后查找**应用程序 ID**数列出 (例如 50c464d3-4930-494c-963c-1e951d15360e)。

- **Azure AD 应用密钥**（必需）：输入在 Azure Active Directory (AD)[应用程序密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 Active Directory 选项卡，选择**应用注册**，然后选择**设置** > **密钥**。

请记住**保存**才能转到下一部分 ！

### <a name="test-drive-listings-optional"></a>测试驱动器清单 （可选）

**体验版节目表**下找到选项**测试驱动器**选项卡显示体验版是可用的语言 （和市场） 英语 （美国） 目前可用的唯一位置. 此外，此页显示特定于语言的列表和已添加的日期/时间的状态。 您需要定义测试驱动器的详细信息 （说明、 用户手册、 视频等） 每个语言/市场。

- **说明**（必需）：介绍了体验版，将演示内容，用户尝试使用、 多功能有待探索，和任何相关信息以帮助用户确定是否要获取产品/服务的目标。 可以在此字段中输入最多 3,000 个字符的文本。 

- **访问信息**（所需的 Azure 资源管理器和逻辑测试驱动器）：说明客户需要知道才能访问和使用此测试驱动器。 演练使用产品/服务和完全客户应掌握的知识来访问在整个测试驱动器的功能的情况。 可以在此字段中输入最多 10,000 个字符的文本。

- **用户手册**（必需）：你的体验版深入演练。 用户手册应涵盖真正需要的客户遇到体验版获得并用作任何问题，它们可能具有的引用。 必须以 PDF 格式并上传后命名为 （255 个字符最大值） 文件。

- **视频：将视频添加**（可选）：可以上传到 YouTube 或 Vimeo 视频和此处引用的链接和缩略图图像 （533x324 像素为单位），以便客户可以查看信息以帮助他们更好地了解测试驱动器，包括如何成功使用的功能的演练操作你产品/服务和了解突出其优点的方案。
  - **名称**（必需）
  - **URL （YouTube 或 Vimeo 仅）** （必需）
  - **缩略图 (533 x 324px)** :图像文件必须为 PNG 格式。

选择**保存**之后完成这些字段。

## <a name="publish"></a>发布

#### <a name="submit-offer-to-preview"></a>提交产品/服务预览

完成的产品/服务所需的所有部分后，选择**发布**在门户的右上角。 你将重定向到**评审并将其发布**页。 

如果这是你首次发布此产品/服务，你可以：

- 请参阅产品/服务的每个部分的完成状态。
    - *未启动*– 意味着未动部分中，需要完成。
    - *不完整*– 意味着本部分包含需要修复的错误或要求提供的详细信息。 请返回到区域，并更新它。
    - *完整*– 表示部分已完成、 已提供所有必需的数据和没有任何错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。
- 提供测试说明到的认证团队，以确保您的应用程序是否正确，除了任何补充说明有助于你了解你的应用中进行测试。
- 通过选择提交用于发布产品/服务**提交**。 我们将发送一封电子邮件，告知你产品/服务的预览版本时，供你审阅并批准。 必须返回到合作伙伴中心，然后选择**上线**产品/服务发布到公共 （或如果专用产品/服务，向专用受众） 产品/服务。

## <a name="next-steps"></a>后续步骤

- [更新商业 Marketplace 中的现有产品/服务](./update-existing-offer.md)
