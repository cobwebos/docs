---
title: 为 Azure 应用程序产品/服务配置 SKU | Microsoft Docs
description: 如何为 Azure 托管应用程序和 Azure 解决方案模板配置 SKU。
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5d6ec0197699f603c79f414e015cdebcde6b9f60
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905607"
---
# <a name="azure-application-skus-tab"></a>Azure 应用程序 SKU 选项卡

本文介绍如何使用 SKU 选项卡为 Azure 应用程序创建 SKU。 

> [!IMPORTANT]
> 为托管应用程序和解决方案模板这两种产品/服务配置 SKU 的步骤不同。 本文对这些不同之处进行了介绍。 

## <a name="configure-azure-application-skus"></a>配置 Azure 应用程序 SKU

### <a name="create-a-new-sku"></a>新建 SKU

通过以下步骤新建 SKU：

1. 选择“SKU”选项卡。
2. 在 SKU 下选择“+ 新建 SKU”。

    ![新建 SKU 提示](./media/azureapp-plus-sku.png)

3. 在“新建 SKU”弹出窗口中，键入 SKU ID。 此 ID 仅限 50 个字符，只能包含小写的字母数字字符、短划线或下划线。 不能以短划线结尾。
4. 客户可以在产品 URL、资源管理器模板和计费报表中看到此 SKU ID。 不能在产品/服务发布后修改此 ID。

### <a name="sku-details-for-a-solution-template"></a>解决方案模板的 SKU 详细信息

提供以下 SKU 设置：

