---
title: 删除 Azure 中的恢复服务保管库
description: 本文介绍了如何删除恢复服务保管库。 本文包括了尝试删除保管库但无法删除时可采用的疑难解答步骤。
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: markgal
ms.openlocfilehash: 844a70aa6fe003c6ad5816aaec9c32db9104c620
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605334"
---
# <a name="delete-a-recovery-services-vault"></a>删除恢复服务保管库
本文介绍了如何在 Azure 门户中删除恢复服务保管库。 如果你之前有备份保管库，则它们已转换为恢复服务保管库。   

删除恢复服务保管库只需一步 — *前提是保管库不包含任何资源*。 删除恢复服务保管库之前，必须先删除保管库中的所有资源。 如果尝试删除包含资源的保管库，会收到类似于下图的错误：

![保管库删除错误](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

清除保管库中的资源之前，单击“**重试**”会产生相同的错误。 如果仍收到此错误消息，请单击“取消”，并按照以下步骤来删除保管库中的资源。

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>从保护 VM 的保管库中删除项
如果已打开恢复服务保管库，请跳到第二步。

1. 打开 Azure 门户，并从仪表板打开要删除的保管库。

   如果没有将恢复服务保管库固定到仪表板，请在“中心”菜单上单击“**更多服务**”，并在资源列表中键入“**恢复服务**”。 开始键入时，会根据输入筛选该列表。 单击“**恢复服务保管库**”。

   ![创建恢复服务保管库步骤 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   此时会显示恢复服务保管库列表。 从列表中选择要删除的保管库。

   ![从列表中选择保管库](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. 在保管库视图查看 **Essentials** 窗格。 若要删除保管库，不能有任何受保护的项。 如果“备份项”或“备份管理服务器”未显示为零，则必须删除那些项。 如果保管库包含数据，则无法将其删除。

    ![查看受保护的项的 Essentials 窗格](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    VM 和文件/文件夹被视为备份项，并在 Essentials 窗格的“**备份项**”区域中列出。 DPM 服务器列在 Essentials 窗格的“**备份管理服务器**”区域中。 “**复制的项**”与 Azure Site Recovery 服务有关。
3. 若要开始从保管库中删除受保护的项，请在保管库中查找这些项。 在保管库仪表板中，单击“设置”，然后单击“备份项”以打开该菜单。

    ![从列表中选择保管库](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    “备份项”菜单根据项类型（Azure 虚拟机或文件-文件夹）提供不同的列表（请参阅图）。 显示的默认“项类型”列表是 Azure 虚拟机。 若要查看保管库中的文件-文件夹项列表，请从下拉菜单选择“**文件-文件夹**”。
4. 从保护 VM 的保管库中删除项之前，必须停止该项目的备份作业并删除恢复点数据。 对于保管库中的每个项，请执行以下步骤：

    a. 在“备份项”菜单上，右键单击该项，并从上下文菜单中选择“停止备份”。

    ![停止备份作业](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    此时会打开“停止备份”菜单。

    b. 在“停止备份”菜单上，从“选择选项”菜单中选择“删除备份数据”，键入项名称，然后单击“停止备份”。

    键入项名称以确认要将其删除。 验证该项后，“停止备份”按钮会激活。 如果看不到要在其中键入备份项名称的对话框，表示已选择“保留备份数据”选项。

    ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    （可选）可以提供删除数据的原因并添加注释。 单击“**停止备份**”后，允许完成删除作业，再尝试删除保管库。 若要验证作业是否已完成，请检查 Azure Messages ![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 <br/>
    作业完成后，该服务将发送一条消息：备份过程已停止，并且备份数据已被删除。

    c. 删除列表中的项目后，请在“**备份项**”菜单上单击“**刷新**”，以查看保管库中的其余项目。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

      如果列表中没有项，则滚动到“恢复服务保管库”菜单中的“概要”窗格。 此时不应列出任何“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 如果保管库中仍有项目，请返回到第三步，并选择不同的项类型列表。  
5. 当保管库工具栏中没有其他项时，请单击“**删除**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-vault.png)
6. 若确认要删除保管库，请单击“是”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>如果我停止了备份过程但保留了数据怎么办？
如果停止了备份过程但意外*保留*了数据，必须先删除备份数据，然后才能删除保管库。 删除备份数据：

1. 在“备份项”菜单中，右键单击该项，并从上下文菜单中选择“删除备份数据”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    此时会打开“删除备份数据”菜单。
2. 在“删除备份数据”菜单上，键入项名称，并单击“删除”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-retained-vault.png)

    删除数据后，请返回到步骤 4c 继续执行该过程。

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>删除用来保护 DPM 服务器的保管库
删除用来保护 DPM 服务器的保管库之前，必须清除已创建的任何恢复点，并从保管库中注销该服务器。

删除与保护组关联的数据：

1. 在 DPM 管理员控制台中，单击“保护”，依次选择保护组和保护组成员，并在工具功能区中单击“删除”。

  选择保护组成员以激活工具功能区中的“删除”按钮。 在本示例中，成员是 **dummyvm9**。 若要选择保护组中的多个成员，请按住 Ctrl 键并同时单击这些成员。

    ![删除备份数据](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    此时会打开“**停止保护**”对话框。
2. 在“**停止保护**”对话框中，选择“**删除受保护的数据**”，并单击“**停止保护**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    若要删除保管库，必须清除或删除受保护数据的保管库。 如果保护组中有许多恢复点和数据，则删除这些数据可能要花费几分钟时间。 当作业完成时会显示“停止保护”对话框。

    ![删除备份数据](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. 对所有保护组中的所有成员继续此过程。

    删除所有受保护的数据和保护组。
4. 从保护组中删除所有成员后，切换到 Azure 门户。 打开保管库仪表板，确保没有“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 在保管库工具栏上，单击“**删除**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-vault.png)

    如果已有注册到保管库的备份管理服务器，即使保管库中没有数据，也不能删除该保管库。 如果已经删除与保管库关联的备份管理服务器，但“概要”窗格中仍有列出的服务器，请参阅[查找注册到保管库的备份管理服务器](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault)。
5. 若确认要删除保管库，请单击“是”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>删除用来保护生产服务器的保管库
删除用来保护生产服务器的保管库之前，必须从保管库中删除或注销服务器。

删除与保管库关联的生产服务器：

1. 在 Azure 门户中，打开保管库仪表板，并单击“**设置** > **备份基础结构** > **生产服务器**”。

    ![打开“生产服务器”菜单](./media/backup-azure-delete-vault/delete-production-server.png)

    “生产服务器”菜单将打开，并且会列出保管库中的所有生产服务器。

    ![生产服务器的列表](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. 在“生产服务器”菜单上，右键单击服务器，并单击“删除”。

    ![删除生产服务器 ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    “删除”菜单随即打开。

    ![删除生产服务器 ](./media/backup-azure-delete-vault/delete-blade.png)
3. 在“删除”菜单上，确认服务器名称，并单击“删除”。 必须正确输入服务器的名称才能激活“**删除**”按钮。

    删除保管库后，将收到一条消息，指明保管库已被删除。 删除保管库中的所有服务器后，滚动回到保管库仪表板中的“Essentials”窗格。
4. 在保管库仪表板中，确保没有“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 在保管库工具栏上，单击“**删除**”。
5. 若确认要删除保管库，请单击“是”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>查找已注册到保管库的备份管理服务器
如果有多个注册到保管库的服务器，会可能很难记住它们。 查看并删除注册到保管库的服务器：

1. 打开保管库仪表板。
2. 在“概要”窗格中，单击“设置”以打开该菜单。

    ![打开“设置”菜单](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. 在“设置”菜单上，单击“备份基础结构”。
4. 在“备份基础结构”菜单上，单击“备份管理服务器”。 “备份管理服务器”菜单随即打开。

    ![备份管理服务器的列表](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. 要从列表中删除服务器，右键单击服务器名称，并单击“**删除**”。
    “删除”菜单随即打开。
6. 在“删除”菜单上，提供服务器的名称。 如果它是一个长名称，可以从备份管理服务器的列表中复制并粘贴它。 然后单击“删除”。  
