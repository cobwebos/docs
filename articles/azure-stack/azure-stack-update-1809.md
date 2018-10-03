---
title: Azure Stack 1809 更新 |Microsoft Docs
description: 了解如何针对 Azure Stack 集成系统，包括已知的问题的新变化 1809年更新和下载更新的位置。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: 4b83d3743f65a3ad1b757c2e6dee8416701b24f3
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48243528"
---
# <a name="azure-stack-1809-update"></a>Azure Stack 1809 更新

*适用于：Azure Stack 集成系统*

本文介绍 1809年更新包的内容。 更新包包括改进、 修复程序和 Azure Stack 的此版本的已知的问题。 本文还包含一个链接，使你能够下载更新。 已知问题分为与更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="build-reference"></a>内部版本参考

Azure Stack 1809 更新内部版本号是**1.1809.x.xx**。  

### <a name="new-features"></a>新增功能

此更新包括适用于 Azure Stack 的以下改进：

- <!--  2712869   | IS  ASDK -->  **Azure Stack syslog 客户端 （公开上市）** 此客户端允许转发的审核、 警报和到 syslog 服务器或安全信息和事件管理 (SIEM) 软件的 Azure Stack 基础结构相关的安全日志Azure Stack 的外部。 Syslog 客户端现在支持指定的 syslog 服务器正在侦听的端口。

此版本中，系统日志客户端正式发布，并且可在生产环境中。

有关详细信息，请参阅 [Azure Stack Syslog 转发](azure-stack-integrate-security.md)。

### <a name="fixed-issues"></a>修复的问题

- <!-- 2702741 -  IS ASDK --> 方法未在部署的使用动态分配的公共 Ip 的已修复的问题后发出停止-解除分配保留保证。 他们现在将保留。

- <!-- 3078022 - IS ASDK --> 如果 VM 已停止解除分配，1808年之前它不能重新分配 1808年更新后。  在 1809年中修复此问题。 可以在通过这项修复 1809年开始，已在此状态下无法启动的实例。 解决方法还可防止此问题再次发生。

### <a name="changes"></a>更改

- <!-- 1697698  | IS, ASDK --> *快速入门教程*在用户门户仪表板现在链接到联机 Azure Stack 文档中的相关文章。

- <!-- 2515955   | IS ,ASDK--> *所有服务*取代*更多服务*在 Azure Stack 管理员和用户门户。 现在，您可以使用*所有服务*作为要在 Azure Stack 门户中导航的方法与在 Azure 门户中执行相同的替代方法。

- <!--  TBD – IS, ASDK --> *基本 A*虚拟机大小的已停用[创建虚拟机规模集](azure-stack-compute-add-scalesets.md)(VMSS) 通过门户。 若要使用此大小创建 VMSS，请使用 PowerShell 或模板。  

### <a name="common-vulnerabilities-and-exposures"></a>常见漏洞和风险

此更新将安装以下安全更新：  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE 2018 0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE 2018 8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE 2018 8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE 2018 8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE 2018 8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE 2018 8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE 2018 8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE 2018 8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE 2018 8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE 2018 8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE 2018 8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE 2018 8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE 2018 8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE 2018 8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE 2018 8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE 2018 8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE 2018 8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE 2018 8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE 2018 8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE 2018 8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE 2018 8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE 2018 8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE 2018 8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE 2018 8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)

