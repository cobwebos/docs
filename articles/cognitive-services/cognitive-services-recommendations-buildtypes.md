---
title: "生成类型和模型质量：建议 API | Microsoft Docs"
description: "Azure 机器学习建议 -- 快速入门指南"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 7e0beaa7-2646-4504-bce3-0d7bf767136a
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f2cabdd547599639ed5b7d9a145c0ce9592d9d46


---
# <a name="build-types-and-model-quality"></a>生成类型和模型质量
<a name="TypeofBuilds"></a>

## <a name="supported-build-types"></a>支持的生成类型
建议 API 目前支持两种生成类型：*建议*和 *FBT*。 每种类型的版本使用不同的算法创建，并具有不同的优点。 本文档将介绍其中的每个版本，以及用于比较所生成的模型质量的方法。

如果你尚未这样做，我们建议先完成[快速入门指南](cognitive-services-recommendations-quick-start.md)。

<a name="RecommendationBuild"></a>

### <a name="recommendation-build-type"></a>建议版本类型
建议生成类型使用矩阵分解来提供建议。 它基于事务生成[潜伏特征](https://en.wikipedia.org/wiki/Latent_variable)向量以描述每个项目，然后使用这些潜伏向量来比较类似的项。

如果你要根据在电子产品商店中所做的采购来训练模型，并提供 Lumia 650 手机作为模型输入，此模型将返回可能购买 Lumia 650 手机的人通常购买的一组项。 这些项可能不互补。 在本示例中，此模型可能返回其他手机，因为喜欢 Lumia 650 的人可能也喜欢其他手机。

建议版本有两项功能可让它更有吸引力：

**建议版本支持*冷项*放置**

将不会大量使用的项目称为冷项。 例如，如果你收到以前从未销售的手机货件，则系统无法单独推断此产品的事务建议。 这意味着系统应该了解产品本身的相关信息。

如果你想要使用冷项放置，则需要提供目录中每个项的特征信息。 目录前几行可能如下所示（请注意特征的键=值格式）。

> 6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128 GB, Memory=4G, Manufacturer=Microsoft
> 
> 73H-00013,Wake Xbox 360,Gaming,, Type=Software, Language=English, Rating=Mature
> 
> WAH-0F05,Minecraft Xbox 360,Gaming,, * Type=Software, Language=Spanish, Rating=Youth
> 
> 

你还需要设置以下版本参数：

| 版本参数 | 说明 |
| --- | --- |
| *useFeaturesInModel* |设置为 **true**。  指示是否可以使用特征来增强建议模型。 |
| *allowColdItemPlacement* |设置为 **true**。 指示建议是否也应该通过特征相似度推送冷项。 |
| *modelingFeatureList* |要在建议版本中使用的特征名称逗号分隔列表，用于增强建议。 例如，上述示例中的“Language,Storage”。 |

**建议版本支持用户建议**

建议版本支持[用户建议](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)。 这意味着它可以基于用户的事务历史记录来为用户提供个性化建议。 对于用户建议，可以为该用户提供用户 ID 或最近的事务历史记录。

你可能想要应用用户建议的典型示例之一是在欢迎页上登录。 可以在那里推广适用于特定用户的内容。

你可能还想要在用户即将签出时应用建议生成类型。 此时，你将获得用户即将购买的项列表，而你可以基于当前的购物篮提供建议。

#### <a name="recommendations-build-parameters"></a>建议版本参数
| Name | 说明 | 类型， <br>  有效值， <br> （默认值） |
| --- | --- | --- |
| *NumberOfModelIterations* |总体计算时间和模型精确度反映模型执行迭代的次数。 数字越大，模型的精确度就越高，但需要的计算时间也较长。 |整数， <br>     10 到 50 <br>(40) |
| *NumberOfModelDimensions* |维度的数目与特征的数目相关，模型尝试查找数据中的数目。 增加维度的数目将允许结果进一步微调成较小的群集。 但是，太多维度将使模型无法查找项目之间的相关性。 |整数， <br> 10 到 40 <br>(20) |
| *ItemCutOffLowerBound* |定义应该在模型中考虑项目应位于其中的最小使用量点数目。 |整数， <br> 2 或更大 <br> (2) |
| *ItemCutOffUpperBound* |定义应该在模型中考虑项目应位于其中的最大使用量点数目。 |整数， <br>2 或更大<br> (2147483647) |
| *UserCutOffLowerBound* |定义模型中应考虑用户必须先执行的最小事务数目。 |整数， <br> 2 或更大 <br> (2) |
| *UserCutOffUpperBound* |定义模型中应考虑用户必须先执行的最大事务数目。 |整数， <br>2 或更大 <br> (2147483647) |
| *UseFeaturesInModel* |指示是否可以使用特征来增强建议模型。 |布尔<br> 默认值：True |
| *ModelingFeatureList* |要在建议版本中使用的特征名称逗号分隔列表，用于增强建议。 它取决于很重要的特征。 |字符串，最多 512 个字符 |
| *AllowColdItemPlacement* |指示建议是否也应该通过特征相似度推送冷项。 |布尔 <br> 默认值：False |
| *EnableFeatureCorrelation* |指示特征是否可用于推论。 |布尔 <br> 默认值：False |
| *ReasoningFeatureList* |用于推论句子（例如建议说明）的特征名称逗号分隔列表。 它取决于对客户很重要的特征。 |字符串，最多 512 个字符 |
| *EnableU2I* |启用个性化建议，又称为用户对项目 (U2I) 建议。 |布尔 <br>默认值：True |
| *EnableModelingInsights* |定义是否应该运行脱机评估，以便收集建模见解（也就是精确度和多样性指标）。 如果设置为 true，则不将数据的子集用于训练，因为要将它保留来供测试模型使用。 详细了解[脱机评估](#OfflineEvaluation)。 |布尔 <br> 默认值：False |
| *SplitterStrategy* |如果将启用建模见解设置为 *true*，则这是出于评估目的用于拆分数据的方式。 |字符串，*RandomSplitter* 或 *LastEventSplitter* <br>默认值：RandomSplitter |

<a name="FBTBuild"></a>

### <a name="fbt-build-type"></a>FBT 版本类型
“经常一起购买”(FBT) 版本将执行分析，以计算两项或三项不同产品一起共同出现的次数。 然后根据相似度函数（**co-occurrences**、**Jaccard**、**lift**）将集合排序。

将 **Jaccard** 和 **lift** 视为标准化共同出现的方法。  这意味着只有在项目与种子项目一起购买时才返回这些项目。

在 Lumia 650 手机示例中，只有在与 Lumia 650 手机相同的会话中购买手机 X 时，才返回手机 X。 因为这不太可能发生，所以我们预期返回 Lumia 650 的补充项目；例如，屏幕保护设备或 Lumia 650 的电源适配器。

目前，如果有两个具有相同用户 ID 和时间戳的项目出现在一个事务中，则假设在相同的会话中购买这两个项目。

FBT 版本目前不支持冷项，因为其根据定义预期在同一个事务中购买两个项目。 FBT 版本可返回项目集（三个一组），但因为它们接受以单个种子项目作为输入，所以不支持个性化建议。

#### <a name="fbt-build-parameters"></a>FBT 版本参数
| Name | 说明 | 类型，  <br> 有效值， <br> （默认值） |
| --- | --- | --- |
| *FbtSupportThreshold* |模型的保守程度。 建模时要考虑项目的共同出现次数。 |整数， <br> 3-50 <br> (6) |
| *FbtMaxItemSetSize* |频繁集中的项目数边界。 |整数    <br> 2-3 <br> (2) |
| *FbtMinimalScore* |频繁集应该具有的最低分数，以包含在返回的结果中。 越高越好。 |Double <br> 0 及以上 <br> (0) |
| *FbtSimilarityFunction* |定义版本使用的相似度函数。 **Lift** 有利于机缘巧合、**co-occurrence** 有助于可预测性，而 **Jaccard** 可在两者间获取平衡。 |字符串，  <br>  <i>cooccurrence, lift, jaccard</i><br> 默认值：<i>jaccard</i> |

<a name="SelectBuild"></a>

## <a name="build-evaluation-and-selection"></a>版本评估和选择
该指导可能会帮助你确定是否应该使用建议版本或 FBT 版本，但是在可以使用其中任一版本的情况下，这并不是明确的答案。 此外，即使知道要使用 FBT 生成类型，但仍可能想要选择 **Jaccard** 或 **lift** 作为相似度函数。

在这两种版本之间进行选择的最佳方式就是在真实世界中进行测试（联机评估），并跟踪不同版本的转换率。 根据建议点选次数、来自显示建议的实际购买数目，或甚至是显示不同建议时的实际购买金额，可以测量转换率。 可以根据业务目标来选择转换率指标。

在某些情况下，建议在模型投入生产之前，先脱机评估模型。 尽管脱机评估无法替换在线评估，但可以作为指标以供我们参考。

<a name="OfflineEvaluation"></a>

## <a name="offline-evaluation"></a>脱机评估
脱机评估的目的是要预测精确度（购买其中一个建议项目的用户数目）和建议的多样性（建议的项目数）。
在精确度和多样性指标评估中，系统发现用户样本，而这些用户的事务分成两个组：训练数据集和测试数据集。

> [!NOTE]
> 若要使用脱机指标，必须有使用情况数据的时间戳。
> 需要时间数据，才能正确分区训练和测试数据集之间的使用情况。
> 
> 此外，脱机评估可能不会生成小型使用情况文件的结果。 为了彻底评估，测试数据集中至少有 1000 个使用点。
> 
> 

<a name="Precision"></a>

### <a name="precision-at-k"></a>K 精度
下表显示了 K 精度脱机评估的输出。

| K | 1 | 2 | 3 | 4 | 5 |
| --- | --- | --- | --- | --- | --- |
| 百分比 |13.75 |18.04 |21 |24.31 |26.61 |
| 测试中的用户数 |10,000 |10,000 |10,000 |10,000 |10,000 |
| 考虑的用户数 |10,000 |10,000 |10,000 |10,000 |10,000 |
| 未考虑的用户数 |0 |0 |0 |0 |0 |

#### <a name="k"></a>K
在上表中，*k* 代表要向客户显示的建议数目。 表显示的意思如下：“在测试期间，只对客户显示一个建议，只有 13.75 的用户购买该项建议。” 此语句基于的假设是，使用采购数据对模型进行训练。 另一种说法是“K=1 时的精确度”是 13.75。

你会发现，对客户显示的项目越多，客户购买建议项目的可能性越大。 在上述实验中，建议 5 个项目时的机率几乎倍增到 26.61%。

#### <a name="percentage"></a>百分比
与至少一个显示的 *k* 建议交互的用户百分比。 此百分比的计算方式：将与至少一个建议交互的用户人数除以考虑的用户总数。 有关详细信息，请参阅“考虑的用户数”。

#### <a name="users-in-test"></a>测试中的用户数
此行的数据表示测试数据集内的用户总数。

#### <a name="users-considered"></a>考虑的用户数
只有在系统根据使用训练数据集所版本的模型而建议至少 *k* 个项目时，才考虑包含某个用户。

#### <a name="users-not-considered"></a>未考虑的用户数
此行的数据表示所有未考虑的用户。 未收到至少 *k* 个建议项目的用户。

未考虑的用户数 = 测试中的用户数 – 考虑的用户数

<a name="Diversity"></a>

### <a name="diversity"></a>多样性
多样性指标度量建议的项目类型。 下表显示了多样性脱机评估的输出。

| 百分位存储桶 | 0-90 | 90-99 | 99-100 |
| --- | --- | --- | --- |
| 百分比 |34.258 |55.127 |10.615 |

建议的项目总数：100,000

建议的唯一项目数：954

#### <a name="percentile-buckets"></a>百分位存储桶
每个百分位存储桶都是以一个范围（介于 0 与 100 之间的最小值和最大值）表示。 接近 100 的项目是最受欢迎的项目，而接近 0 的项目则最不受欢迎。 例如，如果 99-100 百分位存储桶的百分比值为 10.6，这意味着 10.6% 的建议只返回前 1% 最受欢迎的项目。 百分位存储桶最小值包含在内，而最大值则是排除在外（但 100 除外）。

#### <a name="unique-items-recommended"></a>建议的唯一项目数
建议的唯一项目数指标显示返回进行评估的不同项目的数目。

#### <a name="total-items-recommended"></a>建议的项目总数
建议的项目总数指标显示建议的项目数。 某些可能是重复项。

<a name="ImplementingEvaluation"></a>

### <a name="offline-evaluation-metrics"></a>脱机评估指标
精确度和多样性脱机指标可能有助于选择要使用的版本。 构建时在相应 FBT 或建议版本参数中：

* 将 *enableModelingInsights* 版本参数设置为 **true**。
* （可选）选择 *splitterStrategy*（*RandomSplitter* 或 *LastEventSplitter*）。
  *RandomSplitter* 根据指定的 *randomSplitterParameters* 测试百分比和随机种子值，拆分训练和测试集中的使用量数据。
  *LastEventSplitter* 根据每个用户的最后一个事务，拆分训练和测试集中的使用量数据。

这会触发生成，仅使用一部分数据进行训练，其余的数据用于计算评估指标。  在构建完成之后，若要获取评估的输出，只需调用[获取版本指标 API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f)，并传递相应 *modelId* 和 *buildId*。

 下面是我们执行的示例评估的 JSON 输出。

    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }



<!--HONumber=Nov16_HO3-->


