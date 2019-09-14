---
title: 为导入和索引建模 SQL 关系数据-Azure 搜索
description: 了解如何针对 Azure 搜索中的索引和全文搜索，对关系数据进行建模，并将其取消标准化为平面结果集。
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: heidist
ms.openlocfilehash: f6cc119387482e4f5403e91de98916cbe2aaff2a
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963494"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-search"></a>如何为 Azure 搜索中的导入和索引建模关系 SQL 数据

Azure 搜索接受平面行集作为[索引管道](search-what-is-an-index.md)的输入。 如果源数据源自 SQL Server 关系数据库中的联接表，本文将介绍如何构造结果集，以及如何在 Azure 搜索索引中对父子关系建模。

作为说明，我们将根据[演示数据](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)来参考假设的酒店数据库。 假定数据库由一个包含50酒店的酒店 $ 表和一个房间 $ 表，其中包含不同类型、费率和数量的房间，总共750个房间。 表之间存在一对多关系。 在我们的方法中，视图将提供返回50行的查询，每个宾馆占一行，并将关联的空间详细信息嵌入到每一行。

   ![宾馆数据库中的表和视图](media/index-sql-relational-data/hotels-database-tables-view.png "宾馆数据库中的表和视图")


## <a name="the-problem-of-denormalized-data"></a>非规范化数据的问题

使用一对多关系的难题之一是，在联接的表上构建的标准查询将返回不规范的数据，这在 Azure 搜索方案中无法正常工作。 请考虑以下用于联接旅馆和房间的示例。

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
此查询的结果返回所有宾馆字段，后跟所有房间字段，并为每个房间值重复使用初步酒店信息。

   非![规范化数据，添加会议室字段时的冗余宾馆数据]非(media/index-sql-relational-data/denormalize-data-query.png "规范化数据，添加会议室字段时的冗余宾馆数据")


虽然此查询在表面上成功（提供所有数据都在一个平面行集中），但它在为预期的搜索体验交付正确的文档结构时失败。 在编制索引期间，Azure 搜索将为每个行引入创建一个搜索文档。 如果搜索文档看起来像上述结果，就会发现重复-7 个单独的单独文档用于克隆的凸透镜。 "对佛罗里达州的酒店" 的查询将只为 "克隆" 凸透镜返回7个结果，并将其他相关饭店推送到搜索结果中。

若要获得每个旅馆每个旅馆的预期体验，应以正确的粒度提供一个行集，但需提供完整信息。 幸运的是，您可以通过采用本文中的方法轻松实现此目的。

## <a name="define-a-query-that-returns-embedded-json"></a>定义返回嵌入 JSON 的查询

若要传递预期的搜索体验，你的数据集应在 Azure 搜索中的每个搜索文档中各占一行。 在我们的示例中，我们希望每个酒店都有一行，但我们也希望用户能够搜索他们关心的其他房间相关的字段，例如每夜费率、床位的大小和数量，或海滩的视图，所有这些都是会议室详细信息的一部分。

解决方法是将聊天室详细信息捕获为嵌套 JSON，然后将 JSON 结构插入视图中的字段，如第二步所示。 

1. 假设您有两个联接的表：宾馆 $ 和房间 $，其中包含50酒店和750房间的详细信息，并且已加入 HotelID 字段。 这些表分别包含50酒店和750相关的会议室。

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. 创建一个由父表（`SELECT * from dbo.Hotels$`）中的所有字段组成的视图，其中添加了一个包含嵌套查询输出的新的*聊天室*字段。 **FOR json AUTO**子句`SELECT * from dbo.Rooms$`将输出构造为 JSON。 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   下面的屏幕截图显示了生成的视图，底部有*会议室*nvarchar 字段。 *会议室*字段仅存在于 HotelRooms 视图中。

   ![HotelRooms 视图](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 视图")

1. 运行`SELECT * FROM dbo.HotelRooms`以检索行集。 此查询将返回50行（每个宾馆一个），并将关联的会议室信息作为 JSON 集合。 

   ![HotelRooms 视图中的行集](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms 视图中的行集")

此行集现已准备好导入到 Azure 搜索。

> [!NOTE]
> 此方法假定嵌入的 JSON 小于 SQL Server 的[最大列大小限制](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server)。 如果数据不合适，可以尝试编程方法，如示例中[所示：为 Azure 搜索](search-example-adventureworks-modeling.md)的 AdventureWorks 清单数据库建模。

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>为一对多关系的 "多" 方使用复杂集合

在 Azure 搜索侧，使用嵌套 JSON 创建模型为一对多关系的索引架构。 您在上一节中创建的结果集通常对应于下面提供的索引架构（为了简洁起见，我们剪切了某些字段）。

下面的示例与[如何为复杂数据类型建模](search-howto-complex-data-types.md#creating-complex-fields)中的示例类似。 已成为本文重点的*会议室*结构位于名为 "*酒店*" 的索引的 "字段" 集合中。 此示例还显示了*地址*的复杂类型，这不同于*房间*，因为它包含一组固定的项，而不是集合中允许的多个任意数量的项。

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

给定上一个结果集和上述索引架构后，您将拥有成功索引操作所需的所有组件。 平展数据集满足索引要求，但仍保留详细信息。 在 Azure 搜索索引中，搜索结果将轻松进入基于宾馆的实体，同时保留各个房间及其属性的上下文。

## <a name="next-steps"></a>后续步骤

您可以使用[导入数据向导](search-import-data-portal.md)对行集编制索引，这类似于本文中所述的行集。 向导将在*聊天室*中检测嵌入的 JSON 集合，并推断提供适当复杂类型集合的索引架构。 

  "![导入数据" 向导推导的索引]"(media/index-sql-relational-data/search-index-rooms-complex-collection.png "导入数据\" 向导推导的索引")

若要完成导入并创建可用的索引，必须自行选择密钥并设置属性。 如果你不熟悉此向导，请尝试以下快速入门来了解基本步骤。

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 门户创建搜索索引](search-get-started-portal.md)