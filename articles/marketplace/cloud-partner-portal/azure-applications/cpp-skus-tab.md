---
title: 配置 Azure 应用程序产品/服务 Sku |Azure Marketplace
description: 如何为 Azure 托管应用程序和 Azure 解决方案模板配置 SKU。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: ef4ea2419c64d0376023ea5d291460df48a51c63
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943409"
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

下一步的屏幕截图显示 SKU 详细信息窗体的解决方案模板。

![解决方案模板的 SKU 详细信息窗体](./media/azureapp-sku-details-solutiontemplate.png)

提供以下 SKU 值。  追加一个星号的字段是必需的。

|    字段         |       描述                                                            |
|  ---------       |     ---------------                                                          |
|  **标题\***     | Sku 标题。 此标题将显示在此项的库中。   |
| **摘要\***    | SKU 的简短摘要说明。 （最大长度为 100 个字符。）  |
| **说明\*** | SKU 的详细的说明。 支持基本的 HTML。                 | 
| **SKU 类型\***   | 类型的 Azure 应用程序解决方案，选择 ***解决方案模板**对于此方案。 |
| **云可用性\*** | SKU 的位置。 默认值为“公共 Azure”。  <b/>   **公共 Azure** -应用程序为可部署到集成了 marketplace 的所有公共 Azure 区域中的客户。  <b/>   **Azure 政府版云**-将在 Azure 政府版云中部署应用。 之前发布到[Azure 政府版](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)，Microsoft 建议发布者测试并验证其解决方案的工作原理，此环境中的预期。 若要暂存和测试，可请求一个[试用帐户](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。  |
| **这是一种专用的 SKU？\*** | 选择**是**如果此 SKU 仅供一组精选的客户。 |
|   |   |

  > [!NOTE] 
  > Microsoft Azure 政府是政府社区云，美国联邦、州、地方或部落客户和有资格服务于这些实体的合作伙伴对政府社区云具有受控访问权限。


### <a name="sku-details-for-managed-application"></a>托管应用程序的 SKU 详细信息

下一个屏幕截图显示托管应用程序的 SKU 详细信息窗体。

   ![托管应用程序的 SKU 详细信息窗体](./media/azureapp-sku-details-managedapplication.png)

配置以下 SKU 设置。 追加一个星号的字段是必需的。

|    字段         |       描述                                                            |
|  ---------       |     ---------------                                                          |
|  **标题\***     | Sku 标题。 此标题将显示在此项的库中。   |
| **摘要\***    | SKU 的简短摘要说明。 （最大长度为 100 个字符。）  |
| **说明\*** | SKU 的详细的说明。 支持基本的 HTML。                 | 
| **SKU 类型\***   | 类型的 Azure 应用程序解决方案，选择 ***托管应用程序**对于此方案。 
| **云可用性\*** | SKU 的位置。 默认值为“公共 Azure”。  <b/>   **公共 Azure** -应用程序为可部署到集成了 marketplace 的所有公共 Azure 区域中的客户。  <b/>   **Azure 政府版云**-将在 Azure 政府版云中部署应用。 之前发布到[Azure 政府版](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)，Microsoft 建议发布者测试并验证其解决方案的工作原理，此环境中的预期。 若要暂存和测试，可请求一个[试用帐户](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。   Microsoft Azure 政府是政府社区云，美国联邦、州、地方或部落客户和有资格服务于这些实体的合作伙伴对政府社区云具有受控访问权限。 |
| **这是一种专用的 SKU？\*** | 选择**是**如果此 SKU 仅供一组精选的客户。 |
| **国家/地区/区域可用性\*** | 使用**选择区域**若要查看可用的国家/地区的列表。 勾选国家/地区，然后选择“确定”保存你的选择。  <b/>   ![国家/地区和区域可用性列表](./media/azure-app-select-country-region.png)  |
| **旧的定价\*** | 每月的以美元为单位的 sku 价格。 配置时，使用当前汇率换算为以本地货币为单位的价格。 请验证这些设置，因为它们最终归你所有。 若要单独设置或查看每个国家/地区的价格，请导出定价电子表格并导入应用了自定义定价的表格。  必须保存定价更改以便导出/导入的定价数据。  |
| **简化的货币定价\*** | 每月的以美元为单位的 sku 价格。 这与旧定价相同。 有关详细信息，请参阅[简化的货币定价](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)。 |
|  |  |


### <a name="package-details-for-solution-template"></a>解决方案模板的包详细信息

   ![解决方案模板的包详细信息](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

提供以下**包的详细信息**值。  追加一个星号的字段是必需的。

- **版本\*** -将上传的包的版本。 版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- **包文件 (.zip)\***  -此包包含以下文件，保存在.zip 文件。
  - MainTemplate.json - 部署模板文件，用于部署解决方案/应用程序并创建针对解决方案定义的资源。 有关详细信息，请参阅[如何创作部署模板文件](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)。
  - createUIDefinition.json - Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅[为托管应用程序创建 Azure 门户用户界面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。

  >[!IMPORTANT] 
  >此包应该包含预配此应用程序所需的任何嵌套模板或脚本。 MainTemplate.json 和 createUIDefinition.json 必须位于根文件夹中。


### <a name="package-details-for-managed-application"></a>托管应用程序的包详细信息

   ![托管应用程序的包详细信息](./media/azureapp-sku-pkgdetails-managedapplication.png)

提供以下包详细信息。  追加一个星号的字段是必需的。

- **版本\*** -将上传的包的版本。 版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- **包文件 (.zip)\***  -此包包含以下文件，保存在.zip 文件。
  - applianceMainTemplate.json - 部署模板文件，用于部署解决方案/应用程序并创建定义的资源。 有关详细信息，请参阅[快速入门：使用 Azure 门户创建和部署 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。 
  - applianceCreateUIDefinition.json - Azure 门户使用此文件生成用于预配此解决方案/应用程序的用户界面。 有关详细信息，请参阅[为托管应用程序创建 Azure 门户用户界面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。
  - mainTemplate.json - 仅包含 Microsoft.Solution/appliances 资源的模板文件。 有关详细信息，请参阅[了解 Azure 资源管理器模板的结构和语法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 <br>
请注意此资源的以下主要属性：
    - kind - 对于市场托管应用程序而言，该值应该为“市场”。
    - ManagedResourceGroupId - 客户订阅中的资源组，将在其中部署 applianceMainTemplate.json 中定义的所有资源。
    - PublisherPackageId：唯一地标识包的字符串。 需要按以下方式构造该值：串联 [publisherId].[OfferId]-preview[SKUID].[PackageVersion]。

  >[!IMPORTANT] 
  >此包应该包含预配此应用程序所需的任何嵌套模板或脚本。 以下文件必须位于根文件夹中：MainTemplate.json、applianceMainTemplate.json 和 applianceCreateUIDefinition.json。

- **租户 Id\***  -你的组织的 Azure Active Directory 租户 id。
- **启用 JIT 访问？\***  – 选择**是**若要启用实时中使用此产品/服务的客户部署的管理访问权限。

  >[!NOTE] 
  >如果启用 JIT，你必须更新 CreateUiDefinition.json 文件以支持 JIT 访问。

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

您将进一步介绍产品/服务和提供中的市场营销资产[Marketplace 选项卡](./cpp-marketplace-tab.md)。 
