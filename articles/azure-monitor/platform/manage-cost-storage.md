---
title: 管理 Azure Monitor 日志的使用情况和成本 |Microsoft Docs
description: 了解如何在 Azure Monitor 中更改定价计划和管理 Log Analytics 工作区的数据量和保留策略。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 8c4169ccfb35b74b92ea4996cbc779bac35d6ccb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715865"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure Monitor 日志管理使用情况和成本

> [!NOTE]
> 本文介绍如何了解和控制 Azure Monitor 日志的成本。 相关文章[监视使用情况和估计成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)介绍了如何在不同定价模型的多个 Azure 监视功能中查看使用情况和预估成本。

> [!NOTE]
> 本文中所示的所有价格和成本仅用于举例。 

Azure Monitor 日志旨在根据企业中的任何源或在 Azure 中部署的资源，缩放和支持每天收集、索引和存储大量数据。  조직에 대한 주 드라이버인 반면 비용 효율성은 궁극적으로 기본 드라이버입니다. 为此，必须了解 Log Analytics 工作区的成本不仅基于收集的数据量，它还依赖于所选的计划，以及你选择用于存储从连接的源生成的数据的时间。  

在本文中，我们将回顾如何主动监视引入数据量和存储增长，并定义限制来控制这些关联的成本。 

## <a name="pricing-model"></a>가격 책정 모델

Log Analytics 的默认定价是基于数据量引入的即**用即付**模型，还可以选择用于更长的数据保留。 数据量作为要存储的数据的大小来度量。 每个 Log Analytics 工作区都收费为单独的服务，并对 Azure 订阅计费。 数据引入量可能会很大，具体取决于以下因素： 

  - 已启用的管理解决方案的数量及其配置（例如 
  - 监视的 Vm 数
  - 从每个受监视的 VM 收集的数据的类型 
  
