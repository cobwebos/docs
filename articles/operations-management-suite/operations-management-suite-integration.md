---
title: "与 Operations Management Suite (OMS) 集成 | Microsoft 文档"
description: "除了使用 OMS 的标准功能外，还可以将其与其他管理应用程序和服务集成，以提供混合管理环境、提供特定于你的环境的自定义管理方案或向你的客户提供自定义管理体验。  本文概述了用于与 OMS 集成的不同选项以及指向提供详细技术信息的文章链接。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 31291a9b0f452adeae42f744a976c14e61a4b303
ms.lasthandoff: 11/17/2016


---
# <a name="integrating-with-operations-management-suite-oms"></a>与 Operations Management Suite (OMS) 集成
Operations Management Suite 是 Microsoft 的基于云的 IT 管理解决方案，可帮助你管理和保护你的本地和云基础结构。  除了使用 OMS 的标准功能外，还可以将其与其他管理应用程序和服务集成，以提供混合管理环境、提供特定于你的环境的自定义管理方案或向你的客户提供自定义管理体验。  本文概述了用于与 OMS 服务集成的不同选项以及指向提供详细技术信息的文章链接。 

## <a name="log-analytics"></a>Log Analytics
Log Analytics 收集的管理数据存储在 Azure 中托管的存储库中。  存储在存储库中的所有数据均可用于日志搜索，可快速分析极大量的数据。  集成要求可能是将新数据填充在存储库中以供分析，或提取存储库中的数据以提供新可视化或与其他管理工具集成。

存储库中的每段数据作为记录存储。  填充存储库时，应向用户提供你的解决方案使用的记录类型及其属性说明。  检索数据时，需要你正在处理的数据的此相关信息。

![填充 OMS 存储库](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>填充 Log Analytics 存储库
有多种方法可填充 OMS 存储库。  你使用的方法取决于如下因素：源数据所在位置、数据格式和需要支持的客户端。  数据存储在存储库中后，数据收集方式没有任何区别。

以下部分介绍了用于填充 OMS 存储库的不同选项。

#### <a name="connected-sources-and-data-sources"></a>连接的源和数据源
连接的源是可为 OMS 存储库检索数据的位置。  数据源和解决方案在连接的源上运行，并定义收集的特定数据。  如果你的应用程序将数据写入这些数据源之一，则可通过配置数据源收集它。  例如，如果你的应用程序创建 Syslog 事件，Syslog 数据源可在 Linux 代理上收集它们。

* [Log Analytics 中的数据源](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>解决方案
解决方案扩展 OMS 的功能。  解决方案可以从连接的源收集数据，或者可以对已在存储库中收集的记录执行分析。  Microsoft 提供的每个解决方案都有提供所收集数据详细信息的单独文章。

* [Log Analytics 中的解决方案](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>HTTP 数据收集器 API
Log Analytics HTTP 数据收集器 API 是 REST API，允许你将 JSON 数据添加到 Log Analytics 存储库。  当你拥有的应用程序不通过其他数据源或解决方案之一提供数据时，可利用此 API。  它可用于从可调用 API 的任何客户端填充存储库，不依赖任何数据源或解决方案的收集计划。

* [Log Analytics HTTP 数据收集器 API](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>从 Log Analytics 存储库中检索数据
有多种方法可从 OMS 存储库中检索数据。  可能希望用户使用 OMS 控制台检索数据，并向他们提供不同类型的可视化和分析。  此外，可从外部进程（例如其他管理解决方案）中检索数据。

#### <a name="log-searches"></a>日志搜索
存储在 OMS 存储库中的所有数据均可用于日志搜索。  用户可在 OMS 控制台中执行自己的临时分析，或针对特定日志搜索创建带有可视化的仪表板。  根据预定义的搜索，解决方案可以包含带有可视化的自定义视图。  可使用日志搜索 API 从外部应用程序或管理工具访问 OMS 存储库中的数据。  

* [Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-searches.md)
* [Log Analytics 日志搜索 REST API](../log-analytics/log-analytics-log-search-api.md)
* [Log Analytics cmdlet](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>自定义视图
视图设计器允许你在 OMS 控制台中创建自定义视图，可在你的解决方案中向用户提供数据的可视化和分析。  每个视图包括显示在控制台主页上的磁贴和基于你定义的日志搜索的许多可视化部分。

* [Log Analytics 视图设计器](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Log Analytics 可将数据从 OMS 存储库自动导出到 Power BI，以便你可以利用其可视化和分析工具。  按计划执行此导出操作，以便数据保持最新。 

* [将 Log Analytics 数据导出到 Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>自动化
OMS 可自动执行进程以响应收集的数据或执行其他管理功能。  它可能从你的应用程序收集数据并将其存入 OMS 存储库中，或者你可以自动更正已知问题以响应在存储库中找到的数据。 

![自动化](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbook
Azure 自动化中的 Runbook 在 Azure 云中运行 PowerShell 脚本和工作流。  可以使用它们管理 Azure 中的资源或可从云中访问的任何其他资源。  还可以使用混合 Runbook 辅助角色在本地数据中心中运行 Runbook。  可以使用多种方法（例如 PowerShell 或自动化 API）从 Azure 门户或外部进程启动 Runbook。

* [在 Azure 自动化中启动 Runbook](../automation/automation-starting-a-runbook.md)
* [Azure 自动化 cmdlet](https://msdn.microsoft.com/library/dn690262.aspx)
* [自动化 REST API](https://msdn.microsoft.com/library/mt662285.aspx)
* [自动化 .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>警报
根据计划，警报规则自动运行日志搜索。  如果结果符合特定条件，生成的警报可以在 Azure 自动化中启动 Runbook 或调用可启动外部进程的 webhook。  这两个响应中都有警报详细信息（包括在日志搜索中返回的数据）。

* [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)
* [Log Analytics 警报 API](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>备份和站点恢复
Azure 备份和站点恢复提供用于保护企业数据并确保服务器和应用程序可用性的服务。  可利用这些服务执行向应用程序提供备份服务或启动虚拟机的故障转移等方案。

* [Azure 备份 cmdlet](https://msdn.microsoft.com/library/mt619253.aspx)
* [Azure Site Recovery REST API](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Azure Site Recovery Cmdlet](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>自定义解决方案
可将集成逻辑封装在自定义解决方案中，以在你的工作区或客户的工作区中运行。  你的解决方案中包括本文中的所有集成方法以及用于提供完整管理方案的其他资源。  打包解决方案中的资源，以便在删除解决方案后，它创建的所有资源都从 OMS 工作区和 Azure 订阅中删除。

例如，你的解决方案可以包括自动化 Runbook 以收集和处理数据，然后使用 HTTP 数据收集器 API 填充 Log Analytics 存储库。  还可以包括显示和分析所收集数据的自定义视图。  

* 创建自定义解决方案（即将推出）    

## <a name="next-steps"></a>后续步骤
* 有关自动化 OMS 服务的技术信息，请参考 [OMS SDK](operations-management-suite-sdk.md)。  


