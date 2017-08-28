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
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.contentlocale: zh-cn
ms.lasthandoff: 08/11/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace 中的 Azure 托管应用程序

 MSP、ISV 和系统集成商 (SI) 可以使用 Azure 托管应用程序向所有 Azure Marketplace 客户提供其解决方案。 这样的解决方案减少了针对客户的维护和服务开销。 发布者可以通过 Marketplace 出售基础结构和软件。 他们可以将服务和操作支持附加到托管应用程序。 有关详细信息，请参阅[托管应用程序概述](managed-application-overview.md)。

文本介绍了 MSP、ISV 或 SI 如何可以将其应用程序发布到 Marketplace 并将其广泛提供给客户。

## <a name="prerequisites-for-publishing-a-managed-application"></a>发布托管应用程序的先决条件

在 Marketplace 中列入名单的先决条件：

* 技术方面

    *  有关 Azure 资源管理器模板的基本结构和语法的信息，请参阅 [Azure 资源管理器模板](resource-group-authoring-templates.md)。
    *  若要查看完整的模板解决方案，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/en-us/documentation/templates/)或[快速入门模板存储库](https://github.com/azure/azure-quickstart-templates)。
    *  有关如何通过 Marketplace 为部署你应用程序的客户创建界面的信息，请参阅[创建用户界面定义文件](managed-application-createuidefinition-overview.md)。

* 非技术方面（业务要求）

    *   公司或其子公司必须位于 Marketplace 支持的销售国家/地区。
    *   产品必须按与应用商店支持的计费模型兼容的方式获得许可。
    *   你需要以商业上合理的方式负责向客户提供技术支持。 支持形式可以为免费、付费或社区支持。
    *   负责获得自己的软件和任何第三方软件依赖项的许可。
    *   提供的内容必须符合在 Marketplace 上和 Azure 门户中上架产品/服务的标准。
    *   必须同意 Azure Marketplace 参与政策与发布者协议的条款。
    *   必须同意遵守使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。

## <a name="create-a-new-azure-application-offer"></a>创建新的 Azure 应用程序产品/服务

满足先决条件后，便可创建托管应用程序产品/服务。 下面，让我们快速地大致了解一下产品/服务和 SKU。

### <a name="offer"></a>产品

托管应用程序的产品/服务对应于发布者提供的一类产品/服务。 如果你有想要在 Marketplace 中推出的新类型解决方案/应用程序，则可以将其设置为新的产品/服务。 产品/服务是 SKU 的集合。 每个产品/服务在 Marketplace 中都显示为其自己的实体。

### <a name="sku"></a>SKU

SKU 是可购买的产品/服务的最小单位。 你可以使用相同产品类（产品/服务） 中的 SKU 在两者之间进行区分：

* 支持的不同功能。
* 产品/服务为托管还是非托管。
* 支持的计费模型。

SKU 在 Marketplace 中显示在父级产品/服务下。 它在 Azure 门户中显示为其自己的可购买实体。

### <a name="set-up-an-offer"></a>设置产品/服务

1. 登录[云合作伙伴门户](https://cloudpartner.azure.com/)。

2. 在左侧导航窗格中，选择“+ 新产品/服务” > “Azure 应用程序”。

    ![新产品/服务](./media/managed-application-author-marketplace/newOffer.png)

3. 填写显示在“编辑器”视图左侧的表单。 必填字段标有红色的星号 (*)。

    ![产品/服务设置](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    四个主表单用于创建托管应用程序：

    a. 产品/服务设置

    b. SKU

    c. 应用商店

    d.单击“下一步”。 支持

下面各节中更详细地介绍了这些窗体。

## <a name="offer-settings-form"></a>产品/服务设置窗体
使用这种基本形式指定产品/服务设置。

1. 填写“产品/服务设置”表单。 各个字段如下：

    a. 产品/服务 ID：此唯一标识符标识发布者配置文件内的产品/服务。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结尾。 不能超过 50 个字符。 此字段在产品/服务推出后处于锁定状态。

    b. 发布者 ID：使用此下拉列表，可以选择要在其下发布此产品/服务的发布者配置文件。 此字段在产品/服务推出后处于锁定状态。

    c. 名称：此产品/服务显示名称出现在 Marketplace 和在门户中。 该名称不能超过 50 个字符。 其中包含产品的可识别品牌名称。 除非销售需要，否则不要在此处包含公司名称。 如果要在自己的网站上销售此产品/服务，请确保名称与在网站上显示的名称完全相同。

2. 选择“保存”来保存进度。 

## <a name="skus-form"></a>SKU 窗体
下一步是为产品/服务添加 SKU。

1. 选择“SKU” > “新建 SKU”。 

    ![选择“新建 SKU”](./media/managed-application-author-marketplace/newOffer_skus.png)

2. 输入“SKU ID”。 SKU ID是 SKU 在产品/服务内的唯一标识符。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在产品/服务推出后处于锁定状态。 在产品/服务内可以有多个 SKU。 每个计划发布的映像都需要一个 SKU。

3. 填写下面表单上的“SKU 详细信息”部分：

    ![提供新的 SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

    填写以下字段：
    
    a. 标题：为此 SKU 输入一个标题。 此标题出现在该项的库中。

    b. 摘要：为此 SKU 输入简短摘要。 此文本显示在标题下方。

    c. 说明：输入关于此 SKU 的详细说明。

    d.单击“下一步”。 SKU 类型：允许的值为“托管应用程序”和“解决方案模板”。 对于本例，请选择“托管应用程序”。

4. 填写下面表单上的“程序包详细信息”部分：

    ![程序包](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    填写以下字段：

    a. 当前版本：输入你上传的程序包的版本。 它应采用格式 `{number}.{number}.{number}{number}`。

    b. 选择程序包文件：此程序包包含以下压缩为 .zip 文件的文件：
    * applianceMainTemplate.json：用来部署解决方案/应用程序的部署模板文件。 有关如何创建部署模板文件的详细信息，请参阅[创建你的第一个 Azure 资源管理器模板](resource-manager-create-first-template.md)。
    * appliancecreateUIDefinition.json：Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
    * mainTemplate.json：此模板文件仅包含 Microsoft.Solution/appliances 资源。 mainTemplate 文件包括以下属性：

        *  kind：对于 Marketplace 中的托管应用程序，请使用 Marketplace。
        *  ManagedResourceGroupId：客户的订阅中在其中部署 applianceMainTemplate.json 中定义的所有资源的资源组。
        *  PublisherPackageId：此字符串唯一标识程序包。 请采用 `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` 格式提供值。

在发布门户中获取“产品/服务 ID”和“发布者 ID”，如下图所示：

![产品/服务 ID](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
获取“SKU ID”，如下图所示：

![SKU ID](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
获取程序包“版本”，如下图所示：

![程序包版本](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  基于前面的示例，PublisherPackageId 的值为 `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`。

  示例 mainTemplate.json：

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

此程序包应当包含成功预配此应用程序所需的任何其他嵌套模板或脚本。 mainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json 文件必须位于根文件夹中。

* Authorizations：此属性定义谁将针对客户订阅中的资源获得什么级别的访问权限。 发布者可以使用它来代表客户管理应用程序。
* PrincipalId：此属性是针对客户订阅中的资源被授予了特定权限的用户、用户组或应用程序的 Azure Active Directory (Azure AD) 标识符。 Role Definition 描述权限。 
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

![Marketplace 摘要](./media/managed-application-author-marketplace/publishvm10.png)

托管应用程序的“概述”选项卡将显示以下字段：

![应用商店概述](./media/managed-application-author-marketplace/publishvm11.png)

托管应用程序的“计划 + 定价”选项卡将显示以下字段：

![Marketplace 计划](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure 门户

托管应用程序的摘要将显示以下字段：

![门户摘要](./media/managed-application-author-marketplace/publishvm12.png)

托管应用程序的概述将显示以下字段：

![门户概述](./media/managed-application-author-marketplace/publishvm13.png)

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
*   在产品/服务推出后，发布者显示名称、计划标题、产品/服务详细摘要和“创建”按钮将会以编程方式嵌入在特大徽标内。 因此，在设计特大徽标时，请勿输入任何文本。 在右侧留出空白区域，因为文本是以编程方式包含在该空白区域中的。 右侧的文本空白区域应为 415 x 100 像素。 它从左侧偏移了 370 个像素。

    ![特大徽标示例](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>支持窗体

使用公司中的支持联系人填写“支持”表单。 此信息可能是工程联系人和客户支持联系人。

## <a name="publish-an-offer"></a>发布产品/服务

填写所有部分后，选择“发布”，开始向客户提供产品/服务的过程。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅[托管应用程序概述](managed-application-overview.md)。
* 有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
* 有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。

