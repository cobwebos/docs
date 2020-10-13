---
title: 为要导入和编制索引的 SQL 关系数据建模
titleSuffix: Azure Cognitive Search
description: 了解如何为关系数据建模，将其反规范化为平展数据集，以便在 Azure 认知搜索中编制索引和执行全文本搜索。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924514"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>如何为 Azure 认知搜索中要导入和编制索引的 SQL 关系数据建模

Azure 认知搜索接受使用平展行集作为[索引编制管道](search-what-is-an-index.md)的输入。 如果源数据源自 SQL Server 关系数据库中的联接表，你可以参考本文来构造结果集，以及在 Azure 认知搜索索引中为父子关系建模。

为便于演示，我们将基于[演示数据](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)引用一个虚构的酒店数据库。 假设该数据库包括一个 Hotels$ 表（其中包含 50 家酒店），以及一个 Rooms$ 表（其中包含各种类型的提供不同价格和设施的总共 750 间客房）。 表之间存在一对多关系。 在我们的方法中，某个视图将提供返回 50 行（每家酒店各对应一行）以及嵌入到每行中的关联客房详细信息的查询。

   ![酒店数据库中的表和视图](media/index-sql-relational-data/hotels-database-tables-view.png "酒店数据库中的表和视图")


## <a name="the-problem-of-denormalized-data"></a>反规范化数据的问题

使用一对多关系的一个难题在于，基于联接表生成的标准查询将返回反规范化的数据，这在 Azure 认知搜索方案中无法顺利运行。 考虑以下联接酒店和客房的示例。

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
此查询的结果返回所有酒店字段，后接所有客房字段，以及针对每个客房值重复的初步酒店信息。

   ![反规范化数据，添加客房字段后多余的酒店数据](media/index-sql-relational-data/denormalize-data-query.png "反规范化数据，添加客房字段后多余的酒店数据")


尽管此查询表面上可以成功（提供平整行集中的所有数据），但它无法为预期的搜索体验提供正确的文档结构。 在编制索引期间，Azure 认知搜索将为引入的每个行创建一个搜索文档。 如果搜索文档类似于上述结果，则你会看到重复项 - 单单是 Twin Dome 酒店就有七个不同的文档。 根据“佛罗里达州的酒店”进行查询只会返回 Twin Dome 酒店的七条结果，而其他相关酒店则深藏在搜索结果中。

若要获得每家酒店只有一个文档的预期体验，应以适当的粒度提供一个行集，但同时需要提供完整的信息。 幸运的是，采用本文中所述的方法可以轻松实现此目的。

## <a name="define-a-query-that-returns-embedded-json"></a>定义返回嵌入式 JSON 的查询

若要提供预期的搜索体验，数据集应为 Azure 认知搜索中的每个搜索文档包括一行。 在本示例中，我们希望每家酒店只有一行，但同时希望用户能够搜索他们所关心的其他客房相关字段，例如，每晚的价格、床位的尺寸和数量，或者沙滩景观，所有这些信息都是客房详细信息的一部分。

解决方法是捕获嵌套 JSON 格式的客房详细信息，然后将 JSON 结构插入到视图中的字段，如第二步中所述。 

1. 假设你有两个联接表：Hotels$ 和 Rooms$，其中包含 50 家酒店和 750 间客房的详细信息，这两个表已在 HotelID 字段中联接。 这些表各自包含 50 家酒店和 750 间相关的客房。

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

2. 创建一个视图，其中包含父表 (`SELECT * from dbo.Hotels$`) 中的所有字段，加上包含嵌套查询的输出的新 *Rooms* 字段。 `SELECT * from dbo.Rooms$` 中的 **FOR JSON AUTO** 子句将输出结构化为 JSON。 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   以下屏幕截图显示了生成的视图，底部是 *Rooms* nvarchar 字段。 “客房”字段仅存在于 HotelRooms 视图中。

   ![HotelRooms 视图](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 视图")

1. 运行 `SELECT * FROM dbo.HotelRooms` 检索行集。 此查询返回 50 行（每家酒店对应一行），以及 JSON 集合形式的关联客房信息。 

   ![HotelRooms 视图中的行集](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms 视图中的行集")

此行集现已准备好导入 Azure 认知搜索。

> [!NOTE]
> 此方法假设嵌入的 JSON 低于 [SQL Server 的最大列大小限制](/sql/sql-server/maximum-capacity-specifications-for-sql-server)。 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>为一对多关系的“多”端使用复杂集合

在 Azure 认知搜索端，创建一个使用嵌套式 JSON 为一对多关系建模的索引架构。 在上一部分创建的结果集通常对应于下面提供的索引架构（为简洁起见，我们截掉了一些字段）。

以下示例类似于[如何为复杂数据类型建模](search-howto-complex-data-types.md#creating-complex-fields)中的示例。 *Rooms* 结构（本文的重点所在）位于索引的名为 *hotels* 的字段集合中。 此示例还显示了 *Address* 的复杂类型，该类型与 *Rooms* 的不同之处在于，它包含一组固定的项，而不是集合中允许的多个任意数量的项。

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

在给定上述结果集和上述索引架构后，你便获得了成功完成索引编制操作所需的全部组件。 平展数据集符合索引编制要求，同时可以保留详细信息。 在 Azure 认知搜索索引中，搜索结果很容易变成基于酒店的实体，同时可以保留各个客房及其属性的上下文。

## <a name="next-steps"></a>后续步骤

可以使用自己的数据集通过[导入数据向导](search-import-data-portal.md)来创建和加载索引。 该向导将检测嵌入的 JSON 集合（例如 *Rooms* 中包含的集合），并推断包含复杂类型集合的索引架构。 

  ![导入数据向导推断的索引](media/index-sql-relational-data/search-index-rooms-complex-collection.png "导入数据向导推断的索引")

请尝试学习以下快速入门，以了解导入数据向导的基本步骤。

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 门户创建搜索索引](search-get-started-portal.md)