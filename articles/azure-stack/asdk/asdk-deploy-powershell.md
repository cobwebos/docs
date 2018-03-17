---
title: "部署 Azure 堆栈的 PowerShell |Microsoft 文档"
description: "在本教程中，从命令行安装 ASDK。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 48ccccaba6b7f5780f1d42dfbe5d9747c5e30292
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-from-the-command-line"></a>教程： 部署从命令行 ASDK
在本教程中，你将部署 Azure 堆栈开发工具包 (ASDK) 从命令行在非生产环境中。 

ASDK 是一个测试和开发的环境，你可以部署评估并演示 Azure 堆栈功能和服务。 若要获取其启动并正在运行，需要准备环境硬件并运行 （这将需要几个小时） 一些脚本。 之后，你可以登录到管理员和用户门户若要开始使用 Azure 堆栈。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 下载并提取部署包
> * 准备开发工具包主机计算机 
> * 执行后期部署配置
> * 注册 Azure

## <a name="prerequisites"></a>必备组件 
准备开发工具包主机计算机。 规划你的硬件、 软件和网络。 承载开发工具包的计算机（开发工具包主机）必须满足硬件、软件和网络要求。 你还必须使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 之间进行选择。 请务必在开始部署前符合这些先决条件，以便安装进程能顺利运行。 

在部署 ASDK 之前，请确保你计划的开发工具包的主机计算机的硬件、 操作系统、 帐户和网络配置满足安装 ASDK 的最低要求。

**[查看 ASDK 部署规划注意事项](asdk-deploy-considerations.md)**

> [!TIP]
> 你可以使用[Azure 堆栈部署要求检查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)后安装操作系统，以确认你的硬件满足所有要求。

## <a name="download-and-extract-the-deployment-package"></a>下载并提取部署包
确保你开发工具包的主机计算机满足安装 ASDK 的基本要求之后下, 一步是要下载并解压缩 ASDK 部署包。 部署包包括 Cloudbuilder.vhdx 文件，它是虚拟硬盘，其中包括可启动的操作系统和 Azure 堆栈安装文件。

你可以将部署包下载到开发工具包主机或另一台计算机。 提取的部署文件占用 60 GB 的可用磁盘空间，因此使用另一台计算机有助于减少开发工具包主机的硬件要求。

