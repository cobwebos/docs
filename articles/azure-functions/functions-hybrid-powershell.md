---
title: 使用 PowerShell 函数以远程方式管理本地资源
description: 了解如何在 Azure 中继中配置混合连接，以便将 PowerShell 函数应用连接到本地资源，然后将其用来以远程方式管理本地资源。
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122086"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>在 Azure Functions 和应用服务混合连接中使用 PowerShell 管理混合环境

可以通过 Azure 应用服务混合连接功能访问其他网络中的资源。 若要详细了解此功能，可参阅[混合连接](../app-service/app-service-hybrid-connections.md)文档。 本文介绍如何使用此功能运行以本地服务器为目标的 PowerShell 函数， 然后使用该服务器通过 Azure PowerShell 函数管理本地环境中的所有资源。


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>配置适用于 PowerShell 远程处理的本地服务器

以下脚本启用 PowerShell 远程处理，并创建新的防火墙规则和 WinRM Https 侦听器。 出于测试目的，我们使用自签名的证书。 在生产环境中，建议使用签名证书。

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

应用服务混合连接功能只能在“基本”、“标准”和“隔离”定价计划中使用。 使用 PowerShell 创建函数应用时，请创建或选择上述计划中的一项。

1. 在 Azure 门户菜单或“主页”页中，选择“创建资源”   。

1. 在“新建”页面，选择“计算” > “函数应用”    。

