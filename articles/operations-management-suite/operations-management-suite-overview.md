---
title: "Operations Management Suite (OMS) 概述 | Microsoft 文档"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 基于云的 IT 管理解决方案，有助于管理和保护本地和云基础结构。  本文介绍 OMS 的作用，阐述 OMS 中包含的不同服务和产品并提供其详细内容的链接。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 452dd602387db6db04ca87f6834c9e8606185484
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017


---
# <a name="what-is-operations-management-suite-oms"></a>什么是 Operations Management Suite (OMS)？
本文介绍 Operations Management Suite (OMS)，包括它提供的商业价值、服务和管理解决方案以及将不同服务和解决方案打包在一起的产品的简要概述。  文中提供了有关部署和使用每种服务和解决方案的详细文档的链接。

## <a name="from-on-premises-to-the-cloud"></a>从本地到云
长期以来，Microsoft 一直在提供用于管理企业环境的产品。  2007 年的 System Center 管理套件中合并了多个产品。  其中包括提供软件分发和库存等功能的 Configuration Manager、提供系统和应用程序主动监视的 Operations Manager（包含用于将手动过程自动化的 Runbook），以及用于备份和恢复关键数据的 Data Protection Manager。

随着迁移到云中的计算资源越来越多，System Center 产品中融合了更多用于在云中管理资源的云功能，例如 Operations Manager 和 Orchestrator。  不过，这些功能在本质上仍然设计为本地解决方案，需要在部署和维护本地管理环境方面大量投资。  为了完全利用云并支持未来的应用程序，需要制定一套全新的管理方式。

## <a name="introducing-operations-management-suite"></a>Operations Management Suite 简介
Operations Management Suite（简称为 OMS）是在云中从无到有设计出的管理服务集合。  OMS 组件完全在 Azure 中托管，而不是部署和管理本地资源。  配置工作极少，基本上在几分钟内就能将它启动并运行。  

- **极低的部署成本与复杂性。**  由于 OMS 的所有组件和数据都存储在 Azure 中，因此在短时间就能将它启动并运行，没有本地组件那样的复杂性，也不需要额外的投资。
- **缩放到云级别。**  不必担心要为使用不上的计算资源付费，也不必担心存储空间耗尽，因为在云中只需为实际使用的资源付费，并且随时可以根据任何所需的负载进行缩放。  一开始可以管理少量的资源来帮助自己入门，然后根据整个环境扩展。
- **利用最新的功能。**  OMS 服务中的功能正在持续增加和更新。  用户始终可以访问最新的功能，根本不需要部署更新。
- **集成的服务。**  尽管每个 OMS 服务本身都能提供重要的价值，但它们也可以协同工作来解决复杂的管理方案。  例如，Azure 自动化中的 Runbook 可以配合 Azure Site Recovery 来驱动故障转移过程，然后将信息记录到 Log Analytics 来生成警报。
- **全球信息。**  OMS 中的管理解决方案可持续访问最新的信息。  例如，安全和审核解决方案可以使用在全球检测到的最新威胁来执行威胁分析。
- **从任何位置访问。**  只要有浏览器，就能从任何位置访问管理环境。  在智能手机上安装 OMS 应用即可随时访问监视数据。

### <a name="is-it-just-for-the-cloud"></a>它只适用于云环境吗？
不能仅仅因为 OMS 服务在云中运行，就认为它们无法有效管理本地环境。  在数据中心内的任意 Windows 或 Linux 计算机上安装一个代理，就能将数据发送到 Log Analytics，可在其中连同从云或本地服务收集的其他所有数据一起进行分析。  使用 Azure 备份和 Azure Site Recovery 可以利用云来实现本地资源的备份和高可用性。  
云中的 Runbook 通常无法访问本地资源，但你也可以在数据中心内托管 Runbook 的一台或多台计算机上安装代理。  启动 Runbook 时，只需指定是要让它在云中还是本地辅助角色上运行即可。

