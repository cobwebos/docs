---
title: "使用 PowerShell 的 Azure 堆栈启用备份 |Microsoft 文档"
description: "启用基础结构返回服务使用 Windows PowerShell，以便可以还原 Azure 堆栈，如果故障。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: e0be5f1916ddb653550e6428201356290560c00e
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>使用 PowerShell 的 Azure 堆栈启用备份

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

启用基础结构返回服务使用 Windows PowerShell，以便可以还原 Azure 堆栈，如果故障。 你可以访问 PowerShell cmdlet 来启用备份，开始备份，并获取通过运算符管理终结点的备份信息。

## <a name="download-azure-stack-tools"></a>下载 Azure 堆栈工具

安装和配置适用于 Azure 堆栈的 PowerShell 和 Azure 堆栈工具。 请参阅[获取启动并运行 Azure 堆栈中的 PowerShell](https://review.docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart)。

##  <a name="load-the-connect-and-infrastructure-modules"></a>加载连接和基础结构模块

使用提升的提示符下，打开 Windows PowerShell 并运行以下命令：

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>到运算符管理终结点设置 Rm 环境和日志

在相同的 PowerShell 会话中，通过添加你的环境的变量中编辑以下 PowerShell 脚本。 运行更新的脚本来设置 RM 环境并登录到运算符管理终结点。

| 变量    | 说明 |
|---          |---          |
| $TenantName | Azure Active Directory 租户名称。 |
| 运算符帐户名称        | 你 Azure 堆栈运算符的帐户名称。 |
| Azure 资源管理器终结点 | 到 Azure 资源管理器的 URL。 |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>生成新的加密密钥

在相同的 PowerShell 会话中，运行以下命令：

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>提供的备份的共享、 凭据和加密密钥，若要启用备份

在相同的 PowerShell 会话中，通过添加你的环境的变量中编辑以下 PowerShell 脚本。 运行更新的脚本，以提供到基础结构备份服务备份的共享、 凭据和加密密钥。

| 变量        | 说明   |
|---              |---                                        |
| $username       | 类型**用户名**为共享的驱动器位置中使用的域和用户名。 例如，`Contoso\administrator`。 |
| $password       | 类型**密码**用户。 |
| $sharepath      | 键入的路径**备份存储位置**。 必须为文件共享托管在一个单独的设备的路径使用通用命名约定 (UNC) 字符串。 UNC 字符串指定的资源，如共享的文件或设备的位置。 若要确保备份数据的可用性，设备应在单独的位置。 |

   ```powershell
   $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

Set-AzSBackupShare -Location $location -Path $path -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey 

   ```
##  <a name="confirm-backup-settings"></a>确认备份设置

在相同的 PowerShell 会话中，运行以下命令：

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
   ```

结果应类似下面的 JSON 输出：

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>后续步骤

 - 了解如何运行备份，请参阅[备份 Azure 堆栈](azure-stack-backup-back-up-azure-stack.md )。  
- 了解要验证你的备份运行，请参阅[在管理门户中完成的确认备份](azure-stack-backup-back-up-azure-stack.md )。