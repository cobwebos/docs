---
title: Azure Cosmos DB 的自动性能、成本、安全建议
description: 了解如何根据工作负荷模式查看针对 Azure Cosmos DB 的自定义性能、成本、安全性和其他建议。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450083"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Azure Cosmos DB 的自动建议

所有云服务（包括 Azure Cosmos DB）会频繁更新，并提供新的功能、功能和改进。 您的应用程序必须跟上最新的性能和安全更新。 Azure 门户提供自定义的建议，使你能够最大程度地提高应用程序的性能。 Azure Cosmos DB 的顾问引擎会持续分析 Azure Cosmos DB 资源的使用历史记录，并根据工作负荷模式提供建议。 这些建议对应于分区、索引、网络和安全性等领域。这些自定义建议有助于提高应用程序的性能。

## <a name="view-recommendations"></a>查看建议

可以通过以下方式查看 Azure Cosmos DB 的建议：

- 查看建议的一种方法是在 "通知" 选项卡中。如果有新的建议，您将看到一个消息栏。 登录到[Azure 门户](https://portal.azure.com)并导航到 Azure Cosmos 帐户。 在 Azure Cosmos 帐户中，打开 "**通知**" 窗格，然后选择 "**建议**" 选项卡。您可以选择消息并查看建议。  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="查看 Azure Cosmos DB 窗格中的建议":::

- 你还可以使用不同的存储桶（如成本、安全性、可靠性、性能和卓越运营）分类的[Azure 顾问](../advisor/advisor-overview.md)来查找建议。 你可以选择特定订阅并按资源类型进行筛选，该类型**Azure Cosmos DB 帐户**。  选择特定建议后，它会显示可用于对工作负荷进行权益的操作。

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="查看 Azure Advisor 窗格中的建议":::

并非 "Azure Cosmos DB" 窗格中显示的所有建议都可用于 Azure Advisor，反之亦然。 这是因为，它们可在 Azure Advisor 窗格、Azure Cosmos DB 窗格或两者中适应的建议类型。

目前 Azure Cosmos DB 支持有关以下方面的建议。 其中每个建议都包含指向文档相关部分的链接，因此，您可以轻松地执行后续步骤。

## <a name="sdk-usage-recommendations"></a>SDK 使用建议

在此类别中，顾问检测到旧版本的 Sdk 的使用情况，并建议升级到较新的版本，以利用最新的 bug 修复和性能改进。 目前提供了以下 SDK 特定建议：

|“属性”  |描述  |
|---------|---------|
| 旧 Spark 连接器 | 检测是否使用了早期版本的 Spark 连接器并建议升级。 |
| 旧 .NET SDK | 检测旧版本的 .NET SDK 的使用情况，并建议升级。 |
| 旧 Java SDK | 检测旧版本 Java 连接器的使用情况，并建议升级。 |

## <a name="indexing-recommendations"></a>索引建议

在此类别中，顾问检测到索引模式、索引策略、索引路径，并建议更改当前配置是否会影响查询性能。 目前提供了以下索引特定建议：

|“属性”  |描述  |
|---------|---------|
| 延迟索引 | 检测延迟索引模式的使用情况，并建议改用一致的索引模式。 Azure Cosmos DB 延迟索引模式的目的受到限制，并且在某些情况下可能会影响查询结果的新鲜度，因此建议使用一致的索引模式。 |
| 复合索引| 检测查询可以从复合索引中受益并建议使用的帐户。 复合索引可以显著提高某些查询的性能和吞吐量消耗。|
| 具有多个索引路径的默认索引策略 | 检测在默认索引上运行的、具有许多索引路径的容器，并建议自定义索引策略。|
| 具有高 RU/秒费用的 ORDER BY 查询| 检测使用具有较高 RU/秒费用的查询颁发订单的容器，并建议浏览组合索引。|
| MongoDB 3.6 帐户，无索引和高 RU/秒| 检测 Azure Cosmos DB 的适用于 MongoDB 的 API，其中包含3.6 版本的容器，以较高 RU/秒的费用发出查询，并建议添加索引。|

## <a name="cost-optimization-recommendations"></a>成本优化建议

在此类别中，顾问将检测 RU/s 的使用情况，并确定您可以通过对资源进行一些更改或利用不同的定价模型来优化价格。 目前提供以下特定于成本优化的建议：

|“属性”  |描述  |
|---------|---------|
| 预留容量 | 检测 RU/秒利用率，并向可以从中受益的用户推荐预订实例。 |
| 非活动容器 | 检测尚未使用超过30天的容器，并建议减小此类容器的吞吐量或删除这些容器。|
| 高吞吐量的新订阅 | 检测到新的订阅，其帐户花费的时间非常高 RU/秒，并向他们提供通知。 此通知专门用于向新客户提供感知，Azure Cosmos DB 操作基于预配吞吐量的模型而不是基于消耗的模型。 |

## <a name="migration-recommendations"></a>迁移建议

在此类别中，顾问检测到你使用的是旧功能，建议进行迁移，以便能够利用 Azure Cosmos DB 的巨大可伸缩性和其他优点。 目前提供以下特定于迁移的建议：

|“属性”  |描述  |
|---------|---------|
| 未分区容器 | 检测固定大小的容器，这些容器接近其最大存储限制，并建议将它们迁移到已分区容器。|

## <a name="query-usage-recommendations"></a>查询使用建议

在此类别中，顾问将检测查询执行情况，并确定查询性能能否通过一些更改进行优化。 目前提供了以下查询用法建议：

|“属性”  |描述  |
|---------|---------|
| 具有固定页面大小的查询 | 检测使用固定页面大小发出的查询，并建议使用-1 （对页面大小没有限制），而不是定义一个特定值。 此选项可减少检索所有结果所需的网络往返次数。 |

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中优化查询性能](sql-api-query-metrics.md)
* 使用 Azure Cosmos DB[排查查询问题](troubleshoot-query-performance.md)
