---
title: 备份和还原加密的 Azure Vm
description: 介绍如何利用 Azure 备份服务备份和还原加密的 Azure Vm。
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 3719f26211677a1b726d3c3dc1d217fb58db1fb2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583168"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>备份和还原加密的 Azure VM

本文介绍如何使用[Azure 备份](backup-overview.md)服务来备份和还原带有加密磁盘的 Windows 或 Linux Azure 虚拟机（vm）。

如果要了解有关 Azure 备份在开始之前如何与 Azure Vm 交互的详细信息，请查看以下资源：

- [查看](backup-architecture.md#architecture-built-in-azure-vm-backup)Azure VM 备份体系结构。
- [了解](backup-azure-vms-introduction.md)Azure VM 备份和 Azure 备份扩展。

## <a name="encryption-support"></a>加密支持

Azure 备份支持备份具有使用 Azure 磁盘加密（ADE）加密的 OS/data 磁盘的 Azure Vm。 ADE 使用 BitLocker 加密 Windows Vm，并使用适用于 Linux Vm 的 dm-crypt 功能。 ADE 与 Azure Key Vault 集成，以管理磁盘加密密钥和机密。 Key Vault 密钥加密密钥（Kek）可用于添加其他安全层，在将加密机密写入 Key Vault 之前，对其进行加密。

Azure 备份可以使用包含或不带 Azure AD 应用的 ADE 来备份和还原 Azure Vm，如下表中所示。

**VM 磁盘类型** | **ADE （BEK/dm-dm-crypt）** | **ADE 和 KEK**
--- | --- | ---
**无** | 是 | 是
**经过**  | 是 | 是

- 详细了解[ADE](../security/azure-security-disk-encryption-overview.md)、 [Key Vault](../key-vault/key-vault-overview.md)和[kek](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek/)。
- 阅读 Azure VM 磁盘加密的[常见问题解答](../security/azure-security-disk-encryption-faq.md)。

### <a name="limitations"></a>限制

- 可以在同一订阅和区域中备份和还原加密的 Vm。
- Azure 备份支持使用独立密钥加密的 Vm。 当前不支持作为用于加密 VM 的证书的一部分的任何密钥。
- 可以在与恢复服务备份保管库相同的订阅和区域中备份和还原加密的 Vm。
- 无法在文件/文件夹级别恢复已加密的 VM。 你需要恢复整个 VM 来还原文件和文件夹。
- 恢复 VM 时，不能对已加密的 Vm 使用 "[替换现有 VM](backup-azure-arm-restore-vms.md#restore-options) " 选项。 只有未加密的托管磁盘才支持此选项。

## <a name="before-you-start"></a>开始之前

开始之前，请执行以下操作：

1. 请确保有一个或多个启用了 ADE 的[Windows](../security/azure-security-disk-encryption-windows.md)或[Linux](../virtual-machines/linux/disk-encryption-overview.md) vm。
2. 查看 Azure VM 备份[的支持矩阵](backup-support-matrix-iaas.md)
3. 如果还没有恢复服务备份保管库，请[创建](backup-azure-arm-vms-prepare.md#create-a-vault)一个。
4. 如果为已启用备份的 Vm 启用了加密，只需向备份提供访问该 Key Vault 的权限，这样就可以在不中断的情况下继续备份。 [详细了解](#provide-permissions)如何分配这些权限。

此外，在某些情况下，你可能需要执行几项操作：

- 在**vm 上安装 vm 代理**： azure 备份通过将扩展安装到在计算机上运行的 Azure VM 代理来备份 azure vm。 如果 VM 是从 Azure marketplace 映像创建的，则代理已安装并正在运行。 如果创建自定义 VM，或迁移本地计算机，则可能需要[手动安装代理](backup-azure-arm-vms-prepare.md#install-the-vm-agent)。

## <a name="configure-a-backup-policy"></a>配置备份策略

1. 如果尚未创建恢复服务备份保管库，请按照[以下说明进行操作](backup-azure-arm-vms-prepare.md#create-a-vault)
2. 在门户中打开保管库，然后在 "**入门**" 部分中选择 "**备份**"。

    ![“备份”边栏选项卡](./media/backup-azure-vms-encryption/select-backup.png)

3. 在**备份目标** > **工作负荷的运行位置？选择 "** **Azure**"。
4. 在 "**要备份的内容"** 中，选择 "**虚拟机** >  **" 确定 "** 。

      ![“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 在 "**备份策略** > **选择" 备份策略**"，选择要与保管库关联的策略。 然后单击“确定”。
    - 备份策略指定备份的时间和存储时间。
    - 默认策略的详细信息会在下拉菜单下列出。

    ![打开“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 如果你不想使用默认策略，请选择 "**新建**"，然后[创建自定义策略](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

7. 使用选择策略选择要备份的已加密 Vm，然后选择 **"确定"** 。

      ![选择加密 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 如果使用的是 Azure Key Vault，请在保管库页上看到一条消息，指出 Azure 备份需要对 Key Vault 中的密钥和机密具有只读访问权限。

    - 如果收到此消息，则无需执行任何操作。

        ![访问正常](./media/backup-azure-vms-encryption/access-ok.png)

    - 如果收到此消息，则需要设置权限，如[以下过程](#provide-permissions)中所述。

        ![访问警告](./media/backup-azure-vms-encryption/access-warning.png)

9. 单击 "**启用备份**" 将备份策略部署到保管库，并为所选 vm 启用备份。

## <a name="trigger-a-backup-job"></a>触发备份作业

初始备份将按照计划运行，但你可以立即运行，如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在 "**备份项**" 中，单击 " **Azure 虚拟机**"。
3. 在 "**备份项**" 列表中，单击省略号（...）。
4. 单击“立即备份”。
5. 现在，在 "**备份**" 中使用 "日历" 控件选择应该保留恢复点的最后一天。 然后单击“确定”。
6. 监视门户通知。 可以在保管库仪表板 >“备份作业” **“进行中”监视作业进度。**  >  创建初始备份可能需要一些时间，具体取决于 VM 的大小。

## <a name="provide-permissions"></a>提供权限

Azure VM 需要只读访问权限来备份密钥和机密以及关联的 Vm。

- 你的 Key Vault 与 Azure 订阅的 Azure AD 租户相关联。 如果你是**成员用户**，Azure 备份将获取对 Key Vault 的访问权限，而无需执行进一步的操作。
- 如果你是**来宾用户**，则必须提供 Azure 备份访问密钥保管库的权限。

若要设置权限：

1. 在 Azure 门户中，选择 "**所有服务**"，然后搜索 "**密钥保管库**"。
2. 选择与要备份的加密 VM 关联的密钥保管库。
3. 选择 "**访问策略**" > "**新建**"。
4. 选择 "**选择主体**"，然后键入 "**备份管理**"。
5. 选择 "**备份管理服务** ** > "** 。

    ![备份服务选择](./media/backup-azure-vms-encryption/select-backup-service.png)

6. 在 "**添加访问策略**" > **从模板配置（可选）** "，选择" **Azure 备份**"。
    - “密钥权限”和“机密权限”中已预先填充所需的权限。
    - 如果**仅使用 BEK**加密 VM，请删除**密钥权限**的选择，因为你只需要机密的权限。

    ![Azure 备份选择](./media/backup-azure-vms-encryption/select-backup-template.png)

7. 单击“确定”。 **备份管理服务**已添加到**访问策略**。

    ![访问策略](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. 单击 "**保存**" 以提供具有权限的 Azure 备份。

## <a name="restore-an-encrypted-vm"></a>还原已加密的 VM

还原已加密的 Vm，如下所示：

1. [还原 VM 磁盘](backup-azure-arm-restore-vms.md#restore-disks)。
2. 通过执行以下操作之一来重新创建虚拟机实例：
    1. 使用在还原操作过程中生成的模板自定义 VM 设置并触发 VM 部署。 [了解详细信息](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    2. 使用 PowerShell 从还原的磁盘创建新 VM。 [了解详细信息](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。
3. 对于 Linux Vm，请重新安装 ADE 扩展，以便打开并装入数据磁盘。

## <a name="next-steps"></a>后续步骤

如果遇到任何问题，请查看以下文章：

- 备份和还原加密的 Azure Vm 时的[常见错误](backup-azure-vms-troubleshoot.md)。
- [AZURE VM 代理/备份扩展](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)问题。
