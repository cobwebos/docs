---
title: 使用 Azure Cosmos DB 资源管理器管理数据
description: Azure Cosmos DB 资源管理器是基于 Web 的独立界面，允许查看和管理存储在 Azure Cosmos DB 中的数据。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.openlocfilehash: 57ba647ade45928f03cd7fb2b037642d5e4d52cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096818"
---
# <a name="work-with-data-using-azure-cosmos-explorer"></a>使用 Azure Cosmos 资源管理器处理数据 

Azure Cosmos DB 资源管理器是基于 Web 的独立界面，允许查看和管理存储在 Azure Cosmos DB 中的数据。 Azure Cosmos DB 资源管理器相当于创建 Azure Cosmos DB 帐户时 Azure 门户中提供的现有“数据资源管理器”选项卡。 Azure Cosmos DB 资源管理器相对于现有数据资源管理器的主要优势是：

* 提供全屏空间，可用于查看数据，运行查询、存储过程、触发器并查看它们的结果。  

* 可以向其他无权访问 Azure 门户或订阅的用户提供对数据库帐户及其集合的临时或永久读取或读写访问权限。  

* 可以与其他无权访问 Azure 门户或订阅的用户共享查询结果。  

## <a name="access-azure-cosmos-db-explorer"></a>访问 Azure Cosmos DB 资源管理器

1. 登录到 [Azure 门户](https://portal.azure.com/)。 

2. 从“所有资源”中，查找并导航到 Azure Cosmos DB 帐户，然后选择“密钥”并复制“主连接字符串”。  

3. 转到 https://cosmos.azure.com/，粘贴连接字符串，并选择“连接”。 通过使用连接字符串，可以不受任何时间限制地访问 Azure Cosmos DB 资源管理器。  

   如果想为其他用户提供 Azure Cosmos DB 帐户的临时访问权限，可以使用读写和读取访问 URL 来实现。 

4. 打开“数据资源管理器”边栏选项卡，选择“全屏打开” 从弹出对话框中，可以查看两个访问 URL - “读写”和“读取”。 可通过这些 URL 与其他用户暂时分享 Azure Cosmos DB 帐户。 对帐户的访问权限将在 24 小时后到期，之后可使用新的访问 URL 或连接字符串重新连接。 

   **读写** - 与其他用户共享读写 URL 时，他们可以查看和修改与该特定帐户关联的数据库、集合、查询和其他资源。

   **读取** - 与其他用户共享只读 URL 时，他们可以查看与该特定帐户关联的数据库、集合、查询和其他资源。 例如，如果想与无权访问 Azure 门户或 Azure Cosmos DB 帐户的团队成员分享查询结果，可以向他们提供此 URL。

   选择打开帐户时使用的访问权限类型，并单击“打开”。 打开资源管理器后，体验与 Azure 门户中的“数据资源管理器”选项卡相同。   

   ![打开 Azure Cosmos DB 资源管理器](./media/data-explorer/open-data-explorer-with-access-url.png)

## <a name="known-issues"></a>已知问题

Azure Cosmos DB Gremlin 和 Table API 帐户目前尚不支持允许共享临时读写或读取权限的“全屏打开”体验。 通过将连接字符串传递给 Azure Cosmos DB 资源管理器，仍然可以查看 Gremlin 和 Table API 帐户。 

目前数据资源管理器中不支持查看包含 UUID 的文档。 这不会影响加载集合，只查看包含这些文档的单个文档或查询。 若要查看和管理这些文档，用户应继续使用最初用于创建这些文档的工具。

## <a name="next-steps"></a>后续步骤
现已了解了如何开始使用 Azure Cosmos DB 资源管理器来管理数据，接下来可以：

* 开始使用 SQL 语法定义[查询](sql-api-query-reference.md)，并使用存储过程、UDF 和触发器执行[服务器端编程](stored-procedures-triggers-udfs.md)。 
