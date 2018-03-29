---
title: Azure 自动化入门
description: 本文简要介绍了 Azure 自动化服务。 它回顾了相关设计和实现细节，为从 Azure Marketplace 载入该服务做准备。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 961b783b44b95a871c98f96d3783f3429636f295
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-automation"></a>Azure 自动化入门

本文介绍了与 Azure 自动化部署相关的核心概念。 不管是 Azure 自动化新手，还是已有自动化工作流软件（例如 System Center Orchestrator）使用经验，都可以了解如何准备和载入自动化。 阅读本文后便可开始开发 Runbook，以支持过程自动化需求。 


## <a name="automation-architecture-overview"></a>自动化体系结构概述

![Azure 自动化概述](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure 自动化是一种服务型软件 (SaaS) 应用程序，提供可缩放且可靠的多租户环境，用户可在该环境中使用 Runbook 实现过程自动化。 可在 Azure、其他云服务或本地环境中使用 Desired State Configuration (DSC) 管理对 Windows 和 Linux 系统的配置更改。 自动化帐户中的实体（包括 Runbook、资产和运行方式帐户）与订阅以及其他订阅中的其他自动化帐户相互隔离。  

Azure 中运行的 Runbook 在自动化沙盒中执行。 这些沙盒托管在 Azure 平台即服务 (PaaS) 型虚拟机中。 

自动化沙盒针对 Runbook 执行的所有方面（包括模块、存储、内存、网络通信和作业流）提供租户隔离。 此角色由服务管理。 不能从 Azure 或自动化帐户访问或管理此角色。         

若要在本地数据中心或其他云服务中自动部署和管理资源，可在创建自动化帐户后，指定一台或多台 VM 运行[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)。 每个混合 Runbook 辅助角色都需要一个自动化帐户并安装 Microsoft 管理代理。 该代理必须连接到 Azure Log Analytics 工作区。 Log Analytics 可用于启动安装、维护 Microsoft 管理代理，以及监视混合 Runbook 辅助角色的功能。 Azure 自动化执行 Runbook 及其运行指令的传送。

可以部署多个混合 Runbook 辅助角色。 使用混合 Runbook 辅助角色为 Runbook 提供高可用性并对 Runbook 作业进行负载均衡。 在某些情况下，可将 Runbook 作业专用于特定的工作负荷或环境。 混合 Runbook 辅助角色上的 Microsoft Monitoring Agent 通过 TCP 端口 443 启动与自动化服务的通信。 混合 Runbook 辅助角色没有入站防火墙要求。  

用户可能希望使用混合 Runbook 辅助角色上运行的某个 Runbook 针对环境中的其他计算机或服务执行管理任务。 在这种情况下，该 Runbook 可能还需要访问其他端口。 如果 IT 安全策略不允许网络上的计算机连接到 Internet，请参阅 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)。 Operations Management Suite (OMS) 网关充当混合 Runbook 辅助角色的代理。 它从自动化帐户中收集作业状态以及接收配置信息。

在混合 Runbook 辅助角色上运行时，Runbook 是在计算机的本地系统帐户上下文中运行。 在本地 Windows 计算机上执行管理操作时，建议采用安全上下文。 如果希望 Runbook 针对本地计算机的外部资源运行任务，则可能需要在自动化帐户中定义安全凭据资产。 可以从 Runbook 访问安全凭据资产，并用它们对外部资源进行身份验证。 可以使用 Runbook 中的[凭据](automation-credentials.md)、[证书](automation-certificates.md)和[连接](automation-connections.md)资产。 将资产与 cmdlet 搭配使用，这些 cmdlet 可用于指定凭据以对资产进行身份验证。

可以将存储在 Azure 自动化中的 DSC 配置应用于虚拟机。 其他物理计算机和虚拟机可从 Automation DSC 请求服务器请求配置。 无需部署任何支持 Automation DSC 请求服务器的基础结构，来管理本地物理或虚拟 Windows 系统和 Linux 系统的配置。 只需确保通过 Automation DSC 管理的每个系统能够进行出站 Internet 访问即可。 与 OMS 服务通信时使用 TCP 端口 443。   

## <a name="prerequisites"></a>先决条件

### <a name="automation-dsc"></a>自动化 DSC
Automation DSC 可用于管理以下计算机：

* 运行 Windows 或 Linux 的 Azure 虚拟机（经典）。
* 运行 Windows 或 Linux 的 Azure 虚拟机。
* 运行 Windows 或 Linux 的 Amazon Web Services (AWS) 虚拟机。
* 位于本地或者 Azure 或 AWS 以外的云中的物理和虚拟 Windows 计算机。
* 位于本地或者 Azure 或 AWS 以外的云中的物理和虚拟 Linux 计算机。

