---
title: 技能组概念和工作流
titleSuffix: Azure Cognitive Search
description: 技能组是你在 Azure 认知搜索中创作 AI 扩充管道的地方。 了解有关技能组构成的重要概念和详细信息。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: f1d8715fcadeda5ccd1a98192a70939b0c359c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976670"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure 认知搜索中的技能组概念

本文适用于需要更深入地了解技能组概念和构成的开发人员，并且假定这些开发人员熟悉 AI 扩充流程。 如果你不熟悉此概念，请从 [Azure 认知搜索中的 AI 扩充](cognitive-search-concept-intro.md)开始。

## <a name="introducing-skillsets"></a>技能组简介

技能组是 Azure 认知搜索中附加到某个索引器的可重用资源，它指定在编制索引期间用于分析、转换和扩充文本或图像内容的技能集合。 技能具有输入和输出，一项技能的输出通常会成为一系列流程中另一项技能的输入。

技能组有三个主要属性：

+ `skills`，技能的无序集合，平台将根据每项技能所需的输入来确定这些技能的执行顺序。
+ `cognitiveServices`，这是认知服务资源的键，它为技能组（包括内置技能）执行图像和文本处理。
+ `knowledgeStore`（可选），要将扩充的文档投影到的 Azure 存储帐户。 搜索索引也使用扩充的文档。

技能集是以 JSON 格式创作的。 下面的示例是此 [hotel-reviews 技能组](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)的稍简版本，用于阐释本文中的概念。 

前两个技能如下所示：

