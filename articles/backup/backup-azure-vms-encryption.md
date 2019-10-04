---
title: 使用 Azure 备份来备份和还原已加密的 Azure VM
description: 介绍如何使用 Azure 备份服务备份和还原已加密的 Azure VM。
ms.reviewer: geg
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: dacurwin
ms.openlocfilehash: 5968a675c3f0f9a2c6426ed73d06e2d116a8ff3b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827387"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>备份和还原已加密的 Azure VM

本文介绍如何使用 [Azure 备份](backup-overview.md)服务来备份和还原包含已加密磁盘的 Windows 或 Linux Azure 虚拟机 (VM)。

在开始之前，若要详细了解 Azure 备份如何与 Azure VM 交互，请查看以下资源：

- [查看](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 备份体系结构。
- [了解](backup-azure-vms-introduction.md) Azure VM 备份和 Azure 备份备份扩展。

## <a name="encryption-support"></a>支持加密

Azure 备份支持备份已使用 Azure 磁盘加密 (ADE) 功能加密了其 OS/数据磁盘的 Azure VM。 ADE 使用 BitLocker 加密 Windows VM，使用 dm-crypt 功能加密 Linux VM。 ADE 与 Azure Key Vault 集成，可以管理磁盘加密密钥和机密。 使用 Key Vault Key 加密密钥 (KEk) 可以额外增加一个安全层，这样可以在将加密机密写入 Key Vault 之前对其进行加密。

Azure 备份可以在使用或者不使用 Azure AD 应用的情况下，通过 ADE 备份和还原 Azure VM。下表提供了相关摘要内容。

**VM 磁盘类型** | **ADE (BEK/dm-crypt)** | **ADE 和 KEK**
--- | --- | ---
**非托管** | 是 | 是
**托管**  | 是 | 是

- 详细了解[ADE](../security/azure-security-disk-encryption-overview.md)、 [Key Vault](../key-vault/key-vault-overview.md)和[kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)。
- 阅读 Azure VM 磁盘加密的[常见问题解答](../security/azure-security-disk-encryption-faq.md)。



### <a name="limitations"></a>限制

- 可以备份和还原同一订阅与区域中的已加密 VM。
- Azure 备份支持使用独立密钥加密的 VM。 目前不支持属于用于加密 VM 的证书的任何密钥。
- 可以备份和还原恢复服务备份保管库所在的同一订阅与区域中的已加密 VM。
- 无法在文件/文件夹级别恢复已加密的 VM。 需要恢复整个 VM 才能还原文件和文件夹。
- 还原 VM 时，无法对已加密的 VM 的使用[替换现有 VM](backup-azure-arm-restore-vms.md#restore-options) 选项。 只有未加密的托管磁盘才支持此选项。




## <a name="before-you-start"></a>开始之前

开始之前，请执行以下操作：

1. 请确保有一个或多个启用了 ADE 的[Windows](../security/azure-security-disk-encryption-windows.md)或[Linux](../virtual-machines/linux/disk-encryption-overview.md) vm。
2. 查看 Azure VM 备份[的支持矩阵](backup-support-matrix-iaas.md)
3. [创建](backup-azure-arm-vms-prepare.md#create-a-vault)一个恢复服务备份保管库（如果没有）。
4. 如果为已启用备份的 VM 启用加密，则只需为备份服务提供 Key Vault 访问权限，这样，备份就可以继续进行，而不会发生中断。 [详细了解](#provide-permissions)如何分配这些权限。

此外，在某些情况下，还需要完成几项操作：

- **在 VM 上安装 VM 代理**：Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。 如果 VM 是从 Azure 市场映像创建的，则代理已安装并正在运行。 如果创建了自定义 VM 或者迁移了本地计算机，则可能需要[手动安装代理](backup-azure-arm-vms-prepare.md#install-the-vm-agent)。
- **显式允许出站访问**：一般情况下，无需显式允许 Azure VM 的出站网络访问即可让它与 Azure 备份进行通信。 但是，某些 VM 在尝试进行连接时可能会遇到连接问题并显示 **ExtensionSnapshotFailedNoNetwork** 错误。 如果发生这种情况，应该[显式允许出站访问](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access)，使 Azure 备份扩展能够与备份流量的 Azure 公共 IP 地址通信。



## <a name="configure-a-backup-policy"></a>配置备份策略

1. 如果尚未创建恢复服务备份保管库，请遵照[这些说明](backup-azure-arm-vms-prepare.md#create-a-vault)操作
2. 在门户中打开保管库，在“开始”部分选择“备份”。

    ![“备份”边栏选项卡](./media/backup-azure-vms-encryption/select-backup.png)

3. 在“备份目标” > “工作负荷在哪里运行?”中，选择“Azure”。
4. 在“要备份哪些内容?”中，选择“虚拟机” > “确定”。

      ![“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 在“备份策略” > “选择备份策略”中，选择要与保管库关联的策略。 然后单击“确定”。
    - 备份策略指定备份创建时间以及这些备份的存储时长。
    - 默认策略的详细信息会在下拉菜单下列出。

    ![打开“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 如果不想要使用默认策略，请选择“新建”，然后[创建自定义策略](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。


7. 选择要使用所选策略备份的已加密 VM，然后选择“确定”。

      ![选择加密 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. 如果使用的是 Azure Key Vault，则保管库页上会显示一条消息，指出 Azure 备份需要对 Key Vault 中的密钥和机密拥有只读访问权限。

    - 如果收到此消息，不需要执行任何操作。

        ![访问正常](./media/backup-azure-vms-encryption/access-ok.png)

    - 如果收到此消息，需要按[以下过程](#provide-permissions)中所述设置权限。

        ![访问警告](./media/backup-azure-vms-encryption/access-warning.png)

9. 单击“启用备份”以在保管库中部署该备份策略，并为选定的 VM 启用备份。


## <a name="trigger-a-backup-job"></a>触发备份作业

初始备份将根据计划运行，但你可以按如下所述手动运行：

1. 在保管库菜单中，单击“备份项”。
2. 在“备份项”中，单击“Azure 虚拟机”。
3. 在“备份项”列表中，单击省略号 (...)。
4. 单击“立即备份”。
5. 在“立即备份”中，使用日历控件选择恢复点的最后保留日期。 然后单击“确定”。
6. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。 创建初始备份可能需要一些时间，具体取决于 VM 的大小。


## <a name="provide-permissions"></a>提供权限

Azure VM 需要拥有只读访问权限才能备份密钥和机密以及关联的 VM。

- Key Vault 与 Azure 订阅的 Azure AD 租户相关联。 如果你是**成员用户**，则 Azure 备份需要有权访问 Key Vault，但不需要你执行进一步的操作。
- 如果你是**来宾用户**，则必须为 Azure 备份提供 Key Vault 访问权限。

设置权限：

1. 在 Azure 门户中，选择“所有服务”并搜索 **Key Vault**。
2. 选择与要备份的已加密 VM 相关联的 Key Vault。
3. 选择“访问策略” > “新增”。
4. 选择“选择主体”，然后键入“备份管理”。
5. 选择“备份管理服务” > “选择”。

    ![备份服务选择](./media/backup-azure-vms-encryption/select-backup-service.png)

6. 在“添加访问策略” > “从模板配置(可选)”中，选择“Azure 备份”。
    - “密钥权限”和“机密权限”中已预先填充所需的权限。
    - 如果 VM 是**仅使用 BEK** 加密的，请删除“密钥权限”对应的选择内容，因为只需要机密的权限。

    ![Azure 备份选择](./media/backup-azure-vms-encryption/select-backup-template.png)

6. 单击 **“确定”** 。 “备份管理服务”随即会添加到“访问策略”中。

    ![访问策略](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. 单击“保存”，为 Azure 备份提供权限。

## <a name="restore-an-encrypted-vm"></a>还原已加密的 VM

按如下所述还原已加密的 VM：

1. [还原 VM 磁盘](backup-azure-arm-restore-vms.md#restore-disks)。
2. 然后执行以下操作之一：
    - 使用执行还原操作期间生成的模板来自定义 VM 设置，并触发 VM 部署。 [了解详细信息](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    - 使用 PowerShell 从还原的磁盘创建新 VM。 [了解详细信息](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。
    - 对于 Linux Vm，请重置 ADE 扩展，使数据磁盘已打开并装入。 

## <a name="next-steps"></a>后续步骤

如果遇到任何问题，请查看以下文章：

- 备份和还原已加密的 Azure VM 时出现的[常见错误](backup-azure-vms-troubleshoot.md)。
- [Azure VM 代理/备份扩展](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)问题。
