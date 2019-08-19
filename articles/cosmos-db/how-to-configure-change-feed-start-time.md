---
title: 如何配置更改源处理器的开始时间 - Azure Cosmos DB
description: 了解如何将更改源处理器配置为从特定的日期和时间开始读取
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 7db14f6406223486ea0efb80d3d1cfcd0351f414
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69037224"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>如何配置更改源处理器的开始时间

本文介绍如何将[更改源处理器](./change-feed-processor.md)配置为从特定的日期和时间开始读取。

## <a name="default-behavior"></a>默认行为

首次启动更改源处理器时，它将初始化租约容器，并开始其[处理生命周期](./change-feed-processor.md#processing-life-cycle)。 不会检测首次初始化更改源处理器之前在容器中发生的任何更改。

## <a name="reading-from-a-previous-date-and-time"></a>从以前的某个日期和时间读取

将 `DateTime` 的实例传递给 `WithStartTime` 生成器扩展，可将更改源处理器初始化为从**特定的日期和时间**开始读取更改：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

更改源处理器将根据该特定日期和时间初始化，并开始读取此日期和时间之后发生的更改。

## <a name="reading-from-the-beginning"></a>从头开始读取

在其他方案（例如数据迁移，或分析容器的整个历史记录）中，需要从**该容器的生存期开始时间**读取更改源。 为此，可以在生成器扩展中使用 `WithStartTime`，但需要传递 `DateTime.MinValue.ToUniversalTime()`，以便生成最小 `DateTime` 值的 UTC 表示形式，如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

更改源处理器将会初始化，并从容器生存期的开始时间读取更改。

> [!NOTE]
> 这些自定义选项仅用于设置更改源处理器的起始时间点。 首次初始化租约容器后，更改这些选项不起作用。

> [!NOTE]
> 指定时间点时，只会读取容器中当前存在的项的更改。 如果删除某个项，也会删除更改源中该项的历史记录。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的用法示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/usage/changefeed)
* [GitHub 上的其他示例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>后续步骤

接下来，请通过以下文章继续详细了解更改源：

* [更改源概述](change-feed.md)
* [读取更改源的方式](read-change-feed.md)
* [使用更改源处理器](change-feed-processor.md)
