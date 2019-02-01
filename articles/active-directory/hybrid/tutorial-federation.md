---
title: 教程：将单个 AD 林环境联合到 Azure | Microsoft Docs
description: 演示如何使用联合设置混合标识环境。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 2e0afa4566896af98932ccd7a99ecf109ff2e56e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150847"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>教程：将单个 AD 林环境联合到云中

![创建](media/tutorial-federation/diagram.png)

以下教程将引导你使用联合创建混合标识环境。  然后，可以使用此环境进行测试或更熟悉混合标识的工作方式。

## <a name="prerequisites"></a>先决条件
以下是完成本教程所需的先决条件
- 安装了 [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) 的计算机。  建议在 [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) 或 [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) 计算机上执行此操作。
- [Azure 订阅](https://azure.microsoft.com/free)
- - 允许虚拟机与 Internet 通信的[外部网络适配器](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network)。
- Windows Server 2016 的副本
- 可以验证的[自定义域](../../active-directory/fundamentals/add-custom-domain.md)

> [!NOTE]
> 本教程使用 PowerShell 脚本，以便可以在最短的时间内创建教程环境。  每个脚本都使用在脚本开头声明的变量。  你可以而且应该更改变量以反映你的环境。
>
>在安装 Azure AD Connect 之前，所使用的脚本会创建常规 Active Directory 环境。  它们与所有教程相关。
>
> GitHub [此处](https://github.com/billmath/tutorial-phs)提供了本教程中使用的 PowerShell 脚本的副本。

## <a name="create-a-virtual-machine"></a>创建虚拟机
我们需要做的第一件事就是创建一个将用作我们的本地 Active Directory 服务器的虚拟机，以便启动并运行我们的混合标识环境。  

>[!NOTE]
>如果从未在主机上的 PowerShell 中运行脚本，则需要在运行脚本之前运行 `Set-ExecutionPolicy remotesigned` 并在 PowerShell 中说“是”。

请执行以下操作：

1. 以管理员身份打开 PowerShell ISE。
2. 运行以下脚本。

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>完成操作系统部署
要完成虚拟机的构建，需要完成操作系统安装。

1. Hyper-V 管理器，双击虚拟机
2. 单击“开始”按钮。
3.  系统将提示你“按任意键以从 CD 或 DVD 启动”。 继续执行此操作。
4. 在“Windows Server 启动”屏幕上，选择语言，然后单击“下一步”。
5. 单击“立即安装”。
6. 输入许可证密钥并单击“下一步”。
7. 勾选“我接受许可条款”，然后单击“下一步”。
8. 选择“自定义: 仅安装 Windows (高级)”
9. 点击“下一步”
10. 安装完成后，重新启动虚拟机，登录并运行 Windows 更新，以确保 VM 是最新的。  安装最新更新。

## <a name="install-active-directory-pre-requisites"></a>安装 Active Directory 的先决条件
现在我们已经启动了虚拟机，我们需要在安装 Active Directory 之前完成一些操作。  也就是说，我们需要重命名虚拟机，设置静态 IP 地址和 DNS 信息，并安装远程服务器管理工具。   请执行以下操作：

1. 以管理员身份打开 PowerShell ISE。
2. 运行 `Set-ExecutionPolicy remotesigned` 并对所有 [A] 说“是”。  按 Enter。
3. 运行以下脚本。

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>创建 Windows Server AD 环境
现在我们已经创建了 VM 并且已经重命名并且具有静态 IP 地址，我们可以继续安装和配置 Active Directory 域服务。  请执行以下操作：

1. 以管理员身份打开 PowerShell ISE。
2. 运行以下脚本。

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>创建 Windows Server AD 用户
现在我们拥有了 Active Directory 环境，我们还需要一个测试帐户。  此帐户将在我们的本地 AD 环境中创建，然后同步到 Azure AD。  请执行以下操作：

1. 以管理员身份打开 PowerShell ISE。
2. 运行以下脚本。

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-a-certificate-for-ad-fs"></a>创建用于 AD FS 的证书
现在我们将创建一个用于 AD FS 的 SSL 证书。  这将是一个自签名证书，仅用于测试目的。  Microsoft 不建议在生产环境中使用自签名证书。 请执行以下操作：

1. 以管理员身份打开 PowerShell ISE。
2. 运行以下脚本。

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
```

## <a name="create-an-azure-ad-tenant"></a>创建 Azure AD 租户
现在，我们需要创建 Azure AD 租户，以便我们可以将用户同步到云。  要创建新的 Azure AD 租户，请执行以下操作。

1. 浏览到 [Azure 门户](https://portal.azure.com)，使用具有 Azure 订阅的帐户登录。
2. 选择加号图标 (+) 并搜索“Azure Active Directory”。
3. 在搜索结果中选择“Azure Active Directory”。
4. 选择“创建”。</br>
![创建](media/tutorial-password-hash-sync/create1.png)</br>
5. 为组织提供名称以及初始域名。 然后选择“创建”。 随即创建目录。
6. 完成此操作后，单击此处链接以管理目录。

## <a name="create-a-global-administrator-in-azure-ad"></a>在 Azure AD 中创建全局管理员
现在我们有了 Azure AD 租户，我们将创建全局管理员帐户。  此帐户用于在 Azure AD Connect 安装期间创建 Azure AD 连接器帐户。  Azure AD 连接器帐户用于将信息写入 Azure AD。   要创建全局管理员帐户，请执行以下操作。

1.  在“管理”下，选择“用户”。</br>
![创建](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  选择“所有用户”，然后选择“+ 新建用户”。
3.  为此用户提供名称和用户名。 这将是租户的全局管理员。 还需要将“目录角色”更改为“全局管理员”。 还可以显示临时密码。 完成后，选择“创建”。</br>
![创建](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. 完成此操作后，使用新的全局管理员帐户和临时密码打开新的 Web 浏览器并登录 myapps.microsoft.com。
5. 将全局管理员的密码更改为你可以记住的密码。

## <a name="add-the-custom-domain-name-to-your-directory"></a>将自定义域名添加到目录
现在我们有了租户和全局管理员，我们需要添加自定义域，以便 Azure 可以对其进行验证。  请执行以下操作：

1. 请务必返回 [ Azure 门户](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)关闭“所有用户”边栏选项卡。
2. 在左侧选择“自定义域名”。
3. 选择“添加自定义域”。</br>
![联合身份验证](media/tutorial-federation/custom1.png)</br>
4. 在“自定义域名”上的框中输入自定义域的名称，然后单击“添加域”。
5. 在自定义域名屏幕上，将获得 TXT 或 MX 信息。  必须将此信息添加到你域下域注册机构的 DNS 信息中。  因此，需要转到域注册机构，在域的 DNS 设置中输入 TXT 或 MX 信息。  这将允许 Azure 验证你的域。  Azure 最多可能需要 24 小时才能验证它。  有关详细信息，请参阅[添加自定义域](../../active-directory/fundamentals/add-custom-domain.md)文档。</br>
![联合身份验证](media/tutorial-federation/custom2.png)</br>
6. 要确保已验证，请单击“验证”按钮。</br>
![联合身份验证](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>下载并安装 Azure AD Connect
立即下载并安装 Azure AD Connect。  安装完毕后，我们将完成快速安装。  请执行以下操作：

1. 下载 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. 导航到 **AzureADConnect.msi**并双击它。
3. 在“欢迎”屏幕上，选中对应的框，同意许可条款，并单击“继续”。  
4. 在“快速设置”屏幕上，单击“自定义”。  
5. 在“安装所需组件”屏幕上。 单击“安装”。  
6. 在“用户登录”屏幕上，选择“使用 AD FS 进行联合身份验证”，然后单击“下一步”。
![联合身份验证](media/tutorial-federation/fed1.png)

1. 在“连接到 Azure AD”屏幕上，输入我们在上面创建的全局管理员的用户名和密码，然后单击“下一步”。
2. 在“连接目录”屏幕上，单击“添加目录”。  然后选择“新建 AD 帐户”并输入 contoso\管理员的用户名和密码，然后单击“确定”。
3. 单击“下一步”。
4. 在“Azure AD 登录配置”屏幕上，选择“继续但不匹配已验证域的所有 UPN 后缀”，然后单击“下一步”。
5. 在“域和 OU 筛选”屏幕上，单击“下一步”。
6. 在“唯一标识用户”屏幕上，单击“下一步”。
7. 在“筛选用户和设备”屏幕上，单击“下一步”。
8. 在“可选功能”屏幕上，单击“下一步”。
9. 在“域管理员凭据”页上，输入 contoso\管理员用户名和密码，然后单击“下一步”。
10. 在“AD FS 场”屏幕上，确保选择“配置新的 AD FS 场”。
11. 选择“使用联合服务器上安装的证书”，然后单击“浏览”。
12. 在搜索框中输入“DC1”，并在找到它时选择它。  单击“确定” 。
13. 从“证书文件”下拉列表中，选择我们在上面创建的证书“adfs.contoso.com”。  单击“下一步”。
![联合身份验证](media/tutorial-federation/fed2.png)

1. 在“AD FS 服务器”屏幕上，单击“浏览”，在搜索框中输入“DC1”，并在找到它时选择它。  单击“确定” 。  单击“下一步”。
![联合身份验证](media/tutorial-federation/fed3.png)

1. 在“Web 应用代理服务器”屏幕上，单击“下一步”。
2. 在“AD FS 服务帐户”屏幕上，输入 contoso\管理员用户名和密码，然后单击“下一步”。
3. 在“Azure AD 域”屏幕上，从下拉列表中选择已验证的自定义域，然后单击“下一步”。
4. 在“准备好配置”屏幕上，单击“安装”。
5. 安装完成后，单击“退出” 。
6. 安装完成后，请注销并再次登录，即可使用 Synchronization Service Manager 或同步规则编辑器。


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>验证是否已创建用户并正在进行同步
我们现在将验证本地目录中的用户是否已经同步，现在是否存在于 Azure AD 租户中。  请注意，这可能需要几个小时才能完成。  要验证用户是否已同步，请执行以下操作。


1. 浏览到 [Azure 门户](https://portal.azure.com)，使用具有 Azure 订阅的帐户登录。
2. 在左侧选择“Azure Active Directory”
3. 在“管理”下，选择“用户”。
4. 验证租户中是否显示了新用户![同步](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>使用我们的某位用户测试登录

1.  浏览到 [https://myapps.microsoft.com](httpss://myapps.microsoft.com)
2. 使用在我们的新租户中创建的用户帐户登录。  需要使用以下格式登录：(user@domain.onmicrosoft.com)。 使用用户用于在本地登录的相同密码。
![验证](media/tutorial-password-hash-sync/verify1.png)

现在已经成功设置了一个混合标识环境，可以使用它来测试和熟悉 Azure 提供的功能。

## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [自定义设置](how-to-connect-install-custom.md)
- [Azure AD Connect 和联合身份验证](how-to-connect-fed-whatis.md)

