---
title: "添加必应搜索连接器逻辑应用 | Microsoft Docs"
description: "使用 REST API 参数的必应搜索连接器概述"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: a7f530e8-1573-4612-8899-c9c84aa2de6d
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: dbb920f212d46365233e83ba1e0a0ae99917e405


---
# <a name="get-started-with-the-bing-search-connector"></a>必应搜索连接器入门
连接到必应搜索，搜索资讯、视频等。 通过必应搜索，你可以： 

* 根据从搜索中获取的数据生成你的业务流。 
* 使用搜索图像、搜索资讯等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，你可以搜索视频，然后使用 Twitter 将该视频发布到 Twitter 源。

若要在逻辑应用中添加操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
必应搜索包括以下操作。 没有任何触发器。 

| 触发器 | 操作 |
| --- | --- |
| 无 |<ul><li>搜索 Web</li><li>搜索视频</li><li>搜索图像</li><li>搜索资讯</li><li>搜索相关内容</li><li>搜索拼写</li><li>搜索全部内容</li></ul> |

所有连接器都支持采用 JSON 和 XML 格式的数据。

## <a name="swagger-rest-api-reference"></a>Swagger REST API 参考
适用于版本：1.0。

### <a name="search-web"></a>搜索 Web
从必应搜索检索网站。  
```GET: /Web```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |
| webFileType |字符串 |否 |查询 |无 |要缩小搜索范围的文件类型（示例：“文档”） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="search-videos"></a>搜索视频
从必应搜索检索视频。  
```GET: /Video```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |
| videoFilters |字符串 |否 |查询 |无 |根据大小、纵横比、颜色、样式、人物或其中任何组合的筛选器搜索。  有效值： <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例如：“Duration:Short+Resolution:High” |
| videoSortBy |字符串 |否 |查询 |无 |结果的排序顺序。 有效值： <ul><li>日期</li><li>相关性</li></ul> <p>日期排序顺序暗指降序。</p> |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="search-images"></a>搜索图像
从必应搜索检索图像。  
```GET: /Image```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |
| imageFilters |字符串 |否 |查询 |无 |根据大小、纵横比、颜色、样式、人物或其中任何组合的筛选器搜索。 有效值： <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[宽度]</li><li>Size:Height:[高度]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例如：“Size:Small+Aspect:Square” |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="search-news"></a>搜索资讯
从必应搜索检索资讯结果。  
```GET: /News```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |
| newsSortBy |字符串 |否 |查询 |无 |结果的排序顺序。 有效值： <ul><li>日期</li><li>相关性</li></ul> <p>日期排序顺序暗指降序。</p> |
| newsCategory |字符串 |否 |查询 | |缩小搜索范围的资讯类别（示例：“rt_Business”） |
| newsLocationOverride |字符串 |否 |查询 |无 |检测到的必应位置覆盖。 此参数仅适用于 en-US 市场。 输入格式为 US./<state />（示例：“US.WA”） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="search-spellings"></a>搜索拼写
检索拼写建议。  
```GET: /SpellingSuggestions```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="search-related"></a>搜索相关内容
从必应搜索检索相关搜索结果。  
```GET: /RelatedSearch```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

