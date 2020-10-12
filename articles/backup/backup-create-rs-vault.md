---
title: 创建和配置恢复服务保管库
description: 本文介绍如何创建和配置用于存储备份和恢复点的恢复服务保管库。 了解如何使用跨区域还原在次要区域中还原。
ms.topic: conceptual
ms.date: 05/30/2019
ms.custom: references_regions
ms.openlocfilehash: c659efad7f0eaf5793e1fd608eb522964df7befd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981493"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>创建和配置恢复服务保管库

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>设置存储冗余

Azure 备份会自动处理保管库的存储。 需要指定如何复制该存储。

> [!NOTE]
> 在保管库中配置备份之前，必须更改恢复服务保管库的“存储复制类型”（本地冗余/异地冗余）。 配置备份后，将禁用修改选项。
>
>- 如果尚未配置备份，请[遵循这些步骤](#set-storage-redundancy)检查和修改设置。
>- 如果已配置备份，并且必须从 GRS 迁移到 LRS，请[查看这些解决方法](#how-to-change-from-grs-to-lrs-after-configuring-backup)。

1. 在“恢复服务保管库”窗格中，选择新保管库。 在“设置”部分下，选择“属性”。 
1. 在“属性”中的“备份配置”下，选择“更新”。  

1. 选择存储复制类型，然后选择“保存”。

     ![设置新保管库的存储配置](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - 如果使用 Azure 作为主要备份存储终结点，则我们建议继续使用默认的“异地冗余”设置。
   - 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。
   - 详细了解[异地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)和[本地冗余](../storage/common/storage-redundancy.md#locally-redundant-storage)。
   - 如果需要在某个区域不停机的情况下使用数据，请确保数据驻留，然后选择 " [区域冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy#zone-redundant-storage)"。

>[!NOTE]
>此保管库的存储复制设置与 Azure 文件共享备份无关，因为当前解决方案基于快照，并且没有数据传输到保管库。 快照存储在与备份文件共享相同的存储帐户中。

## <a name="set-cross-region-restore"></a>设置跨区域还原

还原选项 **跨区域还原 (CRR) ** 使你可以在辅助、 [Azure 配对区域](../best-practices-availability-paired-regions.md)中还原数据。

它支持以下数据源：

- Azure VM
- 托管在 Azure Vm 上的 SQL 数据库
- 在 Azure Vm 上托管的 SAP HANA 数据库

使用跨区域还原，可以：

- 存在审核或合规性要求时开展演练
- 如果主要区域发生灾难，则还原数据

恢复 VM 时，可以还原 VM 或其磁盘。 如果要从 Azure Vm 上托管的 SQL/SAP HANA 数据库还原，可以还原数据库或其文件。

若要选择此功能，请从 "**备份配置**" 窗格中选择 "**启用跨区域还原**"。

由于此过程在存储级别，因此存在 [定价影响](https://azure.microsoft.com/pricing/details/backup/)。

>[!NOTE]
>开始之前：
>
>- 有关支持的托管类型和区域的列表，请查看[支持矩阵](backup-support-matrix.md#cross-region-restore)。
>- 跨区域还原 (CRR) 功能现已在所有 Azure 公共区域和主权云中预览。
>- CRR 是保管库级别的选用功能（默认已禁用），适用于任何 GRS 保管库。
>- 选择启用后，备份项最长可能需要在 48 小时后才出现在次要区域中。
>- 目前仅支持 azure 资源管理器 Azure Vm 的 CRR。 不支持经典 Azure Vm。  如果其他管理类型支持 CRR，则会 **自动** 注册。
>- 首次启动保护后，不能再将跨区域还原恢复为 GRS 或 LRS。

### <a name="configure-cross-region-restore"></a>配置跨区域还原

使用 GRS 冗余创建的保管库提供用于配置跨区域还原功能的选项。 每个 GRS 保管库具有一个链接到文档的横幅。 若要为保管库配置 CRR，请前往 "备份配置" 窗格，其中包含启用此功能的选项。

 ![“备份配置”横幅](./media/backup-azure-arm-restore-vms/banner.png)

1. 在门户中，转到“恢复服务保管库”>“设置”>“属性”。
2. 选择 " **在此保管库中启用跨区域还原** " 以启用该功能。

   ![启用跨区域还原](./media/backup-azure-arm-restore-vms/backup-configuration.png)

请参阅以下文章，了解有关备份和还原与 CRR 的详细信息：

- [Azure Vm 的跨区域还原](backup-azure-arm-restore-vms.md#cross-region-restore)
- [针对 SQL 数据库的跨区域还原](restore-sql-database-azure-vm.md#cross-region-restore)
- [跨区域还原 SAP HANA 数据库](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>设置加密设置

默认情况下，恢复服务保管库中的数据是使用平台托管密钥加密的。 不需要任何明确的操作即可启用此加密，并将其应用于要备份到恢复服务保管库的所有工作负荷。  你可以选择自带密钥对此保管库中的备份数据进行加密。 这称为客户管理的密钥。 如果要使用自己的密钥加密备份数据，则必须先指定加密密钥，然后才能在此保管库中保护任何项目。 启用密钥加密后，将无法还原。

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>将保管库配置为使用客户托管的密钥进行加密

若要配置保管库以通过客户托管的密钥进行加密，必须按以下顺序执行这些步骤：

1. 为恢复服务保管库启用托管标识

1. 将权限分配给保管库以访问 Azure Key Vault 中的加密密钥

1. 启用软删除和清除保护 Azure Key Vault

1. 将加密密钥分配到恢复服务保管库

可 [在此文中](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)找到有关这些步骤的说明。

## <a name="modifying-default-settings"></a>修改默认设置

在保管库中配置备份之前，我们强烈建议检查“存储复制类型”和“安全设置”的默认设置。 

- “存储复制类型”默认设置为“异地冗余”(GRS) 。 配置备份后，将禁用修改选项。
  - 如果尚未配置备份，请[遵循这些步骤](#set-storage-redundancy)检查和修改设置。
  - 如果已配置备份，并且必须从 GRS 迁移到 LRS，请[查看这些解决方法](#how-to-change-from-grs-to-lrs-after-configuring-backup)。

- “软删除”对新建的保管库默认为“已启用”，旨在防止意外或恶意删除备份数据。  [遵循这些步骤](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)检查和修改设置。

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>配置备份后如何从 GRS 更改为 LRS

在决定从 GRS 改为本地冗余存储 (LRS) 之前，请先根据你的应用场景在较低成本和较高数据持续性之间权衡。 如果必须从 GRS 改用 LRS，你有两种选择。 它们取决于保留备份数据的业务要求：

- [无需保留以前备份的数据](#dont-need-to-preserve-previous-backed-up-data)
- [必须保留以前备份的数据](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>无需保留以前备份的数据

若要在新的 LRS 保管库中保护工作负载，需在 GRS 保管库中删除当前保护和数据，并重新配置备份。

>[!WARNING]
>以下操作是破坏性的，无法撤消。 与受保护服务器关联的所有备份数据和备份项将被永久删除。 请谨慎操作。

停止并删除对 GRS 保管库的当前保护：

1. 在 GRS 保管库属性中禁用软删除。 按照[这些步骤](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)禁用软删除。

1. 停止保护并删除现有 GRS 保管库中的备份。 在保管库仪表板菜单中，选择“备份项”。 此处列出的需要移动到 LRS 保管库的项必须连同其备份数据一起删除。 请参阅如何[删除云中受保护的项](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)以及[删除本地受保护的项](backup-azure-delete-vault.md#delete-protected-items-on-premises)。

1. 如果打算将 AFS (Azure 文件共享) 、SQL server 或 SAP HANA 服务器，则还需要对其进行撤消注册。 在保管库仪表板菜单中，选择“备份基础结构”。 请参阅如何 [取消注册 SQL server](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)、 [注销与 Azure 文件共享相关联的存储帐户](manage-afs-backup.md#unregister-a-storage-account)和 [注销 SAP HANA 实例](sap-hana-db-manage.md#unregister-an-sap-hana-instance)。

1. 将其从 GRS 保管库中删除后，请继续在新的 LRS 保管库中配置工作负载的备份。

#### <a name="must-preserve-previous-backed-up-data"></a>必须保留以前备份的数据

如果需要将当前受保护的数据保留在 GRS 保管库中，并在新的 LRS 保管库中继续保护，则某些工作负载的选项受到限制：

- 对于 MARS，可以[停止保护并保留数据](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)并在新的 LRS 保管库中注册代理。

  - Azure 备份服务将继续保留 GRS 保管库的所有现有恢复点。
  - 需要付费才能将恢复点保留在 GRS 保管库中。
  - 只能还原 GRS 保管库中尚未过期的恢复点的备份数据。
  - 需要在 LRS 保管库中创建数据的新初始副本。

- 对于 Azure VM，可以对 GRS 保管库中的 VM [停止保护并保留数据](backup-azure-manage-vms.md#stop-protecting-a-vm)，将该 VM 移到其他资源组，然后在 LRS 保管库中保护该 VM。 请参阅将 VM 移到其他资源组的[指南和限制](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)。

  同一时间只能在一个保管库中保护 VM。 但是，可以在 LRS 保管库中保护新资源组中的 VM，因为它被视为不同的 VM。

  - Azure 备份服务将在 GRS 保管库中保留已备份的恢复点。
  - 你需要付费才能将恢复点保留在 GRS 保管库中（有关详细信息，请参阅 [Azure 备份定价](azure-backup-pricing.md)）。
  - 如果需要，你将能够从 GRS 保管库还原 VM。
  - LRS 保管库中对新资源组中 VM 的第一个备份将是初始副本。

## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
[了解](backup-azure-delete-vault.md)如何删除恢复服务保管库。
