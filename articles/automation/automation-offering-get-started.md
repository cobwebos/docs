---
title: "Azure 自动化入门 | Microsoft Docs"
description: "本文概述了 Azure 自动化服务，探讨了 Azure 应用商店产品/服务的载入准备过程涉及的设计和实现细节。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 486e2b2da7a78cdc39743e60f4140fc58275a8fc
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="getting-started-with-azure-automation"></a>Azure 自动化入门

本入门指南介绍了与 Azure 自动化部署相关的核心概念。 不管是 Azure 自动化新手，还是已有自动化工作流软件（例如 System Center Orchestrator）使用经验，都可以通过本指南了解如何准备和载入自动化。  之后便可开始开发 runbook，支持流程自动化需求。 


## <a name="automation-architecture-overview"></a>自动化体系结构概述

![Azure 自动化概述](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure 自动化是软件即服务 (SaaS) 型应用程序，提供可缩放且可靠的多租户环境，可以在本地、Azure 或其他云服务中通过 Runbook 实现过程的自动化，并使用 Desired State Configuration (DSC) 管理对 Windows 和 Linux 系统的配置更改。 包含在自动化帐户中的实体（例如 Runbook、资产、运行方式帐户）与订阅以及其他订阅中的其他自动化帐户相互隔离。  

Azure 中运行的 Runbook 在自动化沙盒中执行，这些沙盒托管在 Azure 平台即服务 (PaaS) 型虚拟机中。  自动化沙盒针对 Runbook 执行的所有方面（模块、存储、内存、网络通信、作业流等）提供租户隔离。此角色由服务管理，不可从 Azure 或 Azure 自动化帐户访问，因此，无法控制它。         

若要在本地数据中心或其他云服务中自动部署和管理资源，可在创建自动化帐户后，指定一台或多台计算机运行[混合 Runbook 辅助角色 (HRW)](automation-hybrid-runbook-worker.md)。  每个 HRW 都需要 Microsoft 管理代理，同时需要连接到 Log Analytics 工作区以及一个自动化帐户。  Log Analytics 用于启动安装、维护 Microsoft 管理代理，以及监视 HRW 的功能。  Runbook 及其运行指令的传送由 Azure 自动化来执行。

可以部署多个 HRW，以便为 Runbook 提供高可用性、对 Runbook 作业进行负载均衡，以及在某些情况下将其专用于特定的工作负荷或环境。  HRW 上的 Microsoft Monitoring Agent 通过 TCP 端口 443 启动与自动化服务的通信，没有入站防火墙要求。  在环境中的 HRW 上运行 Runbook 以后，如果希望 Runbook 针对该环境中的其他计算机或服务执行管理任务，则 Runbook 可能需访问其他端口。  如果 IT 安全策略不允许网络上的计算机连接到 Internet，请参阅 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)一文，了解如何让该网关充当 HRW 的代理，以便从自动化帐户收集作业状态以及接收配置信息。

在 HRW 上运行时，Runbook 是在计算机的本地系统帐户上下文中运行，这是在本地 Windows 计算机上执行管理操作时，建议采用的安全上下文。 如果希望 Runbook 针对本地计算机的外部资源运行任务，则可能需要在自动化帐户中定义安全的凭据资产，以便从 Runbook 对其进行访问，并用其对外部资源进行身份验证。 可以将 Runbook 中的[凭据](automation-credentials.md)、[证书](automation-certificates.md)和[连接](automation-connections.md)资产用于 cmdlet，以便指定身份验证凭据。

存储在 Azure 自动化中的 DSC 配置可直接应用于 Azure 虚拟机。 其他物理计算机和虚拟机可从 Azure 自动化 DSC 拉取服务器请求配置。  管理本地物理或虚拟 Windows 系统和 Linux 系统的配置时，不需部署任何支持自动化 DSC 请求服务器的基础结构，只需确保要通过自动化 DSC 管理每个系统能够进行出站 Internet 访问（经 TCP 端口 443 与 OMS 服务通信）即可。   

## <a name="prerequisites"></a>先决条件

### <a name="automation-dsc"></a>自动化 DSC
Azure 自动化 DSC 可用于管理各种计算机：

* 运行 Windows 或 Linux 的 Azure 虚拟机（经典）
* 运行 Windows 或 Linux 的 Azure 虚拟机
* 运行 Windows 或 Linux 的 Amazon Web Services (AWS) 虚拟机
* 位于本地或者 Azure 或 AWS 以外的云中的物理/虚拟 Windows 计算机
* 位于本地或者 Azure 或 AWS 以外的云中的物理/虚拟 Linux 计算机

