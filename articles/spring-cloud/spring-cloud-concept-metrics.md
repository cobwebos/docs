---
title: 了解 Azure Spring Cloud 的指标
description: 了解如何在 Azure 春季云中查看指标
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e488f2ddc44f1339d648cd6fe6b1aae18b748679
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892643"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>了解 Azure 春季云的指标

Azure 指标资源管理器是 Microsoft Azure 门户的一种组件，它允许绘制图表、直观关联趋势，并调查指标中的峰值和 dip。 使用指标资源管理器调查资源的运行状况和利用率。 

在 Azure 春季云中，指标有两个视点。
* 每个应用程序概述页中的图表
* 常用指标页

 ![指标图表](media/metrics/metrics-1.png)

应用程序 **概述** 中的图表提供每个应用程序的快速状态检查。 "常用 **指标** " 页包含可供参考的所有指标。 可以在 "常用指标" 页中生成自己的图表，并将其固定在仪表板中。

## <a name="application-overview-page"></a>应用程序概述页
在 **应用** 中选择应用，在 "概述" 页中查找图表。  

 ![应用程序指标管理](media/metrics/metrics-2.png)

每个应用程序的“应用程序概览”页都会提供一个指标图表，用于对应用程序进行快速状态检查。****  

 ![应用程序指标概述](media/metrics/metrics-3.png)

Azure 春季云提供以下五个图表，其中包含每分钟更新的指标：

* **Http 服务器错误**：向应用发出 http 请求时出现错误计数
* **数据**：应用接收的字节数
* **输出数据**：应用发送的字节数
* **请求**：应用收到的请求
* **平均响应时间**：应用程序的平均响应时间

对于图表，可以选择一个小时到七天的时间范围。

## <a name="common-metrics-page"></a>常用指标页

左侧导航窗格中的 **度量值** 链接到 "常用指标" 页。

首先，选择要查看的度量值：

![选择指标视图](media/metrics/metrics-4.png)

