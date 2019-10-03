---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173412"
---
根据客户端的操作系统，以远程方式连接到设备的过程是不同的。

### <a name="remotely-connect-from-a-windows-client"></a>Windows 客户端从远程连接

在开始之前，请确保 Windows 客户端运行 Windows PowerShell 5.0 或更高版本。

请按照下列步骤以远程方式从 Windows 客户端连接。

1. 以管理员身份运行的 Windows PowerShell 会话。
2. 请确保 Windows 远程管理服务在客户端上运行。 在命令提示符处，键入：

    `winrm quickconfig`

3. 将一个变量分配给设备 IP 地址。

    $ip = "<device_ip>"

    替换为`<device_ip>`与你的设备的 IP 地址。

4. 若要将你的设备的 IP 地址添加到客户端的受信任的主机列表中，键入以下命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在设备上启动 Windows PowerShell 会话：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 根据提示提供密码。 使用相同的密码用于登录本地 web UI。 默认本地 web UI 密码是*Password1*。 当您已成功连接到使用远程 PowerShell 的设备时，您将看到下面的示例输出：  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>从 Linux 客户端远程连接

在 Linux 客户端将用来连接：

- [安装最新适用于 Linux 的 PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6)从 GitHub 获取 SSH 远程处理功能。 
- [仅安装`gss-ntlmssp`NTLM 模块中的包](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 对于 Ubuntu 客户端，使用以下命令：
    - `sudo apt-get install gss-ntlmssp`

有关详细信息，请转到[通过 SSH 的 PowerShell 远程处理](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)。

请按照下列步骤以远程方式从 NFS 客户端连接。

1. 若要打开 PowerShell 会话，请键入：

    `sudo pwsh`
 
2. 对于使用远程客户端进行连接，请键入：

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    出现提示时，提供用于登录到你的设备的密码。
 
> [!NOTE]
> 此过程不适用于 Mac OS。
