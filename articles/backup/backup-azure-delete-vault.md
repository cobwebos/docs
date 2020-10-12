---
title: 删除 Microsoft Azure 恢复服务保管库
description: 本文介绍了如何先删除依赖项，然后删除 Azure 备份恢复服务保管库。
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: fd941db933d243b83c1c19c7ae0fdfc2d7869b8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293061"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>删除 Azure 备份恢复服务保管库

本文介绍如何删除 [Azure 备份](backup-overview.md)恢复服务保管库。 其中分别说明了如何删除依赖项，以及如何删除保管库。

## <a name="before-you-start"></a>开始之前

不能删除具有以下任何依赖项的恢复服务保管库：

- 不能删除包含受保护数据源的保管库 (例如，IaaS Vm、SQL 数据库、Azure 文件共享) 。
- 不能删除包含备份数据的保管库。 删除备份数据后，它将进入已软删除状态。
- 不能删除包含处于已软删除状态的备份数据的保管库。
- 不能删除包含已注册存储帐户的保管库。

如果在不删除依赖项的情况下尝试删除保管库，将会遇到以下错误消息之一：

- 由于此保管库中存在现有资源，因此无法删除此保管库。 请确保不存在与此保管库关联的备份项、受保护的服务器或备份管理服务器。 注销与此保管库关联的以下容器，然后再继续删除。

- 不能删除恢复服务保管库，因为该保管库中存在处于软删除状态的备份项。 完成删除操作 14 天后，软删除的项将永久删除。 请在永久删除了备份项且保管库中未留下任何处于软删除状态的项之后，尝试删除保管库。 有关详细信息，请参阅 [Azure 备份的软删除](./backup-azure-security-feature-cloud.md)。

## <a name="proper-way-to-delete-a-vault"></a>删除保管库的正确方式

>[!WARNING]
>以下操作是破坏性的，无法撤消。 与受保护服务器关联的所有备份数据和备份项将被永久删除。 请谨慎操作。

若要正确删除保管库，必须按以下顺序执行步骤：

- **步骤 1**：禁用软删除功能。 若要了解禁用软删除的步骤，[请参阅此文](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)。

- **步骤 2**：禁用软删除后，请检查是否有任何项之前处于软删除状态。 如果存在处于软删除状态的项，则需要先取消删除它们，然后再次将其删除。 请[按这些步骤操作](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items)，以便查找软删除项并将其永久删除。

