---
title: 删除 Microsoft Azure 备份恢复服务保管库
description: 本文介绍如何删除 Microsoft Azure 备份恢复服务保管库。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: dacurwin
ms.openlocfilehash: ae8421ca9e3705d697e9638e80fc61f853ff9d28
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72028275"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>删除 Azure 备份恢复服务保管库

本文介绍如何删除 Microsoft [Azure 备份](backup-overview.md)恢复服务（MARS）保管库。 它包含删除依赖项，然后删除保管库的说明。


## <a name="before-you-start"></a>开始之前

不能删除具有依赖关系的恢复服务保管库，如受保护的服务器或备份管理服务器。

- 不能删除包含备份数据的保管库（即使已停止保护但保留了备份数据）。

- 如果删除包含依赖项的保管库，将显示以下消息：

  ![删除保管库错误。](./media/backup-azure-delete-vault/error.png)

- 如果从包含依赖项的门户中删除本地保护项，则会出现一条警告消息：

  ![删除受保护的服务器错误。](./media/backup-azure-delete-vault/error-message.jpg)

- 如果备份项处于软删除状态，则将显示警告消息，并且必须等待，直到它们被永久删除。 有关详细信息，请参阅[此文章](https://aka.ms/SoftDeleteCloudWorkloads)。

   ![删除保管库错误。](./media/backup-azure-delete-vault/error-message-soft-delete.png)
  
若要删除保管库，请选择与你的设置相匹配的方案，并遵循建议的步骤：

场景 | 删除依赖项以删除保管库的步骤 |
-- | --
我有使用 Azure 备份代理保护的本地文件和文件夹，备份到 Azure | 执行[从 MARS 管理控制台删除备份项](#delete-backup-items-from-the-mars-management-console)中的步骤
我有使用 MABS （Microsoft Azure 备份 Server）或 DPM （System Center Data Protection Manager）保护的本地计算机到 Azure | 执行[从 MABS 管理控制台中删除备份项](#delete-backup-items-from-the-mabs-management-console)中的步骤
我在云中有受保护的项（例如，laaS 虚拟机或 Azure 文件共享）  | 执行[删除云中受保护的项](#delete-protected-items-in-the-cloud)中的步骤
我在本地和云中都有受保护的项 | 按照以下顺序执行以下所有部分中的步骤： <br> 1.[删除云中受保护的项](#delete-protected-items-in-the-cloud)<br> 2.[从 MARS 管理控制台删除备份项](#delete-backup-items-from-the-mars-management-console) <br> 3.[从 MABS 管理控制台中删除备份项](#delete-backup-items-from-the-mabs-management-console)
我在本地或云上没有任何受保护的项;但仍会遇到保管库删除错误 | 执行[使用 Azure 删除恢复服务保管库](#delete-the-recovery-services-vault-by-using-azure-resource-manager)中的步骤资源管理器


## <a name="delete-protected-items-in-the-cloud"></a>删除云中受保护的项

首先，请阅读 " **[开始之前](#before-you-start)** " 部分，了解依赖关系和保管库删除过程。

若要停止保护并删除备份数据，请执行以下步骤：

1. 从门户中转到 "**恢复服务保管库**"，然后前往 "**备份项**"。 然后，选择云中受保护的项（例如，Azure 虚拟机、Azure 存储 [Azure 文件服务] 或 Azure 虚拟机上的 SQL Server）。

    ![选择备份类型。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 右键单击以选择备份项。 根据备份项是否受保护，菜单显示 "**停止备份**" 窗格或 "**删除备份数据**" 窗格。

    - 如果出现 "**停止备份**" 窗格，请从下拉菜单中选择 "**删除备份数据**"。 输入备份项的名称（此字段区分大小写），然后从下拉菜单中选择一个原因。 如果有，请输入备注。 然后选择 "**停止备份**"。

        !["停止备份" 窗格。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 如果 "**删除备份数据**" 窗格出现，请输入备份项的名称（此字段区分大小写），然后从下拉菜单中选择一个原因。 如果有，请输入备注。 然后选择“删除”。 

         !["删除备份数据" 窗格。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. 检查**通知**图标： ![The 通知图标。](./media/backup-azure-delete-vault/messages.png) 完成此过程后，服务将显示以下消息：*正在停止备份并删除 "* 备份项 *"* 的备份数据。 *已成功完成操作*。
6. 选择 "**备份项**" 菜单上的 "**刷新**"，以确保备份项已删除。

      !["删除备份项" 页。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>在本地删除受保护的项

首先，请阅读 " **[开始之前](#before-you-start)** " 部分，了解依赖关系和保管库删除过程。

1. 在保管库仪表板菜单中，选择 "**备份基础结构**"。
2. 根据本地方案，选择以下选项之一：

      - 对于 MARS，选择 "**受保护的服务器**"，然后选择 " **Azure 备份代理**"。 然后，选择要删除的服务器。 

        ![对于 MARS，请选择保管库以打开其仪表板。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 对于 MABS 或 DPM，请选择 "**备份管理服务器**"。 然后，选择要删除的服务器。 


          ![对于 MABS，请选择保管库以打开其仪表板。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 此时将显示 "**删除**" 窗格，其中显示一条警告消息。

     !["删除" 窗格。](./media/backup-azure-delete-vault/delete-protected-server.png)

     查看警告消息和许可复选框中的说明。
    > [!NOTE]
    >- 如果受保护的服务器与 Azure 服务同步并且存在备份项，则同意复选框将显示相关备份项的数量以及用于查看备份项的链接。
    >- 如果受保护的服务器未与 Azure 服务同步并且存在备份项，则同意复选框将仅显示备份项的数量。
    >- 如果没有备份项，则同意复选框会要求删除。

4. 选中 "同意" 复选框，然后选择 "**删除**"。


5. 选中 "![delete 备份数据 ](./media/backup-azure-delete-vault/messages.png) 的**通知**图标。 操作完成后，服务将显示消息：*正在停止备份并删除 "备份项" 的备份数据。* *已成功完成操作*。
6. 选择 "**备份项**" 菜单上的 "**刷新**"，以确保删除备份项。

完成此过程后，你可以从管理控制台中删除备份项目：
    
   - [从 MARS 管理控制台删除备份项](#delete-backup-items-from-the-mars-management-console)
    - [从 MABS 管理控制台中删除备份项](#delete-backup-items-from-the-mabs-management-console)


### <a name="delete-backup-items-from-the-mars-management-console"></a>从 MARS 管理控制台删除备份项

1. 打开 MARS 管理控制台，中转到 "**操作**" 窗格，然后选择 "**计划备份**"。
2. 从 "**修改或停止计划的备份**" 页上，选择 **"停止使用此备份计划" 并删除所有存储的备份**。 然后，选择“下一步”。

    ![修改或停止计划的备份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 从 "**停止计划的备份**" 页上，选择 "**完成**"。

    ![停止计划的备份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系统会提示输入安全 PIN （个人标识号），必须手动生成。 为此，请先登录到 Azure 门户。
5. 请在 "**恢复服务保管库**"  > **设置**" > **属性**"。
6. 在 "**安全 PIN**" 下，选择 "**生成**"。 复制此 PIN。 PIN 的有效时间仅为五分钟。
7. 在管理控制台中，粘贴 PIN，然后选择 **"确定"** 。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. 在 "**修改备份进度**" 页中，将显示以下消息：*已删除的备份数据将保留14天。之后，备份数据将被永久删除。*  

    ![删除备份基础结构。](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地备份项后，请按照门户中的后续步骤进行操作。

### <a name="delete-backup-items-from-the-mabs-management-console"></a>从 MABS 管理控制台中删除备份项

可以使用两种方法从 MABS 管理控制台中删除备份项。

#### <a name="method-1"></a>方法 1
若要停止保护并删除备份数据，请执行以下步骤：

1.  打开 DPM 管理员控制台，然后在导航栏上选择 "**保护**"。
2.  在 "显示" 窗格中，选择要删除的保护组成员。 右键单击以选择 "**停止保护组成员**" 选项。
3.  从 "**停止保护**" 对话框中，选择 "**删除受保护的数据**"，然后选中 "**联机删除存储**" 复选框。 然后选择 "**停止保护**"。

    ![从 "停止保护" 窗格中选择 "删除受保护的数据"。](./media/backup-azure-delete-vault/delete-storage-online.png)

    受保护的成员状态将更改为 "*可用的非活动副本*"。

4. 右键单击非活动保护组，然后选择 "**删除非活动保护**"。

    ![删除非活动保护。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 从 "**删除非活动保护**" 窗口中，选中 "**删除联机存储**" 复选框，然后选择 **"确定"** 。

    ![删除联机存储。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2
打开**MABS 管理**控制台。 在 "**选择数据保护方法**" 下，取消选中 "**我需要在线保护**" 复选框。

  ![选择数据保护方法。](./media/backup-azure-delete-vault/data-protection-method.png)

删除本地备份项后，请按照门户中的后续步骤进行操作。


## <a name="delete-the-recovery-services-vault"></a>删除恢复服务保管库

1. 删除所有依赖项后，滚动到保管库菜单中的 "**概要**" 窗格。
2. 验证没有列出任何备份项、备份管理服务器或复制的项。 如果保管库中仍显示项目，请参阅 "[开始之前](#before-you-start)" 部分。

3. 如果保管库中没有更多项，请在保管库仪表板上选择 "**删除**"。

    ![在保管库仪表板上选择 "删除"。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 选择 **"是"** 以确认要删除该保管库。 保管库已删除。 门户将返回到 "**新建**服务" 菜单。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>使用 PowerShell 删除恢复服务保管库

首先，请阅读 " **[开始之前](#before-you-start)** " 部分，了解依赖关系和保管库删除过程。

要停止保护并删除备份数据，请执行以下操作：

- 如果在 Azure Vm 备份中使用 SQL 并为 SQL 实例启用了自动保护，请首先禁用自动保护。

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection 
           [-InputItem] <ProtectableItemBase> 
           [-BackupManagementType] <BackupManagementType> 
           [-WorkloadType] <WorkloadType> 
           [-PassThru] 
           [-VaultId <String>] 
           [-DefaultProfile <IAzureContextContainer>] 
           [-WhatIf] 
           [-Confirm] 
           [<CommonParameters>] 
    ```

  了解有关如何为受 Azure 备份保护的项禁用保护的[详细信息](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) 

- 停止保护并删除云中所有受备份保护的项的数据（例如 laaS VM、Azure 文件共享等）：

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection 
       [-Item] <ItemBase> 
       [-RemoveRecoveryPoints] 
       [-Force] 
       [-VaultId <String>] 
       [-DefaultProfile <IAzureContextContainer>] 
       [-WhatIf] 
       [-Confirm] 
       [<CommonParameters>] 
    ```
    [了解更多](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) about 为受保护的项目禁用保护。 

- 对于使用备份到 Azure 的 Azure 备份代理（MARS）保护的本地文件和文件夹，请使用以下 PowerShell 命令从每个 MARS PowerShell 模块中删除已备份的数据：

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    将显示以下提示的 Post：
     
    *Microsoft Azure 备份是否确实要删除此备份策略？删除的备份数据将保留14天。之后，备份数据将被永久删除。 <br/> [Y] 是 [A] 所有 [N] 否 [L] 所有 [S] 挂起 [？]帮助（默认值为 "Y"）：*


- 对于使用 MABS （Microsoft Azure 备份 Server）或 DPM 到 Azure （System Center Data Protection Manager）保护的本地计算机，请使用以下命令删除 Azure 中的已备份数据。

    ```
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin> 
    ```

    将显示以下提示的 Post： 
         
   *Microsoft Azure 备份是否确实要删除此备份策略？删除的备份数据将保留14天。之后，备份数据将被永久删除。 <br/> [Y] 是 [A] 所有 [N] 否 [L] 所有 [S] 挂起 [？]帮助（默认值为 "Y"）：*

删除备份的数据后，取消注册任何本地容器和管理服务器。 

- 对于使用 Azure 备份代理（MARS）备份到 Azure 的本地文件和文件夹，请执行以下操作：

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer 
              [-Container] <ContainerBase> 
              [-PassThru] 
              [-VaultId <String>] 
              [-DefaultProfile <IAzureContextContainer>] 
              [-WhatIf] 
              [-Confirm] 
              [<CommonParameters>] 
    ```
    [详细了解](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)如何从保管库中取消注册 Windows Server 或其他容器。 

- 对于使用 MABS （Microsoft Azure 备份 Server）或 DPM 到 Azure 保护的本地计算机（系统中心数据保护管理：

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [详细了解](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0)如何从保管库中取消注册备份管理容器。

在永久删除备份的数据并取消注册所有容器后，请继续删除保管库。 

若要强制删除恢复服务保管库，请执行以下操作： 

   ```PowerShell
       Remove-AzRecoveryServicesVault 
      -Vault <ARSVault> 
      [-DefaultProfile <IAzureContextContainer>] 
      [-WhatIf] 
      [-Confirm] 
      [<CommonParameters>]        
   ```

[详细了解](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)如何删除恢复服务保管库。 

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>使用 CLI 删除恢复服务保管库

首先，请阅读 " **[开始之前](#before-you-start)** " 部分，了解依赖关系和保管库删除过程。

> [!NOTE]
> 目前，Azure 备份 CLI 仅支持管理 Azure VM 备份，因此，仅当保管库包含 Azure VM 备份时，以下命令才能删除保管库。 如果保管库包含 Azure Vm 以外的其他类型的备份项，则不能使用 Azure 备份 CLI 删除保管库。 

若要删除现有的恢复服务保管库，请执行以下操作： 

- 停止保护并删除备份数据 

    ```CLI
    az backup protection disable --container-name 
                             --item-name 
                             [--delete-backup-data {false, true}] 
                             [--ids] 
                             [--resource-group] 
                             [--subscription] 
                             [--vault-name] 
                             [--yes] 
    ```

    有关详细信息，请参阅此 [文](/cli/azure/backup/protection#az-backup-protection-disable)。 

- 删除现有的恢复服务保管库： 

    ```CLI
    az backup vault delete [--force] 
                       [--ids] 
                       [--name] 
                       [--resource-group] 
                       [--subscription] 
                       [--yes] 
    ```

    有关详细信息，请参阅此 [文](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) 

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>使用 Azure 资源管理器删除恢复服务保管库

仅当删除了所有依赖项并且仍收到*保管库删除错误*时，才建议使用此选项来删除恢复服务保管库。 尝试以下任意或所有提示：

- 在保管库菜单的 "**概要**" 窗格中，验证是否没有列出任何备份项、备份管理服务器或复制的项。 如果有备份项，请参阅 "[开始之前](#before-you-start)" 部分。
- 尝试再次[从门户中删除保管库](#delete-the-recovery-services-vault)。
- 如果删除了所有依赖项，但仍收到*保管库删除错误*，请使用 ARMClient 工具执行以下步骤（在注释之后）。

1. 请切换到[chocolatey.org](https://chocolatey.org/)下载并安装 chocolatey。 然后，运行以下命令安装 ARMClient：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登录到 Azure 帐户，并运行以下命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 门户中，收集要删除的保管库的订阅 ID 和资源组名称。

有关 ARMClient 命令的详细信息，请参阅[ARMCLIENT 自述文件](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

1. 使用你的订阅 ID、资源组名称和保管库名称运行以下命令。 如果没有任何依赖项，则在运行以下命令时，将删除该保管库：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. 如果保管库不为空，则将收到以下错误消息：*无法删除保管库，因为此保管库中存在现有资源。* 若要删除保管库中受保护的项或容器，请运行以下命令：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 门户中，请确保已删除保管库。


## <a name="next-steps"></a>后续步骤

[了解恢复服务保管库](backup-azure-recovery-services-vault-overview.md)<br/>
[了解如何监视和管理恢复服务保管库](backup-azure-manage-windows-server.md)
