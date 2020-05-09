---
title: 在 Azure Marketplace 中创建 Azure 虚拟机产品/服务
description: 了解如何使用所需 SKU 在 Azure Marketplace 中创建虚拟机产品/服务。
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 31b8960f5617566a72545510cf03771f7a3bfcbd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82745011"
---
# <a name="create-an-azure-virtual-machine-offer-in-the-azure-marketplace"></a>在 Azure Marketplace 中创建 Azure 虚拟机产品/服务

> [!IMPORTANT]
> 我们正在将 Azure VM 产品/服务的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移你的产品/服务之前，请继续按照云合作伙伴门户中 "[创建虚拟机产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer)" 中的说明来管理你的产品/服务。

本文介绍如何创建 Azure 虚拟机产品/服务并将其发布到[Azure Marketplace](https://azuremarketplace.microsoft.com/)。 它介绍了基于 Windows 和 Linux 的虚拟机，这些虚拟机包含一个操作系统、一个虚拟硬盘（VHD）和多达16个数据磁盘。 在开始之前，请[在合作伙伴中心创建一个商用 Marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果尚未这样做）。 确保你的帐户已在 "商用 marketplace 计划" 中注册。

## <a name="introduction"></a>简介

### <a name="publishing-benefits"></a>发布的好处

发布到 Azure Marketplace 具有以下优势：

- 使用 Microsoft 品牌推广公司
- 通过 Azure Marketplace 跨100000000个 Office 365 和 Dynamics 365 用户和超过200000个组织
- 从这些市场获取高质量的潜在顾客
- 获取由 Microsoft 现场和电话销售团队升级的服务

### <a name="before-you-begin"></a>开始之前

如果尚未这样做，请查看[虚拟机产品/服务发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)和此 Azure 虚拟机材料：

- 快速入门指南
  - [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 快速入门模板](https://github.com/azure/azure-quickstart-templates)
- 教程
  - [Linux Vm](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Windows Vm](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- 示例
  - [适用于 Linux Vm 的 Azure CLI 示例](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [适用于 Linux Vm 的 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [适用于 Windows Vm 的 Azure CLI 示例](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [适用于 Windows Vm 的 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>技术知识中的基础知识

设计、构建和测试这些资产需要一定的时间，并需要 Azure 平台和用于构建产品/服务的技术的技术知识。

工程团队应了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的实践知识

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧导航菜单中，选择 "**商业市场** > **概述**"。
3. 在 "概述" 页上，选择 " **+ 新建产品/服务** > " "**Azure 虚拟机**"。

    ![阐释左侧导航菜单。](./media/new-offer-azure-vm.png)

> [!NOTE]
> 发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑仅在重新发布产品/服务后出现在商店中。 请确保在进行更改后始终重新发布。

## <a name="new-offer"></a>新产品/服务

输入**产品/服务 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

- 在 marketplace 产品/服务的网络地址中，以及在 Azure PowerShell 和 Azure CLI （如果适用）中，客户可看到此 ID。
- 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果在此处输入了 "**测试/服务-1** "，则 "产品/ `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`服务" 网址为。
- 选择 "**创建**" 后，无法更改产品/服务 ID。

输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

- 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。

选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

### <a name="test-drive"></a>体验版

通过向客户提供 "在购买前试用" 的选项，测试驱动器是向潜在客户展示你的产品/服务的一种很好的方法，导致增加转换和产生高度合格的潜在顾客。 [了解有关测试驱动器的详细信息](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用测试驱动器，请选中 "**启用测试驱动器**" 复选框。 若要从产品/服务中删除测试驱动器，请清除此复选框。

其他测试驱动器资源：

- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [测试驱动器概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)PDF （确保弹出窗口阻止程序处于关闭状态）

### <a name="lead-management"></a>潜在顾客管理

向合作伙伴中心发布产品/服务时，请将其连接到客户关系管理（CRM）系统。 这使你可以在有人向你的产品表达兴趣或使用你的产品时立即收到客户联系信息。 如果你将启用 "**测试驱动器**" （请参阅前面的部分），则需要连接到 CRM，否则为可选。

1. 选择希望我们向其发送潜在客户的潜在客户目标。 合作伙伴中心支持以下 CRM 系统：
    - 适用于客户参与的[Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > 如果上面未列出你的 CRM 系统，请使用[Azure 表](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table)或[Https 终结点](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https)来存储客户的潜在客户数据。 然后将数据导出到 CRM 系统。

2. 在合作伙伴中心发布产品/服务后，将产品/服务连接到潜在客户。
3. 确认正确配置了与潜在客户目标的连接。 在合作伙伴中心发布后，我们将验证连接并向你发送测试线索。 当你在推出产品/服务前预览产品/服务时，你还可以通过尝试在预览环境中自行部署产品/服务来测试你的潜在客户连接。
4. 确保与潜在客户目标的连接保持更新，以便不会丢失任何潜在顾客。

选择 "**保存草稿**"，然后继续。

## <a name="properties"></a>属性

此页面允许你定义用于在 marketplace 上对产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

### <a name="categories"></a>类别

选择最小值，最多五个类别。 这些类别用于将产品/服务置于适当的 marketplace 搜索区域。 在产品/服务说明中，说明产品/服务对这些类别的支持。 虚拟机产品/服务显示在 Azure Marketplace 中的 "**计算**" 类别下。

### <a name="legal"></a>合法

必须提供产品/服务的条款和条件。 可以使用两个选项：

- 使用你自己的条款和条件
- 使用 Microsoft 商业应用商店的标准协定

#### <a name="use-your-own-terms-and-conditions"></a>使用你自己的条款和条件

若要提供自己的自定义条款和条件，请在 "**条款和条件**" 框中输入最多10000个字符的文本。 如果需要较长的说明，请输入指向你的条款和条件所在位置的单个 web 地址。 它将向客户显示为活动链接。

客户必须接受这些条款，然后才能试用你的产品/服务。

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>使用 Microsoft 商业应用商店的标准协定

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 为商业市场提供标准合同。 当你在标准合同下提供软件时，客户只需阅读并接受一次，你就不必创建自定义条款和条件。

通过选中 "**使用 Microsoft 商业应用的标准合同**" 复选框，然后在弹出窗口中**接受**（可能需要向上滚动才能看到），使用标准协定。

![说明合作伙伴中心中的 "概述" 页，其中选择了 "新产品/服务" 按钮和 "咨询服务" 产品。](media/use-standard-contract.png)

> [!NOTE]
> 使用商业应用商店的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 根据标准合同**或**您自己的条款和条件提供您的解决方案。

若要了解有关标准协定的详细信息，请参阅 Microsoft[商用 marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract)的标准约定。 可以将[标准合同](https://go.microsoft.com/fwlink/?linkid=2041178)下载为 PDF （确保弹出窗口阻止程序处于关闭状态）。

##### <a name="standard-contract-amendments"></a>标准协定改正

标准协定修正案使你可以为简单起见选择标准协定术语，并为你的产品或企业创建条款。 如果客户已查看并接受 Microsoft 标准合同，则他们只需查看合同的修正案条款。 可使用两种类型的修正案：通用和自定义。

**通用修正**–它们广泛应用于所有客户的标准协定。 它们显示在购买流程中的产品/服务的每个客户上。 客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。 可以为每个产品/服务提供单个通用修订。 您可以在此框中输入不限数量的字符。 在发现和购买流程期间，会向 AppSource、Azure Marketplace 和/或 Azure 门户中的客户显示这些条款。

**自定义修正**–这些是通过 Azure 租户 id 面向特定客户的标准协定的特殊修正。 你可以选择要作为目标的租户。 只有租户中的客户会看到产品/服务购买流程中的自定义修订条款。 客户必须接受标准合同条款和修正条款，然后才能使用产品/服务。

首先选择 "**添加自定义修订条款（最多10个）**"。 最多可为每个产品提供10个自定义修订条款。

- **自定义修订条款**–在自定义修订条款框中输入你自己的修订条款。 您可以输入不限数量的字符。 只有你为这些自定义术语指定的租户 Id 的客户才能在产品/服务的 Azure 门户中看到这些内容。
- **租户 id** （必需）–每个自定义修正最多可针对20个租户 id。 如果添加自定义修正，则必须至少提供一个租户 ID，以便在 Azure 中标识您的客户。 你的客户可以在 Azure 中的下找到，然后在 "属性" 中找到。 目录 ID 值是租户 ID （例如，50c464d3-4930-494c-963c-1e951d15360e）。 你还可以在 "[我的 Microsoft Azure 和 Office 365 租户 id 是什么？](https://www.whatismytenantid.com/)" 中使用其域名 web 地址查找你的客户的组织的租户 id。
- **描述**（可选）–提供租户 ID 的友好说明，可帮助您识别修正的目标客户。

> [!NOTE]
> 这两种类型的改正堆栈彼此之上。 以自定义修正为目标的客户也会在购买过程中获得标准合同的通用修正。

选择 "**保存草稿**"，然后继续。

## <a name="offer-listing"></a>产品/服务列表

此页可用于定义产品/服务的详细信息，例如产品名称、说明、链接和联系人。

> [!NOTE]
> 产品/服务列表内容（如描述、文档、屏幕截图和使用条款）不要求使用英语，只要产品/服务说明以短语开头，"此应用程序仅在 [非英语语言] 中可用"。 你还可以提供_有用的链接 web 地址_，以使用产品/服务列表内容中所用的语言以外的其他语言提供内容。

### <a name="marketplace-details"></a>Marketplace 详细信息

#### <a name="name"></a>名称

你在此处输入的名称将作为你的产品/服务列表的标题向客户显示。 此字段预先填充了你在创建产品/服务时在 "**产品/服务**" 框中输入的文本。 可以稍后更改此名称。

名称：

- 可以是商标字的（可以包括商标和版权符号）
- 长度不能超过50个字符
- 不能包含表情符号。

#### <a name="search-results-summary"></a>搜索结果摘要

产品/服务的简短说明。 此值最多可包含100个字符，并在 marketplace 搜索结果中使用。

#### <a name="long-summary"></a>长摘要

提供产品/服务的更长说明。 此值最多可包含256个字符，并在 marketplace 搜索结果中使用。

#### <a name="description"></a>说明

提供产品/服务的详细说明，最多3000个字符。 这会显示给商业市场列表概述中的客户。

在说明中包括以下一项或多项：

- 产品/服务的价值和主要优势
- 类别或行业关联
- 应用内购买机会
- 任何所需的公开

下面是有关编写说明的一些提示：

- 在前几句描述中清晰描述产品/服务的价值主张。 包括以下项：
  - 产品/服务的说明。
  - 受益于你的产品/服务的用户类型。
  - 产品/服务的客户需要或问题。
- 请记住，前几个句子可能会显示在搜索引擎结果中。
- 不要依赖特性和功能来出售产品/服务。 而是专注于你的产品/服务提供的价值。
- 使用行业特定的或基于收益的字词。

若要使您的产品/服务说明更具吸引力，请使用富文本编辑器应用格式设置。

![使用富文本编辑器](./media/rich-text-editor.png)

| <center>更改文本格式 | <center>添加项目符号或编号 | <center>添加或删除文本缩进 |
| --- | --- | --- |
| <center>![使用富文本编辑器更改文本格式](./media/text-editor3.png) |  <center>![使用富文本编辑器添加列表](./media/text-editor4.png) |  <center>![使用富文本编辑器缩进](./media/text-editor5.png) |

#### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 web 地址（URL）。 确保你的产品/服务符合隐私法律和法规。 你还必须在网站上发布有效的隐私策略。

### <a name="useful-links"></a>有用链接

提供有关你的产品/服务的补充联机文档。 若要添加链接，请选择 " **+ 添加链接**"，然后完成以下字段：

- **名称**-客户会在详细信息页上看到该名称。
- **链接（URL）** –输入客户的链接以查看你的联机文档。

### <a name="customer-support-links"></a>客户支持链接

提供支持网站，客户可以在其中联系支持团队。

- Azure 全球支持网站
- Azure 政府版支持网站

### <a name="partner-support-contact"></a>合作伙伴支持联系人

提供客户在客户打开支持票证时要使用的 Microsoft 合作伙伴联系信息。 这不会在 marketplace 中列出。

- 名称
- Email
- 电话

### <a name="engineering-contact"></a>工程联系人

向 Microsoft 提供联系信息，以便在你的产品/服务出现问题时使用，包括认证问题。 这不会在 marketplace 中列出。

- 名称
- Email
- 电话

### <a name="marketplace-media"></a>Marketplace 媒体

提供要用于产品/服务的徽标和图像。 所有图像必须为 PNG 格式。 模糊图像将导致提交遭到拒绝。

>[!Note]
>如果上传文件时遇到问题，请确保你的本地网络不会https://upload.xboxlive.com阻止合作伙伴中心使用的服务。

#### <a name="marketplace-logos"></a>Marketplace 徽标

提供产品/服务徽标的 PNG 文件，大小为以下四个像素：

- **小型**（48 x 48）
- **中**（90 x 90）
- **大**（216 x 216）
- **宽**（255 x 115）

所有这四种徽标都是必需的，并用于 marketplace 列表中的不同位置。

#### <a name="screenshots"></a>屏幕截图

最多可添加五个屏幕截图，其中显示了产品/服务的工作方式。 每个屏幕截图的大小必须为 1280 x 720 像素，格式为 PNG。 每个屏幕截图都必须包含一个标题。

#### <a name="videos"></a>视频

添加最多5个视频来演示产品/服务。 这些应托管在外部视频服务上。 输入每个视频的名称、web 地址以及以 1280 x 720 像素为单位的视频缩略图 PNG 图像。

有关更多应用商店列出资源，请参阅[marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)。

选择 "**保存草稿**"，然后继续。

## <a name="preview"></a>预览

在将产品/服务发布到更广泛的 marketplace 受众之前，在 "预览" 选项卡上，选择用于验证产品/服务的受限**预览版观众**。

> [!IMPORTANT]
> 在预览版中检查产品/服务后，**请选择 "上线"** 将产品/服务发布到商业市场公共受众。

预览受众由 Azure 订阅 ID Guid 标识，以及每个用户的可选说明。 这些字段均不能由客户查看。 你可以在 "**订阅**" 页上的 Azure 门户中找到你的 AZURE 订阅 ID。

至少添加一个 Azure 订阅 ID （最多10个）或上传一个 CSV 文件（最大为100）。 通过添加这些订阅 Id，你可以定义在 live 发布之前可以预览你的产品/服务的人员。 如果你的产品/服务已处于活动阶段，你仍可以定义一个预览版观众来测试产品/服务的更改或更新。

> [!NOTE]
> 预览受众不同于专用用户。 预览观众可以在将其发布到市场_之前_，访问你的产品/服务。 他们可以查看和验证所有计划，包括那些在将产品/服务完全发布到 marketplace 后仅对专用用户可用的计划。 专用用户（在 "计划**定价和可用性**" 选项卡中定义）具有特定计划的独占访问权限。

在转到下一节 "计划概述" 之前，请选择 "**保存草稿**"。

## <a name="plan-overview"></a>计划概述

您可以在合作伙伴中心提供不同的计划选项。 这些计划以前称为 Sku。 产品/服务需要至少一个计划，该计划可能不同于盈利受众、Azure 区域、功能或 VM 映像。

创建计划后，"**计划概述**" 选项卡将显示：

- 计划名称
- 许可证模型
- 受众（公共或私有）
- 当前发布状态
- 可用操作

计划概述中的可用操作取决于计划的当前状态。 其中包括：

- **删除草稿**–如果计划状态为草稿
- **停止销售计划**或**同步专用受众**–如果计划状态为 "实时"

### <a name="create-new-plan"></a>创建新计划

选择顶部的 " **+ 创建新计划**"。 此时将显示 "**新建计划**" 对话框。

在 "**计划 id** " 框中，为此产品/服务中的每个计划创建唯一的计划 id。 此 ID 将对产品 web 地址中的客户可见。 仅使用小写字母和数字、短划线或下划线，最多使用50个字符。

> [!NOTE]
> 选择 "**创建**" 后，不能更改计划 ID。

在 "**计划名称**" 框中，输入此计划的名称。 客户在确定要在产品中选择的计划时，将看到此名称。 创建明确指出每个计划差异的唯一名称。 例如，你可以将**Windows Server**与 "即用即**付**"、" **BYOL**"、"**高级**" 和 "**企业**" 结合使用。

选择“创建”。 

### <a name="plan-setup"></a>计划设置

设置计划类型的高级配置，它是否重复使用其他计划中的技术配置，以及计划应在哪个 Azure 区域中可用。 此处所做的选择将确定在同一计划的其他选项卡上显示的字段。

#### <a name="re-use-technical-configuration"></a>重复使用技术配置

如果有多个相同类型的计划，并且这些包在它们之间完全相同，则可以选择此计划，重新使用**其他计划中的技术配置**。 使用此选项，你可以为此服务选择同一类型的其他计划之一，并重复使用其技术配置。

> [!NOTE]
> 当你重复使用其他计划中的技术配置时，将从此计划中消失整个**技术配置**选项卡。 其他计划中的技术配置详细信息（包括将来所做的任何更新）也将用于此计划。 此计划发布后无法更改此设置。

#### <a name="azure-regions"></a>Azure 区域

你的计划必须至少在一个 Azure 区域中提供。

选择**Azure 全局**选项，使你的计划对所有具有商业市场集成的 Azure 全局区域中的客户可用。 有关详细信息，请参阅[地理可用性和货币支持](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

选择**Azure 政府**版选项，使你的计划在[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)版区域中可用。 此区域为美国联邦、州、地方或部落实体的客户以及有资格提供服务的合作伙伴提供控制访问权限。 作为发布者，你将负责任何符合性控制、安全措施和最佳实践。 Azure 政府版使用物理上隔离的数据中心和网络（仅限美国）。

在发布到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)版之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从[Microsoft Azure 政府试用](https://azure.microsoft.com/global-infrastructure/government/request/)版请求试用帐户。

> [!NOTE]
> 计划发布并在特定 Azure 区域中可用后，将无法删除该区域。

#### <a name="azure-government-certifications"></a>Azure 政府认证

仅当选择了 " **Azure 政府**" 时，此选项才可见。

Azure 政府服务处理受特定政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。 若要对这些程序进行了解，可以提供多达100的链接。 这些链接可以是直接链接到您的程序上的列表，也可以是您自己的网站上的符合性说明。 这些链接仅对 Azure 政府客户可见。

选择 "**保存草稿**"，然后继续。

### <a name="plan-listing"></a>计划列表

你可以在此处配置列出计划的详细信息。 此选项卡显示同一个产品/服务中的计划可能有所不同的特定信息。

#### <a name="plan-name"></a>计划名称

该名称预先填充了你在创建计划时为其提供的名称。 此名称在 marketplace 中显示为此计划的标题，并限制为100个字符。

#### <a name="plan-summary"></a>计划摘要

提供计划的简短摘要（而不是产品/服务）。 此摘要限制为100个字符。

#### <a name="plan-description"></a>计划描述

介绍如何使此软件计划独一无二，并提供产品内计划之间的差异。 不要描述该产品/服务，而只是计划。 计划说明最多可包含2000个字符。

选择 "**保存草稿**"，然后继续。

### <a name="pricing-and-availability"></a>定价和可用性

在此选项卡上，你将配置以下内容：

- 这项计划将在
- 每小时价格
- 是使计划对每个人都可见，还是仅对特定客户可见（专用受众）

#### <a name="markets"></a>市场

每个计划都必须至少在一个市场中可用。 对于应可购买此计划的每个市场位置，选中该复选框（这些市场中的用户仍可将产品/服务部署到**[计划设置](#plan-setup)** 中选择的所有 Azure 区域）。 **税汇款**按钮显示了 Microsoft 汇寄销售和使用的国家/地区。 发布到中国的计划仅限**免费**计划或**自带许可**（BYOL）。

如果已将计划的价格设置为美国美元（USD）并添加另一个市场位置，则将根据当前汇率计算新市场的价格。 请始终在发布前查看每个市场的价格。 保存更改后，使用 "**导出价格（.xlsx）** " 链接查看价格。

当你删除市场时，使用活动部署的市场客户将无法创建新部署或扩展其现有部署。 现有部署将不会受到影响。

#### <a name="pricing"></a>定价

**许可证模型**–选择 "**基于使用情况的按月计费计划**" 可配置此计划的定价，或**自带许可证**，让客户将此计划用于现有许可证。

对于基于使用情况的月度计费计划，请使用以下三个定价条目选项之一：

- **每个核心**–以美国美元（USD）为单位提供每个核心的价格。 我们将计算每个核心大小的定价，并使用当前汇率转换为本地货币。
- **每个核心大小**–提供每个核心大小的价格（美元）。 我们使用当前汇率将价格转换为本地货币。
- **每个市场和核心大小**–为所有市场提供每个核心大小的价格。 您可以从电子表格中导入价格。

> [!NOTE]
> 保存定价更改以启用定价数据的导出。 发布计划中市场的价格后，以后无法更改它。 在发布之前，请先通过导出定价电子表格并查看每个市场的价格，确保这些价格是正确的。

#### <a name="free-trial"></a>免费试用

你可以向客户提供一个月或三个月的免费试用版。

#### <a name="visibility"></a>可见性

您可以设计每个计划，使其对每个人可见，或仅对预选用户可见。 使用 Azure 订阅 Id 向此受限受众分配成员身份。

**公开**–所有人都可以查看你的计划。

**专用受众**–使你的计划仅对预选用户可见。 将其发布为私有计划后，你可以更新受众或将其更改为 "公共"。 公开计划后，该计划必须保持公共;不能将其更改回私有。

**受限受众（azure 订阅 id）** –使用 Azure 订阅 id 分配将有权访问此私有计划的受众。 （可选）包含分配的每个 Azure 订阅 ID 的说明。 如果导入 CSV 电子表格，请手动添加最多10个订阅 Id 或20000。 Azure 订阅 Id 表示为 Guid，字母必须为小写。

> [!NOTE]
> 专用或受限制的受众不同于你在 "**预览**" 选项卡上定义的预览观众。预览版观众在 marketplace 发布_之前_，可以访问产品/服务。 尽管专用受众选项仅适用于特定计划，但预览受众可以查看所有计划（private）以用于验证目的。

#### <a name="hide-plan"></a>隐藏计划

如果你的虚拟机仅在通过另一个解决方案模板或托管应用程序引用时才间接使用，则选择此框以发布你的虚拟机，但会直接从客户搜索和浏览它。

> [!NOTE]
> 隐藏的计划不支持预览链接。

选择 "**保存草稿**"，然后继续。

### <a name="technical-configuration"></a>技术配置

提供与此计划关联的映像和其他技术属性。 有关详细信息，请参阅[创建 AZURE VM 技术资产](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)。

> [!NOTE]
> 如果已将此计划配置为在 "**计划设置**" 选项卡上重复使用其他计划中的包，则不会显示此选项卡。

#### <a name="operating-system"></a>操作系统

- **操作系统系列**–从**Windows**或**Linux**操作系统中选择
- **版本**或**供应商**–选择 Windows 版本或 Linux 供应商
- **OS 友好名称**–选择一个友好的操作系统名称。 此名称对客户可见

#### <a name="recommended-vm-sizes"></a>建议的 VM 大小

选择最多六个建议的虚拟机大小，以显示在 Azure Marketplace 中。

#### <a name="open-ports"></a>打开端口

在部署的虚拟机上打开公共或专用端口。

#### <a name="storage-option-for-deployment"></a>部署的存储选项

**磁盘部署选项**-选择用户在使用虚拟机时可以使用的磁盘部署类型。 Microsoft 建议仅将部署限制为托管磁盘部署。

#### <a name="properties"></a>属性

**支持加速网络**–选择 VM 是否支持[加速网络](https://go.microsoft.com/fwlink/?linkid=2124513)。

#### <a name="vm-images"></a>VM 映像

为虚拟机映像提供磁盘版本和 SAS URI。 为每个 VM 映像添加最多16个数据磁盘。 在给定提交中，每个计划仅提供一个新的映像版本。 映像发布后，无法对其进行编辑，但可以将其删除。 删除版本将阻止新用户和现有用户部署已删除版本的新实例。

- **光盘版本**是所提供的映像的版本。
- **SAS URI**是 Azure 存储中存储操作系统 VHD 的位置。
- 数据磁盘映像也是存储在其 Azure 存储中的 VHD SAS Uri。
- 在计划中，每次提交只添加一个映像。

不管使用哪种操作系统，只需添加解决方案所需的最小数据磁盘数。 客户无法在部署时删除作为映像一部分的磁盘，但它们始终可以在部署期间或之后添加磁盘。

选择 "**保存草稿**"，然后继续并返回到**计划概述**。

## <a name="resell-through-csps"></a>通过 Csp 转售

通过向[云解决方案提供商](https://azure.microsoft.com/offers/ms-azr-0145p/)（CSP）计划中的合作伙伴提供产品/服务来扩展产品/服务的覆盖范围。 将自动选择 "自带许可证" （BYOL）计划;可以选择选择不 BYOL 的计划。

选择 "**保存草稿**"，然后继续。

## <a name="test-drive"></a>体验版

设置演示（测试驱动器），让客户在购买前试用产品/服务。 若要创建允许客户在固定时间段内试用你的产品/服务的演示环境，请参阅[在商业应用商店中试用你的产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

若要启用某个测试驱动器，请选中 "[产品/服务设置](#test-drive)" 选项卡上的 "启用测试驱动器" 复选框。若要从产品/服务中删除测试驱动器，请清除此复选框。

其他测试驱动器资源：

- [市场营销最佳做法](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [技术最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（PDF; 请确保弹出窗口阻止程序处于关闭状态）

选择 "**保存草稿**"，然后继续。

## <a name="review-and-publish"></a>查看和发布

完成该产品/服务的所有必需部分后，可以提交它来查看和发布。

在门户的右上角，选择 "**查看并发布**"。

在此页上，您可以：

- 请参阅产品/服务的每个部分的完成状态。
  - **未启动**–此部分尚未启动，需要完成。
  - **不完整**–此部分包含错误，必须修复这些错误，或者要求你提供详细信息。 请参阅本文档前面的部分，了解有关如何更新此部分的指南。
  - **Complete** –此部分已完成，并且没有任何错误。 必须先完成产品/服务的所有部分，然后才能提交产品/服务。
- **认证说明**–为认证团队提供测试说明，确保正确测试您的应用程序。 提供任何有助于了解应用的补充说明。

若要提交产品/服务进行发布，请选择 "**查看并发布**"。

我们将向你发送一封电子邮件，告知你该产品/服务的预览版本可供查看和批准。 若要将产品/服务发布到公共产品/服务（如果是专用用户），请访问合作伙伴中心，找到产品/服务的 "**概述**" 页，然后选择 "**上线**"。 当发布正在进行时，产品/服务的状态将显示在页面顶部。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的**手动验证**步骤代表了对您的产品/服务及其关联技术资产的广泛回顾。 如果此过程发现你的产品/服务出现错误，你将收到详细说明问题的认证报告。 只需进行所需的更正并重新发布产品。

## <a name="offer-overview"></a>产品/服务概述

"**产品概述**" 页显示了发布此产品/服务所需步骤的直观表示形式（已完成和正在进行）以及每个步骤的完成时间。

此页包含基于你所做的选择对此服务执行操作的链接。 例如：

- 如果产品/服务是草稿版/草稿版[产品](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)/服务
- 如果产品/服务是实时的，则[停止销售产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 如果[产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)为预览版-上线
- 如果尚未完成发布服务器注销-[取消发布](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace 示例

这些示例显示了产品/服务在 Azure Marketplace 中的显示方式。

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace 产品/服务详细信息

![Azure Marketplace 产品/服务详细信息屏幕示例](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure Marketplace 搜索结果

![Azure Marketplace 搜索详细信息屏幕示例](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure Marketplace 计划详细信息

![Azure Marketplace 计划详细信息屏幕示例](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure 门户产品/服务详细信息

![Azure 门户产品/服务详细信息屏幕示例](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure 门户搜索结果

![Azure 门户搜索结果屏幕示例](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure 门户计划详细信息

![Azure 门户计划详细信息屏幕示例](media/avm-create6.png)

## <a name="next-step"></a>后续步骤

- [更新商业应用商店中的现有产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