必须先安装最新版的 WMF 5，然后适用于 Windows 的 PowerShell DSC 代理才能与 Azure 自动化通信。 只有安装了[适用于 Linux 的 PowerShell DSC 代理](https://www.microsoft.com/en-us/download/details.aspx?id=49150)的最新版本，Linux 才能与 Azure 自动化通信。

### <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色  
指定某台计算机运行混合 Runbook 作业时，该计算机必须满足以下要求：

* Windows Server 2012 或更高版本
* Windows PowerShell 4.0 或更高版本。  建议在计算机上安装 Windows PowerShell 5.0 以提高可靠性。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=50395)下载新版本
* .NET Framework 4.6.2 或更高版本
* 至少双核
* 至少 4 GB RAM

### <a name="permissions-required-to-create-automation-account"></a>创建自动化帐户所需的权限
若要创建或更新自动化帐户，必须具有完成本主题所需的下述特定权限。   
 
* 若要创建自动化帐户，需将 AD 用户帐户添加到一个角色，该角色的权限相当于 Microsoft.Automation 资源的所有者角色，如 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文所述。  
* Azure AD 租户中的非管理员用户可以 [注册 AD 应用程序](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)，前提是应用注册设置已设置为 **是**。  如果“应用注册设置”设置为“否”，则执行此操作的用户必须是 Azure AD 中的全局管理员。 

如果你在被添加到订阅的全局管理员/共同管理员角色之前不是订阅的 Active Directory 实例的成员，则会将你作为来宾添加到 Active Directory。 在这种情况下，“添加自动化帐户”边栏选项卡中会显示 “你无权创建...”警告。 可以先从订阅的 Active Directory 实例中删除已添加到全局管理员/共同管理员角色的用户，然后重新添加，使其成为 Active Directory 中的完整用户。 若要验证这种情况，可在 Azure 门户的“Azure Active Directory”窗格中选择“用户和组”，选择“所有用户”，在选择特定的用户后再选择“配置文件”。 用户配置文件下的“用户类型”属性值不应等于“来宾”。

## <a name="authentication-planning"></a>身份验证规划
可以通过 Azure 自动化，针对本地资源、Azure 资源以及其他云提供程序资源自动执行任务。  为了使 Runbook 执行所需操作，Runbook 必须有权使用订阅中所需的最小权限来安全地访问资源。  

### <a name="what-is-an-automation-account"></a>什么是自动化帐户 
所有使用 Azure 自动化中的 Azure cmdlet 针对资源执行的自动化任务在向 Azure 进行身份验证时，都使用基于 Azure Active Directory 组织标识凭据的身份验证。  自动化帐户独立于用来登录到门户，对 Azure 资源进行配置和使用的帐户。  随附于帐户的自动化资源有：

* 证书 - 包含用于从 runbook 或 DSC 配置进行身份验证的证书或用于添加这些证书。
* 连接 - 包含从 runbook 或 DSC 配置连接到外部服务或应用程序所需的身份验证和配置信息。
* 凭据 - 一个 PSCredential 对象，包含从 runbook 或 DSC 配置进行身份验证所需的用户名和密码等安全凭据。
* 集成模块 - Azure 自动化帐户中包含的 PowerShell 模块，用于在 runbook 和 DSC 配置中使用 cmdlet。
* 计划 - 包含在指定时间启动或停止 runbook（包括重复频率）的计划。
* 变量 - 包含来自于 runbook 或 DSC 配置的值。
* **DSC 配置** - 属于 PowerShell 脚本，说明如何在 Windows 或 Linux 计算机上配置操作系统功能或设置，或者如何安装应用程序。  
* **Runbooks** - 是基于 Windows PowerShell 的 Azure 自动化中执行某些自动化过程的一组任务。    

每个自动化帐户的自动化资源与单个 Azure 区域相关联，但自动化帐户可以管理订阅中的所有资源。 如果策略要求将数据和资源隔离到特定的区域，请在不同区域中创建自动化帐户。

> [!NOTE]
> 无法在 Azure 经典门户中访问使用在 Azure 门户创建的自动化帐户及其包含的资源。 如果你想要使用 Windows PowerShell 来管理这些帐户或其资源，必须使用 Azure 资源管理器模块。
> 

在 Azure 门户中创建自动化帐户时，会自动创建两个身份验证实体：

