---
title: 用户分析工具疑难解答-Azure 应用程序 Insights
description: 故障排除指南 - 通过 Application Insights 分析站点和应用使用情况。
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670910"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Application Insights 中用户行为分析工具的故障排除
是否对 [Application Insights 中的用户行为分析工具](usage-overview.md)：[用户、会话、事件](usage-segmentation.md)、[漏斗图](usage-funnels.md)、[用户流](usage-flows.md)、[保留期](usage-retention.md)或队列存在疑问？ 请参考下面的解答。

## <a name="counting-users"></a>对用户进行计数
**用户行为分析工具显示我的应用程序有一个用户/会话，但我知道我的应用程序有许多用户/会话。如何修复这些错误的计数？**

Application Insights 中的所有遥测事件都具有[匿名用户 ID](../../azure-monitor/app/data-model-context.md) 和[会话 ID](../../azure-monitor/app/data-model-context.md)，作为这些事件的两个标准属性。 默认情况下，所有使用情况分析工具都基于这些 ID 对用户和会话进行计数。 如果未使用应用中每个用户和会话的唯一 ID 填充这些标准属性，则使用情况分析工具中会显示错误的用户和会话计数。

如果正在监视 Web 应用，则最简单的解决方案是向应用添加 [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md)，并确保已在想要监视的每一页上加载脚本代码片段。 JavaScript SDK 自动生成匿名用户和会话 ID，然后在从应用发送这些 ID 后使用这些 ID 填充遥测事件。

如果正在监视 Web 服务（无用户界面），根据服务的唯一用户和会话概念，[创建遥测初始值设定项用于填充匿名用户 ID 和会话 ID 属性](usage-send-user-context.md)。

如果应用正在发送[已经过身份验证的用户 ID](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)，则可以在“用户”工具中基于经过身份验证的用户 ID 进行计数。 在“显示”下拉菜单中，选择“已经过身份验证的用户”。

用户行为分析工具当前不支持基于匿名用户 ID、已经过身份验证的用户 ID 或会话 ID 之外的属性对用户或会话进行计数。

## <a name="naming-events"></a>命名事件
**我的应用程序具有数千个不同的页面视图和自定义事件名称。很难区分它们，用户行为分析工具通常不会响应。如何解决这些命名问题？**

用户行为分析工具中会频繁使用页面视图和自定义事件名称。 若要从这些工具中获取值，为事件提供合适的名称至关重要。 目标是在具有太少的、过于通用的名称（"单击按钮"）和过多的、太具体的名称（"http：\//www.contoso.com/index" 上单击 "编辑按钮"）之间的平衡。

若要对应用发送的页面视图和自定义事件名称进行更改，需要更改应用的源代码并重新部署。 Application Insights 中的所有遥测数据均存储 90 天，且无法删除，因此对事件名称所做的更改需要 90 天才能完全生效。 名称更改后的 90 天中，新旧事件名称都会显示在遥测中，因此请相应调整查询并在团队内进行相应传达。

如果应用发送过多页面视图名称，检查这些页面视图名称是在代码中手动指定的还是由 Application Insights JavaScript SDK 自动发送的：

* 如果页面视图名称是使用 [`trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) 在代码中手动指定的，将名称更改为不太具体的名称。 避免常见错误，如将 URL 放在页面视图的名称中。 而是应使用 `trackPageView` API 的 URL 参数。 将其他详细信息从页面视图名称移动到自定义属性中。

* 如果是由 Application Insights JavaScript SDK 自动发送页面视图名称，则可以更改页标题或切换为手动发送页面视图名称。 默认情况下，SDK 发送每页的[标题](https://developer.mozilla.org/docs/Web/HTML/Element/title)作为页面视图名称。 可将标题更改为更加常规的标题，但请留意 SEO 和此更改可能产生的其他影响。 通过 `trackPageView` API 手动指定的页面视图名称将替代自动收集的名称，因此可在遥测中发送更宽泛的名称，而无需更改页标题。   

如果应用发送过多自定义事件名称，将代码中的名称更改为不太具体的名称。 同样，避免将 URL 和其他每页或动态信息直接放入自定义事件名称中。 而是应使用 `trackEvent` API 将这些详细信息移动到自定义事件的自定义属性中。 例如，建议使用 `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`，而不是 `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`。

## <a name="next-steps"></a>后续步骤

* [用户行为分析工具概述](usage-overview.md)

## <a name="get-help"></a>获取帮助
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

