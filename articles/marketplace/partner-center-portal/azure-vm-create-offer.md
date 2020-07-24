---
title: 在 Azure 市场创建 Azure 虚拟机产品/服务
description: 了解如何使用所需的计划在 Azure Marketplace 上创建虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: mingshen
ms.date: 07/17/2020
ms.openlocfilehash: 6df1620f2d1097ac23e5dfa3903fefc74d13fa65
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012019"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>在 Azure 市场创建 Azure 虚拟机产品/服务

本文介绍如何在 [Azure 市场](https://azuremarketplace.microsoft.com/)创建 Azure 虚拟机产品/服务并将其发布到 Azure 市场上。 它介绍基于 Windows 和基于 Linux 的虚拟机（包含操作系统、虚拟硬盘 (VHD) 以及多达 16 个数据磁盘）。 

开始前，首先[在合作伙伴中心创建商业市场帐户](create-account.md)。 确保帐户已注册加入商业市场计划。

## <a name="introduction"></a>简介

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>发布到 Azure 市场的好处

将产品/服务发布到 Azure 市场时，可以：

- 借助 Microsoft 品牌推广你的公司。
- 覆盖 1 亿以上的 Office 365 和 Dynamics 365 用户以及 200,000 个以上的组织。
- 从这些市场获得优质的潜在顾客。
- 通过 Microsoft 现场和远程销售团队推广你的服务。

### <a name="before-you-begin"></a>开始之前

如果尚未这样做，请查看[虚拟机产品/服务发布指南](../marketplace-virtual-machines.md)和这篇 Azure 虚拟机资料：

- 快速入门指南
  - [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 快速入门模板](https://github.com/azure/azure-quickstart-templates)
- 教程
  - [Linux VM](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../../virtual-machines/windows/tutorial-manage-vm.md)
- 示例
  - [适用于 Linux VM 的 Azure CLI 示例](../../virtual-machines/linux/cli-samples.md)
  - [适用于 Linux VM 的 Azure PowerShell](../../virtual-machines/linux/powershell-samples.md)
  - [适用于 Windows VM 的 Azure CLI 示例](../../virtual-machines/windows/cli-samples.md)
  - [适用于 Windows VM 的 Azure PowerShell](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>技术基础知识

设计、生成和测试产品/服务的过程需要一些时间，并需要在 Azure 平台和生成产品/服务相关技术方面有一定的专业知识。

工程团队对于以下 Microsoft 技术应有基本的了解和实操方面的知识：

- [Azure 服务](https://azure.microsoft.com/services/)
- [设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧窗格中，选择“商业市场” > “概述”。
3. 在“概述”页上，选择“新建产品/服务”  > “Azure 虚拟机”。

    ![显示左窗格菜单选项和“新建产品/服务”按钮的屏幕截图。](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 发布产品/服务后，在合作伙伴中心对其进行的任何编辑仅会在重新发布该产品/服务后出现在 Azure 市场中。 确保对其进行更改后始终重新发布产品/服务。

## <a name="new-offer"></a>新产品/服务

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在 Azure 市场产品/服务、Azure PowerShell 和 Azure CLI 的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 该 ID 可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 该产品/服务别名是用于合作伙伴中心内产品/服务的名称。

- 此名称不用于 Azure 市场。 它不同于向客户显示的产品/服务名称和其他值。

选择“创建”，以生成产品/服务，并继续操作。

## <a name="offer-setup"></a>产品/服务设置

### <a name="test-drive"></a>体验版

体验版是向潜在客户展示产品/服务的一种很好的方式。 它为客户提供“购买前试用”选项，有助于提升购买转换率并催生高度合格的潜在顾客。 有关详细信息，请参阅[什么是体验版](../what-is-test-drive.md)。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。

其他体验版资源：

- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [市场营销最佳做法](../what-is-test-drive.md)
- [下载体验版概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)的 PDF 文件（确保弹出窗口阻止程序处于关闭状态）。

### <a name="customer-leads"></a>潜在顾客

将产品/服务发布到合作伙伴中心的商业市场时，将其连接到客户关系管理 (CRM) 系统。 这样，只要有人对你的产品表示兴趣或使用你的产品，你就可以收到客户的联系信息。 如果要启用体验版，连接到 CRM（参阅上一节）是必选项。 否则，连接到 CRM 是可选的选项。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > 如果此处未列出你的 CRM 系统，请使用 [Azure 表存储](commercial-marketplace-lead-management-instructions-azure-table.md)或 [ 终结点](commercial-marketplace-lead-management-instructions-https.md)存储潜在顾客数据。 然后将数据导出到你的 CRM 系统。

1. 在合作伙伴中心内发布时，将产品/服务连接到潜在客户目标。
1. 确认是否正确配置了与潜在客户目标的连接。 当你在合作伙伴中心内发布它后，Microsoft 会验证连接，并向你发送测试潜在客户。 若要在上线前预览产品/服务，也可以通过亲自尝试在预览环境中部署产品/服务来测试潜在客户连接。
1. 确保不断更新与潜在客户目标的连接，以免失去任何潜在客户。

1. 选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

在 "**属性**" 页上，定义用于对 Azure Marketplace 上的产品/服务、应用程序版本以及支持产品/服务的法律合同进行分组的类别。

### <a name="category"></a>Category

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别（可选）。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 "**不适用**"。

请参阅[产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。 虚拟机产品/服务始终显示在 Azure Marketplace 上的**计算**类别下。

### <a name="legal"></a>合法

必须向客户提供产品/服务条款和条件。 可以使用两个选项：

- **使用自己的条款和条件**

   若要提供你自己的自定义条款和条件，请在“条款和条件”框中输入最多 10,000 个字符的文字。 如果需要更长的说明，输入条款和条件的单个 Web 地址。 它将作为活动链接向客户显示。

   客户必须接受这些条款，才能试用你的产品/服务。

- **使用 Microsoft 商业市场标准协定**

   为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供商业市场标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

   选中“使用 Microsoft 商业市场标准协定”复选框，然后在弹出窗口中选择“接受”（可能需要向上滚动才能看见），使用该标准协定 。

   ![屏幕截图显示合作伙伴中心的法律窗格，具有“使用 Microsoft 商业市场标准协定”复选框。](media/use-standard-contract.png)

  > [!NOTE]
  > 使用商业市场的标准协定发布产品/服务后，就无法使用你自己的自定义条款和条件了。 要么根据标准协定提供解决方案，要么根据自己的条款和条件提供解决方案。

  有关详细信息，请参阅 [Microsoft 商业市场的标准协定](../standard-contract.md)。 下载[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178)的 PDF 文件（确保弹出窗口阻止程序处于关闭状态）。

  **标准协定修订**

  标准协定修订让你能够出于简单起见选择标准协定条款，同时为自己的产品或业务创建条款。 只有当客户已查看并接受了 Microsoft 标准协定，才需要查看协定的修订。 有两种类型的修订：

  * **通用修订**：这些修订适用于所有客户的标准协定。 在购买流程中，将向产品/服务的所有客户显示它们。 客户必须先接受标准协定的条款和修订，然后才能使用产品/服务。 可以为每个产品/服务提供一个通用修订。 可以在此框中输入无限数量的字符。 在发现和购买流程中，这些条款会在 AppSource、Azure 市场和/或 Azure 门户中向客户显示。

  * **自定义修订**：这些是对标准协定的特殊修订，通过 Azure 租户 ID 面向特定客户提供。 你可以选择要面向的租户。 在产品/服务的购买流程中，只会向租户中的客户显示自定义修订条款。 客户必须先接受标准协定的条款和修订，然后才能使用产品/服务。

    1. 首先选择“添加自定义修订条款(最多10 条)”。 每个产品/服务最多可以提供 10 个自定义修订条款。 请执行以下操作：

       a. 在“自定义修订条款”框中输入自己的修订条款。 可以输入无限数量的字符。 在 Azure 门户的产品/服务购买流程中，只有为这些自定义条款指定的租户 ID 中的客户会看到它们。

       b. 必需提供租户 ID。 每个自定义修订最多可以面向 20 个租户 ID。 如果添加自定义修订，则必须至少提供一个租户 ID 以在 Azure 中标识客户。 客户可以选择“Azure Active Directory” > “属性”，在 Azure 中找到此项。 目录 ID 值即为租户 ID（例如，50c464d3-4930-494c-963c-1e951d15360e）。 还可以使用[我的 Microsoft Azure 和 Office 365 租户 ID 是什么？](https://www.whatismytenantid.com/)中客户的域名 Web 地址来查找其组织的租户 ID。

       c. 可以根据需要为租户 ID 提供友好说明，以帮助确定修订所面向的客户。

        > [!NOTE]
        > 这两类修订可以相互配对。 在购买期间，自定义修订所面向的客户还将看到标准协定的通用修订。

    1. 选择“保存草稿”，然后继续。

## <a name="offer-listing"></a>产品/服务列表

在“产品/服务列表”页上定义产品/服务详细信息，例如产品/服务名称、说明、链接和联系人。

> [!NOTE]
> 你的产品/服务列表内容（如说明、文档、屏幕截图和使用条款）不一定要使用英语，只要产品/服务说明以短语开头，"此应用程序仅在中可用 \<non-English language> "。 还可以提供一个 URL，链接到使用与产品/服务列表内容中所用语言不同的语言提供内容的站点。

### <a name="marketplace-details"></a>市场详细信息

#### <a name="name"></a>名称

在此处输入的名称作为产品/服务列表的标题向客户显示。 此字段自动填充了在创建产品/服务时在“产品/服务别名”框中输入的名称。 可以稍后更改此名称。 名称：

- 可以添加商标。 可以包括商标和版权符号。
- 不能超过 50 个字符。
- 不得包含表情符号。

#### <a name="search-results-summary"></a>搜索结果摘要

提供要在 Azure 市场搜索结果中显示的产品/服务的简短说明。 最多可包含 100 个字符。

#### <a name="long-summary"></a>长摘要

提供要在 Azure 市场搜索结果中显示的产品/服务的较长。 最多可包含 256 个字符。

#### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 Web 地址 (URL)。 确保你的产品/服务符合隐私法律法规。 还必须在网站上发布有效的隐私策略。

### <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 若要添加链接，请选择“添加链接”，然后填写以下字段：

- **Name**：客户将在详细信息页上看到该名称。
- **链接 (URL)** ：输入链接，可供客户查看联机文档。

### <a name="customer-support-links"></a>客户支持链接

提供支持网站，客户可在其中联系支持团队。

- Azure 全球支持网站
- Azure 政府支持网站

### <a name="partner-support-contact"></a>合作伙伴支持联系人

提供客户打开支持票证时 Microsoft 合作伙伴要使用的联系信息。 Azure 市场中未列出此信息。

- 名称
- 电子邮件
- 电话

### <a name="engineering-contact"></a>工程联系人

提供产品/服务出现问题（包括认证问题）时 Microsoft 要使用的联系信息。 Azure 市场中未列出此信息。

- 名称
- 电子邮件
- 电话

### <a name="azure-marketplace-media"></a>Azure 市场媒体

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊图像会导致提交遭到拒绝。

>[!NOTE]
>如果上传文件时遇到问题，请确保本地网络未阻止合作伙伴中心所使用的 https://upload.xboxlive.com 服务。

#### <a name="azure-marketplace-logos"></a>Azure 市场徽标

按以下四个图像尺寸提供产品/服务徽标的 PNG 文件：

- **小**（48 &times; 48 像素）
- **中**（90 &times; 90 像素）
- **大**（216 &times; 216 像素）
- **宽**（255 &times; 115 像素）

所有这四种徽标都是必需的，它们显示在各种 Azure 市场列表中。

#### <a name="screenshots"></a>屏幕截图

最多添加五个屏幕截图来展示产品/服务的工作方式。 每个屏幕截图大小必须为 1280 &times; 720 像素并采用 PNG 格式。 每个屏幕截图都必须包含一个标题。

#### <a name="videos"></a>视频

最多添加五个视频来演示产品/服务。 这些视频应在外部视频服务上进行托管。 输入每个视频的名称、Web 地址和视频的缩略图 PNG 图像（为 1280 &times; 720 像素）。

有关其他市场列表资源，请参阅[市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)。

选择“保存草稿”，然后继续。

## <a name="preview"></a>预览

 选择“预览”选项卡，然后选择有限的“预览受众”来验证产品/服务，然后再向更广泛的商业市场受众发布产品/服务。

> [!IMPORTANT]
>  在“预览”窗格中检查产品/服务后，选择“投入使用”以向商业市场公共受众发布产品/服务。

预览受众通过 Azure 订阅 ID GUID 以及各自的可选说明来标识。 客户看不到这些字段。 可以在 Azure 门户的“订阅”页上找到 Azure 订阅 ID。

添加至少一个 Azure 订阅 ID，可以单独添加（最多 10 个 ID），也可以通过上传 CSV 文件来添加（最多 100 个 ID）。 通过添加这些订阅 ID，定义在发布产品/服务之前谁可以对其进行预览。 如果产品/服务已推出，则仍可以定义预览受众来测试产品/服务更改或是对产品/服务的更新。

> [!NOTE]
> 预览受众不同于专用受众。 预览受众可以在向 Azure 市场实时发布产品/服务之前来访问它。 预览受众可以查看和验证所有计划，包括仅在将产品/服务完整发布到 Azure 市场之后才向专用受众提供的计划。 专用受众（在计划“定价和可用性”窗格中定义）具有对特定计划的独占访问权限。

选择“保存草稿”，然后继续下一部分。

## <a name="plan-overview"></a>计划概述

在合作伙伴中心内，可以在同一产品/服务中提供各种计划选项。 产品/服务需要至少一个计划（以前称为 SKU），这可能会因盈利受众、Azure 区域、功能或 VM 映像而异。

创建计划后，选择“计划概述”选项卡以显示：

- 计划名称
- 许可证模型
- 受众（公共或专用）
- 当前发布状态
- 可用操作

“计划概述”窗格中的可用操作因计划的当前状态而异。

- 如果计划状态为“草稿”，选择“删除草稿”。
-  如果计划状态为上线，选择“停止销售计划”或“同步专用受众”。

### <a name="create-a-new-plan"></a>创建新计划

选择顶部的“新建计划”。 此时，“新建计划”对话框显示。

在“计划 ID”框中，创建此产品/服务中的每个计划的唯一计划 ID。 客户可以在产品 Web 地址中看到此 ID。 只使用小写字母和数字、破折号或下划线字符，最多包含 50 个字符。

> [!NOTE]
> 选择“创建”后，便无法更改计划 ID。

在“计划名称”框中，输入此计划的名称。 客户在确定要在产品/服务中选择哪个计划时会看到此名称。 创建明确指出计划之间差异的唯一名称。    例如，可以输入具有即用即付、BYOL、高级和企业计划的 Windows Server。

选择“创建”。

### <a name="plan-setup"></a>计划设置

为计划类型设置高级别的配置，指定它是否重复使用其他计划中的技术配置，并确定应在其中提供该计划的 Azure 区域。 此处的选择确定同一计划的其他窗格上显示的字段。

#### <a name="reuse-a-technical-configuration"></a>重复使用技术配置

如果有多个具有相同类型的计划，并且包在它们之间完全相同，则可以选择“此计划重复使用其他计划中的技术配置”。 使用此选项，可以为此产品/服务选择相同类型的其他计划，并允许重复使用其技术配置。

> [!NOTE]
> 重复使用其他计划中的技术配置时，整个“技术配置”选项卡会从此计划中消失。 其他计划中的技术配置详细信息（包括将来进行的任何更新）也将用于此计划。 此计划发布之后便无法更改此设置。

#### <a name="azure-regions"></a>Azure 区域

你的计划必须至少在一个 Azure 区域中提供。

选择“Azure 全球”选项可向具有商业市场集成的所有 Azure 全球区域中的客户提供你的计划。 有关详细信息，请参阅[地理区域可用性和货币支持](../marketplace-geo-availability-currencies.md)。

选择“Azure 政府”选项可在 [Azure 政府](../../azure-government/documentation-government-welcome.md)区域中提供你的计划。 此区域为来自美国联邦、州、地方或部落实体的客户，以及有资格为其提供服务的合作伙伴提供受控访问权限。 作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（只位于美国）。

在发布到 [Azure 政府](../../azure-government/documentation-government-manage-marketplace-partners.md)之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)页面中请求试用帐户。

> [!NOTE]
> 在计划已发布并在特定 Azure 区域中可用后，无法删除该区域。

#### <a name="azure-government-certifications"></a>Azure 政府认证

仅当选择“Azure 政府”作为上一部分中的 Azure 区域时，此选项才可见。

Azure 政府服务处理符合特定政府法规和要求的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接只对 Azure 政府客户可见。

选择“保存草稿”，然后继续。

### <a name="plan-listing"></a>计划列表

本部分中，将配置计划的列表详细信息。 此窗格显示可能与相同产品/服务中的其他计划不同的特定信息。

#### <a name="plan-name"></a>计划名称

此字段自动填充了你在创建计划时为计划命名的名称。 此名称在 Azure 市场中显示为此计划的标题。 它最多包含 100 个字符。

#### <a name="plan-summary"></a>计划摘要

提供计划（而不是产品/服务）的简短摘要。 此摘要限制为 100 个字符。

#### <a name="plan-description"></a>计划说明

描述此软件计划的独特之处，以及产品/服务内的计划之间的差异。 仅介绍计划，不介绍产品/服务。 计划说明最多可以包含 2,000 个字符。

选择“保存草稿”，然后继续。

### <a name="pricing-and-availability"></a>定价和可用性

此窗格中，将配置：

- 提供此计划的市场。
- 每小时价格。
- 是让计划对所有人可见，还是仅对特定客户（专用受众）可见。

#### <a name="markets"></a>市场

每个计划必须至少在一个市场中提供。 对于可购买此计划的每个市场位置选中该复选框。 （这些市场中的用户仍可将产品/服务部署到[“计划设置”](#plan-setup)部分中选择的所有 Azure 区域。）“税收减免”按钮显示 Microsoft 会代表你减免销售和使用税的国家/地区。  发布到中国的计划限制为免费计划或自带许可 (BYOL) 计划。

如果已采用美元 (USD) 货币为计划设置了价格并添加另一个市场位置，则系统根据当前汇率计算用于新市场的价格。 请始终在发布之前查看用于每个市场的价格。 保存更改后，选择“导出价格(.xlsx)”来查看定价。

删除市场时，该市场中使用活动部署的客户将无法创建新部署或扩展其现有部署。 现有部署不受影响。

#### <a name="pricing"></a>定价

  对于“许可证模型”，选择“根据使用量每月计费计划”以配置此计划的定价，或选择“自带许可”让客户通过现有许可证使用此计划。

对于根据使用量每月计费计划，请使用以下三个定价条目选项之一：

- **根据核心**：提供每个核心的定价（美元）。 Microsoft 计算每个核心大小的定价，并使用当前汇率将其转换为本地货币。
- **根据核心大小**：提供每个核心大小的定价（美元）。 Microsoft 计算定价，并使用当前汇率将其转换为本地货币。
- **根据市场和核心大小**：为所有市场提供每个核心大小的定价。 可以从电子表格导入价格。

> [!NOTE]
> 保存定价更改，实现定价数据的导出。 发布计划中某个市场的价格后，无法再更改。 若要确保价格发布之前是正确的，请导出定价电子表格，并查看每个市场中的价格。

#### <a name="free-trial"></a>免费试用版

可以为客户提供一个月或三个月的免费试用版。

#### <a name="visibility"></a>可见性

可以设计每个计划，使其对每个人可见，或仅对预选受众可见。 使用 Azure 订阅 ID 在此受限受众中分配成员身份。

**公共**：所有人都可以查看计划。

**专用受众**：使计划仅对预选受众可见。 将其发布为专用计划后，可以更新受众或将其更改为公共。 使计划为公众所见后，该计划必须保持公共状态。 不能将其更改回专用计划。

> [!NOTE]
> 专用或受限受众与在“预览”窗格上定义的预览受众不同。 预览受众可以在向 Azure 市场实时发布产品/服务之前来访问它。 尽管专用受众选项仅适用于特定计划，但预览受众可以查看所有专用或公共计划以进行验证。

**受限受众（Azure 订阅 ID）** ：使用 Azure 订阅 ID 分配将有权访问此专用计划的受众。 根据需要，提供已分配的每个 Azure 订阅 ID 的说明。 如果导入 CSV 电子表格，手动添加最多 10 个订阅 ID 或最多 20,000 个 ID。 Azure 订阅 ID 表示为 GUID，所有字母必须为小写。

>[!Note]
>通过云解决方案提供商计划（CSP）的分销商建立的 Azure 订阅不支持专用产品/服务。

#### <a name="hide-a-plan"></a>隐藏计划

如果虚拟机仅在通过另一个解决方案模板或托管应用程序引用时才被间接使用，请选中此复选框以发布虚拟机，但向可能直接搜索或浏览的客户隐藏它。

> [!NOTE]
> 隐藏的计划不支持预览链接。

选择“保存草稿”，然后继续。

### <a name="technical-configuration"></a>技术配置

提供与此计划关联的映像和其他技术属性。 有关详细信息，请参阅[创建 Azure VM 技术资产](create-azure-container-technical-assets.md)。

> [!NOTE]
>  如果已在“计划设置”选项卡上将此计划配置为重新使用其他计划中的包，则不显示“技术配置”选项卡。

#### <a name="operating-system"></a>操作系统

在“操作系统”窗格中，执行以下操作：

-   对于“操作系统系列”，选择“Windows”或“Linux”操作系统。
-  对于“发布”或“供应商”，选择“Windows 发布”或者“Linux 供应商”。
- 对于“OS 友好名称”，输入一个友好的操作系统名称。 此名称对客户可见。

#### <a name="recommended-vm-sizes"></a>建议的 VM 大小

选择最多六个建议的虚拟机大小显示在 Azure 市场上。

#### <a name="open-ports"></a>打开端口

在部署的虚拟机上打开公共或专用端口。

#### <a name="storage-option-for-deployment"></a>部署的存储选项

对于“磁盘部署选项”，选择客户可用于虚拟机的磁盘部署类型。 Microsoft 建议将部署限制为仅“托管磁盘部署”。

#### <a name="properties"></a>属性

对于“支持加速网络”，选择 VM 是否支持[加速网络](https://go.microsoft.com/fwlink/?linkid=2124513)。

#### <a name="vm-images"></a>VM 映像

为虚拟机映像提供磁盘版本和共享访问签名 (SAS) URI。 为每个 VM 映像添加多达 16 个数据磁盘。 在指定的提交中，每个计划仅提供一个新的映像版本。 映像发布后，无法对其进行编辑，但可以将其删除。 删除版本会阻止新用户和现有用户部署已删除版本的新实例。

- **光盘版本**：正在提供的映像的版本。
- **SAS URI**：Azure 存储帐户中存储操作系统 VHD 的位置。 若要了解如何获取 SAS URI，请参阅[获取 VM 映像的共享访问签名 URI](get-sas-uri.md)。
- 数据磁盘映像也是存储在其 Azure 存储帐户中的 VHD 共享访问签名 URI。
- 计划中的每次提交只添加一个映像。

无论使用何种操作系统，都仅添加解决方案所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。

 继续操作并返回到“计划概述”之前，选择“保存草稿”。

## <a name="resell-through-csps"></a>通过 CSP 转售

可通过向[云解决方案提供商](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) 计划中的合作伙伴提供产品/服务来扩展其覆盖范围。 所有自带许可 (BYOL) 计划将自动选择加入该计划。 还可选择加入到非 BYOL 计划。

选择“保存草稿”，然后继续。

## <a name="test-drive"></a>体验版

设置演示或体验版，让客户在购买产品之前先试用产品/服务固定时段。 若要为客户创建演示环境，请参阅[商业市场中的体验版产品/服务](test-drive.md)。

 若要启用体验版，请在“产品/服务设置”窗格上选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。

其他体验版资源：

- [市场营销最佳做法](../what-is-test-drive.md)
- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [体验版概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)的 PDF 文件（确保弹出窗口阻止程序处于关闭状态）

选择“保存草稿”，然后继续。

## <a name="review-and-publish"></a>审阅和发布

完成产品/服务的所有必需部分后，可以提交产品/服务以供审阅和发布。

在右上方选择“审阅和发布”。

在此窗格中，可以：

- 查看产品/服务的每个部分的完成状态：

  - **未启动**：尚未开始该部分，需要完成。
  - **不完整**：该部分有错误需要修复，或需要你提供更多信息。 有关更新不完整信息的指南，请参阅本文之前的部分。
  - **完整**：该部分完整且无错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- 为认证团队提供“认证说明”，帮助确保正确测试应用程序。 包括测试说明，以及任何有助于团队了解应用程序的补充说明。

若要提交产品/服务进行发布，请选择“审阅和发布”。

Microsoft 将向你发送电子邮件，让你知道何时有预览版产品/服务可供审阅和审核。  若要向公众发布产品/服务（如果是专用产品/服务，将其发布给专用受众），请转到产品/服务的“概述”页面，然后选择“上线”。 当发布正在进行时，产品/服务的状态会显示在页面顶部。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的“手动验证”步骤表示广泛审阅产品/服务及其关联的技术资产。 如果此过程发现产品/服务错误，将收到详细说明问题的认证报告。 只需进行所需的更正并重新发布产品/服务。

## <a name="offer-overview"></a>产品/服务概述

“产品/服务概述”页直观地展示了发布此产品/服务所需的步骤（已完成和进行中），以及每个步骤需要多长时间才能完成。

此页还包含有助于处理产品/服务的链接（具体取决于其状态）：

- 如果产品/服务是草稿：[删除产品/服务草稿](update-existing-offer.md#delete-a-draft-offer)
- 如果产品/服务已上线：[停止销售产品/服务](update-existing-offer.md#stop-selling-an-offer-or-plan)
- 如果产品/服务处于预览状态：[上线](publishing-status.md#publisher-approval)
- 如果尚未完成发布者注销：[取消发布](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>市场示例

下面是有关如何在 Azure Marketplace 中显示产品/服务的示例：

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="说明此产品/服务在 Azure Marketplace 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 大徽标
2. 价格
3. 类别
4. 条款和条件
5. 隐私策略地址（链接）
6. 产品名称
7. 说明
8. 有用链接
9. 屏幕截图/视频

<br>下面是有关如何在 Azure Marketplace 搜索结果中显示产品/服务的示例：

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="说明此产品/服务在 Azure Marketplace 搜索结果中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 小徽标
2. 产品名称
3. 搜索结果摘要
4. 试用

<br>下面是 Azure Marketplace 计划详细信息的示例：

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="说明 Azure Marketplace 计划详细信息。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 计划名称和摘要
2. 建议 VM 大小
3. 计划定价

<br>下面是有关如何在 Azure 门户中显示产品/服务的示例：

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="说明了此产品/服务在 Azure 门户中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 名称
2. 说明
3. 有用链接
4. 屏幕截图/视频

<br>下面是一个示例，说明了产品/服务信息在 "Azure 门户搜索" 结果中的显示方式：

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="说明此产品/服务在 Azure 门户搜索结果中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 小徽标
2. 产品名称
3. 搜索结果摘要

<br>下面是 Azure 门户计划详细信息的示例：

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="说明 Azure 门户计划详细信息。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 计划名称
2. 计划说明

## <a name="next-step"></a>后续步骤

- [更新商业市场中的现有产品/服务](update-existing-offer.md)
