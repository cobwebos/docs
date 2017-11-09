---
title: "部署 Azure 堆栈开发工具包 |Microsoft 文档"
description: "了解如何准备 Azure 堆栈开发工具包和运行 PowerShell 脚本来部署它。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>部署 Azure 堆栈开发工具包

*适用范围： Azure 堆栈开发工具包*

若要部署[Azure 堆栈开发工具包](azure-stack-poc.md)，必须完成以下步骤：

1. [下载部署包](https://azure.microsoft.com/overview/azure-stack/try/?v=try)获取 Cloudbuilder.vhdx。
2. [准备 cloudbuilder.vhdx](#prepare-the-development-kit-host)通过运行 asdk installer.ps1 脚本来配置要在其安装开发工具包的计算机 （开发工具包主机）。 此步骤后，开发工具包主机将启动到 Cloudbuilder.vhdx。
3. [部署开发工具包](#deploy-the-development-kit)开发工具包主机上。

> [!NOTE]
> 为获得最佳结果，即使你想要使用断开连接的 Azure 堆栈环境，则最好部署时连接到 internet。 这样一来，可以在部署时激活的 Windows Server 2016 评估版。
> 
> 

## <a name="download-and-extract-the-development-kit"></a>下载并提取开发工具包
1. 开始下载之前，请确保你的计算机满足以下先决条件：

   * 计算机必须具有至少为 60 GB 的可用磁盘空间。
   * [.NET framework 4.6 （或更高版本）](https://aka.ms/r6mkiy)必须安装。

2. [转到开始页上](https://azure.microsoft.com/overview/azure-stack/try/?v=try)，提供你的详细信息，然后单击**提交**。
3. 下**下载软件**，单击**Azure 堆栈开发工具包**。
4. 运行下载的 AzureStackDownloader.exe 文件。
5. 在**Azure 堆栈开发工具包程序下载程序**窗口中，按照步骤 1 至 5。
6. 下载完成后，单击**运行**以启动 MicrosoftAzureStackPOC.exe。
7. 查看许可协议屏幕和自解压缩程序向导的信息，然后单击**下一步**。
8. 查看隐私声明屏幕和自解压缩程序向导的信息，然后单击**下一步**。
9. 选择目标文件中提取，请单击**下一步**。
   * 默认值是： <drive letter>:\<当前文件夹 > \Microsoft Azure 堆栈
10. 查看目标位置屏幕和自解压缩程序向导的信息，然后单击**提取**提取 CloudBuilder.vhdx (~ 25 GB) 和 ThirdPartyLicenses.rtf 文件。 此过程将需要一些时间才能完成。

> [!NOTE]
> 提取文件后，你可以删除的 exe 和 bin 文件来恢复计算机上的空间。 或者，你可以将移动这些文件复制到另一个位置，因此，如果你需要重新部署你无需再次下载这些文件。
> 
> 

## <a name="prepare-the-development-kit-host"></a>准备开发工具包主机
1. 请确保你可以以物理方式连接到开发工具包主机，或具有物理控制台访问权限 （如 KVM)。 重新启动在下面的步骤 13 中的开发工具包主机后，你必须具有此类访问权限。
2. 请确保开发工具包主机满足[最低要求](azure-stack-deploy.md)。 你可以使用[部署适用于 Azure 堆栈检查器](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)以确认你的要求。
3. 登录以本地管理员身份向开发工具包主机。
4. 复制或移动到 C:\ 驱动器 (C:\CloudBuilder.vhdx) 的根目录的 CloudBuilder.vhdx 文件。
5. 在你开发工具包主机上，c:\AzureStack_Installer 文件夹中运行以下脚本来下载开发工具包安装程序文件 (asdk installer.ps1)。
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. 打开已提升权限的 PowerShell 控制台 > 运行 C:\AzureStack_Installer\asdk-installer.ps1 脚本 > 单击**准备环境**。
7. 上**选择 Cloudbuilder vhdx**的安装程序，浏览到并选择你在前面的步骤中下载的 cloudbuilder.vhdx 文件的页。
8. 可选： 检查**将驱动程序添加**框以指定包含所需主机的其他驱动程序的文件夹。
9. 上**可选设置**页上，为开发工具包主机提供的本地管理员帐户。 如果未提供这些凭据，将在下面的安装过程期间需要 KVM 访问的主机。
10. 同样，在**可选设置**页上，你可以选择以下设置：
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

## <a name="deploy-the-development-kit"></a>部署开发工具包
1. 登录以本地管理员身份到开发工具包主机。 使用前面的步骤中指定的凭据。

    > [!IMPORTANT]
    > 对于 Azure Active Directory 部署，Azure 堆栈需要访问 Internet，直接或通过透明的代理。 部署支持恰好一个 NIC 的网络。 如果你有多个 Nic，请确保只有一个处于启用状态 （，已禁用所有其他协议） 之前运行下一节中的部署脚本。
    
2. 打开已提升权限的 PowerShell 控制台 > 运行 \AzureStack_Installer\asdk-installer.ps1 脚本 （它可能是 Cloudbuilder.vhdx 中的不同驱动器上） > 单击**安装**。
3. 在**类型**框中，选择**Azure 云**或**ADFS**。
    - **Azure 云**: Azure Active Directory 是标识提供程序。 使用此参数来指定特定目录的 AAD 帐户具有全局管理员权限的位置。 一个 AAD 目录租户的完整名称。 例如，。.c o m。 
    - **ADFS**： 目录服务中的默认戳是标识提供程序，用于登录的默认帐户是azurestackadmin@azurestack.local，且要使用的密码正是作为安装的一部分提供。
4. 下**本地管理员密码**中**密码**框中，键入本地管理员密码 （这必须匹配当前配置的本地管理员密码），，然后单击**下一步**。
5. 选择用于开发工具包，然后单击一个网络适配器**下一步**。
6. 选择 DHCP 或 BGPNAT01 虚拟机的静态网络配置。
    - **DHCP** （默认值）： 虚拟机从 DHCP 服务器获取 IP 网络配置。
    - **静态**： 当 DHCP 无法分配 Azure 堆栈访问 Internet 的有效 IP 地址才会使用此选项。 必须与子网掩码长度 (例如，10.0.0.5/24) 指定静态 IP 地址。
7. （可选） 设置以下值：
    - **VLAN ID**： 设置 VLAN id。 如果主机和 AzS BGPNAT01 必须配置要访问的物理网络 （和 Internet） 的 VLAN ID，只能使用此选项。 
    - **DNS 转发器**： 作为 Azure 堆栈部署的一部分创建的 DNS 服务器。 若要允许解析外部戳的名称的解决方案内的计算机，提供你现有的基础结构 DNS 服务器。 在戳 DNS 服务器将转发到此服务器的未知的名称解析请求。
    - **时间服务器**： 这所必需的字段设置的时间服务器，并且必须是 IP 地址。 若要查找的时间服务器 IP 地址，请访问[pool.ntp.org](http:\\pool.ntp.org)或 ping time.windows.com。 
8. 单击“下一步”。 
9. 上**验证网络接口卡属性**页上，你将看到一个进度栏。 
    - 如果它规定**无法下载更新**，按照页上的说明。
    - 当属实**已完成**，单击**下一步**。
10. 上**摘要**页上，单击**部署**。
11. 如果你使用 Azure Active Directory 部署，你将需要输入你的 Azure Active Directory 全局管理员帐户凭据。
12. 部署过程可能需要几个小时，在此期间系统会自动重新启动一次。
   
   > [!IMPORTANT]
   > 如果你想要监视部署进度，请以 azurestack\AzureStackAdmin 身份登录。 如果您在登录以本地管理员在计算机加入到域后，你无法查看部署进度。 不要重新运行部署，而是以登录 azurestack\AzureStackAdmin 以验证它正在运行。
   > 
   > 
   
    如果部署成功，PowerShell 控制台显示：**完成： 操作部署**。
   
如果部署失败，你可以从同一个提升的 PowerShell 窗口使用以下 PowerShell 重新运行脚本：

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

此脚本将重新启动成功的最后一步中的部署。

或者，你可以[重新部署](azure-stack-redeploy.md)从零开始。


## <a name="reset-the-password-expiration-to-180-days"></a>重置为 180 天的密码过期

若要确保开发工具包宿主的密码不过期时间太短，请在部署后执行以下步骤：

若要从 Powershell 中更改的密码过期策略：
1. 在 Powershell 窗口中，运行命令，集 ADDefaultDomainPasswordPolicy-MaxPasswordAge 180.00:00:00-标识 azurestack.local

若要手动更改的密码过期策略：
1. 开发工具包在主机上，打开**组策略管理**并导航到**组策略管理**–**林： azurestack.local** –**域** – **azurestack.local**。
2. 右键单击**默认域策略**单击**编辑**。
3. 在组策略管理编辑器中，导航到**计算机配置**–**策略**– **Windows 设置**–**安全设置**–**帐户策略**–**密码策略**。
4. 在右窗格中，双击**密码最长期限**。
5. 在**密码最长期限属性**对话框中，更改**密码会过期中**值到 180，然后单击**确定**。


## <a name="next-steps"></a>后续步骤

[安装 PowerShell](azure-stack-powershell-configure-quickstart.md)

[Azure 堆栈注册你的 Azure 订阅](azure-stack-register.md)

[连接到 Azure Stack](azure-stack-connect-azure-stack.md)

