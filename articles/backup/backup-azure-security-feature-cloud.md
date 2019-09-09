---
title: 有助于保护使用 Azure 备份的云工作负荷的安全功能
description: 了解如何在 Azure 备份中使用安全功能，使备份更安全。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/04/2019
ms.author: dacurwin
ms.openlocfilehash: 938c65371b05807c774c9f2d73d4c27efe706c47
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143565"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>有助于保护使用 Azure 备份的云工作负荷的安全功能

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防止此类攻击，Azure 备份现在提供了安全功能来帮助保护备份数据，即使在删除后也是如此。 其中一项功能是软删除。 使用软删除，即使恶意执行组件删除了 VM 的备份（或意外删除了备份数据），备份数据仍将保留14天，允许恢复该备份项目，而不会丢失数据。 在 "软删除" 状态中，这些额外的14天的备份数据保留不会对客户产生任何费用。

> [!NOTE]
> 软删除仅保护已删除的备份数据。 如果在没有备份的情况下删除 VM，软删除功能不会保留数据。 所有资源都应通过 Azure 备份进行保护，以确保完全恢复能力。
>

## <a name="soft-delete"></a>软删除

### <a name="supported-regions"></a>支持的区域

美国中部地区目前支持软删除。

### <a name="soft-delete-for-vms"></a>Vm 的软删除

1. 为了删除 VM 的备份数据，必须停止备份。 在 Azure 门户中，请参阅恢复服务保管库，右键单击备份项，然后选择 "**停止备份**"。

    ![Azure 门户备份项的屏幕截图](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在下面的窗口中，您将可以选择是删除还是保留备份数据。 如果选择 "**删除备份数据**"，然后**停止备份**，则不会永久删除 VM 备份。 相反，备份数据将以软删除状态保留14天。 如果选择了 "**删除备份数据**"，则会将 "删除电子邮件" 警报发送到配置的电子邮件 ID，通知用户14天的备份数据保留期为14天。 此外，在第12天发送一封电子邮件警报，它会向你发送两天时间，指出已删除的数据恢复。 删除会推迟到第15天的时间，永久删除将发生，并发送最终的电子邮件警报，告知永久删除数据。

    ![Azure 门户的屏幕截图，停止备份屏幕](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在14天内，在恢复服务保管库中，软删除的 VM 旁边会显示一个红色的 "软删除" 图标。

    ![处于软删除状态的 Azure 门户、VM 的屏幕截图](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

> [!NOTE]
> 如果保管库中存在任何软删除的备份项，则无法在该时间删除该保管库。 请在永久删除备份项后尝试删除保管库，并在保管库中保留未处于软删除状态的项目。

4. 若要还原软删除的 VM，必须先将其删除。 若要删除，请选择软删除的 VM，然后单击 "**撤消**删除" 选项。

     ![Azure 门户，删除 VM 的屏幕截图](./media/backup-azure-security-feature-cloud/choose-undelete.png)

将出现一条警告消息，指出如果选择了 "删除删除"，则会删除 VM 的所有还原点，并可将其用于执行还原操作。 VM 将保留为 "停止保护并保留数据" 状态，备份暂停，备份数据永久保留，无备份策略生效。

 ![Azure 门户的屏幕截图，确认撤消删除 VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

此时，你还可以通过从选定的还原点选择 "**还原 vm** " 来还原 vm。  

   ![Azure 门户，Restore VM 选项的屏幕截图](./media/backup-azure-security-feature-cloud/restore-vm.png)

> [!NOTE]
> 只有在用户执行**恢复备份**操作后，垃圾回收器才会运行并清除过期的恢复点。

5. 完成删除操作后，状态将返回到 "停止备份并保留数据"，然后可以选择 "**恢复备份**"。 **恢复备份**操作返回处于活动状态的备份项，该备份项与定义备份和保留计划的用户选择的备份策略相关联。

      ![Azure 门户，恢复备份选项的屏幕截图](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程图显示了备份项的不同步骤和状态：

   ![软删除备份项的生命周期](./media/backup-azure-security-feature-cloud/lifecycle.png)

有关详细信息，请参阅[下面的常见问题](backup-azure-security-feature-cloud.md#frequently-asked-questions)部分。

## <a name="other-security-features"></a>其他安全功能

### <a name="storage-side-encryption"></a>存储端加密

Azure 存储在将数据保存到云时会自动加密数据。 加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 Azure 存储中的数据以透明方式加密和解密，并使用256位 AES 加密，这是可用的最强大的块密码之一，并且符合 FIPS 140-2。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。 Azure 备份会在存储数据之前自动对其进行加密。 Azure 存储会在检索数据之前解密数据。  

在 Azure 中，Azure 存储和保管库之间传输的数据受 HTTPS 的保护。 此数据保留在 Azure 主干网络上。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/en-in/azure/storage/common/storage-service-encryption)。

### <a name="vm-encryption"></a>VM 加密

可以使用 Azure 备份服务来备份和还原包含加密磁盘的 Windows 或 Linux Azure 虚拟机（Vm）。 有关说明，请参阅[利用 Azure 备份来备份和还原加密的虚拟机](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-encryption)。

### <a name="protection-of-azure-backup-recovery-points"></a>保护 Azure 备份恢复点

恢复服务保管库使用的存储帐户是独立的，用户不能出于任何恶意目的对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 这些管理操作通过基于角色的访问控制（RBAC）进行控制。

有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](https://docs.microsoft.com/en-us/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="soft-delete"></a>软删除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>是否需要在每个保管库上启用软删除功能？

不，默认情况下，它是为所有恢复服务保管库生成并启用的。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>是否可以配置在删除操作完成后，数据将在软删除状态中保留的天数？

不可以。删除操作完成后，它会固定到14天的额外保留期。
  
#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>是否需要支付额外的14天保留期的成本？

不能，这14天的额外保留期作为软删除功能的一部分免费提供。
  
#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

不需要，你需要撤消删除软删除的资源才能还原。 "撤消删除" 操作会将资源恢复为 "停止保护"，并将 "**保留数据" 状态**恢复到任意时间点。 在此状态下，垃圾回收器将保持暂停状态。
  
#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>我的快照是否与保管库中的恢复点遵循相同的生命周期？

是的。
  
#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何对软删除的资源再次触发计划的备份？

删除后的恢复操作将再次保护该资源。 恢复操作将备份策略与选定的保留期一起触发计划的备份。 另外，当恢复操作完成后，垃圾回收器就会立即运行。 如果要从超过其到期日期的恢复点执行还原，则建议在触发恢复操作之前执行此操作。
  
#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保管库中有软删除的项，是否可以删除保管库？

如果保管库中存在软删除状态的备份项，则无法删除恢复服务保管库。 删除操作14天后，将永久删除软删除的项。 只有在清除了所有软删除项之后，才能删除保管库。  

#### <a name="how-can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>如何在删除后删除早于14天软删除期的数据？

删除14天后，无法清除数据。 如果是阻止或符合性问题，请联系 Microsoft 支持部门。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>是否可以在 PowerShell 或 CLI 中执行软删除操作？

不可以，目前不支持 PowerShell 或 CLI。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>其他云工作负荷（如 Azure Vm 中的 SQL Server 和 Azure Vm 中 SAP HANA）支持软删除？

否。 目前仅支持 Azure 虚拟机的软删除。

## <a name="next-steps"></a>后续步骤

* 了解[Azure 备份的安全属性](https://docs.microsoft.com/en-us/azure/backup/backup-security-attributes)。
