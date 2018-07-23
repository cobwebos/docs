---
title: 市场中的 Azure 托管应用程序 | Microsoft 文档
description: 介绍通过市场提供的 Azure 托管应用程序。
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 07/10/2018
ms.author: tomfitz
ms.openlocfilehash: 8f35bda8c6925bdc10097ac6d180f5998bd5cf1d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989779"
---
# <a name="azure-managed-applications-in-the-marketplace"></a>市场中的 Azure 托管应用程序

供应商可以使用 Azure 托管应用程序向所有 Azure 市场客户提供其解决方案。 这些供应商可以包括托管服务提供商 (MSP)、独立软件供应商 (ISV) 和系统集成商 (SI)。 托管应用程序可减少客户的维护和服务开销。 供应商通过市场出售基础结构和软件。 他们可以将服务和操作支持附加到托管应用程序。 有关详细信息，请参阅[托管应用程序概述](overview.md)。

本文介绍如何将应用程序发布到市场并将其广泛提供给客户。

## <a name="prerequisites-for-publishing-a-managed-application"></a>发布托管应用程序的先决条件

若要完成本文中的操作，必须已有包含托管应用程序定义的 .zip 文件。 有关详细信息，请参阅[创建服务目录应用程序](publish-service-catalog-app.md)。

此外，还有多个业务先决条件。 它们是：

* 公司或其子公司必须位于市场支持销售的国家/地区。
* 产品必须按与市场支持的计费模型兼容的方式获得许可。
* 以商业上合理的方式向客户提供技术支持。 支持形式可以为免费、付费或社区支持。
* 针对你的软件和任何第三方软件依赖项授予许可。
* 提供的内容必须符合在市场上和 Azure 门户中上架产品/服务的标准。
* 同意 Azure 市场参与政策与发布者协议的条款。
* 同意遵守使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。

## <a name="become-a-publisher"></a>成为发布者

若要在 Azure 市场中成为一名发布者，必须：

