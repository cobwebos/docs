---
title: 创建和部署模板规格
description: 了解如何通过 ARM 模板创建模板规格。 然后，将模板规格部署到订阅中的资源组。
author: tfitzmac
ms.date: 08/06/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 8fe9ec46050ad831430239b960a7f528af7f4dc2
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924319"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>快速入门：创建和部署模板规格（预览）

本快速入门介绍如何将 Azure 资源管理器模板（ARM 模板）打包为[模板规格](template-specs.md)，然后部署该模板规格。模板规格包含用于部署存储帐户的 ARM 模板。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 模板规格当前提供预览版。 若要使用它，必须[注册等待列表](https://aka.ms/templateSpecOnboarding)。
>
> 从等待列表获得批准后，你将获得有关安装预览版 PowerShell 模块的说明。

## <a name="create-template-spec"></a>创建模板规格

模板规格是名为 Microsoft.Resources/templateSpecs 的新资源类型。 若要创建模板规格，你可以使用 Azure PowerShell 或 ARM 模板。 在所有选项中，你都需要打包在模板规格中的 ARM 模板。

使用 PowerShell 时，ARM 模板作为参数传递给命令。 对于 ARM 模板，要打包在模板规格中的 ARM 模板嵌入在模板规格定义中。

这些选项如下所示。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 使用 PowerShell 创建模板规格时，可以传入本地模板。 复制以下模板并将其本地保存到名为 azuredeploy.json 的文件中。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

   :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. 创建新的资源组以包含模板规格。

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. 然后，在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

   ```powershell
   New-AzTemplateSpec `
     -ResourceGroupName templateSpecRG `
     -Name storageSpec `
     -Version "1.0" `
     -Location westus2 `
     -TemplateJsonFile "c:\Templates\azuredeploy.json"
   ```

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 使用 ARM 模板创建模板规格时，该模板将嵌入资源定义。 复制以下模板并将其本地保存为 azuredeploy.json。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

   > [!NOTE]
   > 在嵌入的模板中，必须使用第二个左括号对所有左括号进行转义。 请使用 `[[`，而不是 `[`。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/templateSpecs",
               "apiVersion": "2019-06-01-preview",
               "name": "storageSpec",
               "location": "westus2",
               "properties": {
                   "displayName": "Storage template spec"
               },
               "tags": {},
               "resources": [
                   {
                       "type": "versions",
                       "apiVersion": "2019-06-01-preview",
                       "name": "1.0",
                       "location": "westus2",
                       "dependsOn": [ "storageSpec" ],
                       "properties": {
                           "template": {
                               "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                               "contentVersion": "1.0.0.0",
                               "parameters": {
                                   "storageAccountType": {
                                       "type": "string",
                                       "defaultValue": "Standard_LRS",
                                       "allowedValues": [
                                           "Standard_LRS",
                                           "Standard_GRS",
                                           "Standard_ZRS",
                                           "Premium_LRS"
                                       ],
                                       "metadata": {
                                           "description": "Storage Account type"
                                       }
                                   },
                                   "location": {
                                       "type": "string",
                                       "defaultValue": "[[resourceGroup().location]",
                                       "metadata": {
                                           "description": "Location for all resources."
                                       }
                                   }
                               },
                               "variables": {
                                   "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                               },
                               "resources": [
                                   {
                                       "type": "Microsoft.Storage/storageAccounts",
                                       "apiVersion": "2019-04-01",
                                       "name": "[[variables('storageAccountName')]",
                                       "location": "[[parameters('location')]",
                                       "sku": {
                                           "name": "[[parameters('storageAccountType')]"
                                       },
                                       "kind": "StorageV2",
                                       "properties": {}
                                   }
                               ],
                               "outputs": {
                                   "storageAccountName": {
                                       "type": "string",
                                       "value": "[[variables('storageAccountName')]"
                                   }
                               }
                           }
                       },
                       "tags": {}
                   }
               ]
           }
       ],
       "outputs": {}
   }
   ```

1. 使用 Azure CLI 或 PowerShell 创建新的资源组。

   ```azurecli
   az group create \
     --name templateSpecRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name templateSpecRG `
     -Location westus2
   ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

   ```azurecli
   az deployment group create \
     --name templateSpecRG \
     --template-file "c:\Templates\azuredeploy.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName templateSpecRG `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

---

## <a name="deploy-template-spec"></a>部署模板规格

现在即可部署模板规格。部署模板规格就像部署它包含的模板一样，只不过你传入了模板规格的资源 ID。使用相同的部署命令，并在需要时为模板规格传递参数值。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建资源组以包含新的存储帐户。

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. 获取模板规格的资源 ID。

   ```azurepowershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Version.Id
   ```

1. 部署模板规格。

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG
   ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -StorageAccountType Standard_GRS
   ```

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 复制以下模板并将其本地保存到名为 storage.json 的文件中。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "functions": [],
       "variables": {},
       "resources": [
           {
               "type": "Microsoft.Resources/deployments",
               "apiVersion": "2020-06-01",
               "name": "demo",
               "properties": {
                   "templateLink": {
                       "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                   },
                   "parameters": {
                   },
                   "mode": "Incremental"
               }
           }
       ],
       "outputs": {}
   }
   ```

1. 使用 Azure CLI 或 PowerShell 为存储帐户创建新的资源组。

   ```azurecli
   az group create \
     --name storageRG \
     --location westus2
   ```

   ```azurepowershell
   New-AzResourceGroup `
     -Name storageRG `
     -Location westus2
   ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

   ```azurecli
   az deployment group create \
     --name storageRG \
     --template-file "c:\Templates\storage.json"
   ```

   ```azurepowershell
   New-AzResourceGroupDeployment `
     -ResourceGroupName storageRG `
     -TemplateFile "c:\Templates\storage.json"
   ```

---

## <a name="grant-access"></a>授予访问权限

如果要让组织中的其他用户部署模板规格，你需要向他们授予读取权限。 对于包含要共享的模板规格的资源组，你可以将读取者角色分配给相应的 Azure AD 组。 有关详细信息，请参阅[教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门中部署的资源，请删除创建的两个资源组。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。

1. 在“按名称筛选”字段中输入资源组名称（templateSpecRG 和 storageRG）。

1. 选择资源组名称。

1. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

若要了解有关创建包含关联模板的模板规格的信息，请参阅[创建关联模板的模板规格](template-specs-create-linked.md)。
