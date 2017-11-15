---
title: "Marketplace 中的 Azure 托管应用程序 | Microsoft 文档"
description: "介绍通过 Marketplace 提供的 Azure 托管应用程序。"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/08/2017
ms.author: gauravbh
ms.openlocfilehash: 41eaff13cf55d58f58b33bc05d39a6665d51ea29
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace 中的 Azure 托管应用程序

供应商可以使用 Azure 托管应用程序向所有 Azure Marketplace 客户提供其解决方案。 这些供应商可以包括托管服务提供商 (MSP)、独立软件供应商 (ISV) 和系统集成商 (SI)。 托管应用程序可减少客户的维护和服务开销。 供应商通过 Marketplace 出售基础结构和软件。 他们可以将服务和操作支持附加到托管应用程序。 有关详细信息，请参阅[托管应用程序概述](overview.md)。

本文介绍如何将应用程序发布到 Marketplace 并将其广泛提供给客户。

## <a name="prerequisites-for-publishing-a-managed-application"></a>发布托管应用程序的先决条件

若要完成本文中的操作，必须已有包含托管应用程序定义的 .zip 文件。 有关详细信息，请参阅[创建服务目录应用程序](publish-service-catalog-app.md)。

此外，还有多个业务先决条件。 它们是：

* 公司或其子公司必须位于 Marketplace 支持销售的国家/地区。
* 产品必须按与 Marketplace 支持的计费模型兼容的方式获得许可。
* 以商业上合理的方式向客户提供技术支持。 支持形式可以为免费、付费或社区支持。
* 针对你的软件和任何第三方软件依赖项授予许可。
* 提供的内容必须符合在 Marketplace 上和 Azure 门户中上架产品/服务的标准。
* 同意 Azure Marketplace 参与政策与发布者协议的条款。
* 同意遵守使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。

## <a name="set-up-your-account-for-publishing-portal"></a>为发布门户设置帐户

发布门户用于发布和管理产品/服务。 若要发布 Marketplace 应用程序，必须具有用于 Azure Marketplace 的一个已批准 Microsoft 开发人员帐户。 如果尚未注册已批准的帐户，请参阅[创建 Microsoft 开发人员帐户](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md)。

