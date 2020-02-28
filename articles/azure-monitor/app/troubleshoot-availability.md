---
title: Azure 应用程序 Insights 可用性测试疑难解答 |Microsoft Docs
description: Azure 应用程序 Insights 中的 web 测试疑难解答。 当网站不可用或响应速度缓慢时接收警报。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: f135aa6c0a4a55f8a42fd858572cc811e25b27c5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671097"
---
# <a name="troubleshooting"></a>故障排除

本文将帮助你排查使用可用性监视时可能出现的常见问题。

## <a name="ssltls-errors"></a>SSL/TLS 错误

|症状/错误消息| 可能的原因|
|--------|------|
|无法创建 SSL/TLS 安全通道  | SSL 版本。 仅支持 TLS 1.0、1.1 和1.2。 **不支持 SSLv3。**
|Tlsv1.1 1.2 记录层：警报（级别：严重，说明：错误的记录 MAC）| 有关[详细信息](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)，请参阅 stackexchange.redis 线程。
|失败的 URL 是 CDN （内容分发网络） | 这可能是由于 CDN 上的配置错误引起的 |  

### <a name="possible-workaround"></a>可能的解决方法

* 如果遇到此问题的 Url 始终为依赖资源，则建议对该 web 测试禁用**分析从属请求**。

## <a name="test-fails-only-from-certain-locations"></a>测试仅在某些位置失败

|症状/错误消息| 可能的原因|
|----|---------|
|连接尝试失败，因为连接的参与方在一段时间后未正确响应  | 某些位置的测试代理被防火墙阻止。|
|    |通过（负载均衡器、异地流量管理器、Azure Express Route）发生某些 IP 地址的重新路由。 
|    |如果使用的是 Azure ExpressRoute，则在某些情况下，可以在[发生非对称路由](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)的情况下删除数据包。|

## <a name="test-failure-with-a-protocol-violation-error"></a>测试失败，出现协议冲突错误

|症状/错误消息| 可能的原因| 可能的解决方法 |
|----|---------|-----|
|服务器提交了协议冲突。 节 = ResponseHeader 详细信息 = CR 必须后跟 LF | 如果检测到格式错误的标头，则会发生这种情况 具体说来，某些标头可能没有使用 CRLF 来指示行尾，这违反了 HTTP 规范。 Application Insights 强制实施此 HTTP 规范，但无法通过格式不正确的标头进行响应。| a. 请联系网站主机提供商/CDN 提供程序来修复有问题的服务器。 <br> b. 如果失败的请求是资源（例如，样式文件、图像、脚本），则可以考虑禁用依赖请求的分析。 请记住，如果您这样做，您将失去监视这些文件的可用性的能力。

> [!NOTE]
> 在 HTTP 标头验证比较宽松的浏览器上，URL 可能不会失败。 有关该问题的详细说明，请参阅此博客文章： http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>常见的故障排除问题

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>网站看起来正常，但看到测试失败了吗？ 为什么 Application Insights 通知我？

   * 你的测试是否已启用**分析依赖请求**？ 这样就会严格检查有关脚本、图像等资源的结果。在浏览器中，这些类型的故障可能不明显。 检查所有图像、脚本、样式表和页面加载的任何其他文件。 如果其中任何一个失败，则即使主 HTML 页面加载时未出现问题，也会将测试报告为失败。 若要使此类资源故障，只需取消选中 "从测试配置分析从属请求"。

   * 若要减少暂时性网络信号等的干扰，请确保选中 "为测试失败启用重试" 配置。 也可从多个位置进行测试并对警报规则阈值进行相应的管理，防止在出现特定于位置的问题时引发不必要的警报。

   * 单击可用性体验中的任意红点或搜索资源管理器中的任意可用性故障，以查看我们报告失败的详细原因。 测试结果以及相关的服务器端遥测数据（如果启用）应该有助于了解测试失败的原因。 暂时性问题的常见原因是网络或连接问题。

   * 测试是否超时？ 我们在 2 分钟后中止测试。 如果你的 ping 或多步骤测试花费的时间超过 2 分钟，我们会将其报告为失败。 请考虑将测试分成多个可在较短持续时间内完成的测试。

   * 是所有位置都报告失败，还是只有部分位置报告失败？ 如果只有部分位置报告失败，则可能是由网络/CDN 问题引起的。 再次单击红点应该有助于了解该位置报告失败的原因。

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>警报触发时未收到电子邮件，或解决了这两种情况？

检查经典警报配置，确认是否已直接列出你的电子邮件，或者你所在的通讯组列表是否配置为接收通知。 如果是，则检查通讯组列表配置，确认它可以接收外部电子邮件。 另外，检查邮件管理员是否有可能配置了任何可能导致此问题的策略。

