---
title: "在门户中将数据导入 Azure Search |Microsoft 文档"
description: "在 Azure 门户中使用 Azure 搜索导入数据向导从 NoSQL Azure Cosmos DB、 Blob 存储、 表存储、 SQL 数据库和 SQL Server 的 Azure 数据爬网在 Azure 虚拟机上。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: Azure Portal
ms.assetid: f40fe07a-0536-485d-8dfa-8226eb72e2cd
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: a3e6dd66197a17bfdc80c04130e198b787692a58
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="import-data-to-azure-search-using-the-portal"></a>将数据导入到 Azure Search 使用门户
Azure 门户提供**导入数据**向导将数据加载到索引的 Azure 搜索仪表板上。 

  ![命令栏上的导入数据][1]

在内部，该向导将配置并调用*索引器*，自动执行此索引的过程的几个步骤： 

* 连接到同一 Azure 订阅中的外部数据源
* 生成基于源数据结构的可修改的索引架构
* 将 JSON 文档加载到使用从数据源检索的行集的索引

你还可以尝试此工作流在 Azure Cosmos DB 中使用示例数据。 请访问[开始使用 Azure 门户中的 Azure Search](search-get-started-portal.md)有关的说明。

> [!NOTE]
> 你可以启动**导入数据**向导从 Azure Cosmos DB 仪表板来简化为该数据源索引。 在左侧的导航窗格中，转到**集合** > **添加 Azure Search**吧。

## <a name="data-sources-supported-by-the-import-data-wizard"></a>支持导入数据向导的数据源
导入数据向导支持以下数据源： 

* Azure SQL Database
* 在 Azure VM 上的 SQL Server 关系数据
* Azure Cosmos DB
* Azure Blob 存储
* Azure 表存储

平展数据集是所需的输入。 从单个表、 数据库视图或等效的数据结构，你可以仅导入。 你应在运行向导之前创建此数据结构。

