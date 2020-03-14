---
title: 在商业应用商店中创建新的 Azure 应用产品/服务
description: 如何在 Azure Marketplace、AppSource 或通过云解决方案提供商（CSP）计划使用 Microsoft 合作伙伴中心上的商业应用商店门户创建新的 Azure 应用服务。
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 69d7bc81f5adc4797be86f946fdb656b4b6cc682
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283363"
---
# <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

此处介绍了在商业应用商店中发布 Azure 应用程序产品/服务的步骤。

## <a name="azure-application-offer-type"></a>Azure 应用程序产品/服务类型

本主题概述了有关 Azure 应用程序产品/服务的基础知识。  在 Marketplace 中开始发布新的 Azure 应用程序产品/服务之前，你应该熟悉这些概念。

### <a name="publishing-overview"></a>发布概述

Azure Marketplace 的视频[构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)是对 azure 应用程序产品/服务类型的介绍：

* 提供哪些可用类型;
* 需要哪些技术资产;
* 如何创作 Azure 资源管理器模板;
* 开发和测试应用程序 UI;
* 如何发布应用程序产品/服务
* 应用程序审核过程。

### <a name="types-of-azure-application-plans"></a>Azure 应用程序计划的类型

有两种类型的 Azure 应用程序计划：托管应用程序和解决方案模板。

* **解决方案模板**是在 Marketplace 中发布解决方案的主要方法之一。 当你的解决方案需要超出单个虚拟机（VM）的其他部署和配置自动化时，可使用此计划类型。  使用解决方案模板，可以自动提供多个资源，包括 Vm、网络和存储资源，以提供复杂的 IaaS 解决方案。  有关生成解决方案模板的详细信息，请参阅[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)文档。

