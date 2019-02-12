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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 280a811e943c2e81a96875e3c8ba8efdb86fbf2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004819"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 为 Azure Stack 启用备份

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

使用 Windows PowerShell 启用基础结构备份服务，以便定期备份以下内容：
 - 内部标识服务和根证书
 - 用户计划、产品/服务、订阅
 - 计算、 存储和网络用户配额
 - 用户密钥保管库机密
 - 用户 RBAC 角色和策略
 - 用户存储帐户

可以访问 PowerShell cmdlet 以启用备份、启动备份，以及通过操作员管理终结点获取备份信息。

## <a name="prepare-powershell-environment"></a>准备 PowerShell 环境

有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack，请参阅[配置操作员环境并登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供备份共享、凭据和加密密钥以启用备份

在同一 PowerShell 会话中，通过添加环境变量编辑以下 PowerShell 脚本。 运行更新的脚本，以向基础结构备份服务提供备份共享、凭据和加密密钥。

| 变量        | 描述   |
|---              |---                                        |
| $username       | 使用共享驱动器位置具有足够访问权限的域和用户名输入**用户名**，以便读取和写入文件。 例如，`Contoso\backupshareuser`。 |
| $password       | 键入用户的**密码**。 |
| $sharepath      | 键入**备份存储位置**的路径。 必须使用通用命名约定 (UNC) 字符串表示单独的设备上托管的文件共享的路径。 UNC 字符串指定资源（如共享文件或设备）的位置。 若要确保备份数据的可用性，设备应放置在单独的位置。 |
| $frequencyInHours | “频率(小时)”决定了以何频率创建备份。 默认值为 12。 计划程序支持的最大值为 12，最小值为 4。|
| $retentionPeriodInDays | “保留期(天)”决定了备份在外部位置保留多少天。 默认值为 7。 计划程序支持的最大值为 14，最小值为 2。 超过保留期的备份会自动从外部位置删除。|
| $encryptioncertpath | 加密证书路径指定的文件路径。CER 文件，其用于数据加密的公钥。 |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>确认备份设置

在同一 PowerShell 会话中，运行以下命令：

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

结果应类似于以下示例输出：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>更新备份设置
在同一 PowerShell 会话中，可以更新备份的保留期和频率的默认值。 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

结果应类似于以下示例输出：

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>Azure Stack PowerShell 
若要配置基础结构备份的 PowerShell cmdlet 是集 AzsBackupConfiguration。 在早期版本中，该 cmdlet 是集 AzsBackupShare。 此 cmdlet 需要提供证书。 如果使用的加密密钥配置基础结构备份，无法更新加密密钥，或查看的属性。 你将需要使用管理员 PowerShell 1.6 版。 

如果基础结构备份已配置到 1901年更新之前，您可以使用版本 1.6 的管理员 PowerShell 设置以及查看加密密钥。 1.6 版将允许你从加密密钥更新到证书文件。
请参阅[安装 Azure Stack PowerShell](azure-stack-powershell-install.md)有关安装该模块的正确版本的详细信息。 


## <a name="next-steps"></a>后续步骤

了解如何运行备份，请参阅[备份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)

了解如何验证是否备份已运行，请参阅[确认在管理门户中完成的备份](azure-stack-backup-back-up-azure-stack.md)
