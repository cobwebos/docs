---
title: 在 Azure Cosmos DB 中使用存储过程、触发器和用户定义的函数
description: 本文介绍 Azure Cosmos DB 中存储过程、触发器和用户定义的函数的概念。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 53ff318dcc034fb11e2d554f9ad8e8814eb32879
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672587"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>存储过程、触发器和用户定义的函数

Azure Cosmos DB 提供 JavaScript 的语言集成式事务执行。 在 Azure Cosmos DB 中使用 SQL API 时，可以采用 JavaScript 语言编写**存储过程**、**触发器**和**用户定义的函数 (UDF)** 。 可以使用 JavaScript 编写可在数据库引擎内部执行的逻辑。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure Cosmos DB 中的 JavaScript 语言集成式查询 API](javascript-query-api.md) 或 [Cosmos DB SQL API 客户端 SDK](how-to-use-stored-procedures-triggers-udfs.md) 来创建及执行触发器、存储过程与 UDF。

## <a name="benefits-of-using-server-side-programming"></a>使用服务器端编程的优势

使用 JavaScript 编写存储过程、触发器和用户定义的函数 (UDF) 可以生成具有以下优势的丰富应用程序：

* **过程逻辑：** 作为一种高级编程语言，JavaScript 提供用户熟悉的丰富接口来表达业务逻辑。 可以针对数据执行一系列复杂操作。

* **原子事务：** Azure Cosmos DB 保证在单个存储过程或触发器内执行的数据库操作具有原子性。 此原子功能可让应用程序在单个批中合并相关操作，因此操作要么全部成功，要么全部失败。

* **性能：** JSON 数据在本质上会映射到 JavaScript 语言类型系统。 这种映射可以实现多种优化，例如，在缓冲池中将 JSON 文档惰性具体化，并使其可按需供执行代码使用。 还有其他与传送业务逻辑到数据库相关的性能优势，包括：

   * 批处理：  可将插入等操作分组到一起并批量提交。 用于创建单独事务的网络流量延迟成本和存储开销显著降低。

   * 预编译：  存储过程、触发器和 UDF 是隐式预编译成字节代码格式，这是为了避免每次脚本调用时产生编译成本。 预编译使得存储过程的调用速度加快，且占用空间减少。

   * 定序：  有时，操作需要通过某个触发机制来对数据执行一项或多项更新。 除了原子性以外，在服务器端执行还可以带来性能优势。

* **封装：** 使用存储过程可在一个位置分组逻辑。 封装在数据的顶层添加一个抽象层，使你能够独立于数据改进应用程序。 如果数据无架构，并且你无需管理直接将其他逻辑添加到应用程序的过程，则此抽象层非常有用。 借助这种抽象，可以通过从脚本简化访问来保证数据的安全。

> [!TIP]
> 存储的过程是最适合的写入密集型，并且需要跨分区键值的事务操作。 在决定是否要使用存储的过程，优化周围封装写入可能的最大数量。 通常情况下，存储的过程不执行大量的读取或查询操作的最有效方式，因此，使用存储过程向批处理大量读取返回到客户端不会产生任何所需的权益。 为了获得最佳性能，应在客户端，使用 Cosmos SDK 上执行这些读取密集型操作。 

## <a name="transactions"></a>事务

典型数据库中的事务可以定义为一系列作为单个逻辑单元工作执行的操作。 每个事务提供 **ACID 属性保证**。 ACID 是一个众所周知的缩写词，表示：原子性、一致性、隔离性和持久性。     

* 原子性保证将一个事务内部执行的所有操作视为一个单位，这些操作要么全部提交，要么都不提交。 

* 一致性确保数据始终在各个事务之间处于有效状态。 

* 隔离性保证不会存在两个事务互相干扰的情况 – 许多商务系统提供多个可以基于应用程序需求使用的隔离级别。 

* 持久性确保数据库中提交的任何更改始终存在。

