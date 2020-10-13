---
title: Azure Application Insights 中的智能检测 | Microsoft Docs
description: Application Insights 执行应用遥测的自动深入分析，并在有潜在问题时发出警告。
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 24ed8eefb7e07930cbd123fbe4081d555f0c27c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87305867"
---
# <a name="smart-detection-in-application-insights"></a>Application Insights 中的智能检测
 当 Web 应用程序中存在潜在性能问题和故障异常时，智能检测会自动向你发出警告。 它会对应用发送至 [Application Insights](./app-insights-overview.md) 的遥测数据执行主动分析。 如果失败率中存在骤升或者客户端或服务器性能中存在异常模式，将收到警报。 此功能不需要任何配置。 它会在应用程序发送足够的遥测时运行。

可以从接收的电子邮件和“智能检测”边栏选项卡中访问智能检测发出的检测信息。

## <a name="review-your-smart-detections"></a>查看智能检测
可通过以下两种方式发现检测：

* **收到来自 Application Insights 的电子邮件**。 下面是一个典型示例：
  
    ![电子邮件警报](./media/proactive-diagnostics/03.png)
  
    单击大按钮以在门户中打开更多详细信息。
* Application Insights 中的“智能检测”边栏选项卡。 选择“调查”菜单下的“智能检测”以查看最近的检测列表 。

![查看最近的检测](./media/proactive-diagnostics/04.png)

选择检测以查看其详细信息。

## <a name="what-problems-are-detected"></a>检测到哪些问题？
智能检测可检测并通知各种问题，例如：

* [智能检测 - 失败异常](./proactive-failure-diagnostics.md)。 我们使用机器学习，通过与负载和其他因素相关为应用设置预期的失败请求速率。 如果失败率超出预期的包络线，我们将发送警报。
* [智能检测 - 性能异常](./proactive-performance-diagnostics.md)。 如果操作的响应时间或依赖关系的持续时间比历史基线慢，或者如果我们在响应时间或页面加载时间中识别到异常模式，会收到通知。   
* 一般降级和问题，如[跟踪降级](./proactive-trace-severity.md)、[内存泄漏](./proactive-potential-memory-leak.md)、[异常量增加](./proactive-exception-volume.md)和[安全反模式](./proactive-application-security-detection-pack.md)。

（每个通知中的帮助链接可转到相关文章。）

## <a name="smart-detection-email-notifications"></a>智能检测电子邮件通知

除了标为预览版的规则以外，所有智能检测规则都默认配置为在发现检测时发送电子邮件通知。

可以通过打开智能检测“设置”边栏选项卡并选择规则（这将打开“编辑规则”边栏选项卡）来为特定的智能检测规则配置电子邮件通知。

另外，还可以使用 Azure 资源管理器模板来更改配置。 有关更多详细信息，请参阅[使用 Azure 资源管理器模板管理 Application Insights 智能检测规则](./proactive-arm-config.md)。

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助检查应用中的遥测数据：

* [指标资源管理器](../platform/metrics-charts.md)
* [搜索资源管理器](./diagnostic-search.md)
* [分析 - 功能强大的查询语言](../log-query/get-started-portal.md)

智能检测是完全自动执行的。 但是或许你想要设置更多的警报？

* [手动配置的指标警报](../platform/alerts-log.md)
* [可用性 Web 测试](./monitor-web-app-availability.md) 

