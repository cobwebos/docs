---
title: Azure Monitor 常见问题 |Microsoft Docs
description: 有关 Azure Monitor 的常见问题的解答。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: b0ec82807857be60f30aa777ff5871334383acf7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715929"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor 常见问题

此 Microsoft FAQ 列出了有关 Azure Monitor 常见问题的列表。

## <a name="general"></a>일반

### <a name="what-is-azure-monitor"></a>Azure Monitor란?
[Azure Monitor](overview.md)是 azure 中的一项服务，它为 azure、其他云环境或本地中的应用程序和服务提供性能和可用性监视。 Azure Monitor 将多个源中的数据收集到一个公共数据平台，可以在其中分析趋势和异常。 Azure Monitor 中的丰富功能可帮助你快速识别和响应可能影响你的应用程序的关键情况。

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Monitor、Log Analytics 和 Application Insights 之间有何区别？
2018年9月，Microsoft 将 Azure Monitor、Log Analytics 和 Application Insights 组合到一个服务中，以便为应用程序及其依赖的组件提供强大的端到端监视功能。 Log Analytics 和 Application Insights 中的功能未发生更改，但某些功能已更名到 Azure Monitor，以便更好地反映其新范围。 Log Analytics 的日志数据引擎和查询语言现在称为 Azure Monitor 日志。 请参阅[Azure Monitor 术语更新](terminology.md)。

### <a name="what-does-azure-monitor-cost"></a>Azure Monitor 费用是什么？
自动启用的 Azure Monitor 功能，如指标和活动日志的收集，免费提供。 存在与其他功能（如日志查询和警报）相关的成本。 请参阅[Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)获取详细定价信息。

### <a name="how-do-i-enable-azure-monitor"></a>如何实现启用 Azure Monitor？
当你创建新的 Azure 订阅，并且自动收集[活动日志](platform/activity-logs-overview.md)和平台[指标](platform/data-platform-metrics.md)时，会启用 Azure Monitor。 创建[诊断设置](platform/diagnostic-settings.md)以收集有关 Azure 资源操作的更多详细信息，并添加[监视解决方案](insights/solutions.md)和[见解](insights/insights-overview.md)，为特定服务的收集数据提供额外分析。 

### <a name="how-do-i-access-azure-monitor"></a>如何实现访问 Azure Monitor？
从 Azure 门户中的 "**监视**" 菜单访问所有 Azure Monitor 功能和数据。 对于不同的 Azure 服务，菜单的 "**监视**" 部分提供对相同工具的访问权限，并将数据筛选到特定资源。 使用 CLI、PowerShell 和 REST API 的各种情况下，也可以访问 Azure Monitor 数据。

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>是否有 Azure Monitor 的本地版本？
아닙니다. Azure Monitor 是一种可缩放的云服务，可处理和存储大量数据，不过 Azure Monitor 可以监视本地和其他云中的资源。

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>能否 Azure Monitor 监视本地资源？
是的，除了收集 Azure 资源的监视数据以外，Azure Monitor 还可以从其他云和本地的虚拟机和应用程序中收集数据。 请参阅[Azure Monitor 的监视数据源](platform/data-sources.md)。

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor 是否与 System Center Operations Manager 集成？
可以将现有 System Center Operations Manager 管理组连接到 Azure Monitor，以便将代理中的数据收集到 Azure Monitor 日志中。 这使你可以使用日志查询和解决方案来分析从代理收集的数据。 你还可以配置现有 System Center Operations Manager 代理，以将数据直接发送到 Azure Monitor。 请参阅[将 Operations Manager 连接到 Azure Monitor](platform/om-agents.md)。

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Monitor 使用哪些 IP 地址？
请参阅[Application Insights 和 Log Analytics 使用的 ip 地址](app/ip-addresses.md)，以获取代理和其他外部资源访问 Azure Monitor 所需的 ip 地址和端口的列表。 

## <a name="monitoring-data"></a>데이터 모니터링