在 Azure Cosmos DB 中，JavaScript 运行时托管在数据库引擎内部。 因此，在存储过程和触发器中发出的请求将在与数据库会话相同的范围内执行。 此功能使 Azure Cosmos DB 能够保证所有属于某个存储过程或触发器的操作的 ACID 属性。 有关示例，请参阅[如何实现事务](how-to-write-stored-procedures-triggers-udfs.md#transactions)一文。

### <a name="scope-of-a-transaction"></a>事务的范围

如果存储过程与某个 Azure Cosmos 容器相关联，则存储过程将在逻辑分区键的事务范围内执行。 每个存储过程执行必须包含一个对应于事务范围的逻辑分区键值。 有关详细信息，请参阅 [Azure Cosmos DB 分区](partition-data.md)一文。

### <a name="commit-and-rollback"></a>提交和回滚

事务原生集成到 Azure Cosmos DB JavaScript 编程模型中。 在 JavaScript 函数内，所有操作自动包装在单个事务下。 如果存储过程中的 JavaScript 逻辑完成且未引发任何异常，则事务中的所有操作都将提交到数据库。 `BEGIN TRANSACTION` 和 `COMMIT TRANSACTION` 等语句（与关系数据库相关）在 Azure Cosmos DB 中是隐式的。 如果脚本引发了任何异常，Azure Cosmos DB JavaScript 运行时将回滚整个事务。 在这种情况下，引发异常实际上等同于 Azure Cosmos DB 中的 `ROLLBACK TRANSACTION`。

### <a name="data-consistency"></a>数据一致性

存储过程和触发器始终在 Azure Cosmos 容器的主要副本上执行。 此功能可确保从存储过程执行的读取提供[非常一致性](consistency-levels-tradeoffs.md)。 使用用户定义的函数的查询可以在主要副本或任何辅助副本上执行。 存储过程和触发器旨在支持事务写入 - 同时，最好是将只读逻辑实现为应用程序端逻辑，使用 [Azure Cosmos DB SQL API SDK](sql-api-dotnet-samples.md) 的查询有助于最大程度地利用数据库吞吐量。 

## <a name="bounded-execution"></a>绑定的执行

所有 Azure Cosmos DB 操作必须在指定的超时持续时间内完成。 此约束适用于 JavaScript 函数 - 存储过程、触发器和用户定义的函数。 如果某个操作未在该时间限制内完成，事务将会回滚。

可以确保 JavaScript 函数在时间限制内完成，或者实施一个基于延续的模型来批处理/恢复执行。 为了简化存储过程和触发器的开发以应对时间限制，Azure Cosmos 容器下的所有函数（例如，项的创建、读取、更新和删除）将返回表示该操作是否完成的布尔值。 如果此值为 false，则表示过程必须结束执行，因为脚本占用的时间或预配吞吐量超过了配置的值。 如果存储过程及时完成且没有任何更多请求在排队的话，将保证完成排在第一个拒绝存储操作之前的操作。 因此，应该使用 JavaScript 的回调约定管理脚本的控制流，以将操作逐个排队。 由于脚本在服务器端环境中执行，因此受到严格的调控。 反复违反执行边界的脚本可标记为非活动状态且不可执行，应根据执行边界重新创建它们。

JavaScript 函数还有[预配的吞吐量容量](request-units.md)方面的约束。 JavaScript 函数可能会在短时间内占用大量的请求单位，如果达到预配的吞吐量容量限制，它们可能受到速率限制。 必须注意，脚本除了在执行数据库操作时需要消耗吞吐量以外，还会消耗其他吞吐量，不过，与从客户端执行相同的操作相比，这些数据库操作的开销略低。

## <a name="triggers"></a>Triggers

Azure Cosmos DB 支持两种类型的触发器：

### <a name="pre-triggers"></a>前触发器

Azure Cosmos DB 提供可以通过对 Azure Cosmos DB 项执行操作来调用的触发器。 例如，可以在创建项时指定前触发器。 在这种情况下，前触发器将在创建项之前运行。 预触发器不能有任何输入参数。 如果需要，可以使用请求对象更新原始请求中的文档正文。 当注册触发器后，用户可以指定随触发器一起运行的操作。 如果使用 `TriggerOperation.Create` 创建了触发器，则不允许在替换操作中使用该触发器。 有关示例，请参阅[如何编写触发器](how-to-write-stored-procedures-triggers-udfs.md#triggers)一文。

### <a name="post-triggers"></a>后触发器

类似于前触发器，后触发器也与针对 Azure Cosmos DB 项执行的操作相关联，但它们不需要任何输入参数。 后触发器在操作完成之后运行，且具有对发送到客户端的响应消息的访问权限。  有关示例，请参阅[如何编写触发器](how-to-write-stored-procedures-triggers-udfs.md#triggers)一文。

> [!NOTE]
> 注册触发器不自动运行时及其相应的操作 （创建 / 删除 / 替换 / 更新） 发生这种情况。 他们必须显式调用时执行这些操作。 若要了解详细信息，请参阅[如何运行触发器](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)一文。

## <a id="udfs"></a>用户定义的函数

使用用户定义的函数 (UDF) 可以轻松扩展 SQL API 查询语言语法和实现自定义业务逻辑。 只能在查询中调用 UDF。 UDF 对上下文对象没有访问权限，旨在用作仅限计算的 JavaScript。 因此，UDF 可以在次要副本上运行。 有关示例，请参阅[如何编写用户定义的函数](how-to-write-stored-procedures-triggers-udfs.md#udfs)一文。

## <a id="jsqueryapi"></a>JavaScript 语言集成式查询 API

除了使用 SQL API 查询语法发出查询以外，还可以借助[服务器端 SDK](https://azure.github.io/azure-cosmosdb-js-server) 使用 JavaScript 接口来执行查询，使用此方法不需要 SQL 相关的知识。 使用 JavaScript 查询 API 可以通过将谓词函数传入函数调用，来以编程方式生成查询。 查询将由 JavaScript 运行时分析，并在 Azure Cosmos DB 内部有效执行。 若要了解 JavaScript 查询 API 的支持，请参阅[使用 JavaScript 语言集成式查询 API](javascript-query-api.md) 一文。 有关示例，请参阅[如何使用 Javascript 查询 API 编写存储过程和触发器](how-to-write-javascript-query-api.md)一文。

## <a name="next-steps"></a>后续步骤

通过以下文章了解如何在 Azure Cosmos DB 中编写和使用存储过程、触发器与用户定义的函数：

* [如何编写存储过程、触发器和用户定义的函数](how-to-write-stored-procedures-triggers-udfs.md)

* [如何使用存储过程、触发器和用户定义的函数](how-to-use-stored-procedures-triggers-udfs.md)

* [使用 JavaScript 语言集成式查询 API](javascript-query-api.md)
