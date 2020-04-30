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
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: b802a9c9df7e7f0c44ea66ee0061efb517b80050
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682759"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure 资源管理器模板创建 Azure 机器学习的工作区

本文介绍几种使用 Azure 资源管理器模板创建 Azure 机器学习工作区的方法。 使用资源管理器模板可以轻松地通过单个协调操作创建资源。 模板是一个 JSON 文档，定义部署所需的资源。 它还可以指定部署参数。 使用模板时，参数用于提供输入值。

有关详细信息，请参阅[使用 Azure Resource Manager 模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* 若要在 CLI 中使用模板，需要安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>Resource Manager 模板

可使用以下资源管理器模板创建 Azure 机器学习工作区和关联的 Azure 资源：

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

此模板创建以下 Azure 服务：

* Azure 资源组
* Azure 存储帐户
* Azure Key Vault
* Azure Application Insights
* Azure 容器注册表
* Azure 机器学习工作区

资源组是保存服务的容器。 Azure 机器学习工作区需要多种服务。

示例模板包含两个参数：

* **位置**：将在其中创建资源组和服务。

    模板将使用你为大多数资源选择的位置。 例外的情况是 Application Insights 服务，它不像其他所有服务一样在所有位置都可用。 如果选择了 Application Insights 服务不可用的位置，将在美国中南部位置创建该服务。

* **工作区名称**：Azure 机器学习工作区的友好名称。

    > [!NOTE]
    > 工作区名称不区分大小写。

    其他服务的名称将随机生成。

> [!TIP]
> 尽管与此文档关联的模板会创建新的 Azure 容器注册表，但你也可以创建新的工作区，而无需创建容器注册表。 当你执行需要容器注册表的操作时，会创建容器注册表。 例如，训练或部署模型。
>
> 还可以在 Azure 资源管理器模板中引用现有的容器注册表或存储帐户，而不是创建一个新的。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

有关模板的详细信息，请参阅以下文章：

* [创作 Azure Resource Manager 模板](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices 资源类型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>高级模板

以下示例模板演示如何创建具有三项设置的工作区：

* 启用工作区的高保密性设置
* 启用工作区加密
* 使用现有 Azure Key Vault 检索客户托管的密钥

有关详细信息，请参阅[静态加密](concept-enterprise-security.md#encryption-at-rest)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "high_confidentiality":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
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
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
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
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbiWorkspace": "[parameters('high_confidentiality')]"
      }
    }
  ]
}
```

若要获取 Key Vault 的 ID 以及此模板所需的密钥 URI，你可以使用 Azure CLI。 下面的命令获取 Key Vault ID：

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
```

此命令会返回类似于 `"/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault"` 的值。

若要获取客户托管密钥的 URI，请使用以下命令：

```azurecli-interactive
az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
```

此命令会返回类似于 `"https://mykeyvault.vault.azure.net/keys/mykey/{guid}"` 的值。

> [!IMPORTANT]
> 创建工作区后，不能更改机密数据、加密、密钥保管库 ID 或密钥标识符的设置。 若要更改这些值，必须使用新值创建新的工作区。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 遵循[从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)中的步骤。 显示“编辑模板”屏幕后，请粘贴本文档中所述的模板。____
1. 选择“保存”以使用该模板。____ 提供以下信息并同意列出的条款和条件：

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

有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)和[使用 SAS 令牌和 Azure PowerShell 部署专用资源管理器模板](../azure-resource-manager/templates/secure-template-with-sas-token.md)。

## <a name="use-the-azure-cli"></a>使用 Azure CLI

此示例假设已将模板保存到当前目录中名为 `azuredeploy.json` 的文件：

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)和[使用 SAS 令牌和 Azure CLI 部署专用资源管理器模板](../azure-resource-manager/templates/secure-template-with-sas-token.md)。

## <a name="troubleshooting"></a>疑难解答

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 访问策略和 Azure 资源管理器模板

使用 Azure 资源管理器模板多次创建工作区和关联的资源（包括 Azure Key Vault）时。 例如，在持续集成和部署管道过程中，对同一参数多次使用模板。

大多数通过模板的资源创建操作都是幂等的，但 Key Vault 每次使用模板时都将清除访问策略。 清除访问策略会中断任何使用该访问的现有工作区对 Key Vault 的访问。 例如，Azure Notebooks VM 的停止/创建功能可能会失败。  

若要避免此问题，我们建议运用以下方法之一：

* 请不要对同一个参数多次部署模板。 或是在使用模板重新创建之前删除现有资源。

* 检查 Key Vault 的访问策略，然后使用这些策略设置模板的 `accessPolicies` 属性。 若要查看访问策略，请使用以下 Azure CLI 命令：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    有关使用模板的`accessPolicies`部分的详细信息，请参阅[AccessPolicyEntry 对象引用](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)。

* 检查 Key Vault 资源是否已存在。 如果存在，请勿通过模板重新创建。 例如，若要使用现有 Key Vault 而不是创建一个新的，请对模板进行以下更改：

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

    * **Remove**从工作`"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",`区的`dependsOn`部分中删除该行。 同时**Change** ，更改`keyVault`工作区的`properties`部分中的条目以引用`keyVaultId`参数：

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

    完成这些更改后，你可以在运行模板时指定现有 Key Vault 资源的 ID。 然后，模板会通过将工作区的`keyVault`属性设置为其 ID 来重用 Key Vault。

    若要获取 Key Vault 的 ID，可以引用原始模板运行的输出或使用 Azure CLI。 以下命令是使用 Azure CLI 获取 Key Vault 资源 ID 的示例：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    此命令会返回类似于以下文本的值：

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>后续步骤

* [部署具有资源管理器模板和资源管理器 REST API 的资源](../azure-resource-manager/templates/deploy-rest.md)。
* [通过 Visual Studio 创建和部署 Azure 资源组](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
