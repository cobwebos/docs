---
title: 排查 Azure Application Insights 可用性测试问题 | Microsoft Docs
description: 排查 Azure Application Insights 中的 Web 测试问题。 当网站不可用或响应速度缓慢时接收警报。
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: ee64a8af35f938def94e369bdb400fed6e2798c0
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146600"
---
# <a name="troubleshooting"></a>疑难解答

本文将有助于排查使用可用性监视时可能出现的常见问题。

## <a name="ssltls-errors"></a>SSL/TLS 错误

|症状/错误消息| 可能的原因|
|--------|------|
|无法创建 SSL/TLS 安全通道  | SSL 版本。 仅支持 TLS 1.0、1.1 和 1.2。 **不支持 SSLv3。**
|TLSv1.2 记录层：警报（级别：严重，说明：错误记录 MAC）| 请查看 StackExchange 线程以了解[详细信息](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)。
|无法连接到 CDN（内容分发网络）的 URL | 这可能是由于 CDN 上的错误配置导致的 |  

### <a name="possible-workaround"></a>可能的解决方法

* 如果遇到问题的 URL 始终指向依赖资源，建议对 Web 测试禁用“分析从属请求”。

## <a name="test-fails-only-from-certain-locations"></a>测试仅在某些位置失败

|症状/错误消息| 可能的原因|
|----|---------|
|连接尝试失败，因为已连接方在一段时间后尚未做出正确的响应  | 某些位置的测试代理正被防火墙阻止。|
|    |正在通过（负载均衡器、异地流量管理器、Azure Express Route）重新路由某些 IP 地址。 
|    |如果使用的是 Azure ExpressRoute，则在[发生非对称路由](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)时，存在着数据包可能被丢弃的情况。|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>间歇性测试失败，出现违反协议错误

|症状/错误消息| 可能的原因| 可能的解决方法 |
|----|---------|-----|
|服务器提交了协议冲突。 节 = ResponseHeader 详细信息 = CR 必须后跟 LF | 检测到格式不正确的标头时，会发生这种情况。 具体说来，某些标头可能没有使用 CRLF 来指示行尾，这违反了 HTTP 规范。 Application Insights 强制实施此 HTTP 规范，但无法通过格式不正确的标头进行响应。| a. 请联系网站主机提供商/CDN 提供程序来修复有问题的服务器。 <br> b. 如果失败的请求是资源（例如，样式文件、图像、脚本），则可以考虑禁用依赖请求的分析。 请记住，如果您这样做，您将失去监视这些文件的可用性的能力。

> [!NOTE]
> 在 HTTP 标头验证比较宽松的浏览器上，URL 可能不会失败。 有关该问题的详细说明，请参阅此博客文章： http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>常见故障排除问题

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>站点看似正常，但我看见测试失败了？ 为何 Application Insights 会向我发出警报？

   * 你的测试是否启用了“分析从属请求”？ 这会导致严格检查脚本、图像等资源。这类故障在浏览器上可能不明显。 检查所有图像、脚本、样式表和页面加载的任何其他文件。 如果其中有任何一个失败，即使 HTML 主页正常加载，测试也会报告为失败。 若要使测试对此类资源故障不再敏感，只需在测试配置中取消选中“分析从属请求”即可。

   * 若要降低暂时性网络问题等各方面因素导致的干扰，请确保选中“测试故障时允许重试”配置。 也可从多个位置进行测试并对警报规则阈值进行相应的管理，防止在出现特定于位置的问题时引发不必要的警报。

   * 单击可用性体验中的任意红点或搜索资源管理器中的任意可用性故障，以查看我们报告失败的详细原因。 测试结果以及相关的服务器端遥测数据（如果启用）应该有助于了解测试失败的原因。 暂时性问题的常见原因是网络或连接问题。

   * 测试是否超时？ 我们在 2 分钟后中止测试。 如果你的 ping 或多步骤测试花费的时间超过 2 分钟，我们会将其报告为失败。 请考虑将测试分成多个可在较短持续时间内完成的测试。

   * 是所有位置都报告失败，还是只有部分位置报告失败？ 如果只有部分位置报告失败，则可能是由网络/CDN 问题引起的。 再次单击红点应该有助于了解该位置报告失败的原因。

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>在警报触发和/或解决时，我并未收到电子邮件？

检查经典警报配置，确认是否已直接列出你的电子邮件，或者你所在的通讯组列表是否配置为接收通知。 如果是，则检查通讯组列表配置，确认它可以接收外部电子邮件。 另外，检查邮件管理员是否有可能配置了任何可能导致此问题的策略。