* 运行方式帐户。 此帐户在 Azure Active Directory (Azure AD) 中创建一个服务主体，并创建一个证书。 此外，它还向参与者分配基于角色的访问控制 (RBAC)，以便使用 Runbook 管理 Resource Manager 资源。
* 经典运行方式帐户。 此帐户上传一个管理证书，用于通过 Runbook 管理经典资源。

基于角色的访问控制在 Azure Resource Manager 中可用，向 Azure AD 用户帐户和运行方式帐户授予允许的操作，并对该服务主体进行身份验证。  请阅读 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文，详细了解如何开发自动化权限管理模型。  

#### <a name="authentication-methods"></a>身份验证方法
下表总结了 Azure 自动化所支持的每个环境的不同身份验证方法。

| 方法 | 环境 
| --- | --- | 
| Azure 运行方式帐户和经典运行方式帐户 |Azure Resource Manager 部署和 Azure 经典部署 |  
| Azure AD 用户帐户 |Azure Resource Manager 部署和 Azure 经典部署 |  
| Windows 身份验证 |使用混合 Runbook 辅助角色的本地数据中心或其他云提供程序 |  
| AWS 凭据 |Amazon Web Services |  

如何\身份验证和安全性部分是支持文章，这些文章概述了如何通过专用于上述环境的现有帐户或新帐户为此类环境配置身份验证，并提供了相应的实现步骤。  [更新自动化运行方式帐户](automation-create-runas-account.md)主题适用于 Azure 运行方式和经典运行方式帐户，介绍了如何从门户或使用 PowerShell 对现有的自动化帐户进行运行方式帐户更新（如果最初没有为其配置运行方式或经典运行方式帐户）。 如果要使用企业证书颁发机构 (CA) 颁发的证书创建运行方式和经典运行方式帐户，请参阅本文，了解如何使用此配置创建帐户。     
 
## <a name="network-planning"></a>网络规划
要使混合 Runbook 辅助角色连接并注册到 Microsoft Operations Management Suite (OMS)，必须让其有权访问下述端口号和 URL。  除了这些端口和 URL 以外，还需有权访问连接到 OMS 时 [Microsoft Monitoring Agent 需要的端口和 URL](../log-analytics/log-analytics-windows-agents.md#network)。 如果使用代理服务器在代理与 OMS 服务之间通信，则需确保能够访问相应的资源。 如果使用防火墙来限制对 Internet 的访问，则需要将防火墙配置为允许访问。

下面的信息列出了混合 Runbook 辅助角色与自动化通信时所要使用的端口和 URL。

* 端口：只需使用 TCP 443 进行出站 Internet 访问
* 全局 URL：*.azure-automation.net

如果为特定的区域定义了自动化帐户并想要限制与该区域数据中心之间的通信，请参考下表中为每个区域提供的 DNS 记录。

| **区域** | **DNS 记录** |
| --- | --- |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲西部 |we-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲北部 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 美国政府弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us |

有关 IP 地址列表（非名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) xml 文件并进行查看。 

> [!NOTE]
> 此文件包含 Microsoft Azure 数据中心使用的 IP 地址范围（包括计算、SQL 和存储范围）。 每周都将发布更新的文件，反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 请每周下载新的 xml 文件，并在网站上执行必要的更改以正确地标识 Azure 中运行的服务。 快速路由用户可能会注意到用于在每个月的第一周更新 Azure 空间中 BGP 播发的此文件。 
> 

## <a name="creating-an-automation-account"></a>创建自动化帐户

可以通过不同方式在 Azure 门户中创建自动化帐户。  下表介绍了每种类型的部署体验以及其间的差异。  

|方法 | 说明 |
|-------|-------------|
| 从应用商店选择“自动化与控制” | 一种服务，用于创建自动化帐户和 OMS 工作区，二者在同一资源组和区域中互相关联。  与 OMS 的集成还包括使用 Log Analytics 监视和分析随时间推移的 runbook 作业状态和作业流，以及利用高级功能上报或调查问题的好处。 该服务还部署更改跟踪和更新管理解决方案（默认启用）。 |
| 从应用商店选择“自动化” | 在新的或现有的资源组中创建自动化帐户，该资源组与 OMS 工作区没有关联，也不包含“自动化与控制”服务提供的任何解决方案。 这是自动化操作入门的基本配置，可以借此了解如何编写 Runbook 和 DSC 配置，以及如何使用该服务的功能。 |
| 选定管理解决方案 | 如果从**[更新管理](../operations-management-suite/oms-solution-update-management.md)**、**[在空闲时间启动/停止 VM](automation-solution-vm-management.md)** 或**[更改跟踪](../log-analytics/log-analytics-change-tracking.md)**中选择一个解决方案，系统会提示你选择现有的自动化与 OMS 工作区，或者允许你同时创建二者，这是在订阅中部署解决方案所必需的。 |

