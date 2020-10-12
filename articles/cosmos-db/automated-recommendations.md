---
title: 针对 Azure Cosmos DB 自动提出的性能、成本和安全性建议
description: 了解如何查看根据你的工作负载模式针对 Azure Cosmos DB 提供的自定义性能、成本、安全性建议和其他建议。
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87450083"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>针对 Azure Cosmos DB 自动提出的建议

所有云服务（包括 Azure Cosmos DB）都会通过频繁更新来提供新的特性、功能和改进内容。 您的应用程序必须跟上最新的性能和安全更新。 Azure 门户提供了让你可以最大限度地提高应用程序性能的自定义建议。 Azure Cosmos DB 的顾问引擎会持续分析 Azure Cosmos DB 资源的使用情况历史记录，并根据工作负载模式提供建议。 这些建议对应于分区、索引编制、网络和安全性等领域。这些自定义建议有助于提高应用程序的性能。

## <a name="view-recommendations"></a>查看建议

可以通过以下方式查看针对 Azure Cosmos DB 的建议：

- 查看建议的一种方法是使用“通知”选项卡。如果有新的建议，则会出现一个消息栏。 登录到 [Azure 门户](https://portal.azure.com)，导航到你的 Azure Cosmos 帐户。 在你的 Azure Cosmos 帐户中，打开“通知”窗格，然后选择“建议”选项卡。你可以选择消息并查看建议。  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="从 Azure Cosmos DB 窗格中查看建议":::

- 还可以通过 [Azure 顾问](../advisor/advisor-overview.md)查找按不同的 Bucket（例如成本、安全性、可靠性、性能和运营绩效）分类的建议。 可以选择特定订阅并按资源类型（即 **Azure Cosmos DB 帐户**）进行筛选。  选择某个特定建议后，它会显示可供采用的有益于你的工作负载的操作。

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="从 Azure Cosmos DB 窗格中查看建议":::

并非 Azure Cosmos DB 窗格中显示的所有建议都会显示在 Azure 顾问中，反之亦然。 这是因为，它们可在 Azure Advisor 窗格、Azure Cosmos DB 窗格或两者中适应的建议类型。

Azure Cosmos DB 目前支持针对以下领域的建议。 其中每个建议都包含指向文档相关部分的链接，因此，您可以轻松地执行后续步骤。

## <a name="sdk-usage-recommendations"></a>SDK 使用建议

在此类别中，顾问检测到你使用了旧版 SDK，因此建议你升级到更新的版本，以利用最新的 bug 修复和性能改进。 目前提供了以下特定于 SDK 的建议：

|名称  |描述  |
|---------|---------|
| 旧的 Spark 连接器 | 检测到使用了早期版本的 Spark 连接器，建议升级。 |
| 旧的 .NET SDK | 检测到使用了早期版本的 .NET SDK，建议升级。 |
| 旧的 Java SDK | 检测到使用了早期版本的 Java 连接器，建议升级。 |

## <a name="indexing-recommendations"></a>索引编制建议

在此类别中，顾问会检测索引编制模式、索引编制策略、索引路径，并会在当前配置影响查询性能的情况下提供更改建议。 目前提供了以下特定于索引编制的建议：

|名称  |描述  |
|---------|---------|
| 惰性索引编制 | 检测到使用了惰性索引编制模式，建议改用一致的索引编制模式。 Azure Cosmos DB 延迟索引模式的目的受到限制，并且在某些情况下可能会影响查询结果的新鲜度，因此建议使用一致的索引模式。 |
| 复合索引编制| 检测到其中的查询可以从复合索引中受益的帐户，建议使用它们。 复合索引可以显著改进某些查询的性能和吞吐量消耗。|
| 具有许多索引路径的默认索引编制策略 | 检测到以默认索引编制方式运行的具有许多索引路径的容器，建议自定义索引编制策略。|
| RU/秒费用很高的 ORDER BY 查询| 检测到所发出的 ORDER BY 查询具有很高的 RU/秒费用的容器，建议尝试使用复合索引。|
| 无索引且 RU/秒消耗很高的 MongoDB 3.6 帐户| 检测 Azure Cosmos DB 的适用于 MongoDB 的 API，其中包含3.6 版本的容器，以较高 RU/秒的费用发出查询，并建议添加索引。|

## <a name="cost-optimization-recommendations"></a>成本优化建议

在此类别中，顾问在检测 RU/秒使用情况后，确定你可以通过对资源进行一些更改或利用不同的定价模型来优化价格。 目前提供了以下特定于成本优化的建议：

|名称  |描述  |
|---------|---------|
| 预留容量 | 检测 RU/秒利用率，并向可以从中受益的用户推荐预订实例。 |
| 非活动容器 | 检测到超过 30 天未使用的容器，建议减小此类容器的吞吐量或删除它们。|
| 吞吐量高的新订阅 | 检测到其中的帐户每天支出的 RU/秒费用非常高的新订阅，向它们提供了通知。 此通知专用于让新客户知道 Azure Cosmos DB 使用基于预配吞吐量的模型运行，而非使用基于消耗的模型运行。 |

## <a name="migration-recommendations"></a>迁移建议

在此类别中，顾问检测到你使用的是旧功能，建议进行迁移，以便能够利用 Azure Cosmos DB 的巨大可伸缩性和其他优点。 目前提供了以下特定于迁移的建议：

|名称  |描述  |
|---------|---------|
| 未分区的容器 | 检测到固定大小的容器即将达到其最大存储限制，建议将它们迁移到已分区容器。|

## <a name="query-usage-recommendations"></a>查询使用情况建议

在此类别中，顾问会在检测查询执行情况后确定能否通过一些更改来优化查询性能。 目前提供了以下查询使用情况建议：

|名称  |描述  |
|---------|---------|
| 具有固定页面大小的查询 | 检测到使用固定页面大小发出的查询，建议使用 -1（不限制页面大小）而非定义特定值。 此选项可减少检索所有结果所需的网络往返次数。 |

## <a name="next-steps"></a>后续步骤

* [优化 Azure Cosmos DB 中的查询性能](sql-api-query-metrics.md)
* [排查使用 Azure Cosmos DB 时遇到的查询问题](troubleshoot-query-performance.md)
