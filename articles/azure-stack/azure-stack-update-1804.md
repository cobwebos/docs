---
title: Azure Stack 1804 更新 | Microsoft Docs
description: 了解 Azure Stack 集成系统 1804 更新的功能、已知问题和更新下载位置。
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
ms.date: 06/22/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: 9fc58d971db18db63e4dc76123ff1311b77e0191
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316427"
---
# <a name="azure-stack-1804-update"></a>Azure Stack 1804 更新

*适用于：Azure Stack 集成系统*

本文介绍了 1804 更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]        
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考    
Azure Stack 1804 更新内部版本号为 **20180513.1**。   

### <a name="new-features"></a>新增功能
此更新包含对 Azure Stack 的以下改进。

- <!-- 15028744 - IS -->  **Visual Studio 支持使用 AD FS 进行 Azure Stack 离线部署**。 在 Visual Studio 中，现在可以使用 AD FS 联合用户凭据来添加订阅和进行身份验证。 
 
- <!-- 1779474, 1779458 - IS --> **使用 Av2 和 F 系列虚拟机**。 Azure Stack 现在可以使用基于 Av2 系列和 F 系列虚拟机大小的虚拟机。 有关详细信息，请参阅 [Azure Stack 中支持的虚拟机大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)。 

- <!-- 1759172 - IS, ASDK --> **新的管理订阅**。 与 1804年有两个新的订阅类型可用在门户中。 这些新的订阅类型是从版本 1804年开始的新 Azure 堆栈安装除了默认提供程序订阅和可见。 *请不要使用此版本的 Azure 堆栈的这些新的订阅类型*。 我们将公布可以使用使用将来的更新这些订阅类型的可用性。 

  如果你更新到版本 1804 Azure 堆栈，则两个新的订阅类型不可见。 但是，Azure 堆栈的新部署集成系统，并且 Azure 堆栈开发工具包版本 1804年或更高版本的安装有权访问所有三个订阅类型。  

  这些新的订阅类型是更改的更大，若要保护的默认提供程序订阅，并以使其更轻松地部署共享的资源，如 SQL 托管服务器的一部分。 我们将此更大的更改以及将来的更新的更多部件添加到 Azure 堆栈时，部署在这些新的订阅类型下的资源可能会丢失。 

  现在可见的三个订阅类型包括：  
  - 默认提供程序订阅： 继续同时使用这种订阅类型。 
  - 计数订阅：*不使用此订阅类型。*
  - 消耗订阅：*不使用此订阅类型*

  



## <a name="fixed-issues"></a>修复的问题

- <!-- IS, ASDK -->  在管理员门户中，不再需要刷新“更新”磁贴才能显示信息。
 
- <!-- 2050709  -->  现在可以使用管理员门户来编辑 Blob 服务、表服务和队列服务的存储指标。
 
- <!-- IS, ASDK --> 在“网络”下单击“连接”以设置 VPN 连接时，“站点到站点(IPsec)”现在是唯一可用的选项。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**。

