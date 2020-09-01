---
title: 将 Azure VM 移到新的订阅或资源组
description: 使用 Azure 资源管理器将虚拟机移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230953"
---
# <a name="move-guidance-for-virtual-machines"></a>针对虚拟机的移动指南

本文介绍当前不支持的方案以及移动使用备份的虚拟机的步骤。

## <a name="scenarios-not-supported"></a>不支持的方案

以下方案尚不受支持：

* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 不能跨订阅移动从具有附加计划的 Marketplace 资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。
* 如果没有移动虚拟网络中的所有资源，则无法将现有虚拟网络中的虚拟机移到新订阅。
* 低优先级虚拟机和低优先级虚拟机规模集不能在资源组或订阅之间移动。
* 可用性集中的虚拟机不能单独移动。

## <a name="azure-disk-encryption"></a>Azure 磁盘加密

不能移动与密钥保管库集成的虚拟机来实现适用于 [Linux vm 的 Azure 磁盘加密](../../../virtual-machines/linux/disk-encryption-overview.md) 或 [适用于 Windows Vm 的 azure 磁盘加密](../../../virtual-machines/windows/disk-encryption-overview.md)。 若要移动 VM，必须禁用加密。

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
   4. 查找类型为 Microsoft.Compute/restorePointCollections 的资源，其命名模式为 `AzureBackup_<name of your VM that you're trying to move>_###########`。
   5. 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
   6. 删除操作完成后，可以移动虚拟机。

3. 将 VM 移到目标资源组。
4. 恢复备份。

### <a name="powershell"></a>PowerShell

1. 找到虚拟机的位置。

1. 查找命名模式为的资源组 `AzureBackupRG_<VM location>_1` 。 例如，名称可能是 `AzureBackupRG_westus2_1` 。

1. 使用以下命令获取还原点集合。

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. 找到虚拟机的位置。

1. 查找命名模式为的资源组 `AzureBackupRG_<VM location>_1` 。 例如，名称可能是 `AzureBackupRG_westus2_1` 。

1. 使用以下命令获取还原点集合。

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. 查找命名模式为的资源的资源 ID `AzureBackup_<VM name>_###########`

1. 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>后续步骤

* 有关用于移动资源的命令，请参阅[将资源移到新资源组或订阅](../move-resource-group-and-subscription.md)。

* 若要了解如何移动恢复服务保管库以完成备份，请参阅[恢复服务限制](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。
