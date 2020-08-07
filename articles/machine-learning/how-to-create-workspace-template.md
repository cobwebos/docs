---
title: 使用 Azure 资源管理器模板创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 资源管理器模板创建新的 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: larryfr
author: Blackmist
ms.date: 07/27/2020
ms.openlocfilehash: 5ddd4fc368a4e479d3d720698c7447d2b3cdf3cc
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986556"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure 资源管理器模板创建 Azure 机器学习的工作区

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

本文介绍几种使用 Azure 资源管理器模板创建 Azure 机器学习工作区的方法。 使用资源管理器模板可以轻松地通过单个协调操作创建资源。 模板是一个 JSON 文档，定义部署所需的资源。 它还可以指定部署参数。 使用模板时，参数用于提供输入值。

有关详细信息，请参阅[使用 Azure Resource Manager 模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* 若要在 CLI 中使用模板，需要安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="workspace-resource-manager-template"></a>工作区资源管理器模板

可以在 Azure 快速入门模板 GitHub 存储库的 "201-[计算机-学习-高级](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json)" 目录中找到整个文档中使用的 azure 资源管理器模板。

此模板创建以下 Azure 服务：

* Azure 存储帐户
* Azure Key Vault
* Azure Application Insights
* Azure 容器注册表
* Azure 机器学习工作区

资源组是保存服务的容器。 Azure 机器学习工作区需要多种服务。

示例模板具有两个**必需**参数：

* 将在其中创建资源的**位置**。

    模板将使用你为大多数资源选择的位置。 例外的情况是 Application Insights 服务，它不像其他所有服务一样在所有位置都可用。 如果选择了 Application Insights 服务不可用的位置，将在美国中南部位置创建该服务。

* **WorkspaceName**，它是 Azure 机器学习工作区的友好名称。

    > [!NOTE]
    > 工作区名称不区分大小写。

    其他服务的名称将随机生成。

> [!TIP]
> 当与本文档关联的模板创建了新的 Azure 容器注册表时，你还可以在无需创建容器注册表的情况下创建新工作区。 当你执行需要容器注册表的操作时，会创建容器注册表。 例如，训练或部署模型。
>
> 还可以在 Azure 资源管理器模板中引用现有的容器注册表或存储帐户，而不是创建一个新的。 但是，所用的容器注册表必须已启用管理员帐户。 有关如何启用管理员帐户的信息，请参阅[管理员帐户](/azure/container-registry/container-registry-authentication#admin-account)。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

有关模板的详细信息，请参阅以下文章：

* [创作 Azure Resource Manager 模板](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices 资源类型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>部署模板

若要部署模板，你必须创建一个资源组。

如果喜欢使用图形用户界面，请参阅[Azure 门户](#use-the-azure-portal)部分。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

成功创建资源组后，请通过以下命令部署模板：

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

默认情况下，作为模板的一部分创建的所有资源都是新的。 不过，您也可以选择使用现有资源。 通过向模板提供其他参数，可以使用现有资源。 例如，如果你想要使用现有的存储帐户，请将**storageAccountOption**值设置为 "**现有**"，并在**storageAccountName**参数中提供存储帐户的名称。

> [!IMPORTANT]
> 如果要使用现有的 Azure 存储帐户，则该帐户不能是高级帐户 (Premium_LRS 并 Premium_GRS) 。 它也不能 (与 Azure Data Lake Storage Gen2) 一起使用的分层命名空间。 工作区的默认存储帐户不支持高级存储或分层命名空间。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>部署加密的工作区

以下示例模板演示如何创建具有三项设置的工作区：

* 启用工作区的高保密性设置
* 启用工作区加密
* 使用现有的 Azure Key Vault 检索客户管理的密钥

> [!IMPORTANT]
> 创建工作区后，无法更改机密数据、加密、密钥保管库 ID 或密钥标识符的设置。 要更改这些值，必须使用新值创建新工作区。

有关详细信息，请参阅[静态加密](concept-enterprise-security.md#encryption-at-rest)。

> [!IMPORTANT]
> 在使用此模板之前，订阅必须满足一些特定要求：
>
> * “Azure 机器学习”应用程序必须是 Azure 订阅的“参与者” 。
> * 你必须具有包含加密密钥的现有 Azure Key Vault。
> * Azure Key Vault 中必须具有访问策略，该策略授予对 Azure Cosmos DB 应用程序的“获取”、“包装”和“解包”访问权限   。
> * Azure Key Vault 必须位于计划创建 Azure 机器学习工作区的同一区域。

要将 Azure 机器学习应用作为参与者添加，请使用以下命令：

1. 登录到 Azure 帐户并获取订阅 ID。 此订阅必须与包含 Azure 机器学习工作区的订阅相同。  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az account list --query '[].[name,id]' --output tsv
    ```

    > [!TIP]
    > 若要选择另一个订阅，请使用 `az account set -s <subscription name or ID>` 命令并指定要切换到的订阅名称或 ID。 有关订阅选择的详细信息，请参阅[使用多个 Azure 订阅](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzSubscription
    ```

    > [!TIP]
    > 若要选择另一个订阅，请使用 `Az-SetContext -SubscriptionId <subscription ID>` 命令并指定要切换到的订阅名称或 ID。 有关订阅选择的详细信息，请参阅[使用多个 Azure 订阅](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps?view=azps-4.3.0)。

    ---

1. 要获取 Azure 机器学习应用的对象 ID，请使用以下命令。 对于每个 Azure 订阅，该值可能不同：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Machine Learning" | select-object DisplayName, Id
    ```

    ---
    此命令返回对象 ID，该 ID 为 GUID。

1. 要将对象 ID 作为参与者添加到订阅，请使用以下命令。 替换 `<object-ID>` 为服务主体的对象 ID。 将 `<subscription-ID>` 替换为 Azure 订阅的名称或 ID：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    New-AzRoleAssignment --ObjectId <object-ID> --RoleDefinitionName "Contributor" -Scope /subscriptions/<subscription-ID>
    ```

    ---

1. 若要在现有 Azure Key Vault 中生成密钥，请使用以下命令之一。 替换 `<keyvault-name>` 为密钥保管库的名称。 替换为 `<key-name>` 要用于该项的名称：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az keyvault key create --vault-name <keyvault-name> --name <key-name> --protection software
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Add-AzKeyVaultKey -VaultName <keyvault-name> -Name <key-name> -Destination 'Software'
    ```
    --- 

__要向密钥保管库添加访问策略，请使用以下命令__：

1. 要获取 Azure Cosmos DB 应用的对象 ID，请使用以下命令。 对于每个 Azure 订阅，该值可能不同：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Cosmos DB" | select-object DisplayName, Id
    ```
    ---

    此命令返回对象 ID，该 ID 为 GUID。 稍后保存

1. 要设置策略，请使用以下命令。 将 `<keyvault-name>` 替换为现有的 Azure Key Vault 的名称。 将 `<object-ID>` 替换为上一步中的 GUID：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    
    ```azurepowershell
    Set-AzKeyVaultAccessPolicy -VaultName <keyvault-name> -ObjectId <object-ID> -PermissionsToKeys get, unwrapKey, wrapKey
    ```
    ---    

要获取此模板所需的 `cmk_keyvault`（Key Vault 的 ID）和 `resource_cmk_uri`（密钥 URI）参数的值，请执行以下操作：

1. 要获取 Key Vault ID，请使用以下命令：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az keyvault show --name <keyvault-name> --query 'id' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'
    ```
    ---

    此命令会返回类似于 `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` 的值。

1. 要获取客户托管密钥的 URI 值，请使用以下命令：

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>'
    ```
    ---

    此命令会返回类似于 `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` 的值。

> [!IMPORTANT]
> 创建工作区后，无法更改机密数据、加密、密钥保管库 ID 或密钥标识符的设置。 要更改这些值，必须使用新值创建新工作区。

成功完成上述步骤后，即可像往常一样部署模板。 若要允许使用客户托管密钥，请设置以下参数：

* **Encryption_status** **启用**。
* **cmk_keyvault**到 `cmk_keyvault` 前面步骤中获取的值。
* **resource_cmk_uri**到 `resource_cmk_uri` 前面步骤中获取的值。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

使用客户管理的密钥时，Azure 机器学习会创建一个包含 Cosmos DB 实例的辅助资源组。 有关详细信息，请参阅[静态加密-Cosmos DB](concept-enterprise-security.md#encryption-at-rest)。

可为数据提供的其他配置是将**confidential_data**参数设置为**true**。 这样做会执行以下操作：

* 开始加密 Azure 机器学习计算群集的本地暂存磁盘，并提供未在订阅中创建任何以前的群集。 如果你之前在订阅中创建了一个群集，请打开支持票证，以便为你的计算群集启用暂存磁盘的加密。
* 清除运行之间的本地暂存磁盘。
* 使用 key vault，将存储帐户、容器注册表和 SSH 帐户的凭据从执行层安全传递到计算群集。
* 启用 IP 筛选，以确保不会由 AzureMachineLearningService 以外的任何外部服务调用基础批处理池。

    > [!IMPORTANT]
    > 创建工作区后，无法更改机密数据、加密、密钥保管库 ID 或密钥标识符的设置。 要更改这些值，必须使用新值创建新工作区。

  有关详细信息，请参阅[静态加密](concept-enterprise-security.md#encryption-at-rest)。

## <a name="deploy-workspace-behind-a-virtual-network"></a>在虚拟网络后部署工作区

通过将 `vnetOption` 参数值设置为 `new` 或，可以 `existing` 创建虚拟网络后面的工作区使用的资源。

> [!IMPORTANT]
> 对于容器注册表，仅支持 "高级" sku。

> [!IMPORTANT]
> Application Insights 不支持虚拟网络后的部署。

### <a name="only-deploy-workspace-behind-private-endpoint"></a>仅将工作区部署到专用终结点后面

如果关联的资源不在虚拟网络后面，则可以将**privateEndpointType**参数设置为 `AutoAproval` 或， `ManualApproval` 以便将工作区部署到专用终结点之后。 此操作可用于新的和现有的工作区。 更新现有工作区时，请使用现有工作区中的信息填充模板参数。

> [!IMPORTANT]
> 部署仅在支持专用终结点的区域内有效。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>使用新的虚拟网络

若要在新的虚拟网络后面部署资源，请将**vnetOption**设置为 "新建"，并将相应资源的虚拟网络设置设置为 "**新建**"。 以下部署显示了如何使用新的虚拟网络后面的存储帐户资源部署工作区。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

或者，你可以在虚拟网络后部署多个或所有从属资源。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>使用现有的虚拟网络 & 资源

若要使用现有的关联资源部署工作区，你必须将**vnetOption**参数设置为 "**现有**" 以及子网参数。 但是，在部署**之前**，需要在虚拟网络中为每个资源创建服务终结点。 与新的虚拟网络部署类似，你可以在虚拟网络后使用一个或全部资源。

> [!IMPORTANT]
> 子网应具有 `Microsoft.Storage` 服务终结点

> [!IMPORTANT]
> 子网不允许创建专用终结点。 禁用专用终结点以启用子网。

1. 为资源启用服务终结点。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. 部署工作区

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 遵循[从自定义模板部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)中的步骤。 当你到达 "__选择模板__" 屏幕时，从下拉列表中选择 " **201-计算机-高级**" 模板。
1. 选择 "__选择模板__" 以使用模板。 根据你的部署方案，提供以下所需的信息和任何其他参数。

   * 订阅：选择用于这些资源的 Azure 订阅。
   * 资源组：选择或创建一个用于包含服务的资源组。
   * 区域：选择将在其中创建资源的 Azure 区域。
   * 工作区名称：要创建的 Azure 机器学习工作区所用的名称。 工作区名称的长度必须为 3 到 33 个字符。 只能包含字母数字字符和“-”。
   * 位置：选择要在其中创建资源的位置。
1. 选择“查看 + 创建”。
1. 在 "__查看__" 和 "创建" 屏幕中，同意列出的条款和条件，然后选择 "__创建__"。

有关详细信息，请参阅[从自定义模板部署资源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="troubleshooting"></a>疑难解答

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 访问策略和 Azure 资源管理器模板

使用 Azure 资源管理器模板多次创建工作区和关联的资源（包括 Azure Key Vault）时。 例如，在持续集成和部署管道过程中，对同一参数多次使用模板。

大多数通过模板的资源创建操作都是幂等的，但 Key Vault 每次使用模板时都将清除访问策略。 清除访问策略会中断任何使用该访问的现有工作区对 Key Vault 的访问。 例如，Azure Notebooks VM 的停止/创建功能可能会失败。  

若要避免此问题，我们建议运用以下方法之一：

* 请不要对同一个参数多次部署模板。 或是在使用模板重新创建之前删除现有资源。

* 检查 Key Vault 访问策略，然后使用这些策略设置模板的 `accessPolicies` 属性。 若要查看访问策略，请使用以下 Azure CLI 命令：

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    若要详细了解如何使用模板的 `accessPolicies` 部分，请参阅 [AccessPolicyEntry 对象参考](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)。

* 查看 Key Vault 资源是否已存在。 如果是这样，请不要通过模板重新创建它。 例如，若要使用现有 Key Vault 而不是创建一个新的，请对模板进行以下更改：

    * **添加**一个参数，该参数接受现有 Key Vault 资源的 ID：

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **删除**用于创建 Key Vault 资源的部分：

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

    * 从工作区的 `dependsOn` 部分**删除** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 行。 另请**更改**工作区的 `properties` 部分中的 `keyVault` 条目，使之引用 `keyVaultId` 参数：

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

    完成这些更改后，可以在运行模板时指定现有 Key Vault 资源的 ID。 然后，模板会通过将工作区的 `keyVault` 属性设置为其 ID 来重用 Key Vault。

    若要获取 Key Vault 的 ID，可以引用原始模板运行的输出或使用 Azure CLI。 以下命令是使用 Azure CLI 获取 Key Vault 资源 ID 的示例：

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    此命令返回类似于以下文本的值：

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>虚拟网络未链接到专用 DNS 区域

使用专用终结点创建工作区时，模板会创建一个名为__privatelink.api.azureml.ms__的专用 DNS 区域。 __虚拟网络链接__自动添加到此专用 DNS 区域。 仅为在资源组中创建的第一个工作区和专用终结点添加链接;如果使用同一资源组中的专用终结点创建另一个虚拟网络和工作区，则可能无法将第二个虚拟网络添加到专用 DNS 区域。

若要查看专用 DNS 区域已存在的虚拟网络链接，请使用以下 Azure CLI 命令：

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

若要添加包含其他工作区和专用终结点的虚拟网络，请使用以下步骤：

1. 若要查找要添加的网络的虚拟网络 ID，请使用以下命令：

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    此命令返回类似于 ""/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet "" 的值。 保存此值并在下一步中使用它。

2. 若要将虚拟网络链接添加到 privatelink.api.azureml.ms 专用 DNS 区域，请使用以下命令。 对于 `--virtual-network` 参数，请使用上一命令的输出：

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>后续步骤

* [使用资源管理器模板和资源管理器 REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)。
* [通过 Visual Studio 创建和部署 Azure 资源组](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
* [有关与 Azure 机器学习相关的其他模板，请参阅 Azure 快速入门模板存储库](https://github.com/Azure/azure-quickstart-templates)
