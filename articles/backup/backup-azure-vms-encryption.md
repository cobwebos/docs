---
title: "使用 Azure 备份来备份和还原加密 VM"
description: "本文介绍了如何备份和还原使用 Azure 磁盘加密加密的 VM。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/18/2017
ms.author: pajosh;markgal;trinadhk
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: 5de5d42037aee5b0ef7745ea5d0cc748a7b5a60e
ms.lasthandoff: 03/02/2017


---
# <a name="how-to-back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>如何通过 Azure 备份来备份和还原加密的虚拟机
本文介绍使用 Azure 备份来备份和还原虚拟机的步骤。 它还提供了关于受支持的方案、先决条件以及针对错误案例的疑难解答步骤的详细信息。

## <a name="supported-scenarios"></a>支持的方案
> [!NOTE]
> 1. 仅资源管理器部署的虚拟机支持加密 VM 的备份和还原。 而经典虚拟机不支持。 <br>
> 2. 使用 Azure 磁盘加密（利用 Windows 行业标准的 BitLocker 功能和 Linux 的 DM-Crypt 功能来提供磁盘的加密）的 Windows 和 Linux 虚拟机支持。 <br>
> 3. 仅同时使用 BitLocker 加密密钥和 Key 加密秘钥加密的虚拟机支持。 仅使用 BitLocker 加密密钥加密的虚拟机不支持。 <br>
>
>

## <a name="pre-requisites"></a>先决条件
1. 虚拟机已使用 [Azure 磁盘加密](../security/azure-security-disk-encryption.md)进行加密。 它应该同时使用 BitLocker 加密密钥和 Key 加密秘钥进行加密。
2. 已创建恢复服务保管库并已按照文章[为备份做环境准备](backup-azure-arm-vms-prepare.md)中所提到的步骤设置存储复制。

## <a name="backup-encrypted-vm"></a>备份加密 VM
按下列步骤设置备份目标、定义策略、配置项目和触发备份。

### <a name="configure-backup"></a>配置备份
1. 如果已打开恢复服务保管库，请转到下一步。 如果未打开恢复服务保管库，而是位于 Azure 门户中，请在“中心”菜单中单击“浏览”。

   * 在资源列表中，键入“恢复服务”。
   * 当你开始键入时，会根据你的输入筛选该列表。 出现“恢复服务保管库”时，请单击它。

      ![创建恢复服务保管库步骤 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     此时将显示恢复服务保管库列表。 在恢复服务保管库列表中选择一个保管库。

     此时将打开选定的保管库仪表板。
2. 从保管库下显示的项目列表中，单击“备份”以打开“备份”边栏选项卡。

      ![打开“备份”边栏选项卡](./media/backup-azure-vms-encryption/select-backup.png)
3. 在“备份”边栏选项卡中，单击“备份目标”打开“备份目标”边栏选项卡。

      ![打开“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
4. 在“备份目标”边栏选项卡中，将“工作负荷的运行位置”设置为 “Azure”，并将“要备份的项”设置为“虚拟机”，然后单击“确定”。

   随即关闭“备份目标”边栏选项卡，然后打开“备份策略”边栏选项卡。

   ![打开“方案”边栏选项卡](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
5. 在“备份策略”边栏选项卡中选择要应用到保管库的备份策略，然后单击“确定”。

      ![选择备份策略](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    默认策略的详细信息将在详细信息中列出。 如果要创建策略，请从下拉菜单中选择“新建”。 单击“确定”后，备份策略将与保管库相关联。

    接下来，选择要与保管库关联的 VM。
6. 选择要与指定策略关联的已加密虚拟机，然后单击“确定”。

      ![选择加密 VM](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. 此页面显示与所选加密 VM 相关联的密钥保管库的消息。 备份服务需要密钥保管库中密钥和机密的只读访问权限。 它使用这些权限来备份密钥和机密以及关联的 VM。

      ![加密 VM 消息](./media/backup-azure-vms-encryption/encrypted-vm-message.png)

      现在，已定义保管库的所有设置，接下来请在“备份”边栏选项卡中，单击页面底部的“启用备份”。 “启用备份”会将策略部署到保管库和 VM。
8. 下一个阶段的准备工作是安装 VM 代理，或确保 VM 代理已安装。 要执行相同操作，请按文章[为备份做环境准备](backup-azure-arm-vms-prepare.md)中所述步骤进行操作。

### <a name="triggering-backup-job"></a>触发备份作业
按文章[将 Azure VM 备份到恢复服务保管库](backup-azure-arm-vms.md)中所述步骤触发备份作业。

## <a name="restore-encrypted-vm"></a>还原加密 VM
若要还原已加密的 VM，请先使用[选择 VM 还原配置](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)的**还原已备份磁盘**部分中提到的步骤还原磁盘。 之后，使用[基于还原的磁盘创建 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)中提到的 PowerShell 步骤基于还原的磁盘创建完整的 VM。

## <a name="troubleshooting-errors"></a>排查错误
| 操作 | 错误详细信息 | 解决方法 |
| --- | --- | --- |
| 备份 |验证失败，因为虚拟机仅使用 BEK 进行加密。 只能对同时使用 BEK 和 KEK 加密的虚拟机启用备份。 |应使用 BEK 和 KEK 加密虚拟机。 首先解密 VM，然后使用 BEK 和 KEK 对其进行加密。 使用 BEK 和 KEK 对 VM 进行加密后，即可启用备份。 了解有关如何[解密和加密 VM](../security/azure-security-disk-encryption.md) 的详细信息  |
| 还原 |无法还原此加密 VM，因为与之关联的密钥保管库不存在。 |通过 [Azure 密钥保管库入门](../key-vault/key-vault-get-started.md)来创建密钥保管库。 请参阅文章[使用 Azure 备份还原密钥保管库秘钥和机密](backup-azure-restore-key-secret.md)，还原密钥和机密（如果它们不存在）。 |
| 还原 |无法还原此加密 VM，因为与之关联的密钥和机密不存在。 |请参阅文章[使用 Azure 备份还原密钥保管库秘钥和机密](backup-azure-restore-key-secret.md)，还原密钥和机密（如果它们不存在）。 |
| 还原 |备份服务没有权限访问你的订阅中的资源。 |如上所述，先使用[选择 VM 还原配置](backup-azure-arm-restore-vms.md#choosing-a-vm-restore-configuration)的**还原已备份磁盘**部分中提到的步骤还原磁盘。 之后，使用 PowerShell [从还原的磁盘创建 VM](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。

