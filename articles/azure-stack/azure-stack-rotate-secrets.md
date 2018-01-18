---
title: "旋转 Azure 堆栈中的机密 |Microsoft 文档"
description: "了解如何旋转您 Azure 堆栈中的机密信息。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>旋转 Azure 堆栈中的机密

*适用范围： Azure 堆栈集成系统*

更新你的密码的正则节奏 Azure 堆栈组件。

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>更新基板管理控制器 (BMC) 的密码

基板管理控制器 (BMC) 监视你的服务器的物理状态。 规范和更新的 bmc 密码的说明取决于你的原始设备制造商 (OEM) 硬件供应商联系。

1. OEM 说明更新 Azure 堆栈的物理服务器上的 BMC。 在你的环境中的每个 BMC 的密码必须相同。
2. 在 Azure 堆栈会话中打开的特权的终结点。 有关说明，请参阅[Azure 堆栈中使用的特权的终结点](azure-stack-privileged-endpoint.md)。
3. 在您的 PowerShell 提示符已更改为**[IP 地址或 ERCS VM name]: PS >**或**[azs ercs01]: PS >**，取决于环境中，运行`Set-BmcPassword`通过运行`invoke-command`。 将你特权终结点的会话变量作为参数传递。 例如：

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    你还可以使用密码使用静态的 PowerShell 版本，如的代码行中：
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>后续步骤

若要了解有关安全和 Azure 堆栈的详细信息，请参阅[Azure 堆栈基础结构安全状况](azure-stack-security-foundations.md)。
