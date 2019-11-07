---
title: 了解 Azure 春季云的指标
description: 了解如何在 Azure 春季云中查看指标
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607812"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure 春季云指标

Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，它允许绘制图表、直观关联趋势，并调查指标中的峰值和 dip。 使用指标资源管理器调查资源的运行状况和利用率。 在 Azure 春季云中，我们提供两个选项用于在 "**应用程序概述**" 页和 "服务级别指标" 页中查看指标：图表。

## <a name="application-overview-page"></a>应用程序概述页

每个应用程序的**应用程序概述**页都提供了一个指标图表，可用于对应用程序进行快速的状态检查。  请在 Azure 春季云服务页上，选择 "**应用程序仪表板**"，然后从列表中选择应用程序。  

对于以下各项，我们提供每分钟更新一次指标的5个图表：

* **Http 服务器错误**：对应用程序的 http 请求的错误计数。
* **数据**：应用接收的字节数。
* **输出数据**：发送到应用的字节数。
* **请求**：应用收到的请求数。
* **平均响应时间**：应用程序的平均响应时间。

您可以为图表选择一个时间范围，时间范围介于1小时到7天之间。

## <a name="service-level-metric-queries"></a>服务级别指标查询

使用 Azure 春季 Cloud，可以监视各种应用程序指标。 查看 Azure Monitor 指标入门[指南](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)，了解有关此服务的详细信息。

若要查看指标数据，请选择度量值、**聚合**和时间范围。  下面介绍了这些概念。

### <a name="aggregation"></a>聚合 

Azure 每分钟轮询并更新指标。 Azure 提供了三种方法来聚合所选时间段内的数据：

* **Total**：汇总所有指标作为目标输出。
* **Average**：使用期间中的平均值作为目标输出。
* **Max/min**：使用时间段中的最大值/最小值作为目标输出。

### <a name="time-range"></a>时间范围

选择一个默认的时间范围或定义自己的时间范围。

### <a name="modifying-the-granularity-of-your-metric-query"></a>修改指标查询的粒度

默认情况下，Azure 会聚合所有 Azure 春季云服务应用程序的指标。 若要查看应用程序或实例级别的指标，请使用筛选器函数。  
选择 "**添加筛选器**"，将属性设置为 "**应用**"，然后选择要监视的目标应用程序。 （可选）使用 "**应用拆分**" 选项为图表中的每个应用绘制单独的行。

>[!TIP]
> 可以在 "指标" 页中构建自己的图表，并将其固定到**仪表板**。 首先命名你的图表。  接下来，**在右上角选择 "固定到仪表板"** 。 你现在可以在门户**仪表板**上检查你的应用程序。