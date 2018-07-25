---
title: 使用 PowerShell 为 Azure Stack 启用备份 | Microsoft Docs
description: 使用 Windows PowerShell 启用基础结构备份服务，以便出现故障时可以还原 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242948"
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
| $key            | 类型**加密密钥**用来加密每个备份。 |
| $password       | 键入用户的**密码**。 |
| $sharepath      | 键入**备份存储位置**的路径。 必须使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 若要确保备份数据的可用性，设备应放置在单独的位置。 |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>确认备份设置

在同一 PowerShell 会话中，运行以下命令：

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

结果应类似于以下输出：

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>后续步骤

 - 了解如何运行备份，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。  
 - 了解如何验证备份是否已运行，请参阅[在管理门户中确认已完成的备份](azure-stack-backup-back-up-azure-stack.md )。