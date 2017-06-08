---
title: "在门户中将数据导入 Azure 搜索 | Microsoft 文档"
description: "在 Azure 门户中使用 Azure 搜索的“导入数据”向导从 NoSQL Azure Cosmos DB、Blob 存储、表存储、SQL 数据库和 Azure VM 上的 SQL Server 搜索 Azure 数据。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a3e6dd66197a17bfdc80c04130e198b787692a58
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="import-data-to-azure-search-using-the-portal"></a>使用门户将数据导入到 Azure 搜索中
Azure 门户在“Azure 搜索”仪表板上提供了“导入数据”向导，用于将数据加载到索引中。 

  ![命令栏中的“导入数据”][1]

在内部，该向导配置并调用 *索引器*，自动执行编制索引过程的几个步骤： 

* 连接到同一 Azure 订阅的外部数据源
* 基于源数据结构生成可修改的索引架构
* 使用从数据源检索的行集将 JSON 文档加载到索引中

可以使用 Azure Cosmos DB 中的示例数据试用此工作流。 有关说明，请访问 [开始在 Azure 门户中使用 Azure 搜索](search-get-started-portal.md) 。

> [!NOTE]
> 可从 Azure Cosmos DB 仪表板启动**导入数据**向导，进而简化该数据源的索引。 在左侧导航栏中，转到“集合” > “添加 Azure 搜索”开始操作。

## <a name="data-sources-supported-by-the-import-data-wizard"></a>“导入数据”向导支持的数据源
“导入数据”向导支持以下数据源： 

* Azure SQL 数据库
* Azure VM 上的 SQL Server 关系数据
* Azure Cosmos DB
* Azure Blob 存储
* Azure 表存储

平展数据集是必需的输入。 只能从单个表、数据库视图或等效的数据结构导入。 在运行向导之前，应创建此数据结构。

