---
title: 连接到 Azure Stack | Microsoft Docs
description: 了解如何连接到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/06/2018
ms.author: mabrigg
ms.openlocfilehash: c1932f2ed0486fb56e467466c0fed53702e8f9b0
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248680"
---
# <a name="connect-to-azure-stack-development-kit"></a>连接到 Azure 的堆栈开发工具包

*适用于：Azure Stack 开发工具包*

若要管理资源，必须先连接到 Azure Stack 开发工具包。 本文将介绍连接到开发工具包所需执行的步骤。 可以使用以下连接选项之一：

* [远程桌面连接](#connect-with-remote-desktop)。 在使用远程桌面连接进行连接，单个用户可以快速连接到开发工具包。
* [虚拟专用网络 (VPN)](#connect-with-vpn)。 当你连接时使用 VPN 时，多个用户可以同时连接从 Azure 堆栈基础结构外部的客户端。 VPN 连接需要进行一些设置。

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>使用远程桌面连接连接到 Azure Stack

单个并发用户可以通过远程桌面连接管理运营商门户或用户门户中的资源。

1. 打开“远程桌面连接”并连接到开发工具包。 对于用户名，输入 **AzureStack\AzureStackAdmin**。 使用设置 Azure Stack 时指定的运营商密码。  

2. 在开发工具包所在的计算机上，打开“服务器管理器”。 选择“本地服务器”，清除“Internet Explorer 增强的安全性”复选框，然后关闭“服务器管理器”。

3. 若要打开[用户门户](azure-stack-key-features.md#portal)，请转到https://portal.local.azurestack.external/。 使用用户凭据登录。 若要打开 Azure 堆栈[运算符门户](azure-stack-key-features.md#portal)，请转到https://adminportal.local.azurestack.external/。 使用在安装期间指定的 Azure Active Directory (Azure AD) 凭据登录。

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>使用 VPN 连接到 Azure Stack

可以与 Azure Stack 开发工具包建立拆分隧道 VPN 连接。 可以使用 VPN 连接来访问 Azure Stack 运营商门户、用户门户，并使用本地安装的工具（如 Visual Studio 和 PowerShell）来管理 Azure Stack 资源。 在 Azure AD 中支持 VPN 连接和 Active Directory 联合身份验证服务 (AD FS) 部署。 使用 VPN 连接，多个客户端能够同时连接到 Azure Stack。

> [!NOTE]
> VPN 连接不提供与 Azure Stack 基础结构 VM 的连接。

### <a name="prerequisites"></a>必备组件

1. 在本地计算机上安装[与 Azure Stack 兼容的 Azure PowerShell](azure-stack-powershell-install.md)。  
2. 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。

### <a name="set-up-vpn-connectivity"></a>设置 VPN 连接

若要创建与开发工具包的 VPN 连接，请在本地基于 Windows 的计算机上以管理员身份打开 Windows PowerShell。 然后，运行以下脚本（更新环境的 IP 地址和密码值）：

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

如果设置成功，**azurestack** 将出现在 VPN 连接列表中。

![网络连接](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

使用以下方法之一连接到 Azure Stack 实例：  

* 使用 `Connect-AzsVpn ` 命令：
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  出现提示时，信任 Azure Stack 主机，并将 **AzureStackCertificateAuthority** 提供的证书安装到本地计算机的证书存储中。 （提示可能会被 PowerShell 窗口隐藏。）

* 在本地计算机上，选择“网络设置” > “VPN” > “azurestack” > “连接”。 在登录提示符下，输入用户名 (**AzureStack\AzureStackAdmin**) 和密码。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接

若要测试的门户连接，打开 web 浏览器，，然后转到用户门户 (https://portal.local.azurestack.external/)或运算符门户 (https://adminportal.local.azurestack.external/)。 登录并创建资源。  

## <a name="next-steps"></a>后续步骤

[向 Azure Stack 用户提供虚拟机](azure-stack-tutorial-tenant-vm.md)
