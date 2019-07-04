---
title: 删除 Azure 中的恢复服务保管库
description: 介绍如何删除恢复服务保管库。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: raynew
ms.openlocfilehash: e195d9a4b9d2bbe21848e083dbccf864188e0790
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508432"
---
# <a name="delete-a-recovery-services-vault"></a>删除恢复服务保管库

本文介绍如何删除 [Azure 备份](backup-overview.md)恢复服务保管库。 其中分别说明了如何删除依赖项，以及如何删除保管库和强制删除保管库。


## <a name="before-you-start"></a>开始之前

在开始之前必须知道，无法删除注册了服务器的，或者存有备份数据的恢复服务保管库。

- 若要正常删除保管库，取消注册它包含的服务器并删除保管库数据，然后删除该保管库。
- 如果你尝试删除保管库仍具有依赖项、 发出一条错误消息，并且将需要手动删除保管库依赖项，包括：
    - 备份的项
    - 受保护的服务器
    - 备份管理服务器（Azure 备份服务器、DPM）![选择保管库以打开其仪表板](./media/backup-azure-delete-vault/backup-items-backup-infrastructure.png)
- 如果你不想要保留恢复服务保管库中的任何数据，并想要删除此保管库，可以强制删除它。
- 如果尝试删除保管库但未成功，此保管库仍配置为接收备份数据。


## <a name="delete-a-vault-from-the-azure-portal"></a>在 Azure 门户中删除保管库

1. 打开保管库仪表板。  
2. 在仪表板中单击“删除”  。 确认是否要删除。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