## <a name="hybrid-management-with-system-center"></a>使用 System Center 进行混合管理
如果有现有的 System Center 安装，可将这些组件与 OMS 服务集成，利用每种产品的相关专业技术为本地和云环境提供混合解决方案。  将现有 Operations Manager 管理组连接到 Log Analytics 可以分析云中托管的代理。  结合 Data Protection Manager 使用现有备份过程可将数据备份到云中。  


## <a name="oms-services"></a>OMS 服务
OMS 的核心功能由 Azure 中运行的一组服务提供。  每个服务提供特定的管理功能，可以组合这些服务来实现不同的管理方案。

|| 服务 | 说明 |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | 监视和分析不同资源（包括物理机和虚拟机）的可用性与性能。 |
| ![Azure 自动化](media/operations-management-suite-overview/icon-automation.png) | 自动化 | 将手动过程自动化，实施物理机和虚拟机的配置。 |
| ![Azure 备份](media/operations-management-suite-overview/icon-backup.png) | 备份 | 备份和还原关键数据。 |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | 站点恢复 | 为关键应用程序提供高可用性。 |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。  这些数据可能包括事件、性能数据或通过 API 提供的自定义数据。 收集后，可以分析、导出数据或针对它们发出警报。  使用这种方法可以整合来自各种源的数据，因此可将 Azure 服务中的服务合并到现有的本地环境。  此外，它还能将数据收集与针对该数据执行的操作明确区分开来，以便能够针对所有类型的数据执行所有操作。  

