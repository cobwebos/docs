---
title: 创建 Azure 虚拟机后配置 WinRM |Azure Marketplace
description: 介绍了在创建 Azure 托管虚拟机后如何配置 Windows 远程管理 (WinRM)。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: ae5a55c6d640852cbd873bc6b36e502b5fe17165
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817955"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>在创建虚拟机后配置 WinRM

本文介绍了如何配置现有 Azure 托管虚拟机 (VM) 以启用基于 HTTPS 的 WinRM。  此配置仅适用于基于 Windows 的 VM，并且需要完成下面的两步过程：

1. 为基于 HTTPS 协议的 WinRM 启用端口流量。  你将在 Azure 门户中为 VM 配置此设置。
2. 通过运行提供的 PowerShell 脚本来配置 VM 以启用 WinRM。


## <a name="enabling-port-traffic"></a>启用端口流量

基于 HTTPS 协议的 WinRM 使用端口 5896，默认情况下，Azure 市场上提供的预配置 Windows VM 未启用此端口。 若要启用此协议，请使用以下步骤通过 [Azure 门户](https://portal.azure.com)向网络安全组 (NSG) 中添加一个新规则。  有关 NSG 的详细信息，请参阅[安全组](https://docs.microsoft.com/azure/virtual-network/security-overview)。

1.  导航到“虚拟机”>vm-name  < *>“设置/网络”边栏选项卡。* >  
2.  单击 NSG 名称（在此示例中为 **testvm11002**）以显示其属性：

    ![网络安全组属性](./media/nsg-properties.png)
 
3. 在“设置”下，选择“入站安全规则”以显示此边栏选项卡。
4. 单击“+添加”来为 TCP 端口 5986 创建名为 `WinRM_HTTPS` 的新规则。

    ![添加入站网络安全规则](./media/nsg-new-rule.png)

5. 提供值后，单击“确定”。  入站安全规则的列表应包含以下新条目。

    ![入站网络安全规则的列表](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>配置 VM 以启用 WinRM 

使用以下步骤在 Windows VM 上启用并配置 Windows 远程管理功能。   

1. 建立到 Azure 托管 VM 的远程桌面连接。  有关详细信息，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。  剩余步骤将在 VM 上运行。
2. 下载以下文件，并将其保存到 VM 上的一个文件夹：
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. 使用提升的权限（**以管理员身份运行**）打开 **PowerShell 控制台**。 
4. 运行以下命令并提供必需的参数：VM 的完全限定的域名 (FQDN)： <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Powershell 配置脚本 1](./media/powershell-file1.png)

    此脚本依赖于同一文件夹中的其他两个文件。


## <a name="next-steps"></a>后续步骤

在配置 WinRM 后，便可[从 VM 的构成 VHD 部署 VM](./cpp-deploy-vm-vhd.md)。
