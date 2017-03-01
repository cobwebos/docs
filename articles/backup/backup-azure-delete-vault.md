---
title: "删除 Azure 备份保管库 | Microsoft Docs"
description: 'How to delete an Azure Backup vault. Troubleshooting why you can''t delete a backup vault. '
services: service-name
documentationcenter: dev-center-name
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 5fa08157-2612-4020-bd90-f9e3c3bc1806
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2016
ms.author: markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 704c21aef6ed8b476bbd14f05bce89dc5b708c21


---
# <a name="delete-an-azure-backup-vault"></a>删除 Azure 备份保管库
Azure 备份服务提供两种类型的保管库：备份保管库和恢复服务保管库。 备份保管库优先级最高。 随后是恢复服务保管库，用于支持扩展的 Resource Manager 部署。 由于扩展的功能和信息依赖项必须存储在保管库中，删除恢复服务保管库比想像得要难。

| **部署类型** | **门户** | **保管库名称** |
| --- | --- | --- |
| 经典 |经典 |备份保管库 |
| 资源管理器 |Azure |恢复服务保管库 |

> [!NOTE]
> 备份保管库无法保护 Resource Manager 部署的解决方案。 但是，可以使用恢复服务保管库保护使用经典部署的服务器和 VM。  
>
>

本文中使用“保管库”一词来泛指备份保管库或恢复服务保管库。 需要区分保管库时，则使用正式名称“备份保管库”或“恢复服务保管库”。

## <a name="deleting-a-recovery-services-vault"></a>删除恢复服务保管库
删除恢复服务保管库只需一步 — *前提是保管库不包含任何资源*。 删除恢复服务保管库之前，必须先删除保管库中的所有资源。 如果尝试删除包含资源的保管库，你会收到类似于下图的错误。

