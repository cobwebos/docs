---
title: 有关使用 Azure 资源管理器模板创建 Azure 恢复服务保管库的快速入门。
description: 本快速入门介绍如何使用 Azure 资源管理器模板创建 Azure 恢复服务保管库。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
author: davidsmatlak
ms.author: v-dasmat
ms.openlocfilehash: 47c25ebd0fe18d470b04ccbcc85a8638c1ce0346
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598380"
---
# <a name="quickstart-create-a-recovery-services-vault-using-a-resource-manager-template"></a>快速入门：使用资源管理器模板创建恢复服务保管库

本快速入门介绍如何使用 Azure 资源管理器模板设置恢复服务保管库。 [Azure Site Recovery](site-recovery-overview.md) 服务有助于实施业务连续性和灾难恢复 (BCDR) 策略，以便在计划内和计划外中断期间使商业应用程序保持联机状态。 Site Recovery 管理本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果没有有效的 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

无。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/)。

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json" range="1-66" highlight="41-65":::

该模板中定义了两个 Azure 资源：

- [Microsoft.RecoveryServices 保管库](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)：创建保管库。
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs)：配置保管库的备份冗余设置。

该模板包含保管库备份配置的可选参数。 存储冗余设置是本地冗余存储 (LRS) 或异地冗余存储 (GRS)。 有关详细信息，请参阅[设置存储冗余](../backup/backup-create-rs-vault.md#set-storage-redundancy)。

有关 Azure 恢复服务模板的详细信息，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)。

### <a name="deploy-the-template"></a>部署模板

若要部署模板，需要指定“订阅”、“资源组”和“保管库名称”    。

1. 若要登录到 Azure 并打开模板，请选择“部署到 Azure”图像  。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. 选择或输入以下值：

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="用于创建恢复服务保管库的模板。":::

   - **订阅**：选择 Azure 订阅。
   - **资源组**：选择现有的组，或选择“新建”以添加组  。
   - **位置**：默认为资源组的位置，选择资源组后将不可用。
   - **保管库名称**：提供保管库的名称。
   - **更改存储类型**：默认值为 **false**。 只有在需要更改保管库的存储类型时，才选择“true”  。
   - **保管库存储类型**：默认值为“GloballyRedundant”  。 如果存储类型设置为“true”，请选择“LocallyRedundant”   。
   - **位置**：函数 `[resourceGroup().location]` 默认为资源组的位置。 若要更改位置，请输入一个值，例如 westus  。
   - 选中“我同意上述条款和条件”复选框。 

1. 若要开始部署保管库，请选择“购买”按钮  。 成功部署后，会显示一条通知。

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="保管库部署成功。":::

## <a name="validate-the-deployment"></a>验证部署

若要确认是否已创建保管库，请使用 Azure CLI 或 Azure PowerShell。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

以下输出是保管库的信息摘录：

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>清理资源

如果打算使用新资源，则无需执行任何操作。 否则，可以删除在本快速入门中创建的资源组和保管库。 若要删除资源组及其资源，请使用 Azure CLI 或 Azure PowerShell。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已创建一个恢复服务保管库。 若要详细了解灾难恢复，请继续学习下一篇快速入门文章。

> [!div class="nextstepaction"]
> [设置灾难恢复](azure-to-azure-quickstart.md)