* **托管应用程序**类似于解决方案模板，但有一个重要区别。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。 使用托管应用程序轻松构建完全托管的全包式应用程序并将其交付给客户。  有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](https://docs.microsoft.com/azure/managed-applications/overview)。

所有 Azure 应用程序在 `.zip` 存档的根文件夹中至少包含两个文件：

* 资源管理器名为[maintemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的模板文件。  此模板用于定义要部署到客户的 Azure 订阅中的资源。  有关资源管理器模板的示例，请参阅[Azure 快速入门模板库](https://azure.microsoft.com/resources/templates/)或相应的[GitHub： Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。

* 名为[createUiDefinition](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)的 Azure 应用程序创建体验的用户界面定义。  在用户界面中，可以指定让使用者能够提供参数值的元素。

所有新的 Azure 应用程序产品/服务必须包括[Azure 合作伙伴客户使用归属 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)。

### <a name="before-you-begin"></a>开始之前

查看以下 Azure 应用程序文档，其中提供了快速入门、教程和示例。

* [了解 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* 快速入门：

    * [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 快速入门模板](https://github.com/azure/azure-quickstart-templates)
    * [发布应用程序定义](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [部署服务目录应用](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 教程：

    * [创建定义文件](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [发布市场应用程序](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 示例：

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [托管应用程序解决方案](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>技术知识中的基础知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。

工程团队应该了解以下 Microsoft 技术：

* 基本了解[Azure 服务](https://azure.microsoft.com/services/)。
* 如何[设计和构建 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)。
* [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和[azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的工作知识。
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的工作知识。
* [JSON](https://www.json.org/)的工作知识。

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

可以在 " [Azure 开发人员工具](https://azure.microsoft.com/tools/)" 页中查看可用的工具。  此外，如果使用的是 Visual Studio，则[Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

创建 Azure 应用程序产品/服务之前，必须先[创建合作伙伴中心帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，并打开 "概述" 选项[卡，并](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)选择 "**概述**" 选项卡。

>[!Note]
>发布产品/服务后，将仅在系统中更新合作伙伴中心提供的产品/服务，并在重新发布后进行编辑。  进行更改后，请确保提交产品/服务进行发布。

### <a name="create-a-new-offer"></a>创建新套餐

选择 " **+ 新建产品/服务**" 按钮，然后选择 " **Azure 应用程序**" 菜单项。 将显示 "**新建产品/服务**" 对话框。

### <a name="offer-id-and-alias"></a>产品/服务 ID 和别名

* **产品/服务 ID**：帐户中每个产品/服务的唯一标识符。 在 marketplace 产品/服务和 Azure 资源管理器模板（如果适用）的 URL 地址中，客户将可以看到此 ID。 <br> <br> 你的产品/服务 ID 必须是小写字母数字字符（包括连字符和下划线，但没有空格）。 它限制为50个字符，并且在你选择 "创建" 后将无法更改。 <br> <br> 例如，如果在此处输入 `test-offer-1`，则将 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`产品/服务 URL。 

* **产品/服务别名**：用于引用合作伙伴中心内的产品/服务的名称。 此名称不会在 marketplace 中使用，并且不同于将向客户显示的产品/服务名称和其他值。 选择 "**创建**" 后，不能更改此值。

输入产品/服务**ID**和**提议**后，选择 "**创建**"。 然后，你将能够使用产品/服务的所有其他部分。

## <a name="offer-setup"></a>产品/服务设置

"**产品/服务设置**" 页面要求提供以下信息。 请确保在完成这些字段后选择 "**保存**"。

### <a name="test-drive"></a>体验版

通过向客户提供 "在购买前试用" 的选项，测试驱动器是向潜在客户展示你的产品/服务的一种好办法 [了解有关测试驱动器的详细信息。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

若要启用某个测试驱动器，请选中 "**启用测试驱动器**" 框。 然后需要在[测试驱动器技术配置](#types-of-azure-application-plans)配置中配置演示环境，让客户在一段固定的时间内试用你的产品/服务。 

>[!Note]
>由于所有 Azure 应用程序都是使用 Azure 资源管理器模板来实现的，因此只能为 Azure 应用程序配置的测试驱动器类型是[基于 Azure 资源管理器的测试驱动器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

#### <a name="additional-test-drive-resources"></a>其他测试驱动器资源

- [测试驱动器技术最佳方案](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [试用版销售最佳实践](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Test Drive 概述 One 寻呼](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>连接潜在客户管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在客户管理概述](./commercial-marketplace-get-customer-leads.md)。

请记住在转到下一节之前**保存**。

## <a name="properties"></a>属性

使用 "**属性**" 页，可以定义用于对 marketplace、应用版本以及支持产品/服务的法律合同进行分组的类别和行业。 完成此页后，选择 "**保存**"。

### <a name="category"></a>类别

选择最小值，最多可选择三个类别，这些类别将用于将你的产品/服务放入相应的 marketplace 搜索区域。 在产品/服务描述中，请务必了解你的产品/服务如何支持这些类别。 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 商用 marketplace 的标准协定

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>产品/服务列表

产品/服务列表页显示你的产品/服务将列出的语言。 目前，只有**英语（美国）** 是可用的选项。

需要为每种语言/市场定义 marketplace 详细信息（产品/服务名称、说明、图像等）。 选择语言/市场名称以提供此信息。

> [!NOTE]
> 产品/服务列表内容（如描述、文档、屏幕截图、使用条款等）无需使用英语，只要提议说明以短语开头，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个*有用的链接 URL*来提供与产品/服务列表内容中所用语言不同的内容。

### <a name="name"></a>名称

你在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预填充了你在创建产品/服务时为 "**产品/服务别名**" 输入的文本，但你可以更改此值。 此名称可能是商标字（并且你可以包括商标或版权符号）。 名称不能超过50个字符，并且不能包含任何表情符号。

### <a name="summary"></a>总结

提供产品/服务的简短说明（最多100个字符），可以在 marketplace 搜索结果中使用。

### <a name="long-summary"></a>长摘要

提供产品/服务的更长说明（最多256个字符）。 此说明可用于 marketplace 搜索结果中。

### <a name="description"></a>说明

提供产品/服务的更长说明（最多3000个字符）。 此说明将在 marketplace 列表概述中向客户显示。 包括产品/服务的价值主张、主要优势、类别和/或行业关联、应用内购买机会，以及任何所需的公开。 

编写说明的一些提示：  

- 在前几句描述中清晰描述产品/服务的价值主张。 在价值主张中包括以下各项：
  - 该产品的说明
  - 从产品中受益的用户类型
  - 产品地址的客户需求或难点
- 请牢记，前几句话可能在搜索引擎结果中显示。  
- 不要依赖于特性和功能来销售产品。 而应该强调提供的价值。  
- 尽可能多使用行业特定用语或基于优势的措辞。 
- 请考虑使用 HTML 标记来设置说明格式，并使其更具吸引力。

### <a name="search-keywords"></a>搜索关键字

你可以根据需要输入最多三个搜索关键字，帮助客户在 marketplace 中找到你的产品/服务。 为获得最佳结果，请尝试在描述中使用这些关键字。

### <a name="support-urls"></a>支持 Url

本部分允许你提供链接，以帮助客户了解你的产品/服务的详细信息。

#### <a name="privacy-policy-url"></a>隐私策略 URL

输入组织的隐私策略的 URL。 你需要负责确保你的应用符合隐私法律和法规，并提供有效的隐私策略。

#### <a name="useful-links"></a>有用链接

提供有关解决方案的可选补充联机文档。  单击 " **+ 添加链接**"，添加其他有用的链接。

### <a name="contacts"></a>联系人

在本部分中，你必须提供**支持联系人**和**工程联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但会提供给 Microsoft，并可提供给 CSP 合作伙伴。

在 "**支持联系人**" 部分中，你还必须提供**支持 URL** ，CSP 合作伙伴可在其中找到对你的产品/服务的支持。

### <a name="marketplace-images"></a>Marketplace 映像

在本部分中，可以提供向客户显示产品/服务时要使用的徽标和图像。 所有图像必须采用 .png 格式。

#### <a name="store-logos"></a>应用商店徽标

以三种大小提供产品/服务的徽标：**小（48 x 48）** 、**中（90 x 90）** 和**大（216 x 216）** 。

#### <a name="hero"></a>英雄

英雄映像是可选的。 如果提供了一个，则必须度量 815 x 290 像素。

#### <a name="screenshots"></a>屏幕截图

添加显示产品/服务工作方式的屏幕截图。 最多可以添加五个屏幕截图。 所有屏幕截图必须为 1280 x 720 像素。

#### <a name="videos"></a>视频

你可以选择性地添加最多5个视频来演示你的产品/服务。 这些视频应托管在 YouTube 和/或 Vimeo 上。 对于每个名称，请输入视频的名称、其 URL 和视频的缩略图（1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

- [Marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>预览

使用 "**预览**" 选项卡，你可以在将产品/服务发布到更广泛的 marketplace 受众之前，定义用于验证产品/服务的受限**预览版观众**。

> [!IMPORTANT]
> 在预览版中检查产品/服务后，你必须选择 "**开始**"，然后将产品/服务发布到 marketplace 公共受众。

预览受众由 Azure 订阅 ID Guid 标识，该 Guid 与每个用户的可选说明配对。  这两个字段对客户都不可见。

手动添加最多10个 Azure 订阅 Id，或最多添加100（如果上传 CSV 文件）。  通过添加这些订阅，你可以定义一个访问群体，使其在完全发布之前允许预览版对你的产品/服务的预览访问权限。  如果你的产品/服务已处于活动阶段，你仍可以定义预览受众来测试产品/服务的任何更改或更新。

>[!Note]
>预览受众不同于一个专用受众。 在市场上发布生活*之前*，允许预览版观众访问你的产品/服务。 你还可以选择创建一个计划并使其仅可供专用用户使用（使用 "计划可用性" 选项卡）。  你的预览版观众将能够查看和验证所有计划，包括将你的产品/服务完全发布到 marketplace 后，将仅适用于专用用户的计划。

## <a name="plan-overview"></a>计划概述

利用 "**计划概述**" 选项卡，你可以在同一个服务中提供不同的计划选项。 这些计划（在云合作伙伴门户中称为 Sku）在计划类型（解决方案模板和托管应用程序）、盈利或受众方面可能有所不同。  至少配置一个计划，以便在 marketplace 中列出你的产品/服务。

创建后，你将在此选项卡上看到计划名称、Id、计划类型、可用性（公共或私有）、当前发布状态和任何可用操作。

**计划概述**中的可用**操作**取决于计划的当前状态，并且可能包括：

* 如果计划状态为 "**草稿**–删除草稿"。
* 如果计划状态为 "**实时**" – "停止销售计划" 或 "同步专用受众"。

### <a name="create-new-plan"></a>创建新计划

***计划 id*** -为此产品/服务中的每个计划创建唯一的计划 id。 此 ID 将对产品 URL 中的客户可见。  仅使用小写字母、字母数字字符、短划线或下划线。 此计划 ID 最多允许50个字符。 选择 "创建" 后，不能修改此 ID。

***计划名称***-客户在确定要在产品/服务中选择的计划时，将看到此名称。 为此产品/服务中的每个计划创建一个唯一的产品/服务名称。 计划名称用于区分可能属于同一产品/服务的软件计划（例如 产品/服务名称： Windows Server;计划： Windows Server 2016、Windows Server 2019）。

### <a name="plan-setup"></a>计划设置

"**计划设置**" 选项卡允许您为计划的类型设置高级配置，无论它是否重复使用其他计划中的包以及计划应在哪些云中可用。  此选项卡上的答案将影响在同一计划的其他选项卡上显示的字段。

#### <a name="plan-type"></a>计划类型

如[Azure 应用程序计划的类型](#types-of-azure-application-plans)中所述，选择计划是否包含解决方案模板或托管应用程序。

#### <a name="this-plan-reuses-packages"></a>此计划重用包

如果有多个相同类型的计划，并且这些包在它们之间完全相同，则可以选择**此计划重用其他计划中的包**。  当你选择此选项时，你将能够选择同一类型的其他计划之一，以便重复使用中的包。 

>[!Note]
>当你从其他计划中重新使用包时，将从此计划中消失整个技术配置选项卡。  其他计划中的技术配置详细信息（包括将来所做的任何更新）也将用于此计划。 <br> <br> 此外，发布此计划后，不能更改此设置。

#### <a name="cloud-availability"></a>云可用性

必须在至少一个云中提供此计划。 

选择**公共 azure**选项，使解决方案可部署到具有 Marketplace 集成的所有公共 Azure 区域中的客户。  了解有关[地理可用性](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)的详细信息。

选择 " **Azure 政府云**" 选项，使解决方案可在[Azure 政府版云中](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)进行部署，这是一个政府社区云，具有对美国联邦、州、地方或部落的客户和有资格提供这些实体的合作伙伴的控制访问权限。  作为发布者，你需要负责提供此云社区的任何符合性控制、安全措施和最佳实践。  Azure 政府版使用物理上隔离的数据中心和网络（仅限美国）。  在发布到[Azure 政府](https://aka.ms/azuregovpublish)版之前，Microsoft 建议您在环境中测试和验证您的解决方案，因为某些终结点可能会有所不同。 若要暂存和测试解决方案，请从此[链接](https://azure.microsoft.com/global-infrastructure/government/request/)请求试用帐户。

>[!Note]
>一旦将某个计划发布为可用的特定云中，该云将无法删除。

**Azure 政府云认证**

仅当在 "**云可用性**" 下选择了 " **Azure 政府云**" 时，此选项才可见。

Azure 政府服务处理受特定政府法规和要求的数据，例如 FedRAMP、NIST 800.171 （DIB）、ITAR、IRS 1075、DoD L4 和 CJIS。  若要对这些程序进行了解，可以提供多达100的链接来描述证书。  这些链接可以直接链接到节目上的列表，也可以链接到自己的网站上的符合性说明。  这些链接仅对 Azure 政府云客户可见。

## <a name="plan-listing"></a>计划列表

"**计划列表**" 选项卡显示特定产品/服务的不同计划的计划特定的列表信息。

### <a name="name"></a>名称

根据你在创建计划时为其分配的名称预先填充。  此名称将显示在 marketplace 中显示的 "软件计划" 的标题中。  最多可包含100个字符。

### <a name="summary"></a>总结

提供软件计划的简短摘要。  最多可包含100个字符。

### <a name="description"></a>说明

此说明是一个机会，用于说明此软件计划的独特之处，以及与你的产品/服务中的其他软件计划的不同之处。 最多可包含2000个字符。

完成这些字段后，选择 "**保存**"。

## <a name="availability"></a>可用性

"**可用性**" 选项卡仅对解决方案模板计划可见。  您可以使该计划对每个人都可见，只允许特定客户（一个专用受众），以及是否将该计划隐藏为仅供其他解决方案模板或托管应用程序使用。

### <a name="plan-audience"></a>规划受众

你可以选择将每个计划配置为对每个用户可见，或者仅配置为你选择的特定群体。 你可以使用 Azure 订阅 Id 分配此受限受众的成员身份。

**隐私/这是专用计划**（可选复选框）-选中此框以使你的计划成为私有计划，且仅对你选择的受限受众可见。 作为专用计划发布后，您可以更新受众或选择使该计划对所有人都可用。 一旦将某个计划发布为每个人都可见，它就必须对所有人都保持可见。 （计划不能再次配置为专用计划）。

**受限受众（Azure 订阅 id）** -分配将有权访问此私有计划的受众。 使用 Azure 订阅 Id 分配访问权限，其中包含为分配的每个 Azure 订阅 ID 提供说明。 如果导入 .csv 电子表格文件，则最多可以添加10个订阅 Id 或20000个客户订阅 Id。  Azure 订阅 Id 表示为 Guid，字母必须为小写。

>[!Note]
>专用受众（或受限受众）不同于你在 "[**预览**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)" 选项卡中定义的预览受众。 在 marketplace 中发布产品/服务*之前*，允许预览版用户访问你的产品/服务。 尽管专用受众称号仅适用于特定计划，但预览受众可以查看所有计划（private）以进行验证。

### <a name="hide-plan"></a>隐藏计划

如果你的解决方案模板仅适用于引用其他解决方案模板或托管应用程序时间接部署，请选中此复选框以发布你的解决方案模板，但会将其从客户直接对其进行搜索和浏览。

## <a name="pricing-and-availability"></a>定价和可用性

"**定价和可用性**" 选项卡仅对托管应用程序计划可见。  你可以配置此计划将在其中可用的市场，解决方案管理的每月价格，以及是使计划对每个人都可见，还是仅供特定客户（一个专用受众）使用。

### <a name="markets"></a>市场

每个计划都必须至少在一个市场中可用。 对于想要使此计划可用的任何市场位置，选中相应的复选框。 用于选择 "已汇款" 国家/地区的搜索框和按钮，其中包含 Microsoft 汇寄的销售和使用条款，其中包含你的帮助。

如果已将计划的价格设置为美国美元（USD）并添加另一个市场位置，则将根据当前汇率计算新市场的价格。 请始终在发布前查看每个市场的价格。 保存更改后，可以通过使用 "导出价格（.xlsx）" 链接来查看定价。

### <a name="pricing"></a>定价

提供此计划的每月价格。  此价格是此解决方案部署的资源所产生的任何 Azure 基础结构或即用即付软件成本的补充。

使用在安装过程中可用的当前汇率，使用当地货币（USD = 美国美元）将价格设置为所有所选市场的本地货币。 在发布之前通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单个市场中设置自定义价格，请修改并导入定价电子表格。 

>[!Note]
>必须先保存定价更改，才能导出定价数据。

发布之前请仔细查看你的价格，因为发布计划后可能会有一些更改。  

>[!Note]
>一旦发布了计划中市场的价格，以后就不能再对其进行更改。

### <a name="plan-audience"></a>规划受众

你可以选择将每个计划配置为对每个用户可见，或者仅配置为你选择的特定群体。 你可以使用 Azure 订阅 Id 分配此受限受众的成员身份。

**隐私/这是专用计划**（可选复选框）-选中此框以使你的计划成为私有计划，且仅对你选择的受限受众可见。 作为专用计划发布后，您可以更新受众或选择使该计划对所有人都可用。 一旦将某个计划发布为每个人都可见，它就必须对所有人都保持可见。 （计划不能再次配置为专用计划）。

**受限受众（Azure 订阅 id）** -分配将有权访问此私有计划的受众。 使用 Azure 订阅 Id 分配访问权限，其中包含为分配的每个 Azure 订阅 ID 提供说明。 如果导入 .csv 电子表格文件，则最多可以添加10个订阅 Id 或20000个客户订阅 Id。  Azure 订阅 Id 表示为 Guid，字母必须为小写。

>[!Note]
>专用受众（或受限受众）不同于你在 "[**预览**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)" 选项卡中定义的预览受众。 在 marketplace 中发布产品/服务*之前*，允许预览版用户访问你的产品/服务。 尽管专用受众称号仅适用于特定计划，但预览受众可以查看所有计划（private）以进行验证。

## <a name="technical-configuration"></a>技术配置 

使用 "**技术配置**" 选项卡，你可以上载部署包，以便客户能够部署你的计划。

>[!Note]
>如果已将此计划配置为从 "**计划设置**" 选项卡上的其他计划中重新使用包，则不会显示此选项卡。

### <a name="package-details"></a>包详细信息

"**包详细信息**" 子标签可用于编辑技术配置的草稿版本。

***版本***-分配技术配置的当前版本。  每次将更改发布到此页面时，请递增此版本。 版本的格式必须 `{integer}.{integer}.{integer}`。

***包文件***（`.zip`）-此包包含此计划所需的所有模板文件以及作为 `.zip` 文件打包的任何其他资源。

所有 Azure 应用程序计划包都必须在 `.zip` 存档的根文件夹中包括以下两个文件：

* 资源管理器名为[maintemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的模板文件。  此模板自动将资源部署到客户的 Azure 订阅中。  有关资源管理器模板的示例，请参阅[Azure 快速入门模板库](https://azure.microsoft.com/documentation/templates/)或相应的[GitHub： Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。

* 名为[createUiDefinition](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)的 Azure 应用程序创建体验的用户界面定义。

所有新的 Azure 应用程序产品/服务还必须包含[Azure 合作伙伴客户使用归属](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)GUID。

### <a name="previously-published-packages"></a>以前发布的包 

通过 "**以前发布的包**" 子标签，可以查看技术配置的所有已发布版本。

## <a name="technical-configuration-managed-application-plans-only"></a>技术配置（仅限托管应用程序计划）

托管应用程序计划在 "**技术配置**" 选项卡上的 "**版本**" 和 "**包文件**" 字段之外有更多的复杂性。 

### <a name="enable-just-in-time-jit-access"></a>启用实时（JIT）访问

选择此选项可为此计划启用实时（JIT）访问。  JIT 访问允许你请求对托管应用程序的资源的提升访问权限，以便进行故障排除或维护。 你始终对资源具有只读访问权限，但对于特定时间段，你可以获得更高的访问权限。  有关详细信息，请参阅[为 Azure 托管应用程序启用和请求实时访问](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)。  若要要求托管应用程序的使用者授予你的帐户永久访问权限，请将此选项保留为未选中状态。

>[!Note]
>请确保更新 `createUiDefinition.json` 文件，以便支持此功能。  

### <a name="deployment-mode"></a>部署模式

选择是否在部署此计划时配置**完整**或**增量部署模式**： 

* 在**完整模式**下，客户重新部署应用程序将导致在 `mainTemplate.json`中未定义资源的情况下删除托管资源组中的资源。 
* 在**增量模式**下，应用程序的重新部署会使现有资源保持不变。

若要详细了解部署模式，请参阅[Azure 资源管理器部署模式](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)。

### <a name="notification-endpoint-url"></a>通知终结点 URL

指定一个 HTTPS Webhook 终结点，以接收有关此计划版本的托管应用程序实例上的所有 CRUD 操作的通知。

### <a name="customize-allowed-customer-actions"></a>自定义允许的客户操作

选择此选项可指定除默认提供的 "`*/read`" 操作外，客户可以对托管资源执行的操作。 

列出要使客户能够在此处执行的其他操作（用分号分隔）。  有关详细信息，请参阅[了解 Azure 资源的拒绝分配](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)。  有关可用操作，请参阅 [Azure 资源管理器资源提供程序操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)。 例如，若要允许使用者重启虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到允许的操作。

### <a name="global-azure--azure-government-cloud"></a>全球 Azure/Azure 政府云

指示在每个受支持的云中对此托管应用程序的管理访问权限。  要向其授予对托管资源组的权限的用户、组或应用程序使用 Azure Active Directory （AAD）标识进行标识。

***Azure Active Directory 租户 id*** -包含要向其授予权限的用户、组或应用程序的标识的 AAD 租户 id （也称为目录 ID）。  可以在 Azure 门户上的 " [Azure Active Directory 的属性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)" 中找到你的 AAD 租户 ID。

***授权***-添加要向其授予对托管资源组的权限的用户、组或应用程序的 AZURE ACTIVE DIRECTORY 对象 ID。 按用户的主体 ID 标识用户，可在[Azure 门户上的 Azure Active Directory 用户 "边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到该用户。

对于每个主体，从列表中选择一个 Azure AD 内置角色（所有者或参与者）。 你选择的角色将描述主体将对客户订阅中的资源拥有的权限。 有关详细信息，请参阅 [Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。  有关基于角色的访问控制（RBAC）的详细信息，请参阅[Azure 门户中的 RBAC 入门](https://docs.microsoft.com/azure/role-based-access-control/overview)。

>[!Note]
>尽管最多可为每个云添加100个授权，但通常可以更轻松地创建 Active Directory 的用户组，并在 "主体 ID" 中指定其 ID。  这将允许你在部署计划后向管理组中添加更多用户，并减少仅更新计划以添加更多授权的需求。

### <a name="policy-settings"></a>策略设置

将[Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview)应用到托管应用程序，以指定所部署解决方案的符合性要求。  有关策略定义和参数值的格式，请参阅 [Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/index)。  最多可以配置五个策略，每个策略选项只有一个实例。  某些策略需要其他参数。  审核策略需选择标准 SKU。  策略名称限制为50个字符。

## <a name="co-sell"></a>共同销售

在发布产品/服务时，提供有关 "做好" 选项卡的信息是完全可选的。 需要实现合作销售就绪和 IP 共同销售就绪状态。 Microsoft 销售团队将使用您提供的信息来了解有关您的解决方案的详细信息。 不会直接向客户提供此功能。

有关完成此选项卡的详细信息，请参阅[合作伙伴中心的共同销售选项](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)。

## <a name="test-drive"></a>体验版

使用 "**测试驱动器**" 选项卡，你可以设置一个演示（或 "测试驱动器"），使客户能够在提交之前试用你的产品/服务。 有关详细信息，请参阅[什么是测试驱动器？](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  如果你不再希望为你的产品/服务提供一个测试驱动器，请返回 "**产品/服务设置**" 页，然后取消选中 "**启用测试驱动器**"。

### <a name="technical-configuration"></a>技术配置

Azure 应用程序原本使用 Azure 资源管理器测试驱动器类型。  有关详细信息，请参阅[Azure 资源管理器测试驱动器的技术配置](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)。

### <a name="deployment-subscription-details"></a>部署订阅详细信息

若要代表你部署测试驱动器，请创建并提供一个单独的唯一 Azure 订阅。 （对于 Power BI 测试驱动器不是必需的）。

- **Azure 订阅 id** （azure 资源管理器和逻辑应用需要）：输入订阅 id 可授予对 azure 帐户服务的访问权限，以供资源使用情况报告和计费。 如果还没有 Azure 订阅，请考虑[创建一个单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)用于测试驱动器。 你可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 "**订阅**" 选项卡来查找你的 Azure 订阅 ID。 选择该选项卡将显示你的订阅 ID （例如 "a83645ac-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租户 id** （必需）：输入 AZURE ACTIVE DIRECTORY （AD）[租户 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "属性"，然后查找列出的**目录 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用域名 URL 查找你组织的租户 ID，网址为： [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。

- **Azure AD 租户名称**（动态365需要）：输入 AZURE ACTIVE DIRECTORY （AD）名称。 若要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，你的租户名称将在你的帐户名称下面列出。

- **Azure AD 应用 id** （必需）：输入 AZURE ACTIVE DIRECTORY （AD）[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**应用注册**"，然后查找列出的**应用程序 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 应用客户端机密**（必需）：输入 Azure AD 应用程序[客户端机密](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要查找此值，请登录到[Azure 门户](https://portal.azure.com/)。 选择左侧菜单中的 " **Azure Active Directory** " 选项卡，选择 "**应用注册**"，然后选择你的测试驱动器应用。 接下来，选择 "**证书和密码**"，选择 "**新建客户端密码**"，输入描述，选择 "**永不** **过期**"，然后选择 "**添加**"。 请确保复制值。 （在复制值之前，请勿导航离开页面，否则将无法访问此值。）

请记住在转到下一节之前**保存**。

### <a name="test-drive-listings-optional"></a>测试驱动器列表（可选）

"**测试驱动器**" 选项卡下的 "**测试驱动器列表**" 选项显示你的测试驱动器可用的语言（和市场），当前只有英语（美国）是可用的位置。 此外，此页还显示特定于语言的列表的状态以及添加它的日期/时间。 需要为每种语言/市场定义测试驱动器详细信息（说明、用户手册、视频等）。

- **说明**（必需）：描述您的测试驱动器、要演示的内容、要浏览的功能，以及帮助用户确定是否获取您的产品/服务的相关信息。 最多可在此字段中输入3000个字符的文本。 

- **访问信息**（对于 Azure 资源管理器和逻辑测试驱动器是必需的）：说明需要了解哪些客户才能访问和使用此试用版。 逐步完成使用产品/服务的方案，并确切了解客户在整个测试过程中访问功能应知道的情况。 最多可在此字段中输入10000个字符的文本。

- **用户手册**（必需）：深入演练测试驱动器体验。 用户手册应确切地说明您希望客户从遇到该测试驱动器中获得的内容，并为他们可能遇到的任何问题提供参考。 文件必须采用 PDF 格式，并在上传后命名为（最多255个字符）。

- **视频：添加视频**（可选）：可以将视频上传到 YouTube 或 Vimeo，并使用链接和缩略图（533 x 324 像素）在此处引用视频，以便客户可以查看信息，以帮助他们更好地了解测试驱动器，包括如何成功使用产品/服务的功能并了解突出显示其优势的方案。
  - **名称**（必需）
  - **URL （仅限 YouTube 或 Vimeo）** （必需）
  - **缩略图（533 x 324 px）** ：图像文件必须采用 PNG 格式。

完成这些字段后，选择 "**保存**"。

## <a name="publish"></a>发布

### <a name="submit-offer-to-preview"></a>将产品/服务提交到预览版

完成产品/服务的所有必需部分后，请选择门户右上角的 "**发布**"。 你将被重定向到 "**查看并发布**" 页面。 

如果你是首次发布此产品/服务，则可以：

- 请参阅产品/服务的每个部分的完成状态。
    - *未开始*-表示该部分尚未接触，需要完成。
    - *不完整*-表示部分包含需要修复的错误或需要提供更多信息。 请返回到部分并对其进行更新。
    - *Complete* -表示该部分已完成，所有必需的数据都已提供并且没有错误。 产品/服务的所有部分必须处于完整状态，然后才能提交产品/服务。
- 向认证团队提供测试说明，以确保正确测试您的应用程序，以及任何有助于了解应用程序的补充说明。
- 通过选择 "**提交**" 提交产品/服务进行发布。 我们将向你发送一封电子邮件，告知你该产品/服务的预览版本何时可供你查看和批准。 返回到 "合作伙伴中心"，并选择 "产品 **/** 服务"，以将你的产品/服务发布到公共（如果是专用受众）。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的**手动验证**步骤代表了对你的产品/服务及其关联技术资产（特别是 Azure 资源管理器模板）的全面审查，问题通常显示为拉取请求（PR）链接。 有关如何查看和响应这些 PR 的说明，请参阅[处理评审反馈](./azure-apps-review-feedback.md)。

如果在一个或多个发布步骤中遇到错误，则必须更正错误并重新发布产品/服务。

## <a name="next-steps"></a>后续步骤

- [在商业市场中更新现有的套餐](./update-existing-offer.md)
