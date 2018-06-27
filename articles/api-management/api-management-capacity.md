---
title: Azure API 管理实例的容量 | Microsoft Docs
description: 本文介绍什么是容量指标，以及在是否扩展 Azure API 管理实例方面如何做出明智的决策。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: 4983854a14a6efe9214692dc677dedeada73933b
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296794"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Azure API 管理实例的容量

**容量**是最重要的单一 [Azure Monitor 指标](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis)，在是否扩展 API 管理实例以容纳更多负载方面，它可以帮助做出明智的决策。 容量的构造比较复杂，并且会施加特定的行为。

本文介绍**容量**的定义及其行为。 其中介绍了如何在 Azure 门户中访问**容量**指标，并建议何时应考虑扩展或升级 API 管理实例。

## <a name="prerequisites"></a>先决条件

若要遵循本文中的步骤，必须准备好：

+ 一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 一个 APIM 实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="what-is-capacity"></a>什么是容量

![容量指标](./media/api-management-capacity/capacity-ingredients.png)

**容量**是 APIM 实例中负载的指标。 它反映资源用量（CPU、内存）和网络队列的长度。 CPU 和内存用量反映以下对象的资源消耗量：

+ APIM 服务，例如管理操作或请求处理，可能包括转发请求或运行策略
+ 选定的操作系统进程，包括在新连接上造成 SSL 握手开销的进程。

总**容量**是 API 管理实例的每个单元中容量自有值的平均值。

## <a name="capacity-metric-behavior"></a>容量指标行为

由于这种构造，在现实中，**容量**可能受到多种可变因素的影响，例如：

+ 连接模式（是根据请求创建新连接，还是重复使用现有连接）
+ 请求和响应的大小
+ 在每个 API 中配置的策略数，或发送请求的客户端数。

请求中的操作越复杂，**容量**消耗就越高。 例如，复杂转换策略消耗的 CPU 比简单的请求转发要高得多。 后端服务响应速度较慢也会增大消耗量。

> [!IMPORTANT]
> **容量**不是所处理请求数量的直接度量值。

![容量指标高峰](./media/api-management-capacity/capacity-spikes.png)

**容量**还可能会出现间歇性的高峰或大于零，即使未处理任何请求。 之所以发生这种情况，是因为存在系统或平台特定的操作。在决定是否缩放实例时，不应考虑这些因素。
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>使用 Azure 门户检查容量
  
![容量指标](./media/api-management-capacity/capacity-metric.png)  

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 APIM 实例。
2. 选择“指标(预览)”。
3. 在紫色部分，从可用指标中选择“容量”，并保留默认的“平均值”聚合。

    > [!TIP]
    > 为避免解释错误，**容量**指标始终按位置分解。

4. 在绿色部分选择“位置”，以便按维度拆分指标。
5. 从该部分的顶部栏中选择所需的时间范围。

    可以设置指标警报，以便在发生意外的情况时收到通知。 例如，当 APIM 实例超出预期峰值容量有 20 分钟以上时收到通知。

    >[!TIP]
    > 可以配置警报，以便在服务容量不足时知道这种状况；或者使用 Azure Monitor 自动缩放功能自动添加 Azure API 管理单元。 缩放操作可能需要大约 30 分钟，因此请相应地安排规则。  
    > 只允许缩放主要位置。

## <a name="use-capacity-for-scaling-decisions"></a>使用容量做出缩放决策

在是否扩展 API 管理实例以容纳更多负载方面，**容量**指标可以帮助做出决策。 请考虑：

+ 查看长期趋势和平均值。
+ 忽略往往与负载提高无关的突发高峰（请参阅“容量指标行为”部分中的说明）。
+ 当**容量**值长时间（例如 30 分钟）超过 60% 或 70% 时，请升级或扩展实例。 不同的值可能更适合服务或方案。

>[!TIP]  
> 如果能够事先估算流量，请在预期的工作负荷上测试 APIM 实例。 可在租户中逐渐增大请求负载，并监视哪个容量指标值对应于峰值负载。 遵循上一部分中的步骤，使用 Azure 门户了解在任意给定时间使用的容量。

## <a name="next-steps"></a>后续步骤

[如何扩展或升级 Azure API 管理服务实例](upgrade-and-scale.md)