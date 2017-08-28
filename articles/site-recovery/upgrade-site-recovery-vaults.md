---
title: "将 Site Recovery 保管库升级到 Azure 恢复服务保管库"
description: "了解如何将 Azure Site Recovery 保管库升级到恢复服务保管库"
documentationcenter: 
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
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>将 Site Recovery 保管库升级到基于 Azure 资源管理器的恢复服务保管库

本文介绍如何在不影响当前复制的情况下将 Azure Site Recovery 保管库升级到基于 Azure 资源管理器的恢复服务保管库。 有关 Azure 资源管理器功能和优势的详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。

## <a name="introduction"></a>介绍
恢复服务保管库是一种 Azure 资源管理器资源，原生可在云中管理备份和灾难恢复。 它是一个可在新 Azure 门户中使用的统一保管库，并可替代经典备份和 Site Recovery 保管库。

恢复服务保管库提供一系列功能，包括：

* Azure 资源管理器支持：为虚拟机和物理计算机提供保护，并将其故障转移到Azure 资源管理器堆栈中。

* 排除磁盘：如果不想让临时文件或高改动数据占用所有带宽，可将卷从复制中排除。 此功能目前已在“VMware 到 Azure”和“Hyper-V 到 Azure”中启用，并已扩展到其他方案。

* 高级和本地冗余存储支持：现在可以在高级存储帐户中保护服务器，从而让客户使用更高的每秒输入/输出运算次数 (IOPS) 保护应用程序。 此功能目前已在“VMware 到 Azure”中启用。

* 简化的入门体验：增强的入门体验在设计上可以简化灾难恢复设置。

* 从同一个保管库管理备份和 Site Recovery：现在可以从同一个保管库保护进行灾难恢复或执行备份的服务器，从而大幅降低管理开销。

有关升级的体验和功能的详细信息，请参阅[存储、备份和恢复博客](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)。

## <a name="salient-features"></a>显著功能

* 不影响进行中的复制：进行中的复制将继续运行，在升级期间和升级之后均不会中断。

* 无额外成本：无需额外成本即可体验整套更新功能。

* 无数据丢失：由于此过程是升级而非迁移，因此在升级期间和升级之后，现有的复制恢复点和设置均保持不变。


## <a name="what-happens-during-the-vault-upgrade"></a>保管库升级期间会发生什么情况？

升级过程中，无法执行注册新服务器或者对虚拟机 (VM) 启用复制等操作。 涉及从保管库读取数据或将数据写入保管库的操作，例如持续将受保护项复制到保管库，将不间断地继续。

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>自动化和工具在升级后的变化
将保管库类型从经典部署模型升级到资源管理器部署模型时，请更新现有的自动化或工具，确保其在升级之后仍可继续运行。

### <a name="prepare-your-environment-for-the-upgrade"></a>为升级准备环境

