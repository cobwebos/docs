---
title: "将 Windows 系统状态备份到 Azure | Microsoft Docs"
description: "了解如何将 Windows Server 的系统状态和/或 Windows 计算机备份到 Azure。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: "如何备份; 备份文件和文件夹"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>备份资源管理器部署中的 Windows 系统状态
本文介绍了如何将 Windows Server 系统状态备份到 Azure。 本教程旨在引导完成基本操作。

如果想要深入了解 Azure 备份，请阅读此 [概述](backup-introduction-to-azure-backup.md)。

如果还没有 Azure 订阅，可以先创建一个 [免费帐户](https://azure.microsoft.com/free/)，这样就可以访问任何 Azure 服务。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
要备份文件和文件夹，需要在要存储数据的区域内创建一个恢复服务保管库。 还需要确定复制存储的方式。

### <a name="to-create-a-recovery-services-vault"></a>创建恢复服务保管库
1. 如果尚未登录 [Azure 门户](https://portal.azure.com/)，请使用 Azure 订阅登录。
2. 在“中心”菜单中，单击“更多服务”，并在资源列表中，键入“恢复服务”并单击“恢复服务保管库”。

    ![创建恢复服务保管库步骤 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    如果在订阅中有恢复服务保管库，则会列出这些保管库。
3. 在“恢复服务保管库”菜单中，单击“添加”。

    ![创建恢复服务保管库步骤 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    此时会打开恢复服务保管库边栏选项卡，其中会提示提供“名称”、“订阅”、“资源组”和“位置”。

    ![创建恢复服务保管库步骤 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. 对于“名称”，请输入一个友好名称以标识保管库 。 名称对于 Azure 订阅需要是唯一的。 键入包含 2 到 50 个字符的名称。 名称必须以字母开头，只能包含字母、数字和连字符。

5. 在“订阅”部分，通过下拉菜单选择 Azure 订阅。 如果只使用一个订阅，则会显示该订阅，用户可以跳到下一步。 如果不确定要使用哪个订阅，请使用默认的（或建议的）订阅。 仅当组织帐户与多个 Azure 订阅关联时，才会有多个选项。

6. 在“资源组”部分：

    * 如果要创建资源组，请选择“新建”。
    或
    * 选择“使用现有项”，并单击下拉菜单查看可用的资源组列表。

  有关资源组的完整信息，请参阅 [Azure Resource Manager 概述](../azure-resource-manager/resource-group-overview.md)。

7. 单击“位置”，为保管库选择地理区域  。 此选项决定了备份数据要发送到的地理区域。

8. 在恢复服务保管库边栏选项卡的底部，单击“创建”。

    创建恢复服务保管库可能需要数分钟。 可以在门户右上区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果在几分钟后看不到保管库，请单击“刷新”。

    ![单击“刷新”按钮](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    一旦在恢复服务保管库列表中看到保管库，即可设置存储冗余。

### <a name="set-storage-redundancy-for-the-vault"></a>为保管库设置存储冗余
在创建恢复服务保管库时，请确保存储冗余配置为所希望的方式。

1. 在“恢复服务保管库”边栏选项卡中，单击新保管库。

    ![在恢复服务保管库列表中选择新保管库](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    选择保管库时，“恢复服务保管库”边栏选项卡会缩窄，“设置”边栏选项卡（*顶部有保管库的名称*）和保管库详细信息边栏选项卡会打开。

    ![查看新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 在新保管库的“设置”边栏选项卡中，使用垂直滚动条向下滚动到“管理”部分，并单击“备份基础结构”。
    此时会打开“备份基础结构”边栏选项卡。
3. 在“备份基础结构”边栏选项卡中，单击“备份配置”打开“备份配置”边栏选项卡。

    ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. 为保管库选择合适的存储复制选项。

    ![存储配置选择](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    默认情况下，保管库具有异地冗余存储。 如果使用 Azure 作为主要备份存储终结点，请继续使用“异地冗余”。 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。 请在此[存储冗余概述](../storage/common/storage-redundancy.md)中深入了解[异地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)和[本地冗余](../storage/common/storage-redundancy.md#locally-redundant-storage)存储选项。

创建保管库之后，即可对其进行配置，以便备份 Windows 系统状态。

## <a name="configure-the-vault"></a>配置保管库
1. 在“恢复服务保管库”边栏选项卡（对应于刚创建的保管库）的“开始”部分单击“备份”，并在“开始使用备份”边栏选项卡上选择“备份目标”。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    此时会打开“备份目标”边栏选项卡。

    ![打开备份目标边栏选项卡](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. 从“工作负荷的运行位置?”下拉菜单中选择“本地”。

    之所以选择“本地”，是因为 Windows Server 或 Windows 计算机是不在 Azure 中的物理机。

3. 从“要备份的项?”菜单中选择“系统状态”，并单击“确定”。

    ![配置文件和文件夹](./media/backup-azure-system-state/backup-goal-system-state.png)

    单击“确定”后，会在“备份目标”旁边显示一个复选标记，并会打开“准备基础结构”边栏选项卡。

    ![配置备份目标以后，接下来准备基础结构](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. 在“准备基础结构”边栏选项卡上，单击“下载 Windows Server 或 Windows 客户端的代理”。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    如果使用的是 Windows Server Essential，则选择下载 Windows Server Essential 的代理。 弹出菜单会提示用户运行或保存 MARSAgentInstaller.exe。

    ![MARSAgentInstaller 对话框](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. 在下载弹出窗口中单击“保存”。

    默认情况下，**MARSagentinstaller.exe** 文件将保存到 Downloads 文件夹。 下载完安装程序以后，会显示一个弹出窗口，询问用户是要运行安装程序，还是要打开文件夹。

    ![准备基础结构](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    此时还不需要安装代理。 下载保管库凭据之后，即可安装代理。

6. 在“准备基础结构”边栏选项卡上，单击“下载”。

    ![下载保管库凭据](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    保管库凭据下载到 Downloads 文件夹。 下载完保管库凭据以后，会显示一个弹出窗口，询问用户是要打开还是要保存凭据。 单击“保存” 。 如果意外地单击了“打开”，可以让尝试打开保管库凭据的对话框关闭。 不能打开保管库凭据。 继续下一步。 保管库凭据位于 Downloads 文件夹中。   

    ![保管库凭据下载完毕](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>安装并注册代理

> [!NOTE]
> 尚未推出通过 Azure 门户启用备份这一功能。 使用 Microsoft Azure 恢复服务代理备份 Windows Server 的系统状态。
>

1. 在 Downloads 文件夹（或其他保存位置）中找到并双击 **MARSagentinstaller.exe**。

    安装程序会在提取、安装和注册恢复服务代理时提供一系列的消息。

    ![运行恢复服务代理安装程序凭据](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. 完成 Microsoft Azure 恢复服务代理安装向导。 要完成该向导，需要：

   * 选择安装和缓存文件夹的位置。
   * 如果使用代理服务器来连接 Internet，请提供代理服务器信息。
   * 如果使用经过身份验证的代理，请提供用户名和密码详细信息。
   * 提供已下载的保管库凭据
   * 将加密通行短语保存在安全的位置。

     > [!NOTE]
     > 如果丢失或忘记了通行短语，Microsoft 无法帮助你恢复备份数据。 请将文件保存在安全的位置。 还原备份时需要用到此文件。
     >
     >

现已安装代理，且已向保管库注册计算机。 接下来可以配置和计划备份。

## <a name="back-up-windows-server-system-state-preview"></a>备份 Windows Server 系统状态（预览版）
初始备份包括三项任务：

* 使用 Azure 备份代理启用系统状态备份
* 计划备份
* 首次备份文件和文件夹

若要完成初始备份，请使用 Microsoft Azure 恢复服务代理。

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>使用 Azure 备份代理启用系统状态备份

1. 在 PowerShell 会话中，可通过运行以下命令来停止 Azure 备份引擎。

  ```
  PS C:\> Net stop obengine
  ```

2. 打开 Windows 注册表。

  ```
  PS C:\> regedit.exe
  ```

3. 向以下注册表项添加指定的 DWord 值。

  | 注册表路径 | 注册表项 | DWord 值 |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. 通过在提升的命令提示符下执行以下命令来重启备份引擎。

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>计划备份作业

1. 打开 Microsoft Azure 恢复服务代理。 可以通过在计算机中搜索 **Microsoft Azure 备份**找到该代理。

    ![启动 Azure 恢复服务代理](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. 在恢复服务代理中，单击“ **计划备份**”。

    ![计划 Windows Server 备份](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. 在计划备份向导的“开始使用”页上，单击“**下一步**”。

4. 在“选择要备份的项”页上，单击“**添加项**”。

5. 选择“系统状态”，并单击“确定”。

6. 单击“下一步”。

7. 系统状态的备份和保留计划自定设置为在当地时间每周日晚上 9:00 进行备份，设置的保留期为 60 天。

   > [!NOTE]
   > 系统状态的备份和保留策略均为自动备份。 除了 Windows Server 系统状态之外，如果还想备份文件和文件夹，请仅根据向导指定备份文件的备份和保留策略。 
   >

8. 在“确认”页上复查信息，并单击“**完成**”。

9. 在向导完成创建备份计划后，请单击“**关闭**”。

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>首次备份 Windows Server 系统状态

1. 请确保需要重启的 Windows Server 没有挂起的更新。

2. 在恢复服务代理中单击“ **立即备份** ”，以通过网络完成初始种子设定。

    ![立即备份 Windows Server](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. 在“确认”页上复查“立即备份向导”用于备份计算机的设置。 然后单击“备份”。

4. 单击“**关闭**”以关闭向导。 如果在备份过程完成之前关闭向导，向导将继续在后台运行。

5. 如果在服务器上备份文件和文件夹，除了 Windows Server 系统状态之外，“立即备份”向导将仅备份文件。 若要进行临时的系统状态备份，请执行以下 PowerShell 命令：

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  完成初始备份后，备份控制台中会显示“**作业已完成**”状态。

  ![IR 完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>常见问题

以下问题和答案可提供补充信息。

### <a name="what-is-the-staging-volume"></a>什么是暂存卷？

暂存卷表示 Windows Server 备份暂存系统状态备份的中间位置，该位置本机可用。 然后由 Azure 备份代理将此中间备份压缩并加密，通过安全的 HTTPS 协议将其发送到配置的恢复服务保管库。 我们强烈建议在非 Windows 操作系统的卷中创建暂存卷。**如果在系统状态备份中发现问题，进行故障排除的第一步就是对暂存卷的位置进行检查。** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>如何更改 Azure 备份代理中指定的暂存卷路径？

暂存卷默认位于缓存文件夹中。 

1. 若要更改此位置，请在提升的命令提示符中使用以下命令：
  ```
  PS C:\> Net stop obengine
  ```

2. 然后更新以下注册表项，使路径指向新的暂存卷文件夹。

  |注册表路径|注册表项|值|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | 新的暂存卷位置 |

暂存路径区分大小写，并且必须与服务器上的路径完全相同。 

3. 更改暂存卷路径后，请重启备份引擎：
  ```
  PS C:\> Net start obengine
  ```
4. 若要选取更改后的路径，请打开 Microsoft Azure 恢复服务代理，然后触发系统状态的临时备份。

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>为何要将系统状态的默认保留期设置为 60 天？

系统状态备份的使用寿命与为 Windows Server Active Directory 角色设置的“逻辑删除生存期”设置相同。 逻辑删除生存期条目的默认值为 60 天。 此值可在目录服务 (NTDS) config 对象上设置。

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>如何更改系统备份的默认备份和保留策略？

若要更改系统备份的默认备份和保留策略，请执行以下操作：
1. 停止备份引擎。 在提升的命令提示符下运行以下命令。

  ```
  PS C:\> Net stop obengine
  ```

2. 在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider 中添加或更新以下注册表项条目。

  |注册表名称|说明|值|
  |-------------|-----------|-----|
  |SSBScheduleTime|用于配置备份时间。 默认值为当地时间晚上 9 点。|DWord：HHMM（十进制）格式，例如 2130 表示当地时间晚上 9:30|
  |SSBScheduleDays|用于配置在指定的时间执行系统状态备份的日期。 单个数字指代星期几。 0 表示星期日，1 表示星期一，由此类推。 备份的默认日期为星期日。|DWord：在星期几运行备份（十进制），例如 1230 表示计划在星期一、星期二、星期三和星期日进行备份。|
  |SSBRetentionDays|用于配置备份保留的天数。 默认值为 60。 允许的最大值为 180。|DWord：备份保留的天数（十进制）。|

3. 使用以下命令重启备份引擎。
    ```
    PS C:\> Net start obengine
    ```

4. 打开 Microsoft Azure 恢复服务代理。

5. 单击“计划备份”，然后单击“下一步”，直到更改反映出来。

6. 单击“完成”应用更改。


## <a name="questions"></a>有疑问？
如果有疑问，或者希望包含某种功能，请 [给我们反馈](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>后续步骤
* 详细了解如何 [备份 Windows 计算机](backup-configure-vault.md)。
* 备份文件和文件夹后，可以 [管理保管库和服务器](backup-azure-manage-windows-server.md)。
* 如果需要还原备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)一文。