### <a name="i-did-not-receive-the-webhook-notification"></a>我未收到 webhook 通知？

检查以确保接收 Webhook 通知的应用程序可用并成功处理 Webhook 请求。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>间歇性测试失败，出现协议冲突错误？

错误（“违反协议: CR 必须后跟 LF”）表明服务器（或依赖项）存在问题。 在响应中设置的标头格式错误时，会发生这种情况。 可能是负载均衡器或 CDN 引发的。 具体说来，某些标头可能没有使用 CRLF 来指示行尾，这违反了 HTTP 规范，因此在 .NET WebRequest 级别的验证失败。 检查可能违反的标头的响应。

> [!NOTE]
> 在 HTTP 标头验证比较宽松的浏览器上，URL 可能不会失败。 有关该问题的详细说明，请参阅此博客文章： http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>我看不到任何相关服务器端遥测来诊断测试失败问题？ *

如果已为服务器端应用程序设置 Application Insights，则可能是因为[采样](../../azure-monitor/app/sampling.md)正在进行。 请选择其他可用性结果。

### <a name="can-i-call-code-from-my-web-test"></a>能否从 web 测试调用代码？

不是。 测试步骤必须在 .webtest 文件中指定。 此外，不能调用其他 Web 测试或使用循环。 但是可以借助一些有用的插件。


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>"Web 测试" 和 "可用性测试" 之间是否存在差异？

这两个术语可以互换引用。 可用性测试是更通用的术语，其中除了包含多步骤 Web 测试外，还包含单 URL ping 测试。

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>我想在防火墙后面运行的内部服务器上使用可用性测试。

   下面是两种可能的解决方案：

   * 请将防火墙配置为允许从[我们的 Web 测试代理 IP 地址](../../azure-monitor/app/ip-addresses.md)发出的传入请求。
   * 编写自己的代码，定期测试内部服务器。 在防火墙后的测试服务器上以后台进程的方式运行该代码。 测试进程可以通过核心 SDK 包中的 [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API 将其结果发送到 Application Insights。 这要求测试服务器能够以传出访问的方式访问 Application Insights 引入终结点，但与允许传入请求相比，这种方式的安全风险要小得多。 结果将显示在 "可用性 web 测试" 边栏选项卡中，但通过门户创建的测试的体验会略有简化。 自定义可用性测试还会在分析、搜索和指标中显示为可用性结果。

### <a name="uploading-a-multi-step-web-test-fails"></a>上传多步骤 web 测试失败

可能导致此问题的一些原因包括：
   * 存在 300 K 大小限制。
   * 不支持循环。
   * 不支持对其他 Web 测试的引用。
   * 不支持数据源。

### <a name="my-multi-step-test-doesnt-complete"></a>我的多步骤测试未完成

存在每个测试 100 个请求的限制。 此外，如果运行时间超过两分钟，测试会停止。

### <a name="how-can-i-run-a-test-with-client-certificates"></a>如何使用客户端证书运行测试？

目前不支持。

## <a name="who-receives-the-classic-alert-notifications"></a>谁会收到（经典）警报通知？

本节仅适用于经典警报，并将帮助优化警报通知以确保只有预期的接收人能收到通知。 若要详细了解[经典警报](../platform/alerts-classic.overview.md)与新的警报体验之间的区别，请参阅[警报概述文章](../platform/alerts-overview.md)。 若要控制新的警报体验中的警报通知，请使用[操作组](../platform/action-groups.md)。

* 建议将经典警报通知用于特定接收人。

* 对于 Y 个位置中 X 个位置的失败相关警报，如已启用“批/组”复选框选项，会向具有管理员/共同管理员角色的用户发送相关通知。  实质上是_订阅_的_所有_管理员均会收到通知。

* 对于 "可用性指标警报" "**批量/组**" 复选框选项如果已启用，则向用户发送订阅中的所有者、参与者或读取者角色。 实际上，可以访问包含 Application Insights 资源在内的订阅的所有用户均会收到通知。 

> [!NOTE]
> 如果当前使用“批/组”复选框选项并禁用它，则无法还原更改。

如果需要根据用户角色通知用户，请使用新的警报体验/近实时警报。 使用[操作组](../platform/action-groups.md)，可以为具有任何参与者/所有者/读者角色（未融合为单一选项）的用户配置电子邮件通知。

## <a name="next-steps"></a>后续步骤

* [多步骤 web 测试](availability-multistep.md)
* [URL ping 测试](monitor-web-app-availability.md)
