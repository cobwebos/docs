---
title: 创建 Azure 应用程序产品/服务 - Microsoft 商业市场
description: 了解在合作伙伴中心的商业市场门户中创建新 Azure 应用程序产品/服务的步骤和注意事项。 可以通过云解决方案提供商 (CSP) 计划在 Azure 市场中列出或销售 Azure 应用程序产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: e7e7845b458d6d1a37b65fb79e0e7e9c13b0d07d
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646938"
---
# <a name="create-an-azure-application-offer"></a>创建 Azure 应用程序产品/服务

本文说明在商业市场中创建新 Azure 应用程序产品/服务的步骤和注意事项。 创建新 Azure 应用程序产品/服务之前，你应熟悉这些概念。

需要首先[在合作伙伴中心创建商业市场帐户](create-account.md)，并确保你的帐户已在商业市场计划中注册，然后才能发布新 Azure 应用程序产品/服务。

## <a name="before-you-begin"></a>开始之前

设计、生成和测试 Azure 应用程序产品/服务需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。 工程团队应该了解以下 Microsoft 技术：

* 基本了解 [Azure 服务](https://azure.microsoft.com/services/)。
* 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)。
* [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的实践知识。
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识。
* [JSON](https://www.json.org/) 的实践知识。

### <a name="technical-documentation-and-resources"></a>技术文档和资源

为商业市场准备 Azure 应用程序产品/服务时，请查看以下资源。

* [了解 Azure 资源管理器模板](../../azure-resource-manager/resource-group-authoring-templates.md)

* 快速入门：

    * [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates/)
    * [Azure 模板最佳实践指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [发布应用程序定义](../../managed-applications/publish-service-catalog-app.md)
    * [部署服务目录应用](../../managed-applications/deploy-service-catalog-quickstart.md)

* 教程：

    * [创建定义文件](../../managed-applications/publish-service-catalog-app.md)
    * [发布市场应用程序](../../managed-applications/publish-marketplace-app.md)

* 示例：

    * [Azure CLI](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [托管应用程序解决方案](../../managed-applications/sample-projects.md)

视频[为 Azure 市场生成解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)全面介绍了 Azure 应用程序产品/服务类型：

* 可用的产品/服务类型
* 需要哪些技术资产
* 如何创作 Azure 资源管理器模板
* 开发和测试应用程序 UI
* 如何发布应用程序产品/服务
* 应用程序审核过程

### <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理 Azure 应用程序：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

我们建议将以下工具添加到开发环境：

* [Azure 存储浏览器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

可以查看 [Azure 开发人员工具](https://azure.microsoft.com/tools/)页中的可用工具。 此外，如果使用的是 Visual Studio，请查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="types-of-azure-application-plans"></a>Azure 应用程序计划的类型

有两种类型的 Azure 应用程序计划：解决方案模板和托管应用程序。

* 解决方案模板是在市场中发布解决方案的主要方式之一。 当解决方案需要在单个虚拟机 (VM) 以外实现附加的部署和配置自动化时，可使用此计划类型。 使用解决方案模板时，可以自动提供多个资源（包括 VM、网络和存储资源），以提供复杂的 IaaS 解决方案。  有关生成解决方案模板的详细信息，请参阅 [Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)。

* 托管应用程序类似于解决方案模板，但有一个重大差异。 在托管应用程序中，资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 发布者指定解决方案的持续支持费用。 使用托管应用程序可轻松生成完全托管的统包应用程序并交付给客户。  有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](../../managed-applications/overview.md)。

## <a name="technical-requirements"></a>技术要求

所有 Azure 应用程序都在 `.zip` 存档的根文件夹中至少包含两个文件：

* 资源管理器模板文件，名为 [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md)。  此模板定义要部署到客户的 Azure 订阅中的资源。 有关资源管理器模板的示例，请参阅 [Azure 快速入门模板库](https://azure.microsoft.com/resources/templates/)或对应的 [GitHub：Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。

* 用于 Azure 应用程序创建体验的用户界面定义，名为 [createUiDefinition.json](../../managed-applications/create-uidefinition-overview.md)。  在用户界面中，可以指定让使用者能够提供参数值的元素。

所有新 Azure 应用程序产品/服务都必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](../azure-partner-customer-usage-attribution.md)。 

若要了解每个应用程序计划的发布要求，请参阅[解决方案模板产品/服务发布要求](../marketplace-solution-templates.md)和[托管应用程序产品/服务发布要求](../marketplace-managed-apps.md)。

## <a name="create-a-new-offer"></a>创建新套餐

>[!NOTE]
>发布产品/服务后，在 "合作伙伴中心" 中对其进行的编辑将不会显示在 "联机" 商店中，直到您重新发布该产品/服务。 确保在进行更改后始终重新发布产品/服务。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左侧菜单中，选择“商业市场” > “概述”。

1. 在“概述”页上，选择“+ 新建产品/服务” > “Azure 应用程序”。

    ![阐释左侧导航菜单。](./media/new-offer-azure-app.png)

1. 在“新建产品/服务”页上，输入“产品/服务 ID”。 这是你的帐户中每个产品/服务的唯一标识符。

     * 客户可以在市场产品/服务和 Azure 资源管理器模板的 Web 地址中看到此 ID（如果适用）。
     * 只使用小写字母和数字。 其中可以包含连字符和下划线，但不能包含空格，并且不得超过 50 个字符。 例如，如果输入“test-offer-1”，则产品/服务 Web 地址将为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
     * 选择“创建”后，无法更改产品/服务 ID。

1. 输入“产品/服务别名”。 这是用于合作伙伴中心的产品/服务的名称。

     * 此名称未用于市场，并且与向客户显示的产品/服务名称和其他值不同。
     * 选择“创建”后，无法更改产品/服务别名。

1. 选择“创建”以生成产品/服务并继续。

## <a name="offer-setup"></a>产品/服务设置

在“产品/服务设置”页上可以为你的产品/服务配置体验版和潜在顾客管理。

### <a name="test-drive"></a>体验版

体验版是一种向潜在客户展示你的产品/服务的好方法，它为潜在客户提供“在购买前试用”这一选择，从而提高了转化率并开发出优质的潜在顾客。 [详细了解体验版](../what-is-test-drive.md)。

若要在固定时间段内启用体验版，请选中“启用体验版”复选框。 若要从产品/服务中删除体验版，请清除此复选框。 在本主题后面的[体验版技术配置](#test-drive-technical-configuration)部分中配置体验版环境。

有关其他信息，请参阅[在商业市场中为你的产品/服务提供体验版](test-drive.md)。 还可以阅读[体验版最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)并下载[体验版概述 PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)（确保弹出窗口阻止程序处于关闭状态）

>[!Note]
>由于所有 Azure 应用程序都使用 Azure 资源管理器模板来实现，因此，唯一可用于 Azure 应用程序的体验版类型是[基于 Azure 资源管理器的体验版](../azure-resource-manager-test-drive.md)。

### <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

有关详细信息，请参阅[潜在顾客管理概述](./commercial-marketplace-get-customer-leads.md)。

选择“保存草稿”，然后继续。

## <a name="properties"></a>属性

在 " **属性** " 页上，可以定义用于对应用商店、应用版本以及支持产品/服务的法律合同进行分组的类别。

### <a name="category"></a>类别

选择类别和子类别，将产品/服务置于适当的 marketplace 搜索区域。 确保在产品/服务说明中介绍产品/服务如何支持这些类别。 选择：

- 至少一个和多达两个类别，包括主类别和辅助类别 (可选) 。
- 每个主节点和/或辅助类别最多包含两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择 " **不适用**"。

请参阅 [产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。

### <a name="legal"></a>合法

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>产品/服务列表

在此页中可管理商业市场产品/服务的副本和图像。

### <a name="marketplace-details"></a>市场详细信息

> [!NOTE]
> 只要产品/服务说明的开头是短语“此应用程序仅以 [非英语语言] 推出”，产品/服务内容（如说明、文档、屏幕截图和使用条款）便不必是英语。 还可以提供一个有用链接 URL，使用与产品/服务列表内容中所使用的语言不同的语言提供内容。

下面是一个示例，说明如何在 Azure Marketplace 中显示产品/服务 (任何列出的价格仅用于举例目的，而不用于反映实际成本) ：

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="说明此产品/服务在 Azure Marketplace 中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. 徽标
2. 类别
3. 支持地址 (链接) 
4. 使用条款
5. 隐私策略地址 (链接) 
6. 产品名称
7. 总结
8. 说明
9. 屏幕截图/视频

<br>下面是有关如何在 Azure 门户中显示产品/服务的示例：

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="说明了此产品/服务在 Azure 门户中的显示方式。":::

#### <a name="call-out-descriptions"></a>拨出说明

1. Title
2. 说明
3. 有用的链接
4. 屏幕截图

#### <a name="name"></a>名称

在此处输入的名称将作为产品/服务列表的标题向客户显示。 此字段预先填充了创建产品/服务时为“产品/服务别名”输入的文本，但可更改此值。 此名称可以是商标字（可以包括商标或版权符号）。 此名称长度不能超过 50 个字符，并且不能包含任何表情符号。

#### <a name="search-results-summary"></a>搜索结果摘要

提供产品/服务的简短说明（最多 100 个字符）。 此说明可在搜索结果中使用。

#### <a name="long-summary"></a>长摘要

提供产品/服务的较长说明（最多 256 个字符）。 此说明可在搜索结果中使用。

#### <a name="description"></a>说明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>搜索关键字

可以选择输入最多三个搜索关键字，以帮助客户在市场中查找你的产品/服务。 为了获得最佳结果，请在说明中也使用这些关键字。

#### <a name="privacy-policy-link"></a>隐私策略链接

输入组织的隐私策略的 URL。 你负责确保你的应用符合隐私法律和法规，并负责提供有效的隐私策略。

### <a name="useful-links"></a>有用链接

通过选择“+ 添加链接”，添加有关你解决方案的可选补充联机文档的链接。

### <a name="contact-information"></a>联系信息

提供支持联系人、工程联系人和 CSP 计划联系人的姓名、电子邮件和电话号码。 此消息不会向客户使用，但是可供 Microsoft 使用，并且可提供给 CSP 合作伙伴。 某些联系人可能需要其他信息。

### <a name="marketplace-media"></a>市场媒体

提供要用于产品/服务的徽标和图像。 所有图像都必须为 PNG 格式。 模糊图像会导致提交遭到拒绝。

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>如果上传文件时遇到问题，请确保本地网络未阻止合作伙伴中心所使用的 https://upload.xboxlive.com 服务。

#### <a name="store-logos"></a>Microsoft Store 徽标

提供 **大** 徽标徽标的 PNG 文件。 合作伙伴中心将使用此来创建 **小型** 和 **中型** 徽标。 稍后可以选择将它们替换为不同的图像。

- **大型** (从 216 x 216 到 350 x 350 px，必需) 
- **中型** (90 x 90 px，可选) 
- **小写** (48 x 48 px，可选) 

在列表中的不同位置使用这些徽标：

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>屏幕截图

最多添加五个显示了产品/服务的工作方式的屏幕截图。 每个屏幕截图大小必须为 1280 x 720 像素并采用 PNG 格式。 每个屏幕截图都必须包含一个标题。

#### <a name="videos"></a>视频

最多添加五个演示产品/服务的视频。 这些应在外部视频服务上进行托管。 输入每个视频的名称、Web 地址和视频的缩略图 PNG 图像（为 1280 x 720 像素）。

#### <a name="additional-marketplace-listing-resources"></a>其他市场列表资源

- [市场产品/服务列表最佳做法](../gtm-offer-listing-best-practices.md)

选择“保存草稿”，然后继续。

## <a name="preview-audience"></a>预览受众

在“预览”选项卡上，可以先选择有限的“预览受众”来验证产品/服务，然后再向更广泛的市场受众发布产品/服务。

> [!IMPORTANT]
> 在预览中检查产品/服务后，选择“投入使用”以向商业市场公共受众发布产品/服务。

预览受众通过 Azure 订阅 ID GUID 以及各自的可选说明来标识。 客户无法查看所有这些字段。 在 Azure 门户的“订阅”页上，可找到 Azure 订阅 ID。

至少添加一个 Azure 订阅 ID，可以单独添加（最多 10 个）或通过上传 CSV 文件来添加（最多 100 个）。 通过添加这些订阅 ID，定义在发布产品/服务之前谁可以对其进行预览。 如果产品/服务已推出，则仍可以定义预览受众来测试产品/服务更改或是对产品/服务的更新。

> [!NOTE]
> 预览受众不同于专用受众。 预览受众可以在向市场发布产品/服务之前来访问它。 他们可以查看和验证所有计划，包括仅在将产品/服务完整发布到市场之后才向专用受众提供的计划。 专用受众（在计划“定价和可用性”选项卡中定义）具有对特定计划的独占访问权限。

选择“保存草稿”，然后继续。

## <a name="technical-configuration"></a>技术配置

仅当产品/服务包含将使用市场计量服务 API 发出计量事件的托管应用程序时，才需完成此部分。 输入在发出计量事件时服务将使用的“Azure Active Directory 租户 ID”和“Azure Active Directory 应用程序 ID”。

选择“保存草稿”，然后继续。

## <a name="technical-configuration-offer-level"></a>技术配置（产品/服务级别）

>[!Note]
>产品/服务级别技术详细信息是可选的。  仅当你发布按流量计费的托管应用程序，并且具有将使用 Azure AD 安全令牌进行身份验证的服务时，才需要配置这些详细信息。 有关详细信息，请参阅有关不同身份验证选项的[身份验证策略](./marketplace-metering-service-authentication.md)。

技术配置定义用于标识服务的详细信息（租户 ID 和应用 ID），服务将使用[市场计量服务 API](./marketplace-metering-service-apis.md) 为托管应用程序发出计量事件。  输入发出计量事件时服务将使用的标识。

* **Azure AD 租户 ID** (必需) ：在 Azure 门户内，你必须 [创建 Azure Active Directory (AD) 应用](../../active-directory/develop/howto-create-service-principal-portal.md) ，以便我们可以验证两个服务之间的连接是否在经过身份验证的通信之后。 若要查找 [租户 ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) ，请参阅 Azure Active Directory 并选择 " **属性**"，然后查找列出 (的 **目录 ID** 号，例如 50c464d3-4930-494c-963c-1e951d15360e) 。
* **Azure AD 应用 id** (必需) ：还需要 [应用程序 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) 和身份验证密钥。 若要获取这些值，请转到 Azure Active Directory 并选择“应用注册”，然后查找列出的“应用程序 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。 若要找到身份验证密钥，请转到“设置”并选择“密钥”。 你将需要提供说明和持续时间，然后系统将向你提供一个数值。

>[!Note]
>Azure 应用程序 ID 将与你的发布者 ID 相关联，只能在此发布者帐户中重复使用。

>[!Note]
>如果要使用) 的 [批处理使用情况事件](marketplace-metering-service-apis.md#metered-billing-batch-usage-event) ，则需要此配置。  如果要提交 [使用情况事件](marketplace-metering-service-apis.md#metered-billing-single-usage-event)) ，还可以使用 [实例元数据服务](../../active-directory/managed-identities-azure-resources/overview.md) 获取 [JSON web 令牌 (JWT) 持有者令牌](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)) 。

## <a name="plan-overview"></a>计划概述

使用此选项卡可以在相同产品/服务内提供不同的计划选项。 这些计划 (以前称为 Sku) 不同于计划类型 (解决方案模板与托管应用程序) 、盈利或受众不同。 至少配置一个计划，以便在市场中列出你的产品/服务。

最多可为每个产品创建100个计划：最多可为45个。 详细了解 [Microsoft 商业应用商店](../private-offers.md)中的专用产品/服务。

创建后，你将在此选项卡上看到计划名称、ID、计划类型、可用性（公共或专用）、当前发布状态和任何可用操作。

“计划概述”中的可用“操作”因计划的当前状态而有所不同，可能包括：

* 如果计划状态为“草稿”– 删除草稿。
* 如果计划状态为“活动”– 停止销售计划或同步专用受众。

### <a name="create-new-plan"></a>创建新计划

计划 ID – 为此产品/服务中的每个计划创建唯一的计划 ID。 客户可以在产品 URL 中看到此 ID。  仅使用小写的字母数字字符、短划线或下划线。 此计划 ID 最多允许 50 个字符。 选择创建之后，便无法删除此 ID。

***计划名称*** –客户在确定要在产品/服务中选择的计划时，将看到此名称。 为此产品/服务中的每个计划创建唯一的产品/服务名称。 计划名称用于区分可能属于同一产品/服务的软件计划（例如，产品/服务名称：Windows Server；计划：Windows Server 2016、Windows Server 2019）。

### <a name="plan-setup"></a>计划设置

使用此选项卡可以为计划的类型设置高级配置，无论它是否重复使用其他计划中的包以及计划应在哪些云中可用。 此选项卡上的答案会影响在其他选项卡上为相同计划显示的字段。

#### <a name="plan-type"></a>计划类型
选择产品/服务的计划类型。 “解决方案模板”计划完全由客户管理。 “托管应用程序”计划使发布者可以代表客户管理应用程序。 有关详细信息，请参阅 [Azure 应用程序计划的类型](#types-of-azure-application-plans)。

#### <a name="re-use-technical-configuration"></a>重复使用技术配置

如果有多个具有相同类型的计划，并且包在它们之间完全相同，则可以选择“此计划重复使用其他计划中的包”。  选择此选项时，你将能够选择具有相同类型的其他计划之一，以便此产品/服务重复使用其中的包。

>[!Note]
>重复使用其他计划中的包时，整个技术配置选项卡会从此计划中消失。 其他计划中的技术配置详细信息（包括将来进行的任何更新）也将用于此计划。<br><br>此计划发布之后便无法更改此设置。

#### <a name="azure-regions-cloud"></a>Azure 区域（云）

你的计划必须至少在一个 Azure 区域中提供。

选择“Azure 全球”选项可向具有商业市场集成的所有 Azure 全球区域中的客户提供你的计划。 有关详细信息，请参阅[地域可用性和货币支持](../marketplace-geo-availability-currencies.md)。

选择“Azure 政府”选项可在 [Azure 政府](../../azure-government/documentation-government-welcome.md)区域中提供你的计划。 此区域为来自美国联邦、州、地方或部落实体以及有资格为其提供服务的合作伙伴的客户提供受控访问权限。 作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。

在发布到 [Azure 政府](../../azure-government/documentation-government-manage-marketplace-partners.md)之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)中请求试用帐户。

>[!NOTE]
>在计划已发布并在特定 Azure 区域中可用后，无法删除该区域。

#### <a name="azure-government-certifications"></a>Azure 政府认证

仅当选择了“Azure 政府”时，此选项才可见。

Azure 政府服务处理受特定政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接仅对 Azure 政府客户可见。

选择“保存草稿”，然后继续。

### <a name="plan-listing"></a>计划列表

这是配置计划的列表详细信息的位置。 此选项卡显示可能在相同产品/服务中的计划之间不同的特定信息。

#### <a name="plan-name"></a>计划名称

此名称预填充了在创建计划时为其提供的名称。 此名称在市场中显示为此计划的标题，限制为 100 个字符。

#### <a name="plan-summary"></a>计划摘要

提供计划（而不是产品/服务）的简短摘要。 此摘要限制为 100 个字符。

#### <a name="plan-description"></a>计划说明

描述此软件计划的独特之处，以及产品/服务内的计划之间的差异。 不要描述产品/服务，仅描述计划。 计划说明最多可以包含 2,000 个字符。

选择“保存草稿”，然后继续。

### <a name="availability-solution-template-plans-only"></a>可用性（仅限解决方案模板计划）

可以使计划对每个人都可见、仅对特定客户（专用受众）可见，以及设置是否使计划隐藏以便仅供另一个解决方案模板或托管应用程序使用。

#### <a name="plan-visibility"></a>计划可见性

可以将每个计划配置为对每个人都可见，或者仅对所选择的特定受众可见。 可以使用 Azure 订阅 ID 在此受限受众中分配成员身份。

选择“这是专用计划”可使计划成为专用计划，仅对所选择的受限受众可见。 作为专用计划发布后，可以更新受众或选择使计划对每个人都可用。 将计划发布为对每个人都可见后，它便必须保持对每个人都可见；无法重新配置为专用计划。

如果将计划设为专用，请输入“Azure 订阅 ID”及其说明。 每个 ID 都是有权访问此专用计划的受众。 通过用于包含所分配的每个 Azure 订阅 ID 的说明的选项，使用 Azure 订阅 ID 分配访问权限。 可单独添加最多 10 个客户订阅 ID，或是通过导入 CSV 文件最多添加 20,000 个。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

>[!Note]
>专用或受限受众与在“预览”选项卡上定义的预览受众不同。预览受众可以在向市场发布产品/服务之前来访问它。 专用受众选项仅适用于特定计划，而预览受众可以查看所有计划（专用或非专用）以进行验证。

#### <a name="hide-plan"></a>隐藏计划

如果你的解决方案模板旨在仅当通过其他解决方案模板或托管应用程序引用时才间接部署，请选中此复选框以发布你的解决方案模板，但是会使它向客户隐藏，从而无法直接搜索和浏览它。

选择“保存草稿”，然后继续。

### <a name="pricing-and-availability-managed-application-plans-only"></a>定价和可用性（仅限托管应用程序计划）

使用它可配置将在其中提供此计划的“市场”、解决方案管理的每月“定价”以及只有特定客户应看到它（专用受众）时的“计划可用性”。

选择“保存草稿”，然后继续。

#### <a name="markets"></a>市场

每个计划都必须至少在一个市场中提供。 对于要在其中提供此计划的任何市场位置，选中相应的复选框。 包含了用于选择“税收减免”国家/地区（在其中 Microsoft 会代表你减免销售和使用税）的搜索框和按钮来提供帮助。

如果已采用美元 (USD) 为计划设置了价格并添加另一个市场位置，则系统将根据当前汇率计算用于新市场的价格。 请始终在发布之前查看用于每个市场的价格。 保存更改之后，可以使用“导出价格(.xlsx)”链接查看定价。

#### <a name="pricing"></a>定价

提供此计划的每月价格。  此价格不包括在此解决方案部署的资源所产生的任何 Azure 基础结构或即用即付软件成本中。

除了每月价格之外，还可以使用[按流量计费](./azure-app-metered-billing.md)为非标准单位的消耗设置价格。  如果需要，可以将每月价格设置为零，专门使用按流量计费来收费。

以 USD（USD = 美元）设置的价格在保存时会使用当前汇率转换为所有选定市场的本地货币。 在发布之前，通过导出定价电子表格并查看每个市场中的价格来验证这些价格。 如果要在单独市场中设置自定义价格，请修改并导入定价电子表格。

>[!Note]
>必须先保存定价更改，才能实现定价数据的导出。

发布之前请仔细查看价格，因为发布计划之后对可以更改的内容有一些限制。  

>[!Note]
>一旦发布了计划中的市场价格，以后便无法更改它。

#### <a name="plan-visibility"></a>计划可见性

可以将每个计划配置为对每个人都可见，或者仅对所选择的特定受众可见。 可以使用 Azure 订阅 ID 在此受限受众中分配成员身份。

选择“这是专用计划”可使计划成为专用计划，仅对所选择的受限受众可见。 作为专用计划发布后，可以更新受众或选择使计划对每个人都可用。 将计划发布为对每个人都可见后，它便必须保持对每个人都可见；无法重新配置为专用计划。

>[!Note]
>专用或受限受众与在“预览”选项卡上定义的预览受众不同。预览受众可以在向市场发布产品/服务之前来访问它。 专用受众选项仅适用于特定计划，而预览受众可以查看所有计划（专用或非专用）以进行验证。

如果将计划设为专用，请输入“Azure 订阅 ID”及其说明。 每个 ID 都是有权访问此专用计划的受众。 通过用于包含所分配的每个 Azure 订阅 ID 的说明的选项，使用 Azure 订阅 ID 分配访问权限。 可单独添加最多 10 个客户订阅 ID，或是通过导入 CSV 文件最多添加 20,000 个。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

>[!Note]
>通过云解决方案提供商计划的经销商 (CSP) 建立的 Azure 订阅不支持专用产品/服务。

### <a name="technical-configuration"></a>技术配置

通过此选项卡可以上传允许客户部署计划的部署包。

>[!Note]
>如果已在“计划设置”选项卡上将此计划配置为重新使用其他计划中的包，则此选项卡将不可见。

#### <a name="package-details"></a>包详细信息

此选项卡使你可以编辑技术配置的草稿版本。

版本 – 分配技术配置的当前版本。  每次将更改发布到此页时，都会递增此版本。 版本格式必须为 `{integer}.{integer}.{integer}`。

包文件 (.zip) – 此包中包含此计划所需的所有模板文件以及任何其他资源，打包为 `.zip` 文件。

所有 Azure 应用程序计划包都必须在 `.zip` 存档的根文件夹中包含以下两个文件：

* 资源管理器模板文件，名为 [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md)。 此模板自动将资源部署到客户 Azure 订阅中。  有关资源管理器模板的示例，请参阅 [Azure 快速入门模板库](https://azure.microsoft.com/documentation/templates/)或对应的 [GitHub：Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。
* 用于 Azure 应用程序创建体验的用户界面定义，名为 [createUiDefinition.json](../../azure-resource-manager/managed-application-createuidefinition-overview.md)。

支持的最大文件大小为：

* 压缩 `.zip` 存档总大小最大为 1 Gb
* `.zip` 存档中任何单个未压缩文件最大为 1 Gb  

所有新 Azure 应用程序产品/服务还必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](../azure-partner-customer-usage-attribution.md)。

>[!Note]
>如果上传文件时遇到问题，请确保本地网络不会阻止合作伙伴中心使用的 https://upload.xboxlive.com 服务。

托管应用程序计划在此选项卡上需要其他信息。

#### <a name="previously-published-packages"></a>以前发布的包

“以前发布的包”子选项卡使你可以查看技术配置的所有已发布版本。

#### <a name="enable-just-in-time-jit-access"></a>启用实时 (JIT) 访问

选择此选项可为此计划启用实时 (JIT) 访问。  通过 JIT 访问可以对托管应用程序的资源请求提升的访问权限，以便进行故障排除或维护。 你始终对资源拥有只读访问权限，但在特定时间段内，可以获得更高访问权限。  有关详细信息，请参阅[对 Azure 托管应用程序启用和请求实时访问](../../managed-applications/request-just-in-time-access.md)。  若要要求托管应用程序的使用者向你的帐户授予永久访问权限，请将此选项保留为未选中状态。

>[!Note]
>请确保更新 `createUiDefinition.json` 文件，以便支持此功能。  

#### <a name="deployment-mode"></a>部署模式

选择是否在部署此计划时配置“完整”或“增量部署模式”： 

* 在“完整模式”下，客户重新部署应用程序会导致删除托管资源组中未在 `mainTemplate.json` 中定义的资源。 
* 在“增量模式”下，重新部署应用程序会使现有资源保持不变。

若要详细了解部署模式，请参阅 [Azure 资源管理器部署模式](../../azure-resource-manager/deployment-modes.md)。

#### <a name="notification-endpoint-url"></a>通知终结点 URL

指定 HTTPS Webhook 终结点，以接收有关此计划版本的托管应用程序实例上的所有 CRUD 操作的通知。

#### <a name="customize-allowed-customer-actions"></a>自定义允许的客户操作

选择此选项可指定除了默认提供的“`*/read`”操作外，客户可以对托管资源执行的操作。

在此处列出要使客户可以执行的其他操作（用分号分隔）。  有关详细信息，请参阅[了解 Azure 资源的拒绝分配](../../role-based-access-control/deny-assignments.md)。 有关可用操作，请参阅 [Azure 资源管理器资源提供程序操作](../../role-based-access-control/resource-provider-operations.md)。 例如，若要允许使用者重启虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到允许的操作。

#### <a name="global-azure--azure-government-cloud"></a>全球 Azure/Azure 政府云

指示在每个受支持的云中应该对此托管应用程序拥有管理访问权限的人员。 要向其授予对托管资源组的权限的用户、组或应用程序使用 Azure Active Directory (AD) 标识进行标识。

**Azure Active Directory 租户 id** – AZURE AD 租户 id (也称为目录 ID) ，其中包含要向其授予权限的用户、组或应用程序的标识。 可以在 Azure 门户的 " [Azure Active Directory 的属性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)" 中找到 AZURE AD 租户 ID。

授权 – 添加要向其授予托管资源组权限的用户、组或应用程序的 Azure Active Directory 对象 ID。 通过其主体 ID 标识用户，该 ID 可在 [Azure 门户上的 Azure Active Directory 用户边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到。

对于每个主体，从列表中选择一个 Azure AD 内置角色（“所有者”或“参与者”）。 选择的角色将描述主体对客户订阅中的资源所拥有的权限。 有关详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。 有关基于角色的访问控制 (RBAC) 的详细信息，请参阅 [Azure 门户中的 RBAC 入门](../../role-based-access-control/overview.md)。

>[!Note]
>尽管最多可对每个云添加 100 个授权，但通常创建 Active Directory 的用户组并在“主体 ID”中指定其 ID 会更加轻松。 这会使你可以在部署计划之后向管理组添加更多用户，并减少只是为了添加更多授权而更新计划的需求。

#### <a name="policy-settings"></a>策略设置

向托管应用程序应用 [Azure 策略](../../governance/policy/overview.md)以便为所部署的解决方案指定符合性要求。 有关策略定义和参数值的格式，请参阅 [Azure Policy 示例](../../governance/policy/samples/index.md)。 最多可以配置五个策略，每个策略选项只有一个实例。 某些策略需要附加参数。 审核策略需选择标准 SKU。 策略名称限制为 50 个字符。

选择“保存草稿”，然后继续。

## <a name="co-sell-with-microsoft"></a>与 Microsoft 联合销售

对于发布产品/服务，在“联合销售”选项卡上提供信息完全是可选的。 需要实现联合销售就绪和 IP 联合销售就绪状态。 Microsoft 销售团队在评估你的解决方案是否满足客户需求时，将使用你提供的信息详细了解你的解决方案。 它不会直接提供给客户。

有关此选项卡的详细信息，请参阅[合作伙伴中心的联合销售选项](commercial-marketplace-co-sell.md)。

## <a name="resell-through-csps"></a>通过 CSP 转售

可通过向[云解决方案提供商](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP) 计划中的合作伙伴提供产品/服务来扩展其覆盖范围。 这会使经销商可以向其客户销售你的产品/服务并创建捆绑销售解决方案。

选择“保存草稿”，然后继续。

## <a name="test-drive"></a>体验版

设置演示（体验版），让客户可在购买之前试用你的产品/服务。 若要创建使客户可以在固定时间段内试用产品/服务的演示环境，请参阅[在商业市场中为你的产品/服务提供体验版](test-drive.md)。

若要启用体验版，请在[产品/服务设置](#test-drive)选项卡上选中“启用体验版”复选框。若要从产品/服务中删除体验版，请清除此复选框。

### <a name="test-drive-technical-configuration"></a>体验版技术配置

- **Azure AD 应用 ID** (必需) ：输入 Azure Active Directory () AD) [应用程序 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 。 若要查找此 ID，请登录到 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择 "Active Directory" 选项卡，选择 " **应用注册**"，然后查找 (的 **应用程序 ID** 号，如 50c464d3-4930-494c-963c-1e951d15360e) 。

#### <a name="deployment-subscription-details"></a>部署订阅详细信息

若要允许代表你部署体验版，请创建并提供单独的唯一 Azure 订阅（Power BI 体验版不需要）。

* Azure 订阅 ID（Azure 资源管理器和逻辑应用需要）– 输入订阅 ID 以授予对 Azure 帐户服务的访问权限，以便进行资源使用情况报告和计费。 建议考虑[创建单独的 Azure 订阅](../../billing/billing-create-subscription.md)以用于体验版（如果尚未创建）。 登录 [Azure 门户](https://portal.azure.com/)并导航到左侧菜单的“订阅”选项卡，即可找到 Azure 订阅 ID。 选择该选项卡将显示您的订阅 ID (如 "a83645ac-1234-5ab6-6789-1h234g764ghty" ) 。
* **Azure AD 租户 ID** (必需) –输入 AZURE ACTIVE DIRECTORY (AD) [租户 ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)) 。 若要找到此 ID，请登录 [Azure 门户](https://portal.azure.com/)，在左侧菜单中选择“Active Directory”选项卡，选择“属性”，然后查找列出的“目录 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。 你还可以在以下位置使用域名 URL 查找组织的租户 ID：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)。
* Azure AD 租户名称（Dynamic 365 需要）– 输入 Azure Active Directory (AD) 名称。 若要找到此名称，请登录 [Azure 门户](https://portal.azure.com/)，租户名称将在右上角的帐户名称下列出。
* **Azure AD 应用 ID** (必需) –输入 Azure Active Directory () AD) [应用程序 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 。 若要找到此 ID，请登录 [Azure 门户](https://portal.azure.com/)，在左侧导航菜单中选择“Active Directory”选项卡，选择“应用注册”，然后查找列出的“应用程序 ID”编号（例如 50c464d3-4930-494c-963c-1e951d15360e）。
* **Azure Active Directory 应用程序客户端机密** (必需) –输入 Azure AD 应用程序 [客户端密钥](../../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)) 。 若要找到此值，请登录 [Azure 门户](https://portal.azure.com/)。 在左侧导航菜单中选择“Azure Active Directory”选项卡，选择“应用注册”，然后选择体验版应用。 接下来，选择“证书和密码”，选择“新建客户端密码”，输入说明，在“过期”下选择“从不”，然后选择“添加”。 请确保在离开此页之前复制值。）

选择“保存草稿”，然后继续。

### <a name="marketplace-listing-optional"></a>市场列表（可选）

介绍体验版体验。

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* 说明（必需）– 介绍体验版、要演示的内容、用户要体验的目标、要浏览的功能，以及帮助用户确定是否要获取产品/服务的任何相关信息。 最多可在此字段中输入 3,000 个字符的文本。 
* 访问信息（Azure 资源管理器和逻辑体验版需要）– 说明客户需要了解哪些内容才能访问和使用此体验版。 演练使用产品/服务的方案，以及客户在整个体验版中访问功能时应了解的确切内容。 最多可在此字段中输入 10,000 个字符的文本。
* 用户手册（必需）– 体验版体验的深入演练。 用户手册应确切地涵盖希望客户通过体验体验版而获得的好处，并用作他们可能遇到的任何问题的参考。 该文件必须采用 PDF 格式，并在上传之后命名（最多 255 个字符）。
* 视频:添加视频（可选）– 可以将视频上传到 YouTube 或 Vimeo，并在此处使用链接和缩略图（533 x 324 像素）进行引用，以便客户可以查看信息演练，来帮助他们更好地了解体验版，包括如何成功使用产品/服务的功能并了解突出其优势的方案。
  * 名称（必需）
  * 地址（仅限 YouTube 或 Vimeo；必需）
  * **缩略图** (图像文件必须为 PNG 格式，533 x 324 像素) 。

选择“保存草稿”，然后继续操作。

## <a name="publish"></a>发布

完成产品/服务的所有必需部分后，选择门户右上角的“查看和发布”。

查看产品/服务的每个部分的完成状态。
    - *未启动* –表示该部分尚未接触，需要完成。
    - *不完整* –表示部分包含需要修复的错误或需要提供详细信息。 返回相应部分并进行更新。
    - *Complete* –表示部分已完成，所有必需的数据都已提供并且没有错误。 产品/服务的所有部分都必须处于完成状态，然后才能提交产品/服务。

如果这是你首次发布此产品/服务，则可以向认证团队提供测试说明以确保正确测试你的应用，以及任何有助于了解应用的补充说明。

选择“提交”以提交产品/服务进行发布。 我们将向你发送一封电子邮件，告知你产品/服务的预览版本可供评审和审批。

返回到合作伙伴中心，为产品/服务选择“投入使用”以将产品/服务发布给公众（或者，如果是专用产品/服务，则发布给专用受众）。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的“手动验证”步骤表示对你的产品/服务及其相关技术资产（尤其是 Azure 资源管理器模板）的广泛评审，所以问题通常呈现为拉取请求 (PR) 链接。 有关如何查看和响应这些 PR 的说明，请参阅[处理评审反馈](./azure-apps-review-feedback.md)。

如果在一个或多个发布步骤中遇到错误，请更正错误，然后重新发布产品/服务。

## <a name="next-steps"></a>后续步骤

* [在商业市场中更新现有的套餐](./update-existing-offer.md)