- **步骤 3**：你必须检查以下全部三个位置，以便验证是否有任何受保护的项：

  - **云中受保护的项**：转到保管库仪表板菜单 >“备份项”。 必须通过“停止备份”或“删除备份数据”删除此处列出的所有项及其备份数据。  请[按这些步骤操作](#delete-protected-items-in-the-cloud)来删除这些项。
  - **SQL Server 实例**：转到保管库仪表板菜单 >“备份基础结构” > “受保护的服务器”。 在“受保护的服务器”中，选择要取消注册的服务器。 若要删除保管库，必须取消注册所有服务器。 右键单击受保护的服务器并选择“取消注册”。
  - **受 MARS 保护的服务器**：转到保管库仪表板菜单 >“备份基础结构” > “受保护的服务器”。 如果你拥有受 MARS 保护的服务器，则必须删除此处列出的所有项及其备份数据。 请[按这些步骤操作](#delete-protected-items-on-premises)，以便删除受 MARS 保护的服务器。
  - **MABS 或 DPM 管理服务器**：转到保管库仪表板菜单 >“备份基础结构” > “备份管理服务器”。 如果你有 DPM 或 Azure 备份服务器 (MABS)，则必须删除或注销此处列出的所有项及其备份数据。 请[按这些步骤操作](#delete-protected-items-on-premises)，以便删除管理服务器。

- **步骤 4**：必须确保删除所有已注册的存储帐户。 转到保管库仪表板菜单 >“备份基础结构” > “存储帐户”。 如果此处列出了你的存储帐户，则必须注销所有这些帐户。 若要了解如何注销帐户，请参阅 [取消注册存储帐户](manage-afs-backup.md#unregister-a-storage-account)。

完成这些步骤后，你可以继续[删除保管库](#delete-the-recovery-services-vault)。

如果你在本地或云上没有任何受保护的项，但仍收到保管库删除错误，请执行[使用 Azure 资源管理器删除恢复服务保管库](#delete-the-recovery-services-vault-by-using-azure-resource-manager)中的步骤

## <a name="delete-protected-items-in-the-cloud"></a>删除云中的受保护项

首先，请阅读 **[开始之前](#before-you-start)** 部分，以了解依赖项和保管库删除过程。

若要停止保护并删除备份数据，请执行以下操作：

1. 在门户中依次转到“恢复服务保管库”、“备份项”。  然后，选择云中的受保护项（例如，Azure 虚拟机、Azure 存储 [Azure 文件服务] 或 Azure 虚拟机上的 SQL Server）。

    ![选择备份类型。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 右键单击并选择备份项。 根据该备份项是否受保护，菜单将显示“停止备份”窗格或“删除备份数据”窗格。 

    - 如果显示了“停止备份”窗格，请从下拉菜单中选择“删除备份数据”。  输入备份项的名称（此字段区分大小写），然后从下拉菜单中选择原因。 输入备注（如果有）。 然后选择“停止备份”。

        ![“停止备份”窗格。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - 如果显示了“删除备份数据”窗格，请输入备份项的名称（此字段区分大小写），然后从下拉菜单中选择原因。 输入备注（如果有）。 然后选择“删除”。

         ![“删除备份数据”窗格。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 查看“通知”图标：![“通知”图标。](./media/backup-azure-delete-vault/messages.png) 完成该过程后，服务将显示以下消息：“正在停止 <备份项> 的备份并删除备份数据。  已成功完成该操作”。
4. 在“备份项”菜单中选择“刷新”，确保已删除该备份项。 

      ![“删除备份项”页。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>删除本地的受保护项

首先，请阅读 **[开始之前](#before-you-start)** 部分，以了解依赖项和保管库删除过程。

1. 在保管库仪表板菜单中，选择“备份基础结构”。
2. 根据本地方案选择以下选项之一：

      - 对于 MARS，请依次选择“受保护的服务器”、“Azure 备份代理”。  然后选择要删除的服务器。

        ![对于 MARS，请选择保管库打开其仪表板。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - 对于 MABS 或 DPM，请选择“备份管理服务器”。 然后选择要删除的服务器。

          ![对于 MABS 或 DPM，请选择保管库以打开其仪表板。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 此时会显示包含警告消息的“删除”窗格。

     ![“删除”窗格。](./media/backup-azure-delete-vault/delete-protected-server.png)

     查看警告消息，以及许可复选框中的说明。
    > [!NOTE]
    >
    >- 如果受保护的服务器已与 Azure 服务同步，并且备份项存在，则许可复选框将显示相关备份项的数量，以及用于查看备份项的链接。
    >- 如果受保护的服务器未与 Azure 服务同步，并且备份项存在，则许可复选框只会显示备份项的数量。
    >- 如果没有任何备份项，则许可复选框将要求确认删除。

4. 选中许可复选框，然后选择“删除”。

5. 查看“通知”图标![删除备份数据](./media/backup-azure-delete-vault/messages.png)。 操作完成后，服务将显示以下消息：正在停止“备份项”的备份并删除备份数据。 已成功完成该操作”。
6. 在“备份项”菜单中选择“刷新”，确保已删除该备份项。 

>[!NOTE]
>如果你从门户中删除包含依赖项的本地受保护项，则会收到一条警告，指出“删除服务器的注册是破坏性操作，无法撤消。 与受保护服务器关联的所有备份数据（还原数据所需的恢复点）和备份项将被永久删除。”

此过程完成后，可以继续从管理控制台删除备份项：

- [从 MARS 管理控制台删除备份项](#delete-backup-items-from-the-mars-management-console)
- [从 MABS 或 DPM 管理控制台删除备份项](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>从 MARS 管理控制台删除备份项

>[!NOTE]
>如果在不停止备份的情况下删除或丢失了源计算机，则下一个计划的备份将失败。 旧恢复点将根据策略过期，但始终会保留最后一个恢复点，直至你停止备份并删除数据。 为此，可以按照[本部分](#delete-protected-items-on-premises)的这些步骤操作。

1. 打开 MARS 管理控制台，转到“操作”窗格并选择“计划备份”。 
2. 在“修改或停止计划的备份”页中选择“停止使用此备份计划并删除所有存储的备份”选项。  然后，选择“下一步”****。

    ![修改或停止计划的备份。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. 在“停止计划的备份”页中选择“完成”。 

    ![停止计划的备份。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. 系统会提示输入安全 PIN（个人标识号）。必须手动生成该 PIN。 为此，请先登录到 Azure 门户。
5. 转到“恢复服务保管库” > “设置” > “属性”。
6. 在“安全 PIN”下选择“生成” 。 复制此 PIN。 该 PIN 的有效时间仅为五分钟。
7. 在管理控制台中粘贴该 PIN，然后选择“确定”。

    ![生成安全 PIN。](./media/backup-azure-delete-vault/security-pin.png)

8. “修改备份进度”页中会显示以下消息：“删除的备份数据会保留 14 天。该时间过后，备份数据会被永久删除。”  

    ![删除备份基础结构。](./media/backup-azure-delete-vault/deleted-backup-data.png)

删除本地备份项后，遵循门户中的后续步骤。

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>从 MABS 或 DPM 管理控制台删除备份项

>[!NOTE]
>如果在不停止备份的情况下删除或丢失了源计算机，则下一个计划的备份将失败。 旧恢复点将根据策略过期，但始终会保留最后一个恢复点，直至你停止备份并删除数据。 为此，可以按照[本部分](#delete-protected-items-on-premises)的这些步骤操作。

可以使用两种方法从 MABS 或 DPM 管理控制台删除备份项。

#### <a name="method-1"></a>方法 1

若要停止保护并删除备份数据，请执行以下步骤：

1. 打开 DPM 管理员控制台，在导航栏中选择“保护”。
2. 在显示窗格中，选择要删除的保护组成员。 右键单击并选择“停止对组成员的保护”选项。
3. 在“停止保护”对话框中选择“删除受保护的数据”，并选中“删除在线存储”复选框。   然后选择“停止保护”。

    ![在“停止保护”窗格中选择“删除受保护的数据”。](./media/backup-azure-delete-vault/delete-storage-online.png)

    受保护成员状态更改为“非活动副本可用”。

4. 右键单击停用保护组，然后选择“删除停用保护”。

    ![删除非活动保护。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. 在“删除非活动保护”窗口中选中“删除在线存储”复选框，然后选择“确定”。  

    ![删除在线存储。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2

打开“MABS 管理”或“DPM 管理”控制台 。 在“选择数据保护方法”下，清除“我需要在线保护”复选框。 

  ![选择数据保护方法。](./media/backup-azure-delete-vault/data-protection-method.png)

删除本地备份项后，遵循门户中的后续步骤。

## <a name="delete-the-recovery-services-vault"></a>删除恢复服务保管库

1. 删除所有依赖项后，在保管库菜单中滚动到“概要”窗格。
2. 确认是否未列出任何“备份项”、“备份管理服务器”或“复制的项”。 如果项仍显示在保管库中，请参阅[开始之前](#before-you-start)部分。

3. 当保管库中没有其他任何项时，在保管库仪表板上选择“删除”。

    ![在保管库仪表板上选择“删除”。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. 选择“是”确认要删除该保管库。 随即会删除该保管库。 门户返回到“新建”服务菜单。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>使用 PowerShell 删除恢复服务保管库

首先，请阅读 **[开始之前](#before-you-start)** 部分，以了解依赖项和保管库删除过程。

若要停止保护并删除备份数据：

- 如果在 Azure VM 备份中使用 SQL 并为 SQL 实例启用了自动保护，请先禁用自动保护。

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

  [详细了解](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection)如何对 Azure 备份保护的项禁用保护。

- 停止保护并删除云中 (中所有受备份保护的项的数据，例如： IaaS VM、Azure 文件共享等) ：

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

    [详细了解](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)如何对备份保护的项禁用保护。

- 对于使用 Azure 备份代理 (MARS) 备份到 Azure 的受保护本地文件和文件夹，请使用以下 PowerShell 命令从每个 MARS PowerShell 模块中删除备份的数据：

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    之后，将出现以下提示：

    *Microsoft Azure 备份。确实要删除此备份策略吗?删除的备份数据会保留 14 天。之后，备份数据将永久删除。<br/> [Y] 是 [A] 全部为“是”[N] 否 [L] 全部为“否”[S] 暂停 [?] 帮助(默认选项为“Y”):*

- 对于使用 MABS 保护的本地计算机 (Microsoft Azure 备份 Server) 或 DPM (System Center Data Protection Manager) 到 Azure，请使用以下命令删除 Azure 中的备份数据。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    之后，将出现以下提示：

   Microsoft Azure 备份 确实要删除此备份策略吗? 删除的备份数据会保留 14 天。 该时间过后，备份数据会被永久删除。 <br/>
   [Y] 是 [A] 全部是 [N] 否 [L] 全部否 [S] 暂停  [?] 帮助 (默认值为 "Y"):*

删除备份的数据后，取消注册所有本地容器和管理服务器。

- 对于使用 Azure 备份代理 (MARS) 备份到 Azure 的受保护本地文件和文件夹：

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

    [详细了解](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer)如何从保管库中取消注册 Windows 服务器或其他容器。

- 对于使用 MABS（Microsoft Azure 备份服务器）或 DPM (System Center Data Protection Manager) 在 Azure 中进行保护的本地计算机：

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

    [详细了解](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer)如何从保管库中取消注册备份管理容器。

永久删除备份的数据并取消注册所有容器后，继续删除保管库。

若要强制删除恢复服务保管库，请执行以下操作：

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[详细了解](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)如何删除恢复服务保管库。

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>使用 CLI 删除恢复服务保管库

首先，请阅读 **[开始之前](#before-you-start)** 部分，以了解依赖项和保管库删除过程。

> [!NOTE]
> 目前，Azure 备份 CLI 仅支持管理 Azure VM 备份，因此，仅当保管库包含 Azure VM 备份时，以下命令才能删除保管库。 如果保管库包含非 Azure VM 类型的任何备份项，则无法使用 Azure 备份 CLI 删除该保管库。

若要删除现有的恢复服务保管库，请执行以下步骤：

- 停止保护并删除备份数据

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    有关详细信息，请参阅 [此文](/cli/azure/backup/protection#az-backup-protection-disable)。

- 删除现有的恢复服务保管库：

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    有关详细信息，请参阅 [此文](/cli/azure/backup/vault)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>使用 Azure 资源管理器删除恢复服务保管库

仅当已删除所有依赖项后，仍然收到“保管库删除错误”时，才建议使用删除恢复服务保管库的选项。 请尝试以下任意或所有提示：

- 在“概要”窗格中的保管库菜单内，确认是否未列出任何备份项、备份管理服务器或复制的项。 如果存在备份项，请参阅[开始之前](#before-you-start)部分。
- 重试[从门户删除保管库](#delete-the-recovery-services-vault)。
- 如果删除了所有依赖项，但仍收到“保管库删除错误”，请使用 ARMClient 工具执行以下步骤（注释后面的步骤）。

1. 访问 [chocolatey.org](https://chocolatey.org/) 下载并安装 Chocolatey。 然后运行以下命令安装 ARMClient：

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 登录到 Azure 帐户，然后运行以下命令：

    `ARMClient.exe login [environment name]`

3. 在 Azure 门户中，收集所要删除的保管库的订阅 ID 和资源组名称。

有关 ARMClient 命令的详细信息，请参阅 [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md)。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>使用 Azure 资源管理器客户端删除恢复服务保管库

1. 使用订阅 ID、资源组名称和保管库名称运行以下命令。 如果没有任何依赖项，则运行以下命令时会删除保管库：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. 如果保管库不为空，将出现以下错误消息：“由于此保管库中存在现有资源，因此无法删除此保管库。” 若要删除保管库中受保护的项或容器，请运行以下命令：

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. 在 Azure 门户中，确保已删除该保管库。

## <a name="next-steps"></a>后续步骤

[了解恢复服务保管库](backup-azure-recovery-services-vault-overview.md)
[了解如何监视和管理恢复服务保管库](backup-azure-manage-windows-server.md)