### <a name="search-all"></a>搜索全部内容
从必应搜索检索所有网站、视频、图像等。  
```GET: /CompositeSearch```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| query |字符串 |是 |查询 |无 |要搜索的文本（示例：“Xbox”） |
| maxResult |integer |否 |查询 |无 |要返回的最大结果数 |
| startOffset |integer |否 |查询 |无 |要跳过的结果数 |
| adultContent |字符串 |否 |查询 |无 |成人内容筛选器。 有效值： <ul><li>关闭</li><li>中等</li><li>严格</li></ul> |
| market |字符串 |否 |查询 |无 |要缩小搜索范围的市场或区域（示例：en-US） |
| longitude |数字 |否 |查询 |无 |要缩小搜索范围的经度（东部/西部坐标）（示例：47.603450） |
| latitude |数字 |否 |查询 |无 |要缩小搜索范围的纬度（北部/南部坐标）（示例：-122.329696） |
| webFileType |字符串 |否 |查询 |无 |要缩小搜索范围的文件类型（示例：“文档”） |
| videoFilters |字符串 |否 |查询 |无 |根据大小、纵横比、颜色、样式、人物或其中任何组合的筛选器搜索。  有效值： <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <br/><br/>例如：“Duration:Short+Resolution:High” |
| videoSortBy |字符串 |否 |查询 |无 |结果的排序顺序。 有效值： <ul><li>日期</li><li>相关性</li></ul> <p>日期排序顺序暗指降序。</p> |
| imageFilters |字符串 |否 |查询 |无 |根据大小、纵横比、颜色、样式、人物或其中任何组合的筛选器搜索。 有效值： <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[宽度]</li><li>Size:Height:[高度]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><br/><br/>例如：“Size:Small+Aspect:Square” |
| newsSortBy |字符串 |否 |查询 |无 |结果的排序顺序。 有效值： <ul><li>日期</li><li>相关性</li></ul> <p>日期排序顺序暗指降序。</p> |
| newsCategory |字符串 |否 |查询 |无 |缩小搜索范围的资讯类别（示例：“rt_Business”） |
| newsLocationOverride |字符串 |否 |查询 |无 |检测到的必应位置覆盖。 此参数仅适用于 en-US 市场。 输入格式为 US./<state />（示例：“US.WA”） |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| default |操作失败。 |

## <a name="object-definitions"></a>对象定义
#### <a name="webresultmodel-bing-web-search-results"></a>WebResultModel：必应 Web 搜索结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 标题 |字符串 |否 |
| 说明 |字符串 |否 |
| DisplayUrl |字符串 |否 |
| ID |字符串 |否 |
| FullUrl |字符串 |否 |

#### <a name="videoresultmodel-bing-video-search-results"></a>VideoResultModel：必应视频搜索结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 标题 |字符串 |否 |
| DisplayUrl |字符串 |否 |
| ID |字符串 |否 |
| MediaUrl |字符串 |否 |
| 运行时 |integer |否 |
| 缩略图 |未定义 |否 |

#### <a name="thumbnailmodel-thumbnail-properties-of-the-multimedia-element"></a>ThumbnailModel：多媒体元素的缩略图属性
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| MediaUrl |字符串 |否 |
| ContentType |字符串 |否 |
| 宽度 |integer |否 |
| 高度 |integer |否 |
| FileSize |integer |否 |

#### <a name="imageresultmodel-bing-image-search-results"></a>ImageResultModel：必应图像搜索结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 标题 |字符串 |否 |
| DisplayUrl |字符串 |否 |
| ID |字符串 |否 |
| MediaUrl |字符串 |否 |
| SourceUrl |字符串 |否 |
| 缩略图 |未定义 |否 |

#### <a name="newsresultmodel-bing-news-search-results"></a>NewsResultModel：必应资讯搜索结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 标题 |字符串 |否 |
| 说明 |字符串 |否 |
| DisplayUrl |字符串 |否 |
| ID |字符串 |否 |
| 源 |字符串 |否 |
| 日期 |字符串 |否 |

#### <a name="spellresultmodel-bing-spelling-suggestions-results"></a>SpellResultModel：必应拼写建议结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| ID |字符串 |否 |
| 值 |字符串 |否 |

#### <a name="relatedsearchresultmodel-bing-related-search-results"></a>RelatedSearchResultModel：必应相关搜索结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 标题 |字符串 |否 |
| ID |字符串 |否 |
| BingUrl |字符串 |否 |

#### <a name="compositesearchresultmodel-bing-composite-search-results"></a>CompositeSearchResultModel：必应复合搜索结果
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| WebResultsTotal |integer |否 |
| ImageResultsTotal |integer |否 |
| VideoResultsTotal |integer |否 |
| NewsResultsTotal |integer |否 |
| SpellSuggestionsTotal |integer |否 |
| WebResults |数组 |否 |
| ImageResults |数组 |否 |
| VideoResults |数组 |否 |
| NewsResults |数组 |否 |
| SpellSuggestionResults |数组 |否 |
| RelatedSearchResults |数组 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

返回到 [API 列表](apis-list.md)。




<!--HONumber=Jan17_HO3-->