对于 Windows 计算机，必须安装最新版的 Windows Management Framework (WMF) 5。 对于 Linux 计算机，必须安装最新版的 [PowerShell DSC agent for Linux](https://www.microsoft.com/en-us/download/details.aspx?id=49150)。 PowerShell DSC 代理使用 WMF 5 与自动化通信。 

### <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色  
指定某台计算机运行混合 Runbook 作业时，该计算机必须满足以下先决条件：

* Windows Server 2012 或更高版本。
* Windows PowerShell 4.0 或更高版本。 为了提高可靠性，建议安装 Windows PowerShell 5.0。 可以从 Microsoft 下载中心[下载新版本](https://www.microsoft.com/download/details.aspx?id=50395)。
* .NET Framework 4.6.2 或更高版本。
* 至少双核。
* 至少 4 GB RAM。

### <a name="permissions-required-to-create-an-automation-account"></a>创建自动化帐户所需的权限
若要创建或更新自动化帐户，并完成本文所述的任务，必须具有以下特权和权限：   
 
* 若要创建自动化帐户，必须将 Azure Active Directory (Azure AD) 用户帐户添加到一个角色，该角色的权限相当于 **Microsoft.Automation** 资源的所有者角色。 有关详细信息，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)。  
* 在 Azure 门户的“Azure Active Directory” > “管理” > “应用注册”下，如果“应用注册”设置为“是”，则 Azure AD 租户中的非管理员用户可以[注册 Active Directory 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)。 如果“应用注册”设置为“否”，则执行此操作的用户必须是 Azure AD 中的全局管理员。 

如果在被添加到订阅的全局管理员/共同管理员角色之前不是订阅的 Active Directory 实例的成员，则将作为来宾添加到 Active Directory。 在这种情况下，“添加自动化帐户”页中会显示此消息：“你无权创建”。 

如果用户已被添加到全局管理员/共同管理员角色，可以先将其从订阅的 Active Directory 实例中删除，然后重新添加到 Active Directory 中的完整用户角色。

若要验证用户角色，请执行以下操作：
1. 在 Azure 门户中，转到“Azure Active Directory”窗格。
2. 选择“用户和组”。
3. 选择“所有用户”。 
4. 选择特定的用户后，选择“配置文件”。 用户配置文件下的“用户类型”属性值不应为“来宾”。

## <a name="authentication-planning"></a>身份验证规划
在 Azure 自动化中，可以针对 Azure 资源、本地资源以及其他云服务资源自动执行任务。 为了使 Runbook 执行所需操作，Runbook 必须有权安全地访问资源。 它必须具有订阅中所需的最小权限。  

### <a name="what-is-an-automation-account"></a>什么是自动化帐户 
所有使用 Azure 自动化中的 cmdlet 针对资源执行的自动化任务在向 Azure 进行身份验证时，都使用基于 Azure AD 组织标识凭据的身份验证。  自动化帐户独立于用来登录到门户，对 Azure 资源进行配置和使用的帐户。 

自动化帐户附带以下资源：

* **证书**。 包含用于从 Runbook 或 DSC 配置进行身份验证的证书。 还可以添加证书。
* **连接**。 包含从 Runbook 或 DSC 配置连接到外部服务或应用程序所需的身份验证和配置信息。
* **凭据**。 包含 **PSCredential** 对象，该对象包含用户名和密码等安全凭据。 从 Runbook 或 DSC 配置进行身份验证时需要这些凭据。
* **集成模块**。 Azure 自动化帐户中包含的 PowerShell 模块。 PowerShell 模块用于在 Runbook 和 DSC 配置中运行 cmdlet。
* **计划**。 包含在指定时间启动或停止 Runbook（包括重复频率）的计划。
* **变量**。 包含来自于 Runbook 或 DSC 配置的值。
* **DSC 配置**。 属于 PowerShell 脚本，说明如何配置操作系统功能或设置，或者如何在 Windows 或 Linux 计算机上安装应用程序。  
* **Runbook**。 基于 Windows PowerShell 在自动化中执行自动化过程的一组任务。    

每个自动化帐户的自动化资源都与单个 Azure 区域相关联。 但是，可以使用自动化帐户管理订阅中的所有资源。 如果策略要求将数据和资源隔离到特定的区域，请在不同区域中创建自动化帐户。

在 Azure 门户中创建自动化帐户时，会自动创建两个身份验证实体：

* **运行方式帐户**。 此帐户执行以下任务：
  - 在 Azure AD 中创建服务主体。
  - 创建证书。
  - 向参与者分配基于角色的访问控制 (RBAC)，以便使用 Runbook 管理 Azure 资源管理器资源。
* **经典运行方式帐户**。 此帐户会上传一个管理证书。 该证书用于通过 Runbook 管理经典资源。

可在资源管理器中使用 RBAC 向 Azure AD 用户帐户和运行方式帐户授予允许的操作。 RBAC 还可用于对该服务主体进行身份验证。 有关详细信息以及如何帮助开发用于管理自动化权限的模型，请参阅 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文。  

#### <a name="authentication-methods"></a>身份验证方法
下表总结了 Azure 自动化所支持的可用于每个环境的身份验证方法。

| 方法 | 环境 
| --- | --- | 
| Azure 运行方式帐户和经典运行方式帐户 |Azure 资源管理器部署和 Azure 经典部署 |  
| Azure AD 用户帐户 |Azure 资源管理器部署和 Azure 经典部署 |  
| Windows 身份验证 |使用混合 Runbook 辅助角色的本地数据中心或其他云服务提供程序 |  
| Amazon Web Services 凭据 |Amazon Web Services |  

以下文章概述了如何为这些环境配置身份验证，并提供了相应的实现步骤。 它们介绍了如何使用专用于该环境的现有帐户和新帐户。 
* [创建独立的 Azure 自动化帐户](automation-create-standalone-account.md)
* [使用 Azure 经典部署和资源管理器部署对 Runbook 进行身份验证](automation-create-aduser-account.md)
* [使用 Amazon Web Services 进行 Runbook 身份验证](automation-config-aws-account.md)
* [更新自动化运行方式帐户](automation-create-runas-account.md)

对于 Azure 运行方式帐户和经典运行方式帐户，[更新自动化运行方式帐户](automation-create-runas-account.md)介绍了如何从门户使用运行方式帐户更新现有的自动化帐户。 它还介绍了如何在最初没有为自动化帐户配置运行方式帐户或经典运行方式帐户的情况下使用 PowerShell。 可以使用企业证书颁发机构 (CA) 颁发的证书创建运行方式帐户和经典运行方式帐户。 请查看[更新自动化运行方式帐户](automation-create-runas-account.md)，了解如何使用此配置创建帐户。     
 
## <a name="network-planning"></a>网络规划
要使混合 Runbook 辅助角色连接并注册到 OMS，必须让其有权访问此部分所述的端口号和 URL。 除了这些端口和 URL 以外，还需要有权访问 [Microsoft Monitoring Agent 连接 OMS 时所要使用的端口和 URL](../log-analytics/log-analytics-windows-agent.md)。 

如果使用代理服务器在代理与 OMS 服务之间通信，请确保能够访问相应的资源。 如果使用防火墙来限制对 Internet 的访问，则必须将防火墙配置为允许访问。

混合 Runbook 辅助角色与自动化通信时需要以下端口和 URL：

* 端口：只需使用 TCP 443 进行出站 Internet 访问。
* 全局 URL：*.azure-automation.net。

如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的通信。 下表提供了每个区域的 DNS 记录。

| **区域** | **DNS 记录** |
| --- | --- |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲西部 |we-jobruntimedata-prod-su1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 美国政府弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us |

有关区域 IP 地址列表（非区域名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) XML 文件。 