本主题演示如何载入“自动化与控制”服务，以便创建自动化帐户和 OMS 工作区。  若要创建独立的自动化帐户，以便进行测试或对服务进行预览，请参阅[创建独立的自动化帐户](automation-create-standalone-account.md)一文。  

### <a name="create-automation-account-integrated-with-oms"></a>创建与 OMS 集成的自动化帐户
若要载入自动化，建议的方法是从应用商店选择“自动化与控制”服务。  这将创建自动化帐户并建立与 OMS 工作区的集成，包括安装服务中提供的管理解决方案的选项。  

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录 Azure 门户。

2. 单击“新建” 。<br><br> ![在 Azure 门户中选择“新建”选项](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. 搜索“自动化”，并在搜索结果中选择“自动化与控制”*。<br><br> ![从应用商店搜索并选择“自动化与控制”](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)。<br>   

4. 在阅读服务说明后，单击“创建”。  

5. 在“自动化与控制”设置边栏选项卡上，选择“OMS 工作区”。  在“OMS 工作区”边栏选项卡上，选择一个 OMS 工作区（其链接到的 Azure 订阅正是自动化帐户所在的订阅），或创建一个 OMS 工作区。  如果没有 OMS 工作区，请选择“新建工作区”，并在“OMS 工作区”边栏选项卡上执行以下操作： 
   - 指定新 **OMS 工作区**的名称。
   - 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   - 对于“资源组”，可以创建资源组，也可以选择现有资源组。  
   - 选择“位置” 。  如需其他信息，请参阅[提供 Azure 自动化的区域](https://azure.microsoft.com/regions/services/)。  解决方案提供两种定价层：“免费”层和“按节点(OMS)”层。  免费层的每日可收集数据量、保留期和 Runbook 作业运行时分钟数有限制。  “按节点 (OMS)”层对每日收集的数据量没有限制。  
   - 选择“自动化帐户”。  若要创建新的 OMS 工作区，则还需创建一个与前面指定的新 OMS 工作区相关联的自动化帐户，其中包括 Azure 订阅、资源组和区域。  可以选择“创建自动化帐户”，并在“自动化帐户”边栏选项卡上提供以下信息： 
  - 在“名称”字段中输入自动化帐户的名称。

    系统会根据所选的 OMS 工作区自动填充所有其他选项，无法修改这些选项。  Azure 运行方式帐户是此服务的默认身份验证方法。  单击“确定”后，系统会验证配置选项并创建自动化帐户。  可以在菜单中的“通知”下面跟踪操作进度。 

    否则，请选择现有的自动化运行方式帐户。  选择的帐户不能已关联到另一 OMS 工作区，否则会在边栏选项卡中显示一条通知消息。  如果该帐户已进行关联，则需选择其他自动化运行方式帐户，或者创建一个。

    提供完所需信息以后，请单击“创建”。  此时会对信息进行验证，并创建自动化帐户和运行方式帐户。  会自动返回到“OMS 工作区”边栏选项卡。  

6. 在“OMS 工作区”边栏选项卡上提供所需信息后，单击“创建”。  在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。  随后将返回到“添加解决方案”边栏选项卡。  

7. 在“自动化与控制”设置边栏选项卡上，确认你想要安装建议的预选解决方案。 如果取消选择某个解决方案，可以稍后单独进行安装。  

8. 单击“创建”，继续载入自动化和 OMS 工作区。 系统会验证所有设置，然后尝试在订阅中部署该服务。  此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。 

载入该服务以后，即可开始创建 runbook，运行已启用的管理解决方案，部署[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)，或者开始使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics) 收集云或本地环境中的资源所生成的数据。   

## <a name="next-steps"></a>后续步骤
* 可以参阅[测试 Azure 自动化运行方式帐户身份验证](automation-verify-runas-authentication.md)一文，确认新的自动化帐户能否针对 Azure 资源进行身份验证。
* 若要开始创建 runbook，请在开始创作前，先查看支持的[自动化 runbook 类型](automation-runbook-types.md)和相关注意事项。