如果具有已批准的 **Microsoft 开发人员中心**帐户，但之前未使用过 [Azure 发布门户](https://cloudpartner.azure.com/)，则必须为发布门户进行注册。

1. 打开新的 Chrome Incognito 或 Internet Explorer InPrivate 浏览会话，以确保未登录到个人帐户。
2. 转到 [https://cloudpartner.azure.com/](https://cloudpartner.azure.com/)。
3. 如果是新用户并且首次登录到发布门户，则必须使用与开发人员中心帐户相同的电子邮件 ID 进行登录。 现在，开发人员中心帐户和发布门户帐户已链接在一起。

之后，可以作为发布门户中的[共同管理员](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md#4-steps-to-add-a-co-admin-in-the-publishing-portal)添加公司的其他成员。 如果已将你添加为发布门户中的共同管理员，则可以使用共同管理员帐户进行登录。

> [!TIP]
> [Azure 网站](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)上对这些参与政策进行了介绍。
>
>

## <a name="create-a-new-azure-application-offer"></a>创建新的 Azure 应用程序产品/服务

满足先决条件后，便可创建托管应用程序产品/服务。

### <a name="set-up-an-offer"></a>设置产品/服务

托管应用程序的产品/服务对应于发布者提供的一类产品/服务。 如果有想要在 Marketplace 中推出的新类型应用程序，则可以将其设置为新的产品/服务。 产品/服务是 SKU 的集合。 每个产品/服务在 Marketplace 中都显示为其自己的实体。

1. 登录[云合作伙伴门户](https://cloudpartner.azure.com/)。

1. 在左侧导航窗格中，选择“+ 新产品/服务” > “Azure 应用程序”。

   ![新产品/服务](./media/publish-marketplace-app/newOffer.png)

1. 在“编辑器”视图中，查看所需的窗体。 将在本文中后面介绍每个窗体。

   ![产品/服务设置](./media/publish-marketplace-app/newOffer_OfferSettings.png)

## <a name="offer-settings-form"></a>产品/服务设置窗体

“产品/服务设置”窗体的字段包括：

* 产品/服务 ID：此唯一标识符标识发布者配置文件内的产品/服务。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结尾。 不能超过 50 个字符。 此字段在产品/服务推出后处于锁定状态。
* 发布者 ID：使用此下拉列表，可以选择要在其下发布此产品/服务的发布者配置文件。 此字段在产品/服务推出后处于锁定状态。
* 名称：此产品/服务显示名称出现在 Marketplace 和在门户中。 该名称不能超过 50 个字符。 其中包含产品的可识别品牌名称。 除非销售需要，否则不要在此处包含公司名称。 如果要在自己的网站上销售此产品/服务，请确保名称与在网站上显示的名称完全相同。

完成后，选择“保存”保存进度。

## <a name="skus-form"></a>SKU 窗体

下一步是为产品/服务添加 SKU。

SKU 是可购买的产品/服务的最小单位。 你可以使用相同产品类（产品/服务） 中的 SKU 在两者之间进行区分：

* 支持的不同功能
* 产品/服务为托管的还是非托管的
* 支持的计费模型

SKU 在 Marketplace 中显示在父级产品/服务下。 它在 Azure 门户中显示为其自己的可购买实体。

1. 选择“SKU” > “新建 SKU”。

   ![选择“新建 SKU”](./media/publish-marketplace-app/newOffer_skus.png)

1. 输入“SKU ID”。 SKU ID是 SKU 在产品/服务内的唯一标识符。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在产品/服务推出后处于锁定状态。 在产品/服务内可以有多个 SKU。 每个计划发布的映像都需要一个 SKU。

1. 填写下面表单上的“SKU 详细信息”部分：

   ![提供新的 SKU](./media/publish-marketplace-app/sku-settings.png)

   填写以下字段：

   * 标题：为此 SKU 输入一个标题。 此标题出现在该项的库中。
   * 摘要：为此 SKU 输入简短摘要。 此文本显示在标题下方。
   * 说明：输入关于此 SKU 的详细说明。
   * SKU 类型：允许的值为“托管应用程序”和“解决方案模板”。 对于本例，请选择“托管应用程序”。
   * **国家/区域可用性**：选择提供了该托管应用程序的国家/地区。

      ![选择国家/地区](./media/publish-marketplace-app/select-country.png)

   * **定价**：提供用于应用程序管理的价格。 在设置价格之前，选择可用的国家/地区。

1. 添加新的程序包。 填写下面表单上的“程序包详细信息”部分：

   ![程序包](./media/publish-marketplace-app/new-package.png)

   填写以下字段：

   * 当前版本：输入你上传的程序包的版本。 它应采用格式 `{number}.{number}.{number}{number}`。
   * 选择程序包文件：此程序包中包含压缩为 .zip 包的两个必需文件。 其中一个文件是资源管理器模板，用于定义要为托管应用程序部署的资源。 另一个文件定义[用户界面](create-uidefinition-overview.md)，以便使用者通过门户部署托管应用程序。 在用户界面中，可以指定让使用者能够提供参数值的元素。
   * **PrincipalId：**此属性是针对客户订阅中的资源被授予了访问权限的用户、用户组或应用程序的 Azure Active Directory (Azure AD) 标识符。 Role Definition 描述权限。
   * Role Definition：此属性是 Azure AD 提供的所有基于角色的访问控制 (RBAC) 内置角色的列表。 你可以选择最合适的角色用于代表客户管理资源。

可以添加多个授权。 我们建议你创建 AD 用户组，并在“PrincipalId”中指定其 ID。 以此，可以将更多的用户添加到用户组，而无需更新 SKU。

有关 RBAC 的详细信息，请参阅 [Azure 门户中的 RBAC 入门](../active-directory/role-based-access-control-what-is.md)。

## <a name="marketplace-form"></a>Marketplace 窗体

Marketplace 窗体要求填写将显示在 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 门户](https://portal.azure.com/)中的字段。

### <a name="preview-subscription-ids"></a>预览订阅 ID

输入 Azure 订阅 ID 列表，可以在发布后访问产品/服务。 通过这些列入允许列表的订阅，可以在推出产品/服务前测试预览的产品/服务。 你可以在合作伙伴门户中编译允许列表的最多 100 个订阅。

### <a name="suggested-categories"></a>建议的类别

从列表中选择最多五个与产品/服务最相关的类别。 这些类别用于将产品/服务映射到在 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 门户](https://portal.azure.com/)中提供的产品类别。

#### <a name="azure-marketplace"></a>Azure 应用商店

托管应用程序的摘要将显示以下字段：

![Marketplace 摘要](./media/publish-marketplace-app/publishvm10.png)

托管应用程序的“概述”选项卡将显示以下字段：

![应用商店概述](./media/publish-marketplace-app/publishvm11.png)

托管应用程序的“计划 + 定价”选项卡将显示以下字段：

![Marketplace 计划](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure 门户

托管应用程序的摘要将显示以下字段：

![门户摘要](./media/publish-marketplace-app/publishvm12.png)

托管应用程序的概述将显示以下字段：

![门户概述](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>徽标准则

在云合作伙伴门户中上传任何徽标时，请遵循以下准则：

*   Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
*   门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。 如果你使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
*   不要在徽标上使用渐变背景。
*   不要在徽标上放置文本，即使是公司或品牌名称也不可以。 徽标的外观应平整，并且应避免渐变。
*   确保徽标未被拉伸。

#### <a name="hero-logo"></a>特大徽标

特大徽标是可选的。 发布者可以选择不上传特大徽标。 上传特大图标后将无法删除。 这种情况下，合作伙伴必须遵循适用于特大图标的 Marketplace 准则。

请遵循适用于特大徽标图标的准则：

*   以白色显示发布者显示名称、计划标题和产品/服务详细摘要。 因此，不要在特大图标的背景下使用浅色。 特大图标不允许使用黑色、白色或透明背景。
*   列出产品/服务后，会以编程方式在特大徽标内嵌入元素。 嵌入的元素包括发布者显示名称、计划标题、产品/服务长摘要和“创建”按钮。 因此，在设计特大徽标时，请勿输入任何文本。 在右侧留出空白区域，因为文本是以编程方式包含在该空白区域中的。 右侧的文本空白区域应为 415 x 100 像素。 它从左侧偏移了 370 个像素。

    ![特大徽标示例](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>支持窗体

使用公司中的支持联系人填写“支持”表单。 此信息可能是工程联系人和客户支持联系人。

## <a name="publish-an-offer"></a>发布产品/服务

填写所有部分后，选择“发布”，开始向客户提供产品/服务的过程。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](overview.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](publish-service-catalog-app.md)。
