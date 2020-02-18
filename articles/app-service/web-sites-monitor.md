---
title: 监视应用
description: 了解如何使用 Azure 门户在 Azure 应用服务中监视应用。 了解所报告的配额和指标。
author: btardif
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.topic: article
ms.date: 01/11/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 2bca4521184fa42002e6649a90bb9101fded595c
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658434"
---
# <a name="monitor-apps-in-azure-app-service"></a>监视 Azure 应用服务中的应用
[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)为[Azure 门户](https://portal.azure.com)中的 web 应用、移动和 API 应用提供内置的监视功能。

在 Azure 门户中，可以查看应用和应用服务计划的*配额*和*度量值*，并设置基于指标的*警报*和自动*缩放*。

## <a name="understand-quotas"></a>了解配额

对于托管在应用服务中的应用，其可用资源受到某些限制。 限制由与应用关联的应用服务计划定义。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

如果应用托管在“免费”或“共享”计划中，则该应用可用资源的限制由配额定义。

如果应用托管在“基本”、“标准”或“高级”计划中，则该应用可用资源的限制由应用服务计划的大小（小、中、大）和实例计数（1、2、3 等等）设置。

“免费”或“共享”应用的配额如下：

| 配额 | Description |
| --- | --- |
| **CPU（短期）** | 5 分钟间隔内允许此应用使用的 CPU 量。 此配额每五分钟重置。 |
| **CPU（天）** | 一天内允许此应用使用的 CPU 总量。 此配额每隔 24 小时在 UTC 午夜时间重置。 |
| **内存** | 允许此应用具有的内存总量。 |
| **带宽** | 一天内允许此应用使用的传出带宽总量。 此配额每隔 24 小时在 UTC 午夜时间重置。 |
| **文件系统** | 允许的存储空间总量。 |

适用于 "*基本*"、"*标准*" 和 "*高级*" 的应用的唯一配额是文件系统。

有关各种应用服务 SKU 的特定配额、限制和可用功能的详细信息，请参阅 [Azure 订阅服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="quota-enforcement"></a>配额强制执行

如果应用超过“CPU（短期）”、“CPU（天）”或“带宽”配额，则将终止该应用，直到配额重置。 在此期间，所有传入请求都将导致 HTTP 403 错误。

![403 错误消息][http403]

如果超过应用内存配额，则将重启该应用。

如果超过文件系统配额，则任何写入操作都会失败。 写入操作失败包括对日志的任何写入。

可通过升级应用服务计划在应用中提高或删除配额。

## <a name="understand-metrics"></a>了解指标

> [!NOTE]
> **文件系统使用**情况是指全局推出的新度量值，除非你已在个人预览版中列入允许列表，否则不会出现任何数据。
> 

指标提供有关应用或应用服务计划行为的信息。

应用的可用指标包括：

| 度量值 | Description |
| --- | --- |
| **平均响应时间** | 应用处理请求所用的平均时间（以秒为单位）。 |
| **平均内存工作集** | 应用使用的平均内存量，以兆字节 (MiB) 为单位。 |
| **连接** | 沙盒中存在的绑定套接字的数目（w3wp.exe 及其子进程）。  绑定套接字是通过调用 bind()/connect() API 创建的，并一直保留到通过 CloseHandle()/closesocket() 关闭所述的套接字。 |
| **CPU 时间** | 应用消耗的 CPU 量，以秒为单位。 有关此指标的详细信息，请参阅 [CPU 时间与 CPU 百分比](#cpu-time-vs-cpu-percentage)。 |
| **当前程序集** | 此应用程序中的所有 AppDomain 中加载的程序集的当前数量。 |
| **数据输入** | 应用消耗的传入带宽量，以 MiB 为单位。 |
| **数据输出** | 应用消耗的传出带宽量，以 MiB 为单位。 |
| **文件系统使用情况** | 应用使用的文件系统配额的百分比。 |
| **第 0 代垃圾回收** | 自应用进程启动以来对第 0 代对象进行垃圾回收的次数。 较高代系的垃圾回收包括所有较低代系的垃圾回收。|
| **第 1 代垃圾回收** | 自应用进程启动以来对第 1 代对象进行垃圾回收的次数。 较高代系的垃圾回收包括所有较低代系的垃圾回收。|
| **第 2 代垃圾回收** | 自应用进程启动以来对第 2 代对象进行垃圾回收的次数。|
| **句柄计数** | 应用进程当前打开的句柄总数。|
| **Http 2xx** | 导致 HTTP 状态代码的请求计数大于等于 200，但小于 300。 |
| **Http 3xx** | 导致 HTTP 状态代码的请求计数大于等于 300，但小于 400。 |
| **Http 401** | 导致 HTTP 401 状态代码的请求计数。 |
| **Http 403** | 导致 HTTP 403 状态代码的请求计数。 |
| **Http 404** | 导致 HTTP 404 状态代码的请求计数。 |
| **Http 406** | 导致 HTTP 406 状态代码的请求计数。 |
| **Http 4xx** | 导致 HTTP 状态代码的请求计数大于等于 400，但小于 500。 |
| **Http 服务器错误** | 导致 HTTP 状态代码的请求计数大于等于 500，但小于 600。 |
| **IO 每秒其他字节数** | 应用进程向不涉及数据的 i/o 操作（如控制操作）发出字节的速率。|
| **IO 每秒其他操作数** | 应用进程发出非读取或写入操作的 i/o 操作的速率。|
| **IO 每秒读取字节数** | 应用进程通过 I/O 操作读取字节的速率。|
| **IO 每秒读取操作数** | 应用进程发出读取 I/O 操作的速率。|
| **IO 每秒写入字节数** | 应用进程向 I/O 操作写入字节的速率。|
| **IO 每秒写入操作数** | 应用进程发出写入 I/O 操作的速率。|
| **内存工作集** | 应用当前使用的内存量，以 MiB 为单位。 |
| **专用字节数** | "专用字节" 是应用进程分配的不能与其他进程共享的内存的当前大小（以字节为单位）。|
| **请求** | 请求总数，不考虑是否导致 HTTP 状态代码。 |
| **应用程序队列中的请求数** | 应用程序请求队列中的请求数。|
| **线程计数** | 应用进程中当前处于活动状态的线程数。|
| **应用程序域总数** | 此应用程序中加载的 AppDomain 的当前数目。|
| **卸载的应用程序域总数** | 自应用程序启动以来卸载的 AppDomain 的总数。|


应用服务计划的可用指标包括：

> [!NOTE]
> 应用服务计划指标仅适用于“基本”、“标准”和“高级”层中的计划。
> 

| 度量值 | Description |
| --- | --- |
| **CPU 百分比** | 计划的所有实例使用的平均 CPU 量。 |
| **内存百分比** | 计划的所有实例使用的平均内存量。 |
| **数据输入** | 计划的所有实例使用的平均输入带宽量。 |
| **数据输出** | 计划的所有实例使用的平均输出带宽量。 |
| **磁盘队列长度** | 在存储上排队的读取和写入请求的平均数量。 较高的磁盘队列长度表示应用程序可能由于磁盘 i/o 过多而速度变慢。 |
| **Http 队列长度** | 必须在队列排满之前排入队列中的 HTTP 请求的平均数量。 较高或不断增长的 HTTP 队列长度表示计划处于高负载状态。 |

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 时间和 CPU 百分比
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

有两个反映 CPU 使用率的指标：

**CPU 时间**：对于在免费或共享计划中托管的应用很有用，因为其中一个配额是在应用使用的 CPU 分钟数中定义的。

**CPU 百分比**：对于托管在基本、标准和高级计划中的应用很有用，因为它们可以横向扩展。CPU 百分比是所有实例的总体使用情况的良好指示。

## <a name="metrics-granularity-and-retention-policy"></a>指标粒度和保留策略
应用和应用服务计划的指标由具有下列粒度和保留策略的服务进行记录和聚合：

* **分钟**粒度指标保留30小时。
* **小时**粒度度量值将保留30天。
* **天**粒度指标保留30天。

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>在 Azure 门户中监视配额和指标
若要查看影响应用的各种配额和指标的状态，请转到 [Azure 门户](https://portal.azure.com)。

![Azure 门户中的“配额”图表][quotas]

若要查找配额，请选择“设置” > “配额”。 在图表中，可以查看： 
1. 配额名称。
1. 配额的重置时间间隔。
1. 配额的当前限制。
1. 配额的当前值。

![Azure 门户中的指标图表][metrics] 可以直接从资源**概述**页访问指标。 在这里，你将看到表示某些应用指标的图表。

单击其中任何图表将转到 "指标" 视图，您可以在其中创建自定义图表，查询不同的指标等等。 

若要详细了解指标，请参阅[监视服务指标](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

## <a name="alerts-and-autoscale"></a>警报和自动缩放
可将应用或应用服务计划的指标挂接到警报。 有关详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-alerts-portal.md)。

在基本或更高的应用服务计划中托管的应用服务应用支持自动缩放。 使用自动缩放可以配置用于监视应用服务计划指标的规则。 规则可以增加或减少实例计数，并根据需要提供更多的资源。 规则还有助于避免过度预配应用，从而节省资金。

有关自动缩放的详细信息，请参阅[如何缩放](../monitoring-and-diagnostics/insights-how-to-scale.md)以及[有关 Azure Monitor 自动缩放的最佳做法](../azure-monitor/platform/autoscale-best-practices.md)。

[fzilla]:https://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:https://go.microsoft.com/fwlink/?LinkID=309169

<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png