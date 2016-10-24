<properties 
	pageTitle="Azure 搜索入门 | Microsoft Azure | 开始使用 Azure 搜索 | DocumentDB | 云搜索服务" 
	description="使用本教程演练创建第一个 Azure 搜索解决方案。了解如何使用 DocumentDB 数据创建 Azure 搜索索引。这是一个使用“导入数据”向导、基于门户且无代码的练习。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="jhubbard" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.date="08/29/2016" 
	ms.author="heidist"/>

# 门户中的 Azure 搜索入门

此无代码的简介让用户通过内置于门户中的功能开始使用 Microsoft Azure 搜索。

本教程采用[示例 Azure DocumentDB 数据库](#apdx-sampledata)，该数据库可以使用数据和指令简单地创建，但用户也可以对 DocumentDB 或 SQL 数据库中的现有数据应用这些步骤。

> [AZURE.NOTE] 本入门教程需要 [Azure 订阅](../../includes/free-trial-note.md)和 [Azure 搜索服务](search-create-service-portal.md)。
 
## 找到服务

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 打开 Azure 搜索服务的服务仪表板。可通过以下几种方法找到仪表板。
	- 在跳转栏中，单击“搜索服务”。跳转栏会列出订阅中预配的每个服务。如果搜索服务已定义，会在列表中看到“搜索服务”。
	- 在跳转栏中，单击“浏览”，然后在搜索框中键入“搜索”，生成订阅中已创建的所有搜索服务的列表。

## 检查空间

很多客户开始使用免费服务。此版本限制为三个索引、三个数据源和三个索引器。在开始之前，请确保有空间存储额外的项目。本演练将每个对象都创建一个。

## 创建索引和加载数据

搜索查询将循环访问*索引*，索引中包含可搜索数据、元数据，以及用于优化某些搜索行为的构造。第一步，定义并填充索引。

有多种方法可创建索引。如果数据是在 Azure 搜索可以搜索的存储中（如 Azure SQL 数据库、Azure VM 上 SQL Server 或 DocumentDB），可以使用*索引器*非常轻松地创建和填充索引。

为了使此任务基于门户，假定可以使用索引器通过“导入数据”向导搜索 DocumentDB 中的数据。

在继续之前，请先创建本教程要使用的[示例 DocumentDB 数据库](#apdx-sampledata)，然后再返回到本部分完成以下步骤。

<a id="defineDS">
#### 步骤 1：定义数据源

1. 在 Azure 搜索服务仪表板上，单击命令栏中的“导入数据”，以启动用于创建和填充索引的向导。

  ![][7]

2. 在向导中，单击“数据源”>“DocumentDB”>“名称”，键入数据源的名称。数据源是 Azure 搜索中的连接对象，可用于其他索引器。创建数据源后，它将作为服务中的“现有数据源”提供。

3. 选择现有的 DocumentDB 帐户，以及数据库和集合。如果使用所提供的示例数据，数据源定义将如下所示：

  ![][2]

请注意，将跳过该查询。这是因为此时不想实现数据集中的更改跟踪。如果数据集中的字段跟踪更新记录时的时间，可以将 Azure 搜索索引器配置为对索引的选择性更新使用更改跟踪。

单击“确定”，完成向导的此步骤。

#### 步骤 2：定义索引

仍在向导中，单击“索引”，然后查看一下用来创建 Azure 搜索索引的设计图面。索引至少需要一个名称和一个字段集合（其中一个字段标记为文档键）。由于使用的是 DocumentDB 数据集，因此向导会自动检测字段，并且索引会预先加载字段和数据类型分配。

  ![][3]

虽然配置了字段和数据类型，但仍需要分配属性。字段列表顶部的复选框为*索引属性*，用于控制如何使用字段。

- “可检索”意味着该字段将显示在搜索结果列表中。清除此复选框即可将单个字段标记为关闭搜索结果限制，例如当字段仅用于筛选器表达式时。
- “可筛选”、“可排序”和“可查找”确定字段是否可用于筛选器、排序或方面导航结构。
- “可搜索”意味着该字段将包括在全文搜索中。字符串通常可搜索。数值字段和布尔字段通常标记为不可搜索。

离开此页之前，请将索引中的字段标记为使用以下选项（“可检索”、“可搜索”等）。大多数字段是“可检索”。大多数字符串字段是“可搜索”（不需要将键设为可搜索）。几个字段（如“genre”、orderableOnline、“rating”和“tags”）也是“可筛选”、“可排序”和“可查找”。
	
字段 | 类型 | 选项 |
------|------|---------|
id | Edm.String | |
albumTitle | Edm.String | 可检索、可搜索 |
albumUrl | Edm.String | 可检索、可搜索 |
genre | Edm.String | 可检索、可搜索、可筛选、可排序、可查找 |
genreDescription | Edm.String | 可检索、可搜索 |
artistName | Edm.String | 可检索、可搜索 |
orderableOnline | Edm.Boolean | 可检索、可筛选、可排序、可查找 |
标记 | 集合 (Edm.String) | 可检索、可筛选、可查找 |
price | Edm.Double | 可检索、可筛选、可查找 |
margin | Edm.Int32 | |
rating | Edm.Int32 | 可检索、可筛选、可排序、可查找 |
inventory | Edm.Int32 | 可检索 |
lastUpdated | Edm.DateTimeOffset | |

作为对比，下面的屏幕截图是对根据上表中的规范构建的索引的说明。

 ![][4]

单击“确定”，完成向导的此步骤。

#### 步骤 3：定义索引器

仍然在“导入数据”向导中，单击“索引器”>“名称”，键入索引器的名称，对所有其他值使用默认值。此对象定义一个可执行过程。创建它后，可将其放置在定期计划上，但现在单击“确定”时将使用默认选项立即运行一次索引器。

导入数据条目应已全部填充并准备就绪。

  ![][5]

若要运行向导，请单击“确定”，以开始导入并关闭向导。

## 检查进度

若要检查进度，请返回到服务仪表板，向下滚动，然后双击“索引器”磁贴以打开索引器列表。应在列表中看到刚创建的索引器，并应看到状态指示“正在进行”或“成功”，以及已在 Azure 搜索中编制索引的文档数。

  ![][6]

## 查询索引

现在已有一个可以查询的搜索索引。

**搜索浏览器**是内置于门户中的查询工具。它提供了搜索框，以便可以验证搜索输入是否返回了所需的数据。

1. 单击命令栏上的“搜索浏览器”。
2. 请注意哪个索引处于活动状态。如果该索引不是刚创建的索引，请在命令栏中单击“更改索引”，选择所需的索引。
2. 将搜索框留空，然后单击“搜索”按钮执行通配符搜索，以返回所有文档。
3. 输入几个全文搜索查询。可以通过通配符搜索查看结果，以熟悉要查询的艺术家、专辑和流派。
4. 请使用[本文末尾提供的示例](https://msdn.microsoft.com/library/azure/dn798927.aspx)尝试其他查询语法以获得想法，并修改查询以使用可能会在索引中找到的搜索字符串。

## 后续步骤

运行一次该向导后，可以返回查看或修改单个组件：索引、索引器或数据源。不允许对索引进行某些编辑操作（如更改字段数据类型），但大多数属性和设置是可修改的。若要查看单个组件，请单击仪表板上的“索引”、“索引器”或“数据源”磁贴，显示现有对象的列表。

若要详细了解本文中提到的其他功能，请访问以下链接：

- [索引器](search-indexer-overview.md)
- [创建索引（包括索引属性的详细说明）](https://msdn.microsoft.com/library/azure/dn798941.aspx)
- [搜索浏览器](search-explorer.md)
- [搜索文档（包括查询语法的示例）](https://msdn.microsoft.com/library/azure/dn798927.aspx)

可以对其他数据源（如 Azure SQL 数据库或 Azure 虚拟机上的 SQL Server）使用“导入数据”向导，尝试此同一工作流。

> [AZURE.NOTE] 新发布的索引器支持搜索 Azure Blob 存储，但该功能处于预览状态，尚不是门户选项。若要试用该索引器，需要编写代码。有关详细信息，请参阅 [Indexing Azure Blob storage in Azure Search](search-howto-indexing-azure-blob-storage.md)（在 Azure 搜索中为 Azure Blob 存储编制索引）。<a id="apdx-sampledata"></a>


## 附录：在 DocumentDB 中创建示例数据

本部分在 DocumentDB 中创建一个小型数据库，可使用它完成本教程中的任务。

下面的说明提供了一般指南，但并不详尽。如果对于 DocumentDB 门户导航或任务需要更多帮助，可以参考 DocumentDB 文档，但所需的大多数命令在仪表板顶部的服务命令栏中或在数据库边栏选项卡中。

  ![][1]

### 为本教程创建 musicstoredb

1. [单击此处](https://github.com/HeidiSteen/azure-search-get-started-sample-data)可下载一个 ZIP 文件，其中包含音乐商店 JSON 数据文件。我们为此数据集提供了 246 个 JSON 文档。
2. 将 DocumentDB 添加到订阅，然后打开服务仪表板。
2. 单击“添加数据库”，创建 ID 为 `musicstoredb` 的新数据库。创建后，此数据库将显示在该页下部的数据库磁贴中。
2. 单击数据库名称，打开数据库边栏选项卡。
3. 单击“添加集合”，创建 ID 为 `musicstorecoll` 的集合。
3. 单击“文档资源管理器”。
4. 单击“上载”。
5. 在“上载文档”中，导航到包含前面下载的 JSON 文件的本地文件夹。以每批 100 个或更少文件选择 JSON 文件。
	- 386\.json
	- 387\.json
	- . . .
	- 486\.json
6. 重复此操作以获取下一批文件，直到已上载最后一个文件 669.json。
7. 单击“查询浏览器”，验证上载的数据是否符合文档资源管理器的上载要求。

执行此操作的简单方法是使用默认查询，但也可以修改默认查询使其选择前 300 个项目（此数据集中的项目少于 300 个）。

应收到这样的 JSON 输出：从编号 386 的文档开始，到编号 669 的文档结束。加载数据后，便可以[返回到本演练中的步骤](#defineDS)，使用“导入数据”向导生成索引。


<!--Image references-->
[1]: ./media/search-get-started-portal/AzureSearch-GetStart-Docdbmenu1.png
[2]: ./media/search-get-started-portal/AzureSearch-GetStart-DataSource.png
[3]: ./media/search-get-started-portal/AzureSearch-GetStart-DefaultIndex.png
[4]: ./media/search-get-started-portal/AzureSearch-GetStart-FinishedIndex.png
[5]: ./media/search-get-started-portal/AzureSearch-GetStart-ImportReady.png
[6]: ./media/search-get-started-portal/AzureSearch-GetStart-IndexerList.png
[7]: ./media/search-get-started-portal/search-data-import-wiz-btn.png

<!---HONumber=AcomDC_0921_2016-->