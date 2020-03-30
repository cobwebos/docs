---
title: 了解 Azure Spring Cloud 的指标
description: 了解如何查看 Azure 春云中的指标
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.openlocfilehash: bb23afff2b4b449897d8e420934d038938d20205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256752"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>了解 Azure 春云的指标

Azure 指标资源管理器是 Microsoft Azure 门户的一个组件，它允许绘制图表、直观地关联趋势以及调查指标中的峰值和下降。 使用指标资源管理器调查资源的运行状况和利用率。 

在 Azure 春云中，指标有两个视图点。
* 每个应用程序概述页中的图表
* 通用指标页

 ![指标图表](media/metrics/metrics-1.png)

应用程序**概述**中的图表为每个应用程序提供快速的状态检查。 通用**指标**页包含可供参考的所有指标。 您可以在通用指标页中构建自己的图表，并将其固定在仪表板中。

## <a name="application-overview-page"></a>应用程序概述页
在**应用管理**中选择应用以在概览页中查找图表。  

 ![应用程序指标管理](media/metrics/metrics-2.png)

每个应用程序的“应用程序概览”页都会提供一个指标图表，用于对应用程序进行快速状态检查。****  

 ![应用程序指标概述](media/metrics/metrics-3.png)

Azure 春云为这五个图表提供了每分钟更新的指标：

* **Http 服务器错误**： 对应用的 HTTP 请求的错误计数
* **数据在**：应用收到的字节
* **数据出：** 应用发送的字节
* **请求**：应用收到的请求
* **平均响应时间**：来自应用的平均响应时间

对于图表，您可以选择一小时到七天的时间范围。

## <a name="common-metrics-page"></a>通用指标页

左侧导航窗格中的 **"指标"** 链接到公共指标页。

首先，选择要查看的指标：

![选择指标视图](media/metrics/metrics-4.png)

