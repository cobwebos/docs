---
title: 创建 Azure 虚拟机产品 /服务 - Azure 应用商店
description: 了解如何在商业市场中创建虚拟机产品/服务。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 161fd9276427db0d0d7d56da1bfc0bb8ccf52a52
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536652"
---
# <a name="create-an-azure-virtual-machine-offer"></a>创建 Azure 虚拟机产品/服务

> [!IMPORTANT]
> 我们将 Azure VM 产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请继续按照云合作伙伴门户中的["创建虚拟机"产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer)中的说明来管理您的产品/服务。

本文介绍如何创建 Azure 虚拟机产品/服务并将其发布到[Azure 应用商店](https://azuremarketplace.microsoft.com/)。 它面向包含操作系统、虚拟硬盘 （VHD） 和多达 16 个数据磁盘的基于 Windows 和 Linux 的虚拟机。

## <a name="introduction"></a>简介

### <a name="publishing-benefits"></a>发布的好处

发布到 Azure 应用商店具有以下优点：

- 使用 Microsoft 品牌推广您的公司
- 通过 Azure 应用商店覆盖超过 1 亿台 Office 365 和 Dynamics 365 用户和 200，000 多个组织
- 从这些市场获得高质量的潜在客户
- 获得 Microsoft 现场和电话销售团队推广的服务

### <a name="before-you-begin"></a>开始之前

如果尚未这样做，请查看[虚拟机提供发布指南](https://aka.ms/Virtualmachineofferpublishingguide)和此 Azure 虚拟机材料：

- 快速入门指南
  - [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 快速入门模板](https://github.com/azure/azure-quickstart-templates)
- 教程
  - [Linux VM](https://aka.ms/LinuxVMtutorial)
  - [视窗 VM](https://aka.ms/windowsvms)
- 示例
  - [Linux VM 的 Azure CLI 示例](https://aka.ms/linuxclisamples)
  - [适用于 Linux VM 的 Azure 电源外壳](https://aka.ms/linuxpowershellsamples)
  - [适用于 Windows VM 的 Azure CLI 示例](https://aka.ms/windowsclisamples)
  - [适用于 Windows VM 的 Azure 电源外壳](https://aka.ms/windowspowershellvmsamples)

### <a name="fundamentals-in-technical-knowledge"></a>技术知识基础知识

设计、构建和测试这些资产需要时间，需要对 Azure 平台和用于构建产品/服务的技术进行技术知识。

您的工程团队应了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的实践知识

## <a name="create-an-azure-virtual-machine-offer"></a>创建 Azure 虚拟机产品/服务

在创建 Azure 虚拟机产品/服务之前，必须在合作伙伴中心中具有商业市场帐户。 如果您尚未创建一个帐户，请参阅[在合作伙伴中心创建商业市场帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)，然后从顶部菜单中选择**仪表板**。
2. 在左导航栏中，选择**商业市场**，然后 **"概述**"。
3. 在 **"概述"** 页上，选择 **+新产品/服务**，然后选择**Azure 虚拟机**。 将显示 **"新建产品/服务**"对话框。

![使用"新建产品/服务"按钮和 Azure 虚拟机产品/服务（已选择）在合作伙伴中心中演示"概述"页面。](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>优惠 ID 和别名

输入**优惠 ID**。 这是您帐户中每个产品/服务的唯一标识符。

- 此 ID 在市场产品/服务的 Web 地址以及 Azure PowerShell 和 Azure CLI（如果适用）中可见。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但没有空格，并且限制为 50 个字符。 例如，如果您在此处输入**测试报价 1，** 则产品/服务 Web 地址将为`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 选择 "**创建**"后，无法更改优惠 ID。

输入**产品/服务别名**。 这是合作伙伴中心中用于产品/服务的名称。 此名称不在市场上使用，并且不同于向客户显示的要约名称和其他值。

选择 **"创建**"以生成产品/服务并继续。

## <a name="offer-setup"></a>优惠设置

### <a name="test-drive"></a>体验版

设置演示（测试驱动器），让客户在购买之前试用您的产品/服务。 要创建一个演示环境，让客户在固定的时间内试用您的产品/服务，请参阅[在商业市场试用您的产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

要启用试驾，请选择"**启用试驾**"复选框。 要从产品/服务中删除试驾，请清除此复选框。

其他试驾资源：

- [技术最佳实践](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [市场营销最佳做法](https://aka.ms/TestDriveMarketingBestPractices)
- [测试驱动器概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)PDF（确保弹出窗口阻止程序已关闭）。

### <a name="lead-management"></a>潜在顾客管理

使用合作伙伴中心将产品/服务发布到商业市场时，请将其连接到您的客户关系管理 （CRM） 系统。 这样，一旦有人对您的产品表示兴趣或使用，您就可以立即接收客户联系信息。 如果要启用 **"测试驱动器**"（请参阅上一节），则需要连接到 CRM，否则它是可选的。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：
    - [动态 365](https://aka.ms/Dyn365LeadMgmt)用于客户互动
    - [Marketo](https://aka.ms/LeadMgmtMarketo)
    - [Salesforce](https://aka.ms/LeadMgmtSalesforce)

    > [!NOTE]
    > 如果上面未列出 CRM 系统，请使用[Azure 表](https://aka.ms/AzureTableLeadMgmt)或[Https 终结点](https://aka.ms/LeadMgmtHTTPS)来存储客户潜在顾客数据。 然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布时，请将您的产品/服务连接到潜在顾客目标。
3. 确认与潜在顾客目标的连接配置正确。 在合作伙伴中心发布后，我们将验证连接并向您发送测试线索。 在产品/服务上线之前预览产品/服务时，还可以尝试在预览环境中自行部署产品/服务，以测试潜在顾客连接。
4. 确保与潜在顾客目标的连接保持更新状态，这样您就不会丢失任何潜在顾客。

选择 **"保存"草稿**，然后再继续。

## <a name="properties"></a>属性

此页面允许您定义用于在市场对产品/服务进行分组的类别和行业、应用版本和支持您的产品/服务的法律合同。

### <a name="categories"></a>类别

选择至少 1 个类别，最多选择五个类别。 这些类别用于将产品/服务放置在相应的市场搜索区域。 在产品/服务说明中，说明您的优惠如何支持这些类别。 虚拟机产品/服务显示在 Azure 应用商店中的 **"计算**"类别下。

### <a name="legal"></a>合法

您必须为优惠提供条款和条件。 可以使用两个选项：

- 使用您自己的条款和条件
- 使用微软商业市场的标准合约

#### <a name="use-your-own-terms-and-conditions"></a>使用您自己的条款和条件

要提供您自己的自定义条款和条件，请在 **"条款和条件"** 框中输入最多 10，000 个字符的文本。 如果您需要较长的说明，请输入指向您的条款和条件的单个网址。 它将作为活动链接向客户显示。

客户必须先接受这些条款，然后才能试用您的优惠。

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>使用微软商业市场的标准合约

为了简化客户的采购流程并降低软件供应商的法律复杂性，Microsoft 为商业市场提供了标准合同。 当您根据标准合同提供软件时，客户只需阅读并接受一次软件，您就无需创建自定义条款和条件。

使用标准合约，选择"**使用 Microsoft 商业市场的标准合约**"复选框，然后在弹出窗口上 **"接受**"（您可能需要向上滚动才能查看）。

![使用"新产品/服务"按钮和咨询服务产品/服务产品，在合作伙伴中心中演示"概述"页面。](media/use-standard-contract.png)

> [!NOTE]
> 使用商业市场的标准合同发布产品/服务后，不能使用您自己的自定义条款和条件。 在标准合同下**或**根据您自己的条款和条件提供您的解决方案。

要了解有关标准合同的更多信息，请参阅 Microsoft[商业市场](https://docs.microsoft.com/azure/marketplace/standard-contract)的标准合约。 您可以将[标准合约](https://go.microsoft.com/fwlink/?linkid=2041178)下载为 PDF（确保弹出窗口阻止程序已关闭）。

##### <a name="standard-contract-amendments"></a>标准合同修订

标准合同修订允许您选择标准合同条款，以便简化，并为您的产品或业务创建条款。 客户只需审核并接受 Microsoft 标准合同，才需要查看合同的修订。 有两种修正案可供选择：通用和习俗。

**通用修订**– 这些修订适用于所有客户的标准合同。 在采购流程中，它们向产品/服务的每个客户显示。 客户必须接受标准合同的条款和修改，然后才能使用您的优惠。 您可以提供每个报价的单一通用修订。 您可以在此框中输入无限数量的字符。 在发现和购买流期间，这些术语在 AppSource、Azure 应用商店和/或 Azure 门户中向客户显示。

**自定义修订**– 这些是标准合同的特殊修订，通过 Azure 租户 ID 面向特定客户。 您可以选择要定位的租户。 只有租户的客户才会在优惠的采购流程中收到自定义修改条款。 客户必须接受标准合同的条款和修改，然后才能使用您的优惠。

首先选择**添加自定义修改条款 （最多 10 个）。** 每个优惠最多可以提供 10 个自定义修订条款。

- **自定义修订条款**– 在自定义修订条款框中输入您自己的修订条款。 您可以输入无限数量的字符。 只有您为这些自定义条款指定的租户 ID 的客户才能在 Azure 门户中的产品/服务采购流中看到这些内容。
- **租户 ID（** 必需） – 每个自定义修订的目标可高达 20 个租户 ID。 如果添加自定义修改，则必须至少提供一个租户 ID，该 ID 标识 Azure 中的客户。 您的客户可以在 Azure 下找到，然后是属性。 目录 ID 值是租户 ID（例如，50c464d3-4930-494c-963c-1e951d15360e）。 您还可以使用我的[Microsoft Azure 和 Office 365 租户 ID](https://www.whatismytenantid.com/)上的域名 Web 地址查找组织的客户的租户 ID。
- **说明**（可选） – 为租户 ID 提供友好的说明，帮助您通过修订确定目标客户的身份。

> [!NOTE]
> 这两种类型的修正相互叠加。 以定制修订为目标的客户在购买过程中还将获得标准合同的普遍修订。

选择 **"保存"草稿**，然后再继续。

## <a name="offer-listing"></a>优惠列表

此页面允许您定义产品/服务详细信息，如产品/服务名称、说明、链接和联系人。

> [!NOTE]
> 报价列表内容（如说明、文档、屏幕截图和使用条款）不需要以英文表示，只要产品/服务说明以短语开头，"此应用程序仅以 [非英语语言]提供。 您还可以提供_有用的链接网址_，以"产品/服务"列表内容中使用的语言以外的语言提供内容。

### <a name="marketplace-details"></a>市场详细信息

#### <a name="name"></a>“属性”

您在此处输入的名称将作为产品/服务列表的标题显示给客户。 此字段预先填充您在创建产品/服务时在 **"产品/服务"别名**框中输入的文本。 可以稍后更改此名称。

名称：

- 可注册商标（您可以包括商标和版权符号）
- 不能超过50个字符长
- 不能包括表情符号。

#### <a name="search-results-summary"></a>搜索结果摘要

您的报价的简短说明。 这最多可达 100 个字符，并且用于市场搜索结果。

#### <a name="long-summary"></a>长摘要

提供更久的报价说明。 这最多可达 256 个字符，并且用于市场搜索结果。

#### <a name="description"></a>说明

提供您的优惠的详细描述，最多 3，000 个字符。 这在商业市场列表概述中向客户显示。

在描述中包括以下一项或多项：

- 您的报价的价值和主要优势
- 类别或行业协会，或两者
- 应用内购买机会
- 任何必需的披露

以下是编写说明的一些提示：

- 在前几句描述中清晰描述产品/服务的价值主张。 包括以下项目：
  - 您的优惠说明。
  - 受益于您的产品/服务的用户类型。
  - 客户需要或产品/服务解决的问题。
- 请记住，前几个句子可能显示在搜索引擎结果中。
- 不要依赖特性和功能来销售您的产品/服务。 相反，关注您的产品/服务提供的价值。
- 使用特定于行业或基于利益的词语。

为了使您的报价描述更具吸引力，请使用丰富的文本编辑器来设置您的说明格式。 富文本编辑器允许您添加数字、项目符号、粗体、斜体和缩进，以使描述更具可读性。

![使用"新产品/服务"按钮和咨询服务产品/服务产品，在合作伙伴中心中演示"概述"页面。](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>隐私政策链接

将网址 （URL） 输入到组织的隐私政策中。 确保您的优惠符合隐私法律和法规。 您还必须在您的网站上发布有效的隐私政策。

### <a name="useful-links"></a>有用链接

提供有关您的优惠的补充在线文档。 要添加链接，请选择 **" 添加链接"，** 然后完成以下字段：

- **名称**– 客户将在详细信息页面上看到该名称。
- **链接 （URL）** – 输入客户查看联机文档的链接。

### <a name="customer-support-links"></a>客户支持链接

提供支持网站，客户可以在该网站联系您的支持团队。

- Azure 全球支持网站
- Azure 政府支持网站

### <a name="partner-support-contact"></a>合作伙伴支持联系人

为 Microsoft 合作伙伴提供联系信息，供客户打开支持票证时使用。 这将不会在市场上列出。

- “属性”
- 电子邮件
- 电话

### <a name="engineering-contact"></a>工程联系人

在您的产品/服务出现问题（包括认证问题）时，为 Microsoft 提供要使用的联系信息。 这将不会在市场上列出。

- “属性”
- 电子邮件
- 电话

### <a name="marketplace-media"></a>市场媒体

提供徽标和图像，以便与您的产品配合使用。 所有图像必须采用 PNG 格式。 模糊的图像将导致您的提交被拒绝。

#### <a name="marketplace-logos"></a>市场徽标

以以下四个像素大小提供产品徽标的 PNG 文件：

- **小**（48 x 48）
- **中等**（90 x 90）
- **大**（216 x 216）
- **宽**（255 x 115）

所有四个徽标都是必需的，并且在市场列表中的不同位置使用。

#### <a name="screenshots"></a>屏幕截图

最多添加五个屏幕截图，显示您的优惠如何工作。 每个屏幕截图的大小和 PNG 格式必须为 1280 x 720 像素。 您还必须添加一个标题来描述您的屏幕截图。

#### <a name="videos"></a>视频

最多添加五个视频，演示您的优惠。 这些视频应托管在外部视频服务上。 输入每个视频的名称、网址和视频的缩略图 PNG 图像，大小为 1280 x 720 像素。

有关其他市场列表资源，请参阅[市场产品/服务列表的最佳做法](https://aka.ms/LdMgmtOfferListingBestPractices)。

选择 **"保存"草稿**，然后再继续。

## <a name="preview"></a>预览

在"预览"选项卡上，选择有限的**预览受众**以验证您的优惠，然后再将其实时发布到更广泛的市场受众。

> [!IMPORTANT]
> 在预览版中检查您的优惠后，选择 **"上线"** 以向商业市场公众受众发布您的优惠。

预览受众由 Azure 订阅 ID GUID 以及每个选项的可选说明标识。 客户看不到这两个字段。 您可以在 Azure 门户中的 **"订阅"** 页上找到 Azure 订阅 ID。

添加至少一个 Azure 订阅 ID，分别（最多 10 个）或通过上传 CSV 文件（最多 100 个）。 通过添加这些订阅 IT，您可以定义谁可以在产品/服务实时发布之前预览产品/服务。 如果您的优惠已是实时的，您仍然可以定义预览受众，以测试产品/服务的变化或更新。

> [!NOTE]
> 预览观众与私人观众不同。 预览受众可以在产品/服务在市场现场发布_之前_访问它。 他们可以查看和验证所有计划，包括仅在您的优惠完全发布到市场后才提供给私人受众的计划。 私人访问群体（在计划**定价和可用性**选项卡中定义）具有对特定计划的独占访问权限。

在继续下一节"计划概述"之前选择 **"保存草稿**"。

## <a name="plan-overview"></a>计划概述

您可以在合作伙伴中心的同一优惠中提供不同的计划选项。 这些计划以前称为 SKU。 产品/服务至少需要一个计划，在货币化受众、Azure 区域、功能或 VM 映像方面可能有所不同。

创建计划后，"**计划概览**"选项卡显示：

- 计划名称
- 许可证模型
- 受众（公共或私人）
- 当前发布状态
- 可用操作

计划概述中可用的操作因计划的当前状态而异。 其中包括：

- **删除草稿**= 如果计划状态为草稿
- **停止销售计划**或**同步私人受众**– 如果计划状态实时发布

### <a name="create-new-plan"></a>创建新计划

选择 **= 在顶部创建新计划**。 将显示"**新建计划**"对话框。

在 **"计划 ID"** 框中，为此产品/服务中的每个计划创建唯一的计划 ID。 此 ID 将在产品 Web 地址中对客户可见。 仅使用小写字母和数字、破折号或下划线，最多 50 个字符。

> [!NOTE]
> 选择 "**创建**"后，无法更改计划 ID。

在 **"计划名称**"框中，输入此计划的名称。 客户在决定在您的产品/服务中选择哪个计划时，会看到此名称。 创建一个唯一的名称，清楚地指出每个计划的差异。 例如，您可以将 Windows**服务器**用于计划即**用即付****、BYOL、****高级****和企业**。

选择“创建”  。

### <a name="plan-setup"></a>计划设置

设置计划类型的高级配置，它是否重用其他计划的技术配置，以及计划应在哪些区域可用。 此处的选择确定同一计划的其他选项卡上显示哪些字段。

#### <a name="reuse-technical-configuration"></a>重用技术配置

如果您有多个相同类型的计划，并且它们之间的包相同，则可以选择**此计划重用其他计划的技术配置**。 此选项允许您为此产品/服务选择相同类型的其他计划之一，并重复使用其技术配置。

> [!NOTE]
> 当您从另一个计划重用技术配置时，整个**技术配置**选项卡将从这个计划中消失。 其他计划中的技术配置详细信息（包括您将来进行的任何更新）也将用于此计划。 此计划发布后，无法更改此设置。

#### <a name="azure-regions"></a>Azure 区域

您的计划必须至少在一个 Azure 区域中可用。

选择**Azure 全局**选项，使计划可供具有商业市场集成的所有公共 Azure 区域的客户使用。 有关详细信息，请参阅[地理可用性和货币支持](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

选择**Azure 政府**选项以使计划在 Azure[政府](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)区域中可用。 此区域为美国联邦、州、地方或部落实体的客户以及有资格为其提供服务的合作伙伴提供受控访问。 作为发布者，您负责任何合规性控制、安全措施和最佳实践。 Azure 政府使用物理隔离的数据中心和网络（仅适用于美国）。

在发布到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，在环境中测试和验证计划，因为某些终结点可能有所不同。 要设置和测试计划，请从 Microsoft Azure[政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)请求试用帐户。

> [!NOTE]
> 计划发布并在特定 Azure 区域中可用后，无法删除该区域。

#### <a name="azure-government-certifications"></a>Azure 政府认证

仅当在**Azure 区域**下选择**Azure 政府**时，此选项才可见。

Azure 政府服务处理受某些政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。 为了提高您对这些计划的认证的认识，您最多可以提供 100 个描述它们的链接。 这些可以是直接指向您在程序上列表的链接，也可以是指向您自己网站上遵守这些列表的链接。 这些链接仅对 Azure 政府客户可见。

选择 **"保存"草稿**，然后再继续。

### <a name="plan-listing"></a>计划列表

这是配置计划列表详细信息的位置。 此选项卡显示同一产品/服务中计划之间可能有所不同的特定信息。

#### <a name="plan-name"></a>计划名称

这预先填写了您在创建计划时给出的名称。 此名称作为此计划的标题出现在市场中，限制为 100 个字符。

#### <a name="plan-summary"></a>计划摘要

提供您的计划（而不是报价）的简短摘要。 此摘要限制为 100 个字符。

#### <a name="plan-description"></a>计划描述

描述是什么让此软件计划独一无二，以及您产品/服务中计划之间的差异。 不要描述这个提议，只是计划。 计划描述最多可以包含 2，000 个字符。

选择 **"保存"草稿**，然后再继续。

### <a name="pricing-and-availability"></a>定价和可用性

在此选项卡上，您将配置以下内容：

- 此计划将在
- 每小时价格
- 是使计划对所有人可见，还是只对特定客户（私人受众） 可见

#### <a name="markets"></a>市场

每个计划必须至少在一个市场可用。 选择此计划可供购买的每个市场位置的复选框（这些市场中的用户仍然可以将产品/服务部署到**[计划设置](#plan-setup)** 中选择的所有 Azure 区域）。 **"税务汇入**"按钮显示 Microsoft 代表您汇入销售和使用税款的国家/地区。 发布到中国仅限于**免费**或**自带许可证**（BYOL） 的计划。

如果您已为计划设定价格（美元），并添加其他市场位置，则新市场的价格将根据当前汇率计算。 在出版之前，务必查看每个市场的价格。 保存更改后，使用 **"导出价格 （xlsx）"** 链接查看价格。

删除市场时，使用活动部署的客户将无法创建新部署或扩展其现有部署。 现有部署不会受到影响。

#### <a name="pricing"></a>定价

**许可证模型**– 选择**基于使用情况的每月计费计划**，以配置此计划的定价，或**自带许可证**，让客户使用此计划及其现有许可证。

对于基于使用情况的每月计费计划，请使用以下三种定价输入选项之一：

- **每个核心**– 以美元 （USD） 提供每个核心的价格。 我们将计算每个核心大小的定价，并使用当前汇率转换为当地货币。
- **每个核心尺寸**– 提供每个核心尺寸的价格（美元）。 我们将使用当前汇率将价格转换为当地货币。
- **每个市场和核心尺寸**– 为所有市场提供每个核心尺寸的价格。 您可以从电子表格导入价格。

> [!NOTE]
> 保存定价更改以启用定价数据导出。 在计划中市场的价格公布后，以后无法更改。 通过导出定价电子表格并查看每个市场的价格，确保这些价格在发布之前是正确的。

#### <a name="free-trial"></a>免费试用

您可以为客户提供一个月或三个月的免费试用。

#### <a name="visibility"></a>可见性

您可以将每个计划设计为对所有人可见，也可以仅对预选的受众可见。 使用 Azure 订阅标识分配此受限访问群体中的成员身份。

**公开**– 您的计划可以被所有人看到。

**私人受众**– 使您的计划仅对预选的受众可见。 在以私人计划身份发布后，您可以更新受众或将其更改为公共。 将计划公之于众后，必须保持公开;你不能把它改回私人。

**受限访问群体（Azure 订阅标识）** – 使用 Azure 订阅标识分配将有权访问此私有计划的受众。 或者，包括您分配的每个 Azure 订阅 ID 的说明。 如果导入 CSV 电子表格，则手动添加最多 10 个订阅 I 或 20，000 个订阅。 Azure 订阅标识表示为 GUID，字母必须小写。

> [!NOTE]
> 私人或受限受众不同于您在 **"预览"** 选项卡上定义的预览受众。预览受众可以在产品/服务发布_于_市场直播之前访问您的产品/服务。 虽然私人受众选择仅适用于特定计划，但预览受众可以出于验证目的查看所有计划（私有计划或计划）。

#### <a name="hide-plan"></a>隐藏计划

如果虚拟机仅打算通过其他解决方案模板或托管应用程序间接使用，请选择此框以发布虚拟机，但直接从客户搜索和浏览虚拟机时将其隐藏。

> [!NOTE]
> 隐藏计划不支持预览链接。

选择 **"保存"草稿**，然后再继续。

### <a name="technical-configuration"></a>技术配置

提供与此计划关联的图像和其他技术属性。 有关详细信息，请参阅[创建 Azure VM 技术资产](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 如果您使用此计划重用 **"计划设置**"选项卡上其他计划的包，则不显示此选项卡。

#### <a name="operating-system"></a>操作系统

- **操作系统系列**– 从**Windows**或**Linux**操作系统中选择
- **发布**或**供应商**- 选择 Windows 版本或 Linux 供应商
- **操作系统友好名称**– 选择友好的操作系统名称。 此名称对客户可见

#### <a name="recommended-vm-sizes"></a>建议的 VM 大小

选择最多六个建议的虚拟机大小，以显示在 Azure 应用商店中。

#### <a name="open-ports"></a>打开端口

在已部署的虚拟机上打开公共或专用端口。

#### <a name="storage-option-for-deployment"></a>部署的存储选项

**磁盘部署选项**- 选择用户在使用虚拟机时可以使用的磁盘部署类型。 Microsoft 建议将部署仅限制为托管磁盘部署。

#### <a name="properties"></a>属性

**支持加速网络**– 选择 VM 是否支持[加速网络](https://go.microsoft.com/fwlink/?linkid=2124513)。

#### <a name="vm-images"></a>VM 映像

为虚拟机映像提供磁盘版本和 SAS URI。 为每个 VM 映像添加最多 16 个数据磁盘。 在给定提交中，每个计划仅提供一个新映像版本。 发布图像后，无法对其进行编辑，但可以将其删除。 删除版本将阻止新用户和现有用户部署已删除版本的新实例。

- **光盘版本**是您提供的图像的版本。
- **SAS URI**是 Azure 存储中存储操作系统 VHD 的位置。
- 数据磁盘映像也是存储在其 Azure 存储中的 VHD SAS URI。
- 在计划中每个提交只添加一个图像。

无论您使用哪种操作系统，都只添加解决方案所需的最小数据磁盘数。 客户无法在部署时删除属于映像的磁盘，但始终可以在部署期间或部署后添加磁盘。

选择 **"保存"草稿**，然后再继续并返回到**计划概述**。

## <a name="resell-through-csps"></a>通过 CSP 转售

通过向[云解决方案提供商](https://azure.microsoft.com/offers/ms-azr-0145p/)（CSP） 计划中的合作伙伴提供产品/服务，从而扩大产品/服务范围。 所有自带许可证 （BYOL） 计划均自动选择;您可以选择加入您的非 BYOL 计划。

选择 **"保存"草稿**，然后再继续。

## <a name="test-drive"></a>体验版

设置演示（测试驱动器），让客户在购买之前试用您的产品/服务。 要创建一个演示环境，让客户在固定的时间内试用您的产品/服务，请参阅[在商业市场试用您的产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

要启用试驾，请在"[产品/服务设置"](#test-drive)选项卡上选中"启用试驾"复选框。要从产品/服务中删除试驾，请清除此复选框。

其他试驾资源：

- 技术最佳实践
- 市场营销最佳做法
- 测试驱动器概述（PDF;请确保您的弹出窗口阻止程序已关闭）。

选择 **"保存"草稿**，然后再继续。

## <a name="review-and-publish"></a>审核和发布

完成产品/服务所需的所有部分后，可以将其提交以供审核和发布。

在门户的右上角，选择 **"审阅"并发布**。

在此页面上，您可以：

- 请参阅产品/服务的每个部分的完成状态。
  - **未启动**- 部分尚未启动，需要完成。
  - **不完整**- 该部分有必须修复或要求您提供详细信息的错误。 有关如何更新本节的指导，请参阅本文档前面的部分。
  - **完成**– 部分已完成，没有错误。 优惠的所有部分必须完成，然后才能提交报价。
- **认证注意事项**– 向认证团队提供测试说明，以确保应用测试正确。 提供有助于了解应用的任何补充说明。

要提交发布要约，请选择 **"审阅"并发布**。

我们将向您发送一封电子邮件，告知您产品/服务预览版本何时可供审核和批准。 要向公众发布您的优惠（或者如果私人优惠，则向私人受众），请转到合作伙伴中心，找到优惠的 **"概述"** 页面，然后选择 **"上线**"。 发布时，您的产品/服务状态将显示在页面顶部。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的**手动验证**步骤是对您的产品/服务及其相关技术资产的广泛审查。 如果此过程发现您的产品/服务出现错误，您将收到一份详细说明问题的认证报告。 只需进行必要的更正并重新发布您的报价。

## <a name="offer-overview"></a>产品/服务概述

**"产品/服务概述"** 页显示发布此产品/服务所需的步骤（已完成和正在进行的）以及每个步骤应完成多长时间的可视化表示形式。

此页包含基于您做出的选择对此产品/服务执行操作的链接。 例如：

- 如果要约是草稿 -[删除草稿要约](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 如果优惠是实时的 -[停止销售优惠](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果优惠处于预览版 -[上线](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 如果您尚未完成发布者签核 -[取消发布](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>市场示例

这些示例显示了产品/服务在 Azure 应用商店中的显示方式。

### <a name="azure-marketplace-offer-details"></a>Azure 应用商店提供详细信息

![Azure 应用商店提供详细信息屏幕示例](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure 应用商店搜索结果

![Azure 应用商店搜索详细信息屏幕示例](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure 应用商店计划详细信息

![Azure 应用商店计划详细信息屏幕示例](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure 门户提供详细信息

![Azure 门户提供详细信息屏幕示例](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure 门户搜索结果

![Azure 门户搜索结果屏幕示例](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure 门户计划详细信息

![Azure 门户计划详细信息屏幕示例](media/avm-create6.png)

## <a name="next-step"></a>后续步骤

- [更新商业市场中的现有产品/服务](https://aka.ms/UpdateOfferCM)