## <a name="additional-releases-timed-with-this-update"></a>随此更新一起推出的其他发行版  
以下版本现在可用，但不需要 Azure Stack 更新 1804。
- **更新到 Microsoft Azure 堆栈 System Center Operations Manager 监视包**。 适用于 Azure Stack 的 Microsoft System Center Operations Manager 监视包的新版本 (1.0.3.0) 已可供[下载](https://www.microsoft.com/download/details.aspx?id=55184)。 使用此版本，可以在添加连接的 Azure Stack 部署时使用服务主体。 此版本还提供更新管理体验，可让你直接从 Operations Manager 中执行修正操作。 还有新的仪表板，用于显示资源提供程序、缩放单元和缩放单元节点。

- **新的 Azure Stack 管理 PowerShell 版本 1.3.0**。  Azure Stack PowerShell 1.3.0 现已可供安装。 此版本针对所有管理资源提供程序提供了用来管理 Azure Stack 的命令。  在此版本中，某些内容将从 Azure Stack 工具 GitHub [存储库](https://github.com/Azure/AzureStack-Tools)中弃用。 

   有关安装的详细信息，请遵循 Azure Stack 模块 1.3.0 的[说明](azure-stack-powershell-install.md)或[帮助](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)内容。 

- **Azure Stack API Rest 参考的初始版本**。 [所有 Azure Stack 管理资源提供程序的 API 参考](https://docs.microsoft.com/rest/api/azure-stack/)现已发布。 


## <a name="before-you-begin"></a>开始之前    

### <a name="prerequisites"></a>必备组件
- 在应用 Azure Stack 1804 更新之前安装 Azure Stack [1803 更新](azure-stack-update-1803.md)。    

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题   
- 在安装期间 1804年更新，你可能会看到警报标题*错误 – FaultType UserAccounts.New 模板是缺失。*  可以放心地忽略这些警报。 更新到 1804 后，这些警报将自动关闭。   
 
- <!-- TBD - IS --> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md#plan-for-updates)。


### <a name="post-update-steps"></a>更新后步骤
安装之后 1804年，安装任何适用的修补程序。 有关详细信息，请查看以下知识库文章，以及我们的[服务策略](azure-stack-servicing-policy.md)。  
 - [KB 4341392-Azure 堆栈修补程序 1.0.180523.11](https://support.microsoft.com/en-us/help/4341392)。




### <a name="known-issues-post-installation"></a>已知问题（安装后）
下面是内部版本 **20180513.1** 的安装后已知问题。

#### <a name="portal"></a>门户
- <!-- 1272111 - IS --> 在安装或更新到此版本的 Azure 堆栈后，你可能不能在管理门户中查看 Azure 堆栈缩放单位。  
  解决方法： 使用 PowerShell，若要查看有关扩展单位的信息。 有关详细信息，请参阅[帮助](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)Azure 堆栈模块 1.3.0 内容。 

- <!-- 2332636 - IS -->  当你 Azure 堆栈标识系统并更新到此版本的 Azure 堆栈使用 AD FS 时，默认提供程序订阅的默认所有者将重置为内置**CloudAdmin**用户。  
  解决方法： 若要解决此问题，在安装此更新后，使用从步骤 3[触发器的自动化功能来配置声明提供方信任 Azure 堆栈中的](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1)过程重新设置默认提供程序订阅的所有者。   

- <!-- TBD - IS ASDK --> 某些管理订阅类型将不可用。  当你升级到此版本的 Azure 堆栈时，两个订阅类型，也是[引入了版本 1804年](#new-features)在控制台中不可见。 这是正常情况。 不可用的订阅类型*计数订阅*，和*消耗订阅*。 这些订阅类型在从版本 1804年开始的新 Azure 堆栈环境中可见，但尚不可供使用。 你应继续使用*默认提供程序*订阅类型。  


- <!-- TBD -  IS ASDK -->在管理员门户中[从下拉列表提交新的支持请求](azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 集成系统，请使用 https://aka.ms/newsupportrequest。

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/azure-stack-update-1804/breadcrumb.png) 

- <!-- TBD - IS --> 无法在管理员门户中查看计算或存储资源。 此问题的原因是更新安装过程中出错，导致系统错误地将更新报告为成功。 如果发生此问题，请联系 Microsoft 客户支持服务部门以寻求帮助。

- <!-- TBD - IS --> 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

- <!-- TBD - IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD - IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

- <!-- TBD - IS ASDK --> 在管理员门户中，可能会看到针对 *Microsoft.Update.Admin* 组件的严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

  可以放心地忽略此警报。 


#### <a name="health-and-monitoring"></a>运行状况和监视
- <!-- 1264761 - IS ASDK -->  你可能会看到的警报*运行状况控制器*组件具有下列详细信息：  

   警报 # 1:
   - 名称： 基础结构角色不正常
   - 严重性： 警告
   - 组件： 运行状况控制器
   - 描述： 检测信号扫描程序的运行状况控制器不可用。 这可能会影响运行状况报告和度量值。  

  警报 # 2:
   - 名称： 基础结构角色不正常
   - 严重性： 警告
   - 组件： 运行状况控制器
   - 描述： 错误扫描程序的运行状况控制器不可用。 这可能会影响运行状况报告和度量值。

  可放心忽略这两个警报。 它们将自动关闭随着时间的推移。  
 

#### <a name="compute"></a>计算
- <!-- TBD - IS --> 选择虚拟机大小进行虚拟机部署时，某些 F 系列 VM 大小在创建 VM 时所需的大小选择器中不可见。 以下 VM 大小不显示在选择器中：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
  解决方法是，使用下列方法之一部署 VM。 在每种方法中，都需要指定要使用的 VM 大小。

  - **Azure 资源管理器模板：** 使用模板时，请将模板中的 *vmSize* 设置为所需的 VM 大小。 例如，以下内容用来部署使用 *F32s_v2* 大小的 VM：  

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

- <!-- TBD - IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 D 系列 VM 的数据磁盘数不正确。 所有受支持的 D 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD - IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后，可以尝试重新下载以前无法下载的 VM 映像。

- <!-- TBD - IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  


#### <a name="networking"></a>网络
- <!-- 1766332 - IS ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 2388980 - IS ASDK --> 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果提高属于某个套餐和计划的网络资源的配额限制，而该套餐和计划与租户订阅相关联，则新的限制不会应用到该订阅。 但是，新限制会应用到在配额提高后创建的新订阅。 

  当计划已与某个订阅相关联时，若要解决此问题，请使用加载项计划来增大网络配额。 有关详细信息，请参阅如何[提供加载项计划](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

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

#### <a name="sql-and-mysql"></a>SQL 和 MySQL

- <!-- TBD - IS --> 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。  

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**或**层级**名称中不支持使用特殊字符（包括空格和句点）。


> [!NOTE]  
> <!-- TBD - IS --> 更新到 Azure Stack 1804 以后，可以继续使用以前部署的 SQL 和 MySQL 资源提供程序。  建议在新版本发布后更新 SQL 和 MySQL。 与 Azure Stack 一样，请将更新按顺序应用到 SQL 和 MySQL 资源提供程序。  例如，如果使用版本 1802，请先应用版本 1803，然后更新到 1804。      
>   
> 安装更新 1804 不会影响用户当前对 SQL 或 MySQL 资源提供程序的使用。
> 不管所用资源提供程序的版本如何，在其数据库中的用户数据不会受到影响，仍然可用。    



#### <a name="app-service"></a>应用服务
- <!-- 2352906 - IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- TBD - IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。

- <!-- TBD - IS ASDK --> 仅可以将应用程序服务部署到**默认提供程序订阅**在此时间。  在未来的更新 App Service 将部署到新计数的订阅中 Azure 堆栈 1804年引入和所有现有部署将迁移到此新订阅还。

#### <a name="usage"></a>使用情况  
- <!-- TBD - IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>下载更新
可从[此处](https://aka.ms/azurestackupdatedownload)下载 Azure Stack 1804 更新包。


## <a name="see-also"></a>另请参阅
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