有关这些漏洞的详细信息，请单击对从前述链接，或请参阅 Microsoft 知识库文章[4457131](https://support.microsoft.com/help/4457131)。

### <a name="prerequisites"></a>必备组件

- 安装 Azure Stack [1808年更新](azure-stack-update-1808.md)应用 Azure Stack 1809 更新之前。 
- 安装最新可用[更新或修补程序版本 1808年](azure-stack-update-1808.md#post-update-steps)。

  > [!TIP]  
  > 订阅下述 *RRS* 或 *Atom* 源，了解 Azure Stack 修补程序的最新更新：
  > - RRS： https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss … 
  > - Atom： https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom …


- 在开始安装此更新之前，请运行[Test-azurestack](azure-stack-diagnostic-test.md)具有以下参数来验证 Azure Stack 的状态并解决发现的任何操作问题，包括所有警告和失败。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

- <!-- 2468613 - IS --> 在安装此更新期间，可能会出现标题如下的警报：“错误 - 缺少 FaultType UserAccounts.New 的模板”。  可以放心地忽略这些警报。 完成此更新的安装后，这些警报会自动关闭。

- <!-- 2489559 - IS --> 在安装此更新的过程中，请勿尝试创建虚拟机。 有关如何管理更新的详细信息，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md#plan-for-updates)。

- <!-- 2830461 - IS --> 在某些情况下，当某个更新需要关注时，相应的警报可能没有生成。 准确的状态仍会反映在门户中，不受影响。

### <a name="post-update-steps"></a>更新后步骤

*没有更新 1809年更新后步骤。*

<!-- After the installation of this update, install any applicable Hotfixes. For more information view the following knowledge base articles, as well as our [Servicing Policy](azure-stack-servicing-policy.md).  
 - [Link to KB]()  
 -->

## <a name="known-issues-post-installation"></a>已知问题（安装后）

下面是此内部版本的安装后已知问题。

### <a name="portal"></a>门户

- Azure Stack 技术文档重点介绍最新版本。 由于各版本之间的门户更改，使用 Azure Stack 门户时看到的内容可能不同于文档中看到的内容。

- <!-- 2930718 - IS ASDK --> 在管理员门户中，访问任何用户订阅的详细信息后关闭该边栏选项卡，并单击时**最近**，未显示用户订阅名称。

- <!-- 3060156 - IS ASDK --> 在管理员和用户门户，单击门户设置并选择**删除所有设置和专用仪表板**未按预期运行。 显示错误通知。 

- <!-- 2930799 - IS ASDK --> 管理员和用户门户网站中下**所有服务**，该资产**DDoS 防护计划**错误地列出。 不在 Azure Stack 中实际可用。 如果您尝试创建它，被显示错误，指出在门户中无法创建 marketplace 项。 

- <!-- 2930820 - IS ASDK --> 门户管理员和用户门户，如果搜索"Docker"，返回此项不正确。 不在 Azure Stack 中实际可用。 如果尝试创建它，会显示一个错误指示的边栏选项卡。 

- <!-- 2967387 – IS, ASDK --> 用于登录到 Azure Stack 管理员或用户门户的帐户将显示为**无法识别的用户**。 发生这种情况是，如果帐户不具有任一*第一个*或*最后一个*指定名称。 若要解决此问题，请编辑用户帐户提供的第一个或最后一个名称。 您必须然后注销，然后重新登录到门户。  

-  <!--  2873083 - IS ASDK --> 当你使用门户创建虚拟机规模集 (VMSS)、*实例大小*下拉列表中不使用 Internet Explorer 时正确加载。 若要解决此问题，请使用门户创建 VMSS 时使用另一个浏览器。  

- <!-- TBD  ASDK --> 所有 Azure Stack 部署默认时区现在设置为协调世界时 (UTC)。 但是，它会自动恢复到 UTC 为默认值在安装过程中，可以安装 Azure Stack 时选择时区。

- <!-- 2931230 – IS  ASDK --> 无法删除加载项计划，即使在用户订阅中删除该计划时都会添加到用户订阅的计划。 该计划将保留，直到引用加载项计划的订阅也会被删除。 

- <!--2760466 – IS  ASDK --> 安装运行此版本的新 Azure Stack 环境时，指示“需要激活”的警报可能不显示。 必须先[激活](azure-stack-registration.md)，然后才能使用市场联合。  

- <!-- TBD - IS ASDK --> [版本 1804 中引入](azure-stack-update-1804.md#new-features)的两种管理订阅类型不应使用。 这两种订阅类型为“计量订阅”和“消耗订阅”。 从版本 1804 开始，这些订阅类型会在新的 Azure Stack 环境中显示，但尚不可用。 请继续使用“默认提供程序”订阅类型。

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


- <!-- 2812138 | IS --> 可能会看到具有以下详细信息的**存储**组件警报：

   - 名称: 存储服务内部通信错误  
   - 严重性：严重  
   - 组件: 存储  
   - 说明: 将请求发送到以下节点时发生存储服务内部通信错误。  

    可以放心地忽略此警报，但需手动关闭它。

- <!-- 2368581 - IS. ASDK --> 如果 Azure Stack 操作员收到内存不足的警报，并且租户虚拟机无法部署并出现“Fabric VM 创建错误”，则可能表示 Azure Stack 模组的可用内存不足。 请使用 [Azure Stack 容量规划工具](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)来充分了解可供工作负荷使用的容量。


### <a name="compute"></a>计算

- <!-- 2869209 – IS, ASDK --> 使用时[**添加 AzsPlatformImage** cmdlet](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0)，则必须使用 **-OsUri**参数作为存储帐户上传到磁盘的 URI。 如果使用的磁盘的本地路径，该 cmdlet 会失败并出现以下错误：*长时间运行的操作失败，状态为 '失败'*。 

- <!--  2966665 – IS, ASDK --> 将 SSD 数据磁盘附加到高级大小托管磁盘 （DS、 DSv2、 Fs、 Fs_V2） 的虚拟机失败并出现错误：*未能更新虚拟机 vmname 错误的磁盘： 请求不能执行操作，因为存储帐户类型Premium_LRS' 不支持的 VM 大小 Standard_DS/Ds_V2/FS/Fs_v2)*

   若要解决此问题，请使用*Standard_LRS*而不是数据磁盘*Premium_LRS 磁盘*。 利用*Standard_LRS* IOPs 或计费成本，不会更改数据磁盘。 

- <!--  2795678 – IS, ASDK --> 当你使用门户创建虚拟机 (VM) 高级 VM 大小 （DS、 Ds_v2、 FS、 FSv2） 中时，在标准存储帐户中创建 VM。 创建标准存储帐户中的不会影响就功能而言，IOPs，或计费。 

   您可以放心地忽略该警告，指出：*你已选择使用标准磁盘上可支持高级磁盘的大小。这可能会影响操作系统的性能，不建议。请考虑改为使用高级存储 (SSD)。*

- <!-- 2967447 - IS, ASDK --> 虚拟机规模集 (VMSS) 创建体验提供了基于 CentOS 7.2，作为一个选项来部署。 由于该映像不是可在 Azure Stack 上，选择为你的部署的另一个操作系统，或使用指定另一个 CentOS 映像已从 marketplace 部署前运算符所下载的 Azure 资源管理器模板。  

- <!-- 2724873 - IS --> 使用 PowerShell cmdlet **Start-AzsScaleUnitNode** 或 **Stop-AzsScaleunitNode** 管理缩放单元时，首次尝试启动或停止缩放单元可能会失败。 如果 cmdlet 在第一次运行时失败，请再次运行 cmdlet。 第二次运行应该能够成功地完成操作。 

- <!-- TBD - IS ASDK --> 在 Azure Stack 用户门户中创建虚拟机时，该门户显示的可以附加到 DS 系列 VM 的数据磁盘数不正确。 DS 系列 VM 可以容纳的数据磁盘数取决于 Azure 配置。

- <!-- TBD - IS ASDK --> 如果在 VM 部署上预配某个扩展时耗时过长，用户应让预配超时，而不应尝试通过停止该进程来解除 VM 的分配或将 VM 删除。  

- <!-- 1662991 IS ASDK --> Azure Stack 不支持 Linux VM 诊断。 在部署启用 VM 诊断的 Linux VM 时，部署会失败。 如果通过诊断设置启用 Linux VM 的基本指标，部署也会失败。  

- <!-- 2724961- IS ASDK --> 注册时**Microsoft.Insight**订阅设置中的资源提供程序并创建 Windows VM 与来宾 OS 诊断已启用，在 VM 的概述页中的 CPU 百分比图表不会显示度量值数据。

   若要查找指标数据，例如 VM 的 CPU 百分比图表，请转到指标窗口并显示所有受支持的 Windows VM 来宾指标。



### <a name="networking"></a>网络  

- <!-- 1766332 - IS ASDK --> 如果在“网络”下单击“创建 VPN 网关”来设置 VPN 连接，则会将“基于策略”列为 VPN 类型。 请不要选择此选项。 Azure Stack 仅支持“基于路由”选项。

- <!-- 1902460 - IS ASDK --> Azure Stack 支持对一个 IP 地址使用一个本地网关。 这适用于所有租户订阅。 在创建第一个本地网关连接以后，系统会阻止使用同一 IP 地址创建本地网关资源的后续尝试。

- <!-- 16309153 - IS ASDK --> 在使用 DNS 服务器设置“自动”创建的虚拟网络上，无法更改为自定义 DNS 服务器。 更新的设置不推送到该 Vnet 中的 VM。

- <!-- 2529607 - IS ASDK --> 在 Azure Stack 机密轮换期间，有一个时段（两到五分钟）会无法访问公共 IP 地址。

-   <!-- 2664148 - IS ASDK --> 在租户位置使用 S2S VPN 隧道访问其虚拟机的情况下，它们可能会遇到其中连接尝试失败，如果已创建网关后，在本地子网已添加到本地网络网关的方案。 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>应用服务

- <!-- 2352906 - IS ASDK --> 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。


### <a name="usage"></a>使用情况  

- <!-- TBD - IS ASDK --> 公共 IP 地址使用计量数据显示相同*EventDateTime*而不是每个记录的值*TimeDate*演示创建记录时的时间戳。 目前，不能使用此数据执行的公共 IP 地址用量的准确计帐。


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>下载更新

可以从 Azure Stack 1809 更新包下载[此处](https://aka.ms/azurestackupdatedownload)。
  

## <a name="next-steps"></a>后续步骤

- 若要查看 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态，请参阅 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要使用特权终结点 (PEP) 来监视和恢复更新，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。  
- 有关 Azure Stack 中更新管理的概述，请参阅[在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。  
- 有关如何在 Azure Stack 中应用更新的详细信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。  
