---
title: "排查 Azure 备份代理（MARS 代理）问题 | Microsoft Docs"
description: "排查 Azure 备份代理的安装和注册问题"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: d05b951277515f3100aefcfb06a17b661267cb37
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>排查 Azure 备份代理配置/注册问题
## <a name="recommended-steps"></a>建议的步骤
请参阅下面指定的建议操作来解决配置或注册 Azure 备份代理期间出现的相应错误。

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>错误：提供的保管库凭据无效。 该文件已损坏，或者没有与恢复服务关联的最新凭据。
| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** </br> *提供的保管库凭据无效。该文件已损坏，或者没有与恢复服务关联的最新凭据。(ID: 34513)* | <ol><li> 保管库凭据无效（例如， 它们是在注册之前的 48 以前下载的）。<li>   Azure 备份代理无法将某个临时文件下载到 Windows 的 Temp 文件夹。 <li>保管库凭据在网络位置上。 <li>TLS 1.0 已禁用<li> 配置的代理服务器正在阻止连接 <br> |  <ol><li>下载新的保管库凭据<li>转到“Internet 选项”>“安全性”>“Internet”，单击“自定义级别”，滚动到“文件下载”部分，选择“启用”。<li>可能还需要将站点添加到[受信任的站点](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>更改设置以使用代理服务器并提供代理详细信息 <li> 使“日期和时间”(UTC) 与计算机匹配<li>转到 C:/Windows/Temp，检查是否存在 60,000 或 65,000 个以上的文件（扩展名为 .tmp），并删除这些文件。<li>可以在服务器上运行 SDP 包进行测试。 如果收到的错误指出不允许文件下载，则可以合理地确定 C:/Windows/Temp 目录中存在大量文件。<li>确保已安装 .NET Framework 4.6.2。 <li>如果由于 PCI 符合性禁用了 TLS 1.0，请参阅此[故障排除链接](https://support.microsoft.com/help/4022913)。 <li>如果服务器中安装了防病毒软件，请从防病毒软件扫描中排除以下文件。 <ol><li>CBengine.exe<li>CSC.exe（与 .NET Framework 相关。 服务器上安装的每个 .NET 版本都有一个 CSC.exe。 请排除与受影响服务器上的所有 .NET Framework 版本相关的所有 CSC.exe 文件。） <li>Scratch 文件夹或缓存位置。 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch

## <a name="error-the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>错误: Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份。

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |
| **错误** </br><ol><li>Microsoft Azure 恢复服务代理无法连接到 Microsoft Azure 备份。(ID: 100050) 请检查网络设置，并确保能够连接到 Internet<li>(407) 需要代理身份验证 |代理正在阻止连接* |  <ol><li>转到“Internet 选项”>“安全性”>“Internet”，单击“自定义级别”，滚动到“文件下载”部分，选择“启用”。<li>可能还需要将站点添加到[受信任的站点](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>更改设置以使用代理服务器并提供代理详细信息 <li>如果服务器中安装了防病毒软件，请从防病毒软件扫描中排除以下文件。 <ol><li>CBengine.exe<li>（不是 dpmra.exe）<li>CSC.exe（与 .NET Framework 相关。 服务器上安装的每个 .NET 版本都有一个 CSC.exe。 请排除与受影响服务器上的所有 .NET Framework 版本相关的所有 CSC.exe 文件。） <li>Scratch 文件夹或缓存位置 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch

## <a name="error-failed-to-set-the-encryption-key-for-secure-backups"></a>错误: 未能设置安全备份的加密密钥

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |      
| **错误** </br>*未能设置安全备份的加密密钥。由于内部服务错误“输入无效错误”，当前操作失败。请稍后重试操作。如果该问题仍然存在，请联系 Microsoft 支持部门* |服务器已注册到另一个保管库| 从该保管库中取消注册服务器，然后再次注册。

## <a name="error-the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>错误: 激活未成功完成。 由于内部服务错误 [0x1FC07]，当前操作失败

| 错误详细信息 | 可能的原因 | 建议的操作 |
| ---     | ---     | ---    |          
| **错误** </br><ol><li>*激活未成功完成。由于内部服务错误 [0x1FC07]，当前操作失败。请稍后重试操作。如果该问题仍然存在，请联系 Microsoft 支持部门* <li>错误 34506。未在此计算机上正确配置存储的加密密码 | <li> Scratch 文件夹位于空间不足的卷上。 <li> 已错误地将 Scratch 文件夹移到另一位置 <li> 缺少 OnlineBackup.KEK 文件 | <li>将 scratch 文件夹或缓存位置移到可用空间相当于备份数据总大小 5-10% 的卷。 请参阅[此文](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中所述的步骤正确移动缓存位置。<li> 确保 OnlineBackup.KEK 文件存在 <br>scratch 文件夹的默认位置或缓存位置路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤
* 获取有关[使用 Azure 备份代理备份 Windows Server](tutorial-backup-windows-server-to-azure.md) 的更多详细信息。
* 如果需要还原备份，请参阅[将文件还原到 Windows 计算机](backup-azure-restore-windows-server.md)一文。
