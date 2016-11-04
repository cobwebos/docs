---
title: 在 Azure 门户中使用索引器将数据导入到 Azure 搜索中 | Microsoft Docs
description: 在 Azure 门户中使用 Azure 搜索的“导入数据”向导从 Azure Blob 存储、表存储、SQL 数据库和 Azure VM 上的 SQL Server 搜索数据。
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: Azure Portal

ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: heidist

---
# 使用门户将数据导入到 Azure 搜索中
Azure 门户在“Azure 搜索”仪表板上提供了“导入数据”向导，用于将数据加载到索引中。

  ![命令栏中的“导入数据”][1]

在内部，该向导配置并调用*索引器*，自动执行编制索引过程的几个步骤：

* 连接到当前 Azure 订阅的外部数据源
* 基于源数据结构自动生成索引架构
* 基于从数据源检索的行集创建文档
* 将文档上载到搜索服务中的索引

可以使用 DocumentDB 中的示例数据试用此工作流。有关说明，请访问[开始在 Azure 门户中使用 Azure 搜索](search-get-started-portal.md)。

## “导入数据”向导支持的数据源
索引自动化和工具可用于以下数据源：

* Azure SQL 数据库
* Azure VM 上的 SQL Server 关系数据
* Azure DocumentDB
* Azure Blob 存储（处于预览状态）
* Azure 表存储（处于预览状态）

平展数据集是必需的输入。只能从单个表、数据库视图或等效的数据结构导入。在运行向导之前，应创建此数据结构。

请注意，有几个索引器仍处于预览状态，这意味着索引器定义由预览版的 API 提供支持。有关详细信息和链接，请参阅[索引器概述](search-indexer-overview.md)。

## 连接到数据
1. 登录到 [Azure 门户](https://portal.azure.com)，并打开服务仪表板。可以单击跳转栏中的“搜索服务”，显示当前订阅中的现有服务。
2. 单击命令栏上的“导入数据”，滑动打开“导入数据”边栏选项卡。
3. 单击“连接到数据”，指定索引器使用的数据源定义。对于订阅内数据源，该向导通常可以检测并读取连接信息，并将整个配置要求降至最低。

|  |  |
| --- | --- |
| **现有数据源** |如果已在搜索服务中定义索引器，则可以为另一个导入选择现有数据源定义。 |
| **Azure SQL 数据库** |可以在此页上或通过 ADO.NET 连接字符串，指定服务名称、具有读取权限的数据库用户的凭据和数据库名称。选择要查看或自定义属性的连接字符串选项。<br/><br/>必须在此页上指定提供行集的表或视图。连接成功后会显示此选项，并提供下拉列表以便可以进行选择。 |
| **Azure VM 上的 SQL Server** |指定完全限定的服务名称、用户 ID 和密码以及数据库作为连接字符串。若要使用此数据源，以前必须已在加密连接的本地存储中安装了证书。<br/><br/>必须在此页上指定提供行集的表或视图。连接成功后会显示此选项，并提供下拉列表以便可以进行选择。 |
| **DocumentDB** |要求包括帐户、数据库和集合。集合中的所有文档都将包含在索引中。可以定义查询以平展或筛选行集，或者检测已更改的文档以便进行后续数据刷新操作。 |
| **Azure Blob 存储** |要求包括存储帐户和容器。（可选）如果 blob 名称遵循用于分组的虚拟命名约定，可以将名称的虚拟目录部分指定为容器下的某个文件夹。有关详细信息，请参阅 [Indexing Blob Storage (preview)](search-howto-indexing-azure-blob-storage.md)（为 Blob 存储（预览版）编制索引）。 |
| **Azure 表存储** |要求包括存储帐户和表名。（可选）可以指定一个查询来检索表的子集。有关详细信息，请参阅 [Indexing Table Storage (preview)](search-howto-indexing-azure-tables.md)（为表存储（预览版）编制索引）。 |

## 自定义目标索引
通常从数据集推断出初步索引。添加、编辑或删除字段以完成架构。此外，还在字段级别设置属性以确定其后续搜索行为。

1. 在“自定义目标索引”中，指定用于唯一标识每个文档的名称和**键**。该键必须是字符串。如果字段值包含空格或短划线，请务必在“导入数据”中设置高级选项，以禁止对这些字符进行验证检查。
2. 查看和修改剩余字段。通常已为用户填充字段名称和类型。用户可以更改数据类型。
3. 为每个字段设置索引属性：
   
   * “可检索”可在搜索结果中返回该字段。
   * “可筛选”允许在筛选表达式中引用该字段。
   * “可排序”允许在排序中使用该字段。
   * “可查找”为分面导航启用该字段。
   * “可搜索”可启用全文搜索。
4. 如果要在字段级别指定语言分析器，请单击“分析器”选项卡。目前，只能指定语言分析器。使用自定义分析器或非语言分析器（如关键字、模式等）将需要代码。
   
   * 单击“可搜索”可指定对字段的全文搜索并启用“分析器”下拉列表。
   * 选择所需的分析器。有关详细信息，请参阅[为多语言文档创建索引](search-language-support.md)。
5. 单击“建议器”可对所选字段启用“提前键入查询建议”。

## 导入数据
1. 在“导入数据”中，提供索引器的名称。请注意，“导入数据”向导的产品是一个索引器。稍后，如果想要查看或编辑它，可以从门户中选择它，而不是重新运行该向导。
2. 指定计划，该计划基于预配服务的区域时区。
3. 设置高级选项，以针对如果丢弃了文档，编制索引操作是否可以继续指定阈值。此外，还可以指定“键”字段是否可以包含空格和斜杠。

## 编辑现有索引器
在服务仪表板中，双击“索引器”磁贴可滑出为订阅创建的所有索引器的列表。双击某一索引器可运行、编辑或删除它。在索引编制过程中，可以将索引替换为另一个现有索引、更改数据源和设置错误阈值选项。

## 编辑现有索引器
在 Azure 搜索中，对索引的结构更新需要重新生成该索引，这包括删除该索引、重新创建该索引以及重新加载数据。结构更新包括更改数据类型和重命名或删除字段。

不需要重新生成的编辑包括添加新字段、更改评分配置文件、更改建议器或更改语言分析器。有关详细信息，请参阅[更新索引](https://msdn.microsoft.com/library/azure/dn800964.aspx)。

## 后续步骤
若要了解有关索引器的详细信息，请查看以下链接：

* [Indexing Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)（为 Azure SQL 数据库编制索引）
* [Indexing DocumentDB](../documentdb/documentdb-search-indexer.md)（为 DocumentDB 编制索引）
* [Indexing Blob Storage (preview)](search-howto-indexing-azure-blob-storage.md)（为 Blob 存储（预览版）编制索引）
* [Indexing Table Storage (preview)](search-howto-indexing-azure-tables.md)（为表存储（预览版）编制索引）

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

<!---HONumber=AcomDC_0921_2016-->