如果收到错误，请依次删除[备份项](#remove-backup-items)、[基础结构服务器](#remove-azure-backup-management-servers)、[恢复点](#remove-azure-backup-agent-recovery-points)和保管库。

![删除保管库错误](./media/backup-azure-delete-vault/error.png)


## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 从[此处](https://chocolatey.org/)安装 chocolatey，若要安装 ARMClient，请运行以下命令：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登录到 Azure 帐户，并运行以下命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 门户中，收集所要删除的保管库的订阅 ID 和资源组名称。

有关 ARMClient 命令的详细信息，请参阅此[文档](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

1. 使用订阅 ID、资源组名称和保管库名称运行以下命令。 时运行该命令会删除保管库，如果你没有任何依赖项。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果该保管库不是空的，则会出现错误“由于此保管库中存在现有资源，因此无法将其删除”。 若要删除保管库中受保护的项/容器，请执行以下操作：

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 门户中，确认要删除该保管库。


## <a name="remove-vault-items-and-delete-the-vault"></a>删除保管库项并删除保管库

删除恢复服务保管库前删除所有依赖项。

### <a name="remove-backup-items"></a>删除备份项

此过程通过一个示例说明如何从 Azure 文件中删除备份数据。

1. 单击“备份项” > “Azure 存储(Azure 文件)”  

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected-list.png)

2. 右键单击要删除的每个 Azure 文件项，然后单击“停止备份”。 

    ![选择备份类型](./media/backup-azure-delete-vault/stop-backup-item.png)


3. 在“停止备份” > “选择选项”中，选择“删除备份数据”。   
4. 键入项的名称，然后单击“停止备份”。 
   - 这表示你确认要删除该项。
   - 确认后，“停止备份”按钮将会激活。 
   - 如果保留（而不是删除）该数据，便无法删除保管库。

     ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. （可选） 提供相应原因，你为什么要删除数据，并添加注释。
6. 若要确认删除作业是否已完成，请检查 Azure 消息。 ![删除备份数据](./media/backup-azure-delete-vault/messages.png).
7. 该作业完成后，服务会发送以下消息：“备份过程已停止，备份数据已删除”  。
8. 删除列表中的项后，请单击“备份项”  菜单上的“刷新”  ，以查看保管库中的项。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="deleting-backup-items-from-management-console"></a>从管理控制台中删除备份项

若要删除的备份基础结构中备份的项，导航到你的本地服务器管理控制台 （MARS、 Azure 备份服务器或 SC DPM，具体取决于受保护应用后的项）。

### <a name="for-mars-agent"></a>MARS 代理

- 启动 MARS 管理控制台，请转到**操作**窗格选择**计划备份**。
- 从**修改或停止计划备份**向导中，选择选项**停止使用此备份计划并删除所有存储的备份**然后单击**下一步**。

    ![修改或停止计划的备份](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- 从**停止计划的备份**向导中，单击**完成**。

    ![停止计划的备份](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 系统会提示你输入安全 Pin。 若要生成的 PIN，请执行以下步骤：
  - 登录到 Azure 门户。
  - 浏览到“恢复服务保管库”   > “设置”   >   “属性”。
  - 单击“安全 PIN”下的“生成”   。 复制此 PIN。（此 PIN 的有效期是仅五分钟）
- 在管理控制台 （客户端应用程序） 中粘贴 PIN，然后单击**确定**。

  ![安全 Pin](./media/backup-azure-delete-vault/security-pin.png)

- 在中**修改备份进度**你将看到的向导*已删除备份数据将保留 14 天。该时间后备份数据将被永久删除。*  

    ![删除备份基础结构](./media/backup-azure-delete-vault/deleted-backup-data.png)

现在，已从本地删除的备份项，完成以下步骤从门户：
- 对于 MARS 请按照中的步骤[删除 Azure 备份代理恢复点](#remove-azure-backup-agent-recovery-points)

### <a name="for-mabs-agent"></a>MABS 代理

有用于停止/删除在线保护，请执行的任何一个不同的方法以下方法：

**方法 1**

启动**MABS 管理**控制台。 在中**选择数据保护方法**部分中，取消选中**我需要在线保护**。

  ![选择数据保护方法](./media/backup-azure-delete-vault/data-protection-method.png)

**方法 2**

若要删除保护组，必须首先停止组的保护。 使用以下过程停止保护并可以删除保护组的删除。

1.  在 DPM 管理员控制台中，单击**保护**导航栏上。
2.  在显示窗格中，选择你想要删除的保护组成员。 右键单击以选择**停止保护组成员**选项。
3.  从**停止保护**对话框中，选择**删除受保护的数据** > **删除存储联机**复选框，然后单击**停止保护**。

    ![删除存储联机](./media/backup-azure-delete-vault/delete-storage-online.png)

受保护的成员状态现在变为**可用的非活动副本**。

5. 右键单击的非活动保护组，然后选择**删除非活动保护**。

    ![删除非活动保护](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. 从**删除非活动保护**窗口中，选择**删除联机存储**然后单击**确定**。

    ![删除磁盘上和联机的副本](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

现在，已从本地删除的备份项，完成以下步骤从门户：
- MABS 和 DPM 按照中的步骤[删除 Azure 备份管理服务器](#remove-azure-backup-management-servers)。


### <a name="remove-azure-backup-management-servers"></a>删除 Azure 备份管理服务器

然后再删除 Azure 备份管理服务器，请务必执行中列出的步骤[从管理控制台中删除备份项](#deleting-backup-items-from-management-console)。

1. 在保管库仪表板菜单中，单击“备份基础结构”  。
2. 单击“备份管理服务器”以查看服务器。 

    ![选择保管库以打开其仪表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 右键单击该项并选择“删除”。 
4. 上**删除**菜单中，键入服务器的名称，然后单击**删除**。

     ![删除备份数据](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)
5.  （可选） 提供相应原因，你为什么要删除数据，并添加注释。

> [!NOTE]
> 如果看到以下错误，则第一个执行中列出的步骤[从管理控制台中删除备份项](#deleting-backup-items-from-management-console)。
>
>![删除失败](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 如果您不能执行的步骤，若要从管理控制台删除备份，例如，由于使用管理控制台中，服务器不可用时联系 Microsoft 支持部门。

6. 若要确认删除作业是否已完成，请检查 Azure 消息。 ![删除备份数据](./media/backup-azure-delete-vault/messages.png).
7. 该作业完成后，服务会发送以下消息：“备份过程已停止，备份数据已删除”  。
8. 删除列表中的项后，请单击“备份基础结构”  菜单上的“刷新”  ，以查看保管库中的项。


### <a name="remove-azure-backup-agent-recovery-points"></a>删除 Azure 备份代理恢复点

然后再删除 Azure 备份和恢复点，请务必执行中列出的步骤[从管理控制台中删除备份项](#deleting-backup-items-from-management-console)。

1. 在保管库仪表板菜单中，单击“备份基础结构”  。
2. 单击**受保护服务器**若要查看基础结构服务器。

    ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

3. 在“受保护的服务器”  列表中，单击“Azure 备份代理”。

    ![选择备份类型](./media/backup-azure-delete-vault/list-of-protected-server-types.png)

4. 在使用 Azure 备份代理保护的服务器列表中单击该服务器。

    ![选择受保护的具体服务器](./media/backup-azure-delete-vault/azure-backup-agent-protected-servers.png)

5. 在选定服务器的仪表板上，单击“删除”  。

    ![删除选定服务器](./media/backup-azure-delete-vault/selected-protected-server-click-delete.png)

6. 上**删除**菜单中，键入服务器的名称，然后单击**删除**。

     ![删除备份数据](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

7. （可选） 提供相应原因，你为什么要删除数据，并添加注释。

> [!NOTE]
> 如果看到以下错误，则第一个执行中列出的步骤[从管理控制台中删除备份项](#deleting-backup-items-from-management-console)。
>
>![删除失败](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 如果您不能执行的步骤，若要从管理控制台删除备份，例如，由于使用管理控制台中，服务器不可用时联系 Microsoft 支持部门。 

8. 若要确认删除作业是否已完成，请检查 Azure 消息。 ![删除备份数据](./media/backup-azure-delete-vault/messages.png).
9. 删除列表中的项后，请单击“备份基础结构”  菜单上的“刷新”  ，以查看保管库中的项。


### <a name="delete-the-vault-after-removing-dependencies"></a>删除依赖项后删除保管库

1. 删除所有依赖项后，在保管库菜单中滚动到“概要”窗格。 
2. 确认是否未列出任何“备份项”、“备份管理服务器”或“复制的项”。    如果保管库中仍有项，请删除这些项。

3. 当保管库中没有其他任何项时，请单击保管库仪表板上的“删除”  。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若确认要删除保管库，请单击“是”  。 随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="what-if-i-stop-the-backup-process-but-retain-the-data"></a>如果我停止了备份过程，但仍保留数据，会怎么样？

如果停止备份过程，但意外保留了数据，则必须先按前面的部分中所述删除备份数据。

## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
