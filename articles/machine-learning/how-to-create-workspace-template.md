---
title: 使用 Azure 资源管理器模板创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 资源管理器模板创建新的 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/04/2019
ms.custom: seoapril2019
ms.openlocfilehash: 63638dccbe489a6d63d4c1875d68ca12f0015836
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689169"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure 资源管理器模板创建 Azure 机器学习的工作区

本文介绍几种使用 Azure 资源管理器模板创建 Azure 机器学习工作区的方法。 使用资源管理器模板可以轻松地通过单个协调操作创建资源。 模板是一个 JSON 文档，定义部署所需的资源。 它还可以指定部署参数。 使用模板时，参数用于提供输入值。

有关详细信息，请参阅[使用 Azure 资源管理器模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="prerequisites"></a>必备组件

* 一个 **Azure 订阅**。 如果没有，请尝试[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。

* 若要在 CLI 中使用模板，需要安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>资源管理器模板

以下资源管理器模板可用于创建 Azure 机器学习工作区和关联的 Azure 资源：

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

此模板创建以下 Azure 服务：

* Azure 资源组
* Azure 存储器帐户
* Azure Key Vault
* Azure Application Insights
* Azure 容器注册表
* Azure 机器学习工作区

资源组是保存服务的容器。 Azure 机器学习工作区需要多种服务。

示例模板包含两个参数：

* **位置**：将在其中创建资源组和服务。

    模板将使用你为大多数资源选择的位置。 例外的情况是 Application Insights 服务，它不像其他所有服务一样在所有位置都可用。 如果选择了 Application Insights 服务不可用的位置，将在美国中南部位置创建该服务。

* **工作区名称**：Azure 机器学习工作区的友好名称。

    其他服务的名称将随机生成。

> [!TIP]
> 尽管与此文档关联的模板会创建新的 Azure 容器注册表，但你也可以创建新的工作区，而无需创建容器注册表。 如果工作区中存在容器注册表，则当你执行需要容器注册表的操作时，将创建一个。 例如，定型或部署模型。
>
> 你还可以在 Azure 资源管理器模板中引用现有的容器注册表或存储帐户，而不是创建一个新的容器。

有关模板的详细信息，请参阅以下文章：

* [创作 Azure 资源管理器模板](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure 资源管理器模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices 资源类型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 遵循[从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)中的步骤。 显示“编辑模板”屏幕后，请粘贴本文档中所述的模板。
1. 选择“保存”以使用该模板。 提供以下信息并同意列出的条款和条件：

   * 订阅：选择要用于这些资源的 Azure 订阅。
   * 资源组：选择或创建一个包含服务的资源组。
   * 工作区名称：要创建的 Azure 机器学习工作区的名称。 工作区名称的长度必须为 3 到 33 个字符。 只能包含字母数字字符和“-”。
   * 位置：选择将在其中创建资源的位置。

有关详细信息，请参阅[从自定义模板部署资源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

此示例假设已将模板保存到当前目录中名为 `azuredeploy.json` 的文件：

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)和[使用 SAS 令牌和 Azure PowerShell 部署专用资源管理器模板](../azure-resource-manager/secure-template-with-sas-token.md)。

## <a name="use-azure-cli"></a>使用 Azure CLI

此示例假设已将模板保存到当前目录中名为 `azuredeploy.json` 的文件：

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)和[使用 SAS 令牌和 Azure CLI 部署专用资源管理器模板](../azure-resource-manager/secure-template-with-sas-token.md)。

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 访问策略和 Azure 资源管理器模板

使用 Azure 资源管理器模板创建工作区和关联资源（包括 Azure Key Vault）时多次。 例如，将模板多次用于与持续集成和部署管道的一部分的相同参数。

大多数通过模板创建的资源操作都是幂等的，但 Key Vault 每次使用模板时都将清除访问策略。 清除访问策略会断开使用该访问策略的任何现有工作区对 Key Vault 的访问。 例如，Azure Notebooks VM 的停止/创建功能可能会失败。  

若要避免此问题，建议采用以下方法之一：

* 不要对相同的参数多次部署模板。 或删除现有资源，然后使用模板重新创建它们。
  
* 检查 Key Vault 访问策略，然后使用这些策略设置模板的 `accessPolicies` 属性。 若要查看访问策略，请使用以下 Azure CLI 命令：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    有关使用模板的 `accessPolicies` 部分的详细信息，请参阅[AccessPolicyEntry 对象引用](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)。

* 检查 Key Vault 资源是否已存在。 如果是这样，请不要通过模板重新创建它。 例如，若要使用现有 Key Vault 而不是创建一个新的，请对模板进行以下更改：

    * **添加**一个参数，该参数接受现有 Key Vault 资源的 ID：

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **删除**创建 Key Vault 资源的部分：

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```
    
    * 从工作区的 `dependsOn` 部分**删除**`"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 行。 还可**更改**工作区的 "`properties`" 部分中的 `keyVault` 条目，以引用 `keyVaultId` 参数：

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```
      
    完成这些更改后，你可以在运行模板时指定现有 Key Vault 资源的 ID。 然后，模板会通过将工作区的 `keyVault` 属性设置为其 ID 来重新使用 Key Vault。

    若要获取 Key Vault 的 ID，可以引用原始模板运行的输出或使用 Azure CLI。 以下命令是使用 Azure CLI 获取 Key Vault 资源 ID 的示例：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    此命令会返回类似于以下文本的值：

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```



## <a name="next-steps"></a>后续步骤

* [使用资源管理器模板和资源管理器 REST API 部署资源](../azure-resource-manager/resource-group-template-deploy-rest.md)。
* [通过 Visual Studio 创建和部署 Azure 资源组](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。
