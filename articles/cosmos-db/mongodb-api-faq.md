---
title: 有关 Azure Cosmos DB 的适用于 MongoDB 的 API 的常见问题
description: 获取有关 Azure Cosmos DB 的 MongoDB API 的常见问题的解答
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: de75ea1bc0a1cf63c74be3f7d9e486e1fe38db6f
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614559"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-dbs-api-for-mongodb"></a>有关 Azure Cosmos DB 的适用于 MongoDB 的 API 的常见问题

Azure Cosmos DB 的 API for MongoDB 是一个线路协议兼容层，允许应用程序使用现有的、社区支持的 SDK 和用于 MongoDB 的驱动程序轻松、透明地与本机 Azure Cosmos 数据库引擎通信。 开发人员现在可以使用现有的 MongoDB 工具链和技术，生成能够充分利用 Azure Cosmos DB 的应用程序。 开发人员受益于 Azure Cosmos DB 的独特功能，其中包括具有多主机复制的全局分发、自动索引、备份维护、财务支持的服务级别协议（Sla）等。

## <a name="how-do-i-connect-to-my-database"></a>如何连接到数据库？

若要通过 Azure Cosmos DB 的用于 MongoDB 的 API 连接到 Cosmos 数据库，最快捷的方法是使用 [Azure 门户](https://portal.azure.com)。 转到帐户，然后在左侧导航菜单上单击“快速启动”。  快速入门是获取连接到数据库的代码片段的最佳方式。

Azure Cosmos DB 实施严格的安全要求和标准。 Azure Cosmos DB 帐户需要通过 TLS 进行身份验证和安全通信，因此务必使用 TLSv1.2。

有关详细信息，请参阅[通过 Azure Cosmos DB 的用于 MongoDB 的 API 连接到 Cosmos 数据库](connect-mongodb-account.md)。

## <a name="error-codes-while-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时出现错误代码？

除了常见的 MongoDB 错误代码外，Azure Cosmos DB 的用于 MongoDB 的 API 还有自己的特定错误代码：

| 错误               | 代码  | 说明  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数超过了容器的预配请求单位率，已被限制。 | 考虑从 Azure 门户中对分配给一个容器或一组容器的吞吐量进行缩放，或者重试。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系技术支持。 <br><br> 示例：`db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |

## <a name="supported-drivers"></a>支持的驱动程序

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>是否支持将 MongoDB 的 Simba 驱动程序与 Azure CosmosDB 的用于 MongoDB 的 API 一起使用？

是的，可以将 Simba 的 Mongo ODBC 驱动程序与 Azure Cosmos DB 的用于 MongoDB 的 API 一起使用

## <a name="next-steps"></a>后续步骤

* [使用 Azure Cosmos DB 的适用于 MongoDB 的 API 构建 .NET web 应用](create-mongodb-dotnet.md)
* [在 Azure Cosmos DB 中使用 Java 和 MongoDB API 创建控制台应用](create-mongodb-java.md)
