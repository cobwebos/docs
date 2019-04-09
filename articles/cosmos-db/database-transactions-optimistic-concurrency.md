---
title: Azure Cosmos DB 中的数据库事务和乐观并发控制
description: 本文介绍 Azure Cosmos DB 中的数据库事务和乐观并发控制
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 568f47aacf39793d4c2da46798682abc002ca33b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279495"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>事务和乐观并发控制

数据库事务提供一种安全且可预测的编程模型来处理数据的并发更改。 传统的关系数据库，例如 SQL Server，您可以编写业务逻辑使用存储过程和/或触发器，将其发送到该服务器以便直接在数据库引擎中执行。 与传统的关系数据库，您都需要处理两个不同的编程语言 （非事务性） 应用程序编程语言，如 JavaScript、 Python、 C#，Java 等和事务性编程语言 （例如 T-SQL)，在本机上执行的数据库。

Azure Cosmos DB 中的数据库引擎支持使用快照隔离且完全符合 ACID（原子性, 一致性, 隔离性, 持久性）的事务。 所有数据库操作的作用域内的容器的[逻辑分区](partition-data.md)的分区的副本由承载在数据库引擎中以事务方式执行。 这些操作同时包括写入（更新逻辑分区内的一个或多个项）和读取操作。 下表说明了不同的操作和事务类型：

| **Operation**  | **操作类型** | **单个或多项事务** |
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

Azure Cosmos DB，可编写[存储过程、 前/后触发器，用户定义的函数 (Udf)](stored-procedures-triggers-udfs.md)和合并在 JavaScript 中的过程。 Azure Cosmos DB 以本机方式支持在其数据库引擎内执行 JavaScript。 可以在容器上注册存储过程、前/后触发器、用户定义的函数 (UDF) 和合并过程，并稍后在 Azure Cosmos 数据库引擎内以事务方式执行它们。 通过采用 JavaScript 编写应用程序逻辑，可以直接使用 JavaScript 语言在数据库事务内自然表达异常处理基元的控制流、变量作用域、分配以及集成。

基于 JavaScript 的存储过程、触发器、UDF 和合并过程包含在环境 ACID 事务中，该事务在逻辑分区内的所有项目之间使用快照隔离。 在其执行过程中，如果 JavaScript 程序引发异常，则整个事务都将中止并回退。 生成的编程模型简单但功能强大。 JavaScript 开发人员在继续使用其自己熟悉的语言构造和库基元的同时，可以获得“持久”的编程模型。

直接在数据库引擎中执行 JavaScript 的能力可实现针对容器项的数据库操作的性能和事务性执行。 此外，由于 Azure Cosmos 数据库引擎本机支持 JSON 和 JavaScript，因此应用程序和数据库类型系统之间不存在任何阻抗失配。

## <a name="optimistic-concurrency-control"></a>乐观并发控制 

乐观并发控制可以防止丢失更新和删除。 并发冲突的操作受数据库引擎的常规悲观锁定的限制，该引擎由拥有该项的逻辑分区托管。 如果两个并发操作尝试更新逻辑分区内的一个最新版的项，则其中一个会成功，而另一个将失败。 但是，如果尝试同时更新相同项的一个或两个操作以前读取过该项的较旧值，则该数据库无法确定这一个或两个冲突操作以前读取的值是否确实是项的最新值。 幸运的是，这种情况下也可以使用检测到**乐观并发控制 (OCC)** 之前让两个操作输入在数据库引擎内部的事务边界。 OCC 可防止数据意外覆盖其他人所做的更改。 它还可以防止其他人意外覆盖你自己的更改。

基于 Azure Cosmos DB 的通信协议层，项的并发更新受 OCC 限制。 Azure Cosmos 数据库可确保要更新（或删除）项的客户端版本与 Azure Cosmos 容器中该项的版本相同。 这可保证你的写入内容不会被他人的写入内容意外覆盖，反之亦然。 在多用户环境中，乐观并发控制可防止意外删除或更新项的错误版本。 在这种情况下，可防止项出现令人痛恨的“丢失更新”或“丢失删除”问题。

存储在 Azure Cosmos 容器中的每个项都具有系统定义的 `_etag` 属性。 每次更新项时，`_etag` 的值都由服务器自动生成和更新。 `_etag` 可以在客户端提供`if-match`请求标头以使服务器能够决定是否可以有条件地更新项。 值`if-match`标头的值匹配`_etag`在服务器上，然后更新项目。 如果的值`if-match`不再当前请求标头，则服务器会拒绝该操作与"HTTP 412 不满足前提条件失败"响应消息。 客户端然后可以重新提取该项以获取服务器上项的当前版本或重写具有其自己的服务器中的项的版本`_etag`的项值。 此外，`_etag`可与`if-none-match`标头以确定是否需要重新提取资源。 

项的`_etag`值发生更改，每次更新项。 有关项的替换操作，`if-match`必须显式表示为请求选项的一部分。 有关示例，请参阅 [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446) 中的示例代码。 `_etag` 接触的存储过程的所有写入项隐式检查值。 如果检测到任何冲突，则存储的过程将回滚事务并引发异常。 通过此方法，将以原子方式应用存储过程中的所有写入内容或不应用任何写入内容。 这是应用程序重新应用更新并重试原始客户端请求的信号。

## <a name="next-steps"></a>后续步骤

请通过以下文章详细了解数据库事务和乐观并发控制：

- [使用 Azure Cosmos 数据库、容器和项](databases-containers-items.md)
- [一致性级别](consistency-levels.md)
- [冲突类型和解决策略](conflict-resolution-policies.md)
- [存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)