![Log Analytics 概述](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>收集数据
可通过多种方法将数据收集到存储库，供 Log Analytics 分析。

- **Windows 或 Linux 计算机和虚拟机。**  在要从中收集数据的 [Windows](../log-analytics/log-analytics-windows-agents.md) 和 [Linux](../log-analytics/log-analytics-linux-agents.md) 计算机或虚拟机上安装 Microsoft 监视代理。  该代理将自动从 Log Analytics 下载用于定义要收集的事件和性能数据的配置。  可以使用 Azure 门户在 Azure 中运行的虚拟机上轻松安装该代理。  如果有现有的 Operations Manager 环境，可将管理组连接到 Log Analytics，然后从所有现有代理自动开始收集数据。
- **Azure 服务。**  Log Analytics 将 [Azure 诊断和 Azure 监视](../log-analytics/log-analytics-azure-storage.md)中的遥测数据收集到存储库，以便可以监视 Azure 资源。
- **数据收集器 API。**  Log Analytics 提供[用于从任何客户端填充数据的 REST API](../log-analytics/log-analytics-data-collector-api.md)。  可以使用此 API 从第三方应用程序收集数据，或实现自定义管理方案。  一种常用方法是使用 Azure 自动化中的 Runbook 收集数据，然后使用数据收集器 API 将数据写入存储库。

#### <a name="reporting-and-analyzing-data"></a>报告和分析数据
Log Analytics 包含强大的查询语言，可提取存储库中存储的数据。  由于所有源中的数据以记录的形式存储，因此使用单个查询就能分析多个源中的数据。
  
除了即席分析以外，Log Analytics 还提供多种方法来报告和分析查询中的数据。

- **视图和仪表板。**  [视图](../log-analytics/log-analytics-view-designer.md)和[仪表板](../log-analytics/log-analytics-dashboards.md)可在门户中将查询结果可视化。  管理解决方案通常包含可用于分析解决方案中的数据的视图。  还可以创建自己的自定义视图来分析数据，并随时在自定义门户中提供这些数据。
- **导出。**  可以使用相应的选项来导出任何查询的结果，以便在 Log Analytics 的外部进行分析。  甚至可以在提供强大可视化与分析功能的 [Power BI](../log-analytics/log-analytics-powerbi.md) 中计划定期导出。
- **日志搜索 API。**  Log Analytics 提供[用于从任何客户端收集数据的 REST API](../log-analytics/log-analytics-log-search-api.md)。  使用此 API 能够以编程方式处理在存储库中收集的数据，或者通过其他监视工具访问这些数据。

#### <a name="alerting"></a>警报
检测到问题时，Log Analytics可以[主动发出警报](../log-analytics/log-analytics-alerts.md)或采取纠正措施。  与 Log Analytics 中的其他所有分析功能一样，这也是通过日志搜索实现的。  这种搜索按定期计划运行，如果结果与特定的条件匹配，则创建警报。

![Log Analytics 警报](media/operations-management-suite-overview/overview-alerts.png)

除了在 Log Analytics 存储库中创建警报记录以外，发生警报时还可以采取以下措施。

- **电子邮件。**  发送一封电子邮件，主动告知检测到的问题。
- **Runbook。**  Log Analytics 中的警报可以启动 Azure 自动化中的 Runbook。  这样做的目的通常是为了尝试纠正检测到的问题。  如果 Azure 或其他云中出现问题，可在云中启动 Runbook；如果物理机或虚拟机上出现问题，可在本地代理上启动 Runbook。
- **Webhook。**  警报可以启动 Webhook，并向其传递日志搜索结果中的数据。  这样，便可以实现与外部服务（例如备用警报系统）的集成，或者尝试针对外部网站采取纠正措施。

### <a name="azure-automation"></a>Azure 自动化
[Azure 自动化](http://azure.microsoft.com/documentation/services/automation)为 OMS 提供过程自动化和配置管理。  它可以将手动过程自动化，帮助实施物理和虚拟计算机的配置。  

#### <a name="process-automation"></a>过程自动化
Azure 自动化使用基于 PowerShell 脚本或 PowerShell 工作流的 [Runbook](../automation/automation-runbook-types.md) 将手动过程自动化。  它还包含资产支持 Runbook，例如，可在多个 Runbook 之间共享的变量，以及用于存储 Runbook 进行身份验证时可能需要的已加密信息的凭据与连接。
Runbook 为套件中的其他服务提供过程自动化。  由于可以使用 PowerShell 或 REST API 访问其他每个服务，因此可以创建 Runbook 来执行在 Log Analytics 中收集管理数据或使用 Azure 备份启动备份等功能。

##### <a name="accessing-resources"></a>访问资源
由于 Runbook 基于 PowerShell，因此能够管理可通过 PowerShell cmdlet 访问的任何资源。  在自动化帐户中[加载模块](../automation/automation-integration-modules.md)时，该模块可供该帐户中的所有 Runbook 使用。 
 
在云中运行 Runbook 时，这些 Runbook 可从云访问任何资源。  这包括你的 Azure 订阅、Amazon Web Services (AWS) 等其他云或可通过 REST API 访问的服务中的资源。  云中的 Runbook 不使用任何凭据运行，但可以利用凭据、连接和证书等自动化资产向它们访问的资源进行身份验证。

数据中心内的资源很有可能无法通过云中运行的 Runbook 访问。  不过，可在数据中心内安装一个或多个[混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)，以运行需要访问本地资源的 Runbook。  启动 Runbook 时，可以指定是要让它在云中还是特定的辅助角色上运行。

![Azure 自动化 Runbook](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>启动 Runbook
可[通过多种方法启动](../automation/automation-starting-a-runbook.md) Runbook，以便可以将它们包含在多个管理方案中。  

- **Azure 门户。**  与其他 Azure 服务一样，可以通过 Azure 门户管理 Azure 自动化。  除了启动 Runbook 以外，还可以导入 Runbook 或创作自己的 Runbook。
- **计划。**  可将 Runbook 计划为定期启动。  这样，便可以自动重复某个定期管理过程，或者将数据收集到 Log Analytics。
- **PowerShell 和 API。**  可以通过 PowerShell cmdlet 或 Azure 自动化 REST API 启动 Runbook 并向其传递所需的参数信息。  
- **Webhook。**  可为任何 Runbook 创建 Webhook，以便能够从外部应用程序或网站启动该 Runbook。
- **Log Analytics 警报。**  Log Analytics 中的警报能够自动启动 Runbook，以尝试纠正警报识别到的问题。

#### <a name="configuration-management"></a>配置管理
[PowerShell Desired State Configuration (DSC)](../automation/automation-dsc-overview.md) 是 Windows PowerShell 中用于部署和实施物理机与虚拟机配置的管理平台。  Azure 自动化可管理 DSC 配置，在云中提供代理可以访问的“拉”服务器用于检索所需的配置。

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure 备份和 Azure Site Recovery
Azure 备份和 Azure Site Recovery 能够为业务连续性和灾难恢复提供辅助。  它们各自的功能可帮助确保在发生服务中断时应用程序保持可用，在系统重新联机时恢复正常操作。  这两个服务都有助于实现针对组织定义的恢复点目标 (RPO) 和恢复时间目标 (RTO)。 RPO 定义在发生服务中断期间数据处于不可用状态的可接受限制，RTO 定义在发生服务中断期间服务或应用处于不可用状态的时间限制。

#### <a name="azure-backup"></a>Azure 备份
[Azure 备份](http://azure.microsoft.com/documentation/services/backup)为 OMS 提供数据备份和还原服务。  它可以保护应用程序数据，并且无需资本投资、只需最低的运行成本，即可将这些数据保留多年。  它可以备份物理和虚拟 Windows 服务器以及 SQL Server 和 SharePoint 等应用程序工作负荷中的数据。  System Center Data Protection Manager (DPM) 也可使用它将受保护的数据复制到 Azure，以实现冗余和长期存储。

Azure 备份中的受保护数据存储在位于特定地理区域的备份保管库。 数据在同一区域内复制，并且根据保管库类型，可能还会复制到其他区域以进一步实现复原能力。

Azure 备份有三种基本方案。

- **使用 Azure 备份代理的 Windows 计算机。** 将文件和文件夹从任何 Windows 服务器或客户端直接备份到 Azure 备份保管库。<br><br>![使用 Azure 备份代理的 Windows 计算机](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) 或 Microsoft Azure 备份服务器。** 利用 DPM 或 Microsoft Azure 备份服务器将文件和文件夹以及 SQL 和 SharePoint 等应用程序工作负荷备份到本地存储器，再复制到 Azure 备份保管库。 支持 Hyper-V 或 VMware 上的 Windows 和 Linux 虚拟机。<br><br>![System Center Data Protection Manager (DPM) 或 Microsoft Azure 备份服务器](media/operations-management-suite-overview/overview-backup-02.png)
- **Azure 虚拟机扩展。** 将 Azure 中的 Windows 或 Linux 虚拟机备份到 Azure 备份保管库。<br><br>![Azure 虚拟机扩展](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 通过协调本地虚拟机和物理机到 Azure 或辅助站点的复制来提供业务连续性。 如果主站点不可用，可故障转移到辅助位置，使用户能够继续工作，系统恢复正常后可故障回复。 

Azure Site Recovery 为服务器和应用程序提供高可用性。  它能够为业务连续性和灾难恢复 (BCDR) 策略提供辅助，因为可以协调本地 Hyper-V 虚拟机、VMware 虚拟机和 Windows/Linux 物理服务器的复制故障转移和恢复。 可将计算机复制到辅助数据中心，或将其复制到 Azure 以扩展数据中心。 Site Recovery 还为工作负荷提供简单的故障转移和恢复。 它与 SQL Server AlwaysOn 等灾难恢复机制集成，提供恢复计划以便对多台计算机间分层的工作负荷实现轻松故障转移。

Azure Site Recovery 有三种基本复制方案。

- **复制 Hyper-V 虚拟机。**  如果 Hyper-V 虚拟机在 VMM 云中托管，则可复制到辅助数据中心或 Azure 存储。 通过安全的 Internet 连接复制到 Azure。 通过 LAN 复制到辅助数据中心。  如果 Hyper-V 虚拟机不由 VMM 托管，则只能复制到 Azure 存储。 通过安全的 Internet 连接复制到 Azure。<br><br>![复制 Hyper-V 虚拟机](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **复制 VMware 虚拟机。**  可将 VMware 虚拟机复制到运行 VMware 的辅助数据中心或复制到 Azure 存储。 可通过站点到站点 VPN 或 Azure ExpressRoute 或安全的 Internet 连接复制到 Azure。 通过 InMage Scout 数据通道复制到辅助数据中心。<br><br>![复制 VMware 虚拟机](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **复制 Windows 和 Linux 物理服务器。**  可将物理服务器复制到辅助数据中心或 Azure 存储。 可通过站点到站点 VPN 或 Azure ExpressRoute 或安全的 Internet 连接复制到 Azure。 通过 InMage Scout 数据通道复制到辅助数据中心。 Azure Site Recovery 具有的 OMS 解决方案可显示一些统计信息，但必须使用 Azure 门户进行任何操作。<br><br>![复制物理 Windows 和 Linux 服务器](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery 将元数据存储在位于特定 Azure 地理区域的保管库。 Site Recovery 服务不存储任何复制的数据。

## <a name="management-solutions"></a>管理解决方案
[管理解决方案](operations-management-suite-solutions.md)是预先打包的逻辑集，可以实现利用一个或多个 OMS 服务的特定管理方案。  Microsoft 与合作伙伴提供不同的解决方案，可将其添加到 Azure 订阅，提高 OMS 投资的价值。  合作伙伴可以创建自己的解决方案来支持自己的应用程序和服务，并通过 Azure Marketplace 或快速启动模板将它们提供给用户。

[更新管理解决方案](oms-solution-update-management.md)就是利用多个服务提供更多功能的解决方案的一个好例子。  此解决方案使用适用于 Windows 和 Linux 的 Log Analytics 代理来收集有关每个代理上所需的更新的信息。  它将这些数据写入 Log Analytics 存储库，你可以使用随附的仪表板来分析这些数据。  创建部署时，可以使用 Azure 自动化中的 Runbook 来安装所需的更新。  可在管理门户中管理整个过程，无需担心如何获取基础详细信息。

![解决方案](media/operations-management-suite-overview/overview-solution.png)

大多数解决方案可以执行以下一项或多项功能。

- 收集其他信息  Log Analytics 从客户端和服务收集各种数据，包括事件和性能数据。  管理解决方案通常使用 Azure 自动化 Runbook 收集其他数据源中不提供的其他信息。
- 针对收集的信息提供附加分析。  管理解决方案包含仪表板和视图，可针对数据提供分析与可视化。  这些信息将链接回到预定义的日志搜索，方便用户钻取详细数据。  它们还可以针对已收集到存储库中的数据执行分析，例如，在所有安全事件中搜索指示出现威胁的模式。
- 添加功能。  Microsoft 提供的某些解决方案可能构建在核心服务的功能基础之上，以提供更多的功能。  例如，服务映射提供自身的控制台用于实时发现和映射服务器与进程依赖关系。
Microsoft 与合作伙伴会定期将解决方案添加到 OMS，使用户能够持续提高投资价值。  可以通过 OMS 门户中的“解决方案目录”浏览并安装 Microsoft 解决方案，或者在 Azure 门户中通过 Azure Marketplace 来浏览并安装 Microsoft 与合作伙伴的解决方案。  

![解决方案库](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>后续步骤
* 了解 [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 的相关信息。
* 了解 [Azure 自动化](../automation/automation-intro.md)的相关信息。
* 了解 [Azure 备份](http://azure.microsoft.com/documentation/services/backup)的相关信息。
* 了解 [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) 的相关信息。
* 发现不同 OMS 产品中[提供的解决方案](../log-analytics/log-analytics-add-solutions.md)。 


