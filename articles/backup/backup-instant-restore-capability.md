---
title: Azure 即时还原功能
description: Azure 即时还原功能以及有关 VM 备份堆栈、资源管理器部署模型的常见问题解答
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: trinadhk
ms.openlocfilehash: a0cbb8650a818c3ae6c839d69bbc9b94f8d080ca
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351390"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>使用 Azure 备份即时还原功能获得更高的备份和还原性能

> [!NOTE]
> 用户提出 **VM Backup Stack V2** 与 Azure Stack 之间存在混淆的情况，根据这种反馈，我们已将前者重命名为“即时还原”，以便提供升级的更佳体验。

即时还原的新模型提供以下功能增强：

* 可以使用执行备份作业期间创建的用于恢复的快照，而无需等待完成将数据传输到保管库的操作。 它缩短了快照在触发还原之前复制到保管库的等待时间。
* 可将快照连同磁盘保留 7 天，缩短了备份和还原时间。
* 最大支持 4 TB 的磁盘。
* 支持标准 SSD 磁盘。
* 还原时可以使用非托管 VM 的原始存储帐户（按磁盘）。 即使 VM 的磁盘跨存储帐户进行分布，也具备此能力。 这可以加快各种 VM 配置的还原操作。


## <a name="whats-new-in-this-feature"></a>功能亮点

目前，备份作业包括两个阶段：

1.  获取 VM 快照。
2.  将 VM 快照传输到 Azure 恢复服务保管库。

只有在完成阶段 1 和 2 之后，才认为已创建恢复点。 在执行此项升级的过程中，完成快照后会立即创建恢复点，可以在相同的还原流中，使用此快照恢复点类型执行还原。 可以在 Azure 门户中使用“快照”作为恢复点类型来识别此恢复点，快照传输到保管库后，恢复点类型将更改为“快照和保管库”。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 存储和保管库](./media/backup-azure-vms/instant-rp-flow.png)

快照保留 7 天。 此功能允许从保管库中的这些快照执行还原操作，并可缩短还原时间。 对于非托管磁盘方案，此功能减少了转换数据并将数据从保管库复制回到用户存储帐户所需的时间；对于托管磁盘用户，它可以基于备份数据创建托管磁盘。

## <a name="feature-considerations"></a>功能注意事项

* 快照将连同磁盘一起存储，以提高恢复点的创建速度并加快还原操作。 因此，可以查看 7 天内创建的快照的相应存储成本。
* 增量快照作为页 blob 存储。 使用非托管磁盘的所有用户需要为其本地存储帐户中存储的快照付费。 由于托管 VM 备份使用的还原点集合在基础存储级别使用 Blob 快照，因此对于托管磁盘，你将看到与 Blob 快照定价对应的成本，并且成本是递增的。
* 对于高级存储帐户，为即时恢复点创建的快照计入分配空间的 10-TB 限制。

## <a name="cost-impact"></a>成本影响

增量快照存储在 VM 的存储帐户中，用于即时恢复。 使用增量快照意味着快照占用的空间等于创建该快照后写入的页面所占用的空间。 费用仍按快照占用的空间按 GB 计算，每 GB 价格与[定价页](https://azure.microsoft.com/pricing/details/managed-disks/)中所述的价格相同。


## <a name="upgrading-to-instant-restore"></a>升级到即时还原

如果使用 Azure 门户，则会在保管库仪表板上看到通知。 此通知涉及对大磁盘的支持以及备份和还原速度的改进。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 支持通知](./media/backup-azure-vms/instant-rp-banner.png)

若要打开相应的屏幕以升级到即时还原，请选择横幅。

![VM 备份堆栈资源管理器部署模型中的备份作业 - 升级](./media/backup-azure-vms/instant-rp.png)

或者，也可以转到保管库的“属性”页以访问“VM Backup Stack”下的“升级”选项。

![VM Backup Stack 中的备份作业 -“属性”页](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>使用 PowerShell 升级到即时还原

若要自助升级到即时还原，请在权限提升的 PowerShell 终端中运行以下 cmdlet：

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

## <a name="upgrade-to-instant-restore-using-cli"></a>使用 CLI 升级到即时还原

从 shell 运行以下命令：

1.  请登录到 Azure 帐户：

    ```
    az login
    ```

2.  选择要注册的订阅：

    ```
    az account set --subscription "Subscription Name"
    ```

3.  注册此订阅：

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>验证升级是否成功

### <a name="powershell"></a>PowerShell
在权限提升的 PowerShell 终端中运行以下 cmdlet：

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
在 shell 中运行以下命令：

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

如果输出中显示“Registered”，则表示订阅已升级到即时还原。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-are-the-cost-implications-of-instant-restore"></a>哪些因素影响即时还原的成本？
快照将连同磁盘一起存储，以加速恢复点的创建和还原操作。 因此，你会看到与 VM 备份策略中选择的快照保留期相对应的存储成本。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>在高级存储帐户中，为即时恢复点创建的快照是否会占用 10-TB 快照限制？
是的，对于高级存储帐户，为即时恢复点创建的快照会占用 10-TB 的已分配快照空间。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>在 5 天期限内，快照保留的工作方式是怎样的？
如果每天创建一个新快照，则有 5 个单独的增量快照。 快照大小取决于数据变动率，在大多数情况下，变动率大约为 2%-7%。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>即时还原快照是增量快照还是完整快照？
即时还原功能创建的快照是增量快照。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>如何计算即时还原功能大约增加的成本？
这取决于 VM 的变动率。 在稳定状态下，可以假设增加的成本 = 快照保留期 * 每个 VM 的每日变动率 * 每 GB 存储成本。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>如果还原点的恢复类型是“快照和保管库”，而我执行了还原操作，那么，将使用哪种恢复类型？
如果恢复类型是“快照和保管库”，则会从本地快照自动执行还原，与从保管库执行还原相比，其速度要快得多。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>如果选择的还原点（第 2 层）保留期小于快照（第 1 层）保留期，会发生什么情况？
除非删除快照（第 1 层），否则新模型不允许删除还原点（第 2 层）。 目前支持的快照（第 1 层）删除保留期为 7 天，因此不支持少于 7 天的还原点（第 2 层）保留期。 建议将还原点（第 2 层）保留期设置为大于 7 天。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>为何我即使在备份策略中设置了保留期，我的快照也仍然存在？
如果恢复点包含快照并且存在最新可用的 RP，则该快照会一直保留到下一次成功备份为止。 这符合当前设计的 GC 策略，该策略强制要求始终至少有一个最新的 RP，以防 VM 中的问题导致所有备份进一步出错。 正常情况下，在 RP 过期后，将在最多 48 小时内予以清理。
