---
title: 删除 Azure 中的恢复服务保管库
description: 介绍如何删除恢复服务保管库。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: raynew
ms.openlocfilehash: 01c20ce84f5c97b3a0ac437fe602861085b5052c
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827884"
---
# <a name="delete-a-recovery-services-vault"></a>删除恢复服务保管库

本文介绍如何删除 [Azure 备份](backup-overview.md)恢复服务保管库。 它包含有关删除依赖项，然后在删除保管库的说明。


## <a name="before-you-start"></a>开始之前

无法删除具有依赖项，如受保护的服务器或与保管库关联的备份管理服务器的恢复服务保管库。<br/>
无法删除保管库包含备份数据，（即，即使已停止保护，但保留备份数据）。

如果您删除包含依赖项的保管库，它将显示以下错误：

![删除保管库错误](./media/backup-azure-delete-vault/error.png)

若要正常删除保管库，请执行以下顺序中所述的步骤：
- 停止保护并删除备份数据
- 删除受保护的服务器或备份管理服务器
- 删除保管库


## <a name="delete-backup-data-and-backup-items"></a>删除备份数据和备份项

在继续操作的更多的读取之前 **[这](#before-you-start)** 部分以了解依赖关系和保管库删除过程。

### <a name="for-protected-items-in-cloud"></a>云中受保护项

若要停止保护并删除备份数据，请执行如下：

1. 从门户 > 恢复服务保管库 > 备份项在云中选择受保护的项。

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected.jpg)

2. 对于每个项，您需要右键单击并选择**停止备份**。

    ![选择备份类型](./media/backup-azure-delete-vault/stop-backup-item.png)

