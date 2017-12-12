---
title: "连接到 Azure 的堆栈 |Microsoft 文档"
description: "了解如何连接到 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: d9d7beae9ea81f2568377ee3593362871aae98fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>连接到 Azure Stack

*适用范围： Azure 堆栈开发工具包*

若要管理的资源，必须首先连接到 Azure 堆栈开发工具包。 在本文中，我们将介绍为连接到开发工具包所采取的步骤。 你可以使用以下连接选项之一：

* [远程桌面连接](#connect-with-remote-desktop)。 在使用远程桌面连接进行连接，一个用户可以快速连接从开发工具包。
* [虚拟专用网络 (VPN)](#connect-with-vpn)。 当你连接时使用 VPN 时，多个用户可以同时连接从外部 （需要安装程序） 的 Azure 堆栈基础结构的客户端。

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>使用远程桌面连接连接到 Azure 堆栈
单个的并发用户可以管理运算符门户或通过远程桌面连接的用户门户中的资源。

1. 打开远程桌面连接并连接到开发工具包。 对于用户名，输入**AzureStack\AzureStackAdmin**。 使用 Azure 堆栈中向上设置时指定的运算符密码。  

2. 开发工具包在计算机上，打开服务器管理器。 选择**本地服务器**，清除**Internet Explorer 增强安全性**复选框，，然后关闭服务器管理器。

3. 若要打开[用户门户](azure-stack-key-features.md#portal)，请转到 https://portal.local.azurestack.external/。 通过使用用户凭据登录。 若要打开 Azure 堆栈[运算符门户](azure-stack-key-features.md#portal)，请转到 https://adminportal.local.azurestack.external/。 使用在安装期间指定的 Azure Active Directory (Azure AD) 凭据登录。

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>通过使用 VPN 连接到 Azure 堆栈

你可以建立与 Azure 堆栈开发工具包的 VPN 连接使用拆分隧道连接。 可以使用 VPN 连接来访问 Azure 堆栈运算符门户，用户门户和本地安装的工具，如 Visual Studio 和 PowerShell 来管理 Azure 堆栈的资源。 在 Azure AD 和 Active Directory 联合身份验证服务 (AD FS) 部署中支持 VPN 连接。 VPN 连接使多个客户端同时连接到 Azure 堆栈。 

> [!NOTE] 
> VPN 连接不提供连接到 Azure 堆栈基础结构 Vm。 

### <a name="prerequisites"></a>必备组件

1. 安装[Azure 堆栈兼容 Azure PowerShell](azure-stack-powershell-install.md)本地计算机上。  
2. 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。 

### <a name="set-up-vpn-connectivity"></a>设置 VPN 连接

若要创建与开发工具包的 VPN 连接，请以管理员身份本地基于 Windows 的计算机上打开 Windows PowerShell。 然后，运行以下脚本 （你的环境的 IP 地址和密码值的更新）：

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

如果安装成功， **azurestack**将出现在你的 VPN 连接的列表。

![网络连接](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

通过使用以下方法之一连接到 Azure 堆栈实例：  

* 使用`Connect-AzsVpn `命令： 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  出现提示时，信任 Azure 堆栈主机和安装证书**AzureStackCertificateAuthority**你本地计算机证书存储中。 （提示符可能被隐藏时 PowerShell 窗口。） 

* 在本地计算机上，选择**网络设置** > **VPN** > **azurestack** > **连接**. 在签入提示符下，输入用户名 (**AzureStack\AzureStackAdmin**) 和你的密码。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接

若要测试的门户连接，打开 web 浏览器中，，然后转到用户门户 (https://portal.local.azurestack.external/) 或运算符门户 (https://adminportal.local.azurestack.external/)。 登录并创建资源。  

## <a name="next-steps"></a>后续步骤

[让 Azure 堆栈用户能够使用虚拟机](azure-stack-tutorial-tenant-vm.md)