除了即用即付模型外，Log Analytics 还提供了**容量预留**层，使你可以将其保存为与即用即付价格相比最多25%。 产能预留价格使你可以购买起价 100 GB/天的保留。 将按现用现付费率对超出预订级别的任何使用量进行计费。 容量预留层具有31天承诺期。 在承诺期内，您可以更改为更高级的容量预留层（这将重新启动31天承诺期间），但您不能移回即用即付或更低的容量预留层，直到完. 
[详细了解](https://azure.microsoft.com/pricing/details/monitor/)Log Analytics 即用即付和产能预留定价。 

在所有定价层中，数据量是在准备存储数据时从数据的字符串表示形式计算得出的。 事件大小的计算中不包括[所有数据类型共有](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties)的几个属性，包括 `_ResourceId`、`_ItemId`、`_IsBillable` 和 `_BilledSize`。

另请注意，某些解决方案（例如[Azure 安全中心](https://azure.microsoft.com/pricing/details/security-center/)和[azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)）有自己的定价模型。 

## <a name="estimating-the-costs-to-manage-your-environment"></a>估计管理环境的成本 

如果尚未使用 Azure Monitor 日志，可以使用[Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用 Log Analytics 的成本。 首先在搜索框中输入 "Azure Monitor"，然后单击生成的 Azure Monitor 磁贴。 向下滚动到 "Azure Monitor"，然后从 "类型" 下拉列表中选择 "Log Analytics"。  可在此处输入要从每个 VM 收集的 Vm 数和 GB 数据。 通常，从典型的 Azure VM 引入的数据月为1到 3 GB。 如果已在评估 Azure Monitor 的日志，则可以使用自己的环境中的数据统计信息。 请参阅下面有关如何确定[受监视 vm 的数目](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[工作区引入的数据量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。 

## <a name="understand-your-usage-and-estimate-costs"></a>了解你的使用情况和估计成本

如果你现在使用 Azure Monitor 日志，则可以很容易地了解基于最近使用模式的可能成本。 为此，请使用**Log Analytics 使用情况和估计成本**来查看和分析数据使用情况。 这会显示每个解决方案收集的数据量、正在保留的数据量，以及基于引入的数据量和超出包含数量的任何附加保留期的成本估算。

![사용량 및 예상 비용](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

데이터를 더 자세히 탐색하려면 **사용량 및 예상 비용** 페이지의 차트 중 하나의 오른쪽 상단 모서리에 있는 아이콘을 클릭합니다. 이제 이 쿼리에 대해 작업하여 사용량을 자세히 살펴볼 수 있습니다.  

![로그 보기](media/manage-cost-storage/logs.png)

**사용량 및 예상 비용** 페이지에서 해당 월의 데이터 볼륨을 검토할 수 있습니다. 여기에는 Log Analytics 작업 영역에 수신되고 보존된 모든 데이터가 포함됩니다.  单击页面顶部的 "**使用情况详细信息**"，以查看 "使用情况" 仪表板，其中包含有关源、计算机和服务的数据量趋势的信息。 보존 기간을 수정하거나 일일 한도를 설정하고 확인하려면 **데이터 볼륨 관리**를 클릭합니다.
 
Log Analytics 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>查看 Azure 帐单上的 Log Analytics 使用情况 

Azure 在[Azure 成本管理 + 计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供了大量有用的功能。 例如，通过 "成本分析" 功能，可以查看 Azure 资源的花费。 按资源类型添加筛选器（到 Log Analytics 的 microsoft.operationalinsights/工作区），可以跟踪你的支出。

通过[从 Azure 门户下载你的使用](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)情况，可以更好地了解你的使用情况。 在下载的电子表格中，你可以查看每天每个 Azure 资源的使用情况（例如 Log Analytics 工作区）。 在此 Excel 电子表格中，可以通过 "计量类别" 列中的第一次筛选来找到 Log Analytics 工作区的使用情况，以显示 "Insights 和分析" （由某些旧的定价层使用）和 "Log Analytics"，然后在 "实例" 上添加筛选器ID "列是" 包含工作区 "。 使用情况显示在 "已消耗数量" 列中，每个条目的单位显示在 "度量单位" 列中。  更多详细信息可帮助你[了解 Microsoft Azure 帐单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

## <a name="changing-pricing-tier"></a>가격 책정 계층 변경

若要更改工作区的 Log Analytics 定价层，请 

1. 在 Azure 门户中，打开工作区中的 "**使用情况和估计成本**"，其中会显示此工作区可用的每个定价层的列表。

2. 查看每个定价层的预估成本。 这一估算基于最后31天的使用情况，因此，这种成本估算依赖于过去的31天，代表您的典型使用量。 在下面的示例中，你可以看到，基于过去31天的数据模式，此工作区将在即用即付层（#1）中的成本低于 100 GB/天容量预留层（#2）。  

    ![가격 책정 계층](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 根据最近31天的使用情况查看估计的成本后，如果决定更改定价层，请单击 "**选择**"。  

还可以通过 Azure 资源管理器使用 `sku` 参数（在 Azure 资源管理器模板中为`pricingTier`）来[设置定价层](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 

## <a name="legacy-pricing-tiers"></a>레거시 가격 책정 계층

在2019企业协议2018年4月2日之前，具有 Log Analytics 工作区或 Application Insights 资源的订阅将继续具有使用旧版定价层的访问权限：**免费**版、**独立版（每 GB）** 和**每节点（OMS）** 。  免费定价层中的工作区将每日数据引入限制为 500 MB （Azure 安全中心收集的安全数据类型除外），并且数据保留期限制为7天。 免费定价层仅用于评估目的。 独立或按节点定价层中的工作区在用户可配置的保留期最多为2年。 

在2016年4月之前创建的工作区还可以访问原始**标准**和**高级**定价层，它们分别固定数据保持期30和365天。 无法在**标准**定价层或**高级**定价层中创建新工作区。如果工作区移出了这些层，则不能将其移回。 

有关定价层限制的更多详细信息，请参阅[此](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)文。

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

다음 단계에서는 로그 데이터가 작업 영역에 보존되는 기간을 구성하는 방법을 설명합니다.

### <a name="default-retention"></a>默认保留期

若要设置工作区的默认保留期， 
 
1. 在 Azure 门户的工作区中，从左窗格中选择 "**使用情况和估计成本**"。
2. **사용량 및 예상 비용** 창의 페이지의 상단에서 **데이터 볼륨 관리**를 클릭합니다.
3. 창에서 슬라이더를 이동하여 일 수를 늘리거나 줄인 다음, **확인**을 클릭합니다.  *무료* 계층에서 작업 중인 경우 데이터 보존 기간을 수정할 수 없으며 이 설정을 제어하기 위해 유료 계층으로 업그레이드해야 합니다.

    ![更改工作区数据保持期设置](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
还可以[通过 Azure 资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)使用 `retentionInDays` 参数设置保留。 此外，如果将数据保留期设置为30天，则可以使用 `immediatePurgeDataOn30Days` 参数触发立即清除旧数据，这对于符合性相关的方案可能很有用。 仅通过 Azure 资源管理器公开此功能。 

默认情况下，两种数据类型（`Usage` 和 `AzureActivity`）将保留90天，此90天保留期不收取任何费用。 这些数据类型还可从数据引入费用中免费接收。 

### <a name="retention-by-data-type"></a>按数据类型保留

还可以为单个数据类型指定不同的保留设置。 每个数据类型都是工作区的子资源。 例如，SecurityEvent 表可以在 Azure 中进行寻址[资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)如下所示：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

请注意，数据类型（表）区分大小写。  若要获取特定数据类型（在此示例中为 SecurityEvent）中的每个数据类型保留设置的当前设置，请使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

若要获取工作区中所有数据类型的当前每个数据类型保持设置，只需省略特定的数据类型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要将特定数据类型（在本例中为 SecurityEvent）的保留期设置为730天，请执行

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`Usage` 和 `AzureActivity` 数据类型不能通过自定义保留设置进行设置。 它们将采用默认工作区保持期或90天内的最大值。 

用于直接连接到 Azure 资源管理器以通过数据类型设置保留的极佳工具是 OSS 工具[ARMclient](https://github.com/projectkudu/ARMClient)。  详细了解 ARMclient [Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章。  下面是一个使用 ARMClient 的示例，将 SecurityEvent 数据设置为730天的保留期：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> 设置单个数据类型的保留期可用于降低数据保留成本。  对于从2019年10月（发布此功能时）开始收集的数据，减少某些数据类型的保留期可降低你的时间。  对于先前收集的数据，为单独类型设置较低的保留期不会影响你的保留费用。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理每日最大数据量

작업 영역에 대한 일일 한도를 구성하고 일일 수집량을 제한할 수 있지만 목표치가 일일 한도에 도달하지 않도록 주의하십시오.  그렇지 않으면 남은 기간 동안의 데이터가 손실됩니다. 이는 해당 기능이 작업 영역에서 사용할 수 있는 최신 데이터에 의존할 수도 있는 다른 Azure 서비스 및 솔루션에 영향을 줄 수 있습니다.  결과적으로 리소스의 상태 조건이 IT 서비스를 지원할 때 경고를 관찰하고 수신하는 기능이 영향을 받습니다.  每日上限旨在用作一种方法，用于管理托管资源中意外增加的数据量并保持在限制范围内，或者当你希望限制工作区的计划外费用时。  

일일 한도에 도달하면 하루의 나머지 시간 동안 청구 가능한 데이터 형식의 수집을 중지합니다. 선택된 Log Analytics 작업 영역에 대한 페이지의 상단에 경고 배너가 표시되고 작업 이벤트가 **LogManagement** 범주 아래의 *작업* 테이블로 전송됩니다. *일일 한도 아래 정의된 재설정 시간이* 로 설정된 후 데이터 수집이 다시 시작합니다. 일일 데이터 한도에 도달했을 때 알려주도록 구성된 이 작업 이벤트를 기반으로 경고 규칙을 정의하는 것이 좋습니다. 

> [!NOTE]
> 除了在2017年6月19日之前安装了 Azure 安全中心的工作区之外，每日上限不会停止从 Azure 安全中心收集数据。 

> [!NOTE]
> 应用每日上限所固有的延迟可能意味着上限不会应用到指定的每日上限级别。 

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

데이터 수집 추세 및 정의할 일일 볼륨 한도를 이해하려면 [Log Analytics 사용량 및 예상 비용](usage-estimated-costs.md)을 검토합니다. 한도에 도달한 후에는 리소스를 모니터링할 수 없으므로 신중하게 고려해야 합니다. 

### <a name="set-the-daily-cap"></a>设置每日上限

以下步骤介绍了如何配置限制，以管理 Log Analytics 工作区将每天摄取的数据量。  

1. 작업 영역의 왼쪽 창에서 **사용량 및 예상 비용**을 선택합니다.
2. 선택한 작업 영역에 대한 **사용량 및 예상 비용** 창의 페이지의 상단에서 **데이터 볼륨 관리**를 클릭합니다. 
3. 일일 한도는 기본으로 **OFF**이며 이를 사용하도록 설정하려면 **ON**을 클릭한 다음, 데이터 볼륨 한도를 GB/일로 설정합니다.

    ![Log Analytics 配置数据限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>达到每日上限时发出警报

데이터 제한 임계값에 도달하는 경우 Azure Portal에 시각적 큐를 표시하는 반면, 이 동작은 즉각적인 주의가 필요한 운영 문제를 관리하는 방법에 맞출 필요는 없습니다.  경고 알림을 수신하려면 Azure Monitor에서 새 경고 규칙을 만들 수 있습니다.  若要了解详细信息，请参阅[如何创建、查看和管理警报](alerts-metric.md)。

시작하려면 경고에 대한 권장 설정은 다음과 같습니다.

- 대상: Log Analytics 리소스 선택
- 조건: 
   - 신호 이름: 사용자 지정 로그 검색
   - 검색 쿼리: 작업 | 세부 정보에 'OverQuota'가 있는 경우
   - 기준: 결과 수
   - 조건: 보다 큼
   - 임계값: 0
   - 기간: 5(분)
   - 빈도: 5(분)
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)

경고가 정의되고 한계에 도달하면 경고가 트리거되고 작업 그룹에서 정의된 응답을 수행합니다. 이메일 및 텍스트 메시지를 통해 팀에 알리거나 웹후크, Automation Runbook 또는 [외부 ITSM 솔루션을 사용해 통합](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)을 사용하여 작업을 자동화할 수 있습니다. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>사용량이 예상보다 더 높은 원인 해결

사용량이 높은 원인은 다음과 같습니다.
- 与将数据发送到 Log Analytics 工作区相比，需要更多节点
- 超出了发送到 Log Analytics 工作区所需的数据

## <a name="understanding-nodes-sending-data"></a>了解发送数据的节点

若要了解上个月每天报告检测信号的计算机数，请使用

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

若要获取将按节点计费的计算机的列表（如果工作区位于 "基于每个节点的旧版" 定价层中），请查找发送**计费数据类型**的节点（某些数据类型是免费的）。 为此，请使用 `_IsBillable`[属性](log-standard-properties.md#_isbillable)，并使用完全限定的域名最左边的字段。 这会返回包含计费数据的计算机的列表：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

可查看的可计费节点数估算如下： 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName)
```

> [!NOTE]
> 여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다. 此查询替代了使用使用情况数据类型查询每台计算机的信息的旧方法。  

更准确地计算实际将计费的内容是获取每小时发送计费数据类型的计算机的计数。 （对于旧的按节点定价层中的工作区，Log Analytics 计算需要按小时计费的节点数。） 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>了解引入数据量

**사용량 및 예상 비용** 페이지에서 *솔루션당 데이터 수집* 차트는 전송된 총 데이터 양과 각 솔루션이 전송하는 데이터 양을 보여 줍니다. 이를 통해 전체 데이터 사용량(또는 특정 솔루션에 의한 사용량)이 증가하는지, 고정적인지, 감소하는지 여부의 추세를 판단할 수 있습니다. 이를 생성하는 데 사용되는 쿼리는 다음과 같습니다.

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

여기서 “where IsBillable = true” 절은 수집 비용이 없는 특정 솔루션에서 데이터 형식을 필터링합니다. 

데이터 추세를 보다 자세히 조사하여 특정 데이터 형식을 발견할 수 있습니다(예: IIS 로그로 인한 데이터를 연구하려는 경우).

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>按计算机的数据量

若要查看每台计算机的可计费事件引入**大小**，请使用 `_BilledSize` 属性，该[属性](log-standard-properties.md#_billedsize)以字节为单位提供大小：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable`[属性](log-standard-properties.md#_isbillable)指定引入数据是否会产生费用。

若要查看每台计算机引入的可**计费**事件计数，请使用 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

특정 컴퓨터로 데이터를 전송하는 청구 가능 데이터 형식 수를 확인하려면 다음을 사용합니다.

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 资源、资源组或订阅的数据量

对于在 Azure 中托管的节点的数据，可以获取__每台计算机__的可计费事件引入的**大小**，使用 _ResourceId 属性，该[属性](log-standard-properties.md#_resourceid)提供资源的完整路径：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

对于 Azure 中托管的节点的数据，可以获取__每个 azure 订阅__的可计费事件引入的**大小**，将 `_ResourceId` 属性分析为：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

将 `subscriptionId` 更改为 `resourceGroup` 会按 Azure 资源组显示可计费引入数据量。 


> [!NOTE]
> 사용량 데이터 형식의 일부 필드가 여전히 스키마에 있지만 더 이상 사용되지 않으며 해당 값은 더 이상 채워지지 않습니다. 이는 **컴퓨터**일 뿐 아니라 수집과 관련된 필드(**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** 및 **AverageProcessingTimeMs**)이기도 합니다.

### <a name="querying-for-common-data-types"></a>查询常用数据类型

특정 데이터 형식의 데이터 소스를 더 자세히 알아보려면 다음 예와 같은 몇 가지 쿼리를 사용합니다.

+ **보안** 솔루션
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **로그 관리** 솔루션
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **성능** 데이터 형식
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **이벤트** 데이터 형식
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 데이터 형식
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 데이터 형식
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>데이터 볼륨을 줄이기 위한 팁

수집된 로그 볼륨을 줄이기 위한 몇 가지 제안 사항은 다음과 같습니다.

| 높은 데이터 볼륨의 소스 | 데이터 볼륨을 줄이는 방법 |
| -------------------------- | ------------------------- |
| 보안 이벤트            | [일반 또는 최소한의 보안 이벤트](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)를 선택합니다. <br> 보안 감사 정책을 변경하여 필요한 이벤트만을 수집합니다. 특히, 다음 항목에 대한 이벤트를 수집할 필요를 검토합니다. <br> - [감사 필터링 플랫폼](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [감사 레지스트리](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [감사 파일 시스템](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [감사 커널 개체](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [감사 핸들 조작](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 이동식 저장소 감사 |
| 성능 카운터       | [성능 카운터 구성](data-sources-performance-counters.md)을 다음과 같이 변경합니다. <br> - 컬렉션의 빈도 감소 <br> - 성능 카운터의 수 감소 |
| 이벤트 로그                 | [이벤트 로그 구성](data-sources-windows-events.md)을 다음과 같이 변경합니다. <br> - 수집된 이벤트 로그의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 수준 이벤트를 수집하지 않습니다. |
| Syslog                     | [syslog 구성](data-sources-syslog.md)을 다음과 같이 변경합니다. <br> - 수집된 기능의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 및 *디버그* 수준 이벤트를 수집하지 않습니다. |
| AzureDiagnostics           | 다음 작업을 수행하도록 리소스 로그 컬렉션을 변경합니다. <br> - Log Analytics로 보내는 리소스 송신 로그의 수 축소 <br> - 필요한 로그만 수집 |
| 솔루션을 사용하지 않는 컴퓨터의 솔루션 데이터 | [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다. |

### <a name="getting-security-and-automation-node-counts"></a>获取安全和自动化节点计数

“노드별 (OMS)” 가격 책정 계층에 속하는 경우 사용하는 노드 및 솔루션 수에 따라 비용이 청구됩니다. 요금이 청구되는 Insights 및 Analytics 노드의 수는 **사용량 및 예상 비용** 페이지의 표에 표시됩니다.  

고유한 보안 노드의 수를 보려면 다음 쿼리를 사용할 수 있습니다.

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

고유한 Automation 노드의 수를 보려면 다음 쿼리를 사용합니다.

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>在数据收集很高的情况时创建警报

이 섹션에서는 경고가 발생하는 경우를 설명합니다.
- 데이터 볼륨이 지정된 크기를 초과합니다.
- 데이터 볼륨이 지정된 크기를 초과할 것으로 예측됩니다.

검색 쿼리를 사용하는 Azure 경고 지원 [경고 로그](alerts-unified-log.md)입니다. 

최근 24시간 내에 수집된 데이터가 100GB를 초과하는 경우 다음 쿼리에 결과가 표시됩니다.

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

다음 쿼리는 간단한 수식을 사용하여 하루에 100GB를 초과하는 데이터가 전송되는 시기를 예측합니다. 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

다른 크기의 데이터 볼륨에 대해 경고하려면 쿼리의 100GB를 원하는 수로 변경합니다.

[새 로그 경고 만들기](alerts-metric.md)에 설명한 단계를 사용하여 데이터 컬렉션이 예상보다 높은 경우 알림을 받을 수 있습니다.

첫 번째 쿼리에 대한 경고를 만들 때 즉, 24시간 내에 데이터가 100GB를 초과하는 경우 다음을 설정합니다.  

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름**은 **로그 검색 사용자 지정**을 선택합니다.
   - **쿼리 검색**을 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`으로
   - **警报逻辑** **基于***结果数*，**条件***大于***阈值** *0*
   - 사용량 데이터가 시간당 한 번만 업데이트되므로 **기간**은 *1440*분이고 **주파수 경고**는 *60*분마다입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *24시간 내에 100GB를 초과하는 데이터 볼륨*으로
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

두 번째 쿼리에 대한 경고를 만들 때 즉, 24시간 내에 100GB를 초과하는 데이터가 예측되는 경우 다음을 설정합니다.

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름**은 **로그 검색 사용자 지정**을 선택합니다.
   - **쿼리 검색**을 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`으로
   - **警报逻辑** **基于***结果数*，**条件***大于***阈值** *0*
   - 사용량 데이터가 시간당 한 번만 업데이트되므로 **기간**은 *180*분이고 **주파수 경고**는 *60*분마다입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *24시간 내에 100GB를 초과한다고 예측되는 데이터 볼륨*으로
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

경고를 수신하는 경우 사용량이 예상보다 더 높은 원인을 해결하려면 다음 섹션의 단계를 사용합니다.

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 的数据传输费用

向 Log Analytics 发送数据可能会导致数据带宽费用。 如[Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位于两个区域的 azure 服务之间的数据传输以正常费率作为出站数据传输收费。 入站数据传输是免费的。 但是，这种收费非常小（几%）与 Log Analytics 数据引入的成本比较。 因此，控制 Log Analytics 的成本需要专注于引入数据量，我们提供了有助于[了解这一点的指南。](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics에서 더 이상 데이터를 수집하지 않는 문제 해결

레거시 무료 가격 책정 계층을 사용 중이고 하루에 500MB 이상의 데이터를 보낸 경우 남은 날 동안 데이터 수집이 중지됩니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다.  Log Analytics는 데이터 수집을 시작하고 중지할 때 Operation 형식의 이벤트를 만듭니다. 일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

数据收集停止时，OperationStatus 为**Warning**。 数据收集开始时，OperationStatus**成功**。 다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.  

|수집 중지 이유| 솔루션| 
|-----------------------|---------|
|레거시 무료 가격 책정 계층의 일일 한도에 도달함 |수집이 다음 날에 자동으로 다시 시작될 때까지 대기 또는 유료 가격 책정 계층으로 변경합니다.|
|작업 영역의 일일 상한에 도달함|수집이 자동으로 다시 시작될 때까지 대기하거나, 최대 일일 데이터 볼륨 관리의 설명처럼 일일 데이터 볼륨 한도를 늘립니다. 일일 상한 다시 설정 시간이 **데이터 볼륨 관리** 페이지에 표시됩니다. |
|Azure 구독이 다음으로 인해 일시 중단된 상태:<br> 평가판 종료<br> Azure 암호 만료<br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)|유료 구독으로 전환<br> 한도 제거 또는 한도가 재설정될 때까지 대기|

若要在数据收集停止时得到通知，请使用*创建每日数据上限*警报中所述的步骤，以便在数据收集停止时收到通知。 使用[创建操作组](action-groups.md)中所述的步骤，为警报规则配置电子邮件、webhook 或 runbook 操作。 

## <a name="limits-summary"></a>제한 요약

还有其他 Log Analytics 限制，其中一些限制依赖于 Log Analytics 定价层。 [此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)介绍了这些内容。


## <a name="next-steps"></a>다음 단계

- 有关如何使用搜索语言的详细说明，请参阅[Azure Monitor 日志中的日志搜索](../log-query/log-query-overview.md)。 사용량 현황 데이터에 대한 추가 분석을 수행하려면 검색 쿼리를 사용할 수 있습니다.
- [새 로그 경고 만들기](alerts-metric.md)에 설명한 단계를 사용하여 검색 기준이 충족되는 경우 알림을 받을 수 있습니다.
- [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다.
- [Azure Security Center 필터링 정책](../../security-center/security-center-enable-data-collection.md)을 검토하여 효과적인 이벤트 컬렉션 정책을 구성합니다.
- [성능 카운터 구성](data-sources-performance-counters.md)을 변경합니다.
- 이벤트 컬렉션 설정을 수정하려면 [이벤트 로그 구성](data-sources-windows-events.md)을 검토합니다.
- syslog 컬렉션 설정을 수정하려면 [syslog 구성](data-sources-syslog.md)을 검토합니다.
