---
title: 将 Azure Vm 移到新的订阅或资源组
description: 使用 Azure 资源管理器将虚拟机移动到新的资源组或订阅。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tomfitz
ms.openlocfilehash: faeba1c0d7342a4c00f19d4cee8d67b8dbde8e6a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528427"
---
# <a name="move-guidance-for-virtual-machines"></a>适用于虚拟机的移动指南

本文介绍目前尚不支持的方案以及将虚拟机与备份一起移动的步骤。

## <a name="scenarios-not-supported"></a>不支持的方案

以下方案尚不受支持：

* 可用性区域中的托管磁盘不能移动到其他订阅。
* 不能移动标准 SKU 负载均衡器或标准 SKU 公共 IP 的虚拟机规模集。
* 无法跨资源组或订阅移动基于附加了计划的市场资源创建的虚拟机。 取消预配当前订阅中的虚拟机，并在新订阅中再次部署。
* 不移动虚拟网络中的所有资源时，无法将现有虚拟网络中的虚拟机移动到新的订阅。
* 低优先级虚拟机和低优先级虚拟机规模集不能在资源组或订阅之间移动。
* 可用性集中的虚拟机不能单独移动。

## <a name="virtual-machines-with-azure-backup"></a>具有 Azure 备份的虚拟机

若要移动使用 Azure 备份配置的虚拟机，请使用以下解决方法：

* 找到虚拟机的位置。
* 找到含有以下命名模式的资源组：`AzureBackupRG_<location of your VM>_1` 例如，AzureBackupRG_westus2_1
* 如果在 Azure 门户中，则查看“显示隐藏的类型”
* 如果在 PowerShell 中，则使用 `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* 如果在 CLI 中，则使用 `az resource list -g AzureBackupRG_<location of your VM>_1`
* 使用类型 `Microsoft.Compute/restorePointCollections` 找到具有命名模式 `AzureBackup_<name of your VM that you're trying to move>_###########` 的资源
* 删除此资源。 此操作仅删除即时恢复点，不删除保管库中的备份数据。
* 删除完成后，可以将保管库和虚拟机移动到目标订阅。 移动后即可继续备份，不会丢失数据。
* 若要了解如何移动恢复服务保管库以完成备份，请参阅[恢复服务限制](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../resource-group-move-resources.md)。
