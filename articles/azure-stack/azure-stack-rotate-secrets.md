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
ms.openlocfilehash: 9b65a3cb5cdcc8a558e5c989026f2eee2f527bb5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>旋转 Azure 堆栈中的机密

*适用范围： Azure 堆栈集成系统*

更新你的密码的正则节奏 Azure 堆栈组件。

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>更新基板管理控制器 (BMC) 的密码

基板管理控制器 (BMC) 监视你的服务器的物理状态。 规范和更新的 bmc 密码的说明取决于你的原始设备制造商 (OEM) 硬件供应商联系。

1. OEM 说明更新你的服务器上的 BMC。 在你的环境中的每个 BMC 的密码必须相同。
2. 在 Azure 堆栈会话中打开的特权的终结点。 有关说明，请参阅[Azure 堆栈中使用的特权的终结点](azure-stack-privileged-endpoint.md)。
3. 在您的 PowerShell 提示符已更改为**[IP 地址或 ERCS VM name]: PS >**或**[azs ercs01]: PS >**，取决于环境中，运行`Set-BmcPassword`通过运行`invoke-command`。 将你特权终结点的会话变量作为参数传递。  
例如：
    ```powershell
    $PEPSession = New-PSSession -ComputerName <ERCS computer name> -Credential <CloudAdmin credential> -ConfigurationName "PrivilegedEndpoint"  
    
    Invoke-Command -Session $PEPSession -ScriptBlock {
        param($password)
        set-bmcpassword -bmcpassword $password
    } -ArgumentList (<LatestPassword as a SecureString>) 
    ```

## <a name="next-steps"></a>后续步骤

若要了解有关安全和 Azure 堆栈的详细信息，请参阅[Azure 堆栈基础结构安全状况](azure-stack-security-foundations.md)。