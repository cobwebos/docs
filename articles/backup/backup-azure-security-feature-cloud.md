---
title: 用于帮助保护云工作负荷的安全功能
description: 了解如何在 Azure 备份中使用安全功能，使备份更加安全。
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 95eb72fe9d918b527cdceec69a0e90a682d62b07
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172720"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>有助于保护使用 Azure 备份的云工作负荷的安全功能

对安全问题（例如恶意软件、勒索软件、入侵）的关注在逐渐上升。 这些安全问题可能会代价高昂（就金钱和数据来说）。 为了防范此类攻击，Azure 备份现提供可帮助保护备份数据（即使数据已删除）的安全功能。 其中一项功能是软删除。 使用软删除，即使恶意执行组件删除了 VM 的备份（或意外删除了备份数据），备份数据仍将保留14天，允许恢复该备份项目，而不会丢失数据。 以“软删除”状态将备份数据额外保留 14 天不会向客户收取任何费用。

> [!NOTE]
> 软删除只是保护已删除的备份数据。 如果删除未备份的 VM，则软删除功能不会保留数据。 应使用 Azure 备份保护所有资源，以确保能够完全复原。
>

## <a name="soft-delete"></a>软删除

### <a name="supported-regions"></a>支持的区域

目前在美国西部、东亚、加拿大中部、加拿大东部、法国中部、法国南部、韩国中部、韩国南部、英国南部、英国西部、澳大利亚东部、澳大利亚东南部、北欧、美国西部、西2、美国中部、美国西部东亚、美国中北部、美国中南部、日本东部、日本西部、印度南部、印度中部、印度西部、美国东部2、瑞士北部、瑞士西部和所有国家地区。

### <a name="soft-delete-for-vms"></a>VM 的软删除

