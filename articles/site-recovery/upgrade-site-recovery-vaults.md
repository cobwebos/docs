---
title: "将 Site Recovery 保管库升级到恢复服务保管库"
description: "了解如何将 Azure Site Recovery 保管库升级到恢复服务保管库"
ddocumentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>将 Site Recovery 保管库升级到基于 Azure 资源管理器的恢复服务保管库

本文介绍如何在不影响当前复制的情况下将“Site Recovery 保管库”升级到基于 Azure 资源管理器的“恢复服务保管库”。 可在[此处](../azure-resource-manager/resource-group-overview.md)阅读有关 Azure 资源管理器功能和优点的更多内容。

## <a name="introduction"></a>介绍
恢复服务保管库是一种 Azure 资源管理器资源，可在云中管理本机需要的备份和灾难恢复。 它是一个统一的保管库，可在新的 Azure 门户中使用，也可替代经典备份和 Site Recovery 保管库。

恢复服务保管库开启的数组功能包括：

-   Azure 资源管理器支持：为虚拟机和物理计算机提供保护，并将它故障转移到Azure 资源管理器堆栈中。

-   排除磁盘 - 如果不想让临时文件或高改动数据占用带宽，可将卷从复制中排除。 此功能目前已在“VMware 到 Azure”和“Hyper-V 到 Azure”中启用，并且将很快扩展到其他方案中。

- 高级和本地冗余存储 (LRS) 支持 – 现在可以将服务器保护到高级存储帐户中，从而让客户使用更高的 IOP 保护应用程序。 此功能目前已在“VMware 到 Azure”中启用。

-   简化的“入门”体验 - 增强版的入门体验经过调整之后，可确保轻松地进行灾难恢复设置。

- 从同一个保管库中管理备份和 Site Recovery - 现在可以从同一个保管库保护进行灾难恢复或执行备份的服务器，从而显著降低了管理开销。

有关升级体验和功能的更多详细信息，请查阅此[博客](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)。

## <a name="salient-features"></a>显著功能

- 不影响进行中的复制：进行中的复制将继续运行，在升级期间和升级之后均不会中断。

- 无额外成本 – 无需额外成本即可体验一系列较新的功能

- 无数据丢失 – 由于这是升级而非迁移，因此在升级期间和升级之后，现有的复制信息（恢复点、复制设置等）均保持不变。


## <a name="what-happens-during-the-upgrade"></a>升级期间会发生什么情况？

升级过程中，不允许注册新的服务器、对 VM 启用复制等操作。 任何仅涉及从保管库读取数据或将数据写入保管库的操作，例如正在将受保护的项复制到保管库，将不间断的继续进行。

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>自动化和工具在保管库升级后的变化
将保管库类型从经典部署模型升级到资源管理器部署模型的过程中，必须更新现有的自动化或工具，确保其在升级之后仍可继续运行。

## <a name="preparing-your-environment-for-vault-upgrade"></a>为保管库的升级准备环境

