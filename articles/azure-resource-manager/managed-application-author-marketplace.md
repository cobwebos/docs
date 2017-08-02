---
title: "Marketplace 中的 Azure 托管应用程序 | Microsoft Docs"
description: "介绍了通过 Marketplace 提供的 Azure 托管应用程序。"
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
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace 中的 Azure 托管应用程序

如[托管应用程序概述](managed-application-overview.md)一文中所讨论，Microsoft Azure Marketplace 中的托管应用程序使得 MSP、ISV 和系统集成商 (SI) 能够将其解决方案提供给所有 Azure 客户。 这样的解决方案减少了针对客户的维护和服务开销。 发布者可以通过 Marketplace 销售基础结构和软件，同时向其附加提供服务和操作支持。 

文本介绍了 MSP、ISV 或 SI 可以如何将其应用程序发布到 Marketplace 并将其广泛提供给客户。  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>发布托管应用程序的先决条件

在 Marketplace 中列入名单的先决条件

* 技术方面

    *  有关 Resource Manager 模板的基本结构和语法的信息，请参阅[创作 Azure Resource Manager 模板](resource-group-authoring-templates.md)。
    *  若要查看完整的模板解决方案，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/en-us/documentation/templates/)或[快速入门模板存储库](https://github.com/azure/azure-quickstart-templates)。
    *  有关为通过 Marketplace 部署你的应用程序的客户创建界面的信息，请参阅[创建用户界面定义文件](managed-application-createuidefinition-overview.md)。

* 非技术方面（业务要求）

    *   你的公司（或其子公司）必须位于 Marketplace 支持的销售国家/地区。
    *   产品/服务必须按与 Marketplace 支持的计费模型兼容的方式获得许可。
    *   你需要负责以商业上合理的方式向客户提供技术支持。 支持形式可以为免费、付费或社区支持。
    *   你负责获得自己的软件和任何第三方软件依赖项的许可。
    *   提供的内容必须满足在 Azure Marketplace 上和 Azure 门户中列入名单的产品/服务的标准
    *   必须同意 Azure Marketplace 参与政策与发布者协议的条款。
    *   必须同意遵守使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。

## <a name="how-to-create-a-new-azure-application-offer"></a>如何创建新的 Azure 应用程序产品/服务

在满足先决条件后，就可以开始创建托管应用程序产品/服务了。 下面，让我们快速地大致了解一下产品/服务和 SKU。

### <a name="offer"></a>产品/服务

托管应用程序的产品/服务对应于发布者提供的一类产品/服务。 如果你有想要在 Marketplace 中提供的新类型的解决方案/应用程序，可以将其设置为新的产品/服务。 产品/服务是 SKU 的集合。 每个产品/服务在 Marketplace 中都显示为其自己的实体。

### <a name="sku"></a>SKU

SKU 是可购买的产品/服务的最小单位。 在相同的产品类（产品/服务）内，通过 SKU 能够区分支持的不同功能、产品/服务是托管的还是非托管的，以及支持的计费模型。

SKU 在 Marketplace 中显示在父级产品/服务下。 它在 Azure 门户中显示为其自己的可购买实体。

### <a name="set-up-offer"></a>设置产品/服务

1.  登录到[云合作伙伴门户](https://cloudpartner.azure.com/)。
2.  在左侧的导航栏中，单击“+ 新建产品/服务”并选择“Azure 应用程序”。

    ![新建产品/服务](./media/managed-application-author-marketplace/newOffer.png)

3.  新建产品/服务“编辑器”视图现在会打开，可以开始创作了。

    ![产品/服务设置](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  需要填写的窗体显示在“编辑器”视图中的左侧。 每个窗体都包含一组要填写的字段。 必填字段标有红色的星号 (*)。

 **有四个用于创作托管应用程序的主要窗体**

    *   产品/服务设置
    *   SKU
    *   Marketplace
    *   支持

下面各节中更详细地介绍了这些窗体。

## <a name="offer-settings-form"></a>产品/服务设置窗体

产品/服务设置窗体是用于指定产品/服务设置的基本窗体。 各个字段如下：

* 产品/服务 ID - 此字段是产品/服务在发布者配置文件内的唯一标识符。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在产品/服务推出后处于锁定状态。
* 发布者 ID - 通过此下拉列表，可以选择要在其下发布此产品/服务的发布者配置文件。 此字段在产品/服务推出后处于锁定状态。
* 名称 - 此字段是产品/服务的显示名称。 它是将显示在 Marketplace 和门户中的名称。 该名称不能超过 50 个字符。 指导原则是在其中包含产品的可识别品牌名称。 除非销售需要，否则不要在此处包含公司名称。 如果要在自己的网站上销售此产品/服务，请确保名称正是在网站上显示的名称。

选择“保存”来保存进度。 下一步是为产品/服务添加 SKU。

## <a name="skus-form"></a>SKU 窗体

选择“SKU”窗体。 可以在此处看到“新建 SKU”这一选项。 选择此选项可输入 **SKU ID**。

![选择“新建 SKU”](./media/managed-application-author-marketplace/newOffer_skus.png)

**SKU ID**是 SKU 在产品/服务内的唯一标识符。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 它只能由小写字母数字字符或短划线 (-) 组成。 ID 不能以短划线结束，并且不能超过 50 个字符。 此字段在产品/服务推出后处于锁定状态。 在产品/服务内可以有多个 SKU。 每个计划发布的映像都需要一个 SKU。

选择“新建 SKU”后，需要填写以下窗体：

![提供新的 SKU](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>如何填写 SKU 详细信息部分

* 标题 - 为此 SKU 提供一个标题。 这是在库中为此项显示的标题。
* 摘要 - 为此 sku 提供简短摘要。 此文本显示在标题的正下方。
* 说明 - 提供关于此 SKU 的详细说明。
* SKU 类型 - 允许的值为“托管应用程序”和“解决方案模板”。 对于本例，请选择“托管应用程序”。

### <a name="how-to-fill-package-details-section"></a>如何填写程序包详细信息部分

程序包部分包含需要填写的以下字段

![程序包](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**当前版本** - 提供你上传的程序包的版本。 它应当采用以下格式：`{number}.{number}.{number}{number}`

**程序包文件** - 此程序包包含压缩为 .zip 文件的以下文件。

* **applianceMainTemplate.json** - 用来部署解决方案/应用程序的部署模板文件。 有关如何创作部署模板文件的更多详细信息，请参阅[创建你的第一个 Azure Resource Manager 模板](resource-manager-create-first-template.md)
* **appliancecreateUIDefinition.json** - Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
* **mainTemplate.json** - 仅包含 Microsoft.Solution/appliances 资源的模板文件。 需要注意的此资源的关键属性如下所述：

mainTemplate 包括以下属性：

*  kind - 对于 Marketplace 中的托管应用程序，请使用 **Marketplace**
*  ManagedResourceGroupId - 客户的订阅中在其中部署 applianceMainTemplate.json 中定义的所有资源的资源组。
*  PublisherPackageId - 唯一地标识程序包的字符串。 请采用 `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` 格式提供值。
  可以从发布门户获取 publisherId 和 OfferId 。

  ![产品/服务 id](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  可以如下图所示获取 SKU ID：

  ![SKU id](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  可以如下图所示获取程序包版本：

  ![程序包版本](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  因此，使用前面的示例，**PublisherPackageId** 为 `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`

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

此程序包应当包含成功预配此应用程序所需的任何其他嵌套模板或脚本。 mainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json 必须位于根文件中。

**Authorizations** - 此属性定义谁将针对客户订阅中的资源获得什么级别的访问权限。 它使得发布者可以代表客户管理应用程序。

* PrincipalId - 此属性是针对客户订阅中的资源被授予了特定权限（如角色定义所述）的用户、用户组或应用程序的 Azure Active Directory 标识符。
* Role Definition - 此属性是 Azure AD 提供的所有内置 RBAC 角色的列表。 你可以选择最合适的角色，并且可以代表客户管理资源。

可以添加多个授权。 但是，建议创建一个 Active Directory 用户组并在 **PrincipalId** 中指定其 Id。 这样，可以向用户组中添加更多用户并且不需要更新 SKU。

有关 RBAC 的详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](../active-directory/role-based-access-control-what-is.md)。

## <a name="marketplace-form"></a>Marketplace 窗体

Marketplace 窗体要求填写将显示在 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和 [Azure 门户](https://portal.azure.com/)中的字段。

### <a name="preview-subscription-ids"></a>预览订阅 ID

在此处输入希望有权访问发布后的产品/服务的 Azure 订阅 ID 列表。 通过这些列入允许列表的订阅，可以在推出产品/服务前测试预览的产品/服务。 通过合作伙伴门户，可以将最多 100 个订阅列入允许列表。

### <a name="suggested-categories"></a>建议的类别

从提供的列表中选择最多五个与产品/服务最相关的类别。 所选的类别用于将产品/服务映射到在 [Azure Marketplace](https://azuremarketplace.microsoft.com) 和[门户](https://portal.azure.com/)中提供的产品类别。

#### <a name="azure-marketplace"></a>Azure Marketplace

在托管应用程序的摘要中，将显示以下字段：

![Marketplace 摘要](./media/managed-application-author-marketplace/publishvm10.png)

在托管应用程序的概述中，将显示以下字段：

![Marketplace 概述](./media/managed-application-author-marketplace/publishvm11.png)

在托管应用程序的计划和定价中，将显示以下字段：

![Marketplace 计划](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure 门户

在托管应用程序的摘要中，将显示以下字段：

![门户摘要](./media/managed-application-author-marketplace/publishvm12.png)

在托管应用程序的概述中，将显示以下字段：

![门户概述](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>徽标准则

对于上传到云合作伙伴门户中的所有徽标，请遵循以下准则：

*   Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。
*   门户的主题颜色为白色和黑色。 因此，应避免将这些颜色用作徽标的背景色。 使用一些可使徽标在门户中比较显眼的颜色。 建议使用简单的主颜色。 **如果使用的是透明背景，请确保徽标/文本不使用白色、黑色或蓝色。**
*   不要在徽标上使用渐变背景。
*   避免在徽标上放置文本，即使是公司或品牌名称也不可以。 徽标的外观应“平整”，并且应避免渐变。
*   确保徽标未被拉伸。

#### <a name="hero-logo"></a>特大徽标

特大徽标是可选的。 发布者可以选择不上载特大徽标。 但是，上传后的特大图标将无法删除。 此时，合作伙伴必须遵循适用于特大图标的 Marketplace 准则。

##### <a name="guidelines-for-the-hero-logo-icon"></a>适用于特大徽标图标的准则

*   发布者显示名称、计划标题和产品/服务详细摘要将以白色字体颜色显示。 因此，应避免在特大图标的背景中保留任何浅色。 特大图标不允许使用黑色、白色和透明背景。
*   在产品/服务推出后，发布者显示名称、计划标题、产品/服务详细摘要和创建按钮将以编程方式嵌入在特大徽标内。 因此，在设计特大徽标时，不应输入任何文本。 请仅在右侧保留空白区域，因为我们将以编程方式将文本（即，发布者显示名称、计划标题、产品/服务长摘要）放置在那里。 右侧文本的空白区域应为 415x100（并从左侧按 370 像素的幅度进行偏移）。

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>支持窗体

要填写的下一个窗体是支持窗体。 此窗体要求填写公司支持联系人（如工程联系人）信息和客户支持联系人信息。

## <a name="how-to-publish-an-offer"></a>如何发布产品/服务

完成所有部分后，选择“发布”来开始向客户提供产品/服务的过程。

## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](managed-application-overview.md)。
* 有关从 Marketplace 使用托管应用程序的信息，请参阅[在 Marketplace 中使用 Azure 托管应用程序](managed-application-consume-marketplace.md)。
* 有关发布服务目录托管应用程序的信息，请参阅[创建和发布服务目录托管应用程序](managed-application-publishing.md)。
* 有关使用服务目录托管应用程序的信息，请参阅[使用服务目录托管应用程序](managed-application-consumption.md)。