1. 在“基本信息”页上，使用下表中指定的函数应用设置。 

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **订阅** | 订阅 | 要在其下创建此新函数应用的订阅。 |
    |  [资源组](../azure-resource-manager/management/overview.md) |  *myResourceGroup* | 要在其中创建 Function App 的新资源组的名称。 |
    | **函数应用名称** | 全局唯一名称 | 用于标识新 Function App 的名称。 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。  |
    |**发布**| 代码 | 用于发布代码文件或 Docker 容器的选项。 |
    | **运行时堆栈** | 首选语言 | 选择 PowerShell Core。 |
    |**版本**| 版本号 | 选择已安装的运行时的版本。  |
    |**区域**| 首选区域 | 选择离你近或离函数访问的其他服务近的[区域](https://azure.microsoft.com/regions/)。 |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="创建函数应用-基础知识。" border="true":::

1. **选择“下一步:** 托管”。 在“托管”  页上，输入以下设置。

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    |  [存储帐户](../storage/common/storage-account-create.md) |  全局唯一名称 |  创建函数应用使用的存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 也可使用现有帐户，但该帐户必须符合[存储帐户要求](../azure-functions/functions-scale.md#storage-account-requirements)。 |
    |**操作系统**| 首选操作系统 | 系统会根据你的运行时堆栈选择为你预先选择一个操作系统，但你可以根据需要更改该设置。 |
    | **[计划类型](../azure-functions/functions-scale.md)** | **应用服务计划** | 选择 "**应用服务计划**"。 按应用服务计划运行时，必须管理[函数应用的缩放](../azure-functions/functions-scale.md)。  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="创建函数应用-托管。" border="true":::

1. **选择“下一步:** 监视”。 在“监视”  页上，输入以下设置。

    | 设置      | 建议的值  | 说明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | 默认 | 在最近的受支持的区域中，创建一个具有相同应用名称  的 Application Insights 资源。 通过展开此设置或选择 "**新建**"，可以更改 Application Insights 名称，或者在[Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)中选择要存储数据的其他区域。 |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="创建函数应用监视。" border="true":::

1. 选择“查看 + 创建”  ，以便查看应用配置选择。

1. 在“查看 + 创建”页上查看设置，然后选择“创建”来预配并部署函数应用   。

1. 选择门户右上角的 "**通知**" 图标，然后查看 "**部署已成功**" 消息。

1. 选择“转到资源”  ，查看新的函数应用。 还可选择“固定到仪表板”  。 固定可以更轻松地从仪表板返回此函数应用资源。

## <a name="create-a-hybrid-connection-for-the-function-app"></a>为函数应用创建混合连接

混合连接在函数应用的网络部分配置：

1. 在刚创建的函数应用的 "**设置**" 下，选择 "**网络**"。 
1. 选择“配置混合连接终结点”。****
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="配置混合连接终结点。" border="true":::

1. 选择“添加混合连接”。****
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="添加混合连接。" border="true":::

1. 输入混合连接的相关信息，如以下屏幕截图后面的内容所示。 可以选择让“终结点主机”设置与本地服务器的主机名匹配。这样，在以后运行远程命令时，就可以更容易地记住此服务器。**** 端口与此前在服务器上定义的默认 Windows 远程管理服务端口匹配。
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="添加混合连接。" border="true":::

    | 设置      | 建议的值  |
    | ------------ | ---------------- |
    | **混合连接名称** | ContosoHybridOnPremisesServer |
    | **终结点主机** | finance1 |
    | **终结点端口** | 5986 |
    | **空间空间命名空间** | 新建 |
    | **位置** | 选取可用位置 |
    | **名称** | contosopowershellhybrid | 

1. 选择“确定”，创建此混合连接。****

## <a name="download-and-install-the-hybrid-connection"></a>下载并安装安装混合连接

1. 选择 "**下载连接管理器**" 以在计算机上本地保存 *.msi*文件。

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="下载安装程序。" border="true":::

1. 将本地计算机中的 *.msi*文件复制到本地服务器。
1. 运行混合连接管理器安装程序，在本地服务器上安装此服务。

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="安装混合连接。" border="true":::

1. 在门户中打开混合连接，然后将网关连接字符串复制到剪贴板。

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="复制混合连接字符串。" border="true":::

1. 在本地服务器上打开“混合连接管理器 UI”。

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="打开混合连接用户界面。" border="true":::

1. 选择 "**手动输入**"，然后从剪贴板中粘贴连接字符串。

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="粘贴混合连接。" border="true":::

1. 在 PowerShell 中重启混合连接管理器（如果它没有显示为已连接）。
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>针对管理员帐户的密码创建应用设置

1. 在函数应用的 "**设置**" 下，选择 "**配置**"。 
1. 选择 " **+ 新建应用程序设置**"。

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="配置管理员帐户的密码。" border="true":::

1. 将设置命名为 **ContosoUserPassword**，然后输入密码。 选择“确定”  。
1. 选择 "**保存**"，将密码存储在函数应用程序中。

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="保存管理员帐户的密码。" border="true":::

## <a name="create-a-function-http-trigger"></a>创建函数 HTTP 触发器

1. 在函数应用中，选择 "**函数**"，然后选择 " **+ 添加**"。

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="创建新的 HTTP 触发器。" border="true":::

1. 选择**HTTP 触发器**模板。

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="选择 HTTP 触发器模板。" border="true":::

1. 将新函数命名为，然后选择 "**创建函数**"。

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="命名并创建新的 HTTP 触发器函数。" border="true":::

## <a name="test-the-function"></a>测试函数

1. 在新函数中，选择 "**代码 + 测试**"。 将模板中的 PowerShell 代码替换为以下代码：

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

1. 选择“保存”  。

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="更改 PowerShell 代码，并保存 HTTP 触发器函数。" border="true":::

 1. 选择 "**测试**"，然后选择 "**运行**" 以测试函数。 查看日志以验证测试是否成功。

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="测试 HTTP 触发器函数。" border="true":::

## <a name="managing-other-systems-on-premises"></a>在本地管理其他系统

可以使用连接的本地服务器连接到本地环境中的其他服务器和管理系统。 这样即可使用 PowerShell 函数在 Azure 中管理数据中心操作。 以下脚本注册一个 PowerShell 配置会话，该会话使用提供的凭据运行。 这些凭据必须是远程服务器上的管理员的。 然后，可以使用此配置访问本地服务器或数据中心中的其他终结点。

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

请将该脚本中的以下变量替换为你环境中的相应值：
* $HybridEndpoint
* $RemoteServer

在上面的两个方案中，可以在 Azure Functions 和混合连接中使用 PowerShell 连接和管理本地环境。 建议[在函数中](./functions-reference-powershell.md)详细了解[混合连接](../app-service/app-service-hybrid-connections.md)和 PowerShell。

还可以通过 Azure Functions 使用 Azure[虚拟网络](./functions-create-vnet.md)连接到本地环境。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [了解有关使用 PowerShell 函数的详细信息](functions-reference-powershell.md)