### <a name="where-does-azure-monitor-get-its-data"></a>Azure Monitor 从何处获取数据？
Azure Monitor 从各种来源（包括来自 Azure 平台的日志和指标，以及在虚拟机上运行的自定义应用程序和代理）收集数据。 其他服务（例如 Azure 安全中心和网络观察程序）会将数据收集到 Log Analytics 工作区中，以便能够使用 Azure Monitor 数据进行分析。 你还可以使用日志或指标的 REST API 将自定义数据发送到 Azure Monitor。 请参阅[Azure Monitor 的监视数据源](platform/data-sources.md)。

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Monitor 收集哪些数据？ 
Azure Monitor 将各种源中的数据收集到[日志](platform/data-platform-logs.md)或[指标](platform/data-platform-metrics.md)中。 每种数据类型都有其自身的相对优点，每种数据都支持 Azure Monitor 中的一组特定功能。 每个 Azure 订阅都有一个指标数据库，可以根据需要创建多个 Log Analytics 工作区来收集日志。 请参阅[Azure Monitor 数据平台](platform/data-platform.md)。

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>是否有可在 Azure Monitor 中收集的最大数据量？
可以收集的指标数据量没有限制，但该数据的存储时间最长为93天。 请参阅[指标保留](platform/data-platform-metrics.md#retention-of-metrics)。 可以收集的日志数据量没有限制，但可能受到您为 Log Analytics 工作区选择的定价层的影响。 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/)를 참조하세요.

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>如何实现访问 Azure Monitor 收集的数据？
见解和解决方案为处理存储在 Azure Monitor 中的数据提供了自定义体验。 您可以使用以 Kusto 查询语言（KQL）编写的日志查询直接处理日志数据。 在 Azure 门户中，可以编写和运行查询，并使用 Log Analytics 以交互方式分析数据。 用指标资源管理器分析 Azure 门户中的度量值。 请参阅[在 Azure Monitor 中分析日志数据](log-query/log-query-overview.md)和[Azure 指标资源管理器](platform/metrics-getting-started.md)入门。





## <a name="solutions-and-insights"></a>解决方案和见解

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Monitor 中有哪些见解？
Insights 提供针对特定 Azure 服务的自定义监视体验。 它们使用与 Azure Monitor 中的其他功能相同的指标和日志，但可能会收集更多的数据，并在 Azure 门户中提供独一无二的体验。 请参阅[Azure Monitor 中的见解](insights/insights-overview.md)。

若要查看 Azure 门户中的见解，请参阅 "**监视**" 菜单或服务菜单的 "**监视**" 部分的 "**见解**" 部分。

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Monitor 中的解决方案是什么？
监视解决方案是一组打包的逻辑，用于基于 Azure Monitor 功能监视特定的应用程序或服务。 它们 Azure Monitor 收集日志数据，并使用 Azure 门户中的常见体验为分析提供日志查询和视图。 请参阅[Azure Monitor 中的监视解决方案](insights/solutions.md)。

若要查看 Azure 门户中的解决方案，请单击 "**监视**" 菜单的 "**见解**" 部分中的 "**更多**"。 单击 "**添加**"，将其他解决方案添加到工作区。






## <a name="logs"></a>로그

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Monitor 日志和 Azure 数据资源管理器之间的区别是什么？
Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. Azure Monitor 日志建立在 Azure 数据资源管理器之上，并使用相同的 Kusto 查询语言（KQL），但有一些细微的差异。 请参阅[Azure Monitor 日志查询语言差异](log-query/data-explorer-difference.md)。

### <a name="how-do-i-retrieve-log-data"></a>如何实现检索日志数据？
所有数据都是使用使用 Kusto 查询语言（KQL）编写的日志查询从 Log Analytics 工作区中检索的。 您可以编写自己的查询，或使用包括特定应用程序或服务的日志查询的解决方案和见解。 请参阅[Azure Monitor 中的日志查询概述](log-query/log-query-overview.md)。

### <a name="what-is-a-log-analytics-workspace"></a>Log Analytics 작업 영역이란 무엇인가요?
Azure Monitor 收集的所有日志数据都存储在 Log Analytics 工作区中。 工作区实质上是从各种源收集日志数据的容器。 您可以为所有监视数据使用单个 Log Analytics 工作区，也可以有多个工作区的要求。 请参阅[设计 Azure Monitor 日志部署](platform/design-logs-deployment.md)。

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>是否可以将现有 Log Analytics 工作区移动到另一个 Azure 订阅？
你可以在资源组或订阅之间移动工作区，但不能在不同的区域之间移动。 请参阅[将 Log Analytics 工作区移动到不同的订阅或资源组](platform/move-workspace.md)。

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>为什么看不到 Log Analytics 中的查询资源管理器和保存按钮？

当[查询范围](log-query/scope.md)设置为特定资源时，"**查询资源管理器**"、"**保存**" 和 "**新建警报规则**" 按钮不可用。 若要创建警报，请保存或加载查询，Log Analytics 必须作用于工作区。 若要打开工作区上下文中 Log Analytics，请从 " **Azure Monitor** " 菜单中选择 "**日志**"。 마지막으로 사용한 작업 영역이 선택되지만 다른 작업 영역을 선택할 수 있습니다. 请参阅[Azure Monitor 中的日志查询范围和时间范围 Log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>为什么会收到错误： "注册此订阅的资源提供程序 ' Microsoft Insights ' 以便在从 VM 打开 Log Analytics 时启用此查询？ 
将自动注册许多资源提供程序，但你可能需要手动注册某些资源提供程序。 등록 범위는 항상 해당 구독입니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요.

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>从 VM 打开 Log Analytics 时，为什么我不会收到访问错误消息？ 
VM 로그를 보려면 VM 로그를 저장하는 작업 영역에 대한 읽기 권한을 부여 받아야 합니다. 이런 경우 관리자가 Azure에서 내게 권한을 부여해 줘야 합니다.




## <a name="alerts"></a>경고

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Monitor 中的警报是什么？
경고는 모니터링 데이터에서 중요한 조건이 발견되면 사전에 알려줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 有多种类型的警报：

- 指标指标值超过阈值。
- 日志查询-日志查询的结果匹配定义的条件。
- 活动日志-活动日志事件与定义的条件匹配。
- Web 测试-可用性测试匹配定义的条件的结果。


请参阅[Microsoft Azure 中的警报概述](platform/alerts-overview.md)。


### <a name="what-is-an-action-group"></a>操作组是什么？
操作组是可以由警报触发的通知和操作的集合。 多个警报可使用单个操作组，使你能够利用常见的通知和操作集。 请参阅[在 Azure 门户中创建和管理操作组](platform/action-groups.md)。


### <a name="what-is-an-action-rule"></a>什么是操作规则？
操作规则允许您修改符合特定条件的一组警报的行为。 这允许您执行在维护时段内禁用警报操作等要求。 你还可以将操作组应用于一组警报，而不是将其直接应用于警报规则。 请参阅[操作规则](platform/alerts-action-rules.md)。


## <a name="agents"></a>에이전트

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor 需要代理吗？
代理只需要从操作系统和虚拟机中的工作负荷收集数据。 虚拟机可以位于 Azure、其他云环境或本地。 请参阅[Azure Monitor 代理概述](platform/agents-overview.md)。


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Monitor 代理之间有何区别？
Azure 诊断扩展适用于 Azure 虚拟机，可将数据收集到 Azure Monitor 指标、Azure 存储和 Azure 事件中心。 Log Analytics 代理适用于 Azure 中的虚拟机、其他云环境或本地，并将数据收集到 Azure Monitor 日志。 依赖关系代理需要 Log Analytics 代理，并收集进程详细信息和依赖项。 请参阅[Azure Monitor 代理概述](platform/agents-overview.md)。


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>代理通讯是否使用 ExpressRoute 连接？
要 Azure Monitor 的流量使用 Microsoft 对等互连 ExpressRoute 线路。 有关 ExpressRoute 流量的不同类型的说明，请参阅[expressroute 文档](../expressroute/expressroute-faqs.md#supported-services)。 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>如何确认 Log Analytics 代理是否能够与 Azure Monitor 通信？
在代理计算机上的 "控制面板" 中，选择 "**安全 & 设置**"， **Microsoft Monitoring Agent** "。 在 " **Azure Log Analytics （OMS）** " 选项卡下，绿色复选标记图标确认代理能够与 Azure Monitor 通信。 黄色警告图标表示代理存在问题。 一个常见原因是**Microsoft Monitoring Agent**服务已停止。 서비스 제어 관리자를 사용하여 서비스를 다시 시작합니다.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>如何实现阻止 Log Analytics 代理与 Azure Monitor 通信？
对于连接到 Log Analytics 的代理，请打开 "控制面板"，然后选择 "**安全 & 设置**" 和 " **Microsoft Monitoring Agent**"。 在 " **Azure Log Analytics （OMS）** " 选项卡下，删除列出的所有工作区。 在 System Center Operations Manager 中，从 "Log Analytics 托管计算机" 列表中删除计算机。 Operations Manager는 Log Analytics에 더 이상 보고하지 않도록 에이전트의 구성을 업데이트합니다. 

### <a name="how-much-data-is-sent-per-agent"></a>에이전트당 얼마나 많은 데이터가 전송되나요?
에이전트당 전송되는 데이터의 양에 따라 달라집니다.

* 사용하도록 설정한 솔루션
* 수집되는 로그 및 성능 카운터 수
* 로그에 있는 데이터 볼륨

有关详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)。

WireData 에이전트를 실행할 수 있는 컴퓨터의 경우 다음 쿼리를 사용하여 전송 중인 데이터의 양을 확인합니다.

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>将数据发送到 Azure Monitor 时，Microsoft 管理代理（MMA）使用多少网络带宽？
대역폭은 전송된 데이터 양에 대한 기능입니다. 네트워크를 통해 데이터가 전송되는 동안 데이터가 압축됩니다.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>当 Log Analytics 代理中的数据收集停止时，如何收到通知？

[새 로그 경고 만들기](platform/alerts-metric.md)에서 설명한 단계를 사용하여 데이터 수집이 중지될 때 알림을 받을 수 있습니다. 将以下设置用于警报规则：

- **定义警报条件**：将 Log Analytics 工作区指定为资源目标。
- **경고 기준** 
   - **信号名称**：*自定义日志搜索*
   - **搜索查询**： `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **警报逻辑**：**基于***结果数*，**条件***大于*，**阈值** *0*
   - **计算依据**：**周期（分钟）** *30*，**频率（分钟）** *10*
- **定义警报详细信息** 
   - **名称**：*数据收集已停止*
   - **严重性**：*警告*

指定一个现有的或新的[操作组](platform/action-groups.md)，以便当日志警报符合条件时，如果检测信号丢失超过15分钟，会收到通知。


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Monitor 代理的防火墙要求是什么？
有关防火墙要求的详细信息，请参阅[网络防火墙要求](platform/log-analytics-agent.md#network-firewall-requirements)。


## <a name="visualizations"></a>시각화

### <a name="why-cant-i-cant-see-view-designer"></a>为什么看不到视图设计器？

视图设计器仅适用于在 Log Analytics 工作区中分配有 "参与者" 权限或更高权限的用户。


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>구성 문제
*다음을 설정하는 데 문제가 있습니다.*

* [.NET 앱](app/asp-net-troubleshoot-no-data.md)
* [이미 실행 중인 앱 모니터링](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 진단](platform/diagnostics-extension-to-application-insights.md)
* [Java 웹앱](app/java-troubleshoot.md)

*내 서버에서 데이터를 가져오지 않습니다.*

* [방화벽 예외 설정](app/ip-addresses.md)
* [ASP.NET 서버 설정](app/monitor-performance-live-website-now.md)
* [Java 서버 설정](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Application Insights와 같이 사용할 수 있나요...?

* [Azure VM 或 Azure 虚拟机规模集中的 IIS 服务器上的 Web 应用](app/azure-vm-vmss-apps.md)
* [IIS 서버의 웹앱 - 온-프레미스 또는 VM](app/asp-net.md)
* [Java 웹앱](app/java-get-started.md)
* [Node.js 앱](app/nodejs.md)
* [Azure의 Web Apps](app/azure-web-apps.md)
* [Azure의 Cloud Services](app/cloudservices.md)
* [Docker에서 실행되는 앱 서버](app/docker.md)
* [단일 페이지 웹앱](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows 데스크톱 앱](app/windows-desktop.md)
* [기타 플랫폼](app/platforms.md)

### <a name="is-it-free"></a>무료입니까?

예, 실험용입니다. 기본 가격 책정 계획에서, 사용자 애플리케이션은 매월 무료로 특정 데이터 할당량을 보낼 수 있습니다. 무료 할당량은 적은 수의 사용자에 대한 앱 개발 및 게시에 충분합니다. 지정된 데이터 크기보다 더 많이 처리하지 않도록 한도를 설정할 수 있습니다.

더 큰 볼륨의 원격 분석은 GB 단위로 청구됩니다. [요금 제한](app/pricing.md) 방법에 대한 몇 가지 팁을 제공합니다.

엔터프라이즈 계획에서는 각 웹 서버 노드에서 원격 분석을 전송하는 각 날짜에 대해 요금이 발생합니다. 대규모로 연속 내보내기를 사용하려는 경우에 적합합니다.

[가격 책정 계획을 참조하세요](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>비용은 얼마인가요?

* Application Insights 리소스에서 **사용량 및 예상 비용 페이지**를 엽니다. 최근 사용 현황에 대한 차트가 있습니다. 원하는 경우 데이터 볼륨 한도를 설정할 수 있습니다.
* [Azure 청구 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)를 열어 모든 리소스에 대한 청구서를 확인합니다.

### <a name="q14"></a>Application Insights에서 내 프로젝트를 어떻게 수정하나요?
세부 정보는 프로젝트의 유형에 따라 달라집니다. 웹 애플리케이션의 경우:

* 프로젝트에 다음 파일이 추가됩니다.
  * ApplicationInsights.config
  * ai.js
* 다음 NuGet 패키지가 설치됩니다.
  * *Application Insights API* - 핵심 API
  * *웹 애플리케이션용 Application Insights API* - 서버에서 원격 분석을 보내는 데 사용
  * *JavaScript 애플리케이션용 Application Insights API* - 클라이언트에서 원격 분석을 보내는 데 사용
* 패키지에는 다음 어셈블리가 포함됩니다.
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 항목 삽입 위치:
  * Web.config
  * packages.config
* （仅限新项目-如果[将 Application Insights 添加到现有项目][start]，则必须手动执行此操作。）将代码片段插入客户端和服务器代码中，以将其初始化为 Application Insights 资源 ID。 例如，在 MVC 应用程序中，代码将插入到母版页视图/Shared/\_布局中。 cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>이전 SDK 버전에서 업그레이드하려면 어떻게 해야 합니까?
사용자의 애플리케이션 유형에 적합한 SDK는 [릴리스 정보](app/release-notes.md)를 참조하세요.

### <a name="update"></a>내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?
솔루션 탐색기에서 `ApplicationInsights.config`를 마우스 오른쪽 단추로 클릭하고 **Application Insights 업데이트**를 선택합니다. Azure에서 기존 또는 새 리소스로 데이터를 보낼 수 있습니다. 업데이트 마법사는 서버 SDK에서 데이터를 전송하는 위치를 결정하는 ApplicationInsights.config의 계측 키를 변경합니다. "모두 업데이트"를 선택 취소하지 않은 한, 웹 페이지에 표시되는 키도 변경됩니다.

### <a name="what-is-status-monitor"></a>상태 모니터란?

웹앱에서 Application Insights를 구성하기 위해 IIS 웹 서버에서 사용할 수 있는 데스크톱 앱입니다. 원격 분석을 수집하지 않으며 앱을 구성하지 않는 경우 중지할 수 있습니다. 

[자세히 알아보기](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>어떤 원격 분석이 Application Insights에서 수집되나요?

서버 웹앱:

* HTTP 요청
* [종속성](app/asp-net-dependencies.md). 호출: SQL Database, 외부 서비스(Azure Cosmos DB, 테이블, Blob Storage 및 큐)에 대한 HTTP 호출. 
* [예외](app/asp-net-exceptions.md) 및 스택 추적.
* [性能计数器](app/performance-counters.md)-如果你使用[状态监视器](app/monitor-performance-live-website-now.md)、[适用于应用服务的 AZURE 监视](app/azure-web-apps.md)、适用[于 VM 或虚拟机规模集的 azure 监视](app/azure-vm-vmss-apps.md)或[Application Insights collectd 编写器](app/java-collectd.md)。
* 코딩하는 [사용자 지정 이벤트 및 메트릭](app/api-custom-events-metrics.md).
* 적절한 수집기를 구성한 경우 [추적 로그](app/asp-net-trace-logs.md).

[클라이언트 웹 페이지](app/javascript.md):

* [페이지 보기 수](app/usage-overview.md)
* 실행되는 스크립트의 [AJAX 호출](app/asp-net-dependencies.md) 요청.
* 페이지 보기 로드 데이터
* 사용자 및 세션 수
* [인증된 사용자 ID](app/api-custom-events-metrics.md#authenticated-users)

다른 원본(구성한 경우):

* [Azure 진단](platform/diagnostics-extension-to-application-insights.md)
* [Analytics로 가져오기](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>일부 원격 분석을 필터링하거나 수정할 수 있나요?

예, 서버에서 다음과 같이 할 수 있습니다.

* 원격 분석 프로세서를 작성하여 앱에서 전송되기 전에 선택한 원격 분석 항목을 필터링하거나 속성을 추가할 수 있습니다.
* 원격 분석 이니셜라이저를 작성하여 원격 분석의 모든 항목에 속성을 추가할 수 있습니다.

[ASP.NET](app/api-filtering-sampling.md) 또는 [Java](app/java-filter-telemetry.md)에 대해 자세히 알아보세요.

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>如何计算城市、国家/地区和其他地理位置数据？

[GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)를 사용하여 웹 클라이언트의 IP 주소(IPv4 또는 IPv6)를 조회합니다.

* 브라우저 원격 분석: 보낸 사람의 IP 주소를 수집합니다.
* 서버 원격 분석: Application Insights 모듈에서 클라이언트 IP 주소를 수집합니다. `X-Forwarded-For`가 설정된 경우에는 수집되지 않습니다.
* 若要详细了解如何在中收集 IP 地址和地理位置数据 Application Insights 参阅此[文](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)。


`ClientIpHeaderTelemetryInitializer`를 구성하여 다른 헤더에서 IP 주소를 가져올 수 있습니다. 예를 들어 일부 시스템에서는 프록시, 부하 분산 장치 또는 CDN에 의해 `X-Originating-IP`로 이동됩니다. [자세히 알아보기](https://apmtips.com/blog/2016/07/05/client-ip-address/).

[Power BI를 사용](app/export-power-bi.md )하여 요청 원격 분석을 지도에 표시할 수 있습니다.


### <a name="data"></a>데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?
查看[数据保留和隐私][data]。

### <a name="could-personal-data-be-sent-in-the-telemetry"></a>개인 데이터를 원격 분석에 보낼 수 있나요?

코드에서 이러한 데이터를 전송하는 경우 가능합니다. 스택 추적의 변수에 개인 데이터가 포함된 경우에도 전송될 수 있습니다. 개발 팀은 개인 데이터가 제대로 처리되도록 위험 평가를 수행해야 합니다. [데이터 보존 및 개인 정보](app/data-retention-privacy.md)에 대해 자세히 알아보세요.

클라이언트 웹 주소의 **모든** 8진수는 지리적 위치 특성을 조회한 후에 항상 0으로 설정됩니다.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>我的检测密钥在网页源中可见。 

* 모니터링 솔루션에서 일반적으로 사용됩니다.
* 데이터를 훔치는 데 사용할 수 없습니다.
* 데이터를 기울이거나 경고를 트리거하는 데 사용할 수 있습니다.
* 고객에게 이러한 문제가 발생한 경우는 없었습니다.

다음과 같이 할 수 있습니다.

* 对于客户端和服务器数据，请使用两个单独的检测密钥（单独的 Application Insights 资源）。 또는
* 서버에서 실행되는 프록시를 작성하고 웹 클라이언트에서 해당 프록시를 통해 데이터를 전송하도록 합니다.

### <a name="post"></a>진단 검색에서 POST 데이터를 어떻게 확인하나요?
POST 데이터를 자동으로 기록 하지 않지만 TrackTrace 호출을 사용할 수 있습니다. 메시지 매개 변수에 데이터를 배치합니다. 이것은 필터링할 수 없지만 문자열 속성에 대한 제한보다 긴 제한이 있습니다.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>단일 또는 여러 Application Insights 리소스를 사용해야 하나요?

단일 비즈니스 시스템의 모든 구성 요소 또는 역할에 대해 단일 리소스를 사용합니다. 개발, 테스트 및 릴리스 버전과 독립 애플리케이션에 대해 별도의 리소스를 사용합니다.

* [여기에서 토론 참조](app/separate-resources.md)
* [예 - 작업자 및 웹 역할이 포함된 클라우드 서비스](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>계측 키를 동적으로 변경하려면 어떻게 해야 하나요?

* [여기에서 토론 참조](app/separate-resources.md)
* [예 - 작업자 및 웹 역할이 포함된 클라우드 서비스](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>사용자 및 세션 수란?

* JavaScript SDK는 웹 클라이언트에서 사용자 쿠키를 설정하여 다시 방문하는 사용자와 그룹 작업에 대한 세션 쿠키를 식별합니다.
* 클라이언트 쪽 스크립트가 없으면 [서버에서 쿠키를 설정](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)할 수 있습니다.
* 한 명의 실제 사용자가 특정 사이트를 다른 브라우저에서 사용하거나, in-private/incognito 검색을 통해 사용하거나, 다른 컴퓨터에서 사용하는 경우 두 번 이상 계산됩니다.
* 여러 컴퓨터 및 브라우저에서 로그인한 사용자를 식별하려면 [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users)에 대한 호출을 추가합니다.

### <a name="q17"></a> Application Insights의 모든 기능을 사용하도록 어떻게 설정하나요?
| 표시 내용 | 시작 방법 | 원하는 이유 |
| --- | --- | --- |
| 가용성 차트 |[웹 테스트](app/monitor-web-app-availability.md) |웹 앱이 작동 중인지 확인 |
| 서버 앱 성능: 응답시간. ... |[프로젝트에 Application Insights 추가](app/asp-net.md) 또는 [서버에 AI 상태 모니터 설치](app/monitor-performance-live-website-now.md)(또는 [종속성 추적](app/api-custom-events-metrics.md#trackdependency)을 위한 자체 코드 작성) |성능 문제 검색 |
| 종속성 원격 분석 |[서버에 AI 상태 모니터 설치](app/monitor-performance-live-website-now.md) |데이터베이스 또는 다른 외부 구성 요소의 문제 진단 |
| 예외에서 스택 추적 가져오기 |[TrackException 호출을 코드에 삽입](app/asp-net-exceptions.md)(하지만 일부는 자동으로 보고됨) |예외 감지 및 진단 |
| 로그 추적 검색 |[로깅 어댑터 추가](app/asp-net-trace-logs.md) |예외, 성능 문제 진단 |
| 클라이언트 사용 기본 사항: 페이지 보기, 세션,... |[웹 페이지의 JavaScript 이니셜라이저](app/javascript.md) |사용량 현황 분석 |
| 클라이언트 사용자 지정 메트릭 |[웹 페이지에서 추적 호출](app/api-custom-events-metrics.md) |사용자 환경 향상 |
| 서버 사용자 지정 메트릭 |[서버에서 호출 추적](app/api-custom-events-metrics.md) |비즈니스 인텔리전스 |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>검색 및 메트릭 차트의 수가 다른 이유는 무엇인가요?

[샘플링](app/sampling.md)을 하면 실제로 앱에서 포털로 전송되는 원격 분석 항목(요청, 사용자 지정 이벤트 등)의 수가 줄어듭니다. 검색에는 실제로 받은 항목 수가 표시됩니다. 이벤트 수를 표시하는 메트릭 차트에는 발생된 원래 이벤트 수가 표시됩니다. 

전송되는 각 항목은 해당 항목이 나타내는 원래 이벤트의 수를 보여 주는 `itemCount` 속성을 전달합니다. 작업 중인 샘플링을 관찰하려면 분석에서 다음 쿼리를 실행할 수 있습니다.

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Application Insights 구성

Azure Resource Monitor를 통해 [PowerShell 스크립트를 작성](app/powershell.md)하여 다음을 수행할 수 있습니다.

* Application Insights 리소스를 만들고 업데이트합니다.
* 가격 책정 계획을 설정합니다.
* 계측 키를 가져옵니다.
* 메트릭 경고를 추가합니다.
* 가용성 테스트를 추가합니다.

메트릭 탐색기 보고서를 설정하거나 연속 내보내기를 설정할 수는 없습니다.

#### <a name="querying-the-telemetry"></a>원격 분석 쿼리

[REST API](https://dev.applicationinsights.io/)를 사용하여 [분석](app/analytics.md) 쿼리를 실행합니다.

### <a name="how-can-i-set-an-alert-on-an-event"></a>이벤트에 대한 경고를 설정하려면 어떻게 해야 하나요?

Azure 경고는 메트릭에 대해서만 설정됩니다. 이벤트가 발생할 때마다 값 임계값을 초과하는 사용자 지정 메트릭을 만듭니다. 그런 다음 메트릭에 대해 경고를 설정합니다. 当指标在任一方向跨越阈值时，你将收到通知;直到第一次交叉时才会收到通知，而无论初始值是高还是低;始终存在几分钟的延迟。

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure 웹앱과 Application Insights 간 데이터 전송 요금이 있나요?

* Azure 웹앱이 Application Insights 컬렉션 엔드포인트가 있는 데이터 센터에서 호스트되는 경우 무료입니다. 
* 호스트 데이터 센터에 컬렉션 엔드포인트가 없으면 앱의 원격 분석에 [Azure 발신 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 부과됩니다.

이 요금은 Application insights 리소스가 호스트되는 위치에 따라 달라지지 않습니다. 엔드포인트의 배포에 따라 달라집니다.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Application Insights 포털에 원격 분석을 보낼 수 있나요?

저희가 제공하는 SDK 및 [SDK API](app/api-custom-events-metrics.md)를 사용하는 것이 좋습니다. 다양한 [플랫폼](app/platforms.md)에 따라 여러 SDK 변형이 있습니다. 이러한 SDK는 버퍼링, 압축, 제한, 다시 시도 등을 처리합니다. 그러나 [수집 스키마](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) 및 [엔드포인트 프로토콜](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)은 공용입니다.

### <a name="can-i-monitor-an-intranet-web-server"></a>인트라넷 웹 서버를 모니터링할 수 있나요?

예, 하지만 방화벽 예외 또는 프록시 리디렉션을 통해 Microsoft 서비스로 전송되는 트래픽을 허용해야 합니다.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


서비스와 IP 주소의 전체 목록은 [여기](app/ip-addresses.md)서 검토하세요.

#### <a name="firewall-exception"></a>방화벽 예외

웹 서버가 엔드포인트에 원격 분석을 전송할 수 있습니다. 

#### <a name="gateway-redirect"></a>网关重定向

구성의 엔드포인트를 덮어쓰는 방식으로 서버에서 인트라넷의 게이트웨이로 트래픽을 라우팅합니다. 구성에 이러한 "엔드포인트" 설정이 없으면 해당 클래스는 아래의 예제 ApplicationInsights.config에 나와 있는 기본값을 사용합니다. 

게이트웨이는 엔드포인트의 기준 주소로 트래픽을 라우팅해야 합니다. 구성에서 기본값을 `http://<your.gateway.address>/<relative path>`로 바꿉니다.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>기본 엔드포인트가 포함된 ApplicationInsights.config 예제:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider 可从 v 2.6.0 开始使用。



#### <a name="proxy-passthrough"></a>代理传递

可以通过配置计算机级别或应用程序级别代理来实现代理通过。
有关详细信息，请参阅[DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)上的 dotnet 文章。
 
 Web.config 示例：
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>인트라넷 서버에서 가용성 웹 테스트를 실행할 수 있나요?

[웹 테스트](app/monitor-web-app-availability.md)는 전 세계에 배포된 클라이언트 로그인 지점에서 실행됩니다. 두 가지 해결 방법이 있습니다.

* 방화벽 문 - [길고 변경 가능한 웹 테스트 에이전트 목록](app/ip-addresses.md)에서 서버에 대한 요청을 허용합니다.
* 인트라넷 내부에서 서버에 주기적으로 요청을 보내는 코드를 직접 작성합니다. 이러한 목적으로 Visual Studio 웹 테스트를 실행할 수 있습니다. 테스터는 TrackAvailability() API를 사용하여 결과를 Application Insights에 보낼 수 있습니다.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>원격 분석을 수집하려면 시간이 얼마나 걸리나요?

대부분의 Application Insights 데이터에는 5분 이하의 대기 시간이 포함됩니다. 일부 데이터(일반적으로 대규모 로그 파일)의 경우 시간이 오래 걸릴 수 있습니다. 자세한 내용은 [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)를 참조하세요.



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

Microsoft FAQ는 컨테이너용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>我看不到在查询 ContainerLog 表时填充的图像和名称属性值。

对于代理版本 ciprod12042019 和更高版本，默认情况下，不会为每个日志行填充这两个属性，以最大程度地减少收集日志数据时产生的成本。 有两个选项可用于查询表，其中包含这些属性及其值：

#### <a name="option-1"></a>옵션 1 

联接其他表以在结果中包含这些属性值。

通过联接到 ContainerID 属性，将查询修改为包含 ```ContainerInventory``` 表中的 Image 和 ImageTag 属性。 通过在 ContainerID 属性上联接，可以在 KubepodInventory 表的 ContaineName 字段中包含 Name 属性（与以前在 ```ContainerLog``` 表中显示的名称相同）。建议使用此选项。

下面的示例是一个示例详细查询，说明如何使用联接获取这些字段的值。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>옵션 2

为每个容器日志行的这些属性重新启用收集。

如果第一个选项因涉及到查询更改而不是很方便，则可以通过启用 "[数据收集配置设置](insights/container-insights-agent-config.md)" 中所述的 "代理配置" 映射中的设置 ```log_collection_settings.enrich_container_logs``` 来重新启用收集这些字段。

> [!NOTE]
> 对于包含50个以上节点的大型群集，不建议使用第二个选项，因为它将从群集中的每个节点生成 API 服务器调用以执行此扩充。 此选项还会增加收集的每个日志行的数据大小。

### <a name="can-i-view-metrics-collected-in-grafana"></a>能否查看在 Grafana 中收集的指标？

容器 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的度量值。 我们提供了一个模板，你可以从 Grafana 的 "[仪表板" 存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下载该模板，以帮助你了解如何从受监视的群集查询其他数据以在自定义 Grafana 仪表板中进行可视化。 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>是否可以监视包含 Azure Monitor 容器的 AKS-engine 群集？

容器 Azure Monitor 支持监视部署到 Azure 上托管的 AKS （以前称为 ACS 引擎）群集的容器工作负荷。 有关为此方案启用监视所需步骤的详细信息和概述，请参阅将[Azure Monitor 用于 AKS 的容器](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为什么我在 Log Analytics 工作区中看不到数据？

如果在特定时间每天都无法查看 Log Analytics 工作区中的任何数据，则可能已达到默认的 500 MB 限制或指定的每日上限，以控制每日收集的数据量。 当当天的限制达到限制时，数据收集将停止并仅在下一天恢复。 若要查看数据使用情况并根据预期的使用模式更新到不同的定价层，请参阅[日志数据使用情况和成本](platform/manage-cost-storage.md)。 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定了哪些容器状态？

ContainerInventory 테이블에는 중지된 컨테이너와 실행 중인 컨테이너 둘 다에 대한 정보가 포함되어 있습니다. 이 테이블은 Docker에서 모든 컨테이너(실행 중 및 중지)를 쿼리하고 해당 데이터를 Log Analytics 작업 영역에 전달하는 에이전트 내 워크플로로 채워집니다.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何实现解决*缺少订阅注册*错误？

如果收到错误 "为 microsoft.operationsmanagement" 中**缺少订阅注册**，可以通过在其中定义了工作区的订阅中注册资源提供程序**为 microsoft.operationsmanagement**来解决此错误。 이 작업을 수행하는 방법에 대한 설명서는 [여기](../azure-resource-manager/templates/error-register-resource-provider.md)에서 찾을 수 있습니다.

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>支持 RBAC 的 AKS 群集是否支持？

容器监视解决方案不支持 RBAC，但对于容器 Azure Monitor 支持。 솔루션 세부 정보 페이지에 이러한 클러스터에 대한 데이터를 표시하는 블레이드에 올바른 정보가 표시되지 않을 수 있습니다.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm을 통해 kube-system 네임스페이스의 컨테이너에 대해 로그 수집을 사용하도록 설정하려면 어떻게 하나요?

kube-system 네임스페이스에 포함된 컨테이너의 로그 수집은 기본적으로 사용하지 않도록 설정됩니다. omsagent에 환경 변수를 설정하여 로그 수집을 사용하도록 설정할 수 있습니다. 有关详细信息，请参阅[容器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)GitHub 的 Azure Monitor 页。 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>omsagent를 최신 릴리스 버전으로 업데이트하려면 어떻게 하나요?

에이전트를 업그레이드하는 방법을 알아보려면 [에이전트 관리](insights/container-insights-manage-agent.md)를 참조하세요.

### <a name="how-do-i-enable-multi-line-logging"></a>여러 줄 로깅을 사용하도록 설정하려면 어떻게 하나요?

현재 컨테이너용 Azure Monitor는 여러 줄 로깅을 지원하지 않지만 해결 방법이 있습니다. JSON 형식으로 쓰도록 모든 서비스를 구성하면 Docker/Moby가 해당 데이터를 한 줄에 씁니다.

예를 들어, 샘플 node.js 애플리케이션에 대한 아래 예제와 같이 로그를 JSON 개체로 래핑할 수 있습니다.

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

查询日志时，此数据将如以下示例 Azure Monitor 中所示：

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有关此问题的详细信息，请查看以下[GitHub 链接](https://github.com/moby/moby/issues/22920)。

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>启用实时日志时如何实现解决 Azure AD 错误？ 

你可能会看到以下错误：**请求中指定的回复 url 与为应用程序配置的回复 url "< 应用程序 ID\>" 不匹配**。 解决方法的解决方案可以在[如何与 Azure Monitor 容器的实时查看容器数据](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)一文中找到。 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>为什么在加入后无法升级群集？

如果为 AKS 群集启用容器 Azure Monitor，将删除群集发送其数据的 Log Analytics 工作区，尝试升级群集时，它将失败。 若要解决此情况，你必须禁用监视，然后重新启用它以引用订阅中的不同有效工作区。 当你尝试再次执行群集升级时，它应进行处理并成功完成。  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>需要为代理打开/允许列表的端口和域？

请参阅 Azure、Azure 美国政府和 Azure 中国世纪互联代理所需的代理和防火墙配置信息的[网络防火墙要求](insights/container-insights-onboard.md#network-firewall-requirements)。

## <a name="azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor （预览）
Microsoft FAQ는 VM용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

### <a name="can-i-onboard-to-an-existing-workspace"></a>기존 작업 영역에 온보딩할 수 있나요?
가상 머신이 Log Analytics 작업 영역에 이미 연결된 경우 VM용 Azure Monitor에 온보딩되면 해당 작업 영역을 계속 사용하도록 [여기](insights/vminsights-enable-overview.md#prerequisites)에서 나열된 지원되는 지역 중 하나에서 제공될 수 있습니다.

온보딩하면 VM용 Azure Monitor에서 표시하고 분석할 이 정보의 수집을 시작하기 위해 모든 VM이 작업 영역에 데이터를 보고하게 되는 작업 영역에 대한 성능 카운터를 구성합니다.  결과적으로, 선택된 작업 영역에 연결된 VM의 모든 성능 데이터가 표시됩니다.  상태 및 맵 기능은 온보딩하도록 지정된 VM에 대해서만 활성화됩니다.

有关启用了哪些性能计数器的详细信息，请参阅我们的[启用概述](insights/vminsights-enable-overview.md#performance-counters-enabled)一文。

### <a name="can-i-onboard-to-a-new-workspace"></a>새 작업 영역에 온보딩할 수 있나요? 
현재 VM이 기존 Log Analytics 작업 영역에 연결되지 않은 경우 데이터를 저장할 새 작업 영역을 만들어야 합니다. Azure Portal을 통해 VM용 Azure Monitor에 대해 단일 Azure VM을 구성한 경우 새 기본 작업 영역을 만드는 작업이 자동으로 수행됩니다.

如果选择使用基于脚本的方法，则[使用 Azure PowerShell 或资源管理器模板文章启用用于 VM 的 Azure Monitor （预览版）](insights/vminsights-enable-at-scale-powershell.md)中介绍了这些步骤。 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>내 VM에서 이미 기존 작업 영역에 보고하는 경우 어떻게 해야 하나요?
이미 가상 머신의 데이터를 수집하는 경우 기존 Log Analytics 작업 영역에 데이터를 보고하도록 이미 구성되었을 수 있습니다.  해당 작업 영역이 지원되는 지역 중 하나라면 기존 작업 영역에 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다.  이미 사용 중인 작업 영역이 지원되는 지역 중 하나가 아닌 경우 이번에는 VM용 Azure Monitor에 온보딩할 수 없습니다.  본사에서는 추가 지역을 지원하기 위해 적극적으로 노력하고 있습니다.

>[!NOTE]
>VM용 Azure Monitor에 온보딩하도록 선택했는지 여부와 상관 없이 작업 영역에 보고는 모든 VM에 영향을 주는 작업 영역에 대한 성능 카운터를 구성합니다. 작업 영역에 대해 성능 카운터를 구성하는 방법에 대한 자세한 내용은 [설명서](platform/data-sources-performance-counters.md)를 참조하세요. 有关为用于 VM 的 Azure Monitor 配置的计数器的信息，请参阅我们的[启用用于 VM 的 Azure Monitor](insights/vminsights-enable-overview.md#performance-counters-enabled)一文。  

### <a name="why-did-my-vm-fail-to-onboard"></a>내 VM이 온보딩하지 못한 이유는?
Azure Portal에서 Azure VM을 온보딩한 경우 다음 단계가 수행됩니다.

* 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
* 선택한 작업 영역에 대한 성능 카운터가 구성됩니다. 이 단계가 실패하면 일부 성능 차트 및 표에서는 온보딩한 VM에 대한 데이터를 표시하지 않습니다. [여기](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters)에 문서화된 PowerShell 스크립트를 실행하여 이 문제를 해결할 수 있습니다.
* 필요한 경우 Log Analytics 에이전트는 VM 확장을 사용하여 설치됩니다.  
* 필요한 경우 VM용 Azure Monitor 맵 종속성 에이전트는 VM 확장을 사용하여 Azure VM에 설치됩니다.  
* 필요한 경우 Azure Monitor 구성 요소가 상태 기능을 지원하도록 구성하고, VM이 상태 데이터를 보고하도록 구성합니다.

온보딩 프로세스 중에 포털에서 알림 상태를 반환하는 위의 각 상태에 대해 확인합니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. 포털에서 모니터링 및 상태 데이터를 보려면 5~10분이 더 걸립니다.  

온보딩을 시작하고 VM이 온보딩되어야 함을 나타내는 메시지가 표시된 경우 VM이 프로세스를 완료하려면 최대 30분이 허용됩니다. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>VM용 Azure Monitor만 사용하도록 설정했는데 상태 기능에서 모니터링되는 모든 VM이 표시되는 이유는 무엇인가요?
상태 기능은 단일 VM에서 작업이 시작된 경우에도 Log Analytics 작업 영역에 연결된 모든 VM에 사용하도록 설정됩니다.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>상태 조건이 상황을 평가하게 되는 일정을 수정할 수 있나요?
아니요. 이 릴리스에서는 상태 조건의 기간 및 빈도를 수정할 수 없습니다. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>모니터링할 필요가 없는 상황에서 상태 조건을 사용하지 않도록 설정할 수 있나요?
이 릴리스에서는 상태 조건을 사용하지 않도록 설정할 수 없습니다.

### <a name="are-the-health-alert-severities-configurable"></a>상태 경고 심각도를 구성할 수 있나요?  
상태 경고 심각도는 수정할 수 없으며, 사용하거나 사용하지 않도록 설정할 수만 있습니다. 또한 일부 경고 심각도는 상태 조건의 상태에 따라 업데이트됩니다. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>특정 상태 조건의 설정을 다시 구성하는 경우 특정 인스턴스로 범위를 지정할 수 있나요?  
상태 조건 인스턴스의 설정을 수정하면 Azure VM에서 동일한 유형의 모든 상태 조건 인스턴스가 수정됩니다. 예를 들어 C: 논리 디스크에 해당하는 사용 가능한 디스크 공간 상태 조건 인스턴스의 임계값을 수정할 경우 이 임계값은 동일한 VM에 대해 검색 및 모니터링되는 다른 모든 논리 디스크에 적용됩니다.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>상태 기능이 논리 프로세서 및 코어를 모니터링하나요?
아니요. 개별 프로세서 및 논리적 프로세서 수준 상태 조건은 Windows에 포함되지 않으며, Azure VM에 사용할 수 있는 논리 CPU의 총 수에 따라 CPU 압력을 효과적으로 평가하기 위해 기본적으로 총 CPU 사용률만 모니터링됩니다. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>모든 상태 조건 임계값을 구성할 수 있나요?  
상태가 *실행 중* 또는 *사용 가능*으로 설정되어 있는 Windows VM을 대상으로 하는 상태 조건의 임계값은 수정할 수 없습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서 상태를 쿼리할 때 다음과 같은 경우 서비스 또는 엔터티에 대해 *comparisonOperator* 값 **LessThan** 또는 **GreaterThan**과 *threshold* 값 **4**가 표시됩니다.
   - DNS 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - DHCP 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - RPC 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 방화벽 서비스 상태 – 서비스가 실행 중이 아닙니다.
   - Windows 이벤트 로그 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 서버 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 원격 관리 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 파일 시스템 오류 또는 손상 - 논리 디스크를 사용할 수 없습니다.

상태가 이미 *true*로 설정되어 있는 다음 Linux 상태 조건의 임계값은 수정할 수 없습니다. 워크로드 모니터링 API에서 엔터티에 대해 쿼리할 때 컨텍스트에 따라 상태에 *comparisonOperator* 값 **LessThan** 및 *threshold* 값 **1**이 표시됩니다.
   - 논리 디스크 상태 - 논리 디스크가 온라인/사용 가능 상태가 아님
   - 디스크 상태 - 디스크가 온라인/사용 가능 상태가 아님
   - 네트워크 어댑터 상태 - 네트워크 어댑터가 사용 안 함임

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>상태 기능에 포함된 경고를 수정하려면 어떻게 해야 하나요?
각 상태 조건에 대해 정의된 경고 규칙은 Azure Portal에 표시되지 않습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서만 상태 경고 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다. 또한 Azure Portal에서는 상태 경고에 대해 [Azure Monitor 작업 그룹](platform/action-groups.md)을 할당할 수 없습니다. 오직 알림 설정 API를 사용하여 상태 경고가 발생할 때마다 트리거되도록 작업 그룹을 구성할 수 있습니다. 현재, VM에 대해 발생한 모든 *상태 경고*가 동일한 작업 그룹을 트리거하도록 VM에 대해 작업 그룹을 할당할 수 있습니다. 기존의 Azure 경고와 달리, 각 상태 경고 규칙에 대한 별도의 작업 그룹이라는 개념이 없습니다. 또한 상태 경고가 트리거될 때 메일 또는 SMS 알림을 제공하도록 구성된 작업 그룹만 지원됩니다. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>내 VM의 성능 차트에 일부 또는 모든 데이터가 표시되지 않습니다
디스크 테이블 또는 일부 성능 차트에 성능 데이터가 표시되지 않는 경우 작업 영역에서 성능 카운터를 구성할 수 없습니다. 이 문제를 해결하려면 다음 [PowerShell 스크립트](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)를 실행합니다.

### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM용 Azure Monitor 맵 기능과 서비스 맵이 어떻게 다른가요?
VM용 Azure Monitor 맵 기능은 서비스 맵을 기반으로 하지만 다음과 같은 차이점이 있습니다.

* 맵 보기는 VM 블레이드 및 Azure Monitor의 VM용 Azure Monitor에서 액세스할 수 있습니다.
* 이제 맵에서 연결은 클릭 가능하고 선택한 연결에 대한 사이드 패널에서 연결 메트릭 데이터 보기를 표시합니다.
* 더 복잡한 맵을 지원하기 위해 맵을 만드는 데 사용되는 새 API가 있습니다.
* 이제 모니터링된 VM은 클라이언트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 가상 머신 및 모니터링되지 않는 가상 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 이제 모니터링된 가상 머신은 서버 포트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 머신 및 모니터링되지 않는 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 맵 스타일은 Application insights에서 앱 맵을 사용하여 더욱 일관되게 업데이트되었습니다.
* 已更新侧面板，但没有服务映射-更新管理、更改跟踪、安全和服务台支持的一组完整集成。 
* 맵에 대한 그룹 및 머신을 선택하는 옵션이 업데이트되어 이제 구독, 리소스 그룹, Azure 가상 머신 확장 집합 및 클라우드 서비스를 지원합니다.
* VM용 Azure Monitor 맵 기능에서 새 서비스 맵 머신 그룹을 만들 수 없습니다.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>내 성능 차트가 점선을 표시하는 이유는?
다음과 같은 이유로 발생할 수 있습니다.  데이터 수집에 간격이 있는 경우에 줄이 점선으로 그려집니다.  사용하도록 설정된 성능 카운터에 대한 데이터 샘플링 빈도를 수정한 경우(기본 설정은 60초마다 데이터를 수집하는 것임) 차트에 대해 좁은 시간 범위를 선택하고 샘플링 빈도가 차트에 사용된 버킷 크기 미만이면(예: 샘플링 빈도는 10분 간격이며 차트의 각 버킷은 5분 간격임) 차트에서 점선을 볼 수 있습니다.  광범위한 시간 범위를 보도록 선택하면 이 경우에 차트 선이 점선이 아닌 실선으로 표시됩니다.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 그룹이 지원되나요?
예, 종속성 에이전트가 설치되면 VM에서 정보를 수집하여 구독, 리소스 그룹, 가상 머신 확장 집합 및 클라우드 서비스에 기반한 그룹을 표시합니다.  서비스 맵을 사용하고 있고 시스템 그룹을 만든 경우에도 이러한 그룹이 표시됩니다.  또한 보고 있는 작업 영역에 대한 컴퓨터 그룹을 만든 경우에도 그룹 필터에 표시됩니다. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>집계 성능 차트에서 95번째 백분위수 줄에 영향을 주는 항목에 대한 세부 정보를 표시하려면 어떻게 할까요?
기본적으로 목록은 5번째 백분위수의 최솟값을 포함한 머신을 보여주는 사용 가능한 메모리 차트를 제외하고 선택된 메트릭에 대해 95번째 백분위수에 대한 최댓값이 있는 VM을 표시하도록 정렬됩니다.  차트를 클릭하면 적절한 메트릭이 선택된 **상위 N개 목록** 보기가 열립니다.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>맵 기능이 다른 VNet 및 서브넷에서 중복 IP를 어떻게 처리하나요?
서브넷 및 VNet에서 VM 또는 Azure 가상 머신 확장 집합을 사용하여 IP 범위를 복제하는 경우 VM용 Azure Monitor에서 잘못된 정보를 표시할 수 있습니다. 알려진 문제이며 본사에서는 이 환경을 개선하는 옵션을 개발하는 중입니다.

### <a name="does-map-feature-support-ipv6"></a>맵 기능이 IPv6을 지원하나요?
맵 기능은 현재 IPv4만 지원하며 본사에서는 IPv6에 대한 지원을 개발하는 중입니다. IPv6 내에서 터널링된 IPv4도 지원합니다.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>리소스 그룹 또는 다른 대규모 그룹에 대한 맵을 로드하는 경우 맵을 보기 어렵습니다.
크고 복잡한 구성을 처리하도록 맵을 개선하는 동안 맵에서는 많은 노드, 연결 및 노드가 클러스터로 작동할 수 있습니다.  계속 확장성을 증가시키도록 지원을 강화하기 위해 최선을 다하고 있습니다.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>성능 탭의 네트워크 차트가 Azure VM 개요 페이지의 네트워크 차트와 다르게 보이는 이유는?

Azure VM의 개요 페이지에는 게스트 VM에서 작업의 호스트 측정값에 따라 차트가 표시됩니다.  Azure VM 개요에 대한 네트워크 차트의 경우 청구되는 네트워크 트래픽만 표시합니다.  这不包括虚拟网络之间的流量。  为用于 VM 的 Azure Monitor 显示的数据和图表基于来宾 VM 中的数据，而网络图表显示所有入站和出站的 TCP/IP 流量（包括虚拟网络之间的流量）。

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>对于存储在 VMConnection 中并显示在连接面板和工作簿中的数据，响应时间是如何度量的？

响应时间为近似值。 由于我们不会检测应用程序的代码，因此，我们不会真正知道请求开始的时间和响应到达的时间。 相反，我们观察到正在连接上发送的数据，然后再返回该连接的数据。 代理跟踪这些发送和接收，并尝试对它们进行配对：发送顺序，后跟一系列接收，被解释为请求/响应对。 这些操作之间的时间间隔是响应时间。 它将包括网络延迟和服务器处理时间。

此近似适用于基于请求/响应的协议：在连接上发出单个请求，并收到单个响应。 这种情况适用于 HTTP （S）（不带管道），但对其他协议不满意。

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics 무료 가격 플랜을 사용하고 있는 경우 제한 사항이 있나요?
*무료* 가격 책정 계층을 사용하여 Log Analytics 작업 영역에 Azure Monitor를 구성한 경우 VM용 Azure Monitor 맵 기능은 5대의 작업 영역에 연결된 머신만 지원합니다. 무료 작업 영역에 5대의 VM이 연결되어 있는 경우 VM 중 하나의 연결을 끊은 후 나중에 새 VM을 연결하면 맵 페이지에서 새 VM이 모니터링 및 반영되지 않습니다.  

따라서 **Insights(미리 보기)** 가 VM에 이미 설치된 후라도 해당 VM을 열고 왼쪽 창에서 Insights(미리 보기)를 선택하면 **지금 사용해 보기** 옵션이 포함된 메시지가 표시됩니다.  그러나 이 VM이 VM용 Azure Monitor에 등록되지 않은 경우에는 일반적인 경우처럼 옵션이 포함된 메시지가 표시되지 않습니다. 


## <a name="next-steps"></a>다음 단계
如果此处未解答你的问题，你可以参考以下论坛来了解其他问题和答案。

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

有关 Azure Monitor 的一般反馈，请访问[反馈论坛](https://feedback.azure.com/forums/34192--general-feedback)。
