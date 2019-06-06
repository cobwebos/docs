---
title: 排查 Azure 备份代理问题
description: 排查 Azure 备份代理的安装和注册问题
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: d8a1d261808eb8f97d1e0dab78b767b37ae6802f
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743137"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>排查 Microsoft Azure 恢复服务 (MARS) 代理问题

下面介绍如何解决在配置、注册、备份和还原期间出现的错误。

## <a name="basic-troubleshooting"></a>基本故障排除

我们建议你执行以下验证，在开始操作之前进行故障排除 Microsoft Azure 恢复服务 (MARS) 代理：

- [请确保是最新的 Microsoft Azure 恢复服务 (MARS) 代理](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [确保在 MARS 代理和 Azure 之间存在网络连接](https://aka.ms/AB-A4dp50)
- 确保 Microsoft Azure 恢复服务正在运行（在服务控制台中）。 根据需要重启，然后重试此操作
- [确保在暂存文件夹位置有 5-10% 的可用卷空间](https://aka.ms/AB-AA4dwtt)
- [检查其他进程或防病毒软件是否正在干扰 Azure 备份](https://aka.ms/AB-AA4dwtk)
- [计划的备份失败，但手动备份成功](https://aka.ms/ScheduledBackupFailManualWorks)
- 确保 OS 有最新更新
- [确保不受支持的驱动器，并具有不受支持的属性的文件从备份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- 请确保受保护系统上的系统时钟配置为正确时区  <br>
- [确保服务器至少具有 .Net Framework 版本 4.5.2 和更高版本](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- 如果尝试**将服务器重新注册**到保管库，则请执行以下操作： <br>
  - 确保在服务器上卸载代理并将其从门户中删除 <br>
  - 使用的密码正是一开始用于注册服务器的 <br>
- 如果脱机备份出现，请确保在开始脱机备份操作之前，源和副本计算机上安装 Azure PowerShell 版本 3.7.0
- [Azure 虚拟机上运行备份代理时的考虑事项](https://aka.ms/AB-AA4dwtr)

## <a name="invalid-vault-credentials-provided"></a>提供的保管库凭据无效

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** </br> *提供的保管库凭据无效。该文件已损坏，或者没有与恢复服务关联的最新凭据。(ID:34513)* | <ul><li> 保管库凭据无效（也就是说，它们是在注册之前的 48 小时前下载的）。<li>MARS 代理无法将文件下载到 Windows Temp 目录。 <li>保管库凭据位于某个网络位置。 <li>TLS 1.0 已禁用<li> 所配置的某个代理服务器正在阻止连接。 <br> |  <ul><li>下载新的保管库凭据。（**注意**：如果以前下载了多个保管库凭据文件，则只有最新下载的文件在 48 小时内有效。） <li>启动“IE” > “设置” > “Internet 选项” > “安全” > “Internet”      。 接下来，选择“自定义级别”，并滚动直至看到“文件下载”部分。  然后选择“启用”。 <li>可能还需要将这些站点添加到 IE 的[受信任的站点](https://docs.microsoft.com/azure/backup/backup-configure-vault#verify-internet-access)中。<li>更改设置以使用代理服务器。 然后提供代理服务器详细信息。 <li> 使日期和时间与你的计算机匹配。<li>如果收到的错误指出不允许下载文件，则可能是因为 C:/Windows/Temp 目录中存在大量文件。<li>转到 C:/Windows/Temp，检查是否存在超过 60,000 或 65,000 个扩展名为 .tmp 的文件。 如果存在，请删除这些文件。<li>确保已安装了 .NET Framework 4.6.2。 <li>如果由于 PCI 符合性而禁用了 TLS 1.0，请参阅此[故障排除页面](https://support.microsoft.com/help/4022913)。 <li>如果服务器中安装了防病毒软件，请从防病毒软件扫描中排除以下文件： <ul><li>CBengine.exe<li>与 .NET Framework 相关的 CSC.exe。 服务器上安装的每个 .NET 版本都有一个 CSC.exe。 排除受影响服务器上的所有 .NET Framework 版本绑定的 CSC.exe 文件。 <li>Scratch 文件夹或缓存位置。 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。 <br><li>bin 文件夹 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin

## <a name="unable-to-download-vault-credential-file"></a>无法下载保管库凭据文件

| 错误详细信息 | 建议的操作 |
| ---     | ---    |
|未能下载保管库凭据文件。 (ID:403) | <ul><li> 尝试使用其他浏览器下载保管库凭据，或执行以下步骤： <ul><li> 启动 IE，按 F12 键。 </li><li> 转到“网络”选项卡，清除 IE 缓存和 cookie  </li> <li> 刷新页面<br>（或者）</li></ul> <li> 检查订阅是否被禁用/已过期<br>（或者）</li> <li> 检查是否有任何防火墙规则阻止下载保管库凭据文件 <br>（或者）</li> <li> 确保未用完保管库的限额（每个保管库 50 台计算机）<br>（或者）</li>  <li> 确保用户具有下载保管库凭据所需的 Azure 备份权限并向保管库注册了服务器，请参阅[此文](backup-rbac-rs-vault.md)</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** <br /><ol><li>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份。 *(ID:100050)请检查网络设置，并确保能够连接到 Internet*<li>*(407) 需要代理身份验证* |代理正在阻止连接。 |  <ul><li>启动“IE” > “设置” > “Internet 选项” > “安全” > “Internet”      。 然后选择“自定义级别”，并滚动直至看到“文件下载”部分。  选择“启用”  。<li>可能还需要将这些站点添加到 IE 的[受信任的站点](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins)中。<li>更改设置以使用代理服务器。 然后提供代理服务器详细信息。<li> 如果你的计算机具有有限的 internet 访问权限，请确保代理在计算机上的防火墙设置允许这些[Url](backup-configure-vault.md#verify-internet-access)并[IP 地址](backup-configure-vault.md#verify-internet-access)。 <li>如果服务器中安装了防病毒软件，请从防病毒软件扫描中排除以下文件。 <ul><li>CBEngine.exe（而非 dpmra.exe）。<li>CSC.exe（与 .NET Framework 相关）。 服务器上安装的每个 .NET 版本都有一个 CSC.exe。 请排除受影响服务器上的所有 .NET Framework 版本绑定的 CSC.exe 文件。 <li>Scratch 文件夹或缓存位置。 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。 <li>bin 文件夹 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin



## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>未能设置安全备份的加密密钥

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** <br />未能设置安全备份的加密密钥。  激活未完全成功，但是加密通行短语已保存到以下文件中。 |<li>服务器已注册到另一个保管库。<li>在配置期间，通行短语已损坏。| 从该保管库中取消注册服务器，然后使用新通行短语重新注册。

## <a name="the-activation-did-not-complete-successfully"></a>激活未成功完成

| 错误详细信息 | 可能的原因 | 建议的操作 |
|---------|---------|---------|
|**错误** <br />*激活未成功完成。由于内部服务错误 [0x1FC07]，当前操作失败。稍后重试操作。如果该问题仍然存在，请联系 Microsoft 支持部门*     | <li> Scratch 文件夹位于空间不足的卷上。 <li> 已错误地将 Scratch 文件夹移到另一位置。 <li> 缺少 OnlineBackup.KEK 文件。         | <li>升级到[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理。<li>将 scratch 文件夹或缓存位置移到可用空间相当于备份数据总大小 5-10% 的卷。 若要正确地移动缓存位置，请参阅[有关 Azure 备份代理的问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步骤。<li> 确保 OnlineBackup.KEK 文件存在。 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。         |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正确配置加密通行短语

| 错误详细信息 | 可能的原因 | 建议的操作 |
|---------|---------|---------|
|**错误** <br />错误 34506。  未在此计算机上正确配置存储的加密通行短语。    | <li> Scratch 文件夹位于空间不足的卷上。 <li> 已错误地将 Scratch 文件夹移到另一位置。 <li> 缺少 OnlineBackup.KEK 文件。        | <li>升级到[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理。<li>将 scratch 文件夹或缓存位置移到可用空间相当于备份数据总大小 5-10% 的卷。 若要正确地移动缓存位置，请参阅[有关 Azure 备份代理的问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中的步骤。<li> 确保 OnlineBackup.KEK 文件存在。 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。          |


## <a name="backups-dont-run-according-to-the-schedule"></a>备份未按计划运行
如果计划的备份未自动触发，而手动备份却能正常进行，请尝试以下操作：

- 确保 Windows Server 备份计划与 Azure 文件和文件夹备份计划不冲突。

- 请确保联机备份状态设置为**启用**。 若要验证状态执行如下：

  - 转到“控制面板” > “管理工具” > “任务计划程序”。   
    - 展开“Microsoft”并选择“联机备份”。  
  - 双击“Microsoft-OnlineBackup”，然后转到“触发器”选项卡。  
  - 验证状态是否设置为**已启用**。 如果不是，请选择“编辑”，并选中“已启用”复选框，然后单击“确定”    。

- 确保选择用于运行任务的用户帐户为**系统**或**本地管理员组**在服务器上。 若要验证的用户帐户，请转到**常规**选项卡并选中**安全选项**。

- 查看服务器上是否已安装 PowerShell 3.0 或更高版本。 若要检查 PowerShell 版本，请运行以下命令，并确认 *Major* 版本号是等于或大于 3。

  `$PSVersionTable.PSVersion`

- 查看 *PSMODULEPATH* 环境变量中包含以下路径。

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果 *LocalMachine* 的 PowerShell 执行策略设置为 restricted，则触发备份任务的 PowerShell cmdlet 可能失败。 以权限提升的模式运行以下命令，将执行策略设置为 *Unrestricted* 或 *RemoteSigned* 并检查。

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 确保备份代理安装完成后重新启动服务器

- 确保没有缺失或已损坏**PowerShell**模块**MSonlineBackup**。 如果有任何缺失或损坏的文件，若要解决问题，请执行如下：

  - 从另一台计算机 (Windows 2008 R2) 具有 MARS 代理正常运行，复制从 MSOnlineBackup 文件夹 *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* 路径。
  - 中的有问题计算机的相同路径粘贴这 *(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)* 。
  - 如果 **MSOnlineBackup** 文件夹已存在在计算机中粘贴/替换其内部的内容文件。


> [!TIP]
> 为确保一致地应用所做的更改，请在执行上述步骤后重新启动服务器。


## <a name="troubleshoot-restore-issues"></a>排查还原问题

即使等待几分钟，Azure 备份也可能不会成功装载恢复卷。 在此过程中，还可能出现错误消息。 若要开始正常恢复，请执行以下步骤：

1.  取消正在进行的安装过程（如果它已运行了几分钟）。

2.  查看是否使用了最新版本的备份代理。 若要查看版本，请在 MARS 控制台的“操作”窗格中，选择“关于 Microsoft Azure 恢复服务代理”。   确认“版本号”等于或高于[此文](https://go.microsoft.com/fwlink/?linkid=229525)中所述的版本。  可在[此处](https://go.microsoft.com/fwLink/?LinkID=288905)下载最新版本。

3.  转到“设备管理器” > “存储控制器”，并找到“Microsoft iSCSI 发起程序”    。 如果可以找到它，请直接转到步骤 7。

4.  如果找不到 Microsoft iSCSI 发起程序服务，请尝试在“设备管理器” > “存储控制器”下找到硬件 ID 为“ROOT\ISCSIPRT”的“未知设备”条目     。

5.  右键单击“未知设备”并选择“更新驱动程序软件”   。

6.  选择“自动搜索更新的驱动程序软件”选项，更新驱动程序  。 完成更新后，“未知设备”应更改为“Microsoft iSCSI 发起程序”，如下所示   。

    ![Azure 备份设备管理器的屏幕截图，其中突出显示了“存储控制器”](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  转到“任务管理器” > “服务(本地)” > “Microsoft iSCSI 发起程序服务”    。

    ![Azure 备份任务管理器的屏幕截图，其中突出显示了“服务(本地)”](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  重启 Microsoft iSCSI 发起程序服务。 为此，请右键单击该服务，选择“停止”，再次右键单击，然后选择“启动”。  

9.  使用[**即时还原**](backup-instant-restore-capability.md)重试恢复。

如果恢复仍然失败，请重新启动服务器或客户端。 如果不想要重新启动，或者即使重新启动服务器，恢复也仍然失败，请尝试从另一台计算机恢复。 遵循[此文](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)中的步骤。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤
* 详细了解[如何使用 Azure 备份代理备份 Windows Server](tutorial-backup-windows-server-to-azure.md)。
* 如果需要还原备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)一文。
