---
title: 将 Azure VM 移到新的订阅或资源组
description: 使用 Azure 资源管理器将虚拟机移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 219a8b438d2715f6e97085a527b386e51759ec2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317100"
---
# <a name="move-guidance-for-virtual-machines"></a>针对虚拟机的移动指南

本文介绍当前不支持的方案以及移动使用备份的虚拟机的步骤。

## <a name="scenarios-not-supported"></a>不支持的方案

以下方案尚不受支持：

* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 无法跨订阅移动基于附加了计划的市场资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。
* 如果没有移动虚拟网络中的所有资源，则无法将现有虚拟网络中的虚拟机移到新订阅。
* 低优先级虚拟机和低优先级虚拟机规模集不能在资源组或订阅之间移动。
* 可用性集中的虚拟机不能单独移动。

## <a name="azure-disk-encryption"></a>Azure 磁盘加密

无法移动与密钥保管库集成的虚拟机以实现[适用于 Linux VM 的 Azure 磁盘加密](../../../virtual-machines/linux/disk-encryption-overview.md)或[适用于 Windows VM 的 Azure 磁盘加密](../../../virtual-machines/windows/disk-encryption-overview.md)。 若要移动 VM，必须禁用加密。

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>使用 Azure 备份的虚拟机

若要移动配置了 Azure 备份的虚拟机，必须从保管库中删除还原点。

如果为虚拟机启用了[软删除](../../../backup/backup-azure-security-feature-cloud.md)，则在保留这些还原点的情况下，你将无法移动虚拟机。 请[禁用软删除](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)，或在删除还原点后等待 14 天。

### <a name="portal"></a>门户

1. 暂时停止备份并保留备份数据。
2. 若要移动配置了 Azure 备份的虚拟机，请执行以下步骤：

   1. 找到虚拟机的位置。
   2. 找到包含以下命名模式的资源组：`AzureBackupRG_<VM location>_1`。 例如，名称的格式为 *AzureBackupRG_westus2_1*。
   3. 在 Azure 门户中，查看“显示隐藏的类型”。
   4. 查找类型为 Microsoft.Compute/restorePointCollections 的资源，其命名模式为 `AzureBackup_<VM name>_###########`。
   5. 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
   6. 删除操作完成后，可以移动虚拟机。

3. 将 VM 移到目标资源组。
4. 恢复备份。

### <a name="powershell"></a>PowerShell

1. 找到虚拟机的位置。

1. 找到采用以下命名模式的资源组：`AzureBackupRG_<VM location>_1`。 例如，名称可以为 `AzureBackupRG_westus2_1`。

1. 如果只移动一个虚拟机，请获取该虚拟机的还原点集合。

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. 如果要在此位置将所有虚拟机与备份一起移动，请获取这些虚拟机的还原点集合。

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   删除每个资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. 找到虚拟机的位置。

1. 找到采用以下命名模式的资源组：`AzureBackupRG_<VM location>_1`。 例如，名称可以为 `AzureBackupRG_westus2_1`。

1. 如果只移动一个虚拟机，请获取该虚拟机的还原点集合。

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. 如果要在此位置将所有虚拟机与备份一起移动，请获取这些虚拟机的还原点集合。

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   删除每个资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>后续步骤

* 有关用于移动资源的命令，请参阅[将资源移到新资源组或订阅](../move-resource-group-and-subscription.md)。

* 若要了解如何移动恢复服务保管库以完成备份，请参阅[恢复服务限制](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。
