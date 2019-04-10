---
title: 备份和还原加密的 Azure Vm 使用 Azure 备份
description: 介绍如何备份和还原加密的 Azure Vm 与 Azure 备份服务。
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358228"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>备份和还原加密的 Azure VM

本文介绍如何备份和还原 Windows 或 Linux Azure 虚拟机 (Vm) 通过使用加密磁盘[Azure 备份](backup-overview.md)服务。

如果你想要详细了解 Azure 备份交互的方式与 Azure Vm 在开始之前，请查看这些资源：

- [查看](backup-architecture.md#architecture-direct-backup-of-azure-vms)Azure VM 备份体系结构。
- [了解有关](backup-azure-vms-introduction.md)Azure VM 备份和 Azure 备份扩展。

## <a name="encryption-support"></a>加密支持

Azure 备份支持已加密及其 OS/数据磁盘与 Azure 磁盘加密 (ADE) 的 Azure Vm 的备份。 ADE 用于加密 Windows Vm 和 dm-crypt 功能适用于 Linux Vm 使用 BitLocker。 ADE 与 Azure 密钥保管库来管理磁盘加密密钥和机密。 密钥保管库密钥加密密钥 (Kek) 可用来添加额外的安全，然后再写入密钥保管库加密加密的机密。

Azure 备份可以备份和还原 Azure Vm 与 Azure AD 应用程序，而使用 ADE 下, 表中进行了总结。

**VM 磁盘类型** | **ADE （BEK/数据挖掘的加密）** | **ADE 和 KEK**
--- | --- | ---
**非托管** | 是 | 是
**托管**  | 是 | 是

- 详细了解如何[ADE](../security/azure-security-disk-encryption-overview.md)， [Key Vault](../key-vault/key-vault-overview.md)，并[Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)。
- 读取[常见问题解答](../security/azure-security-disk-encryption-faq.md)为 Azure VM 磁盘加密。



### <a name="limitations"></a>限制

- 可以备份和还原加密的 Vm 在同一订阅和区域中。
- Azure 备份支持使用独立密钥加密的 Vm。 目前不支持任何密钥是用来加密 VM 的证书的一部分。
- 可以备份和还原加密的 Vm 在同一订阅和区域与恢复服务备份保管库中。
- 无法在文件/文件夹级别恢复已加密的 VM。 你需要恢复整个 VM 还原文件和文件夹。
- 当还原 VM，不能使用[替换为现有 VM](backup-azure-arm-restore-vms.md#restore-options)为加密的 Vm 的选项。 未加密的托管磁盘仅支持此选项。




## <a name="before-you-start"></a>开始之前

开始之前，请执行以下操作：

1. 请确保您有一个或多个[Windows](../security/azure-security-disk-encryption-windows.md)或[Linux](../security/azure-security-disk-encryption-linux.md) ADE 与虚拟机已启用。
2. [复查支持矩阵，](backup-support-matrix-iaas.md)对于 Azure VM 备份
3. [创建](backup-azure-arm-vms-prepare.md#create-a-vault)如果还没有一个恢复服务备份保管库。
4. 如果启用已启用备份的 Vm 的加密，只需为备份提供权限来访问密钥保管库，以便备份持续进行而不发生中断。 [了解详细信息](#provide-permissions)有关分配这些权限。

此外，还有一些可能需要在某些情况下执行的操作：

- **在 VM 上安装 VM 代理**:Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。 如果从 Azure marketplace 映像创建 VM 时，代理已安装并正在运行。 如果创建自定义 VM，或迁移的本地计算机，则可能需要[手动安装代理](backup-azure-arm-vms-prepare.md#install-the-vm-agent)。
- **显式允许出站访问**:通常情况下，您不必显式允许出站网络访问 Azure vm，使其与 Azure 备份进行通信。 但是，某些 Vm 可能会遇到连接问题，其中显示**ExtensionSnapshotFailedNoNetwork**尝试进行连接时的错误。 如果发生这种情况，您应该[显式允许出站访问](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access)，因此，Azure 备份扩展可以与 Azure 备份流量的公共 IP 地址进行通信。



## <a name="configure-a-backup-policy"></a>配置备份策略

1. 如果你尚未创建恢复服务备份保管库，请按照[这些说明](backup-azure-arm-vms-prepare.md#create-a-vault)
2. 在门户中，打开保管库，并选择**备份**中**入门**部分。

    ![“备份”边栏选项卡](./media/backup-azure-vms-encryption/select-backup.png)

3. 在中**备份目标** > **工作负荷的运行位置？** 选择**Azure**。
4. 在中**要备份什么？** 选择**虚拟机** > **确定**。

      ![“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 在中**备份策略** > **选择备份策略**，选择你想要将与保管库相关联的策略。 然后单击“确定”。
    - 备份策略指定何时创建备份，并存储多长时间。
    - 默认策略的详细信息会在下拉菜单下列出。

    ![打开“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 如果不想要使用的默认策略，请选择**创建新**，并[创建自定义策略](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。


7. 选择你想要使用选择的策略，备份已加密的 Vm，然后选择**确定**。

      ![选择加密 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 如果你使用 Azure 密钥保管库，在保管库页上，您将看到一条消息，Azure 备份需要只读访问的密钥和机密密钥保管库中。

    - 如果收到此消息，不不需要任何操作。

        ![访问确定](./media/backup-azure-vms-encryption/access-ok.png)

    - 如果收到此消息，则需要设置权限，如中所述[下面的过程](#provide-permissions)。

        ![访问警告](./media/backup-azure-vms-encryption/access-warning.png)

9. 单击**启用备份**部署在保管库中的备份策略和为所选的 Vm 启用备份。


## <a name="trigger-a-backup-job"></a>触发备份作业

按照计划，将运行初始备份，但你可以运行它立即按如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在“备份项”中，单击“Azure 虚拟机”。
3. 在中**备份项**列表中，单击省略号 （...）。
4. 单击“立即备份”。
5. 在中**立即备份**，使用日历控件选择恢复点应保留的最后一天。 然后单击“确定”。
6. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。 创建初始备份可能需要一些时间，具体取决于 VM 的大小。


## <a name="provide-permissions"></a>提供的权限

Azure VM 需要备份密钥和机密，以及关联的 Vm 的只读访问。

- 密钥保管库是与 Azure 订阅的 Azure AD 租户相关联。 如果您**成员用户**，Azure 备份获取访问密钥保管库，而无需执行进一步的操作。
- 如果您**来宾用户**，必须提供 Azure 备份密钥保管库的访问权限。

若要设置权限：

1. 在 Azure 门户中，选择**所有服务**，并搜索**密钥保管库**。
2. 选择与要备份的加密 VM 关联的密钥保管库。
3. 选择**访问策略** > **新增**。
4. 选择**选择主体**，然后键入**备份管理**。
5. 选择**备份管理服务** > **选择**。

    ![备份服务选择](./media/backup-azure-vms-encryption/select-backup-service.png)

6. 在中**添加访问策略** > **从模板 （可选） 配置**，选择**Azure 备份**。
    - “密钥权限”和“机密权限”中已预先填充所需的权限。
    - 如果使用加密 VM**仅限 BEK**，删除的所选内容**密钥权限**因为您只需要针对机密的权限。

    ![Azure 备份选择](./media/backup-azure-vms-encryption/select-backup-template.png)

6. 单击“确定”。 **备份管理服务**添加到**访问策略**。

    ![访问策略](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. 单击**保存**为 Azure 备份提供权限。

## <a name="restore-an-encrypted-vm"></a>还原已加密的 VM

还原加密的 Vm，如下所示：

1. [还原 VM 磁盘](backup-azure-arm-restore-vms.md#restore-disks)。
2. 然后执行下列任一操作：
    - 使用自定义 VM 设置，并触发 VM 部署在还原操作期间生成的模板。 [了解详细信息](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    - 从还原的磁盘使用 Powershell 创建新的 VM。 [了解详细信息](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

## <a name="next-steps"></a>后续步骤

如果遇到任何问题，请查看

- [常见错误](backup-azure-vms-troubleshoot.md)时备份和还原加密的 Azure Vm。
- [Azure VM 代理/备份扩展](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)问题。
