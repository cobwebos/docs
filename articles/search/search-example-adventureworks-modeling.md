---
title: 示例：为 AdventureWorks Inventory 数据库建模 - Azure 搜索
description: 了解如何为关系数据建模，将其转换为平展数据集，以便在 Azure 搜索中进行索引编制和全文本搜索。
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291873"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>示例：为 Azure 搜索的 AdventureWorks Inventory 数据库建模

将结构化数据库内容建模为有效的搜索索引通常不能直接进行。 除了进行计划和更改管理，还必须将源行进行反规范化处理，从表联接状态变为易于搜索的实体。 本文使用在线提供的 AdventureWorks 示例数据，在从数据库到搜索的转换过程中突出显示常见体验。 

## <a name="about-adventureworks"></a>关于 AdventureWorks

如果有 SQL Server 实例，你可能已熟悉 AdventureWorks 示例数据库。 在此数据库提供的表中，有五个表会公开产品信息。

+ **ProductModel**：名称
+ **Product**：名称、颜色、成本、大小、重量、图像、类别（每个行都联接到特定的 ProductModel）
+ **ProductDescription**：说明
+ **ProductModelProductDescription**：区域设置（就具体语言来说，每个行都会将 ProductModel 联接到特定的 ProductDescription）
+ **ProductCategory**：名称、父类别

当前任务是将所有此类数据组合到一个平展的行集中，以便将其引入搜索索引中。 

## <a name="considering-our-options"></a>考虑我们的选项

比较幼稚的做法是为 Product 表中的所有行（根据情况联接）编制索引，因为 Product 表有最具体的信息。 但是，该方法会将搜索索引公开给结果集中发现的重复项。 例如，Road-650 模型提供两种颜色和六种大小。 如果查询“road bikes”，则满眼全是同一模型的 12 个实例，唯一区别是大小和颜色。 其他 6 个特定于公路的模型全都放到了几乎没人关注的搜索位置：第 2 页。

  ![产品列表](./media/search-example-adventureworks/products-list.png "产品列表")
 
请注意，Road-650 模型有 12 个选项。 在搜索索引中，一对多实体行最好是作为多值字段或预先填充了值的字段呈现。

直接将目标索引移到 ProductModel 表并不能解决此问题。 这样做会忽略 Product 表中的重要数据，这些数据仍会在搜索结果中呈现。

## <a name="use-a-collection-data-type"></a>使用 Collection 数据类型

“正确方法”是利用搜索架构功能，该功能在数据库模型中没有直接的对应项：**Collection(Edm.String)**。 如果有一个由单个字符串组成的列表，而不是一个很长的字符串，则使用 Collection 数据类型。 如果有标记或关键字，则对此字段使用 Collection 数据类型。

针对“color”、“size”和“image”定义 **Collection(Edm.String)** 的多值索引字段后，就会保留用于分面和筛选的辅助信息，不会使用重复项来“污染”索引。 类似地，将聚合函数应用到 Product 表的数字字段，为 **minListPrice** 而不是每个产品 **listPrice** 编制索引。

如果索引使用这些结构，则搜索“mountain bikes”时，会显示离散的自行车车型，但又保留重要的元数据，例如颜色、大小和最低价格。 以下屏幕截图提供了说明。

  ![山地自行车搜索示例](./media/search-example-adventureworks/mountain-bikes-visual.png "山地自行车搜索示例")

## <a name="use-script-for-data-manipulation"></a>使用脚本进行数据处理

遗憾的是，此类建模无法只通过 SQL 语句来轻易实现。 可以改用简单的 NodeJS 脚本来加载数据，然后将其映射到易于搜索的 JSON 实体。

最终的数据库搜索映射如下所示：

+ model（Edm.String：可搜索、可筛选、可检索），来自“ProductModel.Name”
+ description_en（Edm.String：可搜索），来自 culture=’en’ 时的模型的“ProductDescription”
+ color（Collection(Edm.String)：可搜索、可筛选、可分面、可检索）：唯一值，来自模型的“Product.Color”
+ size（Collection(Edm.String)：可搜索、可筛选、可分面、可检索）：唯一值，来自模型的“Product.Size”
+ image（Collection(Edm.String)：可检索）：唯一值，来自模型的“Product.ThumbnailPhoto”
+ minStandardCost（Edm.Double：可筛选、可分面、可排序、可检索）：模型的所有“Product.StandardCost”的最小聚合值
+ minListPrice（Edm.Double：可筛选、可分面、可排序、可检索）：模型的所有“Product.ListPrice”的最小聚合值
+ minWeight（Edm.Double：可筛选、可分面、可排序、可检索）：模型的所有“Product.Weight”的最小聚合值
+ products（Collection(Edm.String)：可搜索、可筛选、可分面、可检索）：唯一值，来自模型的“Product.Name”

将 ProductModel 表与 Product 和 ProductDescription 联接以后，使用 [lodash](https://lodash.com/)（或 C# 中的 Linq）来快速地转换结果集：

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

生成的 JSON 如下所示：

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

最后，下面是可返回初始记录集的 SQL 查询。 我使用了 [mssql](https://www.npmjs.com/package/mssql) npm 模块将数据加载到 NodeJS 应用中。

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> 示例：[Azure 搜索中的多层分面分类](search-example-adventureworks-multilevel-faceting.md)


