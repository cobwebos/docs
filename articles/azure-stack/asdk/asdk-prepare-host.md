---
title: 准备 Azure 堆栈开发工具包 (ASDK) 主机计算机 |Microsoft 文档
description: 描述如何准备 Azure 堆栈开发工具包 (ASDK) 主机计算机上 ASDK 安装。
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
ms.openlocfilehash: 5de25f574cb876701ffce74f1dca8c4bb9764157
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="prepare-the-asdk-host-computer"></a>准备 ASDK 主计算机
你可以在主计算机上安装 ASDK 之前，必须安装准备 ASDK 环境。 当已准备好开发工具包主机计算机时，它将从 CloudBuilder.vhdx 虚拟机硬盘空间，以开始 ASDK 部署进行启动。

## <a name="prepare-the-development-kit-host-computer"></a>准备开发工具包主机计算机
你可以在主计算机上安装 ASDK 之前，必须准备 ASDK 主机计算机环境。
1. 登录到你的开发工具包主机计算机本地管理员联系。
2. 确保已将 CloudBuilder.vhdx 文件移动到 C:\ 驱动器的根目录 (C:\CloudBuilder.vhdx)。
3. 运行以下脚本，将开发工具包安装程序文件 (asdk-installer.ps1) 从 [Azure Stack GitHub 工具存储库](https://github.com/Azure/AzureStack-Tools)下载到开发工具包主机上的 **C:\AzureStack_Installer** 文件夹：

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. 从提升了权限的 PowerShell 控制台启动 **C:\AzureStack_Installer\asdk-installer.ps1** 脚本，然后单击“准备环境”。

    ![](media/asdk-prepare-host/1.PNG) 

5. 上**选择 Cloudbuilder vhdx**页的安装程序，浏览到并选择**cloudbuilder.vhdx**你下载并提取中的文件[前面的步骤](asdk-download.md)。 在此页上，你还可以 （可选） 启用**将驱动程序添加**复选框，如果你需要其他驱动程序添加到开发工具包主机计算机。 单击“下一步”。  

    ![](media/asdk-prepare-host/2.PNG)

6. 上**可选设置**页上，提供本地管理员帐户的开发工具包主计算机的信息，然后单击**下一步**。 您还可以为以下可选设置提供值：
  - **Computername**：此选项设置开发工具包主机的名称。 名称必须符合 FQDN 要求，且长度不得超过 15 个字符。 默认值是由 Windows 生成的随机计算机名称。
  - **时区**：设置开发工具包主机的时区。 默认为“(UTC-8:00)太平洋时间(美国和加拿大)”。
  - **静态 IP 配置**：将部署设置为使用静态 IP 地址。 否则，当安装程序重启到 cloudbuilder.vhx 中时，会使用 DHCP 来配置网络接口。

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > 如果未提供此步骤中的本地管理员凭据，你将需要直接或 KVM 访问的主机之后在计算机重新启动设置的开发工具包的一部分。

7. 如果上一步选择了一个静态 IP 配置，则现在必须执行以下步骤：
    - 选择网络适配器。 确保可以连接到该适配器，然后单击“下一步”。
    - 确保 **IP 地址**、**网关**和 **DNS** 值正确，然后单击“下一步”。
13. 单击“下一步”，启动准备过程。
14. 但准备过程指示“已完成”时，单击“下一步”。
15. 单击**立即重新启动**开发工具包主机计算机启动到 cloudbuilder.vhdx 和[继续部署过程](asdk-install.md)。

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>后续步骤
[安装 ASDK](asdk-install.md)