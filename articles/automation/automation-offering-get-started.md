--- 
title: "Azure 自动化入门 | Microsoft Docs"
description: "本文概述了 Azure 自动化服务，探讨了在准备载入 Azure 应用商店产品/服务过程中涉及的核心概念和实现细节。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 1876b78186da3aa6c0ae9dc7de3b7ab7e46888bb
ms.lasthandoff: 04/18/2017

---

## <a name="getting-started-with-azure-automation"></a>Azure 自动化入门

本入门指南介绍了与 Azure 自动化部署相关的核心概念。 不管你是 Azure 自动化的新手，还是已有自动化工作流软件（例如 System Center Orchestrator）的使用经验，都可以通过本指南了解相关概念和部署细节。 

## <a name="key-concepts"></a>关键概念

### <a name="automation-service"></a>自动化服务
自动化是一项 Azure 服务，该服务使用 Windows PowerShell 和 Azure 技术，帮助你控制 Azure、云以及本地基础结构和应用程序的管理。  Azure 自动化帮助你管理服务和应用程序的整个生命周期，具体方式如下：使用过程自动化进行自动部署、使用 PowerShell 所需状态配置进行操作系统配置、使用更新管理和更改跟踪进行持续更新和监视，以及进行自动故障排除和修复。

### <a name="automation-account"></a>自动化帐户
自动化帐户是你创建的 Azure 资源。  可以通过单个自动化帐户管理所有 Azure、云和本地资源。  自动化帐户是一个容器，包含运行自动化所需的各种项目：Runbook、模块、资产（例如凭据和计划）以及配置。 可以使用多个自动化帐户，将资源分隔到不同的逻辑环境（如开发环境、测试环境、生产环境）中，或者让其跨多个地理区域。  

### <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色
若要在本地数据中心、Azure 或其他云提供程序的物理或虚拟系统中运行 Runbook，可以通过混合 Runbook 辅助角色功能访问和管理这些本地资源。     

### <a name="automation-desired-state-configuration"></a>自动化所需状态配置
基于 PowerShell DSC 的自动化所需状态配置 (DSC) 可以配置、监视以及自动更新托管在 Azure、本地或另一云中的操作系统的所需状态。  

### <a name="management-solutions"></a>管理解决方案
管理解决方案（例如更新管理和更改跟踪）可扩展 Azure 自动化的功能，并可与 Log Analytics 配合使用。  这些解决方案可能包括多个支持特定管理方案的资源，例如自动化 Runbook、预定义的 Log Analytics 搜索查询和警报，以及可视化效果。  

## <a name="architecture-overview"></a>体系结构概述

Azure 自动化是软件即服务 (SaaS) 型应用程序，提供可缩放且可靠的多租户环境，可以在本地、Azure 或其他云服务中通过 Runbook 实现过程的自动化，并使用所需状态配置 (DSC) 管理对 Windows 和 Linux 系统的配置更改。 包含在自动化帐户中的实体（例如 Runbook、资产、运行方式帐户）与你的订阅以及其他订阅中的其他自动化帐户相互隔离。  

Azure 中运行的 Runbook 在自动化沙盒中执行，这些沙盒托管在 Azure 平台即服务 (PaaS) 型虚拟机中。  自动化沙盒针对 Runbook 执行的所有方面（模块、存储、内存、网络通信、作业流等）提供租户隔离。此角色由服务管理，不可从 Azure 或 Azure 自动化帐户访问，因此你无法控制它。         

若要在本地数据中心或其他云服务中自动部署和管理资源，可指定一台或多台计算机运行混合 Runbook 辅助角色 (HRW)。  每个 HRW 都需要 Microsoft 管理代理 (MMA)，同时需要连接到 Log Analytics 工作区以及一个自动化帐户。  Log Analytics 用于启动安装、维护 MMA 代理，以及监视 HRW 的功能。  Runbook 及其运行指令的传送由 Azure 自动化来执行。

可以部署多个 HRW，以便为 Runbook 提供高可用性、对 Runbook 作业进行负载均衡，以及在某些情况下将其专用于特定的工作负荷或环境。  HRW 通过 TCP 出站端口 443 与自动化服务通信。  在数据中心的 HRW 上运行 Runbook 以后，如果希望 Runbook 针对数据中心的其他计算机或服务执行管理任务，则 Runbook 可能需访问其他端口。  如果 IT 安全策略不允许你网络上的计算机连接到 Internet，请参阅 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)一文，了解如何让该网关充当 HRW 的代理，以便从自动化帐户收集作业状态以及接收配置信息。