> [!NOTE]
> Azure 数据中心 IP 地址 XML 文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 文件中包含计算、SQL 和存储范围。 
>
>每周都将发布更新的文件。 该文件反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 
>
> 建议每周下载新的 XML 文件。 然后，更新网站以正确地标识 Azure 中运行的服务。 Azure ExpressRoute 用户应注意，此文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。 
> 

## <a name="creating-an-automation-account"></a>创建自动化帐户

下表介绍了在 Azure 门户中创建自动化帐户的不同方法。 它还介绍了每种类型的部署体验以及其间的差异。  

|方法 | 说明 |
|-------|-------------|
| 在 Azure Marketplace 中选择“自动化与控制” | 一种 Azure Marketplace 服务，用于创建自动化帐户和 OMS 工作区，二者在同一资源组和区域中互相关联。 通过与 OMS 集成，还带来了使用 Log Analytics 监视和分析随时间推移的 Runbook 作业状态和作业流的好处。 此外，还可以使用 Log Analytics 中的高级功能上报或调查问题。 该服务部署**更改跟踪**和**更新管理**解决方案（默认启用）。 |
| 在 Marketplace 中选择“自动化” | 此方法可在新的或现有的资源组中创建自动化帐户，该资源组与 OMS 工作区没有关联。 它不包含“自动化与控制”服务提供的任何解决方案。 此方法是自动化操作入门的基本配置。 可以借此了解如何编写 Runbook 和 DSC 配置，以及如何使用该服务的各项功能。 |
| 选择**管理**解决方案 | 如果选择**管理**解决方案，包括[更新管理](../operations-management-suite/oms-solution-update-management.md)、[在空闲时间启动/停止 VM](automation-solution-vm-management.md) 或[更改跟踪](../log-analytics/log-analytics-change-tracking.md)，该解决方案会提示选择现有的自动化帐户与 OMS 工作区。 该解决方案也允许选择创建自动化帐户与 OMS 工作区，这是在订阅中部署解决方案所必需的。 |

