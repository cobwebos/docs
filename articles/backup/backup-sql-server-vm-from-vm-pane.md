---
title: 从 "VM" 窗格中备份 SQL Server VM
description: 本文介绍如何从 VM 窗格备份 Azure 虚拟机上的 SQL Server 数据库。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88891651"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>从 "VM" 窗格中备份 SQL Server

本文介绍如何通过 [Azure 备份](backup-overview.md) 服务备份在 azure vm 中运行的 SQL Server。 可以使用以下两种方法备份 SQL Server Vm：

- 单个 SQL Server Azure VM：本文中的说明介绍了如何直接从 VM 视图备份 SQL Server VM。
- 多 SQL Server Azure Vm：可以设置一个恢复服务保管库，并为多个 Vm 配置备份。 按照 [此](backup-sql-server-database-azure-vms.md) 方案中的说明进行操作。

## <a name="before-you-start"></a>开始之前

1. 验证你的环境是否具有 [支持矩阵](sql-support-matrix.md)。
2. [大致](backup-azure-sql-database.md)了解适用于 SQL Server VM 的 Azure 备份。
3. 验证 VM 是否已建立[网络连接](backup-sql-server-database-azure-vms.md#establish-network-connectivity)。

## <a name="configure-backup-on-the-sql-server"></a>在 SQL Server 上配置备份

可以从 VM 的 " **备份** " 窗格中启用 SQL Server VM 上的备份。 此方法执行两项操作：

- 向 Azure 备份服务注册 SQL VM 以提供访问权限。
- Autoprotects 在 VM 内运行的所有 SQL Server 实例。 这意味着备份策略将应用于所有现有数据库以及将来将添加到这些实例的数据库。

1. 选择页面顶部的标题以打开 SQL Server 备份 "视图。

    ![SQL Server 备份视图](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >看不到横幅？ 仅对使用 Azure Marketplace 映像创建的 SQL Server Vm 显示版权标志。 此外，还会显示通过 Azure VM 备份进行保护的 Vm。 对于其他映像，可以配置备份，如 [此处](backup-sql-server-database-azure-vms.md)所述。

2. 输入恢复服务保管库名称。 保管库是用于存储和管理所有备份的逻辑实体。 如果创建新的保管库：

    - 它将在与你要保护的 SQL Server VM 相同的订阅和区域中创建。
    - 将用异地冗余存储 (GRS) 设置为所有备份创建该存储。 如果要更改冗余类型，则应在保护 VM 之前执行此操作。 有关详细信息，请参阅[此文章](backup-create-rs-vault.md#set-storage-redundancy)。

3. 选择 **备份策略**。 你可以从默认策略或你在保管库中创建的任何其他现有策略中进行选择。 若要创建新策略，请参阅 [此文](backup-sql-server-database-azure-vms.md#create-a-backup-policy) ，了解分步指南。

    ![选择备份策略](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. 选择“启用备份”。 此操作可能需要几分钟才能完成。

    ![选择 "启用备份"](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. 操作完成后，会在横幅中看到 **保管库名称** 。

    ![保管库名称出现在横幅中](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. 选择横幅以访问保管库视图，在其中可以看到所有已注册的 Vm 及其保护状态。

    ![具有已注册 Vm 的保管库视图](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. 对于非 marketplace 映像，注册可能会成功，但在对 Azure 备份扩展授予对 SQL Server 的权限之前，可能不会触发 **配置备份** 。 在这种情况下，" **备份准备情况** " 列读取 **未就绪**。 需要为非 marketplace 映像手动 [分配适当的权限](backup-azure-sql-database.md#set-vm-permissions) ，以便可以触发 "配置备份"。

    ![备份就绪情况尚未就绪](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. 若要进一步操作或监视需要在备份 SQL Server VM 上执行的操作，请参阅相应的恢复服务保管库。 请访问 " **备份项** " 以查看在此保管库中备份的所有数据库，并触发按需备份和还原等操作。 同样，请参阅 " **备份作业** " 以 [监视](manage-monitor-sql-database-backup.md) 与操作相关的作业，如配置保护、备份和还原。

    ![请参阅备份项中的备份数据库](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>不会自动在可能会添加到受保护 VM 的任何新 SQL Server 实例上配置备份。 若要配置新添加实例上的备份，需要将 VM 注册到的保管库，并按照 [此处](backup-sql-server-database-azure-vms.md)列出的步骤进行操作。

## <a name="next-steps"></a>后续步骤

了解如何：

- [还原已备份的 SQL Server 数据库](restore-sql-database-azure-vm.md)
- [管理已备份的 SQL Server 数据库](manage-monitor-sql-database-backup.md)