在 HRW 上运行时，Runbook 是在计算机的本地系统帐户上下文中运行，因此在本地 Windows 计算机上执行管理操作时，建议采用这种安全上下文。 如果希望 Runbook 针对本地计算机的外部资源运行任务，则可能需要在自动化帐户中定义安全的凭据资产，以便从 Runbook 对其进行访问，并用其对外部资源进行身份验证。 可以将 Runbook 中的[凭据](automation-credentials.md)、[证书](automation-certificates.md)和[连接](automation-connections.md)资产用于 cmdlet，以便指定身份验证凭据。

存储在 Azure 自动化中的 DSC 配置可直接应用于 Azure 虚拟机。 其他物理计算机和虚拟机可从 Azure 自动化 DSC 拉取服务器请求配置。  管理本地物理或虚拟 Windows 系统和 Linux 系统的配置时，不需部署任何支持自动化 DSC 请求服务器的基础结构，只需通过自动化 DSC 管理每个系统的出站 Internet 访问（经 TCP 端口 443 与 OMS 服务通信）即可。   

![Azure 自动化概述](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

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
* 至少双核
* 至少 4 GB RAM

## <a name="security"></a>“安全”
可以通过 Azure 自动化，针对本地资源、Azure 资源以及其他云提供程序资源自动执行任务。  为了使 Runbook 执行所需操作，Runbook 必须有权使用订阅中所需的最小权限来安全地访问资源。  

### <a name="automation-account"></a>自动化帐户 
所有使用 Azure 自动化中的 Azure cmdlet 针对资源执行的自动化任务在向 Azure 进行身份验证时，都使用基于 Azure Active Directory 组织标识凭据的身份验证。  自动化帐户独立于用来登录到门户，对 Azure 资源进行配置和使用的帐户。  

每个自动化帐户的自动化资源与单个 Azure 区域相关联，但自动化帐户可以管理订阅中的所有资源。 如果策略要求将数据和资源隔离到特定的区域，请在不同区域中创建自动化帐户。

> [!NOTE]
> 无法在 Azure 经典门户中访问使用在 Azure 门户创建的自动化帐户及其包含的资源。 如果你想要使用 Windows PowerShell 来管理这些帐户或其资源，必须使用 Azure 资源管理器模块。
> 

在 Azure 门户中创建自动化帐户时，会自动创建两个身份验证实体：

* 运行方式帐户。 此帐户在 Azure Active Directory (Azure AD) 中创建一个服务主体，并创建一个证书。 此外，它还向参与者分配基于角色的访问控制 (RBAC)，以便使用 Runbook 管理 Resource Manager 资源。
* 经典运行方式帐户。 此帐户上载一个管理证书，用于通过 Runbook 管理经典资源。

基于角色的访问控制在 Azure Resource Manager 中可用，向 Azure AD 用户帐户和运行方式帐户授予允许的操作，并对该服务主体进行身份验证。  请阅读 [Azure 自动化中基于角色的访问控制](automation-role-based-access-control.md)一文，详细了解如何开发自动化权限管理模型。  



#### <a name="authentication-methods"></a>身份验证方法
下表总结了 Azure 自动化所支持的每个环境的不同身份验证方法。

| 方法 | 环境 
| --- | --- | 
| Azure 运行方式帐户和经典运行方式帐户 |Azure Resource Manager 部署和 Azure 经典部署 |  
| Azure AD 用户帐户 |Azure Resource Manager 部署和 Azure 经典部署 |  
| Windows 身份验证 |本地数据中心或使用混合 Runbook 辅助角色的其他云提供程序 |  
| AWS 凭据 |Amazon Web Services |  

**How to\Authentication and Security** 部分是支持文章，这些文章概述了如何通过专用于上述环境的现有帐户或新帐户为此类环境配置身份验证，并提供了相应的实现步骤。  [使用 PowerShell 更新自动化运行方式帐户](automation-update-account-powershell.md)主题适用于 Azure 运行方式和经典运行方式帐户，介绍了如何使用 PowerShell 将现有的自动化帐户更新为运行方式帐户（如果最初未为其配置运行方式或经典运行方式帐户）。   
 
## <a name="network"></a>网络
要使混合 Runbook 辅助角色连接并注册到 Microsoft Operations Management Suite (OMS)，必须让其有权访问下述端口号和 URL。  除了这些端口和 URL 以外，还需有权访问连接到 OMS 时 [Microsoft Monitoring Agent 需要的端口和 URL](../log-analytics/log-analytics-proxy-firewall.md#configure-settings-with-the-microsoft-monitoring-agent)。 如果使用代理服务器在代理与 OMS 服务之间通信，则需确保能够访问相应的资源。 如果使用防火墙来限制对 Internet 的访问，则需要将防火墙配置为允许访问。

下面的信息列出了混合 Runbook 辅助角色与自动化通信时所要使用的端口和 URL。

* 端口：只需使用 TCP 443 进行出站 Internet 访问
* 全局 URL：*.azure-automation.net

如果你为特定的区域定义了自动化帐户并想要限制与该区域数据中心之间的通信，请参考下表中为每个区域提供的 DNS 记录。

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

## <a name="implementation"></a>实现

### <a name="creating-an-automation-account"></a>创建自动化帐户

可以通过不同方式在 Azure 门户中创建自动化帐户。  下表介绍了每种类型的部署体验以及其间的差异。  

|方法 | 说明 |
|-------|-------------|
| 从应用商店选择“自动化与控制” | 一种服务，用于创建自动化帐户和 OMS 工作区，二者在同一资源组和区域中互相关联。  它还部署更改跟踪和更新管理解决方案（默认启用）。 |
| 从应用商店选择“自动化” | 在新的或现有的资源组中创建自动化帐户，该资源组与 OMS 工作区没有关联，也不包含“自动化与控制”服务提供的任何解决方案。 这是自动化操作入门的基本配置，你可以借此了解如何编写 Runbook 和 DSC 配置，以及如何使用该服务的功能。 |
| 选定管理解决方案 | 如果从**[更新管理](../operations-management-suite/oms-solution-update-management.md)**、**[在非工作时间启动/停止 VM](automation-solution-vm-management.md)** 或**[更改跟踪](../log-analytics/log-analytics-change-tracking.md)**中选择一个解决方案，系统会提示你选择现有的自动化与 OMS 工作区，或者允许你二者都创建，这是在订阅中部署解决方案所必需的。 |

本主题演示如何载入“自动化与控制”服务，以便创建自动化帐户和 OMS 工作区。  若要创建独立的自动化帐户，以便进行测试或对服务进行预览，请参阅[创建独立的自动化帐户](automation-create-standalone-account.md)一文。  

### <a name="create-automation-account-integrated-with-log-analytics"></a>创建集成 Log Analytics 的自动化帐户
若要载入自动化，建议的方法是从应用商店选择“自动化与控制”服务。  这将创建自动化帐户并建立与 OMS 工作区的集成，包括在服务中提供安装管理解决方案的选项。  

>[!NOTE]
>若要创建自动化帐户，你必须是“服务管理员”角色的成员，或者是授予订阅访问权限的订阅共同管理员。 此外，必须将某个用户添加到该订阅的默认 Active Directory 实例。 不需要将该帐户分配到特权角色。
>
>如果你在被添加到订阅共同管理员角色之前不是订阅的 Active Directory 实例的成员，则会将你作为来宾添加到 Active Directory。 在此实例中，“添加自动化帐户”边栏选项卡中会显示 “你无权创建...”警告。
>
>可以先从订阅的 Active Directory 实例中删除已添加到共同管理员角色的用户，然后重新添加，使其成为 Active Directory 中的完整用户。 若要验证这种情况，可在 Azure 门户的“Azure Active Directory”窗格中选择“用户和组”，选择“所有用户”，在选择特定的用户后再选择“配置文件”。 用户配置文件下的“用户类型”属性值不应等于“来宾”。

1. 以订阅管理员角色成员和订阅共同管理员的帐户登录 Azure 门户。

2. 单击“新建” 。<br><br> ![在 Azure 门户中选择“新建”选项](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. 搜索“自动化”，然后在搜索结果中选择“自动化与控制”*。<br><br> ![从应用商店搜索并选择“自动化与控制”](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)。<br>   

4. 在阅读服务说明后，单击“创建”。  

5. 在“自动化与控制”设置边栏选项卡上，选择“OMS 工作区”。  在“OMS 工作区”边栏选项卡上，选择一个 OMS 工作区（其关联到的 Azure 订阅正是自动化帐户所在的订阅），或者创建一个 OMS 工作区。  如果没有 OMS 工作区，请选择“新建工作区”，然后在“OMS 工作区”边栏选项卡上执行以下操作： 
   - 指定新 **OMS 工作区**的名称。
   - 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   - 对于“资源组”，可以创建资源组，也可以选择现有资源组。  
   - 选择“位置” 。  目前提供的位置只有“澳大利亚东南部”、“美国东部”、“东南亚”、“美国中西部”和“西欧”。
   - 选择“定价层” 。  该解决方案提供两种定价层：“免费”层和“按节点 (OMS)”层。  免费层的每日可收集数据量、保留期和 Runbook 作业运行时分钟数有限制。  “按节点 (OMS)”层对每日收集的数据量没有限制。  
   - 选择“自动化帐户”。  若要创建新的 OMS 工作区，则还需创建一个与前面指定的新 OMS 工作区相关联的自动化帐户，其中包括 Azure 订阅、资源组和区域。  可以选择“创建自动化帐户”，然后在“自动化帐户”边栏选项卡上提供以下信息： 
  - 在“名称”字段中输入自动化帐户的名称。

    系统会根据所选的 OMS 工作区自动填充所有其他选项，无法修改这些选项。  Azure 运行方式帐户是此服务的默认身份验证方法。  单击“确定”后，系统会验证配置选项并创建自动化帐户。  可以在菜单中的“通知”下面跟踪操作进度。 

    否则，请选择现有的自动化运行方式帐户。  选择的帐户不能已关联到另一 OMS 工作区，否则会在边栏选项卡中显示一条通知消息。  如果该帐户已进行关联，则需选择其他自动化运行方式帐户，或者创建一个。

    提供完所需信息以后，请单击“创建”。  此时会对信息进行验证，并创建自动化帐户和运行方式帐户。  你会自动返回到“OMS 工作区”边栏选项卡。  

6. 在“OMS 工作区”边栏选项卡上提供所需信息后，单击“创建”。  在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。  随后将返回到“添加解决方案”边栏选项卡。  

7. 在“自动化与控制”设置边栏选项卡上，确认你想要安装建议的预选解决方案。 如果取消选择某个解决方案，可以稍后单独进行安装。  

8. 单击“创建”，继续载入自动化和 OMS 工作区。 系统将验证所有设置，然后尝试在订阅中部署该服务。  此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。 

载入该服务以后，即可开始创建 Runbook，运行已启用的管理解决方案，或者开始使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics) 收集云或本地环境中的资源所生成的数据。   

### <a name="resources-included"></a>包含的资源
成功创建自动化帐户后，系统将自动为你创建几个资源。 以下两个表格汇总了这些资源：<br>

#### <a name="run-as-account-resources"></a>运行方式帐户资源

| 资源 | 说明 |
| --- | --- |
| AzureAutomationTutorial Runbook | 一个示例图形 Runbook，演示如何使用运行方式帐户进行身份验证并获取所有 Resource Manager 资源。 |
| AzureAutomationTutorialScript Runbook | 一个示例 PowerShell Runbook，演示如何使用运行方式帐户进行身份验证并获取所有 Resource Manager 资源。 |
| AzureRunAsCertificate | 在创建自动化帐户时自动创建的，或针对现有帐户使用以下 PowerShell 脚本创建的证书资产。 使用该证书可向 Azure 进行身份验证，以便通过 Runbook 管理 Azure Resource Manager 资源。 该证书的有效期为一年。 |
| AzureRunAsConnection | 在创建自动化帐户时自动创建的，或针对现有帐户使用 PowerShell 脚本创建的连接资产。 |

#### <a name="classic-run-as-account-resources"></a>经典运行方式帐户资源

| 资源 | 说明 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook | 一个示例图形 Runbook，它使用经典运行方式帐户（证书）获取订阅中使用经典部署模型中创建的所有 VM，然后写入 VM 名称和状态。 |
| AzureClassicAutomationTutorial 脚本 Runbook | 一个示例 PowerShell Runbook，它使用经典运行方式帐户（证书）获取订阅中的所有经典 VM，然后写入 VM 名称和状态。 |
| AzureClassicRunAsCertificate | 自动创建的证书资产，用于向 Azure 进行身份验证，以便从 Runbook 管理 Azure 经典资源。 该证书的有效期为一年。 |
| AzureClassicRunAsConnection | 自动创建的连接资产，用于向 Azure 进行身份验证，以便从 Runbook 管理 Azure 经典资源。 |向 Azure 进行身份验证，以便通过 Runbook 管理 Azure 经典资源。 |

## <a name="next-steps"></a>后续步骤
* 可以参阅[测试 Azure 自动化运行方式帐户身份验证](automation-verify-runas-authentication.md)一文，确认新的自动化帐户能否针对 Azure 资源进行身份验证。
* 若要开始使用 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要了解有关图形创作的详细信息，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。


