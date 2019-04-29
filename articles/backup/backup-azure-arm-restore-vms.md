---
title: Azure 备份：使用 Azure 门户还原虚拟机
description: 使用 Azure 门户从恢复点还原 Azure 虚拟机
services: backup
author: geethalakshmig
manager: vijayts
keywords: 还原备份; 如何还原; 恢复点;
ms.service: backup
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: geg
ms.openlocfilehash: b0408aa296dcbff0c73f2c192e24c290d51fec5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647682"
---
# <a name="restore-azure-vms"></a>还原 Azure VM

本文介绍如何从 [Azure 备份](backup-overview.md)恢复服务保管库中存储的恢复点还原 Azure VM 数据。



### <a name="restore-options"></a>还原选项

Azure 备份提供多种方法用于还原 VM。

**还原选项** | **详细信息**
--- | ---
**创建新 VM** | 从还原点快速创建并正常运行一个基本的 VM。<br/><br/> 可以指定 VM 的名称、选择要将此 VM 放入到的资源组和虚拟网络 (VNet)，以及指定存储类型。
**还原磁盘** | 还原某个 VM 磁盘，然后使用它来创建新的 VM。<br/><br/> Azure 备份提供一个模板来帮助你自定义和创建 VM。 <br/><br/> 此选项将 VHD 复制到所指定的存储帐户。 还原作业会生成一个模板，可以下载该模板，并使用它来指定自定义的 VM 设置和创建 VM。<br/><br/> 存储帐户应位于保管库所在的同一位置。 创建存储帐户（如果没有）。<br/><br/> 将显示存储帐户复制类型。 不支持区域冗余存储 (ZRS)。<br/><br/> 或者，可将磁盘附加到现有 VM，或使用 PowerShell 创建新的 VM。<br/><br/> 若要自定义 VM、添加在备份时不存在的配置设置，或添加必须使用模板或 PowerShell 配置的设置，则此选项非常有用。
**替换现有** | 可以还原某个磁盘，并使用它来替换现有 VM 上的磁盘。<br/><br/> 必须存在当前 VM。 若已删除 VM，则无法使用该选项。<br/><br/> 在替换磁盘之前，Azure 备份会创建现有 VM 的快照。 快照存储在指定的暂存位置。 然后，使用选定的还原点替换已连接到 VM 的现有磁盘。<br/><br/> 创建的快照将复制到保管库，并根据指定的保留策略进行保留。 <br/><br/> 未加密的托管 VM 支持替换现有。 非托管磁盘、[通用 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 或[使用自定义映像创建的 VM](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) 不支持此选项。<br/><br/> 如果还原点中的磁盘数多于或少于当前 VM 中的磁盘数，则还原点中的磁盘数只反映 VM 配置。<br/><br/>

> [!NOTE]
> 还可以恢复 Azure VM 上的特定文件和文件夹。 [了解详细信息](backup-azure-restore-files-from-vm.md)。
>
> 如果运行[最新版本](backup-instant-restore-capability.md)的适用于 Azure VM 的 Azure 备份（称为“即时还原”），则快照最长将保留 7 天；在将备份数据发送到保管库之前，可以从快照还原 VM。 若要从过去 7 天的备份还原 VM，更快的做法是从快照而不是从保管库还原。

## <a name="before-you-start"></a>开始之前

若要还原的 VM （创建新的 VM） 请确保具有正确的基于角色的访问控制 (RBAC)[权限](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions)还原 VM 操作。

如果你没有权限，则可以[还原磁盘](#restore-disks)，然后还原磁盘后，你可以[使用的模板](#use-templates-to-customize-a-restored-vm)生成作为要创建新的 VM 的还原操作的一部分。



## <a name="select-a-restore-point"></a>选择还原点

1. 在与要还原的 VM 关联的保管库中，单击“备份项” > “Azure 虚拟机”。
2. 单击某个 VM。 VM 仪表板默认会显示过去 30 天的恢复点。 可以显示 30 天以前的恢复点，或者根据日期、时间范围和不同类型的快照一致性进行筛选，以找到所需的恢复点。
3. 若要还原 VM，请单击“还原 VM”。

    ![还原点](./media/backup-azure-arm-restore-vms/restore-point.png)

4. 选择用于恢复的还原点。

## <a name="choose-a-vm-restore-configuration"></a>选择 VM 还原配置

1. 在“还原配置”中选择一个还原选项：
    - **新建**：若要创建新的 VM，请使用此选项。 可以使用简单的设置创建 VM，或还原某个磁盘并创建自定义的 VM。
    - **替换现有项**：若要替换现有 VM 上的磁盘，请使用此选项。

        ![还原配置向导](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. 为选定的还原选项指定设置。

## <a name="create-a-vm"></a>创建 VM

作为[还原选项](#restore-options)之一，你可以使用基本的设置从还原点快速创建 VM。

1. 在“还原配置” > “新建 > 还原类型”中，选择“创建虚拟机”。
2. 在“虚拟机名称”中，指定订阅中不存在的 VM。
3. 在“资源组”中，为新 VM 选择现有资源组，或创建具有全局唯一名称的新资源组。 如果分配的名称已存在，则 Azure 将为该组分配与 VM 相同的名称。
4. 在“虚拟网络”中，选择将放置 VM 的 VNet。 将显示与订阅关联的所有 VNet。 选择子网。 默认情况下选择第一个子网。
5. 在“存储位置”中，指定 VM 的存储类型。

    ![还原配置向导](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. 在“还原配置”中，选择“确定”。 在“还原”中，单击“还原”以触发还原操作。


## <a name="restore-disks"></a>还原磁盘

作为[还原选项](#restore-options)之一，你可以从还原点快速创建磁盘。 然后可以使用该磁盘执行以下操作之一：

- 使用执行还原操作期间生成的模板来自定义设置，并触发 VM 部署。 可编辑默认模板设置，并提交 VM 部署模板。
- [将已还原的磁盘附加](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)到现有 VM。
- [创建新的 VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks)从还原的磁盘使用 PowerShell。

1. 在“还原配置” > “新建 > 还原类型”中，选择“还原磁盘”。
2. 在“资源组”中，为还原的磁盘选择现有资源组，或创建具有全局唯一名称的新资源组。
3. 在“存储帐户”中，指定要将 VHD 复制到的帐户。 确保帐户与保管库位于同一区域。

    ![已完成恢复配置](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. 在“还原配置”中，选择“确定”。 在“还原”中，单击“还原”以触发还原操作。

在 VM 还原期间，Azure 备份不使用存储帐户。 但在使用**还原磁盘**和**即时还原**时，存储帐户用于存储模板。

### <a name="use-templates-to-customize-a-restored-vm"></a>使用模板自定义还原 VM

还原磁盘后，使用执行还原操作期间生成的模板进行自定义并创建新的 VM：

1. 打开相关作业的“还原作业详细信息”。

2. 在“还原作业详细信息”中，选择“部署模板”启动模板部署。

    ![深入到还原作业](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. 若要自定义模板中提供的 VM 设置，请单击“编辑模板”。 若要添加其他自定义项，请单击“编辑参数”。
    - [详细了解](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)如何从自定义模板部署资源。
    - [详细了解](../azure-resource-manager/resource-group-authoring-templates.md)如何创作模板。

   ![加载模板部署](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. 输入 VM 的自定义值，接受“条款和条件”，然后单击“购买”。

   ![提交模板部署](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>替换现有磁盘

作为[还原选项](#restore-options)之一，你可以使用选定的还原点替换现有的 VM 磁盘。 [查看](#restore-options)所有还原选项。

1. 在“还原配置”中，单击“替换现有”。
2. 在“还原类型”中，选择“替换磁盘”。 这是用于替换现有 VM 磁盘的还原点。
3. 在“暂存位置”中，指定应保存当前托管磁盘的快照的位置。

   ![还原配置向导“替换现有”](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>采用特殊配置还原 VM

在许多常见情况下，可能需要还原 VM。

**方案** | **指南**
--- | ---
**通过混合使用权益还原 VM** | 如果 Windows VM 使用[混合使用权益 (HUB) 许可](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，请还原磁盘，并使用提供的模板（将“许可证类型”设置为“Windows_Server”）或 PowerShell 创建新的 VM。  创建 VM 后也可以应用此设置。
**在发生 Azure 数据中心灾难期间还原 VM** | 如果保管库使用 GRS 并且 VM 的主数据中心出现故障，Azure 备份支持将已备份的 VM 还原到配对的数据中心。 在配对的数据中心选择一个存储帐户，然后像平时一样进行还原。 Azure 备份使用配对位置中的计算服务来创建已还原的 VM。 [详细了解](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md)数据中心复原能力。
**还原单个域中的单个域控制器 VM** | 像还原其他任何 VM 一样还原该 VM。 请注意：<br/><br/> 从 Active Directory 的角度来看，Azure VM 与任何其他 VM 类似。<br/><br/> 还可使用目录服务还原模式 (DSRM)，因此所有 Active Directory 恢复方案都是可行的。 [详细了解](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v)虚拟化域控制器的备份和还原注意事项。
**还原单一域中的多个域控制器 VM** | 如果可以通过网络访问同一个域中的其他域控制器，则可以像还原任何 VM 一样还原域控制器。 对于域中剩余的最后一个域控制器，或者在隔离的网络中执行恢复，请使用[林恢复](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。
**还原一个林中的多个域** | 建议使用[林恢复](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)。
**裸机还原** | Azure VM 与本地虚拟机监控程序之间的主要差别是 Azure 中不提供 VM 控制台。 某些方案（如使用裸机恢复 (BMR) 类型备份进行恢复）需要控制台。 但是，通过保管库进行 VM 还原完全取代了 BMR。
**还原采用特殊网络配置的 VM** | 特殊网络配置包括使用内部或外部负载均衡、使用多个 NIC 或多个保留 IP 地址的 VM。 可使用[还原磁盘选项](#restore-disks)还原这些 VM。 此选项会将 VHD 复制到指定的存储帐户，然后，你可以根据配置，使用[内部](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)或[外部](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/)负载均衡器、[多个 NIC](../virtual-machines/windows/multiple-nics.md) 或[多个保留 IP 地址](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)创建 VM。
**NIC/子网上的网络安全组 (NSG)** | Azure VM 备份支持在 VNet、子网和 NIC 级别备份和还原 NSG 信息。
**区域固定的 VM** | Azure Backup 支持备份和还原区域固定的 VM。 [了解详细信息](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>跟踪还原操作
触发还原操作后，备份服务会创建一个作业用于跟踪。 Azure 备份在门户中显示有关作业的通知。 如果未显示通知，单击“通知”符号即可显示。

![已触发还原](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 按如下所述跟踪还原：

1. 若要查看针对作业的操作，请单击通知超链接。 或者，在保管库中单击“备份作业”，然后单击相关的 VM。

    ![保管库中的 VM 列表](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. 若要监视还原进度，请单击状态为“正在进行”的任何还原作业。 此时会出现进度栏，其中显示了有关还原进度的信息：

    - **还原估计所需时间**：最初提供完成还原操作所需的时间。 随着操作的不断进行，该时间会不断减少，还原操作完成后，将显示为 0。
    - **还原百分比**。 显示还原操作的完成百分比。
    - **传输的字节数**：如果通过创建新的 VM 进行还原，此字段将显示已传输的字节数，以及有待传输的字节总数。

## <a name="post-restore-steps"></a>还原后的步骤

还原 VM 之后，还需要注意一些事项：

- 将安装备份配置期间存在的扩展，但不会启用这些扩展。 如果出现问题，请重新安装这些扩展。
- 如果备份的 VM 使用了静态 IP 地址，则还原的 VM 将使用动态 IP 地址，以避免冲突。 可[将静态 IP 地址添加到还原的 VM](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)。
- 还原的 VM 没有可用性集。 如果使用了还原磁盘选项，则从磁盘创建 VM 时，可以使用提供的模板或 PowerShell [指定可用性集](../virtual-machines/windows/tutorial-availability-sets.md)。
- 如果使用基于 cloud-init 的 Linux 分发版（例如 Ubuntu），出于安全原因，还原后将阻止密码。 请在还原的 VM 上使用 VMAccess 扩展 [重置密码](../virtual-machines/linux/reset-password.md)。 我们建议在这些分发版中使用 SSH 密钥，这样，在还原后就无需重置密码。


## <a name="backing-up-restored-vms"></a>备份已还原的 VM

- 如果将 VM 还原到了与最初备份 VM 时所在的资源组同名的资源组，则还原之后，会继续备份该 VM。
- 如果将 VM 还原到了其他资源组或者为还原的 VM 指定了其他名称，则需要为还原的 VM 设置备份。

## <a name="next-steps"></a>后续步骤

- 如果在还原过程中遇到难题，请[查看](backup-azure-vms-troubleshoot.md#restore)常见问题和错误。
- 还原 VM 后，请了解如何[管理虚拟机](backup-azure-manage-vms.md)
