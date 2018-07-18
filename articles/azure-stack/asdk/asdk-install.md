---
title: 安装 Azure Stack 开发工具包 (ASDK) | Microsoft Docs
description: 介绍如何安装 Azure Stack 开发工具包 (ASDK)。
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
ms.openlocfilehash: 74a81901c8ad38a84357a9f3c2e1d948aa81e8bc
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084325"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>安装 Azure Stack 开发工具包 (ASDK)
[准备 ASDK 主机](asdk-prepare-host.md)后，可以使用本文中的以下步骤将 ASDK 部署到 CloudBuilder.vhdx 映像中。

## <a name="install-the-asdk"></a>安装 ASDK
本文中的步骤说明如何使用图形用户界面 (GUI)（可通过下载并运行 **asdk-installer.ps1** PowerShell 脚本来获取）部署 ASDK。

> [!NOTE]
> Azure Stack 开发工具包的安装程序用户界面是一种基于 WCF 和 PowerShell 的开源脚本。


1. 在主机成功启动到 CloudBuilder.vhdx 映像之后，使用[准备用于 ASDK 安装的开发工具包主机](asdk-prepare-host.md)时指定的管理员凭据登录。 此凭据应与开发工具包主机本地管理员凭据相同。
2. 打开权限提升的 PowerShell 控制台，运行 **&lt;驱动器号>\AzureStack_Installer\asdk-installer.ps1** 脚本（现在可能位于 CloudBuilder.vhdx 映像中除 C:\ 以外的其他驱动器上）。 单击“安装”。

    ![](media/asdk-install/1.PNG) 

3. 在标识提供者的“类型”下拉框中，选择“Azure 云”或“AD FS”。 在“本地管理员密码”下的“密码”框中，键入本地管理员密码（必须与当前配置的本地管理员密码相符），然后单击“下一步”。
    - **Azure 云**：将 Azure Active Directory (Azure AD) 配置为标识提供者。 若要使用此选项，你需要 internet 连接，Azure AD 的完整名称的窗体中的目录租户*domainname*。 onmicrosoft.com 或 Azure AD 验证自定义域名，并且指定的全局管理员凭据目录。 
    - **AD FS**：将默认的戳记目录服务用作标识提供者。 登录时使用的默认帐户是 azurestackadmin@azurestack.local，要使用的密码是在设置过程中提供的。

    ![](media/asdk-install/2.PNG) 
    
    > [!NOTE]
    > 为获得最佳结果，即使你要使用离线 Azure Stack 环境并使用 AD FS 作为标识提供者，也最好是在连接到 Internet 的情况下安装 ASDK。 这样就可以在部署时激活开发工具包安装版随附的 Windows Server 2016 评估版。
4. 选择用于开发工具包的网络适配器，然后单击“下一步”。

    ![](media/asdk-install/3.PNG)

5. 为 BGPNAT01 虚拟机选择 DHCP 或静态网络配置。
    > [!TIP]
    > BGPNAT01 VM 是边缘路由器，提供适用于 Azure Stack 的 NAT 和 VPN 功能。

    - **DHCP**（默认）：虚拟机 DHCP 服务器获取 IP 网络配置。
    - **静态**：仅当 DHCP 无法为 Azure Stack 分配可访问 Internet 的有效 IP 地址时，才使用此选项。 **静态 IP 地址必须在 CIDR 格式中使用子网掩码长度来指定（例如，10.0.0.5/24）**。
    - 键入有效的**时间服务器 IP** 地址。 此必填字段设置可供开发工具包使用的时间服务器。 必须以有效的时间服务器 IP 地址的形式提供此参数。 服务器名称不受支持。

      > [!TIP]
      > 若要查找时间服务器 IP 地址，请访问 [pool.ntp.org](http://pool.ntp.org) 或 ping time.windows.com。 

    - **（可选）** 设置以下值：
        - **VLAN ID**：设置 VLAN ID。 仅当主机和 AzS-BGPNAT01 必须通过配置 VLAN ID 来访问物理网络（和 Internet）时，才使用此选项。 
        - **DNS 转发器**：在 Azure Stack 部署过程中会创建 DNS 服务器。 若要允许解决方案中的计算机解析标记外部的名称，请提供现有的基础结构 DNS 服务器。 标记内 DNS 服务器将未知的名称解析请求转发至此服务器。

    ![](media/asdk-install/4.PNG)

6. 在“验证网络接口卡属性”页上会看到一个进度栏。 完成验证后，单击“下一步”。

    ![](media/asdk-install/5.PNG)

9. 在“摘要”页上单击“部署”，开始在开发工具包主机上安装 ASDK。

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > 也可在这里复制将要用来安装开发工具包的 PowerShell 安装程序命令。 如果需要[使用 PowerShell 在主机上重新部署 ASDK](asdk-deploy-powershell.md)，此操作会有所帮助。

10. 如果执行 Azure AD 部署，系统会在安装开始后数分钟提示输入 Azure AD 全局管理员帐户凭据。

    ![](media/asdk-install/7.PNG)

11. 部署程序需要花费数小时，在此期间，主机会自动重新启动一次。 若要监视部署进度，请在开发工具包主机重启后，以 azurestack\AzureStackAdmin 身份登录。 如果部署成功，PowerShell 控制台会显示“COMPLETE: Action 'Deployment'”。 
    > [!IMPORTANT]
    > 如果在计算机加入域后以本地管理员身份登录，则看不到部署进度。 请勿重新运行部署，而应以 azurestack\AzureStackAdmin 身份登录，验证其是否正在运行。

    ![](media/asdk-install/8.PNG)

祝贺你，现已成功安装 ASDK！

如果部署出于某种原因失败，可以从头[重新部署](asdk-redeploy.md)，也可以在同一个权限提升的 PowerShell 窗口中使用以下 PowerShell 命令，从最后一个成功步骤重新开始部署：

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>后续步骤
[部署后的配置](asdk-post-deploy.md)
