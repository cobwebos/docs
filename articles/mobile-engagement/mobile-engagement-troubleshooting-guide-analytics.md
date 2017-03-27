---
title: "Azure Mobile Engagement 故障排除指南 - 分析"
description: "Azure Mobile Engagement 中的分析、监视、分段和仪表板问题故障排除"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c407e8f0beb110844ba2ae4ca1a065a122d0fdbd
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>分析、监视、分段和仪表板问题故障排除指南
下面是关于 Azure Mobile Engagement 如何收集有关应用程序、设备和用户的信息方面你可能会遇到的问题。

## <a name="missingdelayed-information"></a>缺少信息/信息延迟
### <a name="issue"></a>问题
* 信息在分析、分段或仪表板中显示时出现延迟。
* 监视中缺少信息。
* 分析、分段或仪表板中缺少信息。
* 点击分段限制。

### <a name="causes"></a>原因
* 你可以使用 Analytics API、Monitor API 和 Segments API 查看 UI 中缺少的任何数据是否通过 API 可见。
* 如果 Azure Mobile Engagement SDK 未正确集成到你的应用中，那么你将无法看到分析、分段、监视或仪表板中的信息。
* 分段一旦创建之后就无法再进行更改，仅可以进行“克隆”（复制）或“销毁”（删除）。 分段仅可以包含 10 个条件。
* 测试监视中缺少信息的最佳方式是设置一个测试设备，在测试设备上卸载和/或重新安装应用。
* 对于分析、分段或仪表板，信息每 24 小时会刷新一次。
* 即使分段是基于先前的信息，新分段中的信息只有在创建新分段之后 24 小时才会显示。
* 筛选 UI 中的分析数据将会显示此类型的所有示例，而不论你的应用是什么版本（例如，按名称筛选的“Crashes”将从你的应用的版本 1 和版本 2 显示）。
* 分析的时间段基于用户的设备设置中的日期，因此，手机日期未正确设置的用户可能会在错误的时间段中显示。
* 当你使用按钮“测试”推送时，不会记录任何服务器端数据，仅对于实际的推送活动，才会记录数据。

## <a name="cant-locate-items-in-ui"></a>无法在 UI 中找到项目
### <a name="issue"></a>问题
* 无法基于特定的内置或自定义应用信息标记条件创建分段。
* 在分析、监视或仪表板中找不到特定的内置或自定义应用信息标记条件。
* 无法在分析、监视、分段或仪表板中解释数据。

### <a name="causes"></a>原因
* 某些内置项目和应用信息标记仅可用作推送条件，而不能添加到分段中，也无法从分析、监视、分段或仪表板中看到。 
* 对于无法添加到分段的内置项目和应用信息标记，需要在每个活动中设置目标条件列表，以执行与基于分段的目标相同的功能。
* 有关更多帮助信息和操作方法信息，请参阅 Azure Mobile Engagement UI 的“分析”、“监视”、“分段”和“仪表板”部分中的上下文菜单。

## <a name="crash-troubleshooting"></a>崩溃故障排除
### <a name="issue"></a>问题
* 在分析、监视或仪表板中出现应用程序崩溃。

### <a name="causes"></a>原因
* 要解决在分析、监视或仪表板中看到的应用程序崩溃问题，请务必查看先前版本的 SDK 的已知问题发行说明。
* 为了进一步解决应用程序崩溃问题，请从安装了应用程序的测试设备执行一个事件，并在 Azure Mobile Engagement UI 的“监视 – 事件”部分中查找你的设备 ID。 然后，执行导致应用程序崩溃的事件，并在 Azure Mobile Engagement UI 的“监视 – 崩溃”部分中查找其他信息。 


