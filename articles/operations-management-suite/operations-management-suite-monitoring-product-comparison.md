---
title: "Microsoft 监视产品比较 | Microsoft 文档"
description: "Microsoft Operations Management Suite (OMS) 是 Microsoft 的基于云的 IT 管理解决方案，可帮助你管理和保护你的本地和云基础结构。  本文标识了 OMS 中包括的不同服务并提供了指向其详细内容的链接。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: a63ca0ad-61f8-425d-a48c-d87ba518c104
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d7325b0bb900dcaf789c01e1a9b534d7f72c8c43


---
# <a name="microsoft-monitoring-product-comparison"></a>Microsoft 监视产品比较
本文在以下方面比较了 Operations Management Suite (OMS) 中的 System Center Operations Manager (SCOM) 和 Log Analytics：体系结构、如何监视资源的逻辑以及如何对所收集数据执行分析。  旨在使你基本了解它们的差异和相对优势。  

## <a name="basic-architecture"></a>基本体系结构
### <a name="system-center-operations-manager"></a>System Center Operations Manager
所有 SCOM 组件均安装在你的数据中心中。  [代理安装](http://technet.microsoft.com/library/hh551142.aspx)在由 SCOM 管理的 Windows 和 Linux 计算机上。  代理连接到可与 SCOM 数据库和数据仓库通信的[管理服务器](https://technet.microsoft.com/library/hh301922.aspx)。  代理依赖于域身份验证来连接到管理服务器。  受信任的域之外的代理可以执行证书身份验证或连接到[网关服务器](https://technet.microsoft.com/library/hh212823.aspx)。

SCOM 需要两个 SQL 数据库，一个用于操作数据，另一个数据仓库支持报告和数据分析。  [报告服务器](https://technet.microsoft.com/library/hh298611.aspx)运行 SQL 报告服务，以报告数据仓库中的数据。 

对于诸如 [Azure](https://www.microsoft.com/download/details.aspx?id=38414)、[Office 365](https://www.microsoft.com/download/details.aspx?id=43708) 和 [AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html) 的产品，SCOM 可使用管理包监视云资源。  这些管理包将一个或多个本地代理用作发现云资源和运行工作流的代理，测量其性能和可用性。  代理程序也用于[监视网络设备](https://technet.microsoft.com/library/hh212935.aspx)和其他外部资源。

操作控制台是 Windows 应用程序，可连接到管理服务器之一，允许管理员查看和分析所收集数据并配置 SCOM 环境。  基于 Web 的控制台可以托管在任何 IIS 服务器上并通过浏览器提供数据分析。

![SCOM 体系结构](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>Log Analytics
大多数 OMS 组件都在 Azure 云中，因此只需最少成本和管理工作即可部署和管理它。  由 Log Analytics 收集的所有数据均存储在 OMS 存储库中。

Log Analytics 可从以下三个来源之一收集数据：

* 运行 Windows、[Microsoft Monitoring Agent (MMA)](https://technet.microsoft.com/library/mt484108.aspx) 或 Linux 和 [Operations Management Suite Agent for Linux](https://technet.microsoft.com/library/mt622052.aspx) 的物理机和虚拟机。  这些虚拟机可能是本地虚拟机，也可能是 Azure 或其他云中的虚拟机。
* 具有 Azure 辅助角色、Web 角色或虚拟机所收集的 [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)数据的 Azure 存储帐户。
* [与 SCOM 管理组的连接](https://technet.microsoft.com/library/mt484104.aspx)。  在此配置中，代理与 SCOM 管理服务器通信，可将数据传递到 SCOM 数据库，随后再传递到 OMS 数据存储。
  管理员通过 OMS 门户分析收集的数据并配置 Log Analytics，它托管在 Azure 中，可从任何浏览器进行访问。  针对标准平台，提供可访问此数据的移动应用。

![Log Analytics 体系结构](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>集成 SCOM 和 Log Analytics
当 SCOM 用作 Log Analytics 的数据源时，你可以在混合监视环境中利用这两个产品的功能。  除了继续从 SCOM 运行管理包外，还可通过操作控制台配置由 OMS 管理的现有 SCOM 代理。  
连接的 SCOM 管理组中的数据通过以下四种方法之一传递到 Log Analytics：

* 事件和性能数据由代理收集并传递到 SCOM。  然后，SCOM 中的管理服务器将该数据传递到 Log Analytics。
* 诸如 IIS 记录的某些事件和安全事件将继续从代理直接传递到 Log Analytics。
* 某些解决方案将其他软件传递到代理或要求安装软件以收集其他数据。  此数据通常直接发送到 Log Analytics。
* 某些解决方案直接从不是源自代理的 SCOM 管理服务器收集数据。  例如，[警报管理解决方案](https://technet.microsoft.com/library/mt484092.aspx)从 SCOM 收集已创建的警报。

## <a name="monitoring-logic"></a>监视逻辑
SCOM 和 Log Analytics 使用从代理收集的相似数据，但在以下方面存在基本差异：针对数据收集定义和实现其逻辑的方式以及分析所收集数据的方式。

### <a name="operations-manager"></a>Operations Manager
在[管理包](https://technet.microsoft.com/library/hh457558.aspx)中实现 SCOM 的监视逻辑，其中包含用于发现要监视的组件、测量这些组件的运行状况和收集要分析的数据的逻辑。  监视数据可以像收集事件或性能计数器一样简单，它也可以使用在脚本中实现的复杂逻辑。  除了硬件和网络设备，包括完整监视的管理包还可用于各种 [Microsoft 和第三方应用程序](http://go.microsoft.com/fwlink/?LinkId=82105)。  可针对自定义应用程序[创建自己的管理包](http://aka.ms/mpauthor)。

管理包包含多个工作流，每个工作流可执行一些不同的监视功能，例如对性能计数器进行采样、检查服务状态或运行脚本。  每个工作流独立运行并定义其自己的结果，例如它将写入哪个数据库以及是否生成警报。 

可重写工作流的详细信息，例如运行频率、视为错误的阈值和所生成警报的严重性。  还可以通过添加你自己的工作流提供其他功能。

![重写](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

管理包安装在 Operations Manager 数据库中，并通过管理服务器自动分配到代理。  每个代理将自动下载管理包，并加载与它们安装的应用程序相关的工作流。  代理所收集的数据将传递回管理服务器，以供插入 SCOM 数据库和数据仓库。  操作控制台允许你通过管理包中包含的自定义视图、仪表板和报告查看和分析此数据。

下图阐释了管理包的分发过程。

![管理包流](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>Log Analytics
#### <a name="event-and-performance-collection"></a>事件和性能收集
Log Analytics 使用 Windows 事件日志、IIS 日志和 Syslog 等源从代理系统中收集事件和性能计数器。  你可以定义通过 Log Analytics 门户收集哪些数据的条件，然后创建日志查询分析所收集的数据。  创建 OMS 工作区时定义一组标准条件，并且可以为特定应用程序定义其他数据。 

![在 Log Analytics 中定义事件日志](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

尽管 SCOM 具有许多通常针对数据和应执行以实现响应的操作定义具体条件的详细工作流，但 Log Analytics 针对数据收集具有更常规的条件。  日志查询和解决方案提供更有针对性的条件，用于分析和处理云中所收集的特定数据。

#### <a name="solutions"></a>解决方案
解决方案提供用于数据收集和分析的其他逻辑。  可以选择将解决方案从解决方案库添加到 OMS 订阅。

![解决方案库](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

解决方案主要在云中运行，用于分析在 OMS 存储库中收集的事件和性能计数器。  解决方案也可以定义要收集的可使用日志查询或可通过 OMS 仪表板中的解决方案提供的其他用户界面进行分析的其他数据。 

例如，[更改跟踪解决方案](https://technet.microsoft.com/library/mt484099.aspx)可检测代理系统上的配置更改，并将事件写入到可通过汇总已检测更改的多个图形视图分析的 OMS 存储库。  可从汇总视图深入探索到显示该解决方案所收集的详细数据的日志查询。

尽管你可以选择要添加到你的订阅的解决方案，但当前无法创建你自己的解决方案。  你可以选择事件和性能计数器，根据自己的日志查询收集和创建自定义视图。

下图总结了 Log Analytics 的监视逻辑。

![Log Analytics 解决方案流](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>运行状况监视
### <a name="operations-manager"></a>Operations Manager
SCOM 可对应用程序的不同组件进行建模，并提供每个组件的实时运行状况。  这不仅使你可以随着时间的推移查看检测到的错误和性能，还可以在任一给定时间验证应用程序或系统及其每个组件的实际运行状况。  因为它了解应用程序可用的时间段，因此 SCOM 中的运行状况引擎还支持可随时间推移分析和报告应用程序可用性的服务级别协议 (SLA)。

例如，下面的视图显示了由 SCOM 监视的 SQL 数据库引擎的实时运行状况。  数据库引擎之一的每个数据库的运行状况显示在视图的下半部分。

![状态视图](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

数据库引擎之一的运行状况资源管理器显示在下面，带有用于确定其整体运行状况的监视器。  这些监视器在 SQL 管理包中定义，并针对 SCOM 发现的所有 SQL 数据库引擎运行。

![运行状况资源管理器](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

可以组合多个系统上的组件，测量已分配应用程序的运行状况。  对于包括多个已分配组件的业务线应用程序，这尤其有用。  可创建用于测量汇总到应用程序可用性的每个组件运行状况的模型。

Active Directory 是一个管理包的示例，用于提供分析其已分配组件的模型。  下面的示例图显示了整个环境的运行状况以及林、域和域控制器之间的关系。  与上述 SQL 示例相似，其中每个组件都包含子组件和多个监视器。

![SCOM 图示视图](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>Log Analytics
OMS 不包括用于对应用程序进行建模或测量其实时运行状况的通用引擎。  单独的解决方案可能根据所收集数据评估特定服务的总体运行状况，并且可能在代理上安装自定义逻辑来执行实时分析。  因为解决方案在云中运行并且有权访问 OMS 存储库，因此与通常由管理包执行的分析相比，它们可以提供更深入的分析。 

例如，[AD 评估 和 SQL 评估解决方案](https://technet.microsoft.com/library/mt484102.aspx)分析所收集数据并为环境的不同方面提供分级。  它包括可用于提高环境的可用性和性能的改进建议。

![AD 评估解决方案](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>数据分析
SCOM 和 Log Analytics 分别提供不同的功能来分析收集的数据。  SCOM 具有操作控制台中用于分析各种格式的最新数据的视图和仪表板以及用于显示数据仓库中表格形式的数据的报告。  Log Analytics 提供了完整的日志查询语言和界面，用于分析 OMS 存储库中的数据。  当 SCOM 用作 Log Analytics 的数据源时，存储库中包括 SCOM 收集的数据，因此 Log Analytics 工具可用于分析来自这两个系统的数据。

### <a name="operations-manager"></a>Operations Manager
#### <a name="views"></a>视图
操作控制台中的视图允许你以不同格式查看 SCOM 收集的不同数据类型，通常使用表格查看事件、警报和状态数据，使用折线图查看性能数据。  视图最小程度地分析或合并数据，但允许你根据特定条件进行筛选。 

![视图](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

管理包通常提供多个视图，可支持它监视的应用程序或系统。  这可能包括管理包发现的不同对象的状态视图、已检测问题的警报视图和计数器的性能视图。

视图尤其适用于分析环境的当前状态（包括打开警报）和已监视系统和对象的运行状况状态。  可深入了解有关支持特定警报以诊断其根本原因的详细事件或性能数据。 同样，可以查看应用程序的不同组件的性能和运行状况，以评估其当前运行状况。

#### <a name="dashboards"></a>仪表板
操作控制台中的仪表板主要使用与视图相同的数据，但前者更易于自定义并且可包含更丰富的可视化效果。  提供一组可针对自身目的轻松自定义的标准仪表板。  还可使用 PowerShell 小组件，用于显示 PowerShell 查询返回的数据。

![仪表板](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

开发人员能够向仪表板添加包含在其管理包中的自定义组件。  这些组件可能高度专用于特定应用程序，例如 SQL 管理包中的仪表板，如下所示。  此仪表板还可以用作自定义版本的模板。

![SQL 仪表板](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>报告
SCOM 中的报告分析数据仓库中表格形式的数据。  可以打印和计划这些数据，以便通过不同的文件格式（包括 PDF、CSV 和 Word）自动提交。  报告使用数据仓库中的数据，因此它们尤其适合长期趋势的分析。

管理包通常为特定应用程序提供自定义报告。  你还可以从通用报告库中进行选择，可为你自己的应用程序自定义这些报告，或用于执行临时分析。

下面是性能报告示例，显示了由 Active Directory 管理包收集的数据。

![报表](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>Log Analytics
Log Analytics 具有[查询语言](https://technet.microsoft.com/library/mt484120.aspx)，可用于对来自多个应用程序的数据执行分析，无需创建自定义视图或报告。  因为 OMS 在云中实现，因此查询和数据分析的性能不受任何硬件限制约束，并且可以快速分析包括数百万条记录的查询。 

Log Analytics 中的查询也是其他功能的基础。  你可以保存查询、将其结果导出到 Excel 或使其定期自动运行并在其结果与特定条件匹配时生成警报。  

![日志查询流](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

下面是 Log Analytics 查询的一个示例。  在此示例中，名称中包含“started”的所有事件都将返回并按事件 ID 分组。  用户只需提供查询，Log Analytics 即可动态生成用户界面来执行分析。  在列表中选择任何项，返回详细的事件数据。

![日志查询](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

除了提供临时分析，还可以保存 Log Analytics 中的查询以供将来使用，这些查询还会添加到你的 [OMS 仪表板](http://technet.microsoft.com/library/mt484090.aspx)，如以下示例所示。

![OMS 仪表板](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>后续步骤
* 部署 [System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh205987.aspx)。
* 注册 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics)。  




<!--HONumber=Nov16_HO3-->


