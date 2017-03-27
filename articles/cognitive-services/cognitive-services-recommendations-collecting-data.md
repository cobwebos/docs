---
title: "收集数据以训练模型：机器学习建议 API | Microsoft Docs"
description: "Azure 机器学习建议 - 收集数据以训练模型"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 4040d2b7-bd79-4946-ad83-5a31529151f8
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c4b748b601be52b8a27540595f99dbbb3a7cf2ef


---
# <a name="collecting-data-to-train-your-model"></a>收集数据以训练模型
建议 API 可从过去的事务进行学习，以确定应该为特定的用户创建哪些项目。

创建模型后，需要提供以下两个信息片段才能执行任何训练：目录文件和使用情况数据。

> 如果你尚未这样做，我们建议先完成[快速入门指南](cognitive-services-recommendations-quick-start.md)。
> 
> 

## <a name="catalog-data"></a>目录数据
### <a name="catalog-file-format"></a>目录文件格式
目录文件包含要提供给客户的项目的相关信息。
目录数据应遵循以下格式：

* 没有特征 - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`
* 有特征 - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>目录文件中的示例行
没有特征：

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

有特征：

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>格式详细信息
| 名称 | 必需 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| 项目 ID |是 |[A-z]、[a-z]、[0-9]、[_] &#40;下划线&#41;、[-] &#40;短划线&#41;<br> 最大长度：50 |项目的唯一标识符。 |
| 项目名称 |是 |任何字母数字字符<br> 最大长度：255 |项目名称。 |
| 项目类别 |是 |任何字母数字字符 <br> 最大长度：255 |此项目所属的类别（例如烹饪书籍、剧本...）；可为空。 |
| 说明 |否，除非有特征（但可为空） |任何字母数字字符 <br> 最大长度：4000 |此项目的说明。 |
| 特征列表 |否 |任何字母数字字符 <br> 最大长度：4000；最大特征数量：20 |特征名称的逗号分隔列表 = 可用于增强模型建议的特征值。 |

#### <a name="uploading-a-catalog-file"></a>上载目录文件
有关如何上载目录文件，请参阅 [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1)（API 参考）。  

请注意，目录文件的内容应该以请求正文的形式传递。

如果将多个目录文件上载到具有多个调用的同一模型，则只能插入新目录项目。 现有项目将保留原始值。 不能使用此方法更新目录数据。

> 注意：文件大小上限为 200MB。
> 目录中支持的项目数上限为 100,000。
> 
> 

## <a name="why-add-features-to-the-catalog"></a>为何要将特征添加到目录？
建议引擎将创建一个统计模型，告诉你客户可能喜欢或可能购买的项目。 如果你未曾与某个新产品交互过，则无法根据共同出现的次数单独创建模型。 假设你在商店中销售一款新的“儿童小提琴”，但以前未曾销售过这款小提琴，则无法知道要连同该款小提琴一起推荐其他哪些商品。

话虽如此，如果引擎知道有关该小提琴的信息（例如，它是一种乐器、它适用于年龄 7-10 岁的儿童、该小提琴的价格不高，等等），则引擎可以从其他具有类似特征的产品中进行学习。 例如，你以前曾经销售过小提琴，知道购买小提琴的人通常都倾向于购买古典乐 CD 和乐谱架。  尽管你的新款小提琴销售量不大，但系统可以在特征之间找到这些关联，并根据特征提供建议。

特征将作为目录数据的一部分导入，然后在排名生成后创建与其排名（特征在模型中的重要性）的关联。 特征排名可根据使用情况数据和项目类型而更改。 但是，对于一致的使用情况/项目，排名只能有小幅的波动。 特征的排名是一个非负数字。 数字 0 表示特征未排名（如果在第一个排名生成完成之前调用此 API，则会发生此情况）。 决定排名的日期称为分数有效时间。

### <a name="features-are-categorical"></a>特征是分类的
这意味着你应创建类似于类别的特征。 例如，“price=9.34”不是分类特征。 另一方面，像 priceRange=Under5Dollars 这样的特征属于分类特征。 另一个常见错误是将项名称用作特征。 这将导致唯一的项名称，因此它不会描述一种类别。 确保特征表示项的类别。

### <a name="how-manywhich-features-should-i-use"></a>应使用多少/哪些特征？
最终，建议版本支持构建一个多达 20 个特征的模型。 可以将 20 多个特征分配给目录中的项，但你应执行排名生成，然后仅选择排名高的特征。 （排名为 2.0 或更高的特征非常适合用户使用！）。 

### <a name="when-are-features-actually-used"></a>何时实际使用特征？
当事务数据不足，无法单独提供有关事务信息的建议时，由模型使用特征。 因此特征将对“冷项”（事务记录很少的项）产生的影响最大。 如果你的所有项都具有足够的事务信息，可能不需要使用特征丰富模型。

### <a name="using-product-features"></a>使用产品特征
若要使用特征作为生成的一部分，你需要：

1. 在上载目录时确保目录具有特征。
2. 触发排名生成。 这将会针对特征的重要性/排名执行分析。
3. 触发建议生成，设置以下生成参数：将 useFeaturesInModel 设置为 true，将 allowColdItemPlacement 设置为 true，并将 modelingFeatureList 设置为用于增强模型的特征的逗号分隔列表。 有关详细信息，请参阅 [Recommendations build type parameters](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0)（建议生成类型参数）。

## <a name="usage-data"></a>使用情况数据
使用情况文件包含有关这些项目的使用方式或者企业中事务的信息。

#### <a name="usage-format-details"></a>使用情况文件格式详细信息
使用情况文件是一个 CSV（逗号分隔值）文件，其中的每一行代表用户与项目之间的交互。 每一行的格式如下：<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`

