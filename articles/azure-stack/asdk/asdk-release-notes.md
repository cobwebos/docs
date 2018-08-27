---
title: Microsoft Azure Stack 开发工具包发行说明 |Microsoft Docs
description: Azure Stack 开发工具包的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: brenduns
ms.reviewer: misainat
ms.openlocfilehash: ed1c47830cbc4a629d827853c7d440b9bbd80d64
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888180"
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 开发工具包发行说明  
这些发行说明提供 Azure Stack 开发工具包的改进、修复和已知问题的相关信息。 如果不确定所运行的版本，可以[使用门户检查版本](.\.\azure-stack-updates.md#determine-the-current-version)。

> 请订阅 [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [源](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)，随时了解 ASDK 的新增功能。


## <a name="build-11807076"></a>生成 1.1807.0.76

### <a name="new-features"></a>新增功能
此内部版本包含以下适用于 Azure Stack 的改进和修复。  

- <!-- 1658937 | ASDK, IS --> **按预定义计划启动的备份**-作为一种设备，Azure Stack 现在可以自动触发基础结构备份定期以消除人为干预。 Azure Stack 还会自动将清除早于定义的保留期的备份的外部共享。 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](.\.\azure-stack-backup-enable-backup-powershell.md)。

- <!-- 2496385 | ASDK, IS -->  **添加的数据传输到备份总时间的时间。** 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](.\.\azure-stack-backup-enable-backup-powershell.md)。

-   <!-- 1702130 | ASDK, IS -->  **备份的外部容量现在显示的外部共享的适当的容量。** (这以前是硬编码到 10 GB。)有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](.\.\azure-stack-backup-enable-backup-powershell.md)。
 
- <!-- 2753130 |  IS, ASDK   -->  **Azure 资源管理器模板现在支持条件元素**-现在，你可以部署使用条件的 Azure 资源管理器模板中的资源。 您可以设计模板以部署基于条件，例如，计算参数值是否存在的资源。 有关使用模板作为一项条件的信息，请参阅[有条件地部署资源](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy)并[Azure 资源管理器模板的变量部分](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables)Azure 文档中。 

   此外可以使用模板[将资源部署到多个订阅或资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)。  

- <!--2753073 | IS, ASDK -->  **已更新的 Microsoft.Network API 资源版本支持**以支持的 API 版本 2017年-10-01，2015年-06-15 Azure Stack 网络资源中。  对资源版本 2017年-10-01 和 2015年-06-15 之间的支持不包括在此版本中，但将包含在将来的版本。  请参阅[Azure Stack 网络注意事项](.\.\user\azure-stack-network-differences.md)的功能差异。

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack 添加了对面向外部的 Azure Stack 基础结构终结点的反向 DNS 查找支持**（这是有关门户、 adminportal、 管理和 adminmanagement）。 这样，Azure Stack 外部终结点名称来解析来自某个 IP 地址。

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack 现在支持将其他网络接口添加到现有 VM。**  此功能可通过使用门户、 PowerShell 和 CLI。 有关详细信息，请参阅[添加或删除网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm)Azure 文档中。 

- <!-- 2222444 | IS, ASDK   -->  **已对网络用量计量表进行准确性和复原能力改进**。 网络使用情况计量器现在更准确，并且考虑到挂起的帐户订阅、 服务中断期间和争用条件。

- <!-- 2297790 | IS, ASDK -->  **对 Azure Stack syslog 客户端 （预览功能） 的改进**。 此客户端允许审核和日志到 syslog 服务器或安全信息和事件管理 (SIEM) 软件到 Azure Stack 外部的 Azure Stack 基础结构相关的转发。 Syslog 客户端现在支持使用纯文本或 TLS 1.2 加密，后者是默认配置的 TCP 协议。 可以使用仅限服务器或相互身份验证配置 TLS 连接。

  若要配置 syslog 客户端 （例如协议、 加密和身份验证） 的通信方式 syslog 服务器，请使用集 SyslogServer cmdlet。 此 cmdlet 可从特权终结点 (PEP)。 

  若要添加的 syslog 客户端 TLS 1.2 进行相互身份验证的客户端证书，请在 PEP 中使用集 SyslogClient cmdlet。

  借助此预览版，可以看到更大量的审核和警报。 

  此功能仍处于预览状态，因为不在生产环境中依赖于它。

  有关详细信息，请参阅[Azure Stack syslog 转发](.\.\azure-stack-integrate-security.md)。

