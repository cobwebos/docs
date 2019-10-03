---
title: 使用 PowerShell 功能管理远程本地资源
description: 了解如何在 Azure 中继中配置混合连接，以便将 PowerShell 函数应用连接到本地资源，然后可以使用这些资源来远程管理本地资源。
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 2a3cf79883d79eb82c361731eb6a632c2df3c9be
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299428"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>在 Azure Functions 和应用服务混合连接中通过 PowerShell 管理混合环境

使用 Azure App Service 混合连接功能可以访问其他网络中的资源。 可以在[混合连接](../app-service/app-service-hybrid-connections.md)文档中了解有关此功能的详细信息。 本文介绍如何使用此功能来运行面向本地服务器的 PowerShell 函数。 然后，可以使用此服务器通过 Azure PowerShell 功能管理本地环境中的所有资源。


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>为 PowerShell 远程处理配置本地服务器

以下脚本启用 PowerShell 远程处理，并创建新的防火墙规则和 WinRM https 侦听器。 出于测试目的，使用自签名证书。 在生产环境中，我们建议使用签名证书。

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>在门户中创建 PowerShell 函数应用

应用服务混合连接功能仅在基本、标准和隔离定价计划中提供。 通过 PowerShell 创建 function app 时，请创建或选择其中一个计划。

1. 在[Azure 门户](https://portal.azure.com)中，在左侧菜单中选择 " **+ 创建资源**"，然后选择 "**函数应用**"。

1. 对于 "**托管计划**"，请选择 "**应用服务计划**"，然后选择 "**应用服务计划/位置**"。

1. 选择 "**新建**"，键入**应用服务计划**名称，选择附近或附近的[区域](https://azure.microsoft.com/regions/)中的某个**位置**或函数访问的其他服务，然后选择 "**定价层**"。

1. 选择 "S1 标准" 计划，然后选择 "**应用**"。

1. 选择 **"确定"** 以创建计划，然后配置以下屏幕截图后面的表中指定的其余**Function App**设置：

    ![PowerShell Core 函数应用](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | 设置      | 建议的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效的字符是 `a-z`、`0-9` 和 `-`。  | 
    | **订阅** | 订阅 | 要在其下创建此新函数应用的订阅。 |
    | **资源组** |  myResourceGroup | 要在其中创建 Function App 的新资源组的名称。 你还可以使用建议的值。 |
    | **OS** | 首选操作系统 | 选择 "窗口"。 |
    | **运行时堆栈** | 首选语言 | 选择 "PowerShell Core"。 |
    | **存储** |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称的长度必须为3到24个字符，并且只能包含数字和小写字母。 也可以使用现有帐户。
    | **Application Insights** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称的 Application Insights 资源。 通过展开此设置，你可以更改**新的资源名称**，或在要存储数据的[Azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)区域中选择其他**位置**。 |

1. 验证设置后，选择 "**创建**"。

1. 选择门户右上角的**通知**图标，并等待 "部署已成功" 消息。

1. 选择“转到资源”，查看新的函数应用。 还可选择“固定到仪表板”。 固定可以更轻松地从仪表板返回此函数应用资源。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>为 function app 创建混合连接

混合连接是通过 function app 的 "网络" 部分配置的：

1. 在函数应用中选择 "**平台功能**" 选项卡，然后选择 "**网络**"。 
   ![平台网络应用概述](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. 选择 "**配置混合连接终结点**"。
   ![网络](./media/functions-hybrid-powershell/select-network-feature.png)  
1. 选择 "**添加混合连接**"。
   ![混合连接](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. 输入有关混合连接的信息，如以下屏幕截图所示。 你可以选择使**终结点主机**设置与本地服务器的主机名匹配，以便在以后运行远程命令时更容易记住服务器。 端口与之前在服务器上定义的默认 Windows 远程管理服务端口匹配。
  ![添加混合连接](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **混合连接名称**：ContosoHybridOnPremisesServer
    
    **终结点主机**： finance1
    
    **终结点端口**：5986
    
    **服务总线命名空间**：新建
    
    **位置**：选取可用位置
    
    **名称**： contosopowershellhybrid

5. 选择 **"确定"** 以创建混合连接。

## <a name="download-and-install-the-hybrid-connection"></a>下载并安装混合连接

1. 选择 "**下载连接管理器**" 以在计算机上本地保存 .msi 文件。
![下载安装程序](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. 将本地计算机中的 .msi 文件复制到本地服务器。
1. 运行混合连接管理器安装程序以在本地服务器上安装服务。
![安装混合连接](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. 在门户中打开 "混合连接"，并将网关连接字符串复制到剪贴板。
![复制混合连接字符串](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. 在本地服务器上打开混合连接管理器 UI。
![打开混合连接 UI](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. 选择 "**手动输入**" 按钮，然后从剪贴板中粘贴连接字符串。
![粘贴连接](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. 如果不显示为 "已连接"，请从 PowerShell 重新启动混合连接管理器。
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>为管理员帐户的密码创建应用设置

1. 在函数应用中选择 "**平台功能**" 选项卡。
1. 在 "**常规设置**" 下，选择 "**配置**"。
![选择平台配置](./media/functions-hybrid-powershell/select-configuration.png)  
1. 展开 "**新应用程序设置**"，为密码创建新的设置。
1. 将该设置命名为_ContosoUserPassword_，然后输入密码。
1. 选择 **"确定"** ，然后单击 "保存" 以在函数应用程序中存储密码。
![为密码添加应用程序设置](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>创建用于测试的函数 http 触发器

1. 从 function app 创建新的 HTTP 触发器函数。
![创建新的 HTTP 触发器](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. 将模板中的 PowerShell 代码替换为以下代码：

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. 选择 "**保存**并**运行**" 以测试该函数。
![测试函数应用](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>管理本地的其他系统

可以使用连接的本地服务器连接到本地环境中的其他服务器和管理系统。 这样，你便可以使用 PowerShell 函数管理 Azure 中的数据中心操作。 以下脚本将注册在提供的凭据下运行的 PowerShell 配置会话。 对于远程服务器上的管理员，这些凭据必须是。 然后，可以使用此配置访问本地服务器或数据中心上的其他终结点。

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

将此脚本中的以下变量替换为你的环境中的合适值：
* $HybridEndpoint
* $RemoteServer

在上述两种情况下，你可以使用 Azure Functions 和混合连接中的 PowerShell 连接和管理本地环境。 建议[在函数中](./functions-reference-powershell.md)详细了解[混合连接](../app-service/app-service-hybrid-connections.md)和 PowerShell。

还可以通过 Azure Functions 使用 Azure[虚拟网络](./functions-create-vnet.md)连接到本地环境。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [了解有关使用 PowerShell 函数的详细信息](functions-reference-powershell.md)
