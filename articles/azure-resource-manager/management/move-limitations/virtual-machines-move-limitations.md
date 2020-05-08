---
title: 将 Azure VM 移到新的订阅或资源组
description: 使用 Azure 资源管理器将虚拟机移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744637"
---
# <a name="move-guidance-for-virtual-machines"></a>针对虚拟机的移动指南

本文介绍当前不支持的方案以及移动使用备份的虚拟机的步骤。

## <a name="scenarios-not-supported"></a>不支持的方案

以下方案尚不受支持：

* 可用性区域中的托管磁盘不能移动到其他订阅。
* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 无法跨资源组或订阅移动基于附加了计划的市场资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。
* 如果没有移动虚拟网络中的所有资源，则无法将现有虚拟网络中的虚拟机移到新订阅。
* 低优先级虚拟机和低优先级虚拟机规模集不能在资源组或订阅之间移动。
* 可用性集中的虚拟机不能单独移动。

## <a name="virtual-machines-with-azure-backup"></a>使用 Azure 备份的虚拟机

若要移动配置了 Azure 备份的虚拟机，你必须从保管库中删除还原点。

如果为虚拟机启用[软删除](../../../backup/backup-azure-security-feature-cloud.md)，则在保留这些还原点时，你无法移动虚拟机。 请在删除还原点后[禁用软删除](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)或等待14天。

### <a name="portal"></a>门户

1. 暂时停止备份并保留备份数据。
2. 若要移动配置了 Azure 备份的虚拟机，请执行以下步骤：

   1. 查找虚拟机的位置。
   2. 使用以下命名模式查找资源组： `AzureBackupRG_<location of your VM>_1`。 例如， *AzureBackupRG_westus2_1*
   3. 在 Azure 门户中，选中 "**显示隐藏的类型**"。
   4. 查找类型为 " **restorePointCollections** " 的资源，该资源具有命名模式`AzureBackup_<name of your VM that you're trying to move>_###########`。
   5. 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
   6. 删除操作完成后，可以移动虚拟机。

3. 将 VM 移动到目标资源组。
4. 恢复备份。

### <a name="powershell"></a>PowerShell

* 找到虚拟机的位置。
* 找到含有以下命名模式的资源组：`AzureBackupRG_<location of your VM>_1` 例如，AzureBackupRG_westus2_1
* 如果在 PowerShell 中，则使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* 使用类型 `Microsoft.Compute/restorePointCollections` 找到具有命名模式 `AzureBackup_<name of your VM that you're trying to move>_###########` 的资源
* 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

### <a name="azure-cli"></a>Azure CLI

* 找到虚拟机的位置。
* 找到含有以下命名模式的资源组：`AzureBackupRG_<location of your VM>_1` 例如，AzureBackupRG_westus2_1
* 如果在 CLI 中，则使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 使用类型 `Microsoft.Compute/restorePointCollections` 找到具有命名模式 `AzureBackup_<name of your VM that you're trying to move>_###########` 的资源
* 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。

## <a name="next-steps"></a>后续步骤

* 有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../move-resource-group-and-subscription.md)。

* 若要了解如何移动恢复服务保管库以完成备份，请参阅[恢复服务限制](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。
