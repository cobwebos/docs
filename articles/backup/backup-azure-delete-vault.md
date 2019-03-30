---
title: 删除 Azure 中的恢复服务保管库
description: 介绍如何删除恢复服务保管库。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: raynew
ms.openlocfilehash: 23e98fd7ea3decc478fc359cec457c70b8fc99dc
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652207"
---
# <a name="delete-a-recovery-services-vault"></a>删除恢复服务保管库

本文介绍如何删除[Azure 备份](backup-overview.md)恢复服务保管库。 它包含有关删除依赖项，然后删除保管库，并删除强行保管库的说明。


## <a name="before-you-start"></a>开始之前

在开始之前，务必了解，不能删除具有服务器的恢复服务保管库中注册它，或保留备份数据。

- 若要正常删除保管库，取消注册在它的服务器、 删除保管库数据，然后删除该保管库。
- 如果你尝试删除仍具有依赖关系的保管库，则发出一条错误消息。 并且需要手动删除保管库依赖项，包括：
    - 备份项
    - 受保护的服务器
    - 备份管理服务器 （Azure 备份服务器，DPM）![选择保管库以打开其仪表板](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- 如果您不想保留任何数据恢复服务保管库，并想要删除保管库，可以删除强行保管库。
- 如果尝试删除保管库但未成功，此保管库仍配置为接收备份数据。


## <a name="delete-a-vault-from-the-azure-portal"></a>Azure 门户中删除保管库

1. 打开保管库仪表板。  
2. 在仪表板中，单击**删除**。 验证要删除。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

如果收到错误，删除[备份项](#remove-backup-items)，[基础结构服务器](#remove-backup-infrastructure-servers)，并[恢复点](#remove-azure-backup-agent-recovery-points)，然后删除该保管库。

![删除保管库错误](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>强制删除恢复服务保管库

可以删除通过使用 PowerShell 强制保管库。 强制删除意味着被永久删除保管库和所有关联的备份数据。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


若要删除强行保管库：

1. 登录到 Azure 订阅与`Connect-AzAccount`命令，并按照屏幕说明操作。

   ```powershell
    Connect-AzAccount
   ```
2. 首次使用 Azure 备份时，必须使用 [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) 在订阅中注册 Azure 恢复服务提供程序。

   ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

3. 使用管理员特权打开 PowerShell 窗口。
4. 运行 `Set-ExecutionPolicy Unrestricted` 命令以撤消任何限制。
5. 运行以下命令，从 chocolately.org 下载 Azure 资源管理器客户端包。

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

6. 运行以下命令，安装 Azure 资源管理器 API 客户端。

   `choco.exe install armclient`

7. 在 Azure 门户中，收集你想要删除的保管库的订阅 ID 和资源组名称。
8. 在 PowerShell 中运行以下命令使用的订阅 ID、 资源组名称和保管库名称。 运行此命令后，它会删除保管库及其所有依赖项。

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
9. 如果保管库的不为空，将收到错误"因为没有在此保管库中的现有资源，无法删除保管库"。 若要删除包含在保管库中，执行以下操作：

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

10. 在 Azure 门户中，验证已删除该保管库。


## <a name="remove-vault-items-and-delete-the-vault"></a>删除保管库项，然后删除该保管库

这些过程提供一些示例删除备份数据和基础结构服务器。 从保管库删除的所有组件后，可以将其删除。

### <a name="remove-backup-items"></a>删除备份项

此过程提供了一个示例，演示如何从 Azure 文件中删除备份数据。

1. 单击**备份项** > **Azure 存储 （Azure 文件）**

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 右键单击要删除，请单击每个 Azure 文件项**停止备份**。

    ![选择备份类型](./media/backup-azure-delete-vault/stop-backup-item.png)


3. 在中**停止备份** > **选择一个选项**，选择**删除备份数据**。
4. 键入项的名称，然后单击**停止备份**。
   - 这将验证你想要删除的项。
   - **停止备份**在验证后，按钮会激活。
   - 如果保留并不删除数据，您将无法删除保管库。

     ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 根据需要提供相应原因，你为什么要删除数据，并添加注释。
6. 若要验证已删除作业，检查 Azure 消息 ![删除备份数据](./media/backup-azure-delete-vault/messages.png).
7. 作业完成后，服务将发送一条消息：**备份过程已停止并且备份数据已被删除**。
8. 删除列表中的项后，请单击“备份项”菜单上的“刷新”，以查看保管库中的项。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)


### <a name="remove-backup-infrastructure-servers"></a>删除备份基础结构服务器

1. 在保管库仪表板菜单中，单击**备份基础结构**。
2. 单击**备份管理服务器**若要查看的服务器。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

2. 右键单击该项目 >**删除**。

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

3. . 在中**停止备份** > **选择一个选项**，选择**删除备份数据**。
4. 键入项的名称，然后单击**停止备份**。
   - 这将验证你想要删除的项。
   - **停止备份**在验证后，按钮会激活。
   - 如果保留并不删除数据，您将无法删除保管库。

     ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 根据需要提供相应原因，你为什么要删除数据，并添加注释。
6. 若要验证已删除作业，检查 Azure 消息 ![删除备份数据](./media/backup-azure-delete-vault/messages.png).
7. 作业完成后，服务将发送一条消息：**备份过程已停止并且备份数据已被删除**。
8. 删除列表中的项后，请单击“备份项”菜单上的“刷新”，以查看保管库中的项。


### <a name="remove-azure-backup-agent-recovery-points"></a>删除 Azure 备份代理恢复点

1. 在保管库仪表板菜单中，单击**备份基础结构**。
2. 单击**备份管理服务器**若要查看基础结构服务器。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. 在“受保护的服务器”列表中，单击“Azure 备份代理”。

    ![选择备份类型](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. 单击在保护使用 Azure 备份代理的服务器列表中的服务器。

    ![选择受保护的具体服务器](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 在所选的服务器仪表板，单击**删除**。

    ![删除选定服务器](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 在“删除”菜单上，键入项名称，再单击“删除”。
   - 这将验证你想要删除的项。
   - **停止备份**在验证后，按钮会激活。
   - 如果保留并不删除数据，您将无法删除保管库。

     ![删除备份数据](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. 根据需要提供相应原因，你为什么要删除数据，并添加注释。
8. 若要验证已删除作业，检查 Azure 消息 ![删除备份数据](./media/backup-azure-delete-vault/messages.png).
7. 作业完成后，服务将发送一条消息：**备份过程已停止并且备份数据已被删除**。
8. 删除列表中的项后，请单击“备份项”菜单上的“刷新”，以查看保管库中的项。






### <a name="delete-the-vault-after-removing-dependencies"></a>删除依赖项后删除该保管库

1. 如果已删除所有依赖项，滚动到**Essentials**保管库菜单中的窗格。
2. 验证是否没有任何**备份项**，**备份管理服务器**，或**复制的项**列出。 如果在保管库中仍有项目，请将其删除。

2. 当保管库中没有其他任何项时，请单击保管库仪表板上的“删除”。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. 若确认要删除保管库，请单击“是”。 随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>如果我停止了备份过程，但仍保留数据，会怎么样？

如果停止备份过程但意外保留数据，则必须删除备份数据，如前面各节中所述。

## <a name="next-steps"></a>后续步骤

[了解有关](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
