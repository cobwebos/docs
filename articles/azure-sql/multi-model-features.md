---
title: 多模型功能
description: Microsoft Azure SQL 使您能够在同一个数据库中处理多个数据模型。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 59a709a206eb29b875272674ee19e414023cc37f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073314"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Azure SQL 数据库和 SQL 托管实例的多模型功能
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

使用多模型数据库可以存储和处理以多种数据格式表示的数据，例如关系数据、图形、JSON/XML 文档、键值对，等等。

## <a name="when-to-use-multi-model-capabilities"></a>何时使用多模型功能

[Azure SQL 系列产品](azure-sql-iaas-vs-paas-what-is-overview.md)设计为使用关系模型，在大多数情况下，该模型可为各种常规用途的应用程序提供最佳性能。 但是，Azure SQL 系列产品并不仅限于关系数据。 借助 Azure SQL 系列产品，可使用各种与关系模型紧密集成的非关系格式。
对于以下情况，应考虑使用 Azure SQL 系列产品的多模型功能：

- 某些信息或结构更适合 NoSQL 模型，并且你不想要使用独立的 NoSQL 数据库。
- 大部分数据适合关系模型，并且你需要以 NoSQL 形式为某些数据部分建模。
- 希望利用丰富的 Transact-SQL 语言来查询和分析关系数据与 NoSQL 数据，并将其集成到可以使用 SQL 语言的各种工具和应用程序。
- 希望应用[内存中技术](in-memory-oltp-overview.md)等数据库功能来改善 NoSQL 数据结构的分析或处理性能，使用[事务复制](managed-instance/replication-transactional-overview.md)或[可读副本](database/read-scale-out.md)在其他位置创建数据的副本，并减轻主数据库中的某些分析工作负荷。

## <a name="overview"></a>概述

Azure SQL 系列产品提供以下多模型功能：