3. 在“停止备份” > “选择选项”中，选择“删除备份数据”。   
4. 键入项的名称，然后单击“停止备份”。 
   - 这表示你确认要删除该项。
   - 确认后，“停止备份”按钮将会激活。 
   - 如果保留（而不是删除）该数据，便无法删除保管库。

     ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 检查**通知**![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 完成操作后，该服务将显示消息：**正在停止备份并删除备份数据"*备份项*"** 。 **已成功完成操作**。
6. 单击**刷新**上**备份项**菜单中，若要检查是否已删除的备份项。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

### <a name="for-mars-agent"></a>MARS 代理

若要停止保护并删除备份数据，下面列出的顺序执行步骤：

- [步骤 1：从 MARS 管理控制台中删除备份项](#step-1-delete-backup-items-from-mars-management-console)
- [步骤 2：从门户删除 Azure 备份代理](#step-1-delete-backup-items-from-mars-management-console)


#### <a name="step-1-delete-backup-items-from-mars-management-console"></a>步骤 1：从 MARS 管理控制台中删除备份项

如果无法执行此步骤中的服务器不可用由于然后联系 Microsoft 支持部门。

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

现在，已从本地删除的备份项，再完成后续步骤在门户中。

#### <a name="step-2-from-portal-remove-azure-backup-agent"></a>步骤 2：从门户删除 Azure 备份代理

请确保[第 1 步](#step-1-delete-backup-items-from-mars-management-console)在继续操作之前完成：

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

> [!NOTE]
> 如果看到以下错误，则第一个执行中列出的步骤[从管理控制台中删除备份项](#step-1-delete-backup-items-from-mars-management-console)。
>
>![删除失败](./media/backup-azure-delete-vault/deletion-failed.png)
>
>如果您不能执行的步骤，若要从管理控制台删除备份，例如，由于使用管理控制台中，服务器不可用时联系 Microsoft 支持部门。

7. 检查**通知**![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 完成操作后，该服务将显示消息：**正在停止备份并删除备份数据"*备份项*"** 。 **已成功完成操作**。
8. 单击**刷新**上**备份项**菜单中，若要检查是否已删除的备份项。


### <a name="for-mabs-agent"></a>MABS 代理

若要停止保护并删除备份数据，下面列出的顺序执行步骤：

- [步骤 1：从 MABS 管理控制台中删除备份项](#step-1-delete-backup-items-from-mabs-management-console)
- [步骤 2：从门户删除 Azure 备份管理服务器](#step-2-from-portal-remove-azure-backup-agent)

#### <a name="step-1-delete-backup-items-from-mabs-management-console"></a>步骤 1：从 MABS 管理控制台中删除备份项

如果无法执行此步骤中的服务器不可用由于然后联系 Microsoft 支持部门。

**方法 1**要停止保护并删除备份数据，请执行以下步骤：

1.  在 DPM 管理员控制台中，单击**保护**导航栏上。
2.  在显示窗格中，选择你想要删除的保护组成员。 右键单击以选择**停止保护组成员**选项。
3.  从**停止保护**对话框中，选择**删除受保护的数据** > **删除存储联机**复选框，然后单击**停止保护**。

    ![删除存储联机](./media/backup-azure-delete-vault/delete-storage-online.png)

受保护的成员状态现在变为**可用的非活动副本**。

5. 右键单击的非活动保护组，然后选择**删除非活动保护**。

    ![删除非活动保护](./media/backup-azure-delete-vault/remove-inactive-protection.png)

6. 从**删除非活动保护**窗口中，选择**删除联机存储**然后单击**确定**。

    ![删除磁盘上和联机的副本](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**方法 2**启动**MABS 管理**控制台。 在中**选择数据保护方法**部分中，取消选中**我需要在线保护**。

  ![选择数据保护方法](./media/backup-azure-delete-vault/data-protection-method.png)

现在，已从本地删除的备份项，再完成后续步骤在门户中。

#### <a name="step-2-from-portal-remove-azure-backup-management-servers"></a>步骤 2：从门户删除 Azure 备份管理服务器

请确保[第 1 步](#step-1-delete-backup-items-from-mabs-management-console)在继续操作之前完成：

1. 在保管库仪表板菜单中，单击“备份基础结构”  。
2. 单击“备份管理服务器”以查看服务器。 

    ![选择保管库以打开其仪表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 右键单击该项并选择“删除”。 
4. 上**删除**菜单中，键入服务器的名称，然后单击**删除**。

     ![删除备份数据](./media/backup-azure-delete-vault/delete-protected-server-dialog.png)

> [!NOTE]
> 如果看到以下错误，则第一个执行中列出的步骤[从管理控制台中删除备份项](#step-2-from-portal-remove-azure-backup-management-servers)。
>
>![删除失败](./media/backup-azure-delete-vault/deletion-failed.png)
>
> 如果您不能执行的步骤，若要从管理控制台删除备份，例如，由于使用管理控制台中，服务器不可用时联系 Microsoft 支持部门。

5. 检查**通知**![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 完成操作后，该服务将显示消息：**正在停止备份并删除备份数据"*备份项*"** 。 **已成功完成操作**。
6. 单击**刷新**上**备份项**菜单中，若要检查是否已删除的备份项。


## <a name="delete-the-recovery-services-vault"></a>删除恢复服务保管库

1. 删除所有依赖项后，在保管库菜单中滚动到“概要”窗格。 
2. 确认是否未列出任何“备份项”、“备份管理服务器”或“复制的项”。    如果在保管库中仍有项目[将其删除](#delete-backup-data-and-backup-items)。

3. 当保管库中没有其他任何项时，请单击保管库仪表板上的“删除”  。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若确认要删除保管库，请单击“是”  。 随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

若要删除恢复服务保管库仅建议使用此选项时将删除所有依赖项并仍然收到*保管库删除错误*。



- 从**Essentials**保管库菜单中的窗格中，确认没有任何**备份项**，**备份管理服务器**，或**复制的项**列出。 如果没有备份的项，然后执行中的步骤[删除备份数据和备份项](#delete-backup-data-and-backup-items)。
- 重试[从门户中删除保管库](#delete-the-recovery-services-vault)。
- 如果删除所有依赖项和仍然收到*保管库删除错误*然后使用 ARMClient 工具来执行; 下面给出的步骤

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 从[此处](https://chocolatey.org/)安装 chocolatey，若要安装 ARMClient，请运行以下命令：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登录到 Azure 帐户，并运行以下命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 门户中，收集所要删除的保管库的订阅 ID 和资源组名称。

有关 ARMClient 命令的详细信息，请参阅此[文档](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

1. 使用订阅 ID、资源组名称和保管库名称运行以下命令。 运行命令时，它会删除保管库，如果你没有任何依赖项。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果保管库不为空，将收到错误"因为没有在此保管库中的现有资源，无法删除保管库"。 若要删除保管库中受保护的项/容器，请执行以下操作：

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 门户中，确认要删除该保管库。


## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。<br/>
[了解有关](backup-azure-manage-windows-server.md)监视和管理恢复服务保管库。
