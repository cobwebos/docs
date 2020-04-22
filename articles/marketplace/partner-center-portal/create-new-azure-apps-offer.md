---
title: 在商业市场创建新的 Azure 应用产品/服务
description: 如何使用 Microsoft 合作伙伴中心的商业应用商店门户创建新的 Azure 应用产品/服务，用于在 Azure 应用商店、AppSource 或通过云解决方案提供商 （CSP） 计划进行上市或销售。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: a62551b31c51836dfa50534ee6db907c8a4e111a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730615"
---
# <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

此处概述了在商业市场中发布 Azure 应用程序产品/服务的步骤。

## <a name="azure-application-offer-type"></a>Azure 应用程序产品/服务类型

本主题概述了有关 Azure 应用程序提供的基础知识。  在开始在应用商店中发布新的 Azure 应用程序产品/服务之前，应熟悉这些概念。

### <a name="publishing-overview"></a>发布概述

Azure[应用商店的视频构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)是 Azure 应用程序产品类型简介：

* 可用的产品/服务类型;
* 需要哪些技术资产;
* 如何编写 Azure 资源管理器模板;
* 开发和测试应用程序 UI;
* 如何发布应用产品/服务;
* 应用程序审阅过程。

### <a name="types-of-azure-application-plans"></a>Azure 应用程序计划的类型

有两种类型的 Azure 应用程序计划，托管应用程序和解决方案模板。

* **解决方案模板**是在应用商店中发布解决方案的主要方法之一。 当解决方案需要单个虚拟机 （VM） 以外的其他部署和配置自动化时，使用此计划类型。  借助解决方案模板，您可以自动提供多个资源，包括 VM、网络和存储资源，以提供复杂的 IaaS 解决方案。  有关构建解决方案模板的详细信息，请参阅[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)文档。