1.  使用此[链接](https://www.microsoft.com/download/details.aspx?id=50395)安装 PowerShell 或将它升级到版本 5 或更高版本

2.  从[此处](https://github.com/Azure/azure-powershell/releases)安装最新的 Azure PowerShell MSI

3.  [下载](https://aka.ms/vaultupgradescript)用于保管库升级的脚本

## <a name="prerequisites-for-upgrade"></a>升级的先决条件
将 Site Recovery 保管库升级到基于 Azure 资源管理器的恢复服务保管库，需满足以下先决条件。

- 最低代理版本：升级需要服务器上安装的 Azure Site Recovery 提供程序的版本至少为 5.1.1700.0。

- 支持的配置：保管库不能配置 SAN、SQL Always 可用性组。 其他所有配置均受支持。

>[!NOTE]
> 升级后，存储映射只能通过 PowerShell 进行托管。

- 支持的部署方案 – 保管库不应位于“VMware 到 Azure”的旧版部署模型中。  继续操作之前，需要转换为增强版的部署模型。

- 没有活动的、用户启动的作业涉及管理平面操作：由于升级期间管理平面的访问受到限制，因此需完成所有的管理平面操作后才能触发升级。 这不包括正在进行的复制。

## <a name="frequently-asked-questions"></a>常见问题

- 这次升级是否会影响正在进行的复制？

  否。 正在进行的复制在升级期间和升级后仍可不间断的继续运行。

- 网络设置 - 站点到站点 VPN、IP设置等会发生什么情况？

  升级不影响网络设置，Azure 到本地的所有连接都将保持不变。
- 如果近期不计划升级，保管库会发生什么情况？

  从 9 月开始，将弃用支持 Site Recovery 保管库的旧版 Azure 门户。 因此，我们强烈建议客户使用升级功能以迁移到新版门户。

- 对于我现有的工具而言，此迁移计划有何意义？  

  将工具更新为恢复服务保管库所基于的资源管理器部署模型，是升级计划中必须考虑的最重要更改之一。

- 管理平面的停机时间持续多久？

  升级大约需要 15-30 分钟。 最多一个小时。

- 升级后是否可以回滚？

  否。 成功升级资源后，就不支持回滚。

- 是否可以验证订阅或资源，确定它们是否可升级？

  是的。 在平台支持的升级选项中，升级的第一个步骤就是验证资源是否能够进行升级。 如果先决条件的验证失败，将收到相应的错误或警告。

- 如何报告有关升级的问题？

  如果在升级期间遇到任何故障，请记下错误中列出的 OperationId。 Microsoft 支持部门会尽力主动解决此问题。 也可以联系支持部门，并提供订阅 ID、保管库名称和操作 ID。 我们将努力尽快解决此问题。 除非 Microsoft 明确指示，否则请不要重试操作。

## <a name="how-to-run-the-script"></a>如何运行脚本？

在 PowerShell 提示符下，运行以下命令：

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionId：与正在升级的保管库相关联的订阅 ID
- VaultName：正在升级的保管库的名称
- 位置：正在升级的保管库的位置
- ResourceType：用于 Site Recovery 保管库的 HyperVRecoveryManagerVault
- TargetResourceGroupName：要放置已升级的保管库的资源组。 TargetResourceGroupName 可以是 Azure 资源管理器中现有的或新的 ResourceGroup。 如果提供的 TargetResourceGroupName 不存在，升级过程中将在保管库相同位置进行创建。 若要阅读资源组的详细信息，请单击[此处](../azure-resource-manager/resource-group-overview.md#resource-groups)：

>[!NOTE]
>资源组名称具有约束。 请按照相同的步骤操作，否则可能会导致保管库升级失败。

示例：

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


或者，可以运行以下脚本，用户需要为所需的所有参数提供输入。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  PowerShell 脚本会提示输入凭据。 需要输入凭据两次 - 一次是输入 ASM 帐户，另一次是输入 Azure 资源管理器帐户。

2.  输入凭据后，脚本会运行先决条件检查，确定基础结构设置是否符合文档之前提到的先决条件。

3.  先决条件检查完成之后，系统会提示对保管库升级进行确认。 确认后，升级过程将开始升级保管库。 整个升级可能需要 15-30 分钟才能完成。

4.  升级成功完成之后，就可以在新的 Azure 门户中访问已升级的保管库。

## <a name="management-experience-post-upgrade"></a>升级后的管理体验

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>如何在恢复服务保管库中使用 Azure Site Recovery 进行复制

- 现在，可以从不同区域对 Azure VM 进行保护。 若要了解详细信息，请参阅[此处](site-recovery-azure-to-azure.md)的文档。

- 若要了解将 VMware VM 复制到 Azure 的详细信息，请参阅[此处](vmware-walkthrough-overview.md)的文档。

- 若要了解将 Hyper-VM（无 VMM）复制到 Azure 的详细信息，请参阅[此处](hyper-v-site-walkthrough-overview.md)的文档。

- 若要了解将 Hyper-VM（有 VMM）复制到 Azure 的详细信息，请参阅[此处](vmm-to-azure-walkthrough-overview.md)的文档。

- 若要了解将 Hyper-VM（有 VMM）复制到辅助站点的详细信息，请参阅[此处](site-recovery-vmm-to-vmm.md)的文档。

- 若要了解将 VMware VM 复制到辅助站点的详细信息，请参阅[此处](site-recovery-vmware-to-vmware.md)的文档。

### <a name="how-to-view-your-replicated-items"></a>如何查看复制的项

以下截屏展示恢复服务保管库仪表板页面，该页面显示保管库的关键实体。 单击“Site Recovery” -> “复制的项”查看保管库中受保护的实体列表。


![复制的项](./media/upgrade-site-recovery-vaults/replicateditems.png)

以下截屏展示查看已复制项以及如何启动故障转移的工作流。

![复制的项](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>如何查看复制设置

在 Site Recovery 保管库中，每个保护组都配置了复制设置（复制频率、恢复点保留期、应用程序一致性快照频率等）。 恢复服务保管库中将这些设置配置为复制策略。 策略名称即保护组或“primarycloud_Policy”的名称。

若要了解复制策略的详细信息，请参阅[此处](site-recovery-setup-replication-settings-vmware.md)

