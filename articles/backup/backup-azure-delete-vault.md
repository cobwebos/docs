---
title: 删除 Azure 中的恢复服务保管库
description: 介绍如何删除恢复服务保管库。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 34484c309cb186aabec519e54269fefae316165e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639906"
---
# <a name="delete-a-recovery-services-vault"></a>删除恢复服务保管库

本文介绍如何删除 [Azure 备份](backup-overview.md)恢复服务保管库。 其中分别说明了如何删除依赖项，以及如何删除保管库。


## <a name="before-you-start"></a>开始之前

无法删除具有依赖项（例如，与保管库关联的受保护服务器或备份管理服务器）的恢复服务保管库。

- 无法删除包含备份数据的保管库（即，停止了保护，但保留了备份数据）。

- 如果删除包含依赖项的保管库，将显示以下错误：

  ![删除保管库错误](./media/backup-azure-delete-vault/error.png)

- 如果从包含依赖项的门户中删除本地受保护项 (MARS、MABS 或 DPM 到 Azure), 将显示一条警告消息:

  ![删除受保护服务器错误](./media/backup-azure-delete-vault/error-message.jpg)

  
若要正常删除保管库, 请选择与你的设置相匹配的方案, 然后执行建议的步骤:

应用场景 | 删除依赖项以删除保管库的步骤 |
-- | --
我有使用 Azure 备份代理 (MARS) 备份到 Azure 的本地文件和文件夹 | 执行删除备份数据和备份项-[适用于 MARS 代理](#delete-backup-items-from-mars-management-console)的步骤
我有使用 MABS (Microsoft Azure 备份 Server) 或 DPM 到 Azure (System Center Data Protection Manager) 保护的本地计算机 | 执行删除备份数据和备份项-[对于 MABS 代理](#delete-backup-items-from-mabs-management-console)的步骤
我在云中有受保护的项 (例如 laaS VM、Azure 文件共享等)  | 执行删除[云中受保护项](#delete-protected-items-in-cloud)的备份数据和备份项中的步骤
我在本地和云上都有受保护的项 | 执行删除以下序列中的 "删除备份数据和备份项" 中的步骤: <br> - [对于云中受保护的项](#delete-protected-items-in-cloud)<br> - [对于 MARS 代理](#delete-backup-items-from-mars-management-console) <br> - [对于 MABS 代理](#delete-backup-items-from-mabs-management-console)
我在本地或云上没有任何受保护的项;但仍会遇到保管库删除错误 | 执行[使用 Azure 资源管理器客户端删除恢复服务保管库](#delete-the-recovery-services-vault-using-azure-resource-manager-client)中的步骤


## <a name="delete-protected-items-in-cloud"></a>删除云中受保护的项

在继续操作之前，请阅读 **[此部分](#before-you-start)** ，以了解依赖项和保管库删除过程。

若要停止保护并删除备份数据，请执行以下操作：

1. 从门户 >**恢复服务保管库** > **备份项**选择云中受保护的项 (例如 AzureVirtual 机、azure 存储 (azure 文件)、SQL 没有 Azure VM 等)。

    ![选择备份类型](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 右键单击备份项, 具体取决于备份项是否受保护, 或者菜单将显示 "**停止备份**" 还是 "**删除备份数据**"。

    - 对于 "**停止备份**", 请从下拉选择 "**删除备份数据**"。 输入备份项的**名称**("区分大小写"), 选择 "**原因**", 输入 "**注释**", 然后单击 "**停止备份**"。

        ![选择备份类型](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 对于 "**删除备份数据**", 请输入备份项的名称 ("区分大小写"), 选择 "**原因**", 输入**注释**, 然后单击 "**删除**"。 

         ![删除备份数据](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 检查“通知”![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 完成后，服务将显示以下消息：“正在停止 <备份项> 的备份并删除备份数据。  已成功完成该操作”。
6. 单击“备份项”菜单中的“刷新”，以检查是否删除了备份项。

      ![删除备份数据](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>删除本地受保护的项

在继续操作之前，请阅读 **[此部分](#before-you-start)** ，以了解依赖项和保管库删除过程。

1. 在保管库仪表板菜单中, 单击 "**备份基础结构**"。
2. 根据本地方案, 请选择以下选项:

      - 对于**azure 备份代理**, 请选择 "**受保护的服务器** > " "**Azure 备份代理**", 然后选择要删除的服务器。 

        ![选择保管库，以打开它的仪表板](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 对于**Azure 备份服务器**/**DPM**, 请选择 " **备份管理服务器**"。 选择要删除的服务器。 


          ![选择保管库，打开其仪表板](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 此时将显示 "**删除**" 边栏选项卡和警告消息。

     ![删除备份数据](./media/backup-azure-delete-vault/delete-protected-server.png)

     查看警告消息和 "同意" 复选框中提供的说明。
    
    > [!NOTE]
    >- 如果受保护的服务器与 Azure 服务同步并且存在备份项, 则同意复选框将显示相关备份项的数量以及用于查看备份项的链接。
    >- 如果受保护的服务器与 Azure 服务不同步并且存在备份项, 则同意复选框将显示备份项的数量。
    >- 如果备份项不存在, 则同意复选框会要求删除。

4. 选中 "同意" 复选框, 并单击 "**删除**"。




5. 检查“通知”![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 完成后，服务将显示以下消息：“正在停止 <备份项> 的备份并删除备份数据。  已成功完成该操作”。
6. 单击“备份项”菜单中的“刷新”，以检查是否删除了备份项。

你现在可以继续从管理控制台中删除备份项目:
    
   - [使用 MARS 保护的项目](#delete-backup-items-from-mars-management-console)
    - [使用 MABS 保护的项](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>从 MARS 管理控制台删除备份项

从 MARS 管理控制台删除备份项

- 启动 MARS 管理控制台，转到“操作”窗格并选择“计划备份”。
- 从“修改或停止计划的备份”向导中选择“停止使用此备份计划并删除所有存储的备份”选项，然后单击“下一步”。

    ![修改或停止计划的备份](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- 在“停止计划的备份”向导中单击“完成”。

    ![停止计划的备份](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- 系统会提示输入安全 PIN。 若要生成该 PIN，请执行以下步骤：
  - 登录到 Azure 门户。
  - 浏览到“恢复服务保管库” > “设置” > “属性”。
  - 单击“安全 PIN”下的“生成”。 复制此 PIN。（此 PIN 的有效时间仅为五分钟）
- 在管理控制台（客户端应用）中粘贴此 PIN 并单击“确定”。

  ![安全 PIN](./media/backup-azure-delete-vault/security-pin.png)

- 在“修改备份进度”向导中，会看到“删除的备份数据会保留 14 天。该时间过后，备份数据会被永久删除。”  

    ![删除备份基础结构](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地的备份项以后，请在门户中完成后续步骤。

### <a name="delete-backup-items-from-mabs-management-console"></a>从 MABS 管理控制台中删除备份项

从 MABS 管理控制台中删除备份项

**方法 1**：若要停止保护并删除备份数据，请执行以下步骤：

1.  在 DPM 管理员控制台中，单击导航栏上的“保护”。
2.  在显示窗格中，选择要删除的保护组成员。 通过右键单击选择“停止对组成员的保护”选项。
3.  从 "**停止保护**" 对话框中, 选择 "**删除受保护的数据** > " "**联机删除存储**" 复选框, 然后单击 "**停止保护**"。

    ![删除在线存储](./media/backup-azure-delete-vault/delete-storage-online.png)

受保护成员状态现在更改为“停用副本可用”。

4. 右键单击停用保护组，然后选择“删除停用保护”。

    ![删除停用保护](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 在“删除停用保护”窗口中，选择“删除在线存储”，然后单击“确定”。

    ![删除磁盘上的和联机的副本](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**方法 2**：启动 **MABS 管理**控制台。 在“选择数据保护方法”部分，取消选择“我需要在线保护”。

  ![选择数据保护方法](./media/backup-azure-delete-vault/data-protection-method.png)

删除本地的备份项以后，请在门户中完成后续步骤。


## <a name="delete-the-recovery-services-vault"></a>删除恢复服务保管库

1. 删除所有依赖项后，在保管库菜单中滚动到“概要”窗格。
2. 确认是否未列出任何“备份项”、“备份管理服务器”或“复制的项”。 如果保管库中仍显示项目, 请参阅 "[开始之前](#before-you-start)" 部分。

3. 当保管库中没有其他任何项时，请单击保管库仪表板上的“删除”。

    ![删除备份数据](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 若确认要删除保管库，请单击“是”。 随即会删除该保管库，门户返回“**新建**”服务菜单。

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

仅当已删除所有依赖项后，仍然收到“保管库删除错误”时，才建议使用删除恢复服务保管库的选项。

- 在“概要”窗格中的保管库菜单内，确认是否未列出任何“备份项”、“备份管理服务器”或“复制的项”。 如果有备份项, 请参阅 "[开始之前](#before-you-start)" 部分。
- 重试[从门户删除保管库](#delete-the-recovery-services-vault)。
- 如果删除了所有依赖项，但仍收到“保管库删除错误”，请使用 ARMClient 工具执行以下步骤：

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 从[此处](https://chocolatey.org/)安装 chocolatey，若要安装 ARMClient，请运行以下命令：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登录到 Azure 帐户并运行以下命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 门户中，收集所要删除的保管库的订阅 ID 和资源组名称。

有关 ARMClient 命令的详细信息，请参阅此[文档](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

1. 使用订阅 ID、资源组名称和保管库名称运行以下命令。 当你运行该命令时, 如果你没有任何依赖关系, 它将删除该保管库。

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果该保管库不是空的，则会出现错误“由于此保管库中存在现有资源，因此无法将其删除”。 若要删除保管库中受保护的项/容器，请执行以下操作：

   ```
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 门户中，确认要删除该保管库。


## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。<br/>
[了解](backup-azure-manage-windows-server.md)如何监视和管理恢复服务保管库。
