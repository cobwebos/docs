---
title: "部署 Azure 堆栈开发工具包 |Microsoft 文档"
description: "了解如何准备 Azure 堆栈开发工具包和运行 PowerShell 脚本来部署它。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2017
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 470a45aea253e1e238983527427b600117e413fe
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>部署 Azure 堆栈开发工具包

*适用范围： Azure 堆栈开发工具包*

若要部署[Azure 堆栈开发工具包](azure-stack-poc.md)，必须完成以下步骤：

1. [下载部署包](https://azure.microsoft.com/overview/azure-stack/try/?v=try)获取 Cloudbuilder.vhdx。
2. 准备 cloudbuilder.vhdx 配置要在其安装开发工具包的计算机 （开发工具包主机）。 此步骤后，开发工具包主机将启动到 Cloudbuilder.vhdx。
3. 部署开发工具包主机上的开发工具包。

> [!NOTE]
> 为获得最佳结果，即使你想要使用断开连接的 Azure 堆栈环境，则最好部署时连接到 internet。 这样一来，可以在部署时激活开发工具包安装中包括的 Windows Server 2016 评估版。

## <a name="download-and-extract-the-development-kit"></a>下载并提取开发工具包
1. 开始下载之前，请确保你的计算机满足以下先决条件：

  - 计算机必须具有至少 60 GB 的可用四个单独的、 完全相同的逻辑硬盘驱动器上的可用磁盘空间此外到操作系统磁盘。
  - [.NET framework 4.6 （或更高版本）](https://aka.ms/r6mkiy)必须安装。

2. [转到开始页上](https://azure.microsoft.com/overview/azure-stack/try/?v=try)其中可以下载 Azure 堆栈开发工具包，提供你的详细信息，然后单击**提交**。
3. 下载并运行[部署适用于 Azure 堆栈开发工具包检查器](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409)先决条件检查程序脚本。 此独立脚本将经历由安装程序完成 Azure 堆栈开发工具包的先决条件检查。 它提供一种方法，以确认你满足了硬件和软件要求、 之前下载的 Azure 堆栈开发工具包的更大的包。
4. 下**下载软件**，单击**Azure 堆栈开发工具包**。

  > [!NOTE]
  > ASDK 下载 (AzureStackDevelopmentKit.exe) 是 approximiately 10 GB 本身。 如果你选择还下载 Windows Server 2016 评估版本 ISO 文件，下载大小会导致增加大约 17 GB。 你可以使用该 ISO 文件创建并 ASDK 安装完成后，将 Windows Server 2016 的虚拟机映像添加到 Azure 堆栈应用商店。 请注意，此 Windows Server 2016 评估映像仅可用于 ASDK 并遵守 ASDK 许可条款。

5. 下载完成后，单击**运行**以启动 ASDK 自解压缩程序 (AzureStackDevelopmentKit.exe)。
6. 查看并接受从显示的许可协议**许可协议**自解压缩程序向导，然后单击页**下一步**。
7. 查看上显示的隐私语句信息**重要说明**自解压缩程序向导，然后单击页**下一步**。
8. 选择 Azure 堆栈安装程序文件提取到的位置**选择目标位置**自解压缩程序向导，然后单击页**下一步**。 默认位置是*当前文件夹*\Azure 堆栈开发工具包。 
9. 查看摘要上的目标位置**已准备好提取**自解压缩程序向导，然后单击页**提取**提取 CloudBuilder.vhdx (大约 25 GB) 和ThirdPartyLicenses.rtf 文件。 此过程将需要一些时间才能完成。
10. 复制或移动到 C:\ 驱动器 (C:\CloudBuilder.vhdx) 的根目录的 ASDK 主计算机上的 CloudBuilder.vhdx 文件。

> [!NOTE]
> 在提取文件后，你可以删除。EXE 和。BIN 文件以恢复硬盘空间。 或者，可以备份这些文件，因此，不需要再次下载这些文件，如果你需要重新部署 ASDK。

## <a name="deploy-the-asdk-using-a-guided-experience"></a>部署使用引导式的体验 ASDK
可以使用下载和运行 asdk installer.ps1 PowerShell 脚本提供的图形用户界面 (GUI) 部署 ASDK。

> [!NOTE]
> Azure 堆栈开发工具包的安装程序用户界面是基于 WCF 和 PowerShell 打开发布的脚本。

### <a name="prepare-the-development-kit-host-using-a-guided-user-experience"></a>准备使用指导的用户体验在开发工具包主机
你可以在主计算机上安装 ASDK 之前，必须准备 ASDK 环境。
1. 登录到你的 ASDK 主机计算机本地管理员联系。
2. 确保 CloudBuilder.vhdx 文件已被移动到 C:\ 驱动器 (C:\CloudBuilder.vhdx) 的根目录。
3. 运行以下脚本以从下载开发工具包安装程序文件 (asdk installer.ps1) [Azure 堆栈 GitHub 工具存储库](https://github.com/Azure/AzureStack-Tools)到**C:\AzureStack_Installer**上的文件夹你开发工具包主机计算机：

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 从提升的 PowerShell 控制台中，启动**C:\AzureStack_Installer\asdk-installer.ps1**编写脚本，并依次**准备环境**。
5. 上**选择 Cloudbuilder vhdx**页的安装程序，浏览到并选择**cloudbuilder.vhdx**你下载并在前面的步骤中提取的文件。 你可以还，可以选择此页上启用**将驱动程序添加**复选框，如果你需要其他驱动程序添加到开发工具包主机计算机。  
6. 上**可选设置**页上，提供用于开发工具包主计算机的本地管理员帐户。 

  > [!IMPORTANT]
  > 如果未提供这些凭据，你将需要直接或 KVM 访问的主机之后在计算机重新启动设置的开发工具包的一部分。

7. 你还可以通过以下方式提供这些可选设置上**可选设置**页：
    - **Computername**： 此选项设置开发工具包主机的名称。 名称必须符合 FQDN 要求，并且必须是长度不超过 15 个字符。 默认值是由 Windows 生成的随机计算机名称。
    - **时区**： 开发工具包主机设置的时间区域。 默认值为 (UTC-8:00) 太平洋时间 （美国和加拿大）。
    - **静态 IP 配置**： 设置您的部署，使用静态 IP 地址。 否则，当安装程序重新启动到 cloudbuilder.vhx，与 DHCP 配置的网络接口。
11. 单击“下一步”。
12. 如果上一步中选择一个静态 IP 配置，你现在必须：
    - 选择一个网络适配器。 请确保你可以连接到该适配器，再单击**下一步**。
    - 请确保**IP 地址**，**网关**，和**DNS**值正确无误，然后单击**下一步**。
13. 单击**下一步**开始准备过程。
14. 准备工作的指示时**已完成**，单击**下一步**。
15. 单击**立即重新启动**启动进入 cloudbuilder.vhdx 并继续部署过程。


### <a name="deploy-the-development-kit-using-a-guided-experience"></a>部署使用引导式的体验开发工具包
在准备好 ASDK 主机计算机后, ASDK 可以部署到 CloudBuilder.vhdx 映像使用以下步骤。 
1. 主机计算机已成功启动到 CloudBuilder.vhdx 映像后，使用前面的步骤中指定的管理员凭据登录。 
2. 打开已提升权限的 PowerShell 控制台并运行**\AzureStack_Installer\asdk-installer.ps1**脚本 （其中现在可能会在 CloudBuilder.vhdx 映像中的其他驱动器上）。 单击“安装” 。
3. 在**类型**下拉列表框中，选择**Azure 云**或**AD FS**。
    - **Azure 云**： 配置 Azure Active Directory (Azure AD) 为标识提供程序。 若要使用此选项，你将需要 internet 连接，Azure AD 的完整名称的窗体中的目录租户*domainname*。 onmicrosoft.com 或 Azure AD 验证自定义域的名称和全局管理员凭据指定的目录。 
    - **AD FS**： 目录服务将用作为标识提供程序的默认戳。 用于登录的默认帐户是azurestackadmin@azurestack.local，且要使用的密码正是作为安装的一部分提供。
4. 下**本地管理员密码**中**密码**框中，键入本地管理员密码 （这必须匹配当前配置的本地管理员密码），，然后单击**下一步**。
5. 选择用于开发工具包，然后单击一个网络适配器**下一步**。
6. 选择 DHCP 或 BGPNAT01 虚拟机的静态网络配置。
    - **DHCP** （默认值）： 虚拟机从 DHCP 服务器获取 IP 网络配置。
    - **静态**： 当 DHCP 无法分配 Azure 堆栈访问 Internet 的有效 IP 地址才会使用此选项。 必须使用的子网掩码长度以 CIDR 格式 (例如，10.0.0.5/24) 指定静态 IP 地址。
7. （可选） 设置以下值：
    - **VLAN ID**： 设置 VLAN id。 如果主机和 AzS BGPNAT01 必须配置要访问的物理网络 （和 internet） 的 VLAN ID，只能使用此选项。 
    - **DNS 转发器**： 作为 Azure 堆栈部署的一部分创建的 DNS 服务器。 若要允许解析外部戳的名称的解决方案内的计算机，提供你现有的基础结构 DNS 服务器。 在戳 DNS 服务器将转发到此服务器的未知的名称解析请求。
    - **时间服务器**： 这所必需的字段设置为供开发工具包的时间服务器。 此参数必须提供有效的时间服务器 IP 地址。 不支持服务器名称。
  
  > [!TIP]
  > 若要查找的时间服务器 IP 地址，请访问[pool.ntp.org](http:\\pool.ntp.org)或 ping time.windows.com。 
  
8. 单击“下一步”。 
9. 上**验证网络接口卡属性**页上，你将看到一个进度栏。 
    - 如果它规定**无法下载更新**，按照页上的说明。
    - 当属实**已完成**，单击**下一步**。
10. 上**摘要**页上，单击**部署**。 此处你还可以复制将用于安装的开发工具包的 PowerShell 安装程序命令。
11. 如果你使用 Azure AD 部署，系统将提示输入安装程序启动后的几分钟的 Azure AD 全局管理员帐户凭据。
12. 部署过程可能需要几个小时，在此期间系统会自动重新启动一次。 如果部署成功，PowerShell 控制台显示：**完成： 操作部署**。 如果部署失败，则可以可以[重新部署](azure-stack-redeploy.md)从从头开始或使用以下 PowerShell 命令，从同一个提升的 PowerShell 窗口，重新启动成功的最后一步中的部署：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

   > [!IMPORTANT]
   > 如果你想要监视部署进度，以登录 azurestack\AzureStackAdmin 开发工具包主机重新启动在安装过程时。 如果您在登录以本地管理员在计算机加入到域后，你无法查看部署进度。 不要重新运行部署，而是以登录 azurestack\AzureStackAdmin 以验证它正在运行。
   

## <a name="deploy-the-asdk-using-powershell"></a>部署使用 PowerShell ASDK
前面的步骤将指导你完成部署 ASDK 使用指导的用户体验。 或者，可以使用 PowerShell 部署开发工具包主机上的 ASDK 按照本部分中的步骤。

### <a name="configure-the-asdk-host-computer-to-boot-from-cloudbuildervhdx"></a>配置 ASDK 主机计算机启动时从 CloudBuilder.vhdx
这些命令将 ASDK 主机将计算机配置为从已下载并解压 Azure 堆栈虚拟硬盘 (CloudBuilder.vhdx) 启动。 完成这些步骤后，重新启动 ASDK 主机计算机。

1. 以管理员身份启动命令提示符。
2. 运行 `bcdedit /copy {current} /d "Azure Stack"`
3. 副本 (CTRL + C) CLSID 值返回，包括所需的 {} ' s。 此值称为 {CLSID}，并且需要进行粘贴 （CTRL + V 或右键单击） 中剩余步骤中。
4. 运行 `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx` 
5. 运行 `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx` 
6. 运行 `bcdedit /set {CLSID} detecthal on` 
7. 运行 `bcdedit /default {CLSID}`
8. 若要验证启动设置，请运行`bcdedit`。 
9. 请确保的 CloudBuilder.vhdx 文件已移动到 C:\ 驱动器 (C:\CloudBuilder.vhdx) 的根目录，并重新启动开发工具包主机计算机。 ASDK 主机重启时它现在应默认为从 CloudBuilder.vhdx VM 启动。 

### <a name="prepare-the-development-kit-host-using-powershell"></a>准备使用 PowerShell 在开发工具包主机 
开发工具包主机计算机已成功启动到 CloudBuilder.vhdx 映像后，可以打开已提升权限的 PowerShell 控制台，并在此部分以部署 ASDK 开发工具包主机上的运行命令。

> [!IMPORTANT] 
> ASDK 安装支持网络恰好一个网络接口卡 (NIC)。 如果你有多个 Nic，请确保只有一个处于启用状态 （，已禁用所有其他协议） 之前正在运行部署脚本。

为标识提供程序，你可以部署与 Azure AD 或 AD FS 的 Azure 堆栈。 Azure 堆栈、 资源提供程序和其他应用程序工作两个相同的方式。 若要了解有关使用 Azure 堆栈中的 AD FS 支持的功能的详细信息，请参阅[主要功能和概念](.\azure-stack-key-features.md)文章。

> [!TIP]
> 如果你不提供任何 （InstallAzureStackPOC.ps1 可选参数和下面的示例，请参阅此项） 的安装程序参数，系统将提示您为所需的参数。

### <a name="deploy-azure-stack-using-azure-ad"></a>部署 Azure 堆栈使用 Azure AD 
若要部署 Azure 堆栈**使用 Azure AD 作为标识提供程序**，直接或通过透明的代理，必须具有 internet 连接。 运行下面的 PowerShell 命令，以部署使用 Azure AD 的开发工具包：

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

  几分钟到 ASDK 安装将会提示你输入 Azure AD 凭据。 你必须提供你的 Azure AD 租户的全局管理员凭据。 

### <a name="deploy-azure-stack-using-ad-fs"></a>部署使用 AD FS 的 Azure 堆栈 
若要部署的开发工具包**为标识提供程序中使用 AD FS**，运行以下 PowerShell 命令 （你只需添加-UseADFS 参数）： 

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

在 AD FS 部署中，默认图章目录服务用作标识提供程序。 用于登录的默认帐户是azurestackadmin@azurestack.local，和的密码将设置为作为 PowerShell 安装程序命令的一部分提供的内容。

部署过程可能需要几个小时，在这段时间系统自动重新启动一次。 如果部署成功，PowerShell 控制台显示：**完成： 操作部署**。 如果部署失败，则可以尝试运行再次使用该脚本-重新运行的参数。 或者，你可以[重新部署 ASDK](.\azure-stack-redeploy.md)从零开始。
> [!IMPORTANT]
> 如果你想要监视部署进度，ASDK 主机重新启动后，必须为 AzureStack\AzureStackAdmin 签名。 如果你以本地管理员身份登录后重新启动 （和加入到 azurestack.local 域） 的主机计算机，你无法查看部署进度。 不要重新运行部署，而是以登录 azurestack 以验证它正在运行。
>

#### <a name="azure-ad-deployment-script-examples"></a>Azure AD 部署脚本示例
你可以编写脚本整个 Azure AD 部署。 下面是一些注释的示例包括一些可选的参数。

如果你的 Azure AD 身份仅与关联**一个**Azure AD 目录：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

如果你的 Azure AD 身份与**大于 1** Azure AD 目录：
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

如果你的环境**不**具有 DHCP 已启用，则您必须包括以下其他参数 （提供的示例用法） 上方选项之一： 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1 -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC.ps1 可选参数
|参数|必需/可选|说明|
|-----|-----|-----|
|AdminPassword|必选|作为开发工具包部署的一部分创建的所有虚拟机上设置本地管理员帐户和所有其他用户帐户。 此密码必须匹配当前主机上的本地管理员密码。|
|InfraAzureDirectoryTenantName|必选|设置租户目录。 使用此参数来指定特定目录的 AAD 帐户有权管理多个目录的位置。 完整的 AAD 目录租户中的格式的名称。 onmicrosoft.com 或 Azure AD 验证自定义域名。|
|时间服务器|必选|使用此参数来指定特定时间服务器。 此参数必须提供有效的时间服务器 IP 地址。 不支持服务器名称。|
|InfraAzureDirectoryTenantAdminCredential|可选|设置 Azure Active Directory 用户名和密码。 这些 Azure 凭据必须是一个 Org id。|
|InfraAzureEnvironment|可选|选择你想要注册此 Azure 堆栈部署的 Azure 环境。 选项包括公共 Azure，Azure-中国 Azure 的美国政府。|
|DNSForwarder|可选|作为 Azure 堆栈部署的一部分创建的 DNS 服务器。 若要允许解析外部戳的名称的解决方案内的计算机，提供你现有的基础结构 DNS 服务器。 在戳 DNS 服务器将转发到此服务器的未知的名称解析请求。|
|NatIPv4Address|DHCP NAT 支持所需的|MA BGPNAT01 设置静态 IP 地址。 如果 DHCP 无法分配一个有效的 IP 地址访问 Internet，仅使用此参数。|
|NatIPv4Subnet|DHCP NAT 支持所需的|NAT 支持通过使用适用于 DHCP 的 IP 子网前缀。 如果 DHCP 无法分配一个有效的 IP 地址访问 Internet，仅使用此参数。|
|PublicVlanId|可选|设置 VLAN id。 如果主机和 MA BGPNAT01 必须配置要访问的物理网络 （和 Internet） 的 VLAN ID，只能使用此参数。 例如，.\InstallAzureStackPOC.ps1-Verbose PublicVLan 305|
|重新运行|可选|使用此标志重新运行部署。 使用所有的前一个输入。 不支持以前提供的重新输入数据，因为几个唯一值是生成和用于部署。|

## <a name="activate-the-administrator-and-tenant-portals"></a>激活管理员和租户门户
在使用 Azure AD 的部署之后, 你必须激活 Azure 堆栈管理员和租户门户。 此激活许可提供 Azure 堆栈门户和 Azure 资源管理器的正确权限 （同意页上列出） 的所有用户的目录。

- 对于管理员门户中，导航到 https://adminportal.local.azurestack.external/guest/signup 阅读相关信息，，然后单击**接受**。 接受后，你可以添加服务管理员不是目录租户管理员。

- 对于租户门户中，导航到 https://portal.local.azurestack.external/guest/signup 阅读相关信息，，然后单击**接受**。 接受后，目录中的用户可以登录到租户门户。 

> [!NOTE] 
> 如果不激活使门户网站，只有目录管理员可以登录并使用门户网站。 如果任何其他用户登录时，他们会看到错误，告知他们管理员不具有向其他用户授予权限。 当管理员不本机属于 Azure 堆栈已注册到的目录时，则 Azure 堆栈目录必须追加到激活 URL。 例如，如果 Azure 堆栈注册到 fabrikam.onmicrosoft.com 和管理员用户是admin@contoso.com，导航到 https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com 激活门户。 

## <a name="reset-the-password-expiration-policy"></a>重置的密码过期策略 
若要确保开发工具包宿主的密码不过期评估期结束前，请按照以下步骤，在部署 ASDK 之后。

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>若要从 Powershell 中更改的密码过期策略：
从提升的 Powershell 控制台中，运行命令：

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>若要手动更改的密码过期策略：
1. 开发工具包在主机上，打开**组策略管理**并导航到**组策略管理**–**林： azurestack.local** –**域** – **azurestack.local**。
2. 右键单击**默认域策略**单击**编辑**。
3. 在组策略管理编辑器中，导航到**计算机配置**–**策略**– **Windows 设置**–**安全设置**–**帐户策略**–**密码策略**。
4. 在右窗格中，双击**密码最长期限**。
5. 在**密码最长期限属性**对话框中，更改**密码会过期中**值到 180，然后单击**确定**。


## <a name="next-steps"></a>后续步骤

[连接到 Azure Stack](azure-stack-connect-azure-stack.md)

[安装适用于 Azure 堆栈环境的 PowerShell](azure-stack-powershell-configure-quickstart.md)

[Azure 堆栈注册你的 Azure 订阅](azure-stack-register.md)