## <a name="connect-to-your-data"></a>连接到你的数据
1. 登录到[Azure 门户](https://portal.azure.com)并打开服务仪表板。 你可以单击**更多的服务**跳转栏搜索当前订阅中的现有"搜索服务"中。 
2. 单击**导入数据**在命令栏到幻灯片上打开导入数据边栏选项卡。  
3. 单击**连接到你的数据**指定一个索引器使用的数据源定义。 对于内订阅数据源，该向导通常可以检测和最大程度减少总体配置要求中读取连接信息。

|  |  |
| --- | --- |
| **现有数据源** |如果你已在你的搜索服务中定义的索引器，你可以选择另一个导入现有数据源定义。 |
| **Azure SQL 数据库** |该页上或者通过 ADO.NET 连接字符串，则可以指定服务名称，数据库用户具有读取权限的凭据和数据库名称。 选择要查看或自定义属性的连接字符串选项。 <br/><br/>页上，必须指定表或提供行集视图。 连接成功，提供下拉列表，以便可以进行选择后，将显示此选项。 |
| **Azure 虚拟机上的 SQL Server** |为连接字符串中指定完全限定的服务名称、 用户 ID 和密码和数据库。 若要使用此数据源，你必须具有以前安装了证书的加密连接的本地存储中。 有关说明，请参阅[SQL 虚拟机连接到 Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。 <br/><br/>页上，必须指定表或提供行集视图。 连接成功，提供下拉列表，以便可以进行选择后，将显示此选项。 |
| **Azure Cosmos DB** |要求包括帐户、 数据库和集合。 集合中的所有文档将都包含在索引中。 你可以定义要平展或筛选行集，或以检测已更改的后续数据刷新操作的文档的查询。 |
| **Azure Blob 存储** |要求包括存储帐户和容器。 或者，如果 blob 名称遵循分组目的的虚拟命名约定，你可以作为容器下的文件夹指定名称的虚拟目录部分。 请参阅[索引 Blob 存储](search-howto-indexing-azure-blob-storage.md)有关详细信息。 |
| **Azure 表存储** |要求包括存储帐户和表名称。 或者，你可以指定查询以检索的表的子集。 请参阅[索引表存储](search-howto-indexing-azure-tables.md)有关详细信息。 |

## <a name="customize-target-index"></a>自定义目标索引
从数据集通常推断出的初步的索引。 添加、 编辑或删除字段，以完成架构。 此外，在字段级别以确定其后续搜索行为设置属性。

1. 在**自定义目标索引**，指定的名称和一个**密钥**用于唯一标识每个文档。 密钥必须是字符串。 如果字段值包含空格或短划线一定要在中设置高级的选项**导入数据**若要禁止显示这些字符的验证检查。
2. 查看和修改其余字段。 字段名称和类型通常填写为你。 之前创建的索引，你可以更改数据类型。 之后更改需要重新生成。
3. 设置每个字段的索引属性：
   
   * 可检索搜索结果中返回的字段。
   * 可筛选允许要在筛选表达式中引用的字段。
   * 可排序允许在排序时要使用的字段。
   * 可分面使分面导航的字段。
   * 可搜索启用的全文搜索。
4. 单击**分析器**选项卡上，如果你想要指定在字段级别的语言分析器。 可以同时指定仅语言分析器。 使用自定义分析器或关键字、 模式中，和等，等的非语言分析器将需要的代码。
   
   * 单击**搜索到**到指定的全文搜索的字段上并启用分析器下拉列表。
   * 选择你想在的分析器。 请参阅[多个语言创建文档的索引](search-language-support.md)有关详细信息。
5. 单击**建议器**若要启用对所选字段的提前键入查询建议。

## <a name="import-your-data"></a>导入数据
1. 在**导入数据**，提供索引器的名称。 回想一下，导入数据向导积是一个索引器。 更高版本，如果你想要查看或编辑它，你将选择它从门户中，而不是通过重新运行该向导。 
2. 指定的计划，基于在其中设置服务区域时区。
3. 设置高级的选项以指定阈值上是否可以继续索引如果文档将被丢弃。 此外，你可以指定是否**密钥**字段可以包含空格、 斜杠。  
4. 单击**确定**要创建索引并导入数据。

你可以监视在门户中的索引。 当已加载文档，文档计数会扩大为已定义的索引。 有时需要几分钟时间门户页后，可以选取最新的更新。

索引是准备查询的所有文档加载时，就会立即。

## <a name="query-an-index-using-search-explorer"></a>查询索引使用搜索资源管理器

此门户包括**搜索资源管理器**，以便您可以查询索引，而无需编写任何代码。 你可以使用[搜索浏览](search-explorer.md)任何索引。

搜索体验取决于默认设置，如[简单语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)和默认 [searchmode 控制查询参数 (https://docs.microsoft.com/rest/api/searchservice/search-documents)。 

结果返回 JSON，详细格式，以便你可以检查整个文档。

## <a name="edit-an-existing-indexer"></a>编辑现有的索引器
如前所述，导入数据向导创建**索引器**，您可以修改为在门户中的独立构造这些。

在服务仪表板中，双击该索引器磁贴以滑出为你的订阅创建的所有索引器的列表。 双击其中一个索引器以运行、 编辑或删除它。 可以将替换另一个现有的索引、 更改数据源，并在索引过程中设置的错误阈值的选项。

## <a name="edit-an-existing-index"></a>编辑现有索引
向导还创建**索引**。 在 Azure 搜索中，结构更新的索引将需要重新生成该索引。 重新生成时，需要删除的索引，重新创建索引使用修改后的架构具有所需的更改和重新加载数据。 结构的更新包括更改数据类型和重命名或删除字段。

不需要重新生成的编辑包括添加新字段，更改评分配置文件，更改建议器，或者更改语言分析器。 请参阅[更新索引](https://msdn.microsoft.com/library/azure/dn800964.aspx)有关详细信息。


## <a name="next-steps"></a>后续步骤
查看这些链接可了解有关索引器的详细信息：

* [索引的 Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [索引 Azure Cosmos DB](search-howto-index-documentdb.md)
* [索引的 Blob 存储](search-howto-indexing-azure-blob-storage.md)
* [索引的表存储](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