- <!-- ####### | IS, ASDK -->  **Azure 资源管理器包括区域名称。** 此版本中，检索从 Azure 资源管理器的对象现在将包括区域名称属性。 如果现有的 PowerShell 脚本直接将对象传递给另一个 cmdlet，该脚本可能会产生错误并失败。 这是 Azure 资源管理器符合行为，并且需要调用客户端以减去相应的区域属性。 详细了解 Azure 资源管理器，请参阅[Azure 资源管理器文档](https://docs.microsoft.com/azure/azure-resource-manager/)。

- <!-- TBD | IS, ASDK -->  **委托的提供商之间移动订阅。** 现在可以属于相同的目录租户的新的或现有委派提供程序订阅之间移动订阅。 此外可以将属于默认提供商订阅的订阅移动到相同的目录租户中的委派提供程序订阅。 有关详细信息请参阅[在 Azure Stack 中委托产品](.\.\azure-stack-delegated-provider.md)。
 
- <!-- 2536808 IS ASDK --> **改进了创建 VM 时**适用于使用从 Azure marketplace 下载的映像创建的 Vm。

### <a name="fixed-issues"></a>修复的问题

- <!-- TBD | ASDK, IS --> 对更新过程进行了各种改进来使其更可靠。 此外，已向基础结构，从而提高了节点排出，因此在更新期间可尽量减少潜在的停机时间的工作负荷进行了修复。

-   <!--2292271 | ASDK, IS --> 修复了以下问题，已修改的配额限制未应用到现有订阅。  现在，当与用户订阅关联的计划并在提高配额限制的网络资源的是产品/服务的一部分，新的限制适用于预先存在的订阅，以及新的订阅。

- <!-- 2448955 | IS ASDK --> 现在可以成功查询系统的部署 UTC + N 时区中的活动的日志。    

- <!-- 2319627 |  ASDK, IS --> 为备份配置参数 （路径/用户名/密码/加密密钥） 的预检查不再设置为备份配置的设置不正确。 （以前，备份设置不正确的设置和备份然后将失败时触发。）

- <!-- 2215948 |  ASDK, IS --> 备份列表现在刷新从外部共享手动删除备份。

- <!-- 2360715 |  ASDK, IS -->  如果设置了数据中心集成时，你无法再从共享访问 AD FS 的元数据文件。 有关详细信息，请参阅[通过提供联合身份验证元数据文件来设置 AD FS 集成](.\.\azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)。 

- <!-- 2388980 | ASDK, IS --> 修复了以下问题阻止的用户分配到现有的公共 IP 地址已以前分配给网络接口或负载均衡器对新的网络接口或负载均衡器。  

- <!-- 2551834 - IS, ASDK --> 当选择*概述*存储帐户中的管理员或用户门户*Essentials*窗格现在显示预期的所有信息正确。 

- <!-- 2551834 - IS, ASDK --> 当选择*标记*管理员或用户门户中的存储帐户，有关的信息现在正确显示。

- <!-- TBD - IS ASDK --> 此版本的 Azure Stack 修复了阻止从 OEM 扩展包的驱动程序更新应用程序问题。

-   <!-- 2055809- IS ASDK --> 修复了以下问题，从而阻止你无法创建 VM 时计算边栏选项卡中删除 Vm。  

- <!--  2643962 IS ASDK -->  为警报*较低的内存容量*不会再出现不正确。

- <!--  TBD ASDK --> 托管的特权终结点 (PEP) 的虚拟机已增加到 4 GB。 在 ASDK 中，此虚拟机是名为 AzS-ERCS01。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**


<!-- ### Changes  -->
<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>已知问题

#### <a name="portal"></a>门户  
- <!--2760466 – IS  ASDK --> 在安装新的 Azure Stack 环境运行此版本时，警报，指示*需要激活*可能不会显示。 [激活](.\.\azure-stack-registration.md)是必需的然后才能使用 marketplace 联合。 

