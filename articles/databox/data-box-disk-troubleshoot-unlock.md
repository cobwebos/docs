---
title: Azure Data Box 磁盘故障排除磁盘解锁问题 |Microsoft Docs
description: 介绍如何排查 Azure Data Box 磁盘中出现的问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148278"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>对 Azure Data Box 磁盘中的磁盘解锁问题进行故障排除

本文适用于 Microsoft Azure Data Box 磁盘，并介绍了用于解决任何问题，使用解锁工具时的工作流。 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Data Box 磁盘解锁工具错误


| 错误消息/工具行为      | 建议                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| 当前的 .NET Framework 不受支持。 支持的版本为 4.5 和以上。<br><br>工具退出并显示一条消息。  | 未安装 .NET 4.5。 在运行 Data Box 磁盘解锁工具的主机上安装 .NET 4.5 或更高版本。                                                                            |
| 无法解锁或验证任何卷。 请联系 Microsoft 支持。  <br><br>工具无法解锁或验证任何已锁定的驱动器。 | 工具无法解锁任何已使用提供的支持密钥锁定的驱动器。 联系 Microsoft 支持部门了解后续步骤。                                                |
| 已解锁并验证以下卷。 <br>卷驱动器号:E:<br>无法使用以下支持密钥解锁任何卷: werwerqomnf、qwerwerqwdfda <br><br>工具解锁了一些驱动器，并列出了成功和失败的驱动器号。| 部分成功。 使用提供的支持密钥无法解锁某些驱动器。 联系 Microsoft 支持部门了解后续步骤。 |
| 找不到锁定的卷。 验证从 Microsoft 收到的磁盘是否已正确连接并处于锁定状态。          | 工具找不到任何已锁定的驱动器。 驱动器已解锁，或检测不到。 确保驱动器已连接并已锁定。                                                           |
| 严重错误:参数无效<br>参数名称: invalid_arg<br>用法:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>示例：DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>示例：DataBoxDiskUnlock /SystemCheck<br>示例：DataBoxDiskUnlock /Help<br><br>/PassKeys:     从 Azure Data Box Disk 顺序中获取此支持密钥。 该支持密钥用于解锁磁盘。<br>/Help:         此选项提供有关 cmdlet 使用情况和示例的帮助。<br>/SystemCheck:  此选项检查你的系统是否满足运行该工具的要求。<br><br>按任意键退出。 | 输入了无效的参数。 只允许参数 /SystemCheck、/PassKey 和 /Help。|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>使用 Windows 客户端时解锁磁盘的问题

本部分详细介绍一些 Windows 客户端使用的数据复制时所面临的 Data Box 磁盘部署期间的最常见问题。

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>问题：无法从 BitLocker 解锁驱动器
 
**原因** 

已在 BitLocker 对话框中使用了密码，并尝试通过 BitLocker 解锁驱动器对话框解锁磁盘。 这不起作用。

**解决方法**

若要解锁 Data Box Disk，需要使用 Data Box Disk解锁工具并从 Azure 门户提供密码。 有关详细信息，请转到[教程：打开包装、连接和解锁 Azure Data Box Disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)。
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>问题：无法解锁或验证某些卷。 请联系 Microsoft 支持。
 
**原因**

你可能会在错误日志中看到以下错误，且无法解锁或验证某些卷。

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
这表示可能在 Windows 客户端上缺少相应版本的 Windows PowerShell。

**解决方法**

可以安装 [Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) 并重试该操作。
 
如果仍无法解锁卷，请从具有 Data Box Disk 解锁工具的文件夹中复制日志，然后[联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何[解决验证问题](data-box-disk-troubleshoot.md)。
