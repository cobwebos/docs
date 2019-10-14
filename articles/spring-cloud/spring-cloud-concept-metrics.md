---
title: 了解 Azure Spring Cloud 的指标
description: 了解如何查看 Azure Spring Cloud 中的指标
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038496"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure Spring Cloud 的指标

Azure Monitor 指标资源管理器是 Microsoft Azure 门户的一个组件，通过它可以绘制图表、以直观的形式关联趋势及研究指标的上升和下降。 使用指标资源管理器调查资源的运行状况和利用率。 在 Azure Spring Cloud 中，我们提供两个选项来查看指标：一个是“应用程序概览”页中的图表，另一个是服务级别“指标”页。 

## <a name="application-overview-page"></a>“应用程序概览”页

每个应用程序的“应用程序概览”页都会提供一个指标图表，用于对应用程序进行快速状态检查。   转到 Azure Spring Cloud 服务页，选择“应用程序仪表板”，然后从列表中选择应用程序。   

我们提供 5 个图表，每分钟更新以下内容的指标：

* **Http 服务器错误**：向应用发出的 HTTP 请求的错误计数。
* **数据输入**：应用接收的字节数。
* **数据输出**：发送到应用的字节数。
* **请求**：应用接收的请求数。
* **平均响应时间**：应用的平均响应时间。

可以为图表选择一个时间范围（1 小时到 7 天）。

## <a name="service-level-metric-queries"></a>服务级别指标查询

Azure Spring Cloud 用于监视各种应用程序指标。 查看 Azure Monitor 指标[入门指南](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)，详细了解该服务。

若要查看指标数据，需选择指标、**聚合**和时间范围。  这些概念的解释如下所示。

### <a name="aggregation"></a>聚合 

Azure 每分钟都会轮询和更新指标。 Azure 提供三种方式来聚合所选时段的数据：

* **Total**：将所有指标作为目标输出求和。
* **Average**：将时段中的“平均”值用作目标输出。
* **最大/最小**：将时段中的“最大/最小”值用作目标输出。

### <a name="time-range"></a>时间范围

选择默认的时间范围，或者定义你自己的时间范围。

### <a name="modifying-the-granularity-of-your-metric-query"></a>修改指标查询的粒度

默认情况下，Azure 聚合 Azure Spring Cloud 服务的所有应用程序的指标。 若要查看应用程序或实例级别的指标，请使用筛选器功能。  
选择“添加筛选器”，将属性设置为“应用”，然后选择要监视的目标应用程序  。  也可选择“应用拆分”选项，为图表中的每个应用绘制单独的折线图  。

>[!TIP]
> 可以在指标页中构建自己的图表，然后将其固定到“仪表板”。  一开始，为图表命名。  接下来，选择“固定到右上角的仪表板”  。 现在可以在门户“仪表板”中检查应用程序了。 