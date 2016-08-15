<properties
	pageTitle="收集数据以训练模型：机器学习建议 API | Azure"
	description="Azure 机器学习建议 - 收集数据以训练模型"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.date="06/06/2016"
	wacn.date=""/>

#  收集数据以训练模型 #

建议 API 可从过去的事务进行学习，以确定应该为特定的用户创建哪些项目。

创建模型后，需要提供以下两个信息片段才能执行任何训练：目录文件和使用情况数据。

>   如果你尚未这样做，我们建议先完成[快速入门指南](/documentation/articles/cognitive-services-recommendations-quick-start/)。


## 目录数据 ##

### 目录文件格式 ###

目录文件包含要提供给客户的项目的相关信息。
目录数据应遵循以下格式：

- 没有特征 - `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- 有特征 - `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### 目录文件中的示例行

没有特征：

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

有特征：

    AAA04294,Office Language Pack Online DwnLd,Office, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia, hardwaretype=mobile

#### 格式详细信息

| 名称 | 必需 | 类型 | 说明 |
|:---|:---|:---|:---|
| 项目 ID |是 | [A-z]、[a-z]、[0-9]、[\_]（下划线）、[-]（短划线）<br>最大长度：50 | 项目的唯一标识符。 |
| 项目名称 | 是 | 任何字母数字字符<br>最大长度：255 | 项目名称。 |
| 项目类别 | 是 | 任何字母数字字符<br>最大长度：255 | 此项目所属的类别（例如烹饪书籍、剧本...）；可为空。 |
| 说明 | 否，除非有特征（但可为空） | 任何字母数字字符<br>最大长度：4000 | 此项目的说明。 |
| 特征列表 | 否 | 任何字母数字字符<br>最大长度：4000；最大特征数：20 | 特征名称的逗号分隔列表 = 可用于增强模型建议的特征值。|

#### 上载目录文件

有关如何上载目录文件，请参阅 [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1)（API 参考）。

请注意，目录文件的内容应该以请求正文的形式传递。

如果将多个目录文件上载到具有多个调用的同一模型，则只能插入新目录项目。现有项目将保留原始值。不能使用此方法更新目录数据。

>   注意：
>   文件大小上限为 200MB。
>   目录中支持的项目数上限为 100,000。


## 为何要将特征添加到目录？

建议引擎将创建一个统计模型，告诉你客户可能喜欢或可能购买的项目。如果你未曾与某个新产品交互过，则无法根据共同出现的次数单独创建模型。假设你在商店中销售一款新的“儿童小提琴”，但以前未曾销售过这款小提琴，则无法知道要连同该款小提琴一起推荐其他哪些商品。

话虽如此，如果引擎知道有关该小提琴的信息（例如，它是一种乐器、它适用于年龄 7-10 岁的儿童、该小提琴的价格不高，等等），则引擎可以从其他具有类似特征的产品中进行学习。例如，你以前曾经销售过小提琴，知道购买小提琴的人通常都倾向于购买古典乐 CD 和乐谱架。尽管你的新款小提琴销售量不大，但系统可以在特征之间找到这些关联，并根据特征提供建议。

特征将作为目录数据的一部分导入，然后在排名生成后创建与其排名（特征在模型中的重要性）的关联。特征排名可根据使用情况数据和项目类型而更改。但是，对于一致的使用情况/项目，排名只能有小幅的波动。特征的排名是一个非负数字。数字 0 表示特征未排名（如果在第一个排名生成完成之前调用此 API，则会发生此情况）。决定排名的日期称为分数有效时间。

若要使用特征作为生成的一部分，你需要：

1. 在上载目录时确保目录具有特征。

2. 触发排名生成。这将会针对特征的重要性/排名执行分析。

3. 触发建议生成，设置以下生成参数：将 useFeaturesInModel 设置为 true，将 allowColdItemPlacement 设置为 true，并将 modelingFeatureList 设置为用于增强模型的特征的逗号分隔列表。有关详细信息，请参阅 [Recommendations build type parameters](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0)（建议生成类型参数）。


## 使用情况数据 ##
使用情况文件包含有关这些项目的使用方式或者企业中事务的信息。

#### 使用情况文件格式详细信息
使用情况文件是一个 CSV（逗号分隔值）文件，其中的每一行代表用户与项目之间的交互。每一行的格式如下：<br> `<User Id>,<Item Id>,<Time>,[<Event>]`



| 名称 | 必需 | 类型 | 说明
|-------|------------|------|---------------
|用户 ID| 是|[A-z]、[a-z]、[0-9]、[\_]（下划线）、[-]（短划线）<br>最大长度：255 |用户的唯一标识符。
|项目 ID|是|[A-z]、[a-z]、[0-9]、[&#95;]（下划线）、[-]（短划线）<br>最大长度：50|项目的唯一标识符。
|时间|是|日期格式：YYYY/MM/DDTHH:MM:SS（例如 2013/06/20T10:00:00）|数据的时间。
|事件|否 | 下列其中一项：<br>• Click<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Purchase| 交易类型。 |

#### 使用情况文件中的示例行

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### 上载使用情况文件

有关如何上载使用情况文件，请参阅 [API reference](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2)（API 参考）。请注意，需要将使用情况文件的内容作为 HTTP 调用的主体来传递。

>  注意：

>  文件大小上限：200MB。可以上载多个使用情况文件。

>  需要先上载目录文件，然后才能开始将使用情况数据添加到模型。在训练阶段，只会使用目录文件中的项目。所有其他项目将被忽略。

## 需要多少数据？

模型的质量在很大程度上取决于数据的质量和数量。系统将在用户购买不同项目（称为共同项目）时进行学习。对于 FBT 版本，还必须知道在相同的交易中购买了哪些项目。

一个良好的经验法则是让大多数项目出现在 20 个或更多的交易中，因此，如果你的目录中有 10,000 个项目，则我们建议提供的交易数是该交易数的 20 倍，即大约 200,000 个交易。再次强调，这只是一条经验法则。你需要使用你的数据进行试验。

创建模型后，可以执行[脱机评估](/documentation/articles/cognitive-services-recommendations-buildtypes/)来检查模型的大致运行状况。

<!---HONumber=Mooncake_0808_2016-->