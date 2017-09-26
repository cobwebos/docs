---
title: "Azure CDN 中的实时统计信息 | Microsoft Docs"
description: "在将内容传送到客户端时，实时统计信息将提供有关 Azure CDN 性能的实时数据。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.contentlocale: zh-cn
ms.lasthandoff: 01/25/2017

---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Microsoft Azure CDN 中的实时统计信息
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概述
本文档介绍 Microsoft Azure CDN 中的实时统计信息。  在将内容传送到客户端时，此功能将提供实时数据（如带宽、缓存状态和并发连接数）给你的 CDN 配置文件。 这样就可以随时连续监视服务的运行状况，包括上线事件。

以下是可用图表：

* [带宽](#bandwidth)
* [状态代码](#status-codes)
* [缓存状态](#cache-statuses)
* [连接](#connections)

## <a name="accessing-real-time-stats"></a>访问实时统计信息
1. 在 [Azure 门户](https://portal.azure.com)中，导航到 CDN 配置文件。
   
    ![CDN 配置文件边栏选项卡](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    随即 CDN 管理门户打开。
3. 将鼠标悬停在“**分析**”选项卡，然后悬停在“**实时统计信息**”浮出控件。  单击“**HTTP 大型对象**”。
   
    ![CDN 管理门户](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    随即显示实时统计信息图表。

每个图表都会显示所选时间范围内的统计信息，并在页面加载时启动。  图表每隔几秒会自动更新。  “**刷新图表**”按钮（如果有）将会清除图表，此后它将仅显示所选的数据。

## <a name="bandwidth"></a>带宽
![带宽图表](./media/cdn-real-time-stats/cdn-bandwidth.png)

“**带宽**”图表显示所选时间范围内的、针对当前平台所使用的带宽量。 图形的阴影部分表示带宽使用情况。 当前正在使用的准确带宽量直接显示在折线图下方。

## <a name="status-codes"></a>状态代码
![状态代码图表](./media/cdn-real-time-stats/cdn-status-codes.png)

“**状态代码**”图表指明特定 HTTP 响应代码在所选时间范围内的出现频率。

> [!TIP]
> 有关每个 HTTP 状态代码选项的说明，请参阅 [Azure CDN HTTP 状态代码](https://msdn.microsoft.com/library/mt759238.aspx)。
> 
> 

HTTP 状态代码列表直接显示在图表上方。 此列表指明可纳入线形图的每个状态代码，以及该状态代码当前每秒出现的次数。 默认情况下，每个状态代码在图中显示为一条线。 但是，你可以选择只监视对 CDN 配置有特殊意义的状态代码。 若要执行此操作，请检查所需的状态代码并清除所有其他选项，然后单击“**刷新图表**”。 

你可以暂时隐藏某个特定状态代码的记录数据。  从图表正下方的图例中，单击想要隐藏的状态代码。 状态代码将立即从图表中隐藏。 再次单击该状态代码将重新显示该选项。

## <a name="cache-statuses"></a>缓存状态
![缓存状态图表](./media/cdn-real-time-stats/cdn-cache-status.png)

“**缓存状态**”图表指明特定类型的缓存状态在所选时间范围内的出现频率。 

> [!TIP]
> 有关每个缓存状态代码选项的说明，请参阅 [Azure CDN 缓存状态代码](https://msdn.microsoft.com/library/mt759237.aspx)。
> 
> 

缓存状态代码列表直接显示在图表上方。 此列表指明可纳入线形图的每个状态代码，以及该状态代码当前每秒出现的次数。 默认情况下，每个状态代码在图中显示为一条线。 但是，你可以选择只监视对 CDN 配置有特殊意义的状态代码。 若要执行此操作，请检查所需的状态代码并清除所有其他选项，然后单击“**刷新图表**”。 

你可以暂时隐藏某个特定状态代码的记录数据。  从图表正下方的图例中，单击想要隐藏的状态代码。 状态代码将立即从图表中隐藏。 再次单击该状态代码将重新显示该选项。

## <a name="connections"></a>连接
![连接数图表](./media/cdn-real-time-stats/cdn-connections.png)

此图表显示与边缘服务器建立了多少个连接。 每个通过 CDN 传递的资产请求都会产生一个连接。

## <a name="next-steps"></a>后续步骤
* 通过 [Azure CDN 中的实时警报](cdn-real-time-alerts.md)获取通知
* 更深入地挖掘[高级 HTTP 报表](cdn-advanced-http-reports.md)
* 分析[使用模式](cdn-analyze-usage-patterns.md)