* **托管应用程序**类似于解决方案模板，有一个关键区别。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。 使用托管应用程序轻松构建和交付完全托管的交钥匙应用程序给您的客户。  有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](https://docs.microsoft.com/azure/managed-applications/overview)。

所有 Azure 应用程序在存档的根文件夹中至少包含两个`.zip`文件：

* 名为[mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的资源管理器模板文件。  这是定义要部署到客户的 Azure 订阅中的资源的模板。  有关资源管理器模板的示例，请参阅[Azure 快速入门模板库](https://azure.microsoft.com/resources/templates/)或相应的[GitHub：Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。

* 名为[createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)的 Azure 应用程序创建体验的用户界面定义。  在用户界面中，可以指定让使用者能够提供参数值的元素。

所有新的 Azure 应用程序产品/服务都必须包括[Azure 合作伙伴客户使用情况归因 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)。

### <a name="before-you-begin"></a>开始之前

查看以下 Azure 应用程序文档，其中提供了快速入门、教程和示例。

* [了解 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* 快速入门：

    * [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 快速入门模板](https://github.com/azure/azure-quickstart-templates)
    * [发布应用程序定义](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [部署服务目录应用](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 教程：

    * [创建定义文件](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [发布市场应用程序](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 示例：

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [托管应用程序解决方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>技术知识基础知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。

工程团队应该了解以下 Microsoft 技术：

* 对[Azure 服务](https://azure.microsoft.com/services/)的基本了解。
* 如何[设计和构建 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)。
* [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)[、Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 Azure[网络](https://azure.microsoft.com/services/?filter=networking#networking)的工作知识。
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的工作知识。
* [JSON](https://www.json.org/)的工作知识 。

### <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理 Azure 应用程序：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

我们建议将以下工具添加到开发环境：

* [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

您可以在[Azure 开发人员工具](https://azure.microsoft.com/tools/)页中查看可用工具。  此外，如果您正在使用视觉工作室，[视觉工作室市场](https://marketplace.visualstudio.com/)。

## <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

在创建 Azure 应用程序产品/服务之前，必须先[创建合作伙伴中心帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)并打开[商业市场仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)，并选中 **"概述**"选项卡。

>[!Note]
>发布产品/服务后，只有在重新发布后，才会在系统和网店中更新对合作伙伴中心中提供的报价进行编辑。  请确保您在进行更改后提交要发布的要约。

### <a name="create-a-new-offer"></a>创建新套餐

选择 **" 新增产品/服务**"按钮，然后选择**Azure 应用程序**菜单项。 将显示"**新建产品/服务**"对话框。

### <a name="offer-id-and-alias"></a>优惠 ID 和别名

* **优惠 ID**：帐户中每个产品/服务的唯一标识符。 此 ID 将在市场产品/服务的 URL 地址和 Azure 资源管理器模板（如果适用）中向客户可见。 <br> <br> 优惠 ID 必须是小写字母数字字符（包括连字符和下划线，但没有空格）。 它限制为 50 个字符，在选择"创建"后无法更改。 <br> <br> 例如，如果您在此处输入`test-offer-1`，则产品/服务 URL`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`将为 。 

* **优惠别名**：用于指合作伙伴中心内产品/服务的名称。 此名称不会在市场中使用，并且与向客户显示的产品/服务名称和其他值不同。 选择 "**创建**"后，无法更改此值。

输入**优惠 ID**和**优惠别名**后，选择 **"创建**"。 然后，您将能够处理产品/服务的所有其他部分。

## <a name="offer-setup"></a>优惠设置

**"产品/服务设置"** 页要求提供以下信息。 请务必在完成这些字段后选择 **"保存**"。

### <a name="test-drive"></a>体验版

试驾是向潜在客户展示您的报价的绝佳方式，为他们提供"购买前试用"选项，从而提高转化率并生成高素质潜在客户。 [了解有关测试驱动器的更多详细信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

要启用试驾，请选中 **"启用试驾"** 框。 然后，您需要在[测试驱动器技术配置](#types-of-azure-application-plans)配置中配置演示环境，以便客户在固定时间段内试用您的产品/服务。 

>[!Note]
>由于所有 Azure 应用程序都是使用 Azure 资源管理器模板实现的，因此可以为 Azure 应用程序配置的唯一类型的测试驱动器是基于[Azure 资源管理器的测试驱动器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

#### <a name="additional-test-drive-resources"></a>其他试驾资源

- [试驾技术最佳实践](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [试驾营销最佳实践](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [试驾概述一个寻呼机](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

在继续下一节之前，请记住**保存**！

## <a name="properties"></a>属性

属性**页面**允许您定义用于在市场对产品/服务进行分组的类别和行业、应用版本和支持产品/服务的法律合同。 完成此页面后选择 **"保存**"。

### <a name="category"></a>类别

选择至少一个且最多三个类别，这些类别将用于将产品/服务放入相应的市场搜索区域。 请务必在产品/服务说明中标注您的产品/服务如何支持这些类别。 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>微软商业市场的标准合同

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>优惠列表

"产品/服务"列表页显示您的优惠将在其中列出的语言。 目前，**英语（美国）** 是唯一可用的选项。

您需要为每个语言/市场定义市场详细信息（报价名称、说明、图像等）。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 报价列表内容（如说明、文档、屏幕截图、使用条款等）不需要以英文表示，只要要约说明以短语开头，"此应用程序仅以 [非英语语言]提供。 提供*有用的链接 URL*以提供产品/服务列表内容中所用语言以外的语言提供内容也是可接受的。

### <a name="name"></a>名称

您在此处输入的名称将作为产品/服务列表的标题显示给客户。 此字段预填充了创建产品/服务时为 **"产品/服务"别名**输入的文本，但您可以更改此值。 此名称可以注册商标（您可以包括商标或版权符号）。 名称不能超过 50 个字符，不能包含任何表情符号。

### <a name="summary"></a>总结

提供产品/服务的简短说明（最多 100 个字符），可在市场搜索结果中使用。

### <a name="long-summary"></a>长摘要

提供更久的报价说明（最多 256 个字符）。 该说明可用于市场搜索结果。

### <a name="description"></a>说明

提供更久的报价说明（最多 3，000 个字符）。 此描述将显示在市场列表概述中的客户。 包括您的产品/服务的价值主张、主要优势、类别和/或行业协会、应用内购买机会以及任何必需的披露。 

编写说明的一些提示：  

- 在前几句描述中清晰描述产品/服务的价值主张。 在价值主张中包括以下项目：
  - 该产品的说明
  - 从产品中受益的用户类型
  - 产品解决的客户需求或痛苦
- 请牢记，前几句话可能在搜索引擎结果中显示。  
- 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
- 尽可能多使用行业特定用语或基于优势的措辞。 
- 请考虑使用 HTML 标记来格式化您的描述，使其更具吸引力。

### <a name="search-keywords"></a>搜索关键字

您可以选择输入最多三个搜索关键字，帮助客户在市场上找到您的产品/服务。 为获得最佳效果，请尝试在描述中使用这些关键字。

### <a name="support-urls"></a>支持 URL

本部分允许您提供链接，帮助客户更好地了解您的产品/服务。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织隐私政策的 URL。 您有责任确保您的应用符合隐私法律和法规，并提供有效的隐私政策。

#### <a name="useful-links"></a>有用链接

提供有关解决方案的可选补充联机文档。  通过单击 **" 添加链接**"添加其他有用的链接。

### <a name="contacts"></a>联系人

在本节中，您必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但可供 Microsoft 使用，并且可能会提供给 CSP 合作伙伴。

在 **"支持"联系人**部分中，还必须提供**支持 URL，** 其中 CSP 合作伙伴可以找到对您的产品/服务的支持。

### <a name="marketplace-images"></a>市场映像

在本节中，您可以提供向客户展示产品/服务时将使用的徽标和图像。 所有图像必须采用 .png 格式。

#### <a name="store-logos"></a>应用商店徽标

提供三种尺寸的产品徽标：**小 （48 x 48）**、**中等 （90 X 90）** 和大 **（216 x 216）**。

#### <a name="hero"></a>英雄

英雄形象是可选的。 如果提供一个，它必须测量 815 x 290 像素。

#### <a name="screenshots"></a>屏幕截图

添加显示优惠工作原理的屏幕截图。 您最多可以添加五个屏幕截图。 所有屏幕截图必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

您可以选择添加最多五个演示您的优惠的视频。 这些视频应托管在 YouTube 和/或 Vimeo 上。 对于每个视频，输入视频的名称、URL 和视频的缩略图图像（1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

- [市场产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>预览

通过 **"预览"** 选项卡，您可以在向更广泛的市场受众实时发布优惠之前定义有限的**预览受众以**验证您的优惠。

> [!IMPORTANT]
> 在预览版中查看您的优惠后，您必须选择 **"上线**"，然后才能将优惠实时发布到市场公众受众。

预览受众由 Azure 订阅 ID GUID 标识，并为每个访问对象加上可选说明。  这两个字段对客户都不可见。

手动添加最多 10 个 Azure 订阅标识，如果上载 CSV 文件，则最多添加 100 个。  通过添加这些订阅，您将定义一个受众，该受众将允许在产品/服务完全发布之前预览访问产品/服务。  如果您的优惠已是实时的，您仍然可以定义预览受众以测试产品/服务的任何更改或更新。

>[!Note]
>预览观众与私人观众不同。 预览受众在市场现场发布*之前*，可以访问您的优惠。 您还可以选择创建计划，使其仅对私人受众可用（使用计划可用性选项卡）。  您的预览受众将能够查看和验证所有计划，包括计划，这些计划仅在您的优惠完全发布到市场后才能提供给私人受众。

## <a name="plan-overview"></a>计划概述

"**计划概述**"选项卡使您能够在同一产品/服务中提供不同的计划选项。 这些计划（在云合作伙伴门户中称为 SKU）在计划类型（解决方案模板与托管应用程序）、货币化或受众方面可能有所不同。  配置至少一个计划，以便在市场上列出您的产品/服务。

创建后，您将看到计划名称、ID、计划类型、可用性（公共或私有）、当前发布状态以及此选项卡上的任何可用操作。

**计划概述**中可用的**操作**因计划的当前状态而异，可能包括：

* 如果计划状态为**草稿**- 删除草稿。
* 如果计划状态为 **"实时**" - 停止销售计划或同步私人受众。

### <a name="create-new-plan"></a>创建新计划

***计划 ID*** - 为此产品/服务中的每个计划创建唯一的计划 ID。 此 ID 将在产品 URL 中对客户可见。  仅使用小写、字母数字字符、破折号或下划线。 此计划 ID 最多允许 50 个字符。 选择创建后无法修改此 ID。

***计划名称***- 客户在决定在您的产品/服务中选择哪个计划时，将看到此名称。 为此产品/服务中的每个计划创建唯一的优惠名称。 计划名称用于区分可能是同一产品/服务一部分的软件计划（例如， 产品名称：Windows 服务器;计划： Windows 服务器 2016， Windows 服务器 2019）.

### <a name="plan-setup"></a>计划设置

"**计划设置**"选项卡使您能够为计划类型设置高级配置，它是否重用来自其他计划的包，以及计划应在哪些云中可用。  此选项卡上的答案将影响同一计划的其他选项卡上显示哪些字段。

#### <a name="plan-type"></a>计划类型

如[Azure 应用程序计划类型](#types-of-azure-application-plans)中所述，选择计划是包含解决方案模板还是托管应用程序。

#### <a name="this-plan-reuses-packages"></a>此计划重用包

如果您有多个相同类型的计划，并且包之间相同，则可以选择**此计划重用来自另一个计划的包**。  选择此选项后，您可以选择相同类型的其他计划之一，以便此产品/服务从中重用包。 

>[!Note]
>当您从其他计划重用包时，整个技术配置选项卡将从这个计划中消失。  其他计划中的技术配置详细信息（包括您将来进行的任何更新）也将用于此计划。 <br> <br> 此外，此计划发布后，无法更改此设置。

#### <a name="cloud-availability"></a>云可用性

此计划必须在至少一个云中提供。 

选择 **"公共 Azure"** 选项可使解决方案部署到具有应用商店集成的所有公共 Azure 区域的客户。  了解有关[地理可用性](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)的更多信息。

选择**Azure 政府云**选项可使解决方案在 Azure[政府云](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)中部署，这是一种政府社区云，对有资格为这些实体提供服务的来自美国联邦、州、本地或部落的客户以及合作伙伴具有受控访问权限。  作为发布者，您负责为此云社区提供服务的任何合规性控制、安全措施和最佳实践。  Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。  在发布到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，Microsoft 建议您在环境中测试和验证解决方案，因为某些终结点可能有所不同。 要暂示和测试您的解决方案，请从此[链接](https://azure.microsoft.com/global-infrastructure/government/request/)请求试用帐户。

>[!Note]
>在特定云中发布计划后，无法删除该云。

**Azure 政府云认证**

仅当在**云可用性**下选择**Azure 政府云**时，此选项才可见。

Azure 政府服务处理受某些政府法规和要求约束的数据，例如 FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。  为了提高您对这些计划的认证的认识，您最多可以提供 100 个描述您的认证的链接。  这些链接可以是直接指向您在程序上列表的链接，也可以是指向您自己网站上遵守这些链接的链接。  这些链接将仅对 Azure 政府云客户可见。

## <a name="plan-listing"></a>计划列表

**"计划列表**"选项卡显示特定于计划的列表信息，这些信息可以不同于同一报价的不同计划。

### <a name="name"></a>名称

根据创建计划时分配的计划的名称进行预填充。  此名称将显示为市场上显示的此"软件计划"的标题。  可能最多包含 100 个字符。

### <a name="summary"></a>总结

提供软件计划的简短摘要。  可能最多包含 100 个字符。

### <a name="description"></a>说明

此说明是一个解释是什么让此软件计划独一无二以及您产品/服务中其他软件计划的任何差异的机会。 可能包含多达 2，000 个字符。

完成这些字段后选择 **"保存**"。

## <a name="availability"></a>可用性

"**可用性**"选项卡仅对解决方案模板计划可见。  您可以使计划仅对特定客户（私人访问群体）以及是否隐藏计划以仅供其他解决方案模板或托管应用程序使用，使计划对所有人可见。

### <a name="plan-audience"></a>规划受众

您可以选择将每个计划配置为对所有人可见，或者仅对您选择的特定受众可见。 您可以使用 Azure 订阅标识分配此受限访问群体的成员身份。

**隐私 /这是一个私人计划**（可选复选框） - 选中此框，使您的计划成为私有的，并且仅对您选择的受限受众可见。 作为私人计划发布后，您可以更新受众或选择使计划可供所有人使用。 一旦计划发布为对每个人都可见，它必须保持对每个人都可见。 （无法再次将计划配置为私有计划）。

**受限访问群体（Azure 订阅标识）** - 分配将有权访问此私人计划的受众。 使用 Azure 订阅 ID 分配访问权限，并选择包括分配的每个 Azure 订阅 ID 的说明。 最多可以添加 10 个订阅 I，如果导入 .csv 电子表格文件，最多可以添加 20，000 个客户订阅 I。  Azure 订阅标识表示为 GUID，字母必须小写。

>[!Note]
>私人受众（或受限观众）不同于您在[**"预览"**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)选项卡中定义的预览受众。 在产品/服务在市场上实时发布*之前*，预览受众可以访问您的优惠。 虽然专用受众指定仅适用于特定计划，但预览观众可以出于验证目的查看所有计划（私有计划或计划）。

### <a name="hide-plan"></a>隐藏计划

如果解决方案模板打算仅在通过其他解决方案模板或托管应用程序引用时间接部署，请选中此框以发布解决方案模板，但直接从客户搜索和浏览它时将其隐藏。

## <a name="pricing-and-availability"></a>定价和可用性

**定价和可用性**选项卡仅对托管应用程序计划可见。  您可以配置此计划可用的市场、解决方案管理每月的价格，以及是使计划对所有人可见，还是仅对特定客户（私人受众）可见。

### <a name="markets"></a>市场

每个计划必须至少在一个市场可用。 选择您希望提供此计划的任何市场位置的复选框。 包含用于选择"免税"国家/地区的搜索框和按钮，其中 Microsoft 代表您汇出销售和使用税，以帮助。

如果您已为计划设定价格（美元），并添加其他市场位置，则新市场的价格将根据当前汇率计算。 在出版之前，务必查看每个市场的价格。 在保存更改后，可以使用"出口价格 （xlsx）"链接查看定价。

### <a name="pricing"></a>定价

提供此计划的每月价格。  此价格是此解决方案部署的资源产生的任何 Azure 基础结构或即用即付软件成本的补充。

以当地货币（美元 = 美元）设置的价格使用设置期间可用的当前汇率转换为所有选定市场的当地货币。 通过导出定价电子表格并查看每个市场的价格，在发布之前验证这些价格。 如果要在单个市场设置自定义价格，请修改并导入定价电子表格。 

>[!Note]
>您必须首先保存定价更改，以启用定价数据的导出。

在发布之前，请仔细查看您的价格，因为在计划发布后，可能会更改哪些内容有一些限制。  

>[!Note]
>一旦计划中市场的价格公布，以后就改变不了。

### <a name="plan-audience"></a>规划受众

您可以选择将每个计划配置为对所有人可见，或者仅对您选择的特定受众可见。 您可以使用 Azure 订阅标识分配此受限访问群体的成员身份。

**隐私 /这是一个私人计划**（可选复选框） - 选中此框，使您的计划成为私有的，并且仅对您选择的受限受众可见。 作为私人计划发布后，您可以更新受众或选择使计划可供所有人使用。 一旦计划发布为对每个人都可见，它必须保持对每个人都可见。 （无法再次将计划配置为私有计划）。

**受限访问群体（Azure 订阅标识）** - 分配将有权访问此私人计划的受众。 使用 Azure 订阅 ID 分配访问权限，并选择包括分配的每个 Azure 订阅 ID 的说明。 最多可以添加 10 个订阅 I，如果导入 .csv 电子表格文件，最多可以添加 20，000 个客户订阅 I。  Azure 订阅标识表示为 GUID，字母必须小写。

>[!Note]
>私人受众（或受限观众）不同于您在[**"预览"**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)选项卡中定义的预览受众。 在产品/服务在市场上实时发布*之前*，预览受众可以访问您的优惠。 虽然专用受众指定仅适用于特定计划，但预览观众可以出于验证目的查看所有计划（私有计划或计划）。

## <a name="technical-configuration"></a>技术配置 

**技术配置**选项卡允许您上载部署包，以便客户能够部署您的计划。

>[!Note]
>如果此计划配置为在 **"计划设置"** 选项卡上重用来自其他计划的包，则此选项卡将不可见。

### <a name="package-details"></a>包详细信息

通过"**包详细信息**"子选项卡，您可以编辑技术配置的草稿版本。

***版本***- 分配技术配置的当前版本。  每次发布此页的更改时，都会增加此版本。 版本必须采用格式`{integer}.{integer}.{integer}`。

***包文件***`.zip`（ ） - 此包包含此计划所需的所有模板文件，以及作为`.zip`文件打包的任何其他资源。

所有 Azure 应用程序计划包都必须在存档的根文件夹中包含这两`.zip`个文件：

* 名为[mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的资源管理器模板文件。  此模板自动将资源部署到客户 Azure 订阅中。  有关资源管理器模板的示例，请参阅[Azure 快速入门模板库](https://azure.microsoft.com/documentation/templates/)或相应的[GitHub：Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。

* 名为[createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)的 Azure 应用程序创建体验的用户界面定义。

所有新的 Azure 应用程序产品/服务还必须包括[Azure 合作伙伴客户使用情况归因](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)GUID。

### <a name="previously-published-packages"></a>以前发布的软件包 

通过 **"以前发布的包**子选项卡"，您可以查看技术配置的所有已发布版本。

## <a name="technical-configuration-managed-application-plans-only"></a>技术配置（仅限托管应用程序计划）

除了上述**版本**和**包文件**字段之外，"**技术配置**"选项卡上托管的应用程序计划具有额外的复杂性。 

### <a name="enable-just-in-time-jit-access"></a>启用准时 （JIT） 访问

选择此选项可启用此计划的准时 （JIT） 访问权限。  JIT 访问允许你请求对托管应用程序资源的提升访问权限，以便进行故障排除或维护。 你始终对资源具有只读访问权限，但在特定的时间段内，你可以具有更高的访问权限。  有关详细信息，请参阅[启用和请求 Azure 托管应用程序的及时访问](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)。  要要求托管应用程序的使用者授予您的帐户永久访问权限，请取消选中此选项。

>[!Note]
>请务必更新文件`createUiDefinition.json`以支持此功能。  

### <a name="deployment-mode"></a>部署模式

选择在部署此计划时是配置 **"完成****"还是"增量部署模式**"： 

* 在**完全模式下**，如果资源未在 中定义，则客户重新部署应用程序将导致删除托管资源组中的资源`mainTemplate.json`。 
* 在**增量模式下**，应用程序的重新部署使现有资源保持不变。

要了解有关部署模式的更多信息，请参阅[Azure 资源管理器部署模式](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)。

### <a name="notification-endpoint-url"></a>通知终结点 URL

指定 HTTPS Webhook 终结点以接收有关此计划版本的托管应用程序实例上所有 CRUD 操作的通知。

### <a name="customize-allowed-customer-actions"></a>自定义允许的客户操作

选择此选项可指定客户除了默认可用的""`*/read`操作外，还可以对托管资源执行的操作。 

列出您希望使客户在此处执行的其他操作，这些操作以分号分隔。  有关详细信息，请参阅了解[Azure 资源的拒绝分配](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)。  有关可用操作，请参阅 [Azure 资源管理器资源提供程序操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)。 例如，若要允许使用者重启虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到允许的操作。

### <a name="global-azure--azure-government-cloud"></a>全局 Azure / Azure 政府云

指示谁应该在每个受支持的云中对此托管应用程序具有管理访问权限。  使用 Azure 活动目录 （AAD） 标识要被授予托管资源组权限的用户、组或应用程序。

***Azure 活动目录租户 ID*** - AAD 租户 ID（也称为目录 ID），包含要向其授予权限的用户、组或应用程序的身份。  您可以在 Azure 门户[（Azure 活动目录的属性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)）中找到 AAD 租户 ID。

***授权***- 添加要授予托管资源组权限的用户、组或应用程序的 Azure 活动目录对象 ID。 通过其主体 ID 标识用户，可在 Azure 门户上的[Azure 活动目录用户边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到该 ID。

对于每个主体，从列表中选择一个 Azure AD 内置角色（所有者或参与者）。 您选择的角色将描述主体将具有的客户订阅中资源的权限。 有关详细信息，请参阅 [Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。  有关基于角色的访问控制 （RBAC） 的详细信息，请参阅[在 Azure 门户中开始使用 RBAC。](https://docs.microsoft.com/azure/role-based-access-control/overview)

>[!Note]
>尽管每个云最多可以添加 100 个授权，但通常更容易创建活动目录用户组并在"主体 ID"中指定其 ID。  这将使您能够在部署计划后向管理组添加更多用户，并减少仅更新计划以添加更多授权的需要。

### <a name="policy-settings"></a>策略设置

将[Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview)应用于托管应用程序，以指定已部署解决方案的合规性要求。  有关策略定义和参数值的格式，请参阅 [Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/index)。  您最多可以配置五个策略，并且每个策略选项只能配置一个实例。  某些策略需要额外的参数。  审核策略需选择标准 SKU。  策略名称限制为 50 个字符。

## <a name="co-sell"></a>联合销售

在 Cosell 选项卡上提供信息是完全可选的，可用于发布您的产品/服务。 它需要实现联合销售就绪和 IP 共同销售就绪状态。 Microsoft 销售团队将使用您提供的信息，在评估解决方案是否适合客户需求时了解有关解决方案的更多信息。 它不能直接提供给客户。

有关完成此选项卡的详细信息，请参阅[合作伙伴中心中的"共同销售"选项](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)。

## <a name="test-drive"></a>体验版

通过 **"测试驱动器**"选项卡，您可以设置演示（或"试驾"），使客户能够在承诺购买之前试用您的产品/服务。 在文章"[什么是试驾"](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)中了解更多信息？  如果您不想再为产品/服务提供试驾，请返回 **"产品/服务设置**"页面并取消选中**启用试驾**。

### <a name="technical-configuration"></a>技术配置

Azure 应用程序本质上使用 Azure 资源管理器测试驱动器类型。  有关详细信息[，请参阅 Azure 资源管理器测试驱动器的技术配置](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

为了代表您部署测试驱动器，请创建并提供单独的唯一 Azure 订阅。 （电源 BI 测试驱动器不需要）。

- **Azure 订阅 ID（Azure**资源管理器和逻辑应用需要）：输入订阅 ID 以授予对 Azure 帐户服务的访问权限，以便进行资源使用情况报告和计费。 如果尚未创建，我们建议您考虑[创建单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用于测试驱动器。 您可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 **"订阅"** 选项卡来查找 Azure 订阅 ID。 选择该选项卡将显示您的订阅 ID（例如"a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租户 ID（** 必需）：输入 Azure 活动目录 （AD）[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧菜单中的"活动目录"选项卡，选择 "属性"，然后查找列出的**目录 ID**号（例如 50c464d3-4930-494c-963c-1e951d15360e）。 您还可以使用域名 URL 在 ：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)查找组织的租户 ID。

- **Azure AD 租户名称**（动态 365 需要）：输入 Azure 活动目录 （AD） 名称。 要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，租户名称将列在您的帐户名称下。

- **Azure AD 应用 ID（** 必需）：输入 Azure 活动目录 （AD）[应用程序 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧菜单中的"活动目录"选项卡，选择**应用注册**，然后查找列出的应用程序**ID**号（例如 50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端机密**（必需）：输入 Azure AD 应用程序[客户端密钥](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 要查找此值，请登录到 Azure[门户](https://portal.azure.com/)。 选择左侧菜单中的**Azure 活动目录**选项卡，选择**应用注册**，然后选择试驾应用。 接下来，选择**证书和机密**，选择 **"新建客户端机密**"，输入说明，选择 **"从不在过期**"下，然后选择"**添加**"。 **Never** 请确保向下复制该值。 （在复制该值之前，不要离开页面，否则您将无法访问该值。

在继续下一节之前，请记住**保存**！

### <a name="test-drive-listings-optional"></a>试驾列表（可选）

**"试驾****"选项卡下的"试驾列表**"选项显示您的试驾可用的语言（和市场），当前英语（美国）是唯一可用的位置。 此外，此页显示特定于语言的列表的状态以及添加该列表的日期/时间。 您需要为每个语言/市场定义试驾详细信息（说明、用户手册、视频等）。

- **说明**（必需）：描述您的试驾、演示的内容、供用户试验的目标、要浏览的功能以及任何相关信息，以帮助用户确定是否获取您的产品/服务。 此字段中最多可输入 3，000 个字符的文本。 

- **访问信息**（Azure 资源管理器和逻辑测试驱动器所需的信息）：解释客户需要了解哪些内容才能访问和使用此测试驱动器。 演练使用产品/服务的方案，以及客户在整个试驾中访问功能的准确情况。 此字段中最多可输入 10，000 个字符的文本。

- **用户手册**（必需）：对试驾体验的深入演练。 《用户手册》应准确介绍您希望客户从体验试驾中获得什么，并作为他们可能存在的任何问题的参考。 文件必须采用 PDF 格式，并在上载后命名（最多 255 个字符）。

- **视频：添加视频**（可选）：视频可以上传到 YouTube 或 Vimeo，并在此处引用链接和缩略图图像 （533 x 324 像素），以便客户可以查看信息的演练，以帮助他们更好地了解试驾，包括如何成功使用您的产品/服务的功能，并了解突出其优势的方案。
  - **名称**（必需）
  - **网址（仅限 YouTube 或 Vimeo）（** 必填）
  - **缩略图 （533 x 324 px）**： 图像文件必须采用 PNG 格式。

完成这些字段后选择 **"保存**"。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>提交优惠以预览

完成产品/服务的所有必需部分后，选择门户右上角的**发布**。 您将被重定向到 **"审阅"和"发布"** 页。 

如果您是第一次发布此优惠，您可以：

- 请参阅产品/服务的每个部分的完成状态。
    - *未启动*- 表示该部分尚未触摸且需要完成。
    - *不完整*- 表示该节有需要修复的错误或需要提供更多信息。 请返回该部分并更新它。
    - *完成*- 表示部分已完成，提供了所有必需的数据，并且没有错误。 报价的所有部分都必须处于完全状态，然后才能提交报价。
- 除了任何有助于了解应用的补充说明外，向认证团队提供测试说明，以确保正确测试你的应用。
- 通过选择 **"提交**"提交要发布的产品/ 我们将向您发送一封电子邮件，告知您何时可以审核和批准优惠的预览版本。 返回合作伙伴中心，选择 **"Go-live"** 以优惠形式向公众发布您的优惠（如果是私人优惠，则向私人受众发布）。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的**手动验证**步骤表示对产品/服务及其相关技术资产（尤其是 Azure 资源管理器模板）的广泛审查，问题通常以拉取请求 （PR） 链接的形式显示。 有关如何查看和响应这些 PR 的说明，请参阅[处理评审反馈](./azure-apps-review-feedback.md)。

如果在一个或多个发布步骤中遇到错误，则必须更正错误并重新发布产品/服务。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
