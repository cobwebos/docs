---
title: 从更改源处理器库迁移到 Azure Cosmos DB .NET V3 SDK
description: 了解如何使用更改源处理器库将应用程序迁移到 Azure Cosmos DB SDK V3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: f651beb181430f65d0b4c86f285e74958f8366eb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588877"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>从更改源处理器库迁移到 Azure Cosmos DB .NET V3 SDK

本文介绍将使用[更改源处理器库](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet)的现有应用程序代码迁移到最新版本的 .net SDK 中的[更改源](change-feed.md)功能（也称为 .net V3 SDK）所需的步骤。

## <a name="required-code-changes"></a>必需的代码更改

.NET V3 SDK 有几项重大更改，以下是迁移应用程序的关键步骤：

1. 将 `DocumentCollectionInfo` 实例转换为受监视容器和租约容器 `Container` 引用。
1. 应更新使用 `WithProcessorOptions` 的自定义，以使用 `WithLeaseConfiguration` 和 `WithPollInterval` 来表示时间间隔，`WithStartTime`[开始时间](how-to-configure-change-feed-start-time.md)，并使用 `WithMaxItems` 来定义最大项计数。
1. 将 `GetChangeFeedProcessorBuilder` 上的 `processorName` 设置为与 `ChangeFeedProcessorOptions.LeasePrefix`上配置的值匹配，或使用 `string.Empty` 其他值。
1. 更改将不再作为 `IReadOnlyList<Document>`传递，而是 `IReadOnlyCollection<T>` 其中 `T` 是需要定义的类型，则不再存在任何基项类。
1. 若要处理这些更改，您不再需要实现，而是需要[定义一个委托](change-feed-processor.md#implementing-the-change-feed-processor)。 委托可以是静态函数，或者，如果需要跨执行维护状态，则可以创建自己的类并作为委托传递实例方法。

例如，如果生成更改源处理器的原始代码如下所示：

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorLibrary":::

迁移的代码如下所示：

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorMigrated":::

和委托可以是静态方法：

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="Delegate":::

## <a name="state-and-lease-container"></a>状态和租赁容器

与更改源处理器库类似，.NET V3 SDK 中的更改源功能使用[租约容器](change-feed-processor.md#components-of-the-change-feed-processor)来存储状态。 但是，架构是不同的。

SDK V3 更改源处理器将检测任何旧的库状态，并在首次执行迁移的应用程序代码时自动将其迁移到新的架构。 

您可以使用旧代码安全地停止应用程序，将代码迁移到新版本，启动已迁移的应用程序，而在应用程序停止时所发生的任何更改都将被新版本选取并处理。

> [!NOTE]
> 将使用库的应用程序迁移到 .NET V3 SDK 是单向的，因为状态（租约）将迁移到新的架构。 迁移不向后兼容。


## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的用法示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他示例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源处理器：

* [更改源处理器概述](change-feed-processor.md)
* [使用更改源估算器](how-to-use-change-feed-estimator.md)
* [更改源处理器开始时间](how-to-configure-change-feed-start-time.md)
