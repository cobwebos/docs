---
title: 在创建 Azure VM 时启用备份
description: 介绍如何在使用 Azure 备份创建 Azure VM 时启用备份。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 0cfea6579791c4fd23c1b7acdfe722d57b5ec2fd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363677"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>在创建 Azure VM 时启用备份

使用 Azure 备份服务备份 Azure 虚拟机（Vm）。 Vm 根据备份策略中指定的计划进行备份，并从备份创建恢复点。 恢复点存储在恢复服务保管库中。

本文详细介绍了在 Azure 门户中创建虚拟机（VM）时如何启用备份。  

## <a name="before-you-start"></a>开始之前

- 如果在创建 VM 时启用备份，[请检查](backup-support-matrix-iaas.md#supported-backup-actions)支持的操作系统。

## <a name="sign-in-to-azure"></a>登录 Azure

如果你尚未登录到你的帐户，请登录到[Azure 门户](https://portal.azure.com)。

## <a name="create-a-vm-with-backup-configured"></a>创建配置了备份的 VM

1. 在 Azure 门户中，单击 "**创建资源**"。

2. 在 Azure Marketplace 中，单击 "**计算**"，然后选择 VM 映像。

3. 按照[Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)或[LINUX](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)说明设置 VM。

4. 在 "**管理**" 选项卡上的 "**启用备份**" 中，单击 **"打开"** 。
5. Azure 备份备份到恢复服务保管库。 如果没有现有的保管库，请单击 "**新建**"。
6. 接受建议的保管库名称或指定自己的保管库名称。
7. 指定或创建将在其中查找保管库的资源组。 资源组保管库可不同于 VM 资源组。

    ![为 VM 启用备份](./media/backup-during-vm-creation/enable-backup.png)

8. 接受默认备份策略，或修改设置。
    - 备份策略指定备份 VM 快照的频率，以及保留这些备份副本所需的时间。
    - 默认策略每天备份一次 VM。
    - 你可以自定义自己的备份策略，以便 Azure VM 每天或每周进行备份。
    - [详细了解](backup-azure-vms-introduction.md#backup-and-restore-considerations)Azure vm 的备份注意事项。
    - [了解](backup-instant-restore-capability.md)有关即时还原功能的详细信息。

      ![默认备份策略](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>适用于虚拟机的 Azure 备份资源组

备份服务创建一个单独的资源组（RG），该资源组与用于存储还原点集合（RPC）的 VM 的资源组不同。 RPC 容纳托管 Vm 的即时恢复点。 备份服务创建的资源组的默认命名格式为： `AzureBackupRG_<Geo>_<number>`。 例如： *AzureBackupRG_northeurope_1*。 你现在可以自定义 Azure 备份创建的资源组名称。

需要注意的要点：

1. 可以使用 RG 的默认名称，也可以根据公司要求对其进行编辑。
2. 可以在创建 VM 时将 RG 名称模式作为输入提供。 RG 名称应采用以下格式： `<alpha-numeric string>* n <alpha-numeric string>`。 "n" 被替换为一个整数（从1开始），并用于在第一个 RG 已满时向外扩展。 目前一个 RG 最多可以有600个 Rpc。
              创建策略时 ![选择名称](./media/backup-during-vm-creation/create-policy.png)
3. 该模式应遵循下面的 RG 命名规则，并且总长度不应超过允许的最大 RG 名称长度。
    1. 资源组名称仅允许字母数字字符、句点、下划线、连字符和括号。 它们不能以句点结束。
    2. 资源组名称最多可包含74个字符，包括 RG 名称和后缀。
4. 第一个 `<alpha-numeric-string>` 是必需的，而 "n" 后面的第二个则是可选的。 这仅适用于你为自定义的名称。 如果未在任何文本框中输入任何内容，则使用默认名称。
5. 如果需要，可以通过修改策略来编辑 RG 的名称。 如果更改了名称模式，将在新 RG 中创建新的 RPs。 但是，旧的 RPs 仍将驻留在旧 RG 中，因此不会移动，因为 RP 集合不支持资源移动。 最终，当点过期时，RPs 将会进行垃圾回收。
修改策略时 ![更改名称](./media/backup-during-vm-creation/modify-policy.png)
6. 建议不要锁定为备份服务使用而创建的资源组。

## <a name="start-a-backup-after-creating-the-vm"></a>创建 VM 后开始备份

VM 备份将按照备份策略运行。 但是，我们建议你运行初始备份。

创建 VM 后，请执行以下操作：

1. 在 VM 属性中，单击 "**备份**"。 VM 状态是初始备份挂起，直到初始备份运行
2. 单击 "**立即备份**" 来运行按需备份。

    ![运行按需备份](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>使用资源管理器模板来部署受保护的 VM

以上步骤说明了如何使用 Azure 门户在恢复服务保管库中创建虚拟机并对其进行保护。 若要快速部署一个或多个 Vm 并在恢复服务保管库中对其进行保护，请参阅模板[部署 WINDOWS VM 并启用备份](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)。

## <a name="next-steps"></a>后续步骤

现在，你已经保护了 VM，接下来请了解如何对其进行管理和还原。

- [管理和监视 Vm](backup-azure-manage-vms.md)
- [还原 VM](backup-azure-arm-restore-vms.md)

如果遇到任何问题，请[查看](backup-azure-vms-troubleshoot.md)故障排除指南。