- **标题** - SKU 的标题。 此标题将显示在此项的库中。
- **摘要** - SKU 的简短摘要说明。 （最大长度为 100 个字符。）
- **说明** - SKU 的详细说明。
- **SKU 类型** - 含以下值的下拉列表：“解决方案模板”和“托管应用程序”。 对于本方案，请选择“解决方案模板”。
- **云可用性** - SKU 的位置。 默认值为“公共 Azure”。
公共 Azure - 客户可以在集成了市场的所有公共 Azure 区域中部署此虚拟机。
- **Azure 政府云** - 将在 Azure 政府云中部署此虚拟机。 微软建议发布者先测试并验证他们的解决方案在环境中是否按预期工作，然后再发布到 [Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)。 若要暂存和测试，可请求一个[试用帐户](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。

  >[!NOTE] 
  >Microsoft Azure 政府是政府社区云，美国联邦、州、地方或部落客户和有资格服务于这些实体的合作伙伴对政府社区云具有受控访问权限。

- **是否为专用 SKU?** - 如果此 SKU 仅供一组特定客户使用，选择“是”。

    ![解决方案模板的 SKU 详细信息窗体](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>托管应用程序的 SKU 详细信息

下一个屏幕截图显示托管应用程序的 SKU 详细信息窗体。

   ![托管应用程序的 SKU 详细信息窗体](./media/azureapp-sku-details-managedapplication.png)

配置以下 SKU 设置：

- **标题** - SKU 的标题。 此标题将显示在此项的库中。
- **摘要** - SKU 的简短摘要说明。 （最大长度为 100 个字符。）
- **说明** - SKU 的详细说明。
- **SKU 类型** - 含以下值的下拉列表：“解决方案模板”和“托管应用程序”。 对于本方案，请选择“托管应用程序”。
- **云可用性** - SKU 的位置。 默认值为“公共 Azure”。
- **公共 Azure** - 客户可以在集成了市场的所有公共 Azure 区域中部署此虚拟机。
- **Azure 政府云** - 将在 Azure 政府云中部署此虚拟机。 微软建议发布者先测试并验证他们的解决方案在环境中是否按预期工作，然后再发布到 [Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)。 若要暂存和测试，可请求一个[试用帐户](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。

  >[!NOTE] 
  >Microsoft Azure 政府是政府社区云，美国联邦、州、地方或部落客户和有资格服务于这些实体的合作伙伴对政府社区云具有受控访问权限。

- **是否为专用 SKU?** - 如果此 SKU 仅供一组特定客户使用，选择“是”。
- **国家/地区可用性** - 通过“选择区域”查看可用的国家/地区的列表。 勾选国家/地区，然后选择“确定”保存你的选择。 

   ![国家/地区可用性列表](./media/azure-app-select-country-region.png)

- **旧定价** - 输入 SKU 的价格，单位为“美元/月”。 配置时，使用当前汇率换算为以本地货币为单位的价格。 请验证这些设置，因为它们最终归你所有。 若要单独设置或查看每个国家/地区的价格，请导出定价电子表格并导入应用了自定义定价的表格。

  >[!NOTE]
  >保存定价更改，实现定价数据的导出/导入。

- **简化的货币定价** - 输入 SKU 的价格，单位为“美元/月”。 这与旧定价相同。 有关详细信息，请参阅[简化的货币定价](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)。

### <a name="package-details-for-solution-template"></a>解决方案模板的包详细信息

提供以下包详细信息：

- **版本** - 要上传的包版本。 版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- **包文件 (.zip)** - 此包包含以下文件，这些文件保存在一个 .zip 文件中。
  - MainTemplate.json - 部署模板文件，用于部署解决方案/应用程序并创建针对解决方案定义的资源。 有关详细信息，请参阅[如何创作部署模板文件](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)。
  - createUIDefinition.json - Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅[为托管应用程序创建 Azure 门户用户界面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。

  >[!IMPORTANT] 
  >此包应该包含预配此应用程序所需的任何嵌套模板或脚本。 MainTemplate.json 和 createUIDefinition.json 必须位于根文件夹中。

   ![解决方案模板的包详细信息](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>托管应用程序的包详细信息

提供以下包详细信息：

- **版本** - 要上传的包版本。 版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- **包文件 (.zip)** - 此包包含以下文件，这些文件保存在一个 .zip 文件中。
  - applianceMainTemplate.json - 部署模板文件，用于部署解决方案/应用程序并创建定义的资源。 有关详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。 
  - applianceCreateUIDefinition.json - Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅[为托管应用程序创建 Azure 门户用户界面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。
  - mainTemplate.json - 仅包含 Microsoft.Solution/appliances 资源的模板文件。 有关详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 <br>
请注意此资源的以下主要属性：
    - kind - 对于市场托管应用程序而言，该值应该为“市场”。
    - ManagedResourceGroupId - 客户订阅中的资源组，将在其中部署 applianceMainTemplate.json 中定义的所有资源。
    - PublisherPackageId：唯一地标识包的字符串。 需要按以下方式构造该值：串联 [publisherId].[OfferId]-preview[SKUID].[PackageVersion]。

  >[!IMPORTANT] 
  >此包应该包含预配此应用程序所需的任何嵌套模板或脚本。 以下文件必须位于根文件夹中：MainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json。

- **租户 Id** - 组织的 Azure Active Directory 租户的 ID。
- **启用 JIT 访问?** - 选择“是”可通过此产品/服务为客户部署启用实时管理访问。

  >[!NOTE] 
  >如果启用 JIT，你必须更新 CreateUiDefinition.json 文件以支持 JIT 访问。

   ![托管应用程序的包详细信息](./media/azureapp-sku-pkgdetails-managedapplication.png)

对于托管应用程序，必须配置授权和策略设置。

#### <a name="authorization"></a>授权

添加要授予对托管资源组的访问权限的用户、组或应用程序的 Azure Active Directory 标识符。 角色定义 ID 指示授予的权限。它可能是所有者、 参与者或任何自定义角色。

#### <a name="policy-settings"></a>策略设置

添加托管应用符合的策略。 详细了解 Azure 资源策略，请参阅[什么是 Azure Policy？](../../../governance/policy/overview.md)


   ![托管应用程序的授权和策略设置](./media/azureapp-sku-details-managedapp-auth-policy.png)

**新建授权：**

1. 在“授权”下，选择“+ 新建授权”。
2. 键入要授予对托管资源组的访问权限的用户、组或应用程序的 Azure Active Directory 标识符，作为“主体 ID”。 角色定义 ID 指示授予的权限。
3. 从下拉列表中选择以下某个选项作为“角色定义”：所有者或参与者。 有关详细信息，请参阅 [Azure 资源的内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

>[!NOTE] 
>可以添加多个授权。 但建议创建一个 Active Directory 用户组并在 PrincipalId 中指定其 ID。 这样，可以向用户组中添加更多用户，而无需更新 SKU。

**创建新策略：**

1. 在“策略设置”下，选择“+ 新建策略”。
2. 在“策略名称”处输入策略的名称。 名称最长 50 个字符。
3. 从下拉列表中选择一项作为“策略”。 选择数据提供商希望在应用程序使用数据时已启用的策略。 有关详细信息，请参阅 [Azure Policy 示例](https://docs.microsoft.com/azure/governance/policy/samples/index)。

    ![托管应用程序的策略设置](./media/azureapp-sku-policy-settings.png)

4. 在“策略 SKU”处，选择“免费”或“标准”作为策略 SKU 类型。 审核策略需选择标准 SKU。

## <a name="next-steps"></a>后续步骤

[“市场”选项卡](./cpp-marketplace-tab.md)