1. 创建 Microsoft ID：使用属于公司的域（而不是属于个人）的电子邮件地址创建 Microsoft 帐户。 此电子邮件地址同时用于 Microsoft 开发人员中心和云合作伙伴门户。 有关详细信息，请参阅 [Azure 市场发布者指南](https://aka.ms/sellerguide)。
1. 提交 [Azure 市场提名表格](https://aka.ms/ampnomination)：对于“你要发布的解决方案?”，选择“托管应用程序”。 一旦提交表格，市场上架团队就会对应用程序进行评审并核实该申请。 审批过程可能需要一到三天。 提名获得批准后，你会收到一个促销代码，可免除开发人员中心注册费用。 如果不完成市场提名表格，你就需要支付 99 美元的注册费用。
1. 在[开发人员中心](http://dev.windows.com/registration?accountprogram=azure)内注册：Microsoft 会核实你的组织在进行注册的国家/地区是否为有效的合法实体以及是否具有有效的税号标识。 审批过程可能需要 5 到 10 天。 若要免除注册费用，请使用在提名过程中以电子邮件形式收到的促销代码。 有关详细信息，请参阅 [Azure 市场发布者指南](https://aka.ms/sellerguide)。
1. 登录到[云合作伙伴门户](https://cloudpartner.azure.com)：在发布者个人资料中，将开发人员中心帐户与市场发布者个人资料相关联。 有关详细信息，请参阅 [Azure 市场发布者指南](https://aka.ms/sellerguide)。

## <a name="create-a-new-azure-application-offer"></a>创建新的 Azure 应用程序产品/服务

创建合作伙伴门户帐户之后，就可以开始创建托管应用程序产品/服务。

### <a name="set-up-an-offer"></a>设置产品/服务

托管应用程序的产品/服务对应于发布者提供的一类产品/服务。 如果有想要在市场中推出的新类型应用程序，则可以将其设置为新的产品/服务。 产品/服务是 SKU 的集合。 每个产品/服务在市场中都显示为其自己的实体。

1. 登录[云合作伙伴门户](https://cloudpartner.azure.com/)。

1. 在左侧导航窗格中，选择“+ 新产品/服务” > “Azure 应用程序”。

1. 在“编辑器”视图中，查看所需的窗体。 将在本文中后面介绍每个窗体。

## <a name="offer-settings-form"></a>产品/服务设置窗体

“产品/服务设置”窗体的字段包括：

* 
  **
  **产品/服务 ID：此唯一标识符标识发布者配置文件内的产品/服务。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结尾。 不能超过 50 个字符。 此字段在套餐推出后处于锁定状态。
* 
  **
  **发布者 ID：使用此下拉列表，可以选择要在其下发布此产品/服务的发布者配置文件。 此字段在套餐推出后处于锁定状态。
* 
  **名称**：此产品/服务显示名称出现在市场和门户中。 该名称不能超过 50 个字符。 其中包含产品的可识别品牌名称。 除非销售需要，否则不要在此处包含公司名称。 如果要在自己的网站上销售此产品/服务，请确保名称与在网站上显示的名称完全相同。

完成后，选择“保存”保存进度。

## <a name="skus-form"></a>SKU 窗体

下一步是为产品/服务添加 SKU。

SKU 是可购买的产品/服务的最小单位。 你可以使用相同产品类（产品/服务） 中的 SKU 在两者之间进行区分：

* 支持的不同功能
* 产品/服务为托管的还是非托管的
* 支持的计费模型

SKU 在市场中显示在父级产品/服务下。 它在 Azure 门户中显示为其自己的可购买实体。

1. 选择“SKU” > “新建 SKU”。

1. 输入“SKU ID”。 SKU ID是 SKU 在套餐内的唯一标识符。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在套餐推出后处于锁定状态。 在产品/服务内可以有多个 SKU。 每个计划发布的映像都需要一个 SKU。

1. 填写下面表单上的“SKU 详细信息”部分：

   填写以下字段：

   * 标题：为此 SKU 输入一个标题。 此标题出现在该项的库中。
   * 摘要：为此 SKU 输入简短摘要。 此文本显示在标题下方。
   * 说明：输入关于此 SKU 的详细说明。
   * SKU 类型：允许的值为“托管应用程序”和“解决方案模板”。 对于本例，请选择“托管应用程序”。
   * **国家/区域可用性**：选择提供了该托管应用程序的国家/地区。
   * **定价**：提供用于应用程序管理的价格。 在设置价格之前，选择可用的国家/地区。

1. 添加新的程序包。 填写下面表单上的“程序包详细信息”部分：

   填写以下字段：

   * **版本**：输入你上传的程序包的版本。 它应采用格式 `{number}.{number}.{number}{number}`。
   * **程序包文件 (.zip)**：此程序包中包含压缩为 .zip 包的两个必需文件。 其中一个文件是资源管理器模板，用于定义要为托管应用程序部署的资源。 另一个文件定义[用户界面](create-uidefinition-overview.md)，以便使用者通过门户部署托管应用程序。 在用户界面中，可以指定让使用者能够提供参数值的元素。
   * **PrincipalId：** 此属性是针对客户订阅中的资源被授予了访问权限的用户、用户组或应用程序的 Azure Active Directory (Azure AD) 标识符。 Role Definition 描述权限。
   * Role Definition：此属性是 Azure AD 提供的所有基于角色的访问控制 (RBAC) 内置角色的列表。 你可以选择最合适的角色用于代表客户管理资源。
   * **策略设置**：向托管应用程序应用 [Azure 策略](../azure-policy/azure-policy-introduction.md)来指定所部署的解决方案的符合性要求。 从可用选项中，选择要应用的策略。 对于“策略参数”，请提供包含参数值的 JSON 字符串。 有关策略定义和参数值的格式，请参阅 [Azure 策略示例](../azure-policy/json-samples.md)。

可以添加多个授权。 我们建议你创建 AD 用户组，并在“PrincipalId”中指定其 ID。 以此，可以将更多的用户添加到用户组，而无需更新 SKU。

有关 RBAC 的详细信息，请参阅 [Azure 门户中的 RBAC 入门](../role-based-access-control/overview.md)。

## <a name="marketplace-form"></a>市场窗体

市场窗体要求填写将显示在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中的字段。

### <a name="preview-subscription-ids"></a>预览订阅 ID

输入 Azure 订阅 ID 列表，可以在发布后访问套餐。 通过这些列入允许列表的订阅，可以在推出套餐前测试预览的套餐。 你可以在合作伙伴门户中编译允许列表的最多 100 个订阅。

### <a name="suggested-categories"></a>建议的类别

从列表中选择最多五个与产品/服务最相关的类别。 这些类别用于将产品/服务映射到在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中提供的产品类别。

#### <a name="azure-marketplace"></a>Azure 市场

托管应用程序的摘要将显示以下字段：

![市场摘要](./media/publish-marketplace-app/publishvm10.png)

托管应用程序的“概述”选项卡将显示以下字段：

![市场概述](./media/publish-marketplace-app/publishvm11.png)

托管应用程序的“计划 + 定价”选项卡将显示以下字段：

![市场计划](./media/publish-marketplace-app/publishvm15.png)

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

特大徽标是可选的。 发布者可以选择不上传特大徽标。 上传特大图标后将无法删除。 这种情况下，合作伙伴必须遵循适用于特大图标的市场准则。

请遵循适用于特大徽标图标的准则：

*   以白色显示发布者显示名称、计划标题和套餐详细摘要。 因此，不要在特大图标的背景下使用浅色。 特大图标不允许使用黑色、白色或透明背景。
*   列出产品/服务后，会以编程方式在特大徽标内嵌入元素。 嵌入的元素包括发布者显示名称、计划标题、套餐长摘要和“创建”按钮。 因此，在设计特大徽标时，请勿输入任何文本。 在右侧留出空白区域，因为文本是以编程方式包含在该空白区域中的。 右侧的文本空白区域应为 415 x 100 像素。 它从左侧偏移了 370 个像素。

    ![特大徽标示例](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>支持窗体

使用公司中的支持联系人填写“支持”表单。 此信息可能是工程联系人和客户支持联系人。

## <a name="publish-an-offer"></a>发布产品/服务

填写所有部分后，选择“发布”，开始向客户提供产品/服务的过程。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](overview.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](publish-service-catalog-app.md)。