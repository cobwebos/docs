---
title: 使用 PowerShell 为 Azure Stack 启用备份 | Microsoft Docs
description: 使用 Windows PowerShell 启用基础结构备份服务，以便出现故障时可以还原 Azure Stack。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139609"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 为 Azure Stack 启用备份

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Windows PowerShell 启用基础结构备份服务，以便定期备份以下内容：
 - 内部标识服务和根证书
 - 用户计划、产品/服务、订阅
 - KeyVault 机密
 - 用户 RBAC 角色和策略

可以访问 PowerShell cmdlet 以启用备份、启动备份，以及通过操作员管理终结点获取备份信息。

## <a name="prepare-powershell-environment"></a>准备 PowerShell 环境

有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack，请参阅[配置操作员环境并登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供备份共享、凭据和加密密钥以启用备份

在同一 PowerShell 会话中，通过添加环境变量编辑以下 PowerShell 脚本。 运行更新的脚本，以向基础结构备份服务提供备份共享、凭据和加密密钥。

| 变量        | 说明   |
|---              |---                                        |
| $username       | 使用共享驱动器位置具有足够访问权限的域和用户名输入**用户名**，以便读取和写入文件。 例如，`Contoso\backupshareuser`。 |
| $password       | 键入用户的**密码**。 |
| $sharepath      | 键入**备份存储位置**的路径。 必须使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 若要确保备份数据的可用性，设备应放置在单独的位置。 |
| $frequencyInHours | 以小时为单位的频率确定何种频率创建备份。 默认值为 12。 计划程序支持最大为 12，至少为 4。|
| $retentionPeriodInDays | 以天为单位的保持期确定多少天的备份将保留在外部位置上。 默认值为 7。 计划程序支持最多 14 个和最小为 2。 从外部位置会自动删除早于保持期的备份。|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>确认备份设置

在同一 PowerShell 会话中，运行以下命令：

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

结果应如以下示例输出所示：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>更新备份设置
在同一 PowerShell 会话中，可以为保持期和备份的频率更新默认值。 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

结果应如以下示例输出所示：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>后续步骤

 - 了解如何运行备份，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。  
 - 了解如何验证备份是否已运行，请参阅[在管理门户中确认已完成的备份](azure-stack-backup-back-up-azure-stack.md )。