1. 若要删除 VM 的备份数据，必须停止备份。 在 Azure 门户中转到你的恢复服务保管库，右键单击备份项，然后选择“停止备份”。

   ![Azure 门户中备份项的屏幕截图](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 在接下来的窗口中，会提供用于删除或保留备份数据的选项。 如果依次选择“删除备份数据”、“停止备份”，则不会永久删除 VM 备份， 而是将备份数据以软删除状态保留 14 天。 如果选择“删除备份数据”，则会将一封“删除”警报电子邮件发送到配置的电子邮件 ID，告知用户备份数据将延期保留 14 天。 在第 12 天时也会发送一封警报电子邮件，告知还有两天时间可以恢复已删除的数据。 删除会推迟到第 15 天，到时数据将永久删除，同时系统会发送最后一封警报电子邮件，告知数据已永久删除。

   ![Azure 门户中“停止备份”屏幕的屏幕截图](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 在这 14 天内，恢复服务保管库中已软删除的 VM 旁边会显示一个红色的“软删除”图标。

   ![Azure 门户中处于软删除状态的 VM 的屏幕截图](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 如果保管库中存在任何已软删除的备份项，此时无法删除该保管库。 请在永久删除了备份项，并且保管库中未留下任何处于软删除状态的项之后，尝试删除保管库。

4. 若要还原软删除的 VM，必须先将其取消删除。 若要取消删除，请选择软删除的 VM，并单击“取消删除”选项。

   ![Azure 门户中“取消删除 VM”的屏幕截图](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   窗口中会显示一条警告，指出如果选择取消删除，将取消删除 VM 的所有还原点，这些还原点可用于执行还原操作。 VM 将保留为“停止保护并保留数据”状态，备份将会暂停，备份数据将永久保留，且没有任何生效的备份策略。

   ![Azure 门户中“确认取消删除 VM”的屏幕截图](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   此时，还可以通过从选定还原点中选择“还原 VM”来还原 VM。  

   ![Azure 门户中“还原 VM”选项的屏幕截图](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 只有在用户执行“恢复备份”操作后，垃圾回收器才会运行并清理已过期的恢复点。

5. 取消删除操作完成后，状态将恢复为“停止备份并保留数据”，然后，你可以选择“恢复备份”。 “恢复备份”操作会恢复处于活动状态的、与定义备份和保留计划的用户所选的备份策略相关联的备份项。

   ![Azure 门户中“恢复备份”选项的屏幕截图](./media/backup-azure-security-feature-cloud/resume-backup.png)

此流程图显示了备份项的不同步骤和状态：

![软删除的备份项的生命周期](./media/backup-azure-security-feature-cloud/lifecycle.png)

有关详细信息，请参阅下面的[常见问题解答](backup-azure-security-feature-cloud.md#frequently-asked-questions)部分。

## <a name="disabling-soft-delete"></a>禁用软删除

默认情况下，在新创建的保管库上启用软删除。 如果禁用软删除安全功能，则不会因意外或恶意删除而保护备份数据。 如果没有软删除功能，受保护项的所有删除都将导致立即删除，而不能还原。 由于 "软删除" 状态中的备份数据不会对客户产生任何费用，因此不建议禁用此功能。 你应考虑禁用软删除的唯一情况是，如果你计划将受保护的项移动到新的保管库，并且无法在删除和重新保护之前等待14天（例如在测试环境中）。

### <a name="prerequisites-for-disabling-soft-delete"></a>禁用软删除的先决条件

- 启用或禁用保管库的软删除（无受保护项）只能是 Azure 门户。 这适用于：
  - 不包含受保护项的新创建的保管库
  - 受保护的项已被删除和过期的现有保管库（超过固定14天的保留期）
- 如果为保管库禁用了软删除功能，则可以重新启用它，但如果保管库包含受保护的项，则不能撤消此选择并再次禁用它。
- 对于包含受保护项或处于软删除状态的项的保管库，不能禁用软删除。 如果需要执行此操作，请执行以下步骤：
  - 停止对所有受保护项的已删除数据的保护。
  - 等待14天的安全保留期到期。
  - 禁用软删除。

若要禁用软删除，请确保满足先决条件，然后执行以下步骤：

1. 在 Azure 门户中转到保管库，然后在 "**设置**" -> **属性**"。
2. 在 "属性" 窗格中，选择 "**安全设置**" -> **更新**。
3. 在 "安全设置" 窗格的 "软删除" 下，选择 "**禁用**"。

![禁用软删除](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

## <a name="other-security-features"></a>其他安全功能

### <a name="storage-side-encryption"></a>存储端加密

Azure 存储在将数据保存到云时会自动加密数据。 加密可以保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。 Azure 存储中的数据将使用 256 位 AES 加密法（可用的最强大块加密法之一）以透明方式进行加密和解密，并符合 FIPS 140-2 规范。 Azure 存储加密法类似于 Windows 上的 BitLocker 加密法。 Azure 备份会在存储数据之前自动加密数据。 Azure 存储会在检索数据之前解密数据。  

在 Azure 中，Azure 存储与保管库之间传输的数据受 HTTPS 保护。 此数据保留在 Azure 主干网络上。

有关详细信息，请参阅[静态数据的 Azure 存储加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)。

### <a name="vm-encryption"></a>VM 加密

可以使用 Azure 备份服务来备份和还原包含已加密磁盘的 Windows 或 Linux Azure 虚拟机 (VM)。 有关说明，请参阅[使用 Azure 备份来备份和还原已加密的虚拟机](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)。

### <a name="protection-of-azure-backup-recovery-points"></a>保护 Azure 备份恢复点

恢复服务保管库使用的存储帐户是隔离的，恶意用户无法对其进行访问。 仅允许通过 Azure 备份管理操作（例如还原）进行访问。 可通过基于角色的访问控制 (RBAC) 来控制这些管理操作。

有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="soft-delete"></a>软删除

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>是否需要对每个保管库启用软删除功能？

否。此功能是为所有恢复服务保管库设计的，默认已启用。

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>是否可以配置在完成删除操作后，以软删除状态保留数据的天数？

否。删除操作完成后，数据将额外保留固定的 14 天。

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>是否需要支付这额外 14 天数据保留的费用？

否。14 天额外保留是软删除功能免费附送的。

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>如果数据处于软删除状态，是否可以执行还原操作？

否。需要取消删除已软删除的资源才能还原。 取消删除操作会将资源恢复到“停止保护并保留数据”状态，然后，你可以还原到任意时间点。 在此状态下，垃圾回收器将保持暂停状态。

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>快照的生命周期是否与保管库中恢复点的生命周期相同？

可以。

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>如何针对软删除的资源再次触发计划的备份？

先取消删除，然后执行恢复操作，即可再次保护资源。 恢复操作将关联某个备份策略，以触发具有选定保留期的计划备份。 此外，在恢复操作完成后，垃圾回收器会立即运行。 若要从超出过期日期的恢复点执行还原，我们建议在触发恢复操作之前执行此还原操作。

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>如果保管库中存在软删除的项，我是否可以删除该保管库？

如果保管库中存在处于软删除状态的备份项，则无法删除恢复服务保管库。 完成删除操作 14 天后，软删除的项将永久删除。 只有在清除所有软删除的项之后，才能删除保管库。  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>删除后，是否可以删除早于14天软删除期的数据？

不能。 不能强制删除软删除项，14天后会自动删除这些项。 启用此安全功能是为了保护备份的数据不被意外删除或恶意删除。  你应等待14天，然后再在 VM 上执行任何其他操作。  不会向软删除的项目收费。  如果需要在14天内将标记为软删除的 Vm 重新保护到新的保管库，请联系 Microsoft 支持部门。

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>是否可以在 PowerShell 或 CLI 中执行软删除操作？

否。目前不支持 PowerShell 或 CLI。

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>其他云工作负荷（例如 Azure VM 中的 SQL Server，以及 Azure VM 中的 SAP HANA）是否支持软删除？

不能。 目前只有 Azure 虚拟机支持软删除。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 备份的安全控制](backup-security-controls.md)。
