---
title: "在 Azure 应用服务中监视应用 | Microsoft Docs"
description: "了解如何使用 Azure 门户在 Azure 应用服务中监视应用。"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: byvinyal
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 25d3776920d683fffedcd8ac6ed0e84dfe875974
ms.contentlocale: zh-cn
ms.lasthandoff: 08/08/2017

---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>如何：在 Azure 应用服务中监视 Web 应用
[应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)在 [Azure 门户](https://portal.azure.com)中提供了内置监视功能。
还能查看应用的**配额**和**度量值**以及应用服务计划、设置**警报**，甚至基于这些度量值自动**缩放**。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>了解配额和度量值
### <a name="quotas"></a>配额
对于托管在应用服务中的应用程序，其可用资源受到某些*限制*。 限制由与应用关联的**应用服务计划**定义。

如果应用程序托管在**免费**或**共享**计划中，则该应用可用资源的限制由**配额**定义。

如果应用程序托管在**基本**、**标准**或**高级**计划中，则该应用可用资源的限制由**应用服务计划**的**大小**（小、中、大）和**实例计数**（1、2、3...）设置。

**免费**或**共享**应用的**配额**如下：

* **CPU（短期）**
  * 5 分钟内允许此应用程序使用的 CPU 量。 此配额每 5 分钟重置。
* **CPU（天）**
  * 1 天内允许此应用程序使用的 CPU 总量。 此配额每隔 24 小时在 UTC 午夜时间重置。
* **内存**
  * 允许此应用程序具有的内存总量。
* **带宽**
  * 1 天内允许此应用程序传出的带宽总量。
    此配额每隔 24 小时在 UTC 午夜时间重置。
* **文件系统**
  * 允许的存储空间总量。

适用于托管在**基本**、**标准**和**高级**计划中的应用的唯一配额是**文件系统**。

有关各种应用服务 SKU 可用的特定配额、限制和功能的详细信息，请参见：[Azure 订阅服务限制](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>配额强制执行
如果应用程序的使用量超过 **CPU（短期）**、**CPU（天）**或**带宽**配额，则将终止该应用程序，直到配额重置。 在此期间，所有传入请求都将导致 **HTTP 403**。
![][http403]

如果超过应用程序**内存**配额，则将重启该应用程序。

如果超过**文件系统**配额，则任何写入操作都会失败，包括写入日志。

可通过升级应用服务计划从应用中增加或删除配额。

### <a name="metrics"></a>度量值
**度量值**提供有关应用或应用服务计划行为的信息。

对于**应用程序**，可用度量值为：

* **平均响应时间**
  * 应用处理请求的平均时间（以毫秒为单位）。
* **平均内存工作集**
  * 应用使用的平均内存量（以 MiB 为单位）。
* **CPU 时间**
  * 应用使用的 CPU 量（以秒为单位）。 有关此度量值的详细信息，请参阅：[CPU 时间与 CPU 百分比](#cpu-time-vs-cpu-percentage)
* **数据输入**
  * 应用使用的传入带宽量（以 MiB 为单位）。
* **数据输出**
  * 应用使用的传出带宽量（以 MiB 为单位）。
* **Http 2xx**
  * 导致 http 状态代码的请求计数大于等于 200，但小于 300。
* **Http 3xx**
  * 导致 http 状态代码的请求计数大于等于 300，但小于 400。
* **Http 401**
  * 导致 HTTP 401 状态代码的请求计数。
* **Http 403**
  * 导致 HTTP 403 状态代码的请求计数。
* **Http 404**
  * 导致 HTTP 404 状态代码的请求计数。
* **Http 406**
  * 导致 HTTP 406 状态代码的请求计数。
* **Http 4xx**
  * 导致 http 状态代码的请求计数大于等于 400，但小于 500。
* **Http 服务器错误**
  * 导致 http 状态代码的请求计数大于等于 500，但小于 600。
* **内存工作集**
  * 应用当前使用的内存量（以 MiB 为单位）。
* **请求**
  * 请求总数（不考虑是否导致 HTTP 状态代码）。

对于**应用服务计划**，可用度量值为：

> [!NOTE]
> 应用服务计划度量值仅适用于**基本**、**标准**和**高级** SKU 中的计划。
> 
> 

* **CPU 百分比**
  * 计划的所有实例使用的平均 CPU 量。
* **内存百分比**
  * 计划的所有实例使用的平均内存量。
* **数据输入**
  * 计划的所有实例使用的平均输入带宽量。
* **数据输出**
  * 计划的所有实例使用的平均输出带宽量。
* **磁盘队列长度**
  * 在存储上排队的读取和写入请求的平均数量。 过高的磁盘队列长度表示应用程序可能由于磁盘 I/O 过多而速度变慢。
* **Http 队列长度**
  * 必须在队列排满之前排入队列中的 HTTP 请求的平均数量。 较高或不断增长的 HTTP 队列长度表示计划处于高负载状态。

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 时间和 CPU 百分比
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

有 2 个反映 CPU 使用率的度量值。 **CPU 时间**和 **CPU 百分比**

**CPU 时间**对托管在**免费**或**共享**计划中的应用很有用，因为这些应用的其中一个配额由应用所用的 CPU 时间定义。

另一方面，**CPU 百分比**对托管在**基本**、**标准**和**高级**计划中的应用很有用，因为这些应用可以按比例扩大，并且该度量值能很好反映所有实例的总体使用情况。

## <a name="metrics-granularity-and-retention-policy"></a>度量值粒度和保留策略
应用程序和应用服务计划的度量值由具有下列粒度和保留策略的服务进行记录和聚合：

* **分钟**粒度级的度量值将保留 **48 小时**
* **小时**粒度级的度量值将保留 **30 天**
* **天**粒度级的度量值将保留 **90 天**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>在 Azure 门户中监视配额和度量值。
可以在 [Azure 门户](https://portal.azure.com) 中查看影响应用程序的各种 **配额** 和 **度量值** 。

可以在“设置”>**配额** 下找到 ![][quotas]
**配额** 。 用户体验允许查看：(1) 配额名称、(2) 配额重置时间间隔、(3) 配额当前限制和 (4) 当前值。

可以直接从资源边栏选项卡中访问![][metrics]
**度量值**。 还可以通过以下操作自定义图表：(1) **单击**图表，并选择 (2)“编辑图表”。
可在此处更改要显示的 (3) **时间范围**、(4) **图表类型**和 (5) **度量值**。  

可以在此处了解有关度量值的详细信息：[监视服务度量值](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

## <a name="alerts-and-autoscale"></a>警报和自动缩放
可将应用或应用服务计划的度量值连接到警报；要了解相关详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

托管在基本、标准或高级应用服务计划中的应用服务应用支持**自动缩放**。 这样便可以配置监视应用服务计划度量值的规则，还能增加或减少根据需要提供其他资源或在过度预配时节约资金的实例计数。 可以在此处了解有关自动缩放的详细信息：[如何缩放](../monitoring-and-diagnostics/insights-how-to-scale.md)以及 [Azure 监视器自动缩放的最佳做法](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

> [!NOTE]
> 如果要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为 应用服务的指南，请参阅 [Azure 应用服务及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png

