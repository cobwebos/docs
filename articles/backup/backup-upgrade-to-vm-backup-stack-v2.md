---
title: 升级到 Azure VM 备份堆栈 V2
description: 有关 VM 备份堆栈、资源管理器部署模型的升级过程和常见问题解答
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 8/1/2018
ms.author: trinadhk
ms.openlocfilehash: 1021900620272cc5476d8972daf9d7e0a161797a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397995"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>升级到 Azure VM 备份堆栈 V2

用于升级到虚拟机 (VM) 备份堆栈的资源管理器部署模型提供以下增强功能：

* 可以查看在执行备份作业期间创建的用于恢复的快照，而无需等待数据传输完成。 它缩短了快照在触发还原之前复制到保管库的等待时间。 此外，借助这项增强功能，无需添置额外的存储来备份高级 VM（首次备份除外）。  

* 可在本地保留快照 7 天，缩短了备份和还原时间。

* 最大支持 4 TB 的磁盘。

* 还原时可使用非托管 VM 的原始存储帐户。 即使 VM 的磁盘跨存储帐户进行分布，也具备此能力。 这可以加快各种 VM 配置的还原操作。
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

* VM 备份堆栈的升级是单向升级，所有备份都会进入此流。 由于更改发生在订阅级别，因此所有 VM 都会进入此流。 所有新功能补充都基于同一个堆栈。 目前无法在策略级别控制此堆栈。

* 快照将存储在本地，以提高恢复点的创建速度并加快还原操作。 因此，可看到七天内创建的快照的相应存储成本。

* 增量快照作为页 blob 存储。 将对使用非托管磁盘的所有客户收取快照存储在客户本地存储帐户 7 天的费用。 由于托管 VM 备份使用的还原点集合在基础存储级别使用 blob 快照，因此对于托管磁盘，你将看到与 [blob 快照定价](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges)对应的成本，并且它们是递增的。 

* 如果从快照恢复点还原高级 VM，则会在创建 VM 时使用临时存储位置。

* 对于高级存储帐户，为即时恢复点创建的快照计入分配空间的 10-TB 限制。

## <a name="upgrade"></a>升级
### <a name="the-azure-portal"></a>Azure 门户
如果使用 Azure 门户，则会在保管库仪表板上看到通知。 此通知涉及对大磁盘的支持以及备份和还原速度的改进。 你也可以转到保管库的“属性”页面来获取升级选项。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 支持通知](./media/backup-azure-vms/instant-rp-banner.png) 

若要打开相应的屏幕以升级到新堆栈，请选择横幅。 

![VM 备份堆栈资源管理器部署模型中的备份作业 - 升级](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
从权限提升的 PowerShell 终端运行以下 cmdlet：
1.  请登录到 Azure 帐户： 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  选择要注册的订阅：

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  注册此订阅：

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>验证升级是否已完成
在权限提升的 PowerShell 终端中运行以下 cmdlet：

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

如果输出中显示“Registered”，则表示订阅已升级到 VM 备份堆栈资源管理器部署模型。

## <a name="frequently-asked-questions"></a>常见问题

我们从论坛和客户问题中收集了以下问题及解答。

### <a name="will-upgrading-to-v2-impact-current-backups"></a>升级到 V2 会影响当前备份吗？

升级到 V2 对当前备份没有影响，也无需重新配置环境。 升级后，备份环境将继续正常工作。

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>升级到 Azure VM 备份堆栈 v2 需要多少费用？

将堆栈升级到 v2 无需任何费用。 快照将存储在本地，以加快恢复点创建和还原操作。 因此，可看到七天内创建的快照的相应存储成本。

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>升级到堆栈 v2 是否会将高级存储帐户快照限制增加 10 TB？

否，每个存储帐户的总快照限制仍保留为 10 TB。 

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在高级存储帐户中，为即时恢复点创建的快照是否会占用 10 TB 快照限制？

是的，对于高级存储帐户，为即时恢复点创建的快照会占用 10 TB 的分配空间。

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>快照在七天内如何运作？ 

每天创建一个新的快照。 一共有七个单独的快照。 该服务在第一天**不会**创建副本，并在接下来六天添加更改。

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>v2 快照是增量快照还是完整快照？

增量快照用于非托管磁盘。 对于托管磁盘，Azure 备份创建的还原点集合使用 blob 快照，因此是增量快照。 
