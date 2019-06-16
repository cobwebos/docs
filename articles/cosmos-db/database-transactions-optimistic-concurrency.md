---
title: Azure Cosmos DB 中的数据库事务和乐观并发控制
description: 本文介绍 Azure Cosmos DB 中的数据库事务和乐观并发控制
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1da5dabad04d72c903072a33dfb7b0229f99c62d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978990"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>事务和乐观并发控制

数据库事务提供一种安全且可预测的编程模型来处理数据的并发更改。 利用 SQL Server 等传统关系数据库，你可以使用存储过程和/或触发器编写业务逻辑，然后将其发送到该服务器以便直接在数据库引擎中执行。 使用传统关系数据库时需要处理两种不同的编程语言 - 非事务性应用程序编程语言（例如 JavaScript、Python、C#、Java 等）和由数据库本机执行的事务性编程语言（例如 T-SQL）。

Azure Cosmos DB 中的数据库引擎支持使用快照隔离且完全符合 ACID（原子性, 一致性, 隔离性, 持久性）的事务。 容器[逻辑分区](partition-data.md)范围内的所有数据库操作都以事务方式在分区副本托管的数据库引擎内执行。 这些操作同时包括写入（更新逻辑分区内的一个或多个项）和读取操作。 下表说明了不同的操作和事务类型：

| **操作**  | **操作类型** | **单项或多项事务** |
|---------|---------|---------|
| 插入（不带前/后触发器） | 写入 | 单项事务 |
| 插入（带前/后触发器） | 写入和读取 | 多项事务 |
| 替换（不带前/后触发器） | 写入 | 单项事务 |
| 替换（带前/后触发器） | 写入和读取 | 多项事务 |
| Upsert（不带前/后触发器） | 写入 | 单项事务 |
| Upsert（带前/后触发器） | 写入和读取 | 多项事务 |
| 删除（不带前/后触发器） | 写入 | 单项事务 |
| 删除（带前/后触发器） | 写入和读取 | 多项事务 |
| 执行存储过程 | 写入和读取 | 多项事务 |
| 系统发起的合并过程执行 | 写入 | 多项事务 |
| 系统基于项的有效期 (TTL) 而发起的删除项执行 | 写入 | 多项事务 |
| 读取 | 读取 | 单项事务 |
| 更改源 | 读取 | 多项事务 |
| 分页读取 | 读取 | 多项事务 |
| 分页查询 | 读取 | 多项事务 |
| 在分页查询中执行 UDF | 读取 | 多项事务 |

## <a name="multi-item-transactions"></a>多项事务

借助 Azure Cosmos DB 可以采用 JavaScript 编写[存储过程、前/后触发器、用户定义的函数 (UDF)](stored-procedures-triggers-udfs.md) 和合并过程。 Azure Cosmos DB 以本机方式支持在其数据库引擎内执行 JavaScript。 可以在容器上注册存储过程、前/后触发器、用户定义的函数 (UDF) 和合并过程，并稍后在 Azure Cosmos 数据库引擎内以事务方式执行它们。 通过采用 JavaScript 编写应用程序逻辑，可以直接使用 JavaScript 语言在数据库事务内自然表达异常处理基元的控制流、变量作用域、分配以及集成。

基于 JavaScript 的存储过程、触发器、UDF 和合并过程包含在环境 ACID 事务中，该事务在逻辑分区内的所有项目之间使用快照隔离。 在其执行过程中，如果 JavaScript 程序引发异常，则整个事务都将中止并回退。 生成的编程模型简单但功能强大。 JavaScript 开发人员在继续使用其自己熟悉的语言构造和库基元的同时，可以获得“持久”的编程模型。

直接在数据库引擎中执行 JavaScript 的能力可实现针对容器项的数据库操作的性能和事务性执行。 此外，由于 Azure Cosmos 数据库引擎本机支持 JSON 和 JavaScript，因此应用程序和数据库类型系统之间不存在任何阻抗失配。

## <a name="optimistic-concurrency-control"></a>乐观并发控制 

乐观并发控制可以防止丢失更新和删除。 并发冲突的操作受数据库引擎的常规悲观锁定的限制，该引擎由拥有该项的逻辑分区托管。 如果两个并发操作尝试更新逻辑分区内的一个最新版的项，则其中一个会成功，而另一个将失败。 但是，如果尝试同时更新相同项的一个或两个操作以前读取过该项的较旧值，则该数据库无法确定这一个或两个冲突操作以前读取的值是否确实是项的最新值。 幸运的是，在允许这两个操作进入数据库引擎内的事务边界之前，可以使用**乐观并发控制 (OCC)** 检测这种情况。 OCC 可防止数据意外覆盖其他人所做的更改。 它还可以防止其他人意外覆盖你自己的更改。

基于 Azure Cosmos DB 的通信协议层，项的并发更新受 OCC 限制。 Azure Cosmos 数据库可确保要更新（或删除）项的客户端版本与 Azure Cosmos 容器中该项的版本相同。 这可保证你的写入内容不会被他人的写入内容意外覆盖，反之亦然。 在多用户环境中，乐观并发控制可防止意外删除或更新项的错误版本。 在这种情况下，可防止项出现令人痛恨的“丢失更新”或“丢失删除”问题。

存储在 Azure Cosmos 容器中的每个项都具有系统定义的 `_etag` 属性。 每次更新项时，`_etag` 的值都由服务器自动生成和更新。 `_etag` 可与客户端提供的 `if-match` 请求标头配合使用，使服务器能够决定是否可以条件性地更新某项。 如果 `if-match` 标头的值与服务器上的 `_etag` 的值匹配，则会更新该项。 如果 `if-match` 请求标头值不再是最新值，则服务器会拒绝该操作，并提供“HTTP 412 前置条件失败”响应消息。 然后客户端可重新提取该项，以在服务器上获取该项的当前版本，或用该项自己的 `_etag` 值覆盖服务器中该项的版本。 此外，`_etag` 可以与 `if-none-match` 标头配合使用，以确定是否需要重新提取资源。 

项的`_etag`值发生更改，每次更新项。 对于替换项操作，必须在请求选项中显式表达 `if-match`。 有关示例，请参阅 [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446) 中的示例代码。 将对存储过程接触的所有写入项隐式检查 `_etag` 值。 如果检查到任何冲突，存储过程将回退事务并引发异常。 通过此方法，将以原子方式应用存储过程中的所有写入内容或不应用任何写入内容。 这是应用程序重新应用更新并重试原始客户端请求的信号。

## <a name="next-steps"></a>后续步骤

请通过以下文章详细了解数据库事务和乐观并发控制：

- [使用 Azure Cosmos 数据库、容器和项](databases-containers-items.md)
- [一致性级别](consistency-levels.md)
- [冲突类型和解决策略](conflict-resolution-policies.md)
- [存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)
