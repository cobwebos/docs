---
title: Azure Stack 1807 更新 |Microsoft Docs
description: 了解如何针对 Azure Stack 集成系统，包括已知的问题的新变化 1807年更新和下载更新的位置。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: fe37a62d364d53d03a232b6fdc41e38a9ae2e30f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228212"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 更新

*适用于：Azure Stack 集成系统*

本文介绍 1807年更新包的内容。 此更新包括改进、 修复和已知的问题，对于此版本的 Azure Stack 中，以及在何处下载更新。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]        
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考    
Azure Stack 1807 更新内部版本号是**1.1807.XX.X**。  


### <a name="new-features"></a>新增功能
此更新包含对 Azure Stack 的以下改进。

- <!-- 1658937 | ASDK, IS --> **按预定义计划启动的备份**-作为一种设备，Azure Stack 现在可以自动触发基础结构备份定期以消除人为干预。 Azure Stack 还会自动将清除早于定义的保留期的备份的外部共享。 

- <!-- 2496385 | ASDK, IS --> **添加的数据传输到备份总时间的时间。**

-   <!-- 1702130 | ASDK, IS --> **备份的外部容量现在显示的外部共享的适当的容量。** (这以前是硬编码到 10 GB。)

- <!-- 2508488 |  IS   -->   展开容量增加[添加更多的缩放单位节点](azure-stack-add-scale-node.md)。



### <a name="fixed-issues"></a>修复的问题

- <!-- 448955 | IS ASDK --> 现在可以成功查询系统的部署 UTC + N 时区中的活动的日志。    

- <!-- 2319627 |  ASDK, IS --> 为备份配置参数 （路径/用户名/密码/加密密钥） 的预检查不再设置为备份配置的设置不正确。 (以前，备份设置不正确的设置，并且备份会将然后失败时 tirggered。)

- <!-- 2215948 |  ASDK, IS --> 备份列表现在刷新从外部共享手动删除备份。

- <!-- 2332636 - IS -->  更新到此版本不能再将重置默认提供商订阅的默认所有者为内置的 CloudAdmin 用户使用 AD FS 部署时。

- <!-- 2360715 |  ASDK, IS -->  如果设置了数据中心集成时，你无法再从共享访问 AD FS 的元数据文件。 有关详细信息，请参阅[通过提供联合身份验证元数据文件来设置 AD FS 集成](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)。 

- <!-- 2388980 | ASDK, IS --> 修复了以下问题阻止的用户分配到现有的公共 IP 地址已以前分配给网络接口或负载均衡器对新的网络接口或负载均衡器。  

-   <!--2292271 | ASDK, IS --> 修复了以下问题，已修改的配额限制未应用到现有订阅。  现在，当与租户订阅关联的计划并在提高配额限制的网络资源的是产品/服务的一部分，新的限制适用于预先存在的订阅，以及新的订阅。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**。


<!-- ### Additional releases timed with this update    -->



## <a name="before-you-begin"></a>开始之前    

### <a name="prerequisites"></a>必备组件
- 安装 Azure Stack [1805年更新](azure-stack-update-1805.md)应用 Azure Stack 1807 更新之前。  不会更新 1806年时出现。  

- 在开始安装更新 1807年之前，请运行[Test-azurestack](azure-stack-diagnostic-test.md)验证 Azure Stack 的状态并解决发现的任何操作问题。 另外，请查看活动警报，并解决所有需要采取措施的警报。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题   
- <!-- 2468613 - IS --> 此更新的安装期间，可能会看到警报标题*错误-FaultType UserAccounts.New 模板缺少。*  可以放心地忽略这些警报。 此更新安装完成后，这些警报将自动关闭。   

- <!-- 2489559 - IS --> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md#plan-for-updates)。


