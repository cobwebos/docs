---
title: 了解 Azure Spring Cloud 的指标
description: 了解如何在 Azure 春季云中查看指标
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: 7ef7614b32dd88e804b1a74749ee87504f9cea40
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205451"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>了解 Azure 春季云的指标

Azure 指标资源管理器是 Microsoft Azure 门户的一种组件，它允许绘制图表、直观关联趋势，并调查指标中的峰值和 dip。 使用指标资源管理器调查资源的运行状况和利用率。 

在 Azure 春季云中，指标有两个视点。
* 每个应用程序概述页中的图表
* 常用指标页

 ![指标图表](media/metrics/metrics-1.png)

应用程序**概述**中的图表提供每个应用程序的快速状态检查。 "常用**指标**" 页包含可供参考的所有指标。 可以在 "常用指标" 页中生成自己的图表，并将其固定在仪表板中。

## <a name="application-overview-page"></a>应用程序概述页
在**应用**中选择应用，在 "概述" 页中查找图表。  

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

左侧导航窗格中的**度量值**链接到 "常用指标" 页。

首先，选择要查看的度量值：

![选择指标视图](media/metrics/metrics-4.png)

可在以下[部分](#user-metrics-options)找到所有指标选项的详细信息。

接下来，为每个指标选择聚合类型：

![指标聚合](media/metrics/metrics-5.png)

聚合类型指示如何按时间对图表中的度量值点进行聚合。 每分钟有一个原始指标点，一分钟内的预聚合类型由指标类型预定义。
* Sum：将所有值的总和作为目标输出。
* Average：使用期间中的平均值作为目标输出。
* Max/Min：使用时间段中的最大值/最小值作为目标输出。

还可以将时间范围从过去30分钟调整到过去30天或自定义时间范围。

![指标修改](media/metrics/metrics-6.png)

默认视图包含所有 Azure 春季云服务的 application's 度量值。 可在显示中筛选一个应用或实例的指标。  单击 "**添加筛选器**"，将属性设置为 "**应用**"，然后在 "**值**" 文本框中选择要监视的目标应用程序。 

可以使用两种类型的筛选器 (属性) ：
* 应用：按应用名称筛选
* 实例：按应用实例筛选

![指标筛选器](media/metrics/metrics-7.png)

你还可以使用 "**应用拆分**" 选项，该选项将为一个应用绘制多个行：

![度量值拆分](media/metrics/metrics-8.png)

>[!TIP]
> 可以在 "指标" 页上构建自己的图表，并将其固定到**仪表板**。 一开始，为图表命名。  接下来，选择“固定到右上角的仪表板”****。 现在可以在门户“仪表板”中检查应用程序了。****

## <a name="user-metrics-options"></a>用户度量值选项

下表显示了可用的指标和详细信息。

### <a name="error"></a>错误
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat. 错误 | tomcat. 错误 | 计数 | 处理的请求中发生的错误数 |

### <a name="performance"></a>性能
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| system.object。用法 | system.object。用法 | 百分比 | 整个系统的最近 CPU 使用率。 此值为 [0.0，1.0] 间隔中的双精度值。 如果值为0.0，则表示在观察到的最近一段时间内所有 Cpu 都处于空闲状态，而如果值为1.0，则表示在观察到最近一段时间内，所有 Cpu 都正在积极运行的时间为100%。|
>| 进程。使用情况 | 应用 CPU 使用率百分比 | 百分比 | Java 虚拟机进程的最近 CPU 使用率。 此值为 [0.0，1.0] 间隔中的双精度值。 如果值为0.0，则表示在观察到的最近一段时间内，任何 Cpu 都不会运行 JVM 进程中的线程，而值1.0 则表示所有 Cpu 都在观察到的最近一段时间内从 100 JVM 中主动运行了线程。 JVM 中的线程包括应用程序线程以及 JVM 内部线程。|
>| jvm。已提交 | jvm。已提交 | 字节 | 表示可保证由 JVM 使用的内存量。 JVM 可能会将内存释放给系统，并提交的时间可能低于 init。 提交的将始终大于或等于使用。 |
>| jvm。已使用 | jvm。已使用 | 字节 | 表示当前使用的内存量（以字节为单位）。 |
>| jvm. max | jvm. max | 字节 | 表示可用于内存管理的最大内存量。 如果定义了 max，则已使用和已提交的内存量将始终小于或等于最大值。 如果内存分配尝试增加已使用的内存（即使使用的内存仍为 (true，即使已使用 <= max），则内存分配可能会失败，例如，当系统的虚拟内存 > 不足时) 。 |
>| jvm. 最大数据大小 | jvm. 最大数据大小 | 字节 | 自启动 Java 虚拟机以来旧代内存池的最大内存使用量。 |
>| jvm. 数据大小 | jvm. 数据大小 | 字节 | 完全 GC 后的旧代内存池的大小。 |
>| jvm。已升级 | jvm。已升级 | 字节 | 在 gc 到 gc 之后，旧代内存池的大小正增加的计数。 |
>| jvm。已分配 | jvm。已分配 | 字节 | 增加了一个 GC 到下一个 GC 之前的空间增加的空间。 |
>| jvm。 total. count | jvm (总计计数)  | 计数 | 此 JMV 开始后的总 GC 计数，包括年轻人和旧 GC。 |
>| jvm： total. time | jvm (总计时间)  | 毫秒 | 此 JMV 启动后使用的 GC 时间总数，包括年轻和旧 GC。 |

### <a name="request"></a>请求
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat. 已发送 | tomcat. 已发送 | 字节 | 已发送的数据 Tomcat web 服务器量 |
>| tomcat。已接收 | tomcat。已接收 | 字节 | 已接收的数据 Tomcat web 服务器数量 |
>| tomcat. total. count | tomcat (总计-计数)  | 计数 | Tomcat web 服务器处理的请求总数 |
>| tomcat. max | tomcat. max | 毫秒 | Tomcat web 服务器处理请求的最长时间 |

### <a name="session"></a>会话
>[!div class="mx-tdCol2BreakAll"]
>| 名称 | 弹簧制动器指标名称 | 计价单位 | 详细信息 |
>|----|----|----|------------|
>| tomcat。最大值 | tomcat。最大值 | 计数 | 同时处于活动状态的会话的最大数量 |
>| tomcat。最大值 | tomcat。最大值 | 毫秒 | ) 过期会话处于活动状态的最长时间 (（秒） |
>| tomcat。已创建 | tomcat。已创建 | 计数 | 已创建的会话数 |
>| tomcat 会话。已过期 | tomcat 会话。已过期 | 计数 | 已过期的会话数 |
>| tomcat 会话。已拒绝 | tomcat 会话。已拒绝 | 计数 | 由于达到了最大活动会话数而未创建的会话数。 |
>| tomcat 会话。当前 | tomcat 会话。当前 | 计数 | Tomcat 会话活动计数 |

## <a name="see-also"></a>另请参阅
* [Azure 指标资源管理器入门](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [通过诊断设置分析日志和指标](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>后续步骤
* [教程：使用警报和操作组监视春季云资源](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [适用于 Azure 春季云的配额和服务计划](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