### <a name="i-did-not-receive-the-webhook-notification"></a>我尚未收到 Webhook 通知？

检查以确保接收 Webhook 通知的应用程序可用并成功处理 Webhook 请求。 有关详细信息，请参阅[此文](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>间歇性测试失败，出现违反协议错误？

错误（“违反协议: CR 必须后跟 LF”）表明服务器（或依赖项）存在问题。 在响应中设置的标头格式错误时，会发生这种情况。 可能是负载均衡器或 CDN 引发的。 具体来说，某些标头可能没有使用 CRLF 来指示行尾，这违反了 HTTP 规范，因此无法通过 .NET WebRequest 级别的验证。 请检查响应，以找出可能违反规范的标头。

> [!NOTE]
> 在 HTTP 标头验证比较宽松的浏览器上，URL 可能不会失败。 有关该问题的详细说明，请参阅此博客文章： http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>看不到任何相关服务器端遥测数据，无法诊断测试失败？*

如果已为服务器端应用程序设置 Application Insights，则可能是因为[采样](../../azure-monitor/app/sampling.md)正在进行。 请选择其他可用性结果。

### <a name="can-i-call-code-from-my-web-test"></a>是否可从 Web 测试调用代码？

否。 测试步骤必须在 .webtest 文件中指定。 此外，不能调用其他 Web 测试或使用循环。 但是可以借助一些有用的插件。


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>“Web 测试”与“可用性测试”之间是否存在差异？

这两个术语可以互换引用。 可用性测试是更通用的术语，其中除了包含多步骤 Web 测试外，还包含单 URL ping 测试。

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>我希望在防火墙后面运行的内部服务器上使用可用性测试。

   有两个可能的解决方案：

   * 请将防火墙配置为允许从[我们的 Web 测试代理 IP 地址](../../azure-monitor/app/ip-addresses.md)发出的传入请求。
   * 编写自己的代码，定期测试内部服务器。 在防火墙后的测试服务器上以后台进程的方式运行该代码。 测试进程可以通过核心 SDK 包中的 [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API 将其结果发送到 Application Insights。 这要求测试服务器能够以传出访问的方式访问 Application Insights 引入终结点，但与允许传入请求相比，这种方式的安全风险要小得多。 结果将显示在 "可用性 web 测试" 边栏选项卡中，但通过门户创建的测试的体验会略有简化。 自定义可用性测试还会在分析、搜索和指标中显示为可用性结果。

### <a name="uploading-a-multi-step-web-test-fails"></a>上传多步骤 Web 测试失败

可能导致此问题的一些原因包括：
   * 存在 300 K 大小限制。
   * 不支持循环。
   * 不支持对其他 Web 测试的引用。
   * 不支持数据源。

### <a name="my-multi-step-test-doesnt-complete"></a>多步骤测试无法完成

存在每个测试 100 个请求的限制。 此外，如果运行时间超过两分钟，测试会停止。

### <a name="how-can-i-run-a-test-with-client-certificates"></a>如何使用客户端证书运行测试？

目前不支持。

## <a name="who-receives-the-classic-alert-notifications"></a>谁会收到（经典）警报通知？

本节仅适用于经典警报，并将帮助优化警报通知以确保只有预期的接收人能收到通知。 若要详细了解[经典警报](../platform/alerts-classic.overview.md)与新的警报体验之间的区别，请参阅[警报概述文章](../platform/alerts-overview.md)。 若要控制新的警报体验中的警报通知，请使用[操作组](../platform/action-groups.md)。

* 建议将经典警报通知用于特定接收人。

* 对于 Y 个位置中 X 个位置的失败相关警报，如已启用“批/组”复选框选项，会向具有管理员/共同管理员角色的用户发送相关通知。  实质上是_订阅_的_所有_管理员均会收到通知。

* 对于可用性指标警报，“批量/组”复选框选项（如果已启用）将发送给订阅中具有所有者、参与者或阅读者角色的用户。 实际上，可以访问包含 Application Insights 资源在内的订阅的所有用户均会收到通知。 

> [!NOTE]
> 如果当前使用“批/组”复选框选项并禁用它，则无法还原更改。

如果需要根据用户角色通知用户，请使用新的警报体验/近实时警报。 使用[操作组](../platform/action-groups.md)，可以为具有任何参与者/所有者/读者角色（未融合为单一选项）的用户配置电子邮件通知。

## <a name="next-steps"></a>后续步骤

* [多步骤 Web 测试](availability-multistep.md)
* [URL ping 测试](monitor-web-app-availability.md)