所有指标选项的详细信息请参阅以下[部分](#user-metrics-options)。

接下来，为每个指标选择聚合类型：

![指标聚合](media/metrics/metrics-5.png)

聚合类型指示如何按时间聚合图表中的指标点。 每分钟有一个原始指标点，并且几分钟内的预聚合类型由指标类型预定义。
* 总和：将所有值作为目标输出相加。
* 平均值：将期间中的平均值用作目标输出。
* 最大值/最小值：使用期间中的最大值/最小值作为目标输出。

也可以修改要显示的时间范围。 时间范围可以选择从过去 30 分钟到持续 30 天，也可以选择自定义时间范围。

![公制修改](media/metrics/metrics-6.png)

默认视图将 Azure Spring Cloud 服务应用程序的所有指标一起包含。 可以在显示屏中筛选一个应用或实例的指标。  单击 **"添加筛选器**"，将属性设置为**App**，并在 **"值**"文本框中选择要监视的目标应用程序。 

可以使用两种类型的筛选器（属性）：
* 应用：按应用名称筛选
* 实例：按应用实例筛选

![指标筛选器](media/metrics/metrics-7.png)

您还可以使用 **"应用拆分**"选项，该选项将为一个应用绘制多条线：

![公制拆分](media/metrics/metrics-8.png)

>[!TIP]
> 可以在指标页中构建自己的图表，然后将其固定到“仪表板”。**** 一开始，为图表命名。  接下来，选择“固定到右上角的仪表板”****。 现在可以在门户“仪表板”中检查应用程序了。****

## <a name="user-metrics-options"></a>用户指标选项

下表显示了可用的指标和详细信息。

### <a name="error"></a>错误
>[!div class="mx-tdCol2BreakAll"]
>| “属性” | 弹簧执行器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| Tomcat 全局错误 | tomcat.global.错误 | Count | 已处理的请求发生的错误数 |

### <a name="performance"></a>性能
>[!div class="mx-tdCol2BreakAll"]
>| “属性” | 弹簧执行器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>|系统 CPU 使用率百分比 | 系统.cpu.使用率 | 百分比 | 整个系统的最近 CPU 使用率。 此值在 [0.0，1.0] 间隔内加倍。 值 0.0 表示在最近的观察期间，所有 CPU 都处于空闲状态，而值 1.0 表示所有 CPU 在最近观察期间都在 100% 的时间内主动运行。|
>| 应用 CPU 使用率百分比 | 应用 CPU 使用率百分比 | 百分比 | Java 虚拟机进程最近的 CPU 使用率。 此值在 [0.0，1.0] 间隔内加倍。 值 0.0 表示在最近的观察期间，没有一个 CPU 运行来自 JVM 进程的线程，而值 1.0 表示在观察的最近一段时间内，所有 CPU 都在主动运行 JVM 100% 的线程。 JVM 的线程包括应用程序线程以及 JVM 内部线程。|
>| 分配的应用内存 | jvm.内存.已提交 | 字节 | 表示保证可供 JVM 使用的内存量。 JVM 可能会向系统释放内存，并且提交内存可能小于 init。 提交将始终大于或等于使用。 |
>| 已使用的应用内存 | jvm.内存.已用 | 字节 | 表示当前在字节中使用的内存量。 |
>| 最大应用内存 | jvm.内存.max | 字节 | 表示可用于内存管理的最大内存量。 如果定义最大值，则使用和提交的内存量将始终小于或等于最大值。 如果内存分配尝试增加已使用内存，即使使用<= 最大值仍然为 true（例如，当系统虚拟内存不足时），内存分配也可能失败，以便使用>提交的内存。 |
>| 最大可用的旧代数据大小 | jvm.gc.max.数据.大小 | 字节 | 自 Java 虚拟机启动以来，旧代内存池的峰值内存使用。 |
>| 旧代数据大小 | jvm.gc.live.data.size.大小 | 字节 | 完整 GC 后旧代内存池的大小。 |
>| 提升为旧代数据大小 | jvm.gc.内存.升级 | 字节 | GC 之前旧代内存池的大小正增长计数到 GC 之后。 |
>| 提升为新代数据大小 | jvm.gc.内存.已分配 | 字节 | 增加一个 GC 后年轻代内存池的大小，以在下一个 GC 之前增加。 |
>| GC 暂停计数 | jvm.gc.暂停（总数） | Count | 此 JMV 启动后的总 GC 计数，包括"年轻"和"旧 GC"。 |
>| GC 暂停总时间 | jvm.gc.暂停（总时间） | 毫秒 | 此 JMV 启动后消耗的总 GC 时间，包括"年轻"和"旧 GC"。 |

### <a name="request"></a>请求
>[!div class="mx-tdCol2BreakAll"]
>| “属性” | 弹簧执行器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| Tomcat 发送的总字节数 | tomcat.global.sent | 字节 | 发送的 Tomcat Web 服务器的数据量 |
>| Tomcat 收到的总字节数 | tomcat.global.收到 | 字节 | 收到的 Tomcat Web 服务器的数据量 |
>| 汤姆卡特请求总时间 | tomcat.global.请求（总时间） | 毫秒 | Tomcat Web 服务器处理请求的总时间 |
>| Tomcat 请求总数 | tomcat.global.请求（总数） | Count | 处理过的 Tomcat Web 服务器请求的总数 |
>| Tomcat 请求最大时间 | tomcat.global.request.max | 毫秒 | Tomcat Web 服务器处理请求的最长时间 |

### <a name="session"></a>会话
>[!div class="mx-tdCol2BreakAll"]
>| “属性” | 弹簧执行器指标名称 | 单位 | 详细信息 |
>|----|----|----|------------|
>| Tomcat 会话最大活动计数 | tomcat.sessions.active.max | Count | 同时处于活动状态的最大会话数 |
>| Tomcat 会话最大活动时间 | 托姆卡特.会话.alive.max | 毫秒 | 过期会话处于活动状态的最长时间（以秒为单位） |
>| Tomcat 创建的会话数 | tomcat.sessions.创建 | Count | 已创建的会话数 |
>| Tomcat 过期的会话数 | tomcat.会话.过期 | Count | 已过期的会话数 |
>| Tomcat 拒绝的会话数 | tomcat.sessions.拒绝 | Count | 由于达到的活动会话的最大数而未创建的会话数。 |

## <a name="see-also"></a>请参阅
* [Azure 指标资源管理器入门](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [使用诊断设置分析日志和指标](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>后续步骤
* [教程：使用警报和操作组监视春云资源](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure 春云的配额和服务计划](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

