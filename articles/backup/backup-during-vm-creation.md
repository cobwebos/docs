---
title: 在创建 Azure VM 时启用备份
description: 介绍了如何在创建 Azure VM 时通过 Azure 备份启用备份。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 7739109eb8bad88c9b723e67e13adc78c127499a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80672810"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>在创建 Azure VM 时启用备份

使用 Azure 备份服务备份 Azure 虚拟机 (VM)。 VM 是根据在备份策略中指定的计划备份的，恢复点是基于备份创建的。 恢复点存储在恢复服务保管库中。

本文详细介绍了在 Azure 门户中创建虚拟机 (VM) 时如何启用备份。  

## <a name="before-you-start"></a>开始之前

- 如果在创建 VM 时启用备份，请[检查](backup-support-matrix-iaas.md#supported-backup-actions)支持哪些操作系统。

## <a name="sign-in-to-azure"></a>登录 Azure

如果尚未登录到你的帐户，请先登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-vm-with-backup-configured"></a>创建配置了备份的 VM

1. 在 Azure 门户中，单击“创建资源”。

2. 在 Azure 市场中，单击“计算”，然后选择一个 VM 映像。

3. 根据适用于 [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 或 [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 的说明设置 VM。

4. 在“管理”选项卡上，在“启用备份”中，单击“开启”。
5. Azure 备份将备份到恢复服务保管库。 如果没有现有的保管库，请单击“新建”。
6. 接受建议的保管库名称或自己指定名称。
7. 指定或创建保管库将位于其中的资源组。 资源组保管库可以不同于 VM 资源组。

    ![为 VM 启用备份](./media/backup-during-vm-creation/enable-backup.png)

8. 接受默认备份策略，或修改设置。
    - 备份策略指定执行 VM 备份快照的频率和这些备份副本的保留期。
    - 默认策略每天备份 VM 一次。
    - 你可以为 Azure VM 自定义你自己的备份策略以便每日或每周进行备份。
    - [详细了解](backup-azure-vms-introduction.md#backup-and-restore-considerations) Azure VM 的备份注意事项。
    - [详细了解](backup-instant-restore-capability.md)即时还原功能。

      ![默认备份策略](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>虚拟机的 Azure 备份资源组

备份服务将创建一个单独的资源组 (RG) 而非 VM 的资源组来存储还原点集合 (RPC)。 RPC 容纳托管 VM 的即时恢复点。 备份服务创建的资源组的默认命名格式为 `AzureBackupRG_<Geo>_<number>`。 例如：*AzureBackupRG_northeurope_1*。 现在可以自定义 Azure 备份创建的资源组名称。

需要注意的要点：

1. 可以使用 RG 的默认名称，也可以根据公司要求对其进行编辑。
2. 可以在创建 VM 备份策略时将 RG 名称模式作为输入提供。 RG 名称应采用以下格式：`<alpha-numeric string>* n <alpha-numeric string>`。 “n”将替换为一个整数（从 1 开始），用于在第一个 RG 已满时进行横向扩展。 目前一个 RG 最多可以有 600 个 RPC。
              ![创建策略时选择名称](./media/backup-during-vm-creation/create-policy.png)
3. 该模式应遵循下面的 RG 命名规则，并且总长度不应超过允许的最大 RG 名称长度。
    1. 资源组名称只允许使用字母数字字符、句点、下划线、连字符和括号。 它们不能以句点结尾。
    2. 资源组名称最多可包含 74 个字符，包括 RG 名称和后缀。
4. 第一个 `<alpha-numeric-string>` 是必需的，但“n”后面的第二个是可选的。 这仅适用于你提供自定义名称的情况。 如果未在任何文本框中输入任何内容，则使用默认名称。
5. 如果需要，可以通过修改策略来编辑 RG 的名称。 如果更改了名称模式，将在新 RG 中创建新的 RP。 但是，旧的 RP 仍将驻留在旧 RG 中，不会被删除，因为 RP 集合不支持资源移动。 最终，当点过期时，RP 会进行垃圾回收。
![修改策略时更改名称](./media/backup-during-vm-creation/modify-policy.png)
6. 建议不要锁定为了供备份服务使用而创建的资源组。

若要使用 PowerShell 为虚拟机配置 Azure 备份资源组，请参阅[在快照保留期间创建 Azure 备份资源组](backup-azure-vms-automation.md#creating-azure-backup-resource-group-during-snapshot-retention)。

## <a name="start-a-backup-after-creating-the-vm"></a>在创建 VM 后启动备份

你的 VM 备份将根据备份策略运行。 但是，我们建议你运行一个初始备份。

创建 VM 后，请执行以下操作：

1. 在 VM 属性中，单击“备份”。 VM 状态将保持为“初始备份挂起”，直到初始备份运行
2. 单击“立即备份”来运行按需备份。

    ![运行按需备份](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用资源管理器模板部署一个受保护的 VM

前面的步骤说明了如何使用 Azure 门户来创建虚拟机，以及使用恢复服务保管库来保护该虚拟机。 若要快速部署一台或多台虚拟机并在恢复服务保管库中保护它们，请参阅模板[部署 Windows VM 并启用备份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。

## <a name="next-steps"></a>后续步骤

现在，你已保护了你的 VM，请了解如何管理和还原它们。

- [管理和监视 VM](backup-azure-manage-vms.md)
- [还原 VM](backup-azure-arm-restore-vms.md)

如果遇到任何问题，请[查看](backup-azure-vms-troubleshoot.md)故障排除指南。