| Name | 必需 | 类型 | 说明 |
| --- | --- | --- | --- |
| 用户 ID |是 |[A-z]、[a-z]、[0-9]、[_] &#40;下划线&#41;、[-] &#40;短划线&#41;<br> 最大长度：255 |用户的唯一标识符。 |
| 项目 ID |是 |[A-z]、[a-z]、[0-9]、[&#95;]、[_] &#40;下划线&#41;、[-] &#40;短划线&#41;<br> 最大长度：50 |项目的唯一标识符。 |
| 时间 |是 |日期格式：YYYY/MM/DDTHH:MM:SS（例如 2013/06/20T10:00:00） |数据的时间。 |
| 事件 |否 |下列类型作之一：<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase |交易类型。 |

#### <a name="sample-rows-in-a-usage-file"></a>使用情况文件中的示例行
    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>上载使用情况文件
有关如何上载使用情况文件，请参阅 [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2)（API 参考）。
请注意，需要将使用情况文件的内容作为 HTTP 调用的主体来传递。

> 注意：
> 
> 文件大小上限：200MB。 可以上载多个使用情况文件。
> 
> 需要先上载目录文件，然后才能开始将使用情况数据添加到模型。 在训练阶段，只会使用目录文件中的项目。 所有其他项目将被忽略。
> 
> 

## <a name="how-much-data-do-you-need"></a>需要多少数据？
模型的质量在很大程度上取决于数据的质量和数量。
系统将在用户购买不同项目（称为共同项目）时进行学习。 对于 FBT 版本，还必须知道在相同的交易中购买了哪些项目。 

一个良好的经验法则是让大多数项目出现在 20 个或更多的交易中，因此，如果你的目录中有 10,000 个项目，则我们建议提供的交易数是该交易数的 20 倍，即大约 200,000 个交易。 再次强调，这只是一条经验法则。 你需要使用你的数据进行试验。

创建模型后，可以执行[脱机评估](cognitive-services-recommendations-buildtypes.md)来检查模型的大致运行状况。




<!--HONumber=Nov16_HO3-->


