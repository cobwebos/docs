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
ms.date: 08/27/2018
ms.author: brenduns
ms.reviewer: justini
ms.openlocfilehash: afbe3ff2e6be4e03f8de8ac2490922c3ec788733
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091374"
---
# <a name="azure-stack-1807-update"></a>Azure Stack 1807 更新

*适用于：Azure Stack 集成系统*

本文介绍 1807年更新包的内容。 此更新包括改进、 修复和已知的问题，对于此版本的 Azure Stack 中，以及在何处下载更新。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1807 更新内部版本号是**1.1807.0.76**。  

### <a name="new-features"></a>新增功能

此更新包含对 Azure Stack 的以下改进。

- <!-- 1658937 | ASDK, IS --> **按预定义计划启动的备份**-作为一种设备，Azure Stack 现在可以自动触发基础结构备份定期以消除人为干预。 Azure Stack 还会自动将清除早于定义的保留期的备份的外部共享。 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md)。

- <!-- 2496385 | ASDK, IS -->  **添加的数据传输到备份总时间的时间。** 有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md)。

-   <!-- 1702130 | ASDK, IS -->  **备份的外部容量现在显示的外部共享的适当的容量。** (这以前是硬编码到 10 GB。)有关详细信息，请参阅[使用 PowerShell 为 Azure Stack 启用备份](azure-stack-backup-enable-backup-powershell.md)。

- <!-- 2508488 |  IS   -->  **扩展容量**由[添加其他缩放单位节点](azure-stack-add-scale-node.md)。

- <!-- 2753130 |  IS, ASDK   -->  **Azure 资源管理器模板现在支持条件元素**-现在，你可以部署使用条件的 Azure 资源管理器模板中的资源。 您可以设计模板以部署基于条件，例如，计算参数值是否存在的资源。 有关使用模板作为一项条件的信息，请参阅[有条件地部署资源](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy)并[Azure 资源管理器模板的变量部分](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables)Azure 文档中。 

   此外可以使用模板[将资源部署到多个订阅或资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment)。  

- <!--2753073 | IS, ASDK -->  **已更新的 Microsoft.Network API 资源版本支持**以支持的 API 版本 2017年-10-01，2015年-06-15 Azure Stack 网络资源中。  对资源版本 2017年-10-01 和 2015年-06-15 之间的支持不包括在此版本中，但将包含在将来的版本。  请参阅[Azure Stack 网络注意事项](user/azure-stack-network-differences.md)的功能差异。

- <!-- 2272116 | IS, ASDK   -->  **Azure Stack 添加了对面向外部的 Azure Stack 基础结构终结点的反向 DNS 查找支持**（这是有关门户、 adminportal、 管理和 adminmanagement）。 这样，Azure Stack 外部终结点名称来解析来自某个 IP 地址。

- <!-- 2780899 |  IS, ASDK   --> **Azure Stack 现在支持将其他网络接口添加到现有 VM。**  此功能可通过使用门户、 PowerShell 和 CLI。 有关详细信息，请参阅[添加或删除网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm)Azure 文档中。 

- <!-- 2222444 | IS, ASDK   -->  **已对网络用量计量表进行准确性和复原能力改进**。  网络使用情况计量器现在更准确，并且考虑到挂起的帐户订阅、 服务中断期间和争用条件。

- <!-- 2753080 | IS -->  **更新可用的通知。** 连接 Azure Stack 部署现在会定期检查受保护的终结点，并确定是否有可用于你的云的更新。 手动检查并导入新的更新后像，更新磁贴中会显示此通知。 详细了解[适用于 Azure Stack 中管理更新](azure-stack-updates.md)。

- <!-- 2297790 | IS, ASDK -->  **对 Azure Stack syslog 客户端 （预览功能） 的改进**。 此客户端允许审核和日志到 syslog 服务器或安全信息和事件管理 (SIEM) 软件到 Azure Stack 外部的 Azure Stack 基础结构相关的转发。 Syslog 客户端现在支持使用纯文本或 TLS 1.2 加密，后者是默认配置的 TCP 协议。 可以使用仅限服务器或相互身份验证配置 TLS 连接。

  若要配置 syslog 客户端与 syslog 服务器 （例如协议、 加密和身份验证） 进行通信的方式，请使用*集 SyslogServer* cmdlet。 此 cmdlet 可从特权终结点 (PEP)。

  若要添加的 syslog 客户端 TLS 1.2 进行相互身份验证的客户端证书，请在 PEP 中使用集 SyslogClient cmdlet。

  借助此预览版，可以看到更大量的审核和警报。 

  由于此功能仍处于预览状态，不依赖于它在生产环境中。

  有关详细信息，请参阅[Azure Stack syslog 转发](azure-stack-integrate-security.md)。

<!-- - | ####### | IS, ASDK |  **Azure Resource Manager includes the region name.** With this release, objects retrieved from the Azure Resource Manager will now include the region name attribute. If an existing PowerShell script directly passes the object to another cmdlet, the script may produce an error and fail. This is Azure Resource Manager compliant behavior, and requires the calling client to subtract the region attribute. For more information about the Azure Resource Manager see [Azure Resource Manager Documentation](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

