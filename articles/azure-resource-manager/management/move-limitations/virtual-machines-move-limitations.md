---
title: 将 Azure VM 移到新的订阅或资源组
description: 使用 Azure 资源管理器将虚拟机移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: df34268b7741f76621c290e9979cf24d828ddc09
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478665"
---
# <a name="move-guidance-for-virtual-machines"></a>针对虚拟机的移动指南

本文介绍当前不支持的方案以及移动使用备份的虚拟机的步骤。

## <a name="scenarios-not-supported"></a>不支持的方案

以下方案尚不受支持：

* 无法将可用区域中的托管磁盘移动到其他订阅。
* 无法移动具有标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 无法跨资源组或订阅移动基于附加了计划的市场资源创建的虚拟机。 在当前订阅中取消预配虚拟机，并在新的订阅中重新部署虚拟机。
* 如果没有移动虚拟网络中的所有资源，则无法将现有虚拟网络中的虚拟机移到新订阅。
* 低优先级虚拟机和低优先级虚拟机规模集无法跨资源组或订阅移动。
* 可用性集中的虚拟机不能单独移动。

## <a name="virtual-machines-with-azure-backup"></a>使用 Azure 备份的虚拟机

要移动配置 Azure 备份的虚拟机，必须从保管库中删除还原点。

如果为虚拟机启用[了软删除](../../../backup/backup-azure-security-feature-cloud.md)，则在保留这些还原点时无法移动虚拟机。 [禁用软删除](../../../backup/backup-azure-security-feature-cloud.md#disabling-soft-delete)或删除还原点后等待 14 天。

### <a name="portal"></a>门户

1. 选择配置为备份的虚拟机。

1. 在左侧窗格中，选择 **"备份**"。

1. 选择 **"停止备份**"。

1. 选择 **"删除回数据**"。

1. 删除完成后，您可以将保管库和虚拟机移动到目标订阅。 移动后，您可以继续备份。

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
