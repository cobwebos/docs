---
title: 发布 Azure 解决方案模板 | Microsoft Docs
description: 将解决方案模板发布到 Azure 市场。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: a19b06e1297b86610ecee01a4e84150813cdb208
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452388"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>将解决方案模板发布到 Azure 市场

本文提供有关将解决方案模板套餐发布到 Azure 市场的步骤。

## <a name="prerequisites"></a>先决条件

以下技术和非技术先决条件适用于在 Azure 市场中列出解决方案模板。

### <a name="technical"></a>技术方面

- [了解 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。
- Azure 快速入门模板：
    - [Azure 快速入门模板文档](https://azure.microsoft.com/documentation/templates/)
    - [GitHub 上的 Azure 快速入门文档](https://github.com/azure/azure-quickstart-templates)
 - [创建 Azure 门户用户界面文件](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
 - 启用[客户使用情况属性](./../azure-partner-customer-usage-attribution.md)，以帮助跟踪在 Azure 上的客户部署软件的 Azure 使用情况。

### <a name="non-technical-business-requirements"></a>非技术方面（业务要求）

- 公司（或其子公司）必须位于 Azure 市场支持的销售国家/地区。
- 产品必须以兼容 Azure 市场支持的计费模式的方式获得许可。
- 你需要负责以商业上合理的方式向客户提供技术支持，支持的形式包括免费、付费或社区支持。
- 负责获得自己的软件和任何第三方软件依赖项的许可。
- 提供的内容必须满足在 Azure 市场上和 Azure 门户中列入名单的产品/服务的标准。
- 同意 Azure 市场参与政策与发布者协议的条款。
- 同意遵守使用条款、Microsoft 隐私声明和 Microsoft Azure 认证计划协议。

## <a name="before-you-begin"></a>开始之前

满足所有先决条件后，可以开始创作解决方案模板套餐。 在开始之前，请查看以下套餐和 SKU 信息。

**产品**

Azure 应用程序套餐对应于发布者提供的一类产品套餐。 如果你有想要在 Azure 市场中提供的新型解决方案/应用程序，最佳方法是创建新的套餐。 产品/服务是 SKU 的集合。 每个产品/服务在 Azure 市场中以自有实体的方式显示。

**SKU**

SKU 是可购买的产品/服务的最小单位。 在相同的产品类（套餐）内，通过 SKU 能够区分支持的不同功能。 例如，套餐是托管的或非托管的，并支持不同的计费模型。

对于以下情况，请添加多个 SKU：
- 要支持不同的计费模型，例如自带许可 (BYOL) 或即用即付 (PAYG)。
- 每个 SKU 支持不同的功能集，每个功能集的定价方式不同。

SKU 显示在 Azure 市场的父套餐下，并在 Azure 门户中以可购买的自有实体的方式显示。

## <a name="to-create-a-new-offer"></a>创建新套餐

1.  登录[云合作伙伴平台](http://cloudpartner.azure.com/)。

2.  在左侧导航栏中，依次选择“+ 新建套餐”、“Azure 应用程序”。

    ![创建新套餐](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  在“新建套餐”下，选择“编辑器”。

    ![“新建套餐”编辑器](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  在“编辑器”下面的以下视图中提供信息：
    - 产品/服务设置
    - SKU
    - 市场
    - 支持

每个视图包含一组要填写的字段。必填字段标有红色星号 (\*)

## <a name="to-configure-offer-settings"></a>配置“套餐设置”

1. 在“套餐设置”中配置以下“套餐标识”字段。

    **套餐 ID**

     发布者配置文件中套餐的唯一标识符。 此 ID 显示在产品 URL、Azure 资源管理器模板和计费报表中。 只能使用小写字母数字字符或短划线 (-)。 ID 不能以短划线结束，并且不能超过 50 个字符。 
    >[!Note]
    >此字段在套餐推出后处于锁定状态。

    **发布者 ID**

    发布者配置文件的下拉列表。 选择要发布的套餐所在的配置文件。 
    >[!Note]
    >此字段在套餐推出后处于锁定状态。

    **名称**

    产品/服务的显示名称。 此名称将显示在 Azure 市场和 Azure 门户中。 该名称不能超过 50 个字符。 参考以下指导指定套餐名称：
    -  其中包含产品的可识别品牌名称。 
    - 除非有套餐营销方面的需要，否则不要在此处包含公司名称。
    - 如果你在自己的网站上营销此套餐，请确保此名称与网站上的名称相同。

2. 选择“保存”以完成套餐的
“套餐设置”。

## <a name="to-create-skus"></a>创建 SKU
------------------

1. 选择“SKU”。 

    ![新建 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU ID是 SKU 在套餐内的唯一标识符。 此 ID 显示在产品 URL、Resource Manager 模板和计费报表中。 SKU ID：
    - 不能超过 50 个字符。
    - 只能由小写字母数字字符或短划线 (-) 组成。
    - ID 不能以短划线结尾。

    >[!Note]
    >添加 SKU 后，它会显示在“SKU”视图中的“SKU”列表内。 若要查看 SKU 详细信息，请选择 SKU 名称。 

2. 选择“新建 SKU”并提供以下屏幕截图中所示的信息。 

    ![SKU 详细信息](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>SKU 设置

提供以下 SKU 设置。

- **标题** - SKU 的标题。 此标题将显示在此项的库中。
- **摘要** - SKU 的简短摘要说明。 （最大长度为 100 个字符。）
- **说明** - SKU 的详细说明。
- **SKU 类型** - 含以下值的下拉列表：“托管应用程序(预览版)”和“解决方案模板”。 对于本方案，请选择“解决方案模板”。
- **云可用性** - SKU 的位置。 默认值为“公共 Azure”。

### <a name="package-details"></a>包详细信息

完成 SKU 设置后，请提供以下包详细信息。

![包详细信息](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **当前版本** - 要上传的包版本。 版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- **包文件** - 此包包含以下文件，这些文件保存在一个 .zip 文件中。
    -   MainTemplate.json - 部署模板文件，用于部署解决方案/应用程序并创建针对解决方案定义的资源。 有关详细信息，请参阅[如何创作部署模板文件](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
    -   createUIDefinition.json - Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅[为托管应用程序创建 Azure 门户用户界面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >此包应该包含预配此应用程序所需的任何其他嵌套模板或脚本。 mainTemplate.json 和 createUIDefinition.json 必须位于根文件夹中。

## <a name="to-configure-the-marketplace"></a>配置市场

使用“市场”视图配置 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中显示的套餐字段。

### <a name="preview-subscription-ids"></a>预览订阅 ID

发布套餐后你希望有权访问的 Azure 订阅 ID 列表。 通过这些列入允许列表的订阅，可以在推出套餐前测试预览的套餐。 通过合作伙伴门户，可以将多达 100 个订阅列入允许列表。

### <a name="suggested-categories"></a>建议的类别

从提供的列表中选择最多五个与产品/服务最相关的类别。 所选的类别用于将产品/服务映射到在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中提供的产品类别。

以下示例显示 Azure 市场和 Azure 门户中的市场信息。

**Azure 市场**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure 门户**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>徽标准则

遵循以下准则将徽标上传到云合作伙伴门户：

-   Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。

-   Azure 门户的主题颜色为白色和黑色。 请避免将这两种颜色用作徽标的背景色。 使用可使徽标在 Azure 门户中突出显示的颜色。 建议使用简单的主颜色。

    >[!Note] 
    >如果使用透明背景，请确保徽标/文本不使用白色、黑色或蓝色。

-   不要在徽标上使用渐变背景。

-   避免在徽标中插入文本， 此准则包括公司名称或品牌名称。 徽标的外观应平整，应避免渐变。

-   不应拉伸徽标。

#### <a name="hero-logo"></a>特大徽标

特大徽标是可选的。 发布者可以选择不上载特大徽标。 但是，上传徽标后无法将其删除。 合作伙伴必须遵循主图标的 Azure 市场准则。

#### <a name="guidelines-for-the-hero-logo-icon"></a>特大徽标图标的准则

-   发布者显示名称、计划标题和套餐详细摘要使用白色字体显示。 避免在背景中使用任何亮色。 主图标不允许使用黑色、白色和透明背景。

-   列出套餐后，发布者显示名称、计划标题、套餐详细摘要和创建按钮以编程方式嵌入在主徽标内。 设计主徽标时，请勿输入任何文本。 在徽标右侧保留空白空间。 此空间应为 415 x 100 像素，并与左侧保留 370 像素的间距。

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>配置支持

使用“支持”视图提供以下信息：

- 公司的支持联系人，例如工程人员。
- 客户支持联系人。

## <a name="to-publish-the-offer"></a>发布套餐

最后一步是发布套餐。 选择“发布”。