![保管库删除错误](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

清除保管库中的资源之前，单击“**重试**”会产生相同的错误。 如果你仍收到此错误消息，请单击“**取消**”，然后按照以下步骤来删除恢复服务保管库中的资源。

### <a name="removing-the-items-from-a-vault-protecting-a-vm"></a>从保护 VM 的保管库中删除项目
如果已打开恢复服务保管库，请跳到第二步。

1. 打开 Azure 门户，并从仪表板打开你想要删除的保管库。

   如果没有将恢复服务保管库固定到仪表板，请在“中心”菜单上单击“**更多服务**”，然后在资源列表中键入“**恢复服务**”。 当你开始键入时，会根据你的输入筛选该列表。 单击“**恢复服务保管库**”。

   ![创建恢复服务保管库步骤 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   此时将显示恢复服务保管库列表。 从列表中选择要删除的保管库。

   ![从列表中选择保管库](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. 在保管库视图查看 **Essentials** 窗格。 若要删除保管库，不能有任何受保护的项。 如果在“**备份项**”或“**备份管理服务器**”下看到非零的数字，必须先删除这些项目，然后才能删除保管库。

    ![查看受保护的项的 Essentials 窗格](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    VM 和文件/文件夹被视为备份项，并在 Essentials 窗格的“**备份项**”区域中列出。 DPM 服务器列在 Essentials 窗格的“**备份管理服务器**”区域中。 “**复制的项**”与 Azure Site Recovery 服务有关。
3. 若要开始从保管库中删除受保护的项，请在保管库中查找这些项。 在保管库仪表板中，单击“**设置**”，然后单击“**备份项**”以打开该边栏选项卡。

    ![从列表中选择保管库](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    “**备份项**”边栏选项卡根据项目类型（Azure 虚拟机或文件-文件夹（请参阅图））提供单独的列表。 显示的默认“项类型”列表是 Azure 虚拟机。 若要查看保管库中的文件-文件夹项列表，请从下拉菜单选择“**文件-文件夹**”。
4. 从保护 VM 的保管库中删除项之前，必须停止该项目的备份作业并删除恢复点数据。 对于保管库中的每个项，请执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“**备份项**”边栏选项卡中，右键单击该项，然后从上下文菜单中选择“**停止备份**”。

    ![停止备份作业](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    此时会打开“停止备份”边栏选项卡。

    b. 在“**停止备份**”边栏选项卡中，从“**选择选项**”菜单中选择“**删除备份数据**”> 键入项名称 >，然后单击“**停止备份**”。

      键入项名称以确认你要将其删除。 验证要停止的项之前，“**停止备份**”按钮将不会激活。 如果看不到该对话框，无法键入备份项的名称，表示你已选择“**保留备份数据**”选项。

    ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

      （可选）可以提供删除数据的原因并添加注释。 单击“**停止备份**”后，允许完成删除作业，然后再尝试删除保管库。 若要验证作业是否已完成，请检查 Azure Messages ![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 <br/>
      作业完成后，你将收到一条消息，表明备份过程已停止并且该项的备份数据已被删除。

    c. 删除列表中的项目后，请在“**备份项**”菜单上单击“**刷新**”，以查看保管库中的其余项目。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

      如果列表中没有项目，请滚动到“备份保管库”边栏选项卡中的“**Essentials**”窗格。 此时不应列出任何“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 如果保管库中仍有项目，请返回到上述第三步，然后选择不同的项类型列表。  
5. 当保管库工具栏中没有其他项时，请单击“**删除**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-vault.png)
6. 当系统询问是否确定要删除保管库时，请单击“**是**”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>如果我停止了备份过程但保留了数据怎么办？
如果你停止了备份过程但意外*保留*了数据，必须先删除备份数据，然后才能删除保管库。 删除备份数据：

1. 在“**备份项**”边栏选项卡中，右键单击该项，然后从上下文菜单中选择“**删除备份数据**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    此时会打开“**删除备份数据**”边栏选项卡。
2. 在“**删除备份数据**”边栏选项卡中，键入项的名称，然后单击“**删除**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-retained-vault.png)

    删除数据后，请转到上面的步骤 4c 继续执行该过程。

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>删除用来保护 DPM 服务器的保管库
删除用来保护 DPM 服务器的保管库之前，必须清除已创建的任何恢复点，然后从保管库中注销该服务器。

删除与保护组关联的数据：

1. 在 DPM 管理员控制台中，单击“**保护**”，依次选择保护组和保护组成员，然后在工具功能区中单击“**删除**”。 必须先选择成员，“**删除**”按钮才会出现在工具功能区。 在本示例中，成员是 **dummyvm9**。 如果保护组中有多个成员，请按住 Ctrl 键以选择多个成员。

    ![删除备份数据](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    此时会打开“**停止保护**”对话框。
2. 在“**停止保护**”对话框中，选择“**删除受保护的数据**”，然后单击“**停止保护**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    由于需要清除保管库以将其删除，因此不需要保留受保护的数据。 根据恢复点个数和保护组中的数据量，删除数据所需的时间可能从几秒钟到几分钟不等。 “**停止保护**”对话框在作业完成时显示状态。

    ![删除备份数据](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. 对所有保护组中的所有成员继续此过程。

    你必须删除所有受保护的数据和保护组。
4. 从保护组中删除所有成员后，切换到 Azure 门户。 打开保管库仪表板，确保没有“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 在保管库工具栏上，单击“**删除**”。

    ![删除备份数据](./media/backup-azure-delete-vault/delete-vault.png)

    如果已有注册到保管库的备份管理服务器，即使保管库中没有数据，你也不能删除该保管库。 如果你认为已经删除与保管库关联的备份管理服务器，但“**Essentials**”窗格中仍显示有服务器，请参阅[查找注册到保管库的备份管理服务器](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault)。
5. 当系统询问是否确定要删除保管库时，请单击“**是**”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>删除用来保护生产服务器的保管库
删除用来保护生产服务器的保管库之前，必须从保管库中删除或注销服务器。

删除与保管库关联的生产服务器：

1. 在 Azure 门户中，打开保管库仪表板，然后单击“**设置** > **备份基础结构** > **生产服务器**”。

    ![打开生产服务器边栏选项卡](./media/backup-azure-delete-vault/delete-production-server.png)

    “**生产服务器**”边栏选项卡打开并列出保管库中的所有生产服务器。

    ![生产服务器的列表](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. 在“**生产服务器**”边栏选项卡中，右键单击服务器，然后单击“**删除**”。

    ![删除生产服务器 ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    此时会打开“**删除**”边栏选项卡。

    ![删除生产服务器 ](./media/backup-azure-delete-vault/delete-blade.png)
3. 在“**删除**”边栏选项卡中，确认要删除的服务器名称，然后单击“**删除**”。 必须正确输入服务器的名称才能激活“**删除**”按钮。

    删除保管库后，你将收到一条消息，指明保管库已被删除。 删除保管库中的所有服务器后，滚动回到保管库仪表板中的“Essentials”窗格。
4. 在保管库仪表板中，确保没有“**备份项**”、“**备份管理服务器**”或“**复制的项**”。 在保管库工具栏上，单击“**删除**”。
5. 当系统询问是否确定要删除保管库时，请单击“**是**”。

    随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="delete-a-backup-vault"></a>删除备份保管库
以下说明适用于在经典门户中删除备份保管库。 删除备份保管库和恢复服务保管库的过程相同：在删除保管库之前，需先删除项和保留的数据。

1. 打开经典门户。
2. 从备份保管库列表中，选择要删除的保管库。

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-delete-vault-open-vault.png)

    此时会打开保管库仪表板。 查看与该保管库关联的 Windows Server 和/或 Azure 虚拟机数目。 另外，请查看 Azure 中消耗的存储总量。 在删除保管库之前，需要停止所有备份作业，然后删除现有数据。
3. 单击“**受保护的项**”选项卡上，然后单击“**停止保护**”

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-delete-vault-stop-protect.png)

    此时将显示“**停止保护 ‘保管库’**”对话框。
4. 在“**停止保护‘保管库’**”对话框中，选中“**删除关联的备份数据**”，然后单击![选中标记](./media/backup-azure-delete-vault/checkmark.png)。 <br/>
   （可选）可以选择停止保护的原因并提供备注。

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-delete-vault-verify-stop-protect.png)

    删除保管库中的项后，该保管库是空的。

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-delete-vault-post-delete-data.png)
5. 在选项卡列表中，单击“**已注册的项**”。 选择保管库中注册的每个项，然后单击“**注销**”。

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-unregister.png)
6. 在选项卡列表中，单击“**仪表板**”打开该选项卡。 检查是否不再有已注册的服务器，或者在云中受保护的 Azure 虚拟机。 另外，请检查存储中是否没有任何数据。 单击“**删除**”，删除该保管库。

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-list-of-tabs-dashboard.png)

    此时将打开“确认删除备份保管库”屏幕。 选择一个选项解释为何要删除该保管库，然后单击 ![选中标记](./media/backup-azure-delete-vault/checkmark.png)。 <br/>

    ![删除备份数据](./media/backup-azure-delete-vault/classic-portal-delete-vault-confirmation-1.png)

    随即会删除该保管库，并返回到经典门户仪表板。

### <a name="find-the-backup-management-servers-registered-to-the-vault"></a>查找已注册到保管库的备份管理服务器
如果有多个注册到保管库的服务器，会可能很难记住它们。 查看并删除注册到保管库的服务器：

1. 打开保管库仪表板。
2. 在“**Essentials**”窗格中，单击“**设置**”以打开该边栏选项卡。

    ![打开设置边栏选项卡](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. 在“**设置边栏选项卡**”中，单击“**备份基础结构**”。
4. 在“**备份基础结构**”边栏选项卡中，单击“**备份管理服务器**”。 此时会打开“备份管理服务器”边栏选项卡。

    ![备份管理服务器的列表](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. 要从列表中删除服务器，右键单击服务器名称，然后单击“**删除**”。
    此时会打开“**删除**”边栏选项卡。
6. 在“**删除**”边栏选项卡中，请提供服务器的名称。 如果它是一个长名称，可以从备份管理服务器的列表中复制并粘贴它。 然后单击“**删除**”。  



<!--HONumber=Nov16_HO3-->


