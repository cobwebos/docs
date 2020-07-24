---
title: 创建和配置恢复服务保管库
description: 本文介绍如何创建和配置恢复服务保管库，用于存储备份和恢复点。
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: 244562efdc4c274a79ea27cdfa00dd51ae671fa4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032946"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>创建和配置恢复服务保管库

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>设置存储冗余

Azure 备份会自动处理保管库的存储。 需要指定如何复制该存储。

> [!NOTE]
> 在保管库中配置备份之前，必须先更改恢复服务保管库的**存储复制类型**（本地冗余/异地冗余）。 配置备份后，将禁用修改的选项。
>
>- 如果尚未配置备份，请[按照以下步骤](#set-storage-redundancy)查看并修改设置。
>- 如果已配置备份，并且必须从 GRS 迁移到 LRS，请[查看这些解决方法](#how-to-change-from-grs-to-lrs-after-configuring-backup)。

1. 从“恢复服务保管库”边栏选项卡中，单击新保管库  。 在“设置”部分下，单击“属性” 。
1. 在“属性”中，在“备份配置”下，单击“更新”。  

1. 选择存储复制类型，然后单击“保存”。

     ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - 如果使用 Azure 作为主要备份存储终结点，则我们建议继续使用默认的“异地冗余”设置。
   - 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。****
   - 详细了解[异地冗余](../storage/common/storage-redundancy.md)和[本地冗余](../storage/common/storage-redundancy.md)。

>[!NOTE]
>保管库的存储复制设置与 Azure 文件共享备份无关，因为当前解决方案基于快照，并且没有数据传输到保管库。 快照存储在与备份文件共享相同的存储帐户中。

## <a name="set-cross-region-restore"></a>设置跨区域还原

作为还原选项之一，跨区域还原 (CRR) 可让你还原次要区域（[Azure 配对区域](../best-practices-availability-paired-regions.md)）中的 Azure VM。 使用此选项可以：

- 存在审核或合规性要求时开展演练
- 主要区域中发生灾难时还原 VM 或其磁盘。

若要选择此功能，请从“备份配置”边栏选项卡中选择“启用跨区域还原”。 

由于此过程是在存储级别执行的，因此价格会受影响。

>[!NOTE]
>开始之前：
>
>- 有关支持的托管类型和区域的列表，请查看[支持矩阵](backup-support-matrix.md#cross-region-restore)。
>- 所有 Azure 公共区域中现已推出跨区域还原 (CRR) 功能的预览版。
>- CRR 是保管库级别的选用功能（默认已禁用），适用于任何 GRS 保管库。
>- 选择启用后，备份项最长可能需要在 48 小时后才出现在次要区域中。
>- 目前，只有备份管理类型“ARM Azure VM”才支持 CRR（不支持经典 Azure VM）。  如果其他管理类型支持 CRR，则会**自动**注册这些类型。
>- 首次启用保护后，当前无法将跨区域还原恢复为 GRS 或 LRS。

### <a name="configure-cross-region-restore"></a>配置跨区域还原

使用 GRS 冗余创建的保管库提供用于配置跨区域还原功能的选项。 每个 GRS 保管库具有一个链接到文档的横幅。 若要为保管库配置 CRR，请转到“备份配置”边栏选项卡，其中包含用于启用此功能的选项。

 ![“备份配置”横幅](./media/backup-azure-arm-restore-vms/banner.png)

1. 在门户中，转到“恢复服务保管库”>“设置”>“属性”。
2. 单击“在此保管库中启用跨区域还原”以启用该功能。

   ![单击“在此保管库中启用跨区域还原”之前](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![单击“在此保管库中启用跨区域还原”之后](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

了解如何[查看次要区域中的备份项](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)。

了解如何[在次要区域中还原](backup-azure-arm-restore-vms.md#restore-in-secondary-region)。

了解如何[监视次要区域还原作业](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)。

## <a name="modifying-default-settings"></a>修改默认设置

在保管库中配置备份之前，我们强烈建议检查“存储复制类型”和“安全设置”的默认设置。 

- 默认情况下，**存储复制类型**设置为**异地冗余**（GRS）。 配置备份后，将禁用修改选项。
  - 如果尚未配置备份，请[按照以下步骤](#set-storage-redundancy)查看并修改设置。
  - 如果已配置备份，并且必须从 GRS 迁移到 LRS，请[查看这些解决方法](#how-to-change-from-grs-to-lrs-after-configuring-backup)。

- “软删除”对新建的保管库默认为“已启用”，旨在防止意外或恶意删除备份数据。  [请按照以下步骤](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)查看并修改设置。

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>配置备份后如何从 GRS 更改为 LRS

在决定从 GRS 迁移到本地冗余存储（LRS）之前，请先查看适用于你的方案的成本更低、数据持续性更高的权衡。 如果必须从 GRS 移动到 LRS，则有两个选择。 它们依赖于你的业务需求来保留备份数据：

- [无需保留以前备份的数据](#dont-need-to-preserve-previous-backed-up-data)
- [必须保留以前备份的数据](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>无需保留以前备份的数据

若要在新的 LRS 保管库中保护工作负荷，将需要在 GRS 保管库中删除当前保护和数据，并重新配置备份。

>[!WARNING]
>以下操作是破坏性的，无法撤消。 与受保护服务器关联的所有备份数据和备份项将被永久删除。 请谨慎操作。

停止并删除 GRS 保管库上的当前保护：

1. 在 GRS 保管库属性中禁用软删除。 请按照[以下步骤](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)禁用软删除。

1. 停止保护并删除现有 GRS 保管库中的备份。 在保管库仪表板菜单中，选择 "**备份项**"。 此处列出的需要移动到 LRS 保管库的项必须连同其备份数据一起删除。 请参阅如何[删除云中受保护的项](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)和[在本地删除受保护的项](backup-azure-delete-vault.md#delete-protected-items-on-premises)。

1. 如果计划移动 AFS （Azure 文件共享）、SQL server 或 SAP HANA 服务器，则还需要对其进行撤消注册。 在保管库仪表板菜单中，选择 "**备份基础结构**"。 请参阅如何[取消注册 SQL server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)、[注销与 Azure 文件共享相关联的存储帐户](manage-afs-backup.md#unregister-a-storage-account)和[注销 SAP HANA 实例](sap-hana-db-manage.md#unregister-an-sap-hana-instance)。

1. 将其从 GRS 保管库中删除后，请继续在新的 LRS 保管库中配置工作负荷的备份。

#### <a name="must-preserve-previous-backed-up-data"></a>必须保留以前备份的数据

如果需要将当前受保护的数据保留在 GRS 保管库中并在新的 LRS 保管库中继续保护，则一些工作负荷的选项是有限的：

- 对于 MARS，可以[停止保护并保留数据](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)，并在新的 LRS 保管库中注册代理。

  - Azure 备份服务将继续保留 GRS 保管库的所有现有恢复点。
  - 需要付费，才能将恢复点保留在 GRS 保管库中。
  - 只能为 GRS 保管库中未过期的恢复点还原已备份的数据。
  - 将需要在 LRS 保管库中创建数据的新初始副本。

- 对于 Azure VM，可以[停止保护，并](backup-azure-manage-vms.md#stop-protecting-a-vm)在 GRS 保管库中保留 vm 的数据，将 vm 移动到另一个资源组，然后在 LRS 保管库中保护 vm。 请参阅将 VM 移到其他资源组的[指南和限制](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)。

  一次只能在一个保管库中保护一个 VM。 但是，可以在 LRS 保管库中保护新资源组中的 VM，因为它被视为不同的 VM。

  - Azure 备份服务将保留已在 GRS 保管库中备份的恢复点。
  - 需要付费，将恢复点保留在 GRS 保管库中（有关详细信息，请参阅[Azure 备份定价](azure-backup-pricing.md)）。
  - 如果需要，你可以从 GRS 保管库还原 VM。
  - 新资源中 VM 的 LRS 保管库上的第一次备份将是初始副本。


## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
[了解](backup-azure-delete-vault.md)如何删除恢复服务保管库。
