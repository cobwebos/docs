---
title: 连接到 Azure Stack | Microsoft Docs
description: 了解如何连接到 ASDK。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: knithinc
ms.openlocfilehash: 1b5d5a2934205877f0e0c2ac891e62c90e960b3d
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085143"
---
# <a name="connect-to-the-asdk"></a>连接到 ASDK

若要管理资源，必须先连接到 Azure Stack 开发工具包 (ASDK)。 在本文中，我们将介绍通过使用以下连接选项连接到 ASDK 所采取的步骤：

* [远程桌面连接 (RDP)](#connect-with-rdp)。 使用远程桌面连接进行连接时，单个用户可以快速连接到开发工具包。
* [虚拟专用网络 (VPN)](#connect-with-vpn)。 使用 VPN 进行连接时，多个用户可以同时从 Azure Stack 基础结构外部的客户端进行连接。 VPN 连接需要一些设置。

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>连接到 Azure Stack 使用 RDP

单个并发用户可以直接从 asdk 主机管理 Azure Stack 管理门户或通过远程桌面连接在用户门户中的资源。 

> [!TIP]
> 此选项还可以使用 RDP 重新登录到 asdk 主机时登录到 ASDK 主机计算机上创建的虚拟机。 

1. 打开远程桌面连接 (mstc.exe) 并连接到开发工具包主机的计算机使用的 IP 地址授权远程登录到 asdk 主机的帐户。 默认情况下**AzureStack\AzureStackAdmin**拥有对远程连接到 asdk 主机中的权限。  

2. 在开发工具包主机上打开服务器管理器 (ServerManager.exe)。 选择**本地服务器**，将关闭**IE 增强的安全配置**，关闭服务器管理器。

3. 登录到管理门户中，如下**AzureStack\CloudAdmin**或使用其他 Azure Stack 操作员的凭据。 ASDK 管理门户地址是[ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)。

4. 登录到用户门户中，如下**AzureStack\CloudAdmin**或使用其他 Azure Stack 用户的凭据。 ASDK 用户门户地址是[ https://portal.local.azurestack.external ](https://portal.local.azurestack.external)。

> [!NOTE]
> 有关何时使用何种帐户的详细信息，请参阅[ASDK 管理基础知识](asdk-admin-basics.md#what-account-should-i-use)。

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>连接到 Azure Stack 使用 VPN

您可以建立拆分隧道 VPN 连接到 ASDK 主机计算机访问 Azure Stack 门户和 Visual Studio 和 PowerShell 之类的本地安装的工具。 使用 VPN 连接，多个用户可以连接到托管的 ASDK 的 Azure Stack 资源同时。

支持 VPN 连接为这两个 Azure AD 和 Active Directory 联合身份验证服务 (AD FS) 部署。

> [!NOTE]
> VPN 连接*却不*向 Azure Stack Vm 提供连接。 你将无法再通过 rdp 连接到 Azure Stack Vm 通过 VPN 连接时。

### <a name="prerequisites"></a>必备组件
在与 ASDK 的 VPN 连接进行设置之前, 请确保已满足以下先决条件。

- 在本地计算机上安装[与 Azure Stack 兼容的 Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)。  
- 下载[使用 Azure Stack 所需的工具](asdk-post-deploy.md#download-the-azure-stack-tools)。

### <a name="set-up-vpn-connectivity"></a>设置 VPN 连接

若要创建与 ASDK 的 VPN 连接，请以管理员身份在本地基于 Windows 的计算机上打开 PowerShell。 然后，运行以下脚本（更新环境的 IP 地址和密码值）：

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

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

![网络连接](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

  使用以下方法之一连接到 Azure Stack 实例：  

  * 使用 `Connect-AzsVpn ` 命令：
      
    ```PowerShell
    Connect-AzsVpn `
      -Password $Password
    ```

  * 在本地计算机上，选择“网络设置” > “VPN” > “azurestack” > “连接”。 在登录提示符下，输入用户名 (**AzureStack\AzureStackAdmin**) 和密码。

第一次连接时，将会提示您安装的 Azure Stack 根证书**AzureStackCertificateAuthority**本地计算机的证书存储区中。 此步骤将 ASDK 证书颁发机构 (CA) 添加到受信任主机列表。 单击**是**安装证书。

![根证书](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > 在提示符下可能会隐藏在 PowerShell 窗口或其他应用程序。

### <a name="test-vpn-connectivity"></a>测试 VPN 连接

若要测试门户连接，请打开 web 浏览器，然后转到用户门户 (https://portal.local.azurestack.external/)或在管理门户 (https://adminportal.local.azurestack.external/)。 

使用要创建和管理资源的相应的订阅凭据登录。  

## <a name="next-steps"></a>后续步骤

[故障排除](asdk-troubleshooting.md)