- [图形功能](#graph-features)：以节点和边缘集的形式呈现数据，并使用通过图形 `MATCH` 运算符增强的标准 Transact-SQL 查询来查询图形数据。
- [JSON 功能](#json-features)：在表中插入 JSON 文档，将关系数据转换为 JSON 文档，或反之。 可以使用通过 JSON 函数增强的标准 Transact-SQL 语言来分析文档，并使用非聚集索引、列存储索引或内存优化表来优化查询。
- [空间功能](#spatial-features)：存储地理或几何图形数据、使用空间索引为其编制索引，并使用空间查询检索数据。
- [XML 功能](#xml-features)：在数据库中存储 XML 数据并为其编制索引，并使用本机 XQuery/XPath 运算来处理 XML 数据。 Azure SQL 系列产品提供专用的内置 XML 查询引擎用于处理 XML 数据。
- [键值对](#key-value-pairs)未明确作为特殊功能受到支持，因为键值对本身可作为包含两个列的表建模。

  > [!Note]
  > 可以在同一个 Transact-SQL 查询中使用 JSON Path 表达式、XQuery/XPath 表达式、空间函数和图形查询表达式来访问数据库中存储的任何数据。 此外，可执行 Transact-SQL 查询的任何工具或编程语言也可以使用该查询接口来访问多模型数据。 这是与 [Azure Cosmos DB](/azure/cosmos-db/) 等多模型数据库之间的主要差别，后者为不同的数据模型提供专用的 API。

以下部分将会介绍 Azures SQL 系列产品中最重要的多模型功能。

## <a name="graph-features"></a>图形功能

Azure SQL 系列产品提供图形数据库功能，用于对数据库中的多对多关系建模。 图形是节点（或顶点）和边缘（或关系）的集合。 节点表示实体（例如，某个人或组织），边缘表示该实体连接的两个节点之间的关系（例如，爱好或朋友）。 下面是使图形数据库独一无二的某些功能：

- 边缘或关系是图形数据库中的第一类实体，可以带有关联的特性或属性。
- 单个边缘可以灵活连接图形数据库中的多个节点。
- 可以轻松表达模式匹配和多跃点导航查询。
- 可以轻松表达传递闭包和多态查询。

[图形关系和图形查询功能](/sql/relational-databases/graphs/sql-graph-overview)集成到 Transact-SQL 中，享有使用 SQL Server 数据库引擎作为基础数据库管理系统所带来的优势。

### <a name="when-to-use-a-graph-capability"></a>何时使用图形功能

不能使用关系数据库实现的目的也不能使用图形数据库来实现。 但是，使用图形数据库可以轻松表达某些查询。 可根据以下因素来确定是要选择图形数据库还是关系数据库：

- 为分层数据建模，其中一个节点可以包含多个父级，因此不能使用 HierarchyId
- 应用程序存在复杂的多对多关系；在应用程序演进过程中，会不断添加新的关系。
- 需要分析互联的数据和关系。

## <a name="json-features"></a>JSON 功能

使用 Azure SQL 系列产品可以分析和查询以 JavaScript 对象表示法 [(JSON)](https://www.json.org/) 格式表示的数据，并将关系数据导出为 JSON 文本。

JSON 是用于在新式 Web 与移动应用程序中交换数据的流行数据格式。 JSON 还用于将半结构化数据存储在日志文件或 NoSQL 数据库（例如 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)）中。 许多 REST Web 服务返回采用 JSON 文本格式的结果，或接受采用 JSON 格式的数据。 大多数 Azure 服务（例如 [Azure 认知搜索](https://azure.microsoft.com/services/search/)、[Azure 存储](https://azure.microsoft.com/services/storage/)和 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)）都具有返回或使用 JSON 的 REST 终结点。


使用 Azure SQL 系列产品可轻松地处理 JSON 数据、将数据库与新式服务集成，并提供以下用于处理 JSON 数据的函数：

![JSON 函数](./media/multi-model-features/image_1.png)

如果具有 JSON 文本，可使用内置函数 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql)、[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) 和 [ISJSON](/sql/t-sql/functions/isjson-transact-sql)，从 JSON 中提取数据，或者验证 JSON 的格式是否正确。 使用 [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) 函数可以更新 JSON 文本中的值。 对于更高级的查询和分析，使用 [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) 函数可将 JSON 对象数组转换成行集。 可对返回的结果集执行任何 SQL 查询。 最后，使用 [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) 可将存储在关系表中的数据格式化为 JSON 文本。

有关详细信息，请参阅[如何处理 JSON 数据](database/json-features.md)。
[JSON](/sql/relational-databases/json/json-data-sql-server) 是 SQL Server 数据库引擎的核心功能。

### <a name="when-to-use-a-json-capability"></a>何时使用 JSON 功能

在某些特定的情况下，可以使用文档模型而不使用关系模型：

- 架构高度规范化不能带来明显的好处，因为你要一次性访问对象的所有字段，或者永远不更新对象的规范化部分。 但是，规范化模型会增大查询的复杂性，因为需要联接大量的表才能获取数据。
- 原生使用 JSON 文档的应用程序采用通信或数据模型，而你不想要引入附加的层来将关系数据转换为 JSON，或反之。
- 需要通过取消规范化子表或“实体-对象-值”模式来简化数据模型。
- 需要加载或导出以 JSON 格式存储的数据，且不使用其他某种用于分析数据的工具。

## <a name="spatial-features"></a>空间功能

空间数据表示有关物理位置和几何对象形状的信息。 这些对象可能是点位置或更复杂的对象，例如国家/地区/区域、道路或湖泊。

 两种支持的空间数据类型如下： 

- 几何类型在欧几里得（平面）坐标系中表示数据。
- 地理类型在环球坐标系中表示数据。

借助可在 Azure SQL 系列产品中使用的大量空间对象，用户能够分析和查询以 JavaScript 对象表示法 [(JSON)](https://www.json.org/) 格式表示的数据，并将关系数据导出为 JSON 文本。
例如 [Point](/sql/relational-databases/spatial/point)、[LineString](/sql/relational-databases/spatial/linestring)、[Polygon](/sql/relational-databases/spatial/polygon)，等等。

Azure SQL 系列产品还提供专用的[空间索引](/sql/relational-databases/spatial/spatial-indexes-overview)，可用于改进空间查询的性能。

[空间支持](/sql/relational-databases/spatial/spatial-data-sql-server)是 SQL Server 数据库引擎的核心功能。

## <a name="xml-features"></a>XML 功能

SQL Server 数据库引擎提供一个强大平台用于开发丰富的半结构化数据管理应用程序。 数据库引擎的所有组件中都集成了 XML 支持，其中包括：

- XML 数据类型。 XML 值原生可存储在 XML 数据类型列中，可以根据 XML 架构集合将该列类型化，或者将其保持非类型化。 可为 XML 列编制索引。
- 可以针对列中存储的 XML 数据和 XML 类型的变量指定 XQuery 查询。 可在任何 Transact-SQL 查询中使用 XQuery 功能来访问数据库中使用的任何数据模型。
- 使用[主要 XML 索引](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)为 XML 文档中的所有元素自动编制索引，或者使用[辅助 XML 索引](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)来指定要编制索引的确切路径。
- OPENROWSET 允许批量加载 XML 数据。
- 将关系数据转换为 XML 格式。

[XML](/sql/relational-databases/xml/xml-data-sql-server) 是 SQL Server 数据库引擎的核心功能。

### <a name="when-to-use-an-xml-capability"></a>何时使用 XML 功能

在某些特定的情况下，可以使用文档模型而不使用关系模型：

- 架构高度规范化不能带来明显的好处，因为你要一次性访问对象的所有字段，或者永远不更新对象的规范化部分。 但是，规范化模型会增大查询的复杂性，因为需要联接大量的表才能获取数据。
- 原生使用 XML 文档的应用程序采用通信或数据模型，而你不想要引入附加的层来将关系数据转换为 XML，或反之。
- 需要通过取消规范化子表或“实体-对象-值”模式来简化数据模型。
- 需要加载或导出以 XML 格式存储的数据，且不使用其他某种用于分析数据的工具。

## <a name="key-value-pairs"></a>键值对

Azure SQL 系列产品不提供支持键值对的专用类型或结构，因为键值结构原生表示为标准的关系表：

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

可根据需求，不受约束地自定义此键值结构。 例如，值可以是 XML 文档而不是 `nvarchar(max)` 类型，如果值为 JSON 文档，则你可以施加 `CHECK` 约束用于验证 JSON 内容的有效性。 可在附加的列中放置与某个键相关的任意数目的值、添加计算列与索引来简化和优化数据访问、将表定义为内存表/优化的仅限架构表以提高性能，等等。

请参阅 [BWin 如何使用内存中 OLTP 实现前所未有的性能和处理规模](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/)，其中提到 BWin 的 ASP.NET 缓存解决方案每秒可以实现 1.200.000 次批处理，并举例说明如何在实践中有效地将关系模型用作键值对解决方案。

## <a name="next-steps"></a>后续步骤

Azure SQL 系列产品中的多模型功能也是 Azure SQL 系列产品共有的 SQL Server 数据库引擎核心功能。 有关这些功能的更多详细信息，请访问 SQL 关系数据库文档页：

- [图形处理](/sql/relational-databases/graphs/sql-graph-overview)
- [JSON 数据](/sql/relational-databases/json/json-data-sql-server)
- [空间支持](/sql/relational-databases/spatial/spatial-data-sql-server)
- [XML 数据](/sql/relational-databases/xml/xml-data-sql-server)