### <a name="post-update-steps"></a>更新后步骤
*没有更新 1807年更新后步骤。*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>已知问题（安装后）
下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户  
- <!-- 2551834 - IS, ASDK --> 在管理员门户或用户门户中选择存储帐户的“概述”时，“概要”窗格中的信息不会显示。  “概要”窗格显示有关帐户的信息，例如其资源组、位置和订阅 ID。  可以访问“概述”中的其他选项，例如“服务”和“监视”，以及“在资源管理器中打开”或“删除存储帐户”。

  若要查看未显示的信息，请使用 [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell cmdlet。

- <!-- 2551834 - IS, ASDK --> 在管理员门户或用户门户中选择存储帐户的“标记”时，信息无法加载且不显示。  

  若要查看未显示的信息，请使用 [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell cmdlet。 

- <!-- TBD - IS ASDK --> 某些管理订阅类型不可用。  将 Azure Stack 升级到此版本时，控制台中不会显示[版本 1804 引入](azure-stack-update-1804.md#new-features)的两个订阅类型。 这是正常情况。 不可用的订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。  

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> 无法在管理员门户中查看计算或存储资源。 此问题的原因是更新安装过程中出错，导致系统错误地将更新报告为成功。 如果发生此问题，请联系 Microsoft 客户支持服务部门以寻求帮助。

- <!-- TBD - IS --> 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

- <!-- TBD - IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD - IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


### <a name="health-and-monitoring"></a>运行状况和监视
- <!-- 1264761 - IS ASDK --> 可能会看到具有以下详细信息的*运行状况控制器*组件的警报：  

   警报 #1：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器检测信号扫描仪不可用。 这可能会影响运行状况报告和指标。  

  警报 #2：
   - 名称：基础结构角色不正常
   - 严重性：警告
   - 组件：运行状况控制器
   - 说明：运行状况控制器故障扫描仪不可用。 这可能会影响运行状况报告和指标。

  可以放心忽略这两条警报，它们在一段时间后会自动关闭。  

- <!-- 2368581 - IS. ASDK --> 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。


### <a name="compute"></a>计算
- <!-- 2724873 - IS --> 使用 PowerShell cmdlet 时**开始 AzsScaleUnitNode**或**停止 AzsScaleunitNode**若要管理的缩放单位，首次尝试启动或停止的缩放单位可能会失败。 在首次运行 cmdlet 时，运行第二次 cmdlet。 第二次运行应成功完成该操作。 

- <!-- 2494144 - IS, ASDK --> 选择虚拟机大小进行虚拟机部署时，某些 F 系列 VM 大小在创建 VM 时所需的大小选择器中不可见。 以下 VM 大小不显示在选择器中：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
  解决方法是，使用下列方法之一部署 VM。 在每种方法中，都需要指定要使用的 VM 大小。

  - **Azure 资源管理器模板：** 使用模板时，请将模板中的 *vmSize* 设置为想要使用的 VM 大小。 例如，以下条目用于部署使用 *F32s_v2* 大小的 VM：  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI：** 可以使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 命令并将 VM 大小指定为参数，类似于 `--size "Standard_F32s_v2"`。

  - **PowerShell：** 通过 PowerShell，可以将 [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) 与指定了 VM 大小的参数一起使用，类似于 `-VMSize "Standard_F32s_v2"`。


- <!-- TBD - IS ASDK --> 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- TBD - IS --> 通过转到“新建” > **“计算”** > “可用性集”在门户中创建可用性集时，只能创建 1 个包含 1 个容错域和 1 个更新域的可用性集。 解决方法是在创建新的虚拟机时，通过 PowerShell、CLI 或门户来创建可用性集。

- <!-- TBD - IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD - IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后可以重试先前失败的 VM 映像的下载。

- <!-- TBD - IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  


### <a name="networking"></a>网络  

- <!-- 1766332 - IS ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 2304134 IS ASDK --> 不能删除与 DNS 区域资源或路由表资源相关联的订阅。 若要成功地删除该订阅，必须先从租户订阅中删除 DNS 区域资源和路由表资源。


- <!-- 1902460 - IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 - IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- <!-- TBD - IS ASDK --> Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。

- <!-- 2096388 IS --> 不能使用管理员门户更新网络安全组的规则。

    针对应用服务的解决方法：如需通过远程桌面连接到控制器实例，请使用 PowerShell 修改网络安全组中的安全规则。  以下示例说明了如何先将配置设置为 *allow*，然后再还原为 *deny*：  

    - *Allow：*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn’t work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

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

    - *Deny：*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn’t work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

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


### <a name="sql-and-mysql"></a>SQL 和 MySQL

- <!-- TBD - IS --> 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。  

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**或**层级**名称中不支持使用特殊字符（包括空格和句点）。


> [!NOTE]  
> <!-- TBD - IS --> 更新到此版本的 Azure Stack 后，你可以继续使用以前部署的 SQL 和 MySQL 资源提供。  建议在新版本发布后更新 SQL 和 MySQL。 与 Azure Stack 一样，请将更新按顺序应用到 SQL 和 MySQL 资源提供程序。 例如，如果使用版本 1804年，首先应用版本为 1805，，然后更新到 1807年。      
>   
> 安装此更新不会影响当前用户的 SQL 或 MySQL 资源提供程序使用。
> 不管所用资源提供程序的版本如何，在其数据库中的用户数据不会受到影响，仍然可用。    



### <a name="app-service"></a>应用服务
- <!-- 2352906 - IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- 2489178 - IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。

- <!-- TBD - IS ASDK --> 目前，应用服务只能部署到“默认提供程序订阅”。 在将来的更新中，应用服务将部署到 Azure Stack 1804 中引入的新“计量订阅”。 支持使用“计量”时，现有的所有部署将迁移到此新订阅类型。


### <a name="usage"></a>使用情况  
- <!-- TBD - IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下载更新
可以从 Azure Stack 1807 更新包下载[此处](https://aka.ms/azurestackupdatedownload)。


## <a name="see-also"></a>另请参阅
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
