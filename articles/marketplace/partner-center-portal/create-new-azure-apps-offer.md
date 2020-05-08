---
title: 创建 Azure 应用程序产品/服务-Microsoft 商业应用商店
description: 了解在合作伙伴中心的商业应用商店门户中创建新的 Azure 应用程序产品/服务的步骤和注意事项。 可以通过云解决方案提供商（CSP）计划在 Azure Marketplace 中列出或销售 Azure 应用程序产品/服务。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 22d6c37b59488633394d7f3ed5ca5b0c78371e7d
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790681"
---
# <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

本文介绍了在商业应用商店中创建新的 Azure 应用程序产品/服务的步骤和注意事项。 在创建新的 Azure 应用程序产品/服务之前，你应该熟悉这些概念。 

在发布新的 Azure 应用程序产品/服务之前，请[在合作伙伴中心创建一个商用 marketplace 帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)，并确保你的帐户已注册到商业市场计划中。

## <a name="before-you-begin"></a>开始之前

设计、构建和测试 Azure 应用程序产品/服务需要了解 Azure 平台和用于构建产品/服务的技术知识。 工程团队应该了解以下 Microsoft 技术：

* 基本了解[Azure 服务](https://azure.microsoft.com/services/)。
* 如何[设计和构建 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)。
* [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和[azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的工作知识。
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的工作知识。
* [JSON](https://www.json.org/)的工作知识。

### <a name="technical-documentation-and-resources"></a>技术文档和资源

为商业市场准备 Azure 应用程序产品/服务时，请查看以下资源。

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

视频[构建解决方案模板和 Azure Marketplace 的托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)提供了 azure 应用程序产品/服务类型的全面介绍：

* 提供哪些可用类型;
* 需要哪些技术资产;
* 如何创作 Azure 资源管理器模板;
* 开发和测试应用程序 UI;
* 如何发布应用程序产品/服务
* 应用程序审核过程。

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

可以在 " [Azure 开发人员工具](https://azure.microsoft.com/tools/)" 页中查看可用的工具。 此外，如果使用的是 Visual Studio，则[Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="types-of-azure-application-plans"></a>Azure 应用程序计划的类型

有两种类型的 Azure 应用程序计划：解决方案模板和托管应用程序。

* **解决方案模板**是在 Marketplace 中发布解决方案的主要方法之一。 如果你的解决方案需要在单个虚拟机（VM）之外额外部署和配置自动化，请使用此计划类型。 使用解决方案模板，可以自动提供多个资源，包括 Vm、网络和存储资源，以提供复杂的 IaaS 解决方案。  有关生成解决方案模板的详细信息，请参阅[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。

* **托管应用程序**类似于解决方案模板，但有一个重要区别。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。 使用托管应用程序轻松构建完全托管的全包式应用程序并将其交付给客户。  有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](https://docs.microsoft.com/azure/managed-applications/overview)。

## <a name="technical-requirements"></a>技术要求

所有 Azure 应用程序在`.zip`存档的根文件夹中至少包含两个文件：

* 资源管理器名为[maintemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的模板文件。  此模板定义要部署到客户的 Azure 订阅中的资源。  有关资源管理器模板的示例，请参阅[Azure 快速入门模板库](https://azure.microsoft.com/resources/templates/)或相应的[GitHub： Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。

* 名为[createUiDefinition](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)的 Azure 应用程序创建体验的用户界面定义。  在用户界面中，可以指定让使用者能够提供参数值的元素。

所有新的 Azure 应用程序产品/服务必须包括[Azure 合作伙伴客户使用归属 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)。 

若要了解有关发布每个应用程序计划的要求，请参阅[解决方案模板提供发布要求](../marketplace-solution-templates.md)和[托管应用程序提供发布要求](../marketplace-managed-apps.md)。

## <a name="create-a-new-offer"></a>创建新套餐

>[!NOTE]
>发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑将不会显示在商店中，直到重新发布该产品/服务。 进行更改后，请务必重新发布产品/服务。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左侧菜单中，选择 "**商业市场** > **概述**"。

1. 在 "概述" 页上，选择 " **+ 新产品/服务** > **Azure 应用程序**"。

    ![阐释左侧导航菜单。](./media/new-offer-azure-app.png)

1. 在 "**新建产品/服务**" 页上，输入**产品 ID**。 这是你的帐户中每个产品/服务的唯一标识符。

     * 适用于 marketplace 产品/服务和 Azure 资源管理器模板的 web 地址中的客户可看到此 ID （如果适用）。
     * 只使用小写字母和数字。 它可以包括连字符和下划线，但不能包含空格，并且限制为50个字符。 例如，如果输入了 "**测试/服务-1**"，则 "产品/服务`https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`" 网址为。
     * 选择 "**创建**" 后，无法更改产品/服务 ID。

1. 输入**提议别名**。 这是在合作伙伴中心中用于产品/服务的名称。

     * 此名称不能在 marketplace 中使用，它与向客户显示的产品/服务名称和其他值不同。
     * 选择 "**创建**" 后，不能更改该产品/服务别名。

1. 选择 "**创建**" 以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

在 "**产品/服务设置**" 页中，你可以为你的产品/服务配置测试驱动器和潜在客户管理。 

### <a name="test-drive"></a>体验版

通过向客户提供 "在购买前试用" 的选项，测试驱动器是向潜在客户展示你的产品/服务的一种很好的方法，导致增加转换和产生高度合格的潜在顾客。 [了解有关测试驱动器的详细信息](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)。

若要在固定时间段内启用测试驱动器，请选中 "**启用测试驱动器**" 复选框。 若要从产品/服务中删除测试驱动器，请清除此复选框。 请在本主题后面的 "[测试驱动器技术配置](#test-drive-technical-configuration)" 部分中配置测试驱动器环境。

有关其他信息，请参阅[在商业应用商店中试用产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。 你还可以阅读有关[测试驱动器最佳实践](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)的信息，并下载[测试驱动器概述 PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) （确保弹出窗口阻止程序处于关闭状态）

>[!Note]
>由于所有 Azure 应用程序都是使用 Azure 资源管理器模板来实现的，因此 Azure 应用程序唯一可用的测试驱动器类型是[基于 Azure 资源管理器的测试驱动器](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

## <a name="lead-management"></a>潜在顾客管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在客户管理概述](./commercial-marketplace-get-customer-leads.md)。

选择 "**保存草稿**"，然后继续。

## <a name="properties"></a>属性

在 "**属性**" 页上，可以定义用于在 marketplace 上对产品/服务进行分组的类别和行业、应用版本以及支持产品/服务的法律合同。

选择至少一个和三个类别，将产品/服务置于适当的 marketplace 搜索区域。 请确保在产品/服务描述中说明产品/服务如何支持这些类别。

### <a name="legal"></a>合法

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>产品/服务列表

在此页中可以管理商业 marketplace 产品/服务的副本和图像。 

### <a name="marketplace-details"></a>Marketplace 详细信息

> [!NOTE]
> 产品/服务列表内容（如描述、文档、屏幕截图和使用条款）不要求使用英语，只要产品/服务说明以短语开头，"此应用程序仅在 [非英语语言] 中可用"。 还可以提供一个*有用的链接 URL*来提供与产品/服务列表内容中所用语言不同的内容。

#### <a name="name"></a>名称

你在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预填充了你在创建产品/服务时为 "**产品/服务别名**" 输入的文本，但你可以更改此值。 此名称可能是商标字（并且你可以包括商标或版权符号）。 名称不能超过50个字符，并且不能包含任何表情符号。

#### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明（最多100个字符），可以在搜索结果中使用。

#### <a name="long-summary"></a>长摘要

提供产品/服务的更长说明（最多256个字符）。 此说明可用于搜索结果中。

#### <a name="description"></a>说明

提供产品/服务的更长说明（最多3000个字符）。 此说明将在列表概述中向客户显示。 包括产品/服务的价值主张、主要优势、类别和/或行业关联、应用内购买机会，以及任何所需的公开。

SHere 是编写说明的一些提示：

* 在前几句描述中清晰描述产品/服务的价值主张。 包括以下项：
  * 产品/服务的说明。
  * 受益于你的产品/服务的用户类型。
  * 产品/服务的客户需要或问题。
* 请记住，前几个句子可能会显示在搜索引擎结果中。
* 不要依赖特性和功能来出售产品/服务。 而是专注于你的产品/服务提供的价值。
* 使用行业特定的或基于收益的字词。
* 请考虑使用 HTML 标记来设置说明的格式，以使其更具吸引力。

#### <a name="search-keywords"></a>搜索关键字

你可以根据需要输入最多三个搜索关键字，帮助客户在 marketplace 中找到你的产品/服务。 为了获得最佳结果，请在说明中使用这些关键字。

#### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 URL。 你需要负责确保你的应用符合隐私法律和法规，并提供有效的隐私策略。

### <a name="useful-links"></a>有用链接

通过选择 " **+ 添加链接**"，将有关您的解决方案的可选补充联机文档的链接添加到其中。

### <a name="contact-information"></a>联系信息

提供**支持联系人**、**工程联系人**和**CSP 节目联系人**的姓名、电子邮件和电话号码。 此信息不会向客户显示，但会提供给 Microsoft，并可提供给 CSP 合作伙伴。 某些联系人可能需要其他信息。

### <a name="marketplace-media"></a>Marketplace 媒体

提供要用于产品/服务的徽标和图像。 所有图像必须为 PNG 格式。 模糊图像将导致提交遭到拒绝。

>[!Note]
>如果上传文件时遇到问题，请确保你的本地网络不会https://upload.xboxlive.com阻止合作伙伴中心使用的服务。

#### <a name="store-logos"></a>应用商店徽标

提供以下三个像素大小的产品/服务徽标的 PNG 文件：

- **小型**（48 x 48）
- **中**（90 x 90）
- **大**（216 x 216）
- **宽**（255 x 115）

所有这三种徽标都是必需的，并在列表中的不同位置使用。

#### <a name="screenshots"></a>屏幕截图

最多可添加五个屏幕截图，其中显示了产品/服务的工作方式。 每个屏幕截图的大小必须为 1280 x 720 像素，格式为 PNG。 每个屏幕截图都必须包含一个标题。

#### <a name="videos"></a>视频

添加最多5个视频来演示产品/服务。 这些应托管在外部视频服务上。 输入每个视频的名称、web 地址以及以 1280 x 720 像素为单位的视频缩略图 PNG 图像。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 列出资源

- [Marketplace 产品/服务列表的最佳实践](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

选择 "**保存草稿**"，然后继续。

## <a name="preview-audience"></a>预览受众

在将产品/服务发布到更广泛的 marketplace 受众之前，在 "预览" 选项卡上，选择用于验证产品/服务的受限**预览版观众**。

> [!IMPORTANT]
> 在预览版中检查产品/服务后，**请选择 "上线"** 将产品/服务发布到商业市场公共受众。

预览受众由 Azure 订阅 ID Guid 标识，以及每个用户的可选说明。 这些字段均不能由客户查看。 你可以在 "**订阅**" 页上的 Azure 门户中找到你的 AZURE 订阅 ID。

至少添加一个 Azure 订阅 ID （最多10个）或上传一个 CSV 文件（最大为100）。 通过添加这些订阅 Id，你可以定义在 live 发布之前可以预览你的产品/服务的人员。 如果你的产品/服务已处于活动阶段，你仍可以定义一个预览版观众来测试产品/服务的更改或更新。

> [!NOTE]
> 预览受众不同于专用用户。 预览观众可以在将其发布到市场_之前_，访问你的产品/服务。 他们可以查看和验证所有计划，包括那些在将产品/服务完全发布到 marketplace 后仅对专用用户可用的计划。 专用用户（在 "计划**定价和可用性**" 选项卡中定义）具有特定计划的独占访问权限。

选择 "**保存草稿**"，然后继续。

## <a name="technical-configuration"></a>技术配置

仅当你的产品/服务包括一个托管应用程序，该应用程序将使用 Marketplace 计量服务 API 发出计量事件时，才能完成本部分。 输入发出计量事件时服务将使用的**Azure Active Directory 租户 id**和**AZURE ACTIVE DIRECTORY 应用程序 id** 。

选择 "**保存草稿**"，然后继续。

## <a name="technical-configuration-offer-level"></a>技术配置（产品/服务级别）

>[!Note]
>产品/服务的技术详细信息是可选的。  仅当使用按流量计费发布托管应用程序，并且该服务将使用 Azure AD 安全令牌进行身份验证时，才需要配置这些详细信息。 有关详细信息，请参阅不同身份验证选项的[身份验证策略](./marketplace-metering-service-authentication.md)。

技术配置定义用于标识服务的详细信息（租户 ID 和应用 ID），这将使用[Marketplace 计量服务 api](./marketplace-metering-service-apis.md)为托管应用程序发出计量事件。  输入发出计量事件时服务将使用的标识。

* **Azure AD 租户 ID** （必需）：在 Azure 门户内部，我们要求你[创建 Azure Active Directory （AD）应用](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)，以便我们可以验证两个服务之间的连接是否在经过身份验证的通信之后。 若要查找[租户 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，请跳到 Azure Active Directory，选择 "**属性**"，然后查找列出的**目录 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。
* **Azure AD 应用 id** （必需）：还需要[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)和身份验证密钥。 若要获取这些值，请转到 Azure Active Directory 并选择**应用注册**，然后查找列出的**应用程序 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。 若要查找身份验证密钥，请单击 "**设置**" 并选择 "**密钥**"。 你将需要提供描述和持续时间，然后将提供一个数值。

>[!Note]
>Azure 应用程序 ID 将与你的发布者 ID 相关联，并且只能在此发布服务器帐户中重新使用。

>[!Note]
>如果要使用[Batch 用量事件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#batch-usage-event)，则需要此配置。  如果要提交[使用情况事件](https://docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis#usage-event)，还可以使用[实例元数据服务](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)获取[JSON web 令牌（JWT）持有者令牌](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app)。

## <a name="plan-overview"></a>计划概述

利用此选项卡，你可以在同一个服务中提供不同的计划选项。 这些计划（在云合作伙伴门户中称为 Sku）在计划类型（解决方案模板和托管应用程序）、盈利或受众方面可能有所不同。  至少配置一个计划，以便在 marketplace 中列出你的产品/服务。

创建后，你将在此选项卡上看到计划名称、Id、计划类型、可用性（公共或私有）、当前发布状态和任何可用操作。

**计划概述**中的可用**操作**取决于计划的当前状态，并且可能包括：

* 如果计划状态为 "**草稿**–删除草稿"。
* 如果计划状态为 "**实时**" – "停止销售计划" 或 "同步专用受众"。

### <a name="create-new-plan"></a>创建新计划

***计划 id*** –为此产品/服务中的每个计划创建唯一的计划 id。 此 ID 将对产品 URL 中的客户可见。  仅使用小写字母、字母数字字符、短划线或下划线。 此计划 ID 最多允许50个字符。 选择 "创建" 后，不能修改此 ID。

***计划名称***-客户在确定要在产品/服务中选择的计划时，将看到此名称。 为此产品/服务中的每个计划创建一个唯一的产品/服务名称。 计划名称用于区分可能属于同一产品/服务（例如，产品/服务名称： Windows Server; 计划： Windows Server 2016，Windows Server 2019）的软件计划。

### <a name="plan-setup"></a>计划设置

使用此选项卡可以为计划的类型设置高级配置，无论它是否重复使用其他计划中的包以及计划应在哪些云中可用。 此选项卡上的答案将影响在同一计划的其他选项卡上显示的字段。

#### <a name="plan-type"></a>计划类型
选择产品/服务的计划类型。 **解决方案模板**计划完全由客户管理。 **托管应用程序**计划使发布者可以代表客户管理应用程序。 有关详细信息，请参阅[Azure 应用程序计划的类型](#types-of-azure-application-plans)。

#### <a name="re-use-technical-configuration"></a>重复使用技术配置

如果有多个相同类型的计划，并且这些包在它们之间完全相同，则可以选择**此计划重用其他计划中的包**。  当你选择此选项时，你将能够选择同一类型的其他计划之一，以便重复使用中的包。 

>[!Note]
>当你从其他计划中重新使用包时，将从此计划中消失整个技术配置选项卡。 其他计划中的技术配置详细信息（包括将来所做的任何更新）也将用于此计划。<br><br>此计划发布后无法更改此设置。

#### <a name="azure-regions-cloud"></a>Azure 区域（云）

你的计划必须至少在一个 Azure 区域中提供。

选择**Azure 全局**选项，使你的计划对所有具有商业市场集成的 Azure 全局区域中的客户可用。 有关详细信息，请参阅[地理可用性和货币支持](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)。

选择**Azure 政府**版选项，使你的计划在[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)版区域中可用。 此区域为美国联邦、州、地方或部落实体的客户以及有资格提供服务的合作伙伴提供控制访问权限。 作为发布者，你将负责任何符合性控制、安全措施和最佳实践。 Azure 政府版使用物理上隔离的数据中心和网络（仅限美国）。

在发布到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)版之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从[Microsoft Azure 政府试用](https://azure.microsoft.com/global-infrastructure/government/request/)版请求试用帐户。

>[!NOTE]
>计划发布并在特定 Azure 区域中可用后，将无法删除该区域。

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

### <a name="availability-solution-template-plans-only"></a>可用性（仅解决方案模板计划）

您可以使该计划对每个人都可见，只允许特定客户（一个专用受众），以及是否将该计划隐藏为仅供其他解决方案模板或托管应用程序使用。

#### <a name="plan-visibility"></a>计划可见性

你可以将每个计划配置为对每个用户可见，或者仅针对你选择的特定受众进行配置。 你可以使用 Azure 订阅 Id 分配此受限受众的成员身份。

选择 "**这是专用计划**"，使你的计划成为私有计划，并且仅对你选择的受限受众可见。 作为专用计划发布后，您可以更新受众或选择使该计划对所有人都可用。 一旦将某个计划发布为每个人都可见，它就必须对所有人都保持可见。不能将其重新配置为私有计划。

如果将工厂设为私有，请输入**Azure 订阅 id**及其说明。 每个用户都有权访问此私有计划。 使用 Azure 订阅 Id 分配访问权限，其中包含为分配的每个 Azure 订阅 ID 提供说明。 通过导入 CSV 文件，单独添加最多10个客户订阅 Id 或20000。 Azure 订阅 Id 表示为 Guid，字母必须为小写。

>[!Note]
>专用或受限制的受众不同于你在 "**预览**" 选项卡上定义的预览观众。预览版观众在 marketplace 发布_之前_，可以访问产品/服务。 尽管专用受众选项仅适用于特定计划，但预览受众可以查看所有计划（private）以用于验证目的。

#### <a name="hide-plan"></a>隐藏计划

如果你的解决方案模板仅适用于引用其他解决方案模板或托管应用程序时间接部署，请选中此复选框以发布你的解决方案模板，但会将其从客户直接对其进行搜索和浏览。

选择 "**保存草稿**"，然后继续。

### <a name="pricing-and-availability-managed-application-plans-only"></a>定价和可用性（仅限托管应用程序计划）

使用它可以配置此计划将在中可用的**市场**，解决方案管理的每个月的**定价**，以及在只有特定客户应查看它（一个专用受众）的情况下**计划可见性**。

选择 "**保存草稿**"，然后继续。

#### <a name="markets"></a>市场

每个计划都必须至少在一个市场中可用。 对于想要使此计划可用的任何市场位置，选中相应的复选框。 用于选择 "已汇款" 国家/地区的搜索框和按钮，其中包含 Microsoft 汇寄的销售和使用条款，其中包含你的帮助。

如果已将计划的价格设置为美国美元（USD）并添加另一个市场位置，则将根据当前汇率计算新市场的价格。 请始终在发布前查看每个市场的价格。 保存更改后，可以通过使用 "导出价格（.xlsx）" 链接来查看定价。

#### <a name="pricing"></a>定价

提供此计划的每月价格。  此价格是此解决方案部署的资源所产生的任何 Azure 基础结构或即用即付软件成本的补充。

除了每个月的价格，还可以使用[按流量计费](./azure-app-metered-billing.md)设置非标准单位的消耗价格。  如果需要，可以将每月价格设置为零，并以独占方式使用按流量计费。 

以 USD （USD = 美国美元）为单位的价格将在保存时使用当前汇率转换为所有选定市场的本地货币。 在发布之前通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单个市场中设置自定义价格，请修改并导入定价电子表格。 

>[!Note]
>必须先保存定价更改，才能导出定价数据。

发布之前请仔细查看你的价格，因为发布计划后可能会有一些更改。  

>[!Note]
>一旦发布了计划中市场的价格，以后就不能再对其进行更改。

#### <a name="plan-visibility"></a>计划可见性

你可以将每个计划配置为对每个用户可见，或者仅针对你选择的特定受众进行配置。 你可以使用 Azure 订阅 Id 分配此受限受众的成员身份。

选择 "**这是专用计划**"，使你的计划成为私有计划，并且仅对你选择的受限受众可见。 作为专用计划发布后，您可以更新受众或选择使该计划对所有人都可用。 一旦将某个计划发布为每个人都可见，它就必须对所有人都保持可见。不能将其重新配置为私有计划。

如果将工厂设为私有，请输入**Azure 订阅 id**及其说明。 每个用户都有权访问此私有计划。 使用 Azure 订阅 Id 分配访问权限，其中包含为分配的每个 Azure 订阅 ID 提供说明。 通过导入 CSV 文件，单独添加最多10个客户订阅 Id 或20000。 Azure 订阅 Id 表示为 Guid，字母必须为小写。

>[!Note]
>专用或受限制的受众不同于你在 "**预览**" 选项卡上定义的预览观众。预览版观众在 marketplace 发布_之前_，可以访问产品/服务。 尽管专用受众选项仅适用于特定计划，但预览受众可以查看所有计划（private）以用于验证目的。

### <a name="technical-configuration"></a>技术配置 

此选项卡允许你上载允许客户部署计划的部署包。

>[!Note]
>如果已将此计划配置为从 "**计划设置**" 选项卡上的其他计划中重新使用包，则不会显示此选项卡。

#### <a name="package-details"></a>包详细信息

此选项卡允许你编辑技术配置的草稿版本。

**版本**–分配技术配置的当前版本。  每次将更改发布到此页面时，请递增此版本。 版本必须采用格式`{integer}.{integer}.{integer}`。

**包文件**（.zip）–此包包含此计划所需的所有模板文件以及作为`.zip`文件打包的任何其他资源。

所有 Azure 应用程序计划包都必须在`.zip`存档的根文件夹中包括以下两个文件：

* 资源管理器名为[maintemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的模板文件。 此模板自动将资源部署到客户的 Azure 订阅中。  有关资源管理器模板的示例，请参阅[Azure 快速入门模板库](https://azure.microsoft.com/documentation/templates/)或相应的[GitHub： Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。
* 名为[createUiDefinition](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)的 Azure 应用程序创建体验的用户界面定义。

支持的最大文件大小为：

* 总压缩`.zip`存档大小为 1 Gb
* 对于`.zip`存档中的任何单个未压缩文件，最大为 1 Gb  

所有新的 Azure 应用程序产品/服务还必须包含[Azure 合作伙伴客户使用归属](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)GUID。

>[!Note]
>如果上传文件时遇到问题，请确保你的本地网络不会阻止https://upload.xboxlive.com合作伙伴中心使用的服务。

托管应用程序计划需要此选项卡上的其他信息。

#### <a name="previously-published-packages"></a>以前发布的包

利用 "**以前发布的包**" 子选项卡，你可以查看技术配置的所有已发布版本。

#### <a name="enable-just-in-time-jit-access"></a>启用实时（JIT）访问

选择此选项可为此计划启用实时（JIT）访问。  JIT 访问允许你请求对托管应用程序资源的提升访问权限，以便进行故障排除或维护。 你始终对资源具有只读访问权限，但在特定的时间段内，你可以具有更高的访问权限。  有关详细信息，请参阅[为 Azure 托管应用程序启用和请求实时访问](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)。  若要要求托管应用程序的使用者授予你的帐户永久访问权限，请将此选项保留为未选中状态。

>[!Note]
>请确保更新`createUiDefinition.json`文件，以便支持此功能。  

#### <a name="deployment-mode"></a>部署模式

选择是否在部署此计划时配置**完整**或**增量部署模式**： 

* 在**完整模式**下，客户重新部署应用程序将导致在中未定义资源的情况下删除托管资源组中的资源`mainTemplate.json`。 
* 在**增量模式**下，应用程序的重新部署会使现有资源保持不变。

若要详细了解部署模式，请参阅[Azure 资源管理器部署模式](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)。

#### <a name="notification-endpoint-url"></a>通知终结点 URL

指定一个 HTTPS Webhook 终结点，以接收有关此计划版本的托管应用程序实例上的所有 CRUD 操作的通知。

#### <a name="customize-allowed-customer-actions"></a>自定义允许的客户操作

选择此选项以指定客户可以对托管资源执行的操作以及默认可用的 "`*/read`" 操作。 

列出要使客户能够在此处执行的其他操作（用分号分隔）。  有关详细信息，请参阅[了解 Azure 资源的拒绝分配](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)。  有关可用操作，请参阅 [Azure 资源管理器资源提供程序操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)。 例如，若要允许使用者重启虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到允许的操作。

#### <a name="global-azure--azure-government-cloud"></a>全球 Azure/Azure 政府云

指示在每个受支持的云中对此托管应用程序的管理访问权限。 要向其授予对托管资源组的权限的用户、组或应用程序使用 Azure Active Directory （AAD）标识进行标识。

**Azure Active Directory 租户 id** -包含要向其授予权限的用户、组或应用程序的标识的 AAD 租户 id （也称为目录 ID）。 可以在 Azure 门户上的 " [Azure Active Directory 的属性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)" 中找到你的 AAD 租户 ID。

**授权**–添加要向其授予对托管资源组的权限的用户、组或应用程序的 AZURE ACTIVE DIRECTORY 对象 ID。 按用户的主体 ID 标识用户，可在[Azure 门户上的 Azure Active Directory 用户 "边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到该用户。

对于每个主体，从列表中选择一个 Azure AD 内置角色（所有者或参与者）。 你选择的角色将描述主体将对客户订阅中的资源拥有的权限。 有关详细信息，请参阅 [Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。 有关基于角色的访问控制（RBAC）的详细信息，请参阅[Azure 门户中的 RBAC 入门](https://docs.microsoft.com/azure/role-based-access-control/overview)。

>[!Note]
>尽管最多可为每个云添加100个授权，但通常可以更轻松地创建 Active Directory 的用户组，并在 "主体 ID" 中指定其 ID。 这将允许你在部署计划后向管理组中添加更多用户，并减少仅更新计划以添加更多授权的需求。

#### <a name="policy-settings"></a>策略设置

将[Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview)应用到托管应用程序，以指定所部署解决方案的符合性要求。 有关策略定义和参数值的格式，请参阅 [Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/index)。 最多可以配置五个策略，每个策略选项只有一个实例。 某些策略需要其他参数。 审核策略需选择标准 SKU。 策略名称限制为50个字符。

选择 "**保存草稿**"，然后继续。

## <a name="co-sell-with-microsoft"></a>与 Microsoft 联合销售

提供有关 "共同销售" 选项卡的信息完全是发布产品/服务的可选信息。 需要实现合作销售就绪和 IP 共同销售就绪状态。 Microsoft 销售团队将使用您提供的信息来了解有关您的解决方案的详细信息。 不会直接向客户提供此功能。

有关此选项卡的详细信息，请参阅[合作伙伴中心的共同销售选项](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)。

## <a name="resell-through-csps"></a>通过 Csp 转售

通过向[云解决方案提供商](https://azure.microsoft.com/offers/ms-azr-0145p/)（CSP）计划中的合作伙伴提供产品/服务来扩展产品/服务的覆盖范围。 这允许经销商向客户销售产品/服务并创建捆绑解决方案。

选择 "**保存草稿**"，然后继续。

## <a name="test-drive"></a>体验版

设置演示（测试驱动器），让客户在购买前试用产品/服务。 若要创建允许客户在固定时间段内试用你的产品/服务的演示环境，请参阅[在商业应用商店中试用你的产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)。

若要启用某个测试驱动器，请选中 "[产品/服务设置](#test-drive)" 选项卡上的 "**启用测试驱动器**" 复选框。若要从产品/服务中删除测试驱动器，请清除此复选框。

### <a name="test-drive-technical-configuration"></a>测试驱动器技术配置

- **Azure AD 应用 id** （必需）：输入 AZURE ACTIVE DIRECTORY （AD）[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**应用注册**"，然后查找列出的**应用程序 ID**号（例如50c464d3-4930-494c-963c-1e951d15360e）。

#### <a name="deployment-subscription-details"></a>部署订阅详细信息

若要允许以你的名义部署测试驱动器，请创建并提供单独的唯一 Azure 订阅（Power BI 测试驱动器不需要此订阅）。

* **Azure 订阅 id** （azure 资源管理器和逻辑应用需要）–输入订阅 id 可授予对 azure 帐户服务的访问权限，以便进行资源使用报告和计费。 如果还没有 Azure 订阅，请考虑[创建一个单独的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)用于测试驱动器。 你可以通过登录到[Azure 门户](https://portal.azure.com/)并导航到左侧菜单的 "**订阅**" 选项卡来查找你的 Azure 订阅 ID。 选择该选项卡将显示你的订阅 ID （例如 "a83645ac-5ab6-6789-1h234g764ghty"）。
* **Azure AD 租户 id** （必需）–输入 AZURE ACTIVE DIRECTORY （AD）[租户 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 "**属性**"，然后查找列出的**目录 ID**号（如50c464d3-4930-494c-963c-1e951d15360e）。 你还可以使用域名 URL 查找你组织的租户 ID，网址为： [https://www.whatismytenantid.com](https://www.whatismytenantid.com)。
* **Azure AD 租户名称**（动态365需要）–输入 AZURE ACTIVE DIRECTORY （AD）名称。 若要查找此名称，请登录到[Azure 门户](https://portal.azure.com/)，在右上角，你的租户名称将在你的帐户名称下面列出。
* **Azure AD 应用 id** （必需）–输入 AZURE ACTIVE DIRECTORY （AD）[应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 若要查找此 ID，请登录到[Azure 门户](https://portal.azure.com/)，选择左侧导航菜单中的 "Active Directory" 选项卡，选择 "**应用注册**"，然后查找列出的**应用程序 ID**号（如50c464d3-4930-494c-963c-1e951d15360e）。
* **Azure Active Directory 应用程序客户端机密**（必需）–输入 Azure AD 应用程序[客户端机密](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 若要查找此值，请登录到[Azure 门户](https://portal.azure.com/)。 选择左侧导航菜单中的 " **Azure Active Directory** " 选项卡，选择 "**应用注册**"，然后选择 "测试驱动器" 应用。 接下来，选择 "**证书和密码**"，选择 "**新建客户端密码**"，输入描述，选择 "**永不****过期**"，然后选择 "**添加**"。 请确保在离开此页之前复制值。）

选择 "**保存草稿**"，然后继续。

### <a name="marketplace-listing-optional"></a>Marketplace 列表（可选）

描述测试驱动器体验。

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **说明**（必需）–介绍你的测试驱动器、将演示的内容、要浏览的功能，以及可帮助用户确定是否获取产品/服务的任何相关信息。 最多可在此字段中输入3000个字符的文本。 
* **访问信息**（对于 Azure 资源管理器和逻辑测试驱动器是必需的）–说明客户需要知道哪些内容才能访问和使用此测试驱动器。 逐步完成使用产品/服务的方案，并确切了解客户在整个测试过程中访问功能应知道的情况。 最多可在此字段中输入10000个字符的文本。
* **用户手册**（必需）-深入了解你的测试驱动器体验。 用户手册应确切地说明您希望客户从遇到该测试驱动器中获得的内容，并为他们可能遇到的任何问题提供参考。 文件必须采用 PDF 格式，并在上传后命名为（最多255个字符）。
* **视频：添加视频**（可选）–可以将视频上传到 YouTube 或 Vimeo，并使用链接和缩略图（533 x 324 像素）在此处引用视频，以便客户可以查看信息，以帮助他们更好地了解测试驱动器，包括如何成功使用产品/服务的功能并了解突出显示其优势的方案。
  * **名称**（必需）
  * **Address** （仅限 YouTube 或 Vimeo; 必需）
  * **缩略图**（图像文件必须采用 PNG 格式，533 x 324 px）。

选择 "**保存草稿**"，然后继续。

## <a name="publish"></a>发布

完成产品/服务的所有必需部分后，请选择门户右上角的 "**查看并发布**"。

查看产品/服务的每个部分的完成状态。
    - *未开始*-表示该部分尚未接触，需要完成。
    - *不完整*-表示部分包含需要修复的错误或需要提供更多信息。 返回到部分并对其进行更新。
    - *Complete* -表示该部分已完成，所有必需的数据都已提供并且没有错误。 产品/服务的所有部分必须处于完整状态，然后才能提交产品/服务。

如果这是你首次发布此产品/服务，则可以向认证团队提供测试说明，以确保正确测试你的应用程序，以及任何有助于了解应用的补充说明。

选择 "**提交**" 以提交你的产品/服务进行发布。 我们将向你发送一封电子邮件，告知你该产品/服务的预览版本何时可供你查看和批准。

返回到 "合作伙伴中心"，并选择 "产品 **/** 服务"，以将你的产品/服务发布到公共（如果是专用受众）。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的**手动验证**步骤代表了对你的产品/服务及其关联技术资产（特别是 Azure 资源管理器模板）的全面审查，问题通常显示为拉取请求（PR）链接。 有关如何查看和响应这些 PR 的说明，请参阅[处理评审反馈](./azure-apps-review-feedback.md)。

如果一个或多个发布步骤出错，请在重新发布产品/服务之前更正这些错误。

## <a name="next-steps"></a>后续步骤

* [在商业市场中更新现有的套餐](./update-existing-offer.md)