- <!-- TBD - IS ASDK --> 是两种管理订阅类型[版本 1804年中引入的](.\.\azure-stack-update-1804.md#new-features)不应使用。 订阅类型是**计数订阅**，并**消耗订阅**。 这些订阅类型是**计数订阅**，并**消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 应继续使用**默认提供商订阅**类型。

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD -  IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

- <!--  TBD | ASDK -->  现在，将获取 Azure Stack 部署默认时区设置为 UTC。 但是，它将自动恢复到 UTC 为默认值在安装过程中，可以安装 Azure Stack 时选择时区。

#### <a name="health-and-monitoring"></a>运行状况和监视
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

- <!-- TBD - IS. ASDK --> ERCS VM 的情况下，当特权终结点 (PEP) 上运行 Test-azurestack cmdlet，将生成警告消息。 您可以继续使用 ASDK。 

#### <a name="compute"></a>计算
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


- <!-- TBD -  IS ASDK --> 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 D 系列 VM 的数据磁盘数不正确。 所有受支持的 D 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD -  IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后可以重试先前失败的 VM 映像的下载。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。

- <!-- 2724961- IS ASDK --> 注册时**Microsoft.Insight**订阅设置中的资源提供程序并创建 Windows VM 与来宾 OS 诊断已启用，VM 的概述页不显示度量值的数据。 

   若要查找 VM 的 CPU 百分比图表类似的指标数据，请转到**指标**边栏选项卡，并显示所有受支持的 Windows VM 来宾指标。

#### <a name="networking"></a>网络
- <!-- 1766332 - IS, ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 1902460 -  IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 -  IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- <!-- 2702741 -  IS ASDK --> 使用动态分配方法部署的公共 Ip 不保证发出停止-解除分配后将保留。

- <!-- 2529607 - IS ASDK --> 在 Azure Stack 期间*机密轮换*，一段中的公共 IP 地址是无法访问为 2 到 5 分钟内。

-   <!-- 2664148 - IS ASDK --> 在租户位置使用 S2S VPN 隧道访问其虚拟机的情况下，它们可能会遇到连接尝试会如果已创建网关后，在本地子网已添加到本地网络网关失败的方案。 


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
- <!-- TBD - ASDK --> 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**名称中不支持使用特殊字符（包括空格和句点）。

#### <a name="app-service"></a>应用服务
- <!-- 2352906 - IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- TBD - IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。  

- <!-- TBD - IS ASDK --> 目前，应用服务只能部署到“默认提供程序订阅”。 在将来的更新中，应用服务将部署到 Azure Stack 1804 中引入的新“计量订阅”。 支持使用“计量”时，现有的所有部署将迁移到此新订阅类型。

#### <a name="usage"></a>使用情况  
- <!-- TBD -  IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- #### Identity -->






## <a name="build-11805147"></a>内部版本 1.1805.1.47

> [!TIP]  
> 根据客户反馈，没有用于 Microsoft Azure Stack 中的版本架构的更新。 从 1805 更新开始，新架构更能代表当前的云版本。  
>
> 版本架构目前为 *Version.YearYearMonthMonth.MinorVersion.BuildNumber*，其中的第二和第三组数字表示版本和发行版。 例如，1805.1 表示 1805 的正式发布 (RTM) 版本。  


### <a name="new-features"></a>新增功能
此内部版本包含以下适用于 Azure Stack 的改进和修复。  

- <!-- 2297790 - IS, ASDK --> **Azure Stack 现已将 *Syslog* 客户端**包含为预览功能。 此客户端允许将与 Azure Stack 基础结构相关的审核和安全日志转发到 Azure Stack 外部的 Syslog 服务器或安全信息与事件管理 (SIEM) 软件。 目前，Syslog 客户端仅支持通过默认端口 514 建立的未经身份验证的 UDP 连接。 每条 Syslog 消息的有效负载采用通用事件格式 (CEF)。

  若要配置 Syslog 客户端，请使用特权终结点中公开的 **Set-SyslogServer** cmdlet。

  此预览版中可能会出现以下三种警报。 当 Azure Stack 显示这些警报时，警报中会包含说明和补救指导。
  - 标题：代码完整性关闭  
  - 标题：代码完整性处于审核模式
  - 标题：已创建用户帐户

  此功能目前以预览版提供，在生产环境中请不要依赖此功能。   


### <a name="fixed-issues"></a>修复的问题
- 我们修复了管理门户中阻止[从下拉列表建立新支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)的问题。 此选项现在按预期工作。

- <!--  TBD ASDK --> 托管的特权终结点 (PEP) 的虚拟机已增加到 4 GB。 在 ASDK 中，此虚拟机是名为 AzS-ERCS01。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**


<!-- ### Changes  -->


<!--   ### Additional releases timed with this update  -->


### <a name="known-issues"></a>已知问题

#### <a name="portal"></a>门户
- <!-- 2551834 - IS, ASDK --> 在管理员门户或用户门户中选择存储帐户的“概述”时，“概要”窗格中的信息不会显示。  “概要”窗格显示有关帐户的信息，例如其资源组、位置和订阅 ID。  可以访问“概述”中的其他选项，例如“服务”和“监视”，以及“在资源管理器中打开”或“删除存储帐户”。  

  若要查看未显示的信息，请使用 [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell cmdlet。

- <!-- 2551834 - IS, ASDK --> 在管理员门户或用户门户中选择存储帐户的“标记”时，信息无法加载且不显示。  

  若要查看未显示的信息，请使用 [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell cmdlet。

- <!-- TBD - IS ASDK --> 不要使用新的管理订阅类型：“计量订阅”和“消耗订阅”。 这些新订阅类型是在版本 1804 中引入的，目前尚不可用。 请继续使用“默认提供程序”订阅类型。  
- <!-- TBD - IS ASDK --> 不能将驱动程序更新应用与此版本的 Azure Stack 使用 OEM 扩展包。  没有针对此问题的解决方法。
 
- <!-- TBD - IS ASDK --> 在管理员门户中[从下拉列表提交新的支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 开发工具包，请使用 https://aka.ms/azurestackforum。    

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD -  IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


#### <a name="health-and-monitoring"></a>运行状况和监视
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

    可以放心忽略警报 #1 和 #2，它们在一段时间后会自动关闭。 

  你可能还会看到针对*容量*的以下警报。 对于此警报，说明中标识的可用内存百分比可能会有所不同：  

  警报 #3：
   - 名称：内存容量低
   - 严重性：严重
   - 组件：容量
   - 说明：该区域已使用了 80.00% 以上的可用内存。 使用大量内存创建虚拟机可能会失败。  

  在此版本的 Azure Stack 中，该警报可能会错误地触发。 如果租户虚拟机继续成功部署，则可以放心地忽略此警报。 
  
  警报 #3 不会自动关闭。 如果你关闭此警报，Azure Stack 将在 15 分钟内创建相同的警报。  

- <!-- 2368581 - IS. ASDK --> 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。

- <!-- TBD - IS. ASDK --> ERCS VM 的情况下，当特权终结点 (PEP) 上运行 Test-azurestack cmdlet，将生成警告消息。 您可以继续使用 ASDK。 


#### <a name="compute"></a>计算
- <!-- TBD - IS, ASDK --> 选择虚拟机大小进行虚拟机部署时，某些 F 系列 VM 大小在创建 VM 时所需的大小选择器中不可见。 以下 VM 大小不显示在选择器中：*F8s_v2*、*F16s_v2*、*F32s_v2* 和 *F64s_v2*。  
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


- <!-- TBD -  IS ASDK --> 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 D 系列 VM 的数据磁盘数不正确。 所有受支持的 D 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD -  IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后可以重试先前失败的 VM 映像的下载。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。

#### <a name="networking"></a>网络
- <!-- TBD - IS ASDK --> 无法在管理员或用户门户中创建用户定义的路由。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell)。

- <!-- 1766332 - IS, ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 2388980 -  IS ASDK --> 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原始 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果提高属于某个套餐和计划的网络资源的配额限制，而该套餐和计划与租户订阅相关联，则新的限制不会应用到该订阅。 但是，新限制会应用到在配额提高后创建的新订阅。

  当计划已与某个订阅相关联时，若要解决此问题，请使用加载项计划来增大网络配额。 有关详细信息，请参阅如何[提供加载项计划](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 不能删除与 DNS 区域资源或路由表资源相关联的订阅。 若要成功地删除该订阅，必须先从租户订阅中删除 DNS 区域资源和路由表资源。


- <!-- 1902460 -  IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 -  IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- <!-- TBD -  IS ASDK --> Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
- <!-- TBD - ASDK --> 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**名称中不支持使用特殊字符（包括空格和句点）。

#### <a name="app-service"></a>应用服务
- <!-- 2352906 - IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- TBD - IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。  

- <!-- TBD - IS ASDK --> 目前，应用服务只能部署到“默认提供程序订阅”。 <!-- In a future update, App Service will deploy into the new *Metering subscription* that was introduced in Azure Stack 1804. When Metering is supported for use, all existing deployments will be migrated to this new subscription type. -->

#### <a name="usage"></a>使用情况  
- <!-- TBD -  IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!-- #### Identity -->


## <a name="build-201805131"></a>内部版本 20180513.1

### <a name="new-features"></a>新增功能
此内部版本包含以下适用于 Azure Stack 的改进和修复。  

- <!-- 1759172 - IS, ASDK --> **新的管理订阅**。 应用更新 1804 后，门户中会显示两个新的订阅类型。 这些新订阅类型是对“默认提供程序”订阅的补充，从版本 1804 开始，会显示在新的 Azure Stack 安装中。 请不要在此 Azure Stack 版本中使用这些新订阅类型。 <!-- We will announce the availability to use these subscription types in with a future update. -->

  这些新订阅类型会显示，但它们是某项重大更改的一部分，旨在保护“默认提供程序”订阅，以及方便部署 SQL 宿主服务器等共享资源。

  目前提供的三个订阅类型是：  
  - “默认提供程序”订阅：请继续使用此订阅类型。
  - “计量”订阅：请不要继续使用此订阅类型。
  - “消耗量”订阅：请不要继续使用此订阅类型。

### <a name="fixed-issues"></a>修复的问题
- <!-- IS, ASDK -->  在管理员门户中，不再需要刷新“更新”磁贴才能显示信息。

- <!-- 2050709 - IS, ASDK -->  现在可以使用管理员门户来编辑 Blob 服务、表服务和队列服务的存储指标。

- <!-- IS, ASDK --> 在“网络”下单击“连接”以设置 VPN 连接时，“站点到站点(IPsec)”现在是唯一可用的选项。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**

<!-- ### Changes  -->
### <a name="additional-releases-timed-with-this-update"></a>随此更新一起推出的其他发行版  
以下版本现在可用，但不需要 Azure Stack 更新 1804。
- **更新到 Microsoft Azure Stack 的 System Center Operations Manager 监视包**。 适用于 Azure Stack 的 Microsoft System Center Operations Manager 监视包的新版本 (1.0.3.0) 已可供[下载](https://www.microsoft.com/download/details.aspx?id=55184)。 使用此版本，可以在添加连接的 Azure Stack 部署时使用服务主体。 此版本还提供更新管理体验，可让你直接从 Operations Manager 中执行修正操作。 还有新的仪表板，用于显示资源提供程序、缩放单元和缩放单元节点。

- **新的 Azure Stack 管理 PowerShell 版本 1.3.0**。  Azure Stack PowerShell 1.3.0 现已可供安装。 此版本针对所有管理资源提供程序提供了用来管理 Azure Stack 的命令。  在此版本中，某些内容将从 Azure Stack 工具 GitHub [存储库](https://github.com/Azure/AzureStack-Tools)中弃用。

   有关安装的详细信息，请遵循 Azure Stack 模块 1.3.0 的[说明](.\.\azure-stack-powershell-install.md)或[帮助](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)内容。

- **Azure Stack API Rest 参考的初始版本**。 [所有 Azure Stack 管理资源提供程序的 API 参考](https://docs.microsoft.com/rest/api/azure-stack/)现已发布。

### <a name="known-issues"></a>已知问题

#### <a name="portal"></a>门户
- <!-- TBD - IS ASDK --> 在管理员门户中[从下拉列表提交新的支持请求](.\.\azure-stack-manage-portals.md#quick-access-to-help-and-support)的功能不可用。 请改用以下链接：     
    - 对于 Azure Stack 开发工具包，请使用 https://aka.ms/azurestackforum。    

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。
  ![痕迹](media/asdk-release-notes/breadcrumb.png)

- <!-- TBD -  IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD -  IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。

-   <!-- TBD -  IS ASDK --> 在管理员门户中，可能会看到针对 Microsoft.Update.Admin 组件的严重警报。 警报名称、说明和修正均显示为：  
    - 错误 - FaultType 为 ResourceProviderTimeout 的模板缺失。

    可以放心地忽略此警报。

#### <a name="health-and-monitoring"></a>运行状况和监视
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

  可以放心地忽略这两个警报。 它们将随着时间的推移自动关闭。  

#### <a name="marketplace"></a>市场
- 用户无需订阅就能浏览整个市场，并且将会看到计划和套餐等管理项。 对用户而言，这些项是非功能性的。

#### <a name="compute"></a>计算
- <!-- TBD -  IS ASDK --> 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。

- <!-- TBD -  IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD -  IS ASDK --> 无法创建 VM 映像时，可能会向 VM 映像计算边栏选项卡添加一个无法删除的故障项。

  解决方法是通过虚拟 VHD 创建新的 VM 映像，而该 VHD 则可以通过 Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB) 来创建。 此过程应该解决妨碍删除故障项的问题。 然后，在创建虚拟映像 15 后，就可以成功删除该故障项。

  然后可以重试先前失败的 VM 映像的下载。

- <!-- TBD -  IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 - IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。

#### <a name="networking"></a>网络
- <!-- 1766332 - IS, ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 2388980 -  IS ASDK --> 创建 VM 并将其与公共 IP 地址关联以后，就无法取消该 VM 与该 IP 地址的关联。 取消关联看似可以正常使用，但以前分配的公共 IP 地址仍与原始 VM 相关联。

  目前只能将新建的公共 IP 地址用于新建的 VM。

  即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原始 VM，而不是新的 VM。

- <!-- 2292271 - IS ASDK --> 如果提高属于某个套餐和计划的网络资源的配额限制，而该套餐和计划与租户订阅相关联，则新的限制不会应用到该订阅。 但是，新限制会应用到在配额提高后创建的新订阅。

  当计划已与某个订阅相关联时，若要解决此问题，请使用加载项计划来增大网络配额。 有关详细信息，请参阅如何[提供加载项计划](.\.\azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available)。

- <!-- 2304134 IS ASDK --> 不能删除与 DNS 区域资源或路由表资源相关联的订阅。 若要成功地删除该订阅，必须先从租户订阅中删除 DNS 区域资源和路由表资源。


- <!-- 1902460 -  IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 -  IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- <!-- TBD -  IS ASDK --> Azure Stack 不支持在部署某个 VM 实例后向该 VM 添加其他的网络接口。 如果该 VM 需要多个网络接口，这些接口必须在部署时定义。


#### <a name="sql-and-mysql"></a>SQL 和 MySQL
- <!-- TBD - ASDK --> 托管服务器的数据库必须专用于资源提供程序和用户工作负荷。 不能使用其他任何使用者（包括 应用服务）正在使用的实例。

- <!-- IS, ASDK --> 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**名称中不支持使用特殊字符（包括空格和句点）。

#### <a name="app-service"></a>应用服务
- <!-- TBD -  IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。

- <!-- TBD -  IS ASDK --> 若要横向扩展基础结构（辅助角色、管理角色、前端角色），必须按照计算发行说明中的说明来使用 PowerShell。

#### <a name="usage"></a>使用情况  
- <!-- TBD -  IS ASDK --> 公共 IP 地址使用计量数据针对每条记录显示相同的 *EventDateTime* 值，而不是创建记录时显示的 *TimeDate* 时间戳。 目前，无法使用此数据来执行公共 IP 地址用量的准确计帐。

<!--
#### Identity
-->



#### <a name="downloading-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具
- 使用 *invoke-webrequest* PowerShell cmdlet 从 Github 下载 Azure Stack 工具时，收到一个错误：     
    -  invoke-webrequest: 请求已终止: 未能创建 SSL/TLS 安全通道。     

  之所以发生此错误，是因为最近的 GitHub 支持弃用了 Tlsv1 和 Tlsv1.1 加密标准（PowerShell 的默认设置）。 有关详细信息，请参阅 [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/)（弱加密标准删除通知）。

<!-- #### Identity -->



