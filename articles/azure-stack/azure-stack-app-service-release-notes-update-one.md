---
title: App Service 上 Azure 堆栈 update 1 发行说明 |Microsoft 文档
description: 了解什么是 Azure 堆栈上的 App Service 的其中一个更新中的已知的问题，以及在何处下载更新。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 538d31f5b50ee22c06ba22c78e1aa92281a3b212
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service 上 Azure 堆栈 update 1 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明描述的改进和在 Azure App Service 中的修补程序，Azure 堆栈 Update 1 和任何已知的问题。 已知的问题划分为与部署、 更新过程和生成 （安装后） 的问题直接相关的问题。

> [!IMPORTANT]
> 将 1802年更新应用于你的 Azure 堆栈集成系统，或在部署 Azure App Service 之前部署的最新的 Azure 堆栈开发工具包。
>
>

## <a name="build-reference"></a>内部版本参考

Azure 堆栈 Update 1 生成号上的应用程序服务已**69.0.13698.9**

### <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 现在，Azure 堆栈上的 Azure App Service 的新部署要求[三个使用者通配证书](azure-stack-app-service-before-you-get-started.md#get-certificates)由于处理的方式在其中 Kudu 的 SSO 现在在 Azure App Service 中的改进。  新的使用者是 * * *。 sso.appservice。<region>。<domainname>.<extension>**
>
>

请参阅[之前要开始文档](azure-stack-app-service-before-you-get-started.md)在开始部署之前。

### <a name="new-features-and-fixes"></a>新功能和修复

Azure 堆栈 Update 1 上的 azure 应用程序服务包括以下改进和修补程序：

- **高可用性的各种 Azure App Service** -跨部署 Azure 堆栈 1802年启用的更新的工作负荷故障域。  因此 App Service 基础结构是能够具有容错能力为跨容错域将已部署的。  默认情况下的 Azure App Service 的所有新部署将拥有此功能但对于在 Azure 堆栈 1802年之前完成的部署更新正在应用，请参阅到[应用服务容错域文档](azure-stack-app-service-fault-domain-update.md)

- **在现有虚拟网络中部署**-客户现在可在现有的虚拟网络中部署 Azure 堆栈上的 App Service。  在现有虚拟网络中部署使客户能够连接到 SQL Server 和文件服务器，所需的 Azure App Service，通过私有端口。  在部署期间，客户可以选择以将部署在现有的虚拟网络中，但是[必须创建子网以供 App Service](azure-stack-app-service-before-you-get-started.md#virtual-network)部署之前。

- 更新到**应用服务租户的管理员，函数门户和 Kudu 工具**。  与 Azure 堆栈门户 SDK 版本一致。

- **以下应用程序框架和工具的更新**:
    - 添加**.Net 核心 2.0**支持
    - 添加**Node.JS**版本：
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - 添加**NPM**版本：
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 添加**PHP**更新：
        - 5.6.32
        - 7.0.26 （x86 和 x64）
        - 7.1.12 （x86 和 x64）
    - 更新**Git for Windows**到 v 2.14.1
    - 更新**Mercurial**到 v4.5.0

  - 增加了对支持**仅 HTTPS**内应用程序服务租户门户中的自定义域功能的功能。 

  - 添加的验证的自定义存储选取器中，Azure 函数的存储连接 

#### <a name="fixes"></a>修复项

- 在创建时的脱机的部署包，客户不会再收到拒绝访问错误消息时从应用程序服务安装程序中打开文件夹

- 在应用程序服务租户门户中的自定义域功能中工作时，请解决问题。

- 阻止客户在安装过程中使用保留的管理员名称

- 启用了与 App Service 部署**加入域**文件服务器

- 改进的检索 Azure 堆栈根证书在脚本和现在验证在 App Service 安装程序中的根证书。

- 固定不正确的状态时订阅是返回到 Azure 资源管理器中删除该 Microsoft.Web 命名空间中的包含的资源。

### <a name="known-issues-with-the-deployment-process"></a>在部署过程的已知的问题

- 证书验证错误

某些客户在集成的系统中，由于在安装程序中的过度限制性验证部署时，到应用程序服务安装程序提供证书时都遇到了问题。  App Service 安装程序已被重新发布，客户应[下载更新的安装程序](https://aka.ms/appsvconmasinstaller)。  如果继续遇到问题验证与更新的安装程序的证书，请联系支持部门。

- 从集成的系统中检索 Azure 堆栈根证书的问题。

由于 Get AzureStackRootCert.ps1 中的发生错误，客户无法在没有安装的根证书的计算机上执行该脚本时检索 Azure 堆栈根证书。  该脚本还现在已重新发布，解决此问题，并请求客户[下载更新的帮助程序脚本](https://aka.ms/appsvconmashelpers)。  如果继续遇到问题检索与更新的脚本的根证书，请联系支持部门。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- 没有进行更新的 Azure App Service 上 Azure 堆栈 Update 1 已知的问题。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 槽交换不起作用

站点槽交换是中断此版本中。  若要还原功能，请完成以下步骤：

1. 修改 ControllersNSG 网络安全组添加到**允许**App Service 控制器实例的远程桌面连接。  将 AppService.local 替换为部署 App Service 中的资源组的名称。

    ```powershell
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

2. 浏览到**CN0 VM**下 Azure 堆栈管理员门户中的虚拟机和**单击连接**若要打开远程桌面会话的控制器实例。  使用在应用程序服务的部署过程中指定的凭据。
3. 启动**以管理员身份的 PowerShell**并执行以下脚本

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. 关闭远程桌面会话。
5. 还原 ControllersNSG 网络安全组添加到**拒绝**App Service 控制器实例的远程桌面连接。  将 AppService.local 替换为部署 App Service 中的资源组的名称。

    ```powershell

        Login-AzureRMAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>运行 Azure 堆栈上的 Azure App Service 的云管理员的已知的问题

中的文档，请参阅[Azure 堆栈 1802年发行说明](azure-stack-update-1802.md)

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure 堆栈概述 Azure App Service](azure-stack-app-service-overview.md)。
- 有关如何准备部署 Azure 堆栈上的应用程序服务的详细信息，请参阅[开始使用 Azure 堆栈上的 App Service 之前](azure-stack-app-service-before-you-get-started.md)。