**[下载并提取 Azure 堆栈开发工具包 (ASDK)](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>准备开发工具包主机计算机
你可以在主计算机上安装 ASDK 之前，必须准备环境和系统配置为启动从 VHD。 此步骤后，开发工具包主机将启动到 Cloudbuilder.vhdx （虚拟硬盘，其中包括可启动的操作系统和 Azure 堆栈安装文件）。

使用 PowerShell 来配置 ASDK 主计算机来从 CloudBuilder.vhdx 启动。 这些命令将从已下载并解压 Azure 堆栈虚拟硬盘 (CloudBuilder.vhdx) 启动你 ASDK 主机计算机的配置。 完成这些步骤后，请重启 ASDK 主机。

若要配置 ASDK 主机计算机启动时从 CloudBuilder.vhdx:

  1. 以管理员身份启动命令提示符。
  2. 运行 `bcdedit /copy {current} /d "Azure Stack"`
  3. 复制 (CTRL+C) 返回的 CLSID 值，包括必需的 {}。 该值称为 {CLSID}，需要在余下的步骤中粘贴进去（CTRL+V 或右键单击）。
  4. 运行 `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
  5. 运行 `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
  6. 运行 `bcdedit /set {CLSID} detecthal on` 
  7. 运行 `bcdedit /default {CLSID}`
  8. 若要验证启动设置，请运行 `bcdedit`。 
  9. 确保已将 CloudBuilder.vhdx 文件移动到 C:\ 驱动器的根目录 (C:\CloudBuilder.vhdx)，然后重启开发工具包主机。 重新启动 ASDK 主机计算机后，它应从 CloudBuilder.vhdx 虚拟机硬盘空间，以开始 ASDK 部署进行启动。 

> [!IMPORTANT]
> 确保你能够直接物理计算机或 KVM 访问开发工具包主机计算机然后重新启动它。 当第一次启动 VM 时，它会提示你完成 Windows Server 安装程序。 提供用于登录到开发工具包主机计算机的相同管理员凭据。 

### <a name="prepare-the-development-kit-host-using-powershell"></a>使用 PowerShell 准备开发工具包主机 
之后的开发工具包主机计算机已成功启动到 CloudBuilder.vhdx 映像，使用相同的本地管理员凭据用于登录到开发工具包主计算机 （和完成 Windows Server 的一部分提供的日志安装程序仅当从 VHD 启动主计算机）。 

> [!NOTE]
> （可选） 还可以配置[Azure 堆栈遥测设置](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)*之前*安装 ASDK。

打开已提升权限的 PowerShell 控制台，并在此部分以部署 ASDK 开发工具包主机上的运行命令。

> [!IMPORTANT] 
> ASDK 安装支持使用一个网络接口卡 (NIC) 进行网络连接。 如果有多个 NIC，请确保只启用一个（所有其他 NIC 均禁用），然后再运行部署脚本。

你可以部署与 Azure AD 的 Azure 堆栈或为标识提供程序的 Windows Server AD FS。 Azure Stack、资源提供程序和其他应用程序使用这二者的方式是相同的。

> [!TIP]
> 如果你不提供任何 （InstallAzureStackPOC.ps1 可选参数和下面的示例，请参阅此项） 的安装程序参数，则会提示所需的参数。

### <a name="deploy-azure-stack-using-azure-ad"></a>使用 Azure AD 部署 Azure Stack 
若要**使用 Azure AD 作为标识提供者**来部署 Azure Stack，必须通过直接方式或透明代理方式建立 Internet 连接。 

运行以下 PowerShell 命令，以便使用 Azure AD 部署开发工具包：

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

进行 ASDK 安装后数分钟，系统会提示输入 Azure AD 凭据。 必须提供 Azure AD 租户的全局管理员凭据。 

### <a name="deploy-azure-stack-using-ad-fs"></a>使用 AD FS 部署 Azure Stack 
若要**使用 AD FS 作为标识提供者**来部署开发工具包，请运行以下 PowerShell 命令（只需添加 -UseADFS 参数）： 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

在 AD FS 部署中，默认的标记目录服务用作标识提供者。 登录时使用的默认帐户是 azurestackadmin@azurestack.local，而密码则会设置为在 PowerShell 安装命令中提供的密码。

部署过程可能需要数小时，在此期间系统会自动重启一次。 如果部署成功，PowerShell 控制台会显示“完成操作‘部署’”。 如果部署失败，可以尝试使用 -rerun 参数再次运行脚本。 也可从头开始[重新部署 ASDK](asdk-redeploy.md)。

> [!IMPORTANT]
> 若要在 ASDK 主机重启后监视部署进度，必须以 AzureStack\AzureStackAdmin 身份登录。 如果在主机重启（并加入 azurestack.local 域）后以本地管理员身份登录，将看不到部署进度。 请勿重新运行部署，而应以 azurestack 身份登录，验证其是否正在运行。


#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 部署脚本示例
可以编写整个 Azure AD 部署的脚本。 下面是几个注释的示例，包括一些可选的参数。

如果 Azure AD 标识只与**一个** Azure AD 目录相关联，请执行以下代码：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

如果 Azure AD 标识与**多个** Azure AD 目录相关联，请执行以下代码：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

如果你的环境不具有已启用 DHCP，您必须包括以下其他参数 （提供的示例用法） 上方选项之一： 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 可选参数
|参数|必需/可选|说明|
|-----|-----|-----|
|AdminPassword|需要|在所有虚拟机（在开发工具包部署过程中创建）上设置本地管理员帐户和所有其他的用户帐户。 此密码必须与当前主机上的本地管理员密码匹配。|
|InfraAzureDirectoryTenantName|需要|设置租户目录。 使用此参数指定一个具体的目录，使 AAD 帐户有权在其中管理多个目录。 完整的 AAD 目录租户中的格式的名称。 onmicrosoft.com 或 Azure AD 验证自定义域名。|
|TimeServer|需要|使用此参数指定具体的时间服务器。 必须以有效的时间服务器 IP 地址的形式提供此参数。 服务器名称不受支持。|
|InfraAzureDirectoryTenantAdminCredential|可选|设置 Azure Active Directory 用户名和密码。 这些 Azure 凭据必须是组织 ID。|
|InfraAzureEnvironment|可选|选择 Azure 环境，以便将此 Azure Stack 部署注册到其中。 选项包括“公共 Azure”、“Azure - 中国”、“Azure - 美国政府”。|
|DNSForwarder|可选|在 Azure Stack 部署过程中会创建 DNS 服务器。 若要允许解决方案中的计算机解析标记外部的名称，请提供现有的基础结构 DNS 服务器。 标记内 DNS 服务器将未知的名称解析请求转发至此服务器。|
|NatIPv4Address|DHCP NAT 支持所需|为 MAS-BGPNAT01 设置静态 IP 地址。 仅当 DHCP 无法分配可以访问 Internet 的有效 IP 地址时，才使用此参数。|
|NatIPv4Subnet|DHCP NAT 支持所需|IP 子网前缀，适用于经 NAT 的 DHCP 支持。 仅当 DHCP 无法分配可以访问 Internet 的有效 IP 地址时，才使用此参数。|
|PublicVlanId|可选|设置 VLAN ID。 仅当主机和 MAS-BGPNAT01 必须通过配置 VLAN ID 来访问物理网络（和 Internet）时，才使用此参数。 例如，.\InstallAzureStackPOC.ps1 -Verbose -PublicVLan 305|
|重新运行|可选|使用此标志重新运行部署。 将使用所有以前的输入。 不支持重新输入以前提供的数据，因为已生成多个唯一的值并将其用于部署。|


## <a name="perform-post-deployment-configurations"></a>执行后期部署配置
安装后 ASDK，有几个建议的安装后检查和应做的配置更改。 你可以验证你的安装已成功使用测试 AzureStack cmdlet 中，安装并安装 Azure 堆栈 PowerShell 和 GitHub 工具。 

你应该重置以确保开发工具包宿主的密码不会评估期结束前过期的密码过期策略。

> [!NOTE]
> （可选） 还可以配置[Azure 堆栈遥测设置](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)*后*安装 ASDK。

**[后 ASDK 部署任务](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>注册 Azure
你必须向 Azure 注册 Azure 堆栈，以便您能够[下载 Azure 应用商店项](asdk-marketplace-item.md)到 Azure 堆栈。

**[向 Azure 注册 Azure 堆栈](asdk-register.md)**

## <a name="next-steps"></a>后续步骤
祝贺你！ 完成这些步骤后，你将有两种版本同时开发工具包环境[管理员](https://adminportal.local.azurestack.external)和[用户](https://portal.local.azurestack.external)门户。 

本教程介绍了如何：

> [!div class="checklist"]
> * 下载并提取部署包
> * 准备开发工具包主机计算机 
> * 执行后期部署配置
> * 注册 Azure

前进到下一步的教程，若要了解如何添加 Azure 堆栈应用商店项。

> [!div class="nextstepaction"]
> [添加 Azure 堆栈应用商店项](asdk-marketplace-item.md)

