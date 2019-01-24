---
title: 将 Azure 托管应用程序发布到 Azure 市场
description: 将 Azure 托管应用程序发布到 Azure 市场
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6e5bcd6d9923b4051d44d51ff1a2534bc5e02f41
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445654"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>将 Azure 托管应用程序发布到 Azure 市场 
========================================================

本文列出了将托管应用程序产品/服务发布到 Azure 市场所涉及的各个步骤。

<a name="pre-requisites-for-publishing-a-managed-application"></a>发布托管应用程序的先决条件 
---------------------------------------------------

为加入市场而需满足的先决条件

1.  技术方面

    -   [创作 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Azure 快速入门模板：

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   创建 UI 定义

        -   [创建用户界面定义文件](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  非技术方面（业务要求）

    -   贵公司（或其子公司）必须位于 Azure 市场支持的销售原产地国家/地区

    -   产品必须以兼容 Azure 市场支持的计费模型的方式获得许可

    -   你负责为客户提供技术支持：免费、付费或通过社区支持。

    -   你负责为自己的软件和任何第三方软件依赖项授予许可。

    -   提供的内容必须满足在 Azure 市场上和 Azure 管理门户中列出的产品/服务的标准

    -   必须同意 Azure 市场参与政策与发布者协议的条款

    -   你必须同意遵守使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。

<a name="how-to-create-a-new-azure-application-offer"></a>如何创建新的 Azure 应用程序产品/服务 
-------------------------------------------

一旦满足所有先决条件，即可开始创作托管应用程序产品/服务了。 在开始之前，请先快速浏览产品/服务和 SKU

**产品**

Azure 应用程序套餐对应于发布者提供的一类产品套餐。 如果要在 Azure 市场中发布新的解决方案/应用程序，则可以使用新的产品/服务。 产品/服务是 SKU 的集合。 每个产品/服务在 Azure 市场中以自有实体的方式显示。

**SKU**

SKU 是产品/服务可购买的最小单位。 在同一产品类（产品/服务）内，通过 SKU 能够区分支持的不同功能、产品/服务是托管的还是非托管的以及计费模型。

如果要支持自带许可 (BYOL)、即付即用 (PAYG) 等不同的计费模型，请添加多个 SKU。 

当每个 SKU 支持不同的功能集且定价方式不同时，请添加多个 SKU。

SKU 显示在 Azure 市场的父产品/服务下，同时在 azure.com 中以可购买的自有实体的方式显示。

1.  登录[云合作伙伴平台](http://cloudpartner.azure.com)。

2.  在左侧的导航栏中，单击“+ 新建产品/服务”并选择“Azure 应用程序”。\"\"\"\"

    ![新建产品/服务](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  新产品/服务的“编辑器”视图现在会打开，可以开始创作了。\"\"

4.  需要填写的窗体显示在“编辑器”视图中的左侧。\"\"\"\" 每个\"窗体\"都包含一组要填写的字段。必填字段标有红色的星号 (\*)。

    > 有 4 个用于创作托管应用程序的主要窗体

    -   产品/服务设置

    -   SKU

    -   市场

    -   支持

<a name="how-to-fill-out-the-offer-settings-form"></a>如何填写产品/服务设置表单 
---------------------------------------

产品/服务设置表单是用于指定产品/服务设置的基本表单。
不同的字段如下所述。

**产品/服务 ID**

发布者个人资料内产品/服务的唯一标识符。
此 ID 会显示在产品 URL、资源管理器模板和计费报表中。 只允许使用小写字母数字字符或短划线 (-)。 此 ID 不能以短划线结束，最多也不能超过 50 个字符。 此字段在套餐推出后处于锁定状态。

**发布者 ID**

通过此下拉列表，可以选择要在其下发布此产品/服务的发布者个人资料。 此字段在套餐推出后处于锁定状态。

**名称**

产品/服务的显示名称。 该名称将显示在 Azure 市场和 Azure 门户中。 该名称不能超过 50 个字符。 指导原则是在其中包含产品的可识别品牌名称。 除非销售需要，否则不要在此处包含公司名称。 如果要在自己的网站上销售此产品/服务，请确保名称正是在网站上显示的名称。

单击“保存”以保存进度。\"\" 下一步是为产品/服务添加 SKU。

<a name="how-to-create-skus"></a>如何创建 SKU 
------------------

单击“SKU”窗体。\"\" 在此处可以看见“添加 SKU”选项，单击此选项即可输入“SKU ID”。\"\"\"\"

![新建产品/服务 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"SKU ID\"是 SKU 在套餐内的唯一标识符。 此 ID 会显示在产品 URL、ARM 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。
此 ID 不能以短划线结束，最多也不能超过 50 个字符。 此字段在套餐推出后处于锁定状态。 在产品/服务内可以有多个 SKU。 每个计划发布的映像都需要一个 SKU。

添加 SKU 后，它会显示在“SKU”窗体内的 SKU 列表中。\"\" 单击 SKU 名称以获取该特定 SKU 的详细信息。 下面是某些字段的一些详细信息。

单击“新建 SKU”后，需要填写以下窗体：\"\"

![新建产品/服务 - 新建 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>如何填写“SKU 详细信息”部分 

标题 - 为此 SKU 提供一个标题。 这是在库中为此项显示的标题。

摘要 - 为此 SKU 提供简短摘要。 此文本会显示在标题的正下方。

说明 - 提供关于此 SKU 的详细说明。

SKU 类型 - 允许的值为“托管应用程序”和“解决方案模板”。\"\"\"\" 在本例中，将选择“托管应用程序”。\"\"

### <a name="how-to-fill-package-details-section"></a>如何填写“程序包详细信息”部分 

程序包部分包含需要填写的以下字段

![新建产品/服务 - 新建 SKU 程序包](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

当前版本 - 提供将上传的程序包的版本。
它应采用以下格式。

程序包文件 - 程序包包含压缩为 .zip 文件的以下文件。

applianceMainTemplate.json - 部署模板文件，用于部署解决方案/应用程序并创建其中定义的资源。 可以在此处找到有关如何创作部署模板文件的更多详细信息 - <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json - azure.com 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 可以在此处找到有关如何创建此文件的更多详细信息 - <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json - 仅包含 Microsoft.Solution/appliances 资源的模板文件。 需要注意的此资源的关键属性如下所述：

-   kind - 对于市场托管应用程序方案，值应该为 Marketplace\"\"\"\"
-   ManagedResourceGroupId：客户订阅中的资源组，将在其中部署 applianceMainTemplate.json 中定义的所有资源。\"\"\'
-   PublisherPackageId：唯一地标识程序包的字符串。\"\" 

值需要按以下方式构造 - 它是 \[publisherId\].\[OfferId\]-preview\[SKUID\].\[PackageVersion\] 的串联。
可以获取这些值中的每个值，如下所示。

此程序包应包含成功预配此应用程序所需的任何其他嵌套模板或脚本。 mainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json 必须位于根文件夹中。

Authorizations - 此属性定义哪些用户可以访问客户订阅中的资源以及所处的访问权限级别。 这使得发布者可以代表客户管理应用程序。

-   PrincipalId - 针对客户订阅中的资源被授予了特定权限（如角色定义所述）的用户、用户组或应用程序的 Azure Active Directory 标识符。

-   Role Definition - Azure AD 提供的所有内置 RBAC 角色的列表。 你可以选择最合适的角色，以便可以代表客户管理资源。

可以添加多个授权。 但是，建议创建一个 Active Directory 用户组并在 PrincipalId 中指定其 ID。\"\.  这样，可以向用户组中添加更多用户，而无需更新 SKU。

可以在此处找到有关 RBAC 的更多详细信息 - <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>市场表单
----------------

Azure 应用程序产品/服务中的市场窗体要求填写将在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)上显示的字段。 下面是某些字段的一些详细信息。

#### <a name="preview-subscription-ids"></a>预览订阅 ID

在此处输入在发布产品/服务后有权访问它们的 Azure 订阅 ID 的列表。 通过这些列入允许列表的订阅，可以在推出预览的套餐前先进行测试。 通过合作伙伴门户，可以将多达 100 个订阅列入允许列表。

#### <a name="suggested-categories"></a>建议的类别

从提供的列表中选择最多五个与产品/服务最相关的类别。 所选的类别用于将产品/服务映射到在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中提供的产品类别。

以下是一些在此表单上提供的数据的显示位置。

##### <a name="azure-marketplace"></a>Azure 市场

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>azure.com 门户

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>徽标准则

上传到云合作伙伴门户中的所有徽标都应遵循以下准则：

-   Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。

-   azure.com 门户的主题颜色为白色和黑色。 请避免将这两种颜色用作徽标的背景色。 使用一些可使徽标在 azure.com 门户中比较显眼的颜色。
    建议使用简单的主颜色。 如果使用的是透明背景，请确保徽标和文本不使用白色、黑色或蓝色。

-   不要在徽标上使用渐变背景。

-   请避免在徽标上放置文本，即使是公司或品牌名称也不可以。
    徽标的外观应“平整”，并且应避免渐变。\'\'

-   避免拉伸徽标。

##### <a name="hero-logo"></a>特大徽标

特大徽标是可选的。 发布者可以选择不上载特大徽标。 但是，上传后的特大图标将无法删除。 在这种情况下，合作伙伴必须遵循针对特大图标的 Azure 市场准则。

###### <a name="guidelines-for-the-hero-logo-icon"></a>特大徽标图标的准则

-   发布者显示名称、计划标题和套餐详细摘要以白色字体颜色显示。 请避免在特大图标的背景中保留任何浅色。 特大图标不允许使用黑色、白色和透明背景。

-   在推出产品/服务后，发布者显示名称、计划标题、产品/服务详细摘要和创建按钮会以编程方式嵌入到特大徽标内。 在设计特大徽标时，不需要输入任何文本。 在右侧留出空白区域，以放置发布者显示名称、计划标题、产品/服务详细摘要等。它们将以编程方式包含在内。
    右侧的文本空白区域应该为 415x100 像素，并从左侧偏移 370 像素。

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>支持表单
------------

要填写的下一个窗体是支持窗体。 此窗体要求填写贵公司的支持联系人。  例如，工程联系信息和客户支持联系信息。

<a name="how-to-publish-an-offer"></a>如何发布产品/服务
-----------------------

现在，已完成了产品/服务的开发阶段，下一步是将该产品/服务发布到 Azure 市场。 单击“发布”按钮开始产品/服务激活过程。\"\"
