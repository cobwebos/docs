---
title: 创建或管理 B2B 集成帐户
description: 创建、链接和管理集成帐户，以便与 Azure 逻辑应用进行企业集成
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270324"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>在 Azure 逻辑应用中为 B2B 企业集成创建和管理集成帐户

你需先创建一个集成帐户，然后才能使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)构建[企业集成和 B2B 解决方案](../logic-apps/logic-apps-enterprise-integration-overview.md)。该集成帐户是一个单独的 Azure 资源，该资源为你定义并用于逻辑应用工作流的集成项目提供安全、可缩放和可管理的容器。

例如，您可以创建、存储和管理 B2B 工件，如贸易伙伴、协议、地图、架构、证书和批处理配置。 此外，在逻辑应用可以使用这些项目并使用逻辑应用 B2B 连接器之前，必须[将集成帐户链接到](#link-account)逻辑应用。 集成帐户和逻辑应用必须*存在于同一*位置或区域中。

> [!TIP]
> 要在[集成服务环境中](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)创建集成帐户，请参阅在[ISE 中创建集成帐户](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。

本主题介绍如何执行这些任务：

* 创建集成帐户。
* 将集成帐户链接到逻辑应用。
* 更改集成帐户的定价层。
* 从逻辑应用取消链接集成帐户。
* 将集成帐户移到另一个 Azure 资源组或订阅。
* 删除集成帐户。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="create-integration-account"></a>创建集成帐户

对于此任务，可以通过按照本节中的步骤[Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)或[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)使用 Azure 门户。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 主菜单上选择“创建资源”****。 在搜索框中，输入"集成帐户"作为筛选器，然后选择 **"集成帐户**"。

   ![创建新的集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. 在 **"集成帐户**"下，选择 **"创建**"。

   ![选择“添加”以创建集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. 提供有关集成帐户的信息：

   ![提供集成帐户详细信息](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | properties | 必选 | “值” | 描述 |
   |----------|----------|-------|-------------|
   | **名称** | 是 | <*集成帐户名称*> | 集成帐户的名称，只能包含字母、数字、连字符`-`（）、下划线 （）、`_`括号 （`(`和`)`期间 （`.`）。 此示例使用"法布里卡姆集成"。 |
   | **订阅** | 是 | <*Azure 订阅名称*> | Azure 订阅的名称 |
   | **资源组** | 是 | <*Azure 资源组名称*> | 用于组织相关[资源的 Azure 资源组](../azure-resource-manager/management/overview.md)的名称。 在此示例中，创建一个名称为"Fabrikam 集成-RG"的新资源组。 |
   | **定价层** | 是 | <*定价级别*> | 集成帐户的定价层，您可以稍后更改该层。 在此示例中，选择 **"免费**"。 有关详细信息，请参阅以下主题： <p>- [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **位置** | 是 | <*Azure 区域*> | 存储集成帐户元数据的区域。 选择与逻辑应用相同的位置，或者在与集成帐户相同的位置创建逻辑应用。 在此示例中，请使用"美国西部"。 <p>**注意**：要在[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中创建集成帐户，请选择该 ISE 作为位置。 有关详细信息，请参阅在[ISE 中创建集成帐户](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)。 |
   | **日志分析** | 否 | 关，开 | 保留此示例的 **"关闭**"设置。 |
   |||||

1. 完成后，选择“创建”****。

   部署完成后，Azure 将打开集成帐户。

   ![Azure 打开集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. 在逻辑应用可以使用集成帐户之前，请按照后续步骤将集成帐户和逻辑应用链接在一起。

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>链接到逻辑应用

要授予逻辑应用对包含 B2B 项目的功能的集成帐户的访问权限，必须首先将集成帐户链接到逻辑应用。 逻辑应用和集成帐户必须存在于同一区域中。 要完成此任务，可以使用 Azure 门户。 如果使用 Visual Studio，并且逻辑应用位于[Azure 资源组项目中](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)，则可以使用[Visual Studio 将逻辑应用链接到集成帐户](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)。

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在[Azure 门户](https://portal.azure.com)中，打开现有逻辑应用或创建新的逻辑应用。

1. 在逻辑应用的菜单中，在“设置”**** 下，选择“工作流设置”****。 在 **"集成帐户**"下，打开 **"选择集成帐户**"列表。 选择要链接到逻辑应用的集成帐户。

   ![选择集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 要完成链接，请选择"**保存**"。

   ![选择集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   成功链接集成帐户后，Azure 将显示一条确认消息。

   ![Azure 确认成功链接](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

现在，逻辑应用可以使用集成帐户中的工件以及 B2B 连接器，如 XML 验证和平面文件编码或解码。  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>更改定价层

要增加集成[帐户的限制，](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)可以[升级到更高的定价层](#upgrade-pricing-tier)（如果可用）。 例如，可以从免费层升级到基本层或标准层。 您也可以[降级到较低层](#downgrade-pricing-tier)（如果可用）。 有关详细信息定价信息，请参阅以下主题：

* [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)
* [逻辑应用定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>升级定价层

要进行此更改，可以使用 Azure 门户，请按照本节中的步骤或[Azure CLI](#upgrade-tier-azure-cli)。

#### <a name="azure-portal"></a>Azure 门户

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在主 Azure 搜索框中，输入"集成帐户"作为筛选器，然后选择 **"集成帐户**"。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 显示 Azure 订阅中的所有集成帐户。

1. 在“集成帐户”下，选择要移动的集成帐户。**** 在集成帐户菜单上，选择 **"概述**"。

   ![在集成帐户菜单上，选择"概述"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在"概述"窗格中，选择 **"升级定价层**"，其中列出了任何可用的较高层。 选择层时，更改将立即生效。

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. 如果尚未执行此操作，[请安装 Azure CLI 先决条件](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 在 Azure 门户中，打开 Azure[**云外壳**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)环境。

   ![打开 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示符处，输入[**az 资源**命令](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)，`skuName`并设置为所需的较高层。

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   例如，如果您有基本层，则可以设置为`skuName``Standard`：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>降级定价层

要进行此更改，请使用[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 如果尚未执行此操作，[请安装 Azure CLI 先决条件](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

1. 在 Azure 门户中，打开 Azure[**云外壳**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)环境。

   ![打开 Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 在命令提示符处，输入[**az 资源**命令](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)并将其设置为`skuName`所需的下层。

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   例如，如果您有标准层，则可以设置为`skuName``Basic`：

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>从逻辑应用取消链接

如果要将逻辑应用链接到其他集成帐户，或者不再使用逻辑应用的集成帐户，请使用 Azure 资源资源管理器删除链接。

1. 打开浏览器窗口，然后转到[Azure 资源资源管理器https://resources.azure.com)（。](https://resources.azure.com) 使用相同的 Azure 帐户凭据登录。

   ![Azure 资源浏览器](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 在搜索框中，输入逻辑应用的名称，以便找到并选择逻辑应用。

   ![查找并选择逻辑应用](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 在资源管理器标题栏上，选择 **"读/写**"。

   ![启用“读/写”模式](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. 在"**数据**"选项卡上，选择 **"编辑**"。

   ![在"数据"选项卡上，选择"编辑"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 在编辑器中，查找`integrationAccount`对象，并删除具有以下格式的属性：

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   例如：

   ![查找"集成帐户"对象](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. 在"**数据**"选项卡上，选择 **"放置"** 以保存更改。

   ![要保存更改，请选择"放置"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. 在 Azure 门户中，查找并选择“逻辑应用”。 在应用的 **"工作流设置**"下，检查 **"集成"帐户**属性现在显示为空。

   ![检查集成帐户是否未链接](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>移动集成帐户

您可以将集成帐户移动到其他 Azure 资源组或 Azure 订阅。 移动资源时，Azure 会创建新的资源标识，因此请确保改用新标识，并更新与移动资源关联的任何脚本或工具。 如果要更改订阅，还必须指定现有资源组或新资源组。

对于此任务，可以通过按照本节中的步骤或[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)使用 Azure 门户。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在主 Azure 搜索框中，输入"集成帐户"作为筛选器，然后选择 **"集成帐户**"。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 显示 Azure 订阅中的所有集成帐户。

1. 在“集成帐户”下，选择要移动的集成帐户。**** 在集成帐户菜单上，选择 **"概述**"。

   ![在集成帐户菜单上，选择"概述"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在**资源组**或**订阅名称**旁边，选择**更改**。

   ![更改资源组或订阅](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 选择您也要移动的任何相关资源。

1. 根据您的选择，按照以下步骤更改资源组或订阅：

   * 资源组：从 **"资源组"** 列表中，选择目标资源组。 或者，要创建其他资源组，请选择"**创建新资源组**"。

   * 订阅：从**订阅**列表中选择目标订阅。 在“资源组”列表中选择目标资源组。**** 或者，要创建其他资源组，请选择"**创建新资源组**"。

1. 要确认您的理解，即与移动资源关联的任何脚本或工具在使用新资源指示更新它们之前都不起作用，请选择确认框，然后选择"**确定**"。

1. 完成后，请确保使用移动资源的新资源 I 更新任何和所有脚本。  

## <a name="delete-integration-account"></a>删除集成帐户

对于此任务，可以通过按照本节中的步骤[Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)或[Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)使用 Azure 门户。

1. 使用 Azure 帐户凭据登录到 [Azure 门户](https://portal.azure.com)。

1. 在主 Azure 搜索框中，输入"集成帐户"作为筛选器，然后选择 **"集成帐户**"。

   ![查找集成帐户](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure 显示 Azure 订阅中的所有集成帐户。

1. 在“集成帐户”下，选择要删除的集成帐户。**** 在集成帐户菜单上，选择 **"概述**"。

   ![在集成帐户菜单上，选择"概述"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 在"概述"窗格中，选择 **"删除**"。

   ![在"概述"窗格中，选择"删除"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 要确认要删除集成帐户，请选择"**是**"。

   ![要确认删除，请选择"是"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>后续步骤

* [在您的集成帐户中创建贸易伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [在集成帐户中的合作伙伴之间创建协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)