可在以下 [部分](#user-metrics-options) 找到所有指标选项的详细信息。

接下来，为每个指标选择聚合类型：

![指标聚合](media/metrics/metrics-5.png)

聚合类型指示如何按时间对图表中的度量值点进行聚合。 每分钟有一个原始指标点，一分钟内的预聚合类型由指标类型预定义。
* Sum：将所有值的总和作为目标输出。
* Average：使用期间中的平均值作为目标输出。
* Max/Min：使用时间段中的最大值/最小值作为目标输出。

还可以将时间范围从过去30分钟调整到过去30天或自定义时间范围。

![指标修改](media/metrics/metrics-6.png)

默认视图包含所有 Azure 春季云服务的 application's 度量值。 可在显示中筛选一个应用或实例的指标。  单击 " **添加筛选器**"，将属性设置为 " **应用**"，然后在 " **值** " 文本框中选择要监视的目标应用程序。 

可以使用两种类型的筛选器 (属性) ：
* 应用：按应用名称筛选
* 实例：按应用实例筛选

![指标筛选器](media/metrics/metrics-7.png)

你还可以使用 " **应用拆分** " 选项，该选项将为一个应用绘制多个行：

![度量值拆分](media/metrics/metrics-8.png)

>[!TIP]
> 可以在 "指标" 页上构建自己的图表，并将其固定到 **仪表板**。 一开始，为图表命名。  接下来，选择“固定到右上角的仪表板”****。 现在可以在门户“仪表板”中检查应用程序了。****

## <a name="user-metrics-options"></a>用户度量值选项

下表显示了可用的指标和详细信息。

### <a name="error"></a>错误
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat.global.error | tomcat.global.error | 计数 | 处理的请求中发生的错误数 |

### <a name="performance"></a>性能
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| system.cpu.usage | system.cpu.usage | 百分比 | 整个系统的最近 CPU 使用率。 此值为 [0.0，1.0] 间隔中的双精度值。 如果值为0.0，则表示在观察到的最近一段时间内所有 Cpu 都处于空闲状态，而如果值为1.0，则表示在观察到最近一段时间内，所有 Cpu 都正在积极运行的时间为100%。|
>| process.cpu.usage | 应用 CPU 使用率百分比 | 百分比 | Java 虚拟机进程的最近 CPU 使用率。 此值为 [0.0，1.0] 间隔中的双精度值。 如果值为0.0，则表示在观察到的最近一段时间内，任何 Cpu 都不会运行 JVM 进程中的线程，而值1.0 则表示所有 Cpu 都在观察到的最近一段时间内从 100 JVM 中主动运行了线程。 JVM 中的线程包括应用程序线程以及 JVM 内部线程。|
>| jvm.memory.committed | jvm.memory.committed | 字节 | 表示可保证由 JVM 使用的内存量。 JVM 可能会将内存释放给系统，并提交的时间可能低于 init。 提交的将始终大于或等于使用。 |
>| jvm.memory.used | jvm.memory.used | 字节 | 表示当前使用的内存量（以字节为单位）。 |
>| jvm.memory.max | jvm.memory.max | 字节 | 表示可用于内存管理的最大内存量。 如果定义了 max，则已使用和已提交的内存量将始终小于或等于最大值。 如果内存分配尝试增加已使用的内存（即使使用的内存仍为 (true，即使已使用 <= max），则内存分配可能会失败，例如，当系统的虚拟内存 > 不足时) 。 |
>| jvm.gc.max.data.size | jvm.gc.max.data.size | 字节 | 自启动 Java 虚拟机以来旧代内存池的最大内存使用量。 |
>| jvm.gc.live.data.size | jvm.gc.live.data.size | 字节 | 完全 GC 后的旧代内存池的大小。 |
>| jvm.gc.memory.promoted | jvm.gc.memory.promoted | 字节 | 在 gc 到 gc 之后，旧代内存池的大小正增加的计数。 |
>| jvm.gc.memory.allocated | jvm.gc.memory.allocated | 字节 | 增加了一个 GC 到下一个 GC 之前的空间增加的空间。 |
>| jvm.gc.pause.total.count | jvm (总计计数)  | Count | 此 JMV 开始后的总 GC 计数，包括年轻人和旧 GC。 |
>| jvm.gc.pause.total.time | jvm (总计时间)  | 毫秒 | 此 JMV 启动后使用的 GC 时间总数，包括年轻和旧 GC。 |

::: zone pivot="programming-language-csharp"
### <a name="performance-net"></a>.NET) 的性能 (

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 单位 | 详细信息 |
>|------|-----------------------------|------|---------|
>| CPU 使用率       | cpu 使用情况      | 毫秒 | 进程使用 CPU 的时间。 |
>| 工作集     | 工作集    | 兆字节    | 进程使用的工作集数量。 |
>| GC 堆大小    | gc-堆大小   | 兆字节    | 垃圾回收器报告的堆总大小。 |
>| 第0代 GC 计数  | 第0代-gc 计数 | Count        | 每秒0代垃圾回收数。 |
>| 第1代 GC 计数  | 第1代-gc 计数 | Count        | 每秒1代垃圾回收数。 |
>| 第2代 GC 计数  | 第2代-gc 计数 | Count        | 每秒第2代垃圾回收数。 |
>| Gen 0 heap size（第 0 代堆大小） | 0-size     | 字节        | 第0代堆大小。 |
>| Gen 1 heap size（第 1 代堆大小） | 第1代-大小     | 字节        | 第1代堆大小。 |
>| Gen 2 heap size（第 2 代堆大小） | 第2代-大小     | 字节        | 第2代堆大小。 |
>| LOH 堆大小   | loh-大小       | 字节        | 大型对象堆堆大小。 |
>| 分配速率 | 分配速率     | 字节        | 每秒分配的字节数。 |
>| 程序集计数  | 程序集计数 | Count        | 已加载的程序集的数目。 |
>| 异常计数 | 异常-计数 | Count       | 每秒的异常数。 |
>| 线程池线程计数      | threadpool 线程计数              | Count | 线程池线程数。 |
>| 监视器锁争用计数 | 监视-锁定争用计数        | Count | 尝试拍摄监视器锁时每秒发生争用的次数。 |
>| 线程池队列长度      | 线程池-队列长度              | Count | 线程池工作项队列长度。 |
>| 线程池已完成项计数 | threadpool-已完成项-计数 | Count | 线程池已完成工作项计数。 |
>| 活动计时器计数               | 活动计时器-计数               | Count | 当前处于活动状态的计时器的数目。 活动计时器是注册后要在将来某个时间点进行计时的计时器，尚未取消。 |

有关详细信息，请参阅 [dotnet 计数器](/dotnet/core/diagnostics/dotnet-counters)。
::: zone-end

### <a name="request"></a>请求
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat.global.sent | tomcat.global.sent | 字节 | 已发送的数据 Tomcat web 服务器量 |
>| tomcat.global.received | tomcat.global.received | 字节 | 已接收的数据 Tomcat web 服务器数量 |
>| tomcat.global.request.total.count | tomcat (总计-计数)  | Count | Tomcat web 服务器处理的请求总数 |
>| tomcat.global.request.max | tomcat.global.request.max | 毫秒 | Tomcat web 服务器处理请求的最长时间 |

::: zone pivot="programming-language-csharp"
### <a name="request-net"></a> ( .NET) 请求

>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 单位 | 详细信息 |
>|------|-----------------------------|------|---------|
>| 每秒请求数 | 每秒请求数 | Count | 请求速率。 |
>| 请求总数 | 请求总数 | Count | 请求总数。 |
>| 当前请求 | 当前请求 | Count | 当前请求数。 |
>| 失败的请求 | 失败-请求 | Count | 失败的请求数。 |

有关详细信息，请参阅 [dotnet 计数器](/dotnet/core/diagnostics/dotnet-counters)。
::: zone-end

### <a name="session"></a>会话
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat.sessions.active.max | tomcat.sessions.active.max | 计数 | 同时处于活动状态的会话的最大数量 |
>| tomcat.sessions.alive.max | tomcat.sessions.alive.max | 毫秒 | ) 过期会话处于活动状态的最长时间 (（秒） |
>| tomcat.sessions.created | tomcat.sessions.created | 计数 | 已创建的会话数 |
>| tomcat.sessions.expired | tomcat.sessions.expired | 计数 | 已过期的会话数 |
>| tomcat.sessions.rejected | tomcat.sessions.rejected | 计数 | 由于达到了最大活动会话数而未创建的会话数。 |
>| tomcat.sessions.active.current | tomcat.sessions.active.current | 计数 | Tomcat 会话活动计数 |

## <a name="see-also"></a>请参阅

* [快速入门：通过日志、指标和跟踪来监视 Azure Spring Cloud 应用](spring-cloud-quickstart-logs-metrics-tracing.md)

* [Azure 指标资源管理器入门](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [通过诊断设置分析日志和指标](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>后续步骤

* [教程：使用警报和操作组监视春季云资源](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [适用于 Azure 春季云的配额和服务计划](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)
