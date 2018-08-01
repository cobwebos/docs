---
title: 对 Azure 备份代理进行故障排除
description: 排除与 Azure 备份代理的安装和注册相关的问题
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: e7a63167285c06fdfe632e7d45d9fddd3cca7842
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248516"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>排查 Microsoft Azure 恢复服务 (MARS) 代理问题
## <a name="recommended-steps"></a>建议的步骤
请参考本文解决使用 MARS 代理进行配置、注册、备份和还原期间遇到的错误。

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>提供的保管库凭据无效。 该文件已损坏，或者没有与恢复服务关联的最新凭据。
| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** </br> *提供的保管库凭据无效。该文件已损坏，或者没有与恢复服务关联的最新凭据。(ID: 34513)* | <ul><li> 保管库凭据无效（也就是说，它们是在注册之前的 48 小时前下载的）。<li>MARS 代理无法将文件下载到 Windows Temp 目录。 <li>保管库凭据位于某个网络位置。 <li>TLS 1.0 已禁用<li> 所配置的某个代理服务器正在阻止连接。 <br> |  <ul><li>下载新的保管库凭据。<li>转到“Internet 选项” > “安全” > “Internet”。 接下来，选择“自定义级别”，并滚动直至看到“文件下载”部分。 然后选择“启用”。<li>可能还需要将站点添加到[受信任的站点](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>更改设置以使用代理服务器。 然后提供代理服务器详细信息。 <li> 使日期和时间与你的计算机匹配。<li>转到 C:/Windows/Temp，检查是否存在超过 60,000 或 65,000 个扩展名为 .tmp 的文件。 如果存在，请删除这些文件。<li>可以在服务器上运行 SDP 包进行验证。 如果收到的错误指出不允许下载文件，则可能是因为 C:/Windows/Temp 目录中存在大量文件。<li>确保已安装了 .NET Framework 4.6.2。 <li>如果由于 PCI 符合性而禁用了 TLS 1.0，请参阅此[故障排除页面](https://support.microsoft.com/help/4022913)。 <li>如果服务器中安装了防病毒软件，请从防病毒软件扫描中排除以下文件： <ul><li>CBengine.exe<li>CSC.exe，这与 .NET Framework 相关。 服务器上安装的每个 .NET 版本都有一个 CSC.exe。 请排除受影响服务器上的所有 .NET Framework 版本绑定的 CSC.exe 文件。 <li>Scratch 文件夹或缓存位置。 <br>*scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** </br><ol><li>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份。(ID: 100050) 请检查网络设置，并确保能够连接到 Internet<li>(407) 需要代理身份验证 |代理正在阻止连接。 |  <ul><li>转到“Internet 选项” > “安全” > “Internet”。 然后选择“自定义级别”，并滚动直至看到“文件下载”部分。 选择“启用”。<li>可能还需要将站点添加到[受信任的站点](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>更改设置以使用代理服务器。 然后提供代理服务器详细信息。 <li>如果服务器中安装了防病毒软件，请从防病毒软件扫描中排除以下文件。 <ul><li>CBEngine.exe（而非 dpmra.exe）。<li>CSC.exe（与 .NET Framework 相关）。 服务器上安装的每个 .NET 版本都有一个 CSC.exe。 请排除受影响服务器上的所有 .NET Framework 版本绑定的 CSC.exe 文件。 <li>Scratch 文件夹或缓存位置。 <br>*scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>未能设置安全备份的加密密钥

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |      
| **错误** </br>*无法设置安全备份的加密密钥，激活未完全成功，但是加密密码已保存到以下文件中*。 |<li>服务器已注册到另一个保管库。<li>在配置期间，密码已损坏| 从该保管库中取消注册服务器，然后使用新密码重新注册。

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>激活未成功完成。 由于内部服务错误 [0x1FC07]，当前操作失败

| 错误详细信息 | 可能的原因 | 建议的操作 |
|---------|---------|---------|
|**错误** </br><ol>*激活未成功完成。由于内部服务错误 [0x1FC07]，当前操作失败。稍后重试操作。如果该问题仍然存在，请联系 Microsoft 支持部门*     | <li> Scratch 文件夹位于空间不足的卷上。 <li> 已错误地将 Scratch 文件夹移到另一位置。 <li> 缺少 OnlineBackup.KEK 文件。         | <li>升级到[最新版本](http://aka.ms/azurebackup_agent)的 MARS 代理。<li>将 scratch 文件夹或缓存位置移到可用空间相当于备份数据总大小 5-10% 的卷。 若要正确地移动缓存位置，请参阅[有关 Azure 备份代理的问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步骤。<li> 确保 OnlineBackup.KEK 文件存在。 <br>*scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>错误 34506。 存储在此计算机上的加密密码未正确配置

| 错误详细信息 | 可能的原因 | 建议的操作 |
|---------|---------|---------|
|**错误** </br><ol>错误 34506。*未在此计算机上正确配置存储的加密密码*。    | <li> Scratch 文件夹位于空间不足的卷上。 <li> 已错误地将 Scratch 文件夹移到另一位置。 <li> 缺少 OnlineBackup.KEK 文件。        | <li>升级到[最新版本](http://aka.ms/azurebackup_agent)的 MARS 代理。<li>将 scratch 文件夹或缓存位置移到可用空间相当于备份数据总大小 5-10% 的卷。 若要正确地移动缓存位置，请参阅[有关 Azure 备份代理的问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步骤。<li> 确保 OnlineBackup.KEK 文件存在。 <br>*scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。         |

## <a name="backups-do-not-run-as-per-schedule"></a>备份不按计划运行
如果计划的备份不会自动触发，而手动备份却能正常进行，请执行以下步骤。 
 
<li>确保服务器上已安装 PowerShell 3.0 或更高版本。 若要检查 PowerShell 版本，请执行以下命令，并确认 *Major* 版本号是等于或大于 3。

`$PSVersionTable.PSVersion`
<li>确保 *PSMODULEPATH* 环境变量中包含以下路径。

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>如果 *LocalMachine* 的 powershell 执行策略设置为 restricted，则触发备份任务的 powershell cmdlet 可能失败。 以权限提升的模式执行以下命令，将执行策略设置为 *Unrestricted* 或 *RemoteSigned* 并检查

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>转到“控制面板”->“管理工具”->“任务计划程序”-> 展开“Microsoft”-> 选择“联机备份”
<li>双击“Microsoft-OnlineBackup”任务，然后转到“触发器”选项卡。
<li>确保该任务的“状态”设置为“已启用”。 否则，请单击“编辑”并选中“已启用”复选框
<li>导航到“常规”选项卡的“安全选项”部分
<li>确保选择用于运行任务的用户帐户是 *SYSTEM* 或服务器上的本地管理员组 > [!TIP] 建议在执行上述步骤后重新启动服务器，以确保一致性地应用所做的更改


## <a name="troubleshooting-restore-issues"></a>排查还原问题

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>恢复文件和文件夹期间无法装载恢复卷
如果即使在单击“装载”几分钟后 Azure 备份仍无法成功装载恢复卷，或者无法装载具有一个或多个错误的恢复卷，请按照以下步骤正常恢复。

1.  取消正在进行的安装过程（如果它已运行了几分钟）。

2.  确保使用最新版本的 Azure 备份代理。 若要了解 Azure 备份代理的版本信息，请在 Microsoft Azure 备份控制台的“操作”窗格上单击“关于 Microsoft Azure 恢复服务代理”，并确保“版本”号等于或高于[本文](https://go.microsoft.com/fwlink/?linkid=229525)中提到的版本。 可以在[此处](https://go.microsoft.com/fwLink/?LinkID=288905)下载最新版本

3.  转到“设备管理器” -> “存储控制器”，确保可以找到“Microsoft iSCSI 发起程序”。 如果可以找到它，请直接转到下面的步骤 7。 

4.  如果无法在步骤 3 中找到“Microsoft iSCSI 发起程序”服务，请检查是否可以在“设备管理器” -> “存储控制器”下找到硬件 ID 为“ROOT\ISCSIPRT”的“未知设备”条目。

5.  右键单击“未知设备”并选择“更新驱动程序软件”。

6.  选择“自动搜索更新的驱动程序软件”选项，更新驱动程序。 完成更新后，“未知设备”应更改为“Microsoft iSCSI 发起程序”，如下所示。 

    ![加密](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  转到“任务管理器” -> “服务(本地)” -> “Microsoft iSCSI 发起程序服务”。 

    ![加密](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  右键单击服务，单击“停止”，然后再次单击右键并单击“启动”，以重启 Microsoft iSCSI 发起程序服务。

9.  使用即时还原重试恢复。 

如果恢复仍失败，请重启服务器/客户端。 如果不需要重新启动或者即使在重新启动服务器后恢复仍然失败，请尝试遵循[此文](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)中所列的步骤，从另一台计算机进行恢复

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤
* 详细了解[如何使用 Azure 备份代理备份 Windows Server](tutorial-backup-windows-server-to-azure.md)。
* 如果需要还原备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)一文。
