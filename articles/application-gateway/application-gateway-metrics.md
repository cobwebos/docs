---
title: 应用程序网关 Azure Monitor 度量值
description: 了解如何使用指标来监视应用程序网关的性能
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 12759deb3e1775b5170d40cc609fe8c6226bf0d6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704572"
---
# <a name="metrics-for-application-gateway"></a>应用程序网关的指标

应用程序网关将称为 "指标" 的数据点发布到[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) ，以便为应用程序网关和后端实例提供性能。 这些度量值是一组有序的时间序列数据中的数值，用于在特定时间描述应用程序网关的某些方面。 如果有请求通过应用程序网关流动，则会按60秒的间隔测量并发送其指标。 如果没有请求通过应用程序网关流动或没有指标的数据，则不报告指标。 有关详细信息，请参阅 [Azure Monitor 指标](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>应用程序网关 V2 SKU 支持的指标

### <a name="timing-metrics"></a>计时指标

以下与请求和响应的计时相关的指标可用。 通过为特定侦听器分析这些度量值，你可以确定应用程序在中的应用程序是否受 WAN、应用程序网关、应用程序网关与后端应用程序之间的网络或后端应用程序性能的下降。

> [!NOTE]
>
> 如果应用程序网关上有多个侦听器，则在比较不同滞后时间度量值时始终按*侦听器*维度进行筛选以获取有意义的推理。

- **后端连接时间**

  与后端应用程序建立连接所用的时间。 这包括网络延迟以及后端服务器的 TCP 堆栈建立新连接所用的时间。 对于 SSL，它还包括在握手中花费的时间。 

- **后端首字节响应时间**

  与后端服务器建立连接和接收响应标头的第一个字节之间的时间间隔。 这近似于*后端连接时间*和后端应用程序的响应时间（服务器生成内容所用的时间，可能会提取数据库查询，并开始将响应传输回应用程序网关）

- **后端最后字节响应时间**

  与后端服务器建立连接和接收响应正文的最后一个字节之间的时间间隔。 这近似于*后端首字节响应时间*和数据传输时间之和（此数量可能会因所请求对象的大小和服务器网络的延迟而大不相同）

- **应用程序网关总时间**

  处理请求和发送响应所用的平均时间。 这是从应用程序网关接收到 HTTP 请求的第一个字节到响应发送操作完成的时间的平均间隔计算得出的，这接近于应用程序网关的处理时间和*后端最后字节响应时间*的总和

- **客户端 RTT**

  客户端和应用程序网关之间的平均往返时间。 此指标表示建立连接和返回确认所用的时间。 

这些指标可用于确定观察到的缓慢是否是由应用程序网关、网络和后端服务器 TCP 堆栈饱和、后端应用程序性能或大文件大小导致的。
例如，如果后端的第一个字节响应时间有峰值，但后端连接时间是常量，则可以推断出应用程序网关到后端延迟以及建立连接所用的时间是稳定的，并且由于后端应用程序的响应时间增加。 同样，如果后端第一个字节响应时间中的峰值与后端连接时间中的相应高峰相关联，则可以推导出网络或服务器 TCP 堆栈已饱和。 如果你注意到后端的最后字节响应时间峰值，但后端第一个字节响应时间是常量，则大多数情况下，这可能是由于需要更大的文件而造成的。 同样，如果应用程序网关总时间比后端最后字节响应时间多很多，则它可以是应用程序网关上的性能瓶颈。



### <a name="application-gateway-metrics"></a>应用程序网关指标

应用程序网关支持以下指标：

- **接收的字节数**

   应用程序网关从客户端接收的字节数

- **发送的字节数**

   应用程序网关发送到客户端的字节计数

- **客户端 TLS 协议**

   与应用程序网关建立连接的客户端启动的 TLS 和非 TLS 请求计数。 若要查看 TLS 协议分发，请按维度 TLS 协议进行筛选。

- **当前容量单位**

   使用的容量单位计数。 容量单位用于衡量除固定成本以外，还需支付的费用。 容量单元计算单元、持续连接和吞吐量有三个 qps。 每个容量单位的最大数目为：1个计算单元、2500持续连接或 2.22-Mbps 吞吐量。

- **当前计算单元**

   使用的处理器容量计数。 影响计算单元的因素包括 TLS 连接数/秒、URL 重写计算和 WAF 规则处理。 

- **当前连接数**

   使用应用程序网关建立的当前连接计数

- **失败的请求数**

   应用程序网关已提供的失败请求计数。 可以进一步筛选请求计数，以显示每个/特定的后端池的计数-http 设置组合。


- **响应状态**

   应用程序网关返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。

- **吞吐量**

   应用程序网关每秒提供的字节数

- **请求总数**

   应用程序网关已提供的成功请求计数。 可以进一步筛选请求计数，以显示每个/特定的后端池的计数-http 设置组合。

- **Web 应用程序防火墙匹配规则**

- **Web 应用程序防火墙触发的规则**

### <a name="backend-metrics"></a>后端指标

应用程序网关支持以下指标：

- **后端响应状态**

  后端返回的 HTTP 响应状态代码的计数。 这不包括任何由应用程序网关生成的响应代码。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。

- **正常主机计数**

  运行状况探测确定正常的后端的数目。 可以按每个后端池进行筛选来显示特定后端池中正常的/不正常的主机数。

- **不正常的主机计数**

  运行状况探测确定不正常的后端的数目。 你可以根据每个后端池进行筛选，以显示特定后端池中不正常的主机。

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>应用程序网关 V1 SKU 支持的指标

### <a name="application-gateway-metrics"></a>应用程序网关指标

应用程序网关支持以下指标：

- **CPU 使用率**

  显示分配给应用程序网关的 Cpu 的使用率。  正常情况下，CPU 使用率不应定期超过90%，因为这可能导致在应用程序网关后托管的网站中出现延迟，并破坏客户端体验。 可以通过增加实例计数或通过移动到更大的 SKU 大小或同时执行这两种操作，来间接控制或提高 CPU 利用率。

- **当前连接数**

  使用应用程序网关建立的当前连接计数

- **失败的请求数**

  应用程序网关已提供的失败请求计数。 可以进一步筛选请求计数，以显示每个/特定的后端池的计数-http 设置组合。

- **响应状态**

  应用程序网关返回的 HTTP 响应状态。 可以进一步对响应状态代码分布进行归类来显示 2xx、3xx、4xx 和 5xx 类别的响应。

- **吞吐量**

  应用程序网关每秒提供的字节数

- **请求总数**

  应用程序网关已提供的成功请求计数。 可以进一步筛选请求计数，以显示每个/特定的后端池的计数-http 设置组合。

- **Web 应用程序防火墙匹配规则**

- **Web 应用程序防火墙触发的规则**

### <a name="backend-metrics"></a>后端指标

应用程序网关支持以下指标：

- **正常主机计数**

  运行状况探测确定正常的后端的数目。 可以按每个后端池进行筛选来显示特定后端池中正常的/不正常的主机数。

- **不正常的主机计数**

  运行状况探测确定不正常的后端的数目。 你可以根据每个后端池进行筛选，以显示特定后端池中不正常的主机。

## <a name="metrics-visualization"></a>指标可视化

浏览到应用程序网关，在 "**监视**" 选择**指标**。 若要查看可用值，请选择“指标”下拉列表。

在下图中可以看到过去 30 分钟显示的三个指标的示例：

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

若要查看当前的指标列表，请参阅 [Azure Monitor 支持的指标](../azure-monitor/platform/metrics-supported.md)。

### <a name="alert-rules-on-metrics"></a>有关指标的警报规则

可基于资源的指标启动警报规则。 例如，如果应用程序网关的吞吐量在指定时间段内高于、低于或等于阈值，警报即可调用 webhook 或给管理员发送电子邮件。

以下示例指导创建警报规则，以在吞吐量违反阈值时给管理员发送电子邮件：

1. 选择 "**添加指标警报**" 以打开 "**添加规则**" 页。 你还可以从 "指标" 页访问此页。

   ![“添加指标警报”按钮][6]

2. 在 "**添加规则**" 页上，填写 "名称"、"条件" 和 "通知" 部分，然后选择 **"确定"** 。

   * 在“条件”选择器中，选择以下 4 个值之一：“大于”、“大于或等于”、“小于”或“小于或等于”。

   * 在“时间段”选择器中，选择 5 分钟到 6 小时之间的一个时间段。

   * 如果选择“电子邮件所有者、参与者和读者”，则电子邮件将基于有权访问该资源的用户动态发送。 否则，可以在“其他管理员电子邮件”框中提供用户名单并以逗号分隔。

   ![添加规则页][7]

如果违反阈值，用户将收到类似下图内容的电子邮件：

![违反阈值电子邮件][8]

创建指标警报后，会显示警报列表。 它提供所有警报规则概述。

![警报和规则列表][9]

若要了解有关警报通知的详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。

若要深入了解 webhook 以及如何将其与警报搭配使用，请参阅[针对 Azure 指标警报配置 webhook](../azure-monitor/platform/alerts-webhooks.md)。

## <a name="next-steps"></a>后续步骤

* 使用 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)可视化计数器和事件日志。
* [Visualize your Azure activity log with Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)（使用 Power BI 可视化 Azure 活动日志）博客文章。
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)（在 Power BI 和其他组件中查看和分析 Azure 活动日志）博客文章。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