- <!-- TBD | IS, ASDK -->  **委托的提供商功能的更改。** 从开始 1807年委派提供程序模型简化以便更好地符合 Azure 经销商模型和委托的提供商将无法再创建其他委托的提供商，实质上是平展模型并使委派提供程序在单个级别上提供的功能。 若要启用过渡到新的模型和订阅的管理，用户订阅可以现在新的或现有委派提供程序在订阅之间移动属于相同的目录租户。 此外可以将用户订阅属于默认提供商订阅移动到相同的目录租户中的委派提供程序订阅。  有关详细信息请参阅[在 Azure Stack 中委托产品](azure-stack-delegated-provider.md)。

- <!-- 2536808 IS ASDK --> **改进了创建 VM 时**适用于使用从 Azure marketplace 下载的映像创建的 Vm。

- <!-- TBD | IS, ASDK -->  **Azure Stack 容量规划器的可用性改进**。 Azure Stack [Capacity Planner](http://aka.ms/azstackcapacityplanner)现在提供了用于定义解决方案 Sku 时输入 S2D 缓存和 S2D 容量简化的体验。 1000 VM 限制已被取消。


### <a name="fixed-issues"></a>修复的问题

- <!-- TBD | ASDK, IS --> 对更新过程进行了各种改进来使其更可靠。 此外，修补程序已对底层基础结构，其最大程度减少潜在的停机时间的工作负荷的更新过程。

- <!--2292271 | ASDK, IS --> 修复了以下问题，已修改的配额限制未应用到现有订阅。 现在，当与用户订阅关联的计划并在提高配额限制的网络资源的是产品/服务的一部分，新的限制适用于预先存在的订阅，以及新的订阅。

- <!-- 448955 | IS ASDK --> 现在可以成功查询系统的部署 UTC + N 时区中的活动的日志。    

- <!-- 2319627 |  ASDK, IS --> 为备份配置参数 （路径/用户名/密码/加密密钥） 的预检查不再设置为备份配置的设置不正确。 （以前，备份设置不正确的设置和备份然后将失败时触发。）

- <!-- 2215948 |  ASDK, IS --> 备份列表现在刷新从外部共享手动删除备份。

- <!-- 2332636 | IS -->  此版本的更新不再会将默认提供商订阅的默认所有者的重置为内置**CloudAdmin**用户使用 AD FS 部署时。

- <!-- 2360715 |  ASDK, IS -->  如果设置了数据中心集成时，无法再从 Azure 文件共享访问 AD FS 的元数据文件。 有关详细信息，请参阅[通过提供联合身份验证元数据文件来设置 AD FS 集成](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file)。 

- <!-- 2388980 | ASDK, IS --> 修复了以下问题阻止的用户分配到现有的公共 IP 地址已以前分配给网络接口或负载均衡器对新的网络接口或负载均衡器。  

- <!-- 2551834 - IS, ASDK --> 当选择*概述*存储帐户中的管理员或用户门户*Essentials*窗格现在显示预期的所有信息正确。 

- <!-- 2551834 - IS, ASDK --> 当选择*标记*管理员或用户门户中的存储帐户，有关的信息现在正确显示。

- <!-- TBD - IS ASDK --> 此版本的 Azure Stack 修复了阻止从 OEM 扩展包的驱动程序更新应用程序问题。

-   <!-- 2055809- IS ASDK --> 修复了以下问题，从而阻止你无法创建 VM 时计算边栏选项卡中删除 Vm。  

- <!--  2643962 IS ASDK -->  为警报*较低的内存容量*不会再出现不正确。

- 针对性能、稳定性、安全性以及 Azure Stack 所用操作系统的**各种修复**。


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>通用漏洞和披露
Azure Stack 使用服务器核心安装的 Windows Server 2016 主机基础结构。 此版本中将以下 Windows Server 2016 更新基础结构服务器上安装适用于 Azure Stack: 
- [CVE 2018 8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE 2018 8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE 2018 8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE 2018 8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE 2018 8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE 2018 8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE 2018 8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE 2018 8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

有关这些漏洞的详细信息，单击上面的链接，或请参阅 Microsoft 知识库文章[4338814](https://support.microsoft.com/help/4338814)并[4345418](https://support.microsoft.com/help/4345418)。



## <a name="before-you-begin"></a>开始之前

### <a name="prerequisites"></a>必备组件

- 安装 Azure Stack [1805年更新](azure-stack-update-1805.md)应用 Azure Stack 1807 更新之前。  不会更新 1806年时出现。  

- 安装最新可用[更新或修补程序版本为 1805年](azure-stack-update-1805.md#post-update-steps)。  
  > [!TIP]  
  > 订阅以下*RRS*或*Atom*源，以保持与 Azure Stack 修补程序：
  > - RRS: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss ... 
  > - Atom: https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom ...


- 在开始安装更新 1807年之前，请运行[Test-azurestack](azure-stack-diagnostic-test.md)验证 Azure Stack 的状态并解决发现的任何操作问题。 另外，请查看活动警报，并解决所有需要采取措施的警报。

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- <!-- 2468613 - IS --> 此更新的安装期间，可能会看到警报标题*错误-FaultType UserAccounts.New 模板缺少。*  可以放心地忽略这些警报。 此更新安装完成后，这些警报将自动关闭。

- <!-- 2489559 - IS --> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md#plan-for-updates)。

- <!-- 2830461 - IS --> 在某些情况下更新需要引起注意，当相应可能不会生成警报。 准确的状态仍会反映在门户中，不受影响。

### <a name="post-update-steps"></a>更新后步骤

- <!-- 2933866 – IS --> **改进了对于失败的更新安装的状态。** 此版本引入了两个新的状态类别，以提供有关失败的更新安装的更多详细信息的运算符。 两个类别都*PreparationFailed*，并*InstallationFailed*。 安装此版本后，可能会看到以前更新安装失败进行了修订以反映这些新类别的信息。 

<!-- *There are no post-update steps for update 1807.* -->

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - Link to KB  
 -->

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户
- <!-- 2931230 – IS  ASDK --> 无法删除加载项计划，即使在用户订阅中删除该计划时都会添加到用户订阅的计划。 该计划将保留，直到引用加载项计划的订阅也会被删除。 

- <!--2760466 – IS  ASDK --> 在安装新的 Azure Stack 环境运行此版本时，警报，指示*需要激活*可能不会显示。 [激活](azure-stack-registration.md)是必需的然后才能使用 marketplace 联合。  

- <!-- TBD - IS ASDK --> 是两种管理订阅类型[版本 1804年中引入的](azure-stack-update-1804.md#new-features)不应使用。 订阅类型是**计数订阅**，并**消耗订阅**。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

- <!-- 2403291 - IS ASDK --> 可能无法使用管理员和用户门户底部的水平滚动条。 如果无法访问水平滚动条，请使用痕迹导航到门户中的上一边栏选项卡，只需从门户左上角的痕迹列表中选择要查看的边栏选项卡的名称即可。

  ![痕迹导航](media/azure-stack-update-1804/breadcrumb.png)

- <!-- TBD - IS --> 无法在管理员门户中查看计算或存储资源。 此问题的原因是更新安装过程中出错，导致系统错误地将更新报告为成功。 如果发生此问题，请联系 Microsoft 客户支持服务部门以寻求帮助。

- <!-- TBD - IS --> 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。

- <!-- TBD - IS ASDK --> 删除用户订阅会形成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。

- <!-- TBD - IS ASDK --> 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。


### <a name="health-and-monitoring"></a>运行状况和监视
- <!-- 1264761 - IS ASDK --> 可能会看到具有以下详细信息的**运行状况控制器**组件的警报：  

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


- <!-- 2812138 | IS --> 可能会看到一个警报，以便**存储**组件具有下列详细信息：

   - 名称： 存储服务的内部通信错误  
   - 严重性：严重  
   - 组件： 存储  
   - 说明： 将请求发送到以下节点时发生存储服务的内部通信错误。  

    可以安全地忽略警报，但需要手动关闭警报。

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

- <!-- TBD - IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

- <!-- 2724961- IS ASDK --> 注册时**Microsoft.Insight**订阅设置中的资源提供程序并创建 Windows VM 与来宾 OS 诊断已启用，VM 的概述页不显示度量值的数据。 

   若要查找 VM 的 CPU 百分比图表类似的指标数据，请转到**指标**边栏选项卡，并显示所有受支持的 Windows VM 来宾指标。

### <a name="networking"></a>网络  

- <!-- 1766332 - IS ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 1902460 - IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 - IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- <!-- 2702741 -  IS ASDK --> 使用动态分配方法部署的公共 Ip 不保证发出停止-解除分配后将保留。

- <!-- 2529607 - IS ASDK --> 在 Azure Stack 期间*机密轮换*，一段中的公共 IP 地址是无法访问为 2 到 5 分钟内。

-   <!-- 2664148 - IS ASDK --> 在租户位置使用 S2S VPN 隧道访问其虚拟机的情况下，它们可能会遇到连接尝试会如果已创建网关后，在本地子网已添加到本地网络网关失败的方案。 


### <a name="sql-and-mysql"></a>SQL 和 MySQL



- <!-- No Number - IS, ASDK -->  中不支持特殊字符，包括空格和句点**系列**时 SQL 和 MySQL 资源提供程序创建的 SKU 的名称。 

- <!-- TBD - IS --> 只有资源提供程序才能在托管 SQL 或 MySQL 的服务器上创建项目。 如果在不是由资源提供程序创建的主机服务器上创建项目，则此类项目可能导致状态不匹配。  

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


## <a name="next-steps"></a>后续步骤
- 若要查看 Azure Stack 集成系统，并保持您的系统中受支持的状态而必须执行的操作的服务策略，请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。  