* [安装 PowerShell 或将其升级到版本 5 或更高版本](https://www.microsoft.com/download/details.aspx?id=50395)
* [安装最新版本的 Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases)
* [下载恢复服务保管库升级脚本](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>先决条件
若要将 Site Recovery 保管库升级到基于 Azure 资源管理器的恢复服务保管库，必须满足以下要求：

* 最低代理版本：在服务器上安装的 Azure Site Recovery 提供程序版本必须为 5.1.1700.0 或更高版本。

* 支持的配置：不能使用存储区域网络 (SAN) 或 SQL Server AlwaysOn 可用性组配置保管库。 其他所有配置均受支持。

    >[!NOTE]
    >升级后，只能通过 PowerShell 管理存储映射。

* 支持的部署方案：保管库不应是“VMware 到 Azure”旧版部署模型。 在继续之前，请先转换为增强的部署模型。

* 没有活动的、用户启动的作业涉及管理平面操作：由于升级期间管理平面的访问受到限制，因此请在触发升级之前完成所有管理平面操作。 此过程不包括正在进行的复制。

## <a name="frequently-asked-questions"></a>常见问题

**这次升级是否会影响正在进行的复制？**

不会。 正在进行的复制在升级期间和升级后仍可不间断地继续。

**站点到站点 VPN 和 IP 设置等网络设置会发生什么情况？**

升级不影响网络设置。 Azure 到本地的所有连接保持不变。

**如果近期不打算升级，保管库会发生什么情况？**

从 2017 年 9 月开始，将停止支持旧版 Azure 门户中的 Site Recovery 保管库。 我们强烈建议使用升级功能迁移到新门户。

**对于我的现有工具而言，此迁移计划有何意义？**  

将工具更新为资源管理器部署模型，是必须在升级计划中考虑的最重要的更改之一。 恢复服务保管库基于资源管理器部署模型。 

**管理平面的停机时间持续多久？**

升级大约需要 15 到 30 分钟，最多一个小时。

**升级后是否可以回滚？**

否。 成功升级资源后，就不支持回滚。

**是否可以验证订阅或资源，确定它们是否可升级？**

是的。 在平台支持的升级选项中，升级的第一个步骤就是验证资源是否能够进行升级。 如果验证失败，会显示相应的错误消息或警告。

**如何报告有关升级的问题？**

如果在升级期间发生任何失败，请记下错误中列出的操作 ID。 Microsoft 支持部门会尽力主动解决此问题。 也可以联系支持团队，并提供订阅 ID、保管库名称和操作 ID。 支持人员会尽快解决问题。 除非 Microsoft 明确指示，否则请不要重试操作。

## <a name="run-the-script"></a>运行脚本

在 PowerShell 中运行以下命令：

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID：与正在升级的保管库相关联的订阅 ID。

* VaultName：正在升级的保管库的名称。

* Location：正在升级的保管库的位置。

* ResourceType：Site Recovery 保管库的 HyperVRecoveryManagerVault。

* TargetResourceGroupName：用于放置已升级的保管库的资源组。 TargetResourceGroupName 可以是 Azure 资源管理器中现有的或新的资源组。 如果提供的 TargetResourceGroupName 不存在，升级过程中会在保管库相同位置进行创建。 有关详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md#resource-groups)的“资源组”部分。

    >[!NOTE]
    >资源组命名需遵守特定的约束。 为了防止保管库升级失败，请务必认真遵循命名约定。
    >
    >例如：
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

或者，可运行以下脚本。 输入必需参数的值。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. PowerShell 脚本会提示输入凭据。 请输入凭据两次：一次是输入经典部署模型帐户的凭据，另一次是输入 Azure 资源管理器帐户的凭据。

2. 输入凭据后，脚本会运行检查来确定基础结构设置是否符合前面所述的要求。

3. 检查并确认先决条件之后，系统会提示继续升级保管库。 升级过程开始升级保管库。 整个升级可能需要 15 到 30 分钟才能完成。

4. 升级成功完成后，可以在新 Azure 门户中访问升级的保管库。

## <a name="post-upgrade-vault-management"></a>升级后的保管库管理

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>在恢复服务保管库中使用 Azure Site Recovery 进行复制

* 现在，可以从不同区域对 Azure VM 进行保护。 有关详细信息，请参阅[使用 Azure Site Recovery 在区域间复制 Azure VM](site-recovery-azure-to-azure.md)。

* 有关将 VMware VM 复制到 Azure 的详细信息，请参阅[使用 Site Recovery 将 VMware VM 复制到 Azure](vmware-walkthrough-overview.md)。

* 有关将 Hyper-VM（不带 VMM）复制到 Azure 的详细信息，请参阅[将 Hyper-V 虚拟机（不带 VMM）复制到 Azure](hyper-v-site-walkthrough-overview.md)。

* 有关将 Hyper-VM（带 VMM）复制到 Azure 的详细信息，请参阅[在 Azure 门户中使用 Site Recovery 将 VMM 云中的 Hyper-V 虚拟机复制到 Azure](vmm-to-azure-walkthrough-overview.md)。

* 有关将 Hyper-VM（带 VMM）复制到辅助站点的详细信息，请参阅[使用 Azure 门户将 VMM 云中的 Hyper-V 虚拟机复制到辅助 VMM 站点](site-recovery-vmm-to-vmm.md)。

* 有关将 VMware VM 复制到辅助站点的详细信息，请参阅[在经典 Azure 门户中将本地 VMware 虚拟机或物理服务器复制到辅助站点](site-recovery-vmware-to-vmware.md)。

### <a name="view-your-replicated-items"></a>查看复制的项

下图展示了恢复服务保管库仪表板页，其中显示了保管库的关键实体。 若要查看保管库中受保护实体的列表，请单击“Site Recovery” > “复制的项”。


![复制的项](./media/upgrade-site-recovery-vaults/replicateditems.png)

下图展示了查看复制项的工作流，以及用于启动故障转移的“故障转移”命令。

![复制的项](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>查看复制设置

在 Site Recovery 保管库中，每个保护组都配置有复制频率、恢复点保留期、应用程序一致性快照频率和其他复制设置。 恢复服务保管库中将这些设置配置为复制策略。 该策略的名称是保护组的名称或 *primarycloud_Policy*。

有关复制策略的详细信息，请参阅[管理从 VMware 到 Azure 的复制策略](site-recovery-setup-replication-settings-vmware.md)。