+ 技能 #1 是一个[文本拆分技能](cognitive-search-skill-textsplit.md)，它接受“reviews_text”字段的内容作为输入，并将该内容拆分为大小为 5000 个字符的“页面“作为输出。
+ 技能 #2 是一个[情绪检测技能](cognitive-search-skill-sentiment.md)，它接受“页面”作为输入，并生成一个名为“Sentiment”的新字段作为输出，该输出包含情绪分析结果。


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> 你可以构建带循环和分支的复杂技能组，并使用[条件技能](cognitive-search-skill-conditional.md)来创建表达式。 语法基于 [JSON 指针](https://tools.ietf.org/html/rfc6901)路径表示法，做了少量的修改来标识扩充树中的节点。 `"/"` 遍历树中的较低级别，`"*"` 充当上下文中的 for-each 运算符。 本文中的许多示例阐释了此语法。 

### <a name="enrichment-tree"></a>扩充树

在[扩充管道中的步骤](cognitive-search-concept-intro.md#enrichment-steps)的进度图中，内容处理跟在从源中提取文本和图像的“文档破解”阶段之后。 然后，可以将图像内容路由到指定了图像处理的技能，而文本内容将排队等待进行文本处理。 对于包含大量文本的源文档，可在索引器上设置“分析模式”，将文本分段为较小的区块进行更好的处理。 

![管道中的知识存储示意图](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "管道中的知识存储示意图")

将文档放入扩充管道后，它将表示为内容和关联扩充内容的树。 此树实例化为文档破解的输出。  扩充管道可以使用扩充树格式将元数据附加到平整基元数据类型。扩充树格式不是有效的 JSON 对象，但可以投影为有效的 JSON 格式。 下表显示了进入扩充管道的文档的状态：

|数据源\分析模式|默认|JSON、JSON 行 和 CSV|
|---|---|---|
|Blob 存储|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|空值 |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|空值|

 技能在执行时，会将新节点添加到扩充树。 然后，这些新节点可用作下游技能的输入、投影到知识存储，或映射到索引字段。 扩充是不可变的：创建节点后无法对其进行编辑。 随着技能集变得越来越复杂，扩充树也会更加复杂，但是，并非扩充树中的所有节点都需要将扩充保存到索引或知识存储中。 

你可以选择性地将一部分扩充保存到索引或知识存储中。

### <a name="context"></a>上下文

每个技能都需要一个上下文。 上下文确定：

+ 根据所选节点执行技能的次数。 对于类型集合的上下文值，在末尾添加 `/*` 会导致为该集合中的每个实例调用技能一次。 

+ 在扩充树中添加技能输出的位置。 输出始终作为上下文节点的子级添加到树中。 

+ 输入的形状。 对于多级别集合，将上下文设置为父集合会影响技能输入的形状。 例如，如果某个扩充树包含国家/地区列表，其中的每个国家/地区已使用包含邮政编码列表的省/自治区/直辖市列表进行扩充。

|上下文|输入|输入的形状|技能调用|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |国家/地区中所有邮政编码的列表 |对每个国家/地区调用一次 |
|`/document/countries/*/states/*` |`/document/countries/ */states/* /zipcodes/*`` |省/自治区/直辖市中邮政编码的列表 | 对每个国家/地区和州/省的组合调用一次|

## <a name="generate-enriched-data"></a>生成扩充数据 

我们将使用[酒店评论技能组](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)作为参考点来查看：

+ 扩充树如何随着每个技能的执行而演变
+ 如何结合上下文和输入来确定技能的执行次数
+ 哪种输入形状基于上下文

扩充流程中的“文档“表示 hotel_reviews.csv 源文件内的单个行（酒店评论）。

### <a name="skill-1-split-skill"></a>技能 #1：拆分技能

当源内容由大块文本组成时，将其分解成较小的部分会很有帮助，可以提高语言、情绪和关键短语检测的准确度。 有两个可用的粒度：页面和句子。 一个页面包含大约 5000 个字符。

文本拆分技能通常是技能组中的第一个技能。

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

在技能上下文为 `"/document/reviews_text"` 的情况下，拆分技能将针对 `reviews_text` 执行一次。 技能输出是一个列表，其中的 `reviews_text` 分块成包含 5000 个字符的段。 拆分技能的输出名为 `pages`，将添加到扩充树中。 使用 `targetName` 功能可以在将技能输出添加到扩充树之前对其重命名。

现在，扩充树的技能上下文下包含一个新节点。 此节点可用于任何技能、投影或输出字段映射。 从概念上讲，该树如下所示：

![完成文档破解后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "完成文档破解之后、执行技能之前的扩充树")

所有扩充的根节点是 `"/document"`。 使用 Blob 索引器时，`"/document"` 节点包含 `"/document/content"` 和 `"/document/normalized_images"` 的子节点。 使用 CSV 数据时（如本示例所示），列名称将映射到 `"/document"` 下的节点。 

若要访问由技能添加到节点的任何扩充，需要使用扩充的完整路径。 例如，若要使用 ```pages``` 节点中的文本作为另一技能的输入，需将该节点指定为 ```"/document/reviews_text/pages/*"```。
 
 ![执行技能 #1 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "执行技能 #1 后的扩充树")

### <a name="skill-2-language-detection"></a>技能 #2：语言检测

酒店评论文档包含以多种语言表达的客户反馈。 语言检测技能确定使用的是哪种语言。 然后，结果将传递给关键短语提取和情绪检测，在检测情绪和短语时将考虑语言。

尽管语言检测技能是技能集中定义的第三个技能（技能 #3），但它是下一个要执行的技能。 由于它不会受到阻止（无需输入），因此它将与前一个技能同时执行。 与前面的拆分技能一样，语言检测技能也只对每个文档调用一次。 扩充树现在包含新的语言节点。

 ![执行技能 #2 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "执行技能 #2 后的扩充树")
 
 ### <a name="skill-3-key-phrases-skill"></a>技能 #3：关键短语技能 

将会针对 `pages` 集合中的每个项，根据给定的 `/document/reviews_text/pages/*` 上下文调用关键短语技能一次。 该技能的输出是关联的页元素下的一个节点。 

 现在，你应该可以查看技能集中的其他技能，并直观地了解扩充树如何随着每个技能的执行而不断扩大。 某些技能（例如合并技能和整形程序技能）也会创建新的节点，但只使用现有节点中的数据，而不会创建全新的扩充。

![执行所有技能后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "执行所有技能后的扩充树")

上述树中的连接器颜色指示扩充由不同的技能创建，节点需要单独寻址，并且在选择父节点时不会成为返回的对象部分。

## <a name="save-enrichments"></a>保存扩充

在 Azure 认知搜索中，索引器保存它创建的输出。 其中一个输出始终是[可搜索的索引](search-what-is-an-index.md)。 必须指定索引。在附加技能组时，索引所引入的数据将包括扩充的内容。 通常，特定技能的输出（例如关键短语或情绪评分）将引入到为此目的而创建的字段的索引中。

此外，索引器还可以将输出发送到[知识存储](knowledge-store-concept-intro.md)，以便在其他工具或流程中使用。 知识存储是作为技能组的一部分定义的。 它的定义决定了你的已扩充文档是被投影为表还是对象（文件或 blob）。 表格投影非常适用于 Power BI 等工具中的交互式分析，而文件和 blob 通常用于数据科学或类似的流程中。 在本部分中，你将了解技能组成分如何对你要投影的表或对象进行整形。

### <a name="projections"></a>投影数

对于以知识存储为目标的内容，你需要考虑内容的构造方式。 投影是从扩充树中选择节点并在知识存储中创建其物理表示形式的过程。 投影是可输出为表或对象投影的文档（内容和扩充）的自定义形状。 若要详细了解如何使用投影，请参阅[使用投影](knowledge-store-projection-overview.md)。

![字段映射选项](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "扩充管道的字段映射选项")

### <a name="sourcecontext"></a>SourceContext

`sourceContext` 元素仅用于技能输入和投影。 它用于构造多级别嵌套对象。 你可能需要创建一个新对象，以将其作为技能或项目的输入传递到知识存储中。 由于扩充节点可能不是扩充树中的有效 JSON 对象，并且引用树中的某个节点仅返回该节点在创建时的状态，因此，使用扩充作为技能输入或预测时，需要创建一个格式正确的 JSON 对象。 `sourceContext` 可用于构造分层的匿名类型对象，如果你仅使用上下文，则需要多个技能。 

下面的示例展示了如何使用 `sourceContext`。 查看生成了扩充的技能输出，以确定它是否为有效的 JSON 对象而不是基元类型。

### <a name="slicing-projections"></a>切片投影

定义表投影组时，可将扩充树中的单个节点切片为多个相关表。 如果添加的表的源路径是现有表投影的子级，则生成的子节点将不是现有表投影的子级，而会投影到新的相关表。 借助此切片技术，可以使用整形程序技能定义单个节点，该节点可以用作所有表投影的源。 

### <a name="shaping-projections"></a>整形投影

可通过两种方式来定义投影：

+ 使用文本整形程序技能来创建一个新节点，该节点可充当你要投影的所有扩充的根节点。 然后，在投影中，只需引用整形程序技能的输出。

+ 使用投影定义本身中的内联整形投影。

整形程序方法比内联整形更繁琐，但可确保扩充树的所有变换都包含在技能中，并确保输出是可重用的对象。 相反，内联整形可让你创建所需的形状，但它是一个匿名对象，仅适用于定义它时所针对的投影。 这些方法可以结合使用，也可以单独使用。 在门户工作流中创建的技能集包含这两种方法。 该技能集对表投影使用整形程序技能，但同时使用内联整形来投影关键短语表。

为了延伸该示例，可以选择删除内联整形，并使用整形程序技能为关键短语创建新节点。 若要创建投影到三个表（即 `hotelReviewsDocument`、`hotelReviewsPages` 和 `hotelReviewsKeyPhrases`）的形状，请使用后续部分所述的两个选项。

#### <a name="shaper-skill-and-projection"></a>整形程序技能和投影

此 

> [!Note]
> 为简洁起见，本示例中已删除了文档表中的某些列。
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

使用上述 `outputs` 节中定义的 `tableprojection` 节点，我们现在可以使用切片功能将 `tableprojection` 节点的部分投影到不同的表：

> [!Note]
> 这只是知识存储配置中的一个投影片段。
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>内联整形投影

内嵌整形方法不需要整形程序技能，因为投影所需的所有形状只在有必要时才会创建。 若要投影与上一个示例相同的数据，可使用如下所示的内联投影选项：

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
使用这两种方法后，可以观察如何使用 `"sourceContext"` 来投影 `"Keyphrases"` 的值。 包含字符串集合的 `"Keyphrases"` 节点本身是页文本的子级。 但是，由于投影需要 JSON 对象并且页是基元（字符串），因此使用了 `"sourceContext"` 将关键短语包装为具有命名属性的对象。 使用此技术可以单独投影平整基元。

## <a name="next-steps"></a>后续步骤

接下来，请创建包含认知技能的第一个技能集。

> [!div class="nextstepaction"]
> [创建第一个技能集](cognitive-search-defining-skillset.md)。
