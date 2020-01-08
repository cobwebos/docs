---
title: 排查 Azure 备份代理问题
description: 本文介绍如何排查 Azure 备份代理的安装和注册问题。
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 06c741547e0206059195f481ed29dc8e69aa4dd3
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665320"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>排查 Microsoft Azure 恢复服务（MARS）代理问题

本文介绍如何解决配置、注册、备份和还原期间可能出现的错误。

## <a name="basic-troubleshooting"></a>基本故障排除

建议你在开始诊断 Microsoft Azure 恢复服务（MARS）代理之前检查以下内容：

- [确保 MARS 代理是最新的](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [确保 MARS 代理与 Azure 之间已建立网络连接](https://aka.ms/AB-A4dp50)。
- 确保 MARS 正在运行（在服务控制台中）。 如果需要，请重新启动，然后重试该操作。
- [确保暂存文件夹位置中有5% 到10% 的可用卷空间](https://aka.ms/AB-AA4dwtt)。
- [检查其他进程或防病毒软件是否正在干扰 Azure 备份](https://aka.ms/AB-AA4dwtk)。
- 如果计划的备份失败但手动备份工作，请参阅[根据计划不运行备份](https://aka.ms/ScheduledBackupFailManualWorks)。
- 确保操作系统具有最新更新。
- [确保不受支持的驱动器和具有不支持的属性的文件已从备份中排除](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 确保将受保护系统上的时钟配置为正确的时区。
- [请确保在服务器上安装了 .NET Framework 4.5.2 或更高版本](https://www.microsoft.com/download/details.aspx?id=30653)。
- 如果你正在尝试将服务器重新注册到保管库：
  - 请确保在服务器上卸载代理，并将其从门户中删除。
  - 使用最初用于注册服务器的相同密码。
- 对于脱机备份，请确保在开始备份之前在源计算机和副本计算机上都安装 Azure PowerShell 3.7.0。
- 如果备份代理在 Azure 虚拟机上运行，请参阅[此文](https://aka.ms/AB-AA4dwtr)。

## <a name="invalid-vault-credentials-provided"></a>提供的保管库凭据无效

**错误消息**：提供的保管库凭据无效。 该文件已损坏，或者没有与恢复服务关联的最新凭据。 （ID：34513）

| 原因 | 建议的操作 |
| ---     | ---    |
| **保管库凭据无效** <br/> <br/> 保管库凭据文件可能已损坏或可能已过期。 （例如，在注册时间之前，它们可能下载了超过48小时。）| 在 Azure 门户上从恢复服务保管库下载新凭据。 （请参阅[下载 MARS agent](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent)部分中的步骤6。）然后，根据需要执行以下步骤： <ul><li> 如果已安装并注册了 MARS，请打开 Microsoft Azure 备份代理 MMC 控制台，然后在 "**操作**" 窗格中选择 "**注册服务器**"，以通过新凭据完成注册。 <br/> <li> 如果新的安装失败，请尝试重新安装新的凭据。</ul> **注意**：如果已下载多个保管库凭据文件，则在接下来的48小时内仅可使用最新的文件。 建议下载新的保管库凭据文件。
| **代理服务器/防火墙正在阻止注册** <br/>或 <br/>**无 internet 连接** <br/><br/> 如果计算机或代理服务器的 internet 连接受到限制，并且不确保访问必要的 Url，则注册将失败。| 执行以下步骤：<br/> <ul><li> 与你的 IT 团队合作，确保系统具有 internet 连接。<li> 如果没有代理服务器，请确保在注册代理时未选择 "代理" 选项。 [检查代理设置](#verifying-proxy-settings-for-windows)。<li> 如果你有防火墙/代理服务器，请与你的网络团队合作，以确保这些 Url 和 IP 地址具有访问权限：<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP 地址**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>完成上述故障排除步骤后，请尝试再次注册。
| **防病毒软件正在阻止注册** | 如果在服务器上安装了防病毒软件，请在防病毒扫描中添加这些文件和文件夹所需的排除规则： <br/><ul> <li> CBengine.exe <li> CSC.exe<li> 暂存文件夹。 其默认位置为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch。 <li> C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 中的 bin 文件夹

### <a name="additional-recommendations"></a>更多建议

- 转到 C:/Windows/Temp，检查是否存在超过 60,000 或 65,000 个扩展名为 .tmp 的文件。 如果存在，请删除这些文件。
- 确保计算机的日期和时间与本地时区匹配。
- 确保将[这些站点](backup-configure-vault.md#verify-internet-access)添加到 Internet Explorer 中的受信任站点。

### <a name="verifying-proxy-settings-for-windows"></a>验证 Windows 的代理设置

1. 从 " [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) " 页下载 PsExec。
1. 在提升的命令提示符下运行 `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`。

   此命令将打开 Internet Explorer。
1. 请参阅 "**工具**" > **Internet 选项**" > **连接** > **LAN 设置**"。
1. 检查系统帐户的代理设置。
1. 如果未配置代理并且提供了代理详细信息，请删除详细信息。
1. 如果配置了代理并且代理详细信息不正确，请确保**代理 IP**和**端口**详细信息正确。
1. 关闭 Internet Explorer。

## <a name="unable-to-download-vault-credential-file"></a>无法下载保管库凭据文件

| 错误   | 建议的操作 |
| ---     | ---    |
|未能下载保管库凭据文件。 （ID：403） | <ul><li> 尝试使用不同的浏览器下载保管库凭据，或执行以下步骤： <ul><li> 启动 Internet Explorer。 选择 "F12"。 </li><li> 请在 "**网络**" 选项卡上，清除缓存和 cookie。 </li> <li> 刷新页面。<br></li></ul> <li> 检查订阅是否已禁用/已过期。<br></li> <li> 检查是否有任何防火墙规则正在阻止下载。 <br></li> <li> 确保你尚未用尽保管库的限制（每个保管库50台计算机）。<br></li>  <li> 确保用户具有下载保管库凭据所需的 Azure 备份权限，并向保管库注册服务器。 请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](backup-rbac-rs-vault.md)。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份

| 错误  | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份。 （ID：100050）检查你的网络设置，并确保你能够连接到 internet。<li>(407) 需要代理身份验证。 |代理正在阻止连接。 |  <ul><li>在 Internet Explorer 中，依次访问 "**工具**" > **internet 选项**" > **Security** > **internet**"。 选择 "**自定义级别**"，并向下滚动到 "**文件下载**" 部分。 选择“启用”。<p>你可能还需要在 Internet Explorer 中将[url 和 IP 地址](backup-configure-vault.md#verify-internet-access)添加到受信任的站点。<li>更改设置以使用代理服务器。 然后提供代理服务器详细信息。<li> 如果计算机具有有限的 internet 访问权限，请确保计算机或代理上的防火墙设置允许这些[url 和 IP 地址](backup-configure-vault.md#verify-internet-access)。 <li>如果在服务器上安装了防病毒软件，请从防病毒扫描中排除以下文件： <ul><li>CBEngine.exe（而非 dpmra.exe）。<li>CSC.exe（与 .NET Framework 相关）。 服务器上安装的每个 .NET Framework 版本都有一个 CSC。 在受影响的服务器上的所有版本的 .NET Framework 中排除 CSC 文件。 <li>暂存文件夹或缓存位置。 <br>暂存文件夹的默认位置或缓存路径为 "C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch."<li>C:\Program Files\Microsoft Azure Recovery Services Agent\Bin. 中的 bin 文件夹

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>未能设置安全备份的加密密钥

| 错误 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| <br />未能设置安全备份的加密密钥。 激活未完全成功，但加密密码已保存到以下文件。 |<li>服务器已注册到另一个保管库。<li>在配置期间，通行短语已损坏。| 从保管库中取消注册服务器，并使用新密码重新注册。

## <a name="the-activation-did-not-complete-successfully"></a>激活未成功完成

| 错误  | 可能的原因 | 建议的操作 |
|---------|---------|---------|
|<br />激活未成功完成。 由于内部服务错误 [0x1FC07]，当前操作失败。 稍后重试操作。 如果该问题仍然存在，请联系 Microsoft 支持部门。     | <li> 暂存文件夹位于空间不足的卷上。 <li> 暂存文件夹已被错误地移动。 <li> 缺少 OnlineBackup.KEK 文件。         | <li>升级到[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理。<li>将暂存文件夹或缓存位置移到可用空间介于5% 到备份数据总大小的10% 之间的卷。 若要正确移动缓存位置，请参阅[有关备份文件和文件夹的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)。<li> 确保 OnlineBackup.KEK 文件存在。 <br>*暂存文件夹的默认位置或缓存路径为 "C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch"* 。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>未正确配置加密通行短语

| 错误  | 可能的原因 | 建议的操作 |
|---------|---------|---------|
| <br />错误 34506。 此计算机上存储的加密密码配置不正确。    | <li> 暂存文件夹位于空间不足的卷上。 <li> 暂存文件夹已被错误地移动。 <li> 缺少 OnlineBackup.KEK 文件。        | <li>升级到[最新版本](https://aka.ms/azurebackup_agent)的 MARS 代理。<li>将暂存文件夹或缓存位置移到可用空间介于5% 到备份数据总大小的10% 之间的卷。 若要正确移动缓存位置，请参阅[有关备份文件和文件夹的常见问题](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)。<li> 确保 OnlineBackup.KEK 文件存在。 <br>*暂存文件夹的默认位置或缓存路径为 "C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch"* 。         |

## <a name="backups-dont-run-according-to-schedule"></a>不会根据计划运行备份

如果未自动触发计划的备份，但手动备份正常工作，请尝试以下操作：

- 确保 Windows Server backup 计划不会与 Azure 文件和文件夹备份计划冲突。

- 确保 "联机备份状态" 设置为 "**启用**"。 若要验证状态，请执行以下步骤：

  1. 在任务计划程序中，展开 " **Microsoft** "，然后选择 "**联机备份**"。
  1. 双击 "**缺少 onlinebackup.kek** "，并单击 "**触发器**" 选项卡。
  1. 检查状态是否设置为 "**已启用**"。 如果没有，请选择 "**编辑**"，选择 "**已启用**"，然后选择 **"确定"** 。

- 确保选择用于运行任务的用户帐户是服务器上的系统**管理员或本地管理员组**。 若要验证用户帐户，请在 "**常规**" 选项卡上检查**安全**选项。

- 请确保在服务器上安装了 PowerShell 3.0 或更高版本。 若要检查 PowerShell 版本，请运行此命令并验证 `Major` 版本号是否为3或更高版本：

  `$PSVersionTable.PSVersion`

- 请确保此路径是 `PSMODULEPATH` 环境变量的一部分：

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- 如果 `LocalMachine` 的 PowerShell 执行策略设置为 `restricted`，则触发备份任务的 PowerShell cmdlet 可能会失败。 在提升模式下运行这些命令可检查并将执行策略设置为 `Unrestricted` 或 `RemoteSigned`：

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- 请确保 PowerShell 模块 MSOnlineBackup 文件没有丢失或损坏。 如果文件丢失或损坏，请执行以下步骤：

  1. 从具有正常运行的 MARS 代理的任何计算机中，复制 MSOnlineBackup 文件夹 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules。
  1. 在有问题的计算机上，将复制的文件粘贴到同一文件夹位置（C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules）。

     如果计算机上已有 MSOnlineBackup 文件夹，请将文件粘贴到其中或替换任何现有文件。

> [!TIP]
> 若要确保按一致性应用更改，请在执行上述步骤后重新启动服务器。

## <a name="troubleshoot-restore-problems"></a>还原问题疑难解答

即使等待几分钟，Azure 备份也可能不会成功装载恢复卷。 在此过程中，您可能会收到错误消息。 若要开始正常恢复，请执行以下步骤：

1. 如果安装过程已运行几分钟，请取消该过程。

2. 检查是否有最新版本的备份代理。 若要检查版本，请在 MARS 控制台的 "**操作**" 窗格上，选择 "**关于 Microsoft Azure 恢复服务代理**"。 确认“版本号”等于或高于[此文](https://go.microsoft.com/fwlink/?linkid=229525)中所述的版本。 选择此链接以[下载最新版本](https://go.microsoft.com/fwLink/?LinkID=288905)。

3. 请参阅**设备管理器** > **存储控制器**"，并找到" **Microsoft iSCSI 发起程序**"。 如果找到它，请直接跳到步骤7。

4. 如果找不到 "Microsoft iSCSI 发起程序" 服务，请尝试查找**设备管理器** > 名为 "**未知设备**"、"硬件 ID **ROOT\ISCSIPRT**" 的**存储控制器**下的条目。

5. 右键单击 "**未知设备**"，然后选择 "**更新驱动程序软件**"。

6. 选择“自动搜索更新的驱动程序软件”选项，更新驱动程序。 此更新应将**未知设备**更改为**Microsoft iSCSI 发起程序**：

    ![Azure 备份设备管理器的屏幕截图，其中突出显示了“存储控制器”](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. 请参阅**Microsoft ISCSI 发起程序服务** > 的**任务管理器** > **服务（本地）** ：

    ![Azure 备份任务管理器的屏幕截图，其中突出显示了“服务(本地)”](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. 重启 Microsoft iSCSI 发起程序服务。 为此，请右键单击该服务，然后选择 "**停止**"。 然后再次右键单击，然后选择 "**启动**"。

9. 使用[“即时还原”](backup-instant-restore-capability.md)重试恢复。

如果恢复仍失败，请重新启动服务器或客户端。 如果你不想重新启动，或者即使在重启服务器后恢复仍然失败，请尝试[从另一台计算机进行恢复](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

## <a name="troubleshoot-cache-problems"></a>排查缓存问题

如果缓存文件夹（也称为暂存文件夹）配置不正确、缺少必备项或具有受限访问权限，则备份操作可能会失败。

### <a name="prerequisites"></a>必备组件

要使 MARS 代理操作成功，缓存文件夹需要符合以下要求：

- [确保暂存文件夹位置中有5% 到10% 的可用卷空间](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [确保暂存文件夹位置有效且可访问](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [请确保缓存文件夹上的文件属性受支持](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [确保分配的卷影副本存储空间足以满足备份过程](#increase-shadow-copy-storage)
- [确保没有其他进程（例如防病毒软件）限制对缓存文件夹的访问](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>增加卷影副本存储

如果用于保护数据源的卷影副本存储空间不足，则备份操作可能会失败。 若要解决此问题，请使用 vssadmin 增加受保护卷上的卷影副本存储空间，如下所示：

- 在提升的命令提示符下检查当前的影子存储空间：<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 使用以下命令增加卷影存储空间：<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>阻止访问缓存文件夹的另一个进程或防病毒软件

如果在服务器上安装了防病毒软件，请在防病毒扫描中添加这些文件和文件夹所需的排除规则：  

- 暂存文件夹。 其默认位置为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- C:\Program Files\Microsoft Azure Recovery Services Agent\Bin 中的 bin 文件夹
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>常见问题

本部分介绍使用 MARS 代理时遇到的常见错误。

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

错误消息 | 建议的操作 |
-- | --
Microsoft Azure 恢复服务代理无法访问存储在暂存位置的备份校验和 | 若要解决此问题，请执行以下，然后重新启动服务器 <br/> - [检查是否存在防病毒或其他进程锁定暂存位置文件](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [检查暂存位置是否有效并可由 mars 代理访问。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

错误消息 | 建议的操作 |
-- | --
Microsoft Azure 恢复服务代理无法访问暂存位置，因此无法初始化 VHD | 若要解决此问题，请执行以下，然后重新启动服务器 <br/> - [检查是否存在防病毒或其他进程锁定暂存位置文件](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [检查暂存位置是否有效并可由 mars 代理访问。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

错误消息 | 建议的操作 |
-- | --
备份失败，因为暂存文件夹所在的卷中有足够的存储空间 | 若要解决此问题，请验证以下步骤，然后重试该操作：<br/>- [确保 MARS 代理最新](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [验证和解决影响备份暂存空间的存储问题](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

错误消息 | 建议的操作 |
-- | --
找不到文件中的更改。 这可能是由于各种原因。 请重试该操作 | 若要解决此问题，请验证以下步骤，然后重试该操作：<br/> - [确保 MARS 代理最新](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [验证和解决影响备份暂存空间的存储问题](#prerequisites)

## <a name="next-steps"></a>后续步骤

- 详细了解[如何通过 Azure 备份代理备份 Windows Server](tutorial-backup-windows-server-to-azure.md)。
- 如果需要还原备份，请参阅将[文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)。
