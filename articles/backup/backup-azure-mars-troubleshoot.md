---
title: 排查 Azure 备份代理
description: 安装和注册 Azure 备份代理进行故障排除
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434012"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>排除 Microsoft Azure 恢复服务 (MARS) 代理的问题

本文介绍如何解决的错误可能会看到在配置、 注册、 备份、 期间和还原。

## <a name="basic-troubleshooting"></a>基本故障排除

我们建议在开始解决 Microsoft Azure 恢复服务 (MARS) 代理之前检查以下：

- [请确保是最新的 MARS 代理](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [请确保具有 MARS 代理和 Azure 之间的网络连接](https://aka.ms/AB-A4dp50)。
- 请确保 MARS 运行 （在服务控制台中）。 如果需要重新启动，然后重试该操作。
- [请确保 5%到 10%可用卷空间中可用的暂存文件夹位置](https://aka.ms/AB-AA4dwtt)。
- [检查是否另一个进程或防病毒软件正在干扰 Azure 备份](https://aka.ms/AB-AA4dwtk)。
- 如果计划备份失败，但手动备份的工作原理，请参阅[不按照计划运行备份](https://aka.ms/ScheduledBackupFailManualWorks)。
- 请确保您的操作系统具有最新的更新。
- [确保不受支持的驱动器，并具有不受支持的属性的文件从备份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 请确保受保护的系统上的时钟配置为正确的时区。
- [确保.NET Framework 4.5.2 或更高版本安装在服务器上](https://www.microsoft.com/download/details.aspx?id=30653)。
- 如果想要重新注册到保管库服务器：
  - 请确保在服务器上卸载代理，并且它从门户中删除。
  - 使用第一次用于注册服务器的相同通行短语。
- 对于脱机备份，请确保在开始备份之前，源服务器和副本计算机上安装 Azure PowerShell 3.7.0。
- 如果 Azure 虚拟机上运行备份代理，请参阅[这篇文章](https://aka.ms/AB-AA4dwtr)。

## <a name="invalid-vault-credentials-provided"></a>提供的保管库凭据无效

**错误消息**：提供的保管库凭据无效。 该文件已损坏，或者没有与恢复服务关联的最新凭据。 (ID:34513)

| 原因 | 建议的操作 |
| ---     | ---    |
| **保管库凭据无效** <br/> <br/> 保管库的凭据文件可能已损坏或可能已过期。 （例如，它们可能已下载超过 48 小时之前注册的时间。）| 从 Azure 门户上的恢复服务保管库下载新的凭据。 (请参阅中的步骤 6[下载 MARS 代理](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)部分。)然后执行以下步骤，根据需要： <ul><li> 如果你已安装并注册 MARS，打开 Microsoft Azure 备份代理 MMC 控制台，然后选择**注册服务器**中**操作**窗格来完成与新的注册凭据。 <br/> <li> 如果新安装将失败，请尝试重新安装使用新的凭据。</ul> **注意**：如果已经下载了多个保管库凭据文件，只有最新的文件的有效期为接下来的 48 小时。 我们建议您下载新的保管库凭据文件。
| **代理服务器/防火墙阻止注册** <br/>或 <br/>**没有 internet 连接** <br/><br/> 如果您的计算机或代理服务器具有有限的 internet 连接，并且您不确保所必需的 Url 的访问，则注册将失败。| 执行以下步骤：<br/> <ul><li> 使用你的 IT 团队，以确保系统已建立 internet 连接。<li> 如果没有代理服务器，请确保注册代理时，不选择代理选项。 [检查你的代理设置](#verifying-proxy-settings-for-windows)。<li> 如果你有防火墙/代理服务器，使用网络团队协作，确保这些 Url 和 IP 地址具有访问权限：<br/> <br> **URLs**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 地址**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>请尝试在完成上述故障排除步骤后再次注册。
| **防病毒软件正在阻止注册** | 如果必须在服务器上安装的防病毒软件，将需要排除规则添加到这些文件和文件夹的防病毒扫描： <br/><ui> <li> CBengine.exe <li> CSC.exe<li> Scratch 文件夹中。 其默认位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。 <li> 位于 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin 的 bin 文件夹。

### <a name="additional-recommendations"></a>其他建议
- 转到 C:/Windows/Temp，检查是否存在超过 60,000 或 65,000 个扩展名为 .tmp 的文件。 如果存在，请删除这些文件。
- 确保计算机的日期和时间匹配的本地时区。
- 请确保[这些站点](backup-configure-vault.md#verify-internet-access)添加到受信任网站 Internet Explorer 中。

### <a name="verifying-proxy-settings-for-windows"></a>验证 Windows 代理设置

1. 下载从 PsExec [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec)页。
1. 运行`psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`从提升的命令提示符。

   此命令将打开 Internet Explorer。
1. 转到**工具** > **Internet 选项** > **连接** > **LAN 设置**。
1. 检查系统帐户的代理设置。
1. 如果未配置代理则提供了代理服务器详细信息，请删除详细信息。
1. 如果配置了代理和代理的详细信息不正确，请确保**代理 IP**并**端口**详细信息正确无误。
1. 关闭 Internet Explorer。

## <a name="unable-to-download-vault-credential-file"></a>无法下载保管库凭据文件

| 错误   | 建议的操作 |
| ---     | ---    |
|未能下载保管库凭据文件。 (ID:403) | <ul><li> 请尝试使用不同的浏览器，下载保管库凭据或执行以下步骤： <ul><li> 启动 Internet Explorer。 选择按 F12。 </li><li> 转到**网络**选项卡并清除缓存和 cookie。 </li> <li> 刷新页面。<br></li></ul> <li> 检查是否已禁用或已过期订阅。<br></li> <li> 检查任何防火墙规则是否阻止下载。 <br></li> <li> 请确保你未用完保管库 （每个保管库的 50 个机） 上的限制。<br></li>  <li> 请确保用户具有下载保管库凭据和服务器注册到保管库所需的 Azure 备份权限。 请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](backup-rbac-rs-vault.md)。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份

| 错误  | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份。 (ID:100050) 检查网络设置，并确保你能够连接到 internet。<li>(407) 代理需要身份验证。 |代理正在阻止连接。 |  <ul><li>在 Internet Explorer 中，转到**工具** > **Internet 选项** > **安全** > **Internet**. 选择**自定义级别**向下滚动到**文件下载**部分。 选择“启用”  。<p>您可能还需要添加[Url 和 IP 地址](backup-configure-vault.md#verify-internet-access)到在 Internet Explorer 中受信任的站点。<li>更改设置以使用代理服务器。 然后提供代理服务器详细信息。<li> 如果你的计算机具有有限的 internet 访问权限，请确保代理在计算机上的防火墙设置允许这些[Url 和 IP 地址](backup-configure-vault.md#verify-internet-access)。 <li>如果必须在服务器上安装的防病毒软件，请从防病毒扫描中排除这些文件： <ul><li>CBEngine.exe（而非 dpmra.exe）。<li>CSC.exe（与 .NET Framework 相关）。 没有为每个服务器上安装的.NET Framework 版本 CSC.exe。 排除受影响的服务器上所有版本的.NET Framework 的 CSC.exe 文件。 <li>Scratch 文件夹或缓存位置。 <br>Scratch 文件夹或缓存路径的默认位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。<li>位于 C:\Program Files\Microsoft Azure Recovery Services Agent\Bin 的 bin 文件夹。


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>未能设置安全备份的加密密钥

| 错误 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| <br />未能设置安全备份的加密密钥。 激活未完全成功，但加密密码已保存到以下文件。 |<li>服务器已注册到另一个保管库。<li>在配置期间，通行短语已损坏。| 服务器从保管库取消注册并使用新的通行短语重新注册它。

## <a name="the-activation-did-not-complete-successfully"></a>激活未成功完成

| 错误  | 可能的原因 | 建议的操作 |
|---------|---------|---------|
|<br />激活未成功完成。 由于内部服务错误 [0x1FC07]，当前操作失败。 请过些时间后重试此操作。 如果问题仍然存在，请联系 Microsoft 支持部门。     | <li> Scratch 文件夹位于不具有足够的空间的卷上。 <li> Scratch 文件夹已被错误地移动。 <li> 缺少 OnlineBackup.KEK 文件。         | <li>升级到[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理。<li>将 scratch 文件夹或缓存位置移动到具有介于 5%到 10%的备份数据的总大小之间的可用空间的卷。 若要正确移动缓存位置，请参阅中的步骤[备份文件和文件夹有关的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)。<li> 确保 OnlineBackup.KEK 文件存在。 <br>*Scratch 文件夹或缓存路径的默认位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正确配置加密通行短语

| 错误  | 可能的原因 | 建议的操作 |
|---------|---------|---------|
| <br />错误 34506。 未正确配置此计算机上存储的加密密码。    | <li> Scratch 文件夹位于不具有足够的空间的卷上。 <li> Scratch 文件夹已被错误地移动。 <li> 缺少 OnlineBackup.KEK 文件。        | <li>升级到[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理。<li>将 scratch 文件夹或缓存位置移动到具有介于 5%到 10%的备份数据的总大小之间的可用空间的卷。 若要正确移动缓存位置，请参阅中的步骤[备份文件和文件夹有关的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)。<li> 确保 OnlineBackup.KEK 文件存在。 <br>*Scratch 文件夹或缓存路径的默认位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*。         |


## <a name="backups-dont-run-according-to-schedule"></a>不按照计划运行备份
如果计划的备份不会自动触发，但手动备份正常工作，请尝试以下操作：

- 请确保 Windows Server 备份计划不与 Azure 文件和文件夹备份计划冲突。

- 请确保联机备份状态设置为**启用**。 若要验证的状态，请执行以下步骤：

  1. 在任务计划程序中，展开**Microsoft** ，然后选择**联机备份**。
  1. 双击**Microsoft onlinebackup**并转到**触发器**选项卡。
  1. 检查状态是否设置为**已启用**。 如果不可用，则选择**编辑**，选择**已启用**，然后选择**确定**。

- 确保选择用于运行任务的用户帐户为**系统**或**本地管理员组**在服务器上。 若要验证的用户帐户，请转到**常规**选项卡并选中**安全**选项。

- 请确保在服务器上安装 PowerShell 3.0 或更高版本。 若要检查的 PowerShell 版本，运行以下命令并确认`Major`的版本编号是 3 个或更高版本：

  `$PSVersionTable.PSVersion`

- 请确保此路径的一部分`PSMODULEPATH`环境变量：

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果 PowerShell 执行策略为`LocalMachine`是设置为受限制，将触发备份任务的 PowerShell cmdlet 可能会失败。 在提升模式下检查，并将执行策略设置为运行以下命令`Unrestricted`或`RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- 请确保没有任何缺失或损坏 PowerShell 模块 MSOnlineBackup 文件。 如果有任何缺失或损坏的文件，执行以下步骤：

  1. 从任何具有正常工作的 MARS 代理的计算机，将从 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules 复制 MSOnlineBackup 文件夹。
  1. 有问题的计算机上将粘贴复制的文件在相同的文件夹位置 (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)。

     如果在计算机上已存在 MSOnlineBackup 文件夹，将文件粘贴到其中或替换任何现有文件。


> [!TIP]
> 若要确保更改一致应用，可以执行上述步骤后重新启动服务器。


## <a name="troubleshoot-restore-problems"></a>还原的疑难解答

即使等待几分钟，Azure 备份也可能不会成功装载恢复卷。 和在过程中可能会收到错误消息。 若要开始正常恢复，请执行以下步骤：

1.  如果它已运行几分钟内，取消的安装过程。

2.  检查您是否具有备份代理的最新版本。 若要查看版本中，在**操作**MARS 控制台中，选择窗格**有关 Microsoft Azure 恢复服务代理**。 确认“版本号”等于或高于[此文](https://go.microsoft.com/fwlink/?linkid=229525)中所述的版本。  选择此链接[下载最新版本](https://go.microsoft.com/fwLink/?LinkID=288905)。

3.  转到**设备管理器** > **存储控制器**并找到**Microsoft iSCSI 发起程序**。 如果你找到它，直接转到步骤 7。

4.  如果找不到 Microsoft iSCSI 发起程序服务，尝试查找下的一个条目**设备管理器** > **存储控制器**名为**未知设备**使用硬件 ID **ROOT\ISCSIPRT**。

5.  右键单击**未知的设备**，然后选择**更新驱动程序软件**。

6.  选择“自动搜索更新的驱动程序软件”选项，更新驱动程序  。 此更新应更改**未知的设备**到**Microsoft iSCSI 发起程序**:

    ![Azure 备份设备管理器的屏幕截图，其中突出显示了“存储控制器”](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  转到**任务管理器** > **服务 （本地）**  > **Microsoft iSCSI 发起程序服务**:

    ![Azure 备份任务管理器的屏幕截图，其中突出显示了“服务(本地)”](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  重启 Microsoft iSCSI 发起程序服务。 若要执行此操作，右键单击该服务，并选择**停止**。 然后再次右键单击并选择**启动**。

9.  使用[即时还原](backup-instant-restore-capability.md)重试恢复。

如果恢复仍失败，重新启动服务器或客户端。 如果不想要重新启动，或者如果恢复仍失败即使重新启动服务器，请尝试[从另一台计算机恢复](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。

## <a name="next-steps"></a>后续步骤
* 获取有关详细信息[如何使用 Azure 备份代理备份 Windows Server](tutorial-backup-windows-server-to-azure.md)。
* 如果需要还原的备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)。
