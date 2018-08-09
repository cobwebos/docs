---
title: 删除 Azure 中的恢复服务保管库
description: 本文介绍了如何删除恢复服务保管库。 本文包括了尝试删除保管库但无法删除时可采用的疑难解答步骤。
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 7/6/2018
ms.author: markgal
ms.openlocfilehash: 4dc5b006be8599177fb908fe022a3a821b137e12
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422937"
---
# <a name="delete-a-recovery-services-vault"></a>删除恢复服务保管库

本文介绍了如何删除恢复服务保管库中的所有项，然后删除此保管库。 无法删除已注册到服务器且保留备份数据的恢复服务保管库。 如果尝试删除保管库但未成功，此保管库仍配置为接收备份数据。

若要了解如何删除保管库，请参阅[在 Azure 门户中删除保管库](backup-azure-delete-vault.md#delete-a-vault-from-azure-portal)部分。 如果不想保留恢复服务保管库中的任何数据，并要删除此保管库，请参阅[强制删除保管库](backup-azure-delete-vault.md#delete-the-recovery-services-vault-by-force)部分。 如果不确定保管库中的内容，并需要确保可以删除保管库，请参阅[删除保管库依赖项并删除保管库](backup-azure-delete-vault.md#remove-vault-dependencies-and-delete-vault)部分。

## <a name="delete-a-vault-from-azure-portal"></a>在 Azure 门户中删除保管库

如果已打开恢复服务保管库，请跳到第二步。

1. 打开 Azure 门户，并从仪表板打开要删除的保管库。

   如果没有将恢复服务保管库固定到仪表板，请单击“中心”菜单上的“所有服务”，再在资源列表中键入“恢复服务”。 开始键入时，会根据输入筛选该列表。 若要查看订阅中的保管库列表，请单击“恢复服务保管库”。

   ![创建恢复服务保管库步骤 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   此时会显示恢复服务保管库列表。 

   ![从列表中选择保管库](./media/backup-azure-delete-vault/choose-vault-to-delete-.png)

1. 从列表中选择要删除的保管库。 选择保管库后，它的保管库仪表板会打开。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

1. 若要删除保管库，请单击保管库仪表板中的“删除”。 系统会要求确认是否要删除保管库。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/click-delete-button-to-delete-vault.png)

    如果看到“保管库删除错误”，可以从保管库中删除依赖项，也可以使用 PowerShell 强制删除保管库。 下面各部分介绍了如何完成这些任务。

    ![保管库删除错误](./media/backup-azure-delete-vault/vault-delete-error.png)


## <a name="delete-the-recovery-services-vault-by-force"></a>强制删除恢复服务保管库

PowerShell 可用于强制删除恢复服务保管库。 强制删除意味着永久删除恢复服务保管库及其所有关联备份数据。 

> [!Warning]
> 使用 PowerShell 删除恢复服务保管库时，请确保自己要永久删除保管库中的所有备份数据。
>

若要强制删除恢复服务保管库，请执行以下操作：

1. 登录到 Azure 帐户。

   运行 `Connect-AzureRmAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
    Connect-AzureRmAccount
   ```
   首次使用 Azure 备份时，必须使用 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 在订阅中注册 Azure 恢复服务提供程序。

   ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
   ```

1. 使用管理员特权打开 PowerShell 窗口。

1. 运行 `Set-ExecutionPolicy Unrestricted` 命令以撤消任何限制。

1. 运行以下命令，从 chocolately.org 下载 Azure 资源管理器客户端包。

    `iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

1. 运行以下命令，安装 Azure 资源管理器 API 客户端。

   `choco.exe install armclient`

1. 在 Azure 门户中，为要删除的恢复服务保管库收集订阅 ID 和关联的资源组名称。

1. 在 PowerShell 中，使用订阅 ID、资源组名称和恢复服务保管库名称运行以下命令。 运行此命令后，它会删除保管库及其所有依赖项。

   ```powershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
1. 在 Azure 门户中登录订阅，并确认保管库是否已遭删除。


## <a name="remove-vault-dependencies-and-delete-vault"></a>删除保管库依赖项并删除保管库

若要手动删除保管库依赖项，请删除每个项或服务器和恢复服务保管库之间的配置。 执行下面的过程时，请全程将“备份项”菜单（见下图）用于：

* Azure 存储（Azure 文件）备份
* Azure VM 中的 SQL Server 备份
* Azure 虚拟机备份
* Microsoft Azure 恢复服务代理备份

将“备份基础结构”菜单（见下图）用于：

* Azure 备份服务器备份
* System Center DPM 备份

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)

1. 在保管库仪表板菜单中，向下滚动到“受保护的项”部分，再单击“备份项”。 在此菜单中，可以停止和删除 Azure 文件服务器、Azure VM 中的 SQL Server 和 Azure 虚拟机。 此示例将从 Azure 文件服务器中删除备份数据。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/selected-backup-items.png)

1. 选择备份类型，以查看相应类型的所有项。

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

1. 对于列表中的所有项，右键单击相应项，再选择关联菜单中的“停止备份”。

    ![选择备份类型](./media/backup-azure-delete-vault/stop-backup-item.png) 

    此时会打开“停止备份”菜单。

1. 在“停止备份”菜单中，选择“选择选项”菜单中的“删除备份数据”，键入项名称，再单击“停止备份”。

    键入项名称以确认要将其删除。 验证该项后，“停止备份”按钮会激活。 如果保留数据，便无法删除保管库。

    ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    根据需要，提供删除数据的原因，并添加注释。 若要确认作业是否已完成，请检查 Azure 消息 ![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 <br/>
    作业完成后，服务就会发送以下消息：“备份过程已停止，备份数据已删除”。

1. 删除列表中的项后，请单击“备份项”菜单上的“刷新”，以查看保管库中的项。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

      如果列表中没有项，则滚动到“恢复服务保管库”菜单中的“概要”窗格。 此时不应列出任何“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 如果保管库中仍有项目，请返回到第三步，并选择不同的项类型列表。  

1. 当保管库工具栏中没有其他项时，请单击“**删除**”。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. 若确认要删除保管库，请单击“是”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="removing-azure-backup-server-or-dpm"></a>删除 Azure 备份服务器或 DPM

1. 在保管库仪表板菜单中，向下滚动到“管理”部分，再单击“备份基础结构”。 

1. 在子菜单中，单击“备份管理服务器”，以查看 Azure 备份服务器和 System Center DPM 服务器。 可以停止和删除 Azure 文件服务器、Azure VM 中的 SQL Server 和 Azure 虚拟机。 

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

1. 右键单击要删除的项，并选择子菜单中的“删除”。

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

    此时会打开“停止备份”菜单。

1. 在“停止备份”菜单中，选择“选择选项”菜单中的“删除备份数据”，键入项名称，再单击“停止备份”。

    为了确认自己确实要删除它，请键入它的名称。 验证该项后，“停止备份”按钮会激活。 如果保留数据，便无法删除保管库。

    ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    （可选）可以提供删除数据的原因并添加注释。 若要验证作业是否已完成，请检查 Azure Messages ![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 <br/>
    作业完成后，该服务将发送一条消息：备份过程已停止，并且备份数据已被删除。

1. 删除列表中的项目后，请在“**备份项**”菜单上单击“**刷新**”，以查看保管库中的其余项目。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

      如果列表中没有项，则滚动到“恢复服务保管库”菜单中的“概要”窗格。 此时不应列出任何“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 如果保管库中仍有项目，请返回到第三步，并选择不同的项类型列表。  
1. 当保管库中没有其他任何项时，请单击保管库仪表板上的“删除”。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. 若确认要删除保管库，请单击“是”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。


## <a name="removing-azure-backup-agent-recovery-points"></a>删除 Azure 备份代理恢复点

1. 在保管库仪表板菜单中，向下滚动到“管理”部分，再单击“备份基础结构”。

1. 在子菜单中，单击“受保护的服务器”，以查看受保护的服务器类型列表，其中包括 Azure 备份代理。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

1. 在“受保护的服务器”列表中，单击“Azure 备份代理”。

    ![选择备份类型](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

    此时，使用 Azure 备份代理保护的服务器列表打开。

    ![选择受保护的具体服务器](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

1. 在服务器列表中，单击一个服务器，以打开它的菜单。

    ![查看选定服务器的仪表板](./media/backup-azure-delete-vault/selected-protected-server.png)

1. 在选定服务器的仪表板菜单上，单击“删除”。

    ![删除选定服务器](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

1. 在“删除”菜单上，键入项名称，再单击“删除”。

    键入项名称以确认要将其删除。 在确认要删除项后，“删除”按钮就会激活。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

    （可选）可以提供删除数据的原因并添加注释。 若要验证作业是否已完成，请检查 Azure Messages ![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 <br/>
    作业完成后，该服务将发送一条消息：备份过程已停止，并且备份数据已被删除。

1. 删除列表中的项目后，请在“**备份项**”菜单上单击“**刷新**”，以查看保管库中的其余项目。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

      如果列表中没有项，则滚动到“恢复服务保管库”菜单中的“概要”窗格。 此时不应列出任何“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 如果保管库中仍有项目，请返回到第三步，并选择不同的项类型列表。  
1. 当保管库中没有其他任何项时，请单击保管库仪表板上的“删除”。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

1. 若确认要删除保管库，请单击“是”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>如果我停止了备份过程，但仍保留数据，会怎么样？

如果停止备份过程，但意外保留数据，必须先删除备份数据，然后才能删除保管库。 删除备份数据：

1. 在“备份项”菜单中，右键单击该项，并从上下文菜单中选择“删除备份数据”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    此时会打开“删除备份数据”菜单。
1. 在“删除备份数据”菜单上，键入项名称，并单击“删除”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-retained-vault.png)

    删除数据后，请返回到步骤 4c 继续执行该过程。
