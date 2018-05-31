---
title: 升级到 Azure VM 备份堆栈的 Azure 资源管理器部署模型 | Microsoft Docs
description: 有关 VM 备份堆栈、资源管理器部署模型的升级过程和常见问题解答
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 1e5515486afac5a6d84a35bca33f55ae98e287d3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764853"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>升级到 Azure VM 备份堆栈的 Azure 资源管理器部署模型
用于升级到虚拟机 (VM) 备份堆栈的资源管理器部署模型提供以下增强功能：
* 可以查看在执行备份作业期间创建的用于恢复的快照，而无需等待数据传输完成。 它缩短了快照在触发还原之前复制到保管库的等待时间。 此外，借助这项增强功能，无需添置额外的存储来备份高级 VM（首次备份除外）。  

* 可在本地保留快照 7 天，缩短了备份和还原时间。

* 最大支持 4 TB 的磁盘。

* 还原时可使用非托管 VM 的原始存储帐户。 即使 VM 的磁盘跨存储帐户进行分布，也具备此能力。 这可以加快各种 VM 配置的还原速度。
    > [!NOTE] 
    > 此能力与重写原始 VM 不同。 
    >

## <a name="whats-changing-in-the-new-stack"></a>新堆栈有哪些变化？
目前，备份作业包括两个阶段：
1.  获取 VM 快照。 
2.  将 VM 快照传输到 Azure 备份保管库。 

只在完成阶段 1 和 2 之后，才认为已创建恢复点。 在新堆栈中，一旦完成快照，就会立即创建一个恢复点。 也可使用相同的还原流从此恢复点进行还原。 可使用“快照”作为恢复点类型，在 Azure 门户中识别此恢复点。 将快照传输到保管库后，恢复点类型将更改为“快照和保管库”。 

![VM 备份堆栈资源管理器部署模型中的备份作业 - 存储和保管库](./media/backup-azure-vms/instant-rp-flow.jpg) 

默认情况下，快照保留 7 天。 借助此功能，可从这些快照中更快完成还原操作。 这可缩短将数据从保管库复制回客户存储帐户所需的时间。 

## <a name="considerations-before-upgrade"></a>升级前的注意事项
* VM 备份堆栈的升级是单向升级。 所有备份都会进入此流。 因为在订阅级别启用，所有 VM 都会进入此流。 所有新功能补充都基于同一个堆栈。 将来的版本将会支持在策略级别控制此功能。

* 将快照存储在本地以提高恢复点的创建速度并加速还原。 因此，可在七天内看到与快照对应的存储成本。

* 增量快照作为页 blob 存储。 将对使用非托管磁盘的所有客户收取快照存储在客户本地存储帐户 7 天的费用。 根据当前的定价模型，不对托管磁盘上的客户收费。

* 如果从快照恢复点执行高级 VM 还原操作，在还原过程中创建 VM 时，会看到使用了一个临时存储位置。

* 对于高级存储帐户，为即时恢复创建的快照占用 10 TB 的分配空间。

## <a name="upgrade"></a>升级
### <a name="the-azure-portal"></a>Azure 门户
如果使用 Azure 门户，则会在保管库仪表板上看到通知。 此通知涉及对大磁盘的支持以及备份和还原速度的改进。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 支持通知](./media/backup-azure-vms/instant-rp-banner.png) 

若要打开相应的屏幕以升级到新堆栈，请选择横幅。 

![VM 备份堆栈资源管理器部署模型中的备份作业 - 升级](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
从权限提升的 PowerShell 终端运行以下 cmdlet：
1.  请登录到 Azure 帐户： 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  选择要注册其预览版的订阅：

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  注册此订阅的个人预览版：

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>验证升级是否已完成
在权限提升的 PowerShell 终端中运行以下 cmdlet：

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

如果输出中显示“Registered”，则表示订阅已升级到 VM 备份堆栈资源管理器部署模型。
