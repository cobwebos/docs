---
title: 安装 Azure 堆栈开发工具包 (ASDK) |Microsoft 文档
description: 描述如何安装 Azure 堆栈开发工具包 (ASDK)。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 7b8fe61731a9412c61152bc58e55deebb611d011
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>安装 Azure 堆栈开发工具包 (ASDK)
后[准备 ASDK 主机计算机](asdk-prepare-host.md)，ASDK 可以部署到此文章中使用以下步骤 CloudBuilder.vhdx 映像。

## <a name="install-the-asdk"></a>安装 ASDK
这篇文章中的步骤说明如何部署使用通过下载并运行提供的图形用户界面 (GUI) ASDK **asdk installer.ps1** PowerShell 脚本。

> [!NOTE]
> Azure 堆栈开发工具包的安装程序用户界面是基于 WCF 和 PowerShell 的开源脚本。


1. 使用管理员凭据登录主机计算机已成功启动到 CloudBuilder.vhdx 映像后，指定当你[准备开发工具包主机计算机](asdk-prepare-host.md)ASDK 安装。 这应该是开发工具包主机本地管理员凭据相同。
2. 打开已提升权限的 PowerShell 控制台并运行**&lt;驱动器号 > \AzureStack_Installer\asdk-installer.ps1**脚本 （其中现在可能会与 CloudBuilder.vhdx 映像中的 C:\ 不同的驱动器上）。 单击“安装”。

    ![](media/asdk-install/1.PNG) 

3. 在标识提供程序中**类型**下拉列表框中，选择**Azure 云**或**AD FS**。 下**本地管理员密码**中键入本地管理员密码 （必须匹配当前配置的本地管理员密码）**密码**框中，并依次**下一步**。
    - **Azure 云**：将 Azure Active Directory (Azure AD) 配置为标识提供者。 若要使用此选项，你需要 internet 连接，Azure AD 的完整名称的窗体中的目录租户*domainname*。 onmicrosoft.com 或 Azure AD 验证自定义域名，并且指定的全局管理员凭据目录。 
    - **AD FS**： 默认戳目录服务用作标识提供程序。 用于登录的默认帐户是azurestackadmin@azurestack.local，且要使用的密码正是作为安装程序的一部分提供。

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > 为获得最佳结果，即使你想要使用断开连接的 Azure 堆栈环境使用 AD FS 作为标识提供程序，则最好安装 ASDK 时连接到 internet。 这样就可以在部署时激活开发工具包安装版随附的 Windows Server 2016 评估版。
4. 选择用于开发工具包的网络适配器，然后单击“下一步”。

    ![](media/asdk-install/3.PNG)

5. 为 BGPNAT01 虚拟机选择 DHCP 或静态网络配置。
    > [!TIP]
    > BGPNAT01 VM 是 Azure 堆栈提供 NAT 和 VPN 功能的边缘路由器。

    - **DHCP**（默认）：虚拟机 DHCP 服务器获取 IP 网络配置。
    - **静态**：仅当 DHCP 无法为 Azure Stack 分配可访问 Internet 的有效 IP 地址时，才使用此选项。 **必须使用的子网掩码长度以 CIDR 格式 (例如，10.0.0.5/24) 指定静态 IP 地址**。
    - 中是有效类型**时间服务器 IP**地址。 此必需的字段设置为供开发工具包的时间服务器。 必须以有效的时间服务器 IP 地址的形式提供此参数。 服务器名称不受支持。

      > [!TIP]
      > 若要查找时间服务器 IP 地址，请访问 [pool.ntp.org](http:\\pool.ntp.org) 或 ping time.windows.com。 

    - **（可选)**，设置以下值：
        - **VLAN ID**：设置 VLAN ID。 仅当主机和 AzS-BGPNAT01 必须通过配置 VLAN ID 来访问物理网络（和 Internet）时，才使用此选项。 
        - **DNS 转发器**：在 Azure Stack 部署过程中会创建 DNS 服务器。 若要允许解决方案中的计算机解析标记外部的名称，请提供现有的基础结构 DNS 服务器。 标记内 DNS 服务器将未知的名称解析请求转发至此服务器。

    ![](media/asdk-install/4.PNG)

6. 在“验证网络接口卡属性”页上会看到一个进度栏。 在完成验证后，单击**下一步**。

    ![](media/asdk-install/5.PNG)

9. 上**摘要**页上，单击**部署**开发工具包主机计算机上启动 ASDK 安装。

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > 也可在这里复制将要用来安装开发工具包的 PowerShell 安装程序命令。 这非常有用，如果您曾需要到[重新部署使用 PowerShell 的主机计算机上 ASDK](asdk-deploy-powershell.md)。

10. 如果要执行的 Azure AD 部署，系统将提示输入安装程序启动后的几分钟的 Azure AD 全局管理员帐户凭据。

    ![](media/asdk-install/7.PNG)

11. 部署过程将需要几个小时，在这段时间主计算机将自动重新启动一次。 如果你想要监视部署进度，以登录 azurestack\AzureStackAdmin 开发工具包主机重启后。 如果部署成功，PowerShell 控制台显示：**完成： 操作部署**。 
    > [!IMPORTANT]
    > 如果在计算机加入域后以本地管理员身份登录，则看不到部署进度。 请勿重新运行部署，而应以 azurestack\AzureStackAdmin 身份登录，验证其是否正在运行。

    ![](media/asdk-install/8.PNG)

祝贺你，你已成功安装 ASDK ！

如果出于某种原因，部署将失败，则可以[重新部署](asdk-redeploy.md)从从头开始或使用以下 PowerShell 命令，从同一个提升的 PowerShell 窗口，重新启动成功的最后一步中的部署：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>后续步骤
[部署配置后](asdk-post-deploy.md)