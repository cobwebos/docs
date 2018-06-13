---
title: 连接到 Azure 的堆栈 |Microsoft 文档
description: 了解如何连接 Azure 堆栈
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
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 7479202a8afabf5a84560691a2bccf849206c077
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636171"
---
# <a name="connect-to-azure-stack"></a>连接到 Azure Stack

若要管理的资源，你必须连接到 Azure 堆栈开发工具包。 本文详细介绍连接到开发工具包所需的步骤。 你可以使用以下连接选项之一：

* [远程桌面](#connect-with-remote-desktop)： 允许单个并发用户快速连接从开发工具包。
* [虚拟专用网络 (VPN)](#connect-with-vpn)： 允许从 Azure 堆栈的基础结构 （需要配置） 以外的客户端的多个并发用户连接。

## <a name="connect-to-azure-stack-with-remote-desktop"></a>连接到远程桌面与 Azure 堆栈
使用远程桌面连接，单个并发用户可以使用门户管理资源。

1. 打开远程桌面连接并连接到开发工具包。 输入**AzureStack\AzureStackAdmin**用户名，以及在 Azure 堆栈设置期间提供的管理密码。  

2. 从开发工具包计算机上，打开服务器管理器中，单击**本地服务器**关闭 Internet Explorer 增强安全性，，然后关闭服务器管理器。

3. 若要打开门户，导航到 (https://portal.local.azurestack.external/)，并使用用户凭据登录。


## <a name="connect-to-azure-stack-with-vpn"></a>连接到使用 VPN 的 Azure 堆栈

你可以建立拆分隧道虚拟专用网络 (VPN) 连接到 Azure 堆栈开发工具包。 通过 VPN 连接，您可以访问管理员门户中，用户门户，并且本地安装的工具，例如 Visual Studio 和 PowerShell 来管理 Azure 堆栈的资源。 在这两个 Azure Active Directory(AAD) 支持 VPN 连接和 Active Directory 联合身份验证 Services(AD FS) 基于部署。 VPN 连接使多个客户端同时连接到 Azure 堆栈。 

> [!NOTE] 
> 此 VPN 连接不提供连接到 Azure 堆栈基础结构 Vm。 

### <a name="prerequisites"></a>必备组件

* 安装[Azure 堆栈兼容的 Azure PowerShell](azure-stack-powershell-install.md)本地计算机上。  
* 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。 

### <a name="configure-vpn-connectivity"></a>配置 VPN 连接

若要创建与开发工具包的 VPN 连接，从本地基于 Windows 的计算机打开提升的 PowerShell 会话并运行以下脚本 （请确保更新你的环境的 IP 地址和密码值）：

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

如果成功进行的设置，你应看到**azurestack**列表中的 VPN 连接。

![网络连接](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>连接到 Azure Stack

通过使用以下两种方法之一连接到 Azure 堆栈实例：  

* 通过使用`Connect-AzsVpn `命令： 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  出现提示时，信任 Azure 堆栈主机和安装证书**AzureStackCertificateAuthority**到本地计算机的证书存储。 （提示可能会出现后面的 PowerShell 会话窗口中）。 

* 打开本地计算机的**网络设置** > **VPN** > 单击**azurestack** > **连接**。 在签入提示符下，输入用户名 (AzureStack\AzureStackAdmin) 和密码。

### <a name="test-the-vpn-connectivity"></a>测试 VPN 连接

若要测试的门户连接，打开 Internet 浏览器并导航到用户门户 (https://portal.local.azurestack.external/)、 登录和创建资源。  

## <a name="next-steps"></a>后续步骤