## <a name="connect-to-your-data"></a>连接到数据
1. 登录到 [Azure 门户](https://portal.azure.com)，并打开服务仪表板。 可以单击跳转栏中的“更多服务”，搜索当前订阅中的现有“搜索服务”。 
2. 单击命令栏上的“导入数据”  ，滑动打开“导入数据”边栏选项卡。  
3. 单击“连接到数据”  ，指定索引器使用的数据源定义。 对于订阅内数据源，该向导通常可以检测并读取连接信息，并将整个配置要求降至最低。

|  |  |
| --- | --- |
| **现有数据源** |如果已在搜索服务中定义索引器，则可以为另一个导入选择现有数据源定义。 |
| **Azure SQL 数据库** |可以在此页上或通过 ADO.NET 连接字符串，指定服务名称、具有读取权限的数据库用户的凭据和数据库名称。 选择要查看或自定义属性的连接字符串选项。 <br/><br/>必须在此页上指定提供行集的表或视图。 连接成功后会显示此选项，并提供下拉列表以便可以进行选择。 |
| **Azure VM 上的 SQL Server** |指定完全限定的服务名称、用户 ID 和密码以及数据库作为连接字符串。 若要使用此数据源，以前必须已在加密连接的本地存储中安装了证书。 如需说明，请参阅[与 Azure 搜索的 SQL VM 连接](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。 <br/><br/>必须在此页上指定提供行集的表或视图。 连接成功后会显示此选项，并提供下拉列表以便可以进行选择。 |
| **Azure Cosmos DB** |要求包括帐户、数据库和集合。 集合中的所有文档都将包含在索引中。 可以定义查询以平展或筛选行集，或者检测已更改的文档以便进行后续数据刷新操作。 |
| **Azure Blob 存储** |要求包括存储帐户和容器。 （可选）如果 blob 名称遵循用于分组的虚拟命名约定，可以将名称的虚拟目录部分指定为容器下的某个文件夹。 有关详细信息，请参阅[为 Blob 存储编制索引](search-howto-indexing-azure-blob-storage.md)。 |
| **Azure 表存储** |要求包括存储帐户和表名。 （可选）可以指定一个查询来检索表的子集。 有关详细信息，请参阅[为表存储编制索引](search-howto-indexing-azure-tables.md)。 |

## <a name="customize-target-index"></a>自定义目标索引
通常从数据集推断出初步索引。 添加、编辑或删除字段以完成架构。 此外，还在字段级别设置属性以确定其后续搜索行为。

1. 在“自定义目标索引”中，指定用于唯一标识每个文档的名称和**键**。 该键必须是字符串。 如果字段值包含空格或短划线，请务必在“导入数据”中  设置高级选项，以禁止对这些字符进行验证检查。
2. 查看和修改剩余字段。 通常已为用户填充字段名称和类型。 在创建索引之前，可以更改数据类型。 在之后进行更改需要重新生成。
3. 为每个字段设置索引属性：
   
   * “可检索”可在搜索结果中返回该字段。
   * “可筛选”允许在筛选表达式中引用该字段。
   * “可排序”允许在排序中使用该字段。
   * “可查找”为分面导航启用该字段。
   * “可搜索”可启用全文搜索。
4. 如果要在字段级别指定语言分析器，请单击“分析器”  选项卡。 目前，只能指定语言分析器。 使用自定义分析器或非语言分析器（如关键字、模式等）将需要代码。
   
   * 单击“可搜索”  可指定对字段的全文搜索并启用“分析器”下拉列表。
   * 选择所需的分析器。 有关详细信息，请参阅[为多语言文档创建索引](search-language-support.md)。
5. 单击“建议器”  可对所选字段启用“提前键入查询建议”。

## <a name="import-your-data"></a>导入数据
1. 在“导入数据” 中，提供索引器的名称。 请注意，“导入数据”向导的产品是一个索引器。 稍后，如果想要查看或编辑它，可以从门户中选择它，而不是重新运行该向导。 
2. 指定计划，该计划基于预配服务的区域时区。
3. 设置高级选项，以针对如果丢弃了文档，编制索引操作是否可以继续指定阈值。 此外，还可以指定“键”字段是否  可以包含空格和斜杠。  
4. 单击“确定”创建索引并导入数据。

可以在门户中监视索引。 加载文档以后，针对所定义索引的文档计数会增加。 有时候，门户页选取最新更新需要几分钟时间。

加载所有文档以后，即可通过索引进行查询。

## <a name="query-an-index-using-search-explorer"></a>使用搜索资源管理器查询索引

门户中提供了“搜索资源管理器”用于查询索引，无需编写任何代码。 可以针对任何索引使用[搜索资源管理器](search-explorer.md)。

搜索体验取决于默认设置，例如[简单语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)和默认的 [searchMode 查询参数](https://docs.microsoft.com/rest/api/searchservice/search-documents)。 

结果将以详细的 JSON 格式返回，方便用户检查整个文档。

## <a name="edit-an-existing-indexer"></a>编辑现有索引器
如前所述，导入数据向导会创建**索引器**，用户可以在门户中将其修改为独立构造。

在服务仪表板中，双击“索引器”磁贴可滑出为订阅创建的所有索引器的列表。 双击某一索引器可运行、编辑或删除它。 在索引编制过程中，可以将索引替换为另一个现有索引、更改数据源和设置错误阈值选项。

## <a name="edit-an-existing-index"></a>编辑现有索引器
向导还创建了**索引**。 在 Azure 搜索中，对索引进行结构更新将需要重新生成该索引。 重新生成时，需要删除该索引，并使用修改后的架构（包含所要的更改）重新创建索引，然后重新加载数据。 结构更新包括更改数据类型和重命名或删除字段。

不需要重新生成的编辑包括添加新字段、更改评分配置文件、更改建议器或更改语言分析器。 有关详细信息，请参阅 [更新索引](https://msdn.microsoft.com/library/azure/dn800964.aspx) 。


## <a name="next-steps"></a>后续步骤
若要了解有关索引器的详细信息，请查看以下链接：

* [为 Azure SQL 数据库编制索引](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [为 Azure Cosmos DB 编制索引](search-howto-index-documentdb.md)
* [为 Blob 存储编制索引](search-howto-indexing-azure-blob-storage.md)
* [为表存储编制索引](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png