### <a name="create-an-automation-account-thats-integrated-with-oms"></a>创建与 OMS 集成的自动化帐户
若要载入自动化，建议在 Marketplace 中选择“自动化与控制”服务。 使用此方法创建自动化帐户，并建立与 OMS 工作区的集成。 使用此方法时，还可以选择安装该服务提供的管理解决方案。  

[创建独立的自动化帐户](automation-create-standalone-account.md)演示如何载入“自动化与控制”服务，以便创建自动化帐户和 OMS 工作区。 可以了解如何创建独立的自动化帐户以便进行测试或对服务进行预览。  

若要使用“自动化与控制”Marketplace 服务创建自动化帐户和 OMS 工作区，请执行以下操作：

1. 使用帐户登录到 Azure 门户，该帐户应当是订阅管理员角色的成员并且是订阅的共同管理员。
2. 选择“新建”。<br><br> ![在 Azure 门户中选择“新建”](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. 搜索“自动化”。 在搜索结果中，选择“自动化与控制”。<br><br> ![在 Azure Marketplace 中搜索并选择“自动化与控制”](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)。<br>   
4. 查看服务说明，然后选择“创建”。  
5. 在“自动化与控制”下，选择“OMS 工作区”。 在“OMS 工作区”下，选择与自动化帐户所在的 Azure 订阅相关联的 OMS 工作区。 如果没有 OMS 工作区，请选择“新建工作区”。 在“OMS 工作区”下： 
  1. 对于“OMS 工作区”，输入新工作区的名称。
  2. 对于“订阅”，选择要关联的订阅。 如果默认选择不合适，请从下拉列表中选择订阅。
  3. 对于“资源组”，可以创建资源组，也可以选择现有资源组。  
  4. 对于“位置”，选择一个区域。 有关详细信息，请参阅[提供 Azure 自动化的区域](https://azure.microsoft.com/regions/services/)。 解决方案提供两种定价层：免费层和“按节点(OMS)”层。 免费层的每日可收集数据量、保留期和 Runbook 作业运行时分钟数有限制。 “按节点(OMS)”层对每日收集的数据量没有限制。  
  5. 选择“自动化帐户”。  如果创建新的 OMS 工作区，则还需创建一个与新 OMS 工作区相关联的自动化帐户。 包括 Azure 订阅、资源组和区域。 
    1. 选择“创建自动化帐户”。
    2. 在“自动化帐户”下的“名称”字段中输入自动化帐户的名称。
    系统会根据所选的 OMS 工作区自动填充所有其他选项。 无法修改这些选项。 
    3. Azure 运行方式帐户是此服务的默认身份验证方法。 选择“确定”后，系统会验证配置选项并创建自动化帐户。 若要跟踪其进度，请在菜单上选择“通知”。 
    4. 否则，请选择现有的自动化运行方式帐户。 选择的帐户不能已关联另一个 OMS 工作区， 否则会显示一条通知消息。 如果该帐户已关联 OMS 工作区，请选择其他自动化运行方式帐户，或者创建一个。
    5. 输入或选择必填信息后，选择“创建”。 此时会对信息进行验证，并创建自动化帐户和运行方式帐户。 用户会自动返回到“OMS 工作区”窗格。  
6. 在“OMS 工作区”窗格上输入或选择必填信息后，选择“创建”。  此时会对信息进行验证并创建工作区。 若要跟踪其进度，请在菜单上选择“通知”。 随后将返回到“添加解决方案”窗格。  
7. 在“自动化与控制”设置下，确认想要安装建议的预选解决方案。 如果更改任意默认选项，可以稍后单独安装解决方案。  
8. 若要继续载入自动化和 OMS 工作区，请选择“创建”。 在验证所有设置后，Azure 会尝试在订阅中部署该服务。 此过程可能需要数秒钟的时间。 若要跟踪其进度，请在菜单中选择“通知”。 

载入该服务后，可执行以下任务：
* 开始创建 Runbook。
* 运行已启用的管理解决方案。
* 部署[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)。
* 开始使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics) 收集云或本地环境中的资源所生成的数据。   

## <a name="next-steps"></a>后续步骤
* 验证新的自动化帐户能否对 Azure 资源进行身份验证。 有关详细信息，请参阅[测试 Azure 自动化运行方式帐户身份验证](automation-verify-runas-authentication.md)。
* 在开始创作前，先了解如何开始创建 Runbook 和相关注意事项。 有关详细信息，请参阅[自动化 Runbook 类型](automation-runbook-types.md)。


