---
title: 技能组合概念和工作流
titleSuffix: Azure Cognitive Search
description: 技能集是在 Azure 认知搜索中创作 AI 扩充管道的位置。 了解有关技能组合组合的重要概念和详细信息。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 340e6d3feaf0265597a70229fd2658f009c01f64
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790884"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure 认知搜索中的技能组合概念和组合

本文面向需要更深入地了解扩充管道工作方式的开发人员，并假设您对 AI 扩充过程有一个概念了解。 如果你是新的概念，请从开始：
+ [Azure 认知搜索中的 AI 扩充](cognitive-search-concept-intro.md)
+ [知识存储（预览版）](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>指定技能组合
技能组合是 Azure 认知搜索中可重复使用的资源，它指定在索引期间用于分析、转换和浓缩文本或图像内容的认知技能的集合。 通过创建技能组合，你可以在数据引入阶段中附加文本和图像根据，从原始内容中提取并创建新的信息和结构。

技能组合有三个属性：

+   ```skills```，一系列无序技能，平台根据每项技能所需的输入来确定执行顺序
+   ```cognitiveServices```，计费所调用认知技能所需的认知服务密钥
+   ```knowledgeStore```，将在其中投影已扩充的文档的存储帐户



技能集是采用 JSON 编写的。 您可以使用[表达式语言](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)通过循环和[分支](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)来构建复杂的技能集。 表达式语言使用[JSON 指针](https://tools.ietf.org/html/rfc6901)路径表示法进行一些修改，以标识扩充树中的节点。 遍历树中较低级别的 ```"/"```，```"*"``` 在上下文中充当每个运算符。 下面是一个示例，介绍了这些概念。 为了说明一些概念和功能，我们将指导你完成[宾馆审查示例](knowledge-store-connect-powerbi.md)技能组合。 若要在执行导入数据工作流后查看技能组合，需要使用 REST API 客户端[获取技能组合](https://docs.microsoft.com/rest/api/searchservice/get-skillset)。

### <a name="enrichment-tree"></a>扩充树

为了构想技能组合如何逐步丰富您的文档，让我们先从文档的外观开始，然后再进行任何扩充。 文档破解的输出取决于数据源以及所选的特定分析模式。 这也是在将数据添加到搜索索引时，[字段映射](search-indexer-field-mappings.md)可以从中源内容的文档的状态。
![管道中的知识存储示意图](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "K管道关系图中的 nowledge 存储区 ")

文档位于扩充管道中后，它将表示为内容树和关联的根据。 此树实例化为文档破解的输出。 使用扩充树格式，扩充管道可以将元数据附加到甚至基元数据类型，但它不是有效的 JSON 对象，但可以投影为有效的 JSON 格式。 下表显示了进入扩充管道的文档的状态：

|Data Source\Parsing 模式|默认|JSON，JSON 行 & CSV|
|---|---|---|
|Blob 存储|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/A |
|Azure Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/A|

 随着技能的执行，他们将新节点添加到扩充树。 然后，这些新节点可用作下游技能的输入，投影到知识存储或映射到索引字段。 根据不可变：创建后，无法编辑节点。 由于您的技能集变得更加复杂，因此您的扩充树，但并非扩充树中的所有节点都需要将其设为索引或知识存储区。 可以有选择地仅将根据的一个子集保存到索引或知识存储区。

可以有选择地仅将根据的一个子集保存到索引或知识存储区。
对于本文档的其余部分，我们假设我们使用[宾馆审查示例](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)，但相同的概念也适用于从所有其他数据源中丰富的文档。

### <a name="context"></a>上下文
每项技能都需要一个上下文。 上下文确定：
+   根据所选节点执行技能的次数。 对于集合类型的上下文值，在末尾添加 ```/*``` 会导致为集合中的每个实例调用一次技能。 
+   在扩充树中添加技能输出的位置。 输出始终作为上下文节点的子级添加到树中。 
+   输入的形状。 对于多级别集合，将上下文设置为父集合将影响输入技能的形状。 例如，如果你有一个包含国家/地区列表的扩充树，每个包含一个包含 zipcodes 列表的状态列表。

|上下文|输入|输入的形状|技能调用|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |该国家/地区中所有 zipcodes 的列表 |每个国家/地区 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |处于状态的 zipcodes 列表 | 每个国家/地区和状态组合一次|

### <a name="sourcecontext"></a>SourceContext

`sourceContext` 仅用于技能输入和[预测](knowledge-store-projection-overview.md)。 它用于构造多层嵌套对象。 可能需要创建一个新的项目，将其作为技能或项目的输入传递到知识库中。 由于扩充节点可能不是扩充树中的有效 JSON 对象，并且树中的节点仅在创建时才返回节点的状态，因此，使用根据作为技能输入或预测需要创建格式正确的 JSON 对象。 使用 `sourceContext` 可构造分层匿名类型的对象，如果仅使用上下文，该对象将需要多项技能。 下一节将演示如何使用 `sourceContext`。 查看生成扩充的技能输出，以确定它是否为有效的 JSON 对象而不是基元类型。

### <a name="projections"></a>投影

投影是从扩充树中选择要保存在知识库中的节点的过程。 投影是文档（content 和根据）的自定义形状，可以输出为表或对象投影。 若要了解有关使用投影的详细信息，请参阅使用[投影](knowledge-store-projection-overview.md)。

![字段映射选项](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "扩充管道的字段映射选项")

上图描述了根据你在扩充管道中的位置使用的选择器。

## <a name="generate-enriched-data"></a>生成扩充的数据 

现在，让我们逐步了解酒店评论技能组合，你可以按照[教程](knowledge-store-connect-powerbi.md)创建技能组合或[查看](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json)技能组合。 我们将查看：

* 扩充树如何随着每个技能的执行而发展 
* 上下文和输入的工作原理确定技能的执行次数 
* 输入的形状基于上下文。 

由于我们使用的是索引器的分隔文本分析模式，扩充进程中的文档表示 CSV 文件中的单个行。

### <a name="skill-1-split-skill"></a>技能 #1：拆分技能 

![文档破解后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "文档破解后和执行技能之前的扩充树")

在 ```"/document/reviews_text"```的技能上下文中，这一技能对于 `reviews_text`会执行一次。 技能输出是一个列表，其中 `reviews_text` 分为5000个字符段。 拆分技能的输出 `pages` 命名，并添加到扩充树。 `targetName` 功能允许在将技能输出添加到扩充树之前对其进行重命名。

现在，扩充树中有一个新节点放置在该技能的上下文中。 此节点可用于任何技能、预测或输出字段映射。


所有根据的根节点均 `"/document"`。 使用 blob 索引器时，`"/document"` 的节点将具有 `"/document/content"` 和 `"/document/normalized_images"`的子节点。 当使用 CSV 数据时，如本示例所示，列名称将映射到 `"/document"`下的节点。 若要访问通过技能添加到节点的任何根据，需要扩充的完整路径。 例如，如果要使用 ```pages``` 节点中的文本作为另一种技能的输入，则需要将其指定为 ```"/document/reviews_text/pages/*"```。
 
 ![技能 #1 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "执行技能 #1 后的扩充树")

### <a name="skill-2-language-detection"></a>技能 #2 语言检测
 尽管语言检测技能是在技能组合中定义的第三种（技能 #3）技术，但这是执行的下一项技能。 由于它不会被要求输入，因此它将与以前的技能并行执行。 就像前面的拆分技能一样，语言检测技能也只为每个文档调用一次。 扩充树现在具有新的语言节点。
 ![技能 #2 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "En执行技能 #2 后的 richment 树 "）
 
 ### <a name="skill-3-key-phrases-skill"></a>技能 #3：关键短语技能 

考虑到 ```/document/reviews_text/pages/*``` 的上下文，将对 `pages` 集合中的每个项调用一次关键短语技能。 技能的输出将是关联 page 元素下的一个节点。 

 现在，您应该可以查看技能组合中的其他技能，并直观显示根据的树如何随着每项技能的执行而不断增长。 一些技能（例如合并技能和整形者技能）还创建新节点，但只使用现有节点中的数据，而不创建 net new 根据。

![所有技能后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "所有技能后的扩充树")

上面树中的连接器颜色指示根据是由不同技能创建的，并且需要单独寻址节点，并且不会成为选择父节点时返回的对象的一部分。

## <a name="save-enrichments-in-a-knowledge-store"></a>将根据保存到知识库中 

技能集还定义了一个知识存储，您可以在其中将所扩充的文档投影为表或对象。 若要将您的已扩充数据保存在知识库中，可以定义一组丰富的文档的预测。 若要了解有关知识库的详细信息，请参阅[知识 store 概述](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>切片投影

定义表投影组时，可以将扩充树中的单个节点切片为多个相关表。 如果您添加的表的源路径是现有表投影的子节点，则生成的子节点将不是现有表投影的子节点，而是投影到新的相关表中。 使用此切片技术，可以在一种整形程序技能中定义单个节点，这种技能可以是所有表投影的源。 

### <a name="shaping-projections"></a>定形投影

可以通过两种方式来定义投影。 您可以使用整形程序技能来创建新节点，该节点是要投影的所有根据的根节点。 然后，在您的计划中，只引用整形程序技能的输出。 您还可以在投影定义本身中内联绘制投影。

整形程序方法比内联整形更详细，但可确保扩充树的所有突变都包含在技能中，并确保输出是可重复使用的对象。 内联造型允许您创建所需的形状，但它是一个匿名对象，并且仅适用于其定义的投影。 这些方法可以一起使用，也可以单独使用。 门户工作流中为你创建的技能组合包含这两者。 它为表投影使用整形程序技能，但也使用内联造型来投影关键短语表。

若要扩展此示例，可以选择删除内联变形，并使用整形程序技能为关键短语创建新节点。 若要创建投影到三个表（即 `hotelReviewsDocument`、`hotelReviewsPages`和 `hotelReviewsKeyPhrases`）的形状，请参阅以下各节中所述的两个选项。


#### <a name="shaper-skill-and-projection"></a>整形技能和投影 

> [!Note]
> 为了简洁起见，此示例中已删除了文档表中的某些列。
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

使用上文 `outputs` 部分定义的 `tableprojection` 节点，现在可以使用切片功能将 `tableprojection` 节点的各个部分投影到不同的表中：

> [!Note]
> 这只是知识存储配置中的投影的一个片段。
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

#### <a name="inline-shaping-projections"></a>内嵌成形投影

内嵌成形方法不需要使用整形程序技能，因为预测所需的所有形状都是在需要时创建的。 若要投影与上一个示例相同的数据，内联投影选项如下所示：

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
  
两种方法中的一种观察是如何使用 `"sourceContext"`来投影 `"Keyphrases"` 的值。 `"Keyphrases"` 节点（包含字符串集合）本身就是页面文本的子节点。 但是，因为投影需要 JSON 对象，并且页面为基元（字符串），所以 `"sourceContext"` 用于将关键短语包装为具有命名属性的对象。 此方法允许对基元进行单独投影。

## <a name="next-steps"></a>后续步骤

作为下一步，请创建具有认知技能的第一个技能组合。

> [!div class="nextstepaction"]
> [创建第一个技能组合](cognitive-search-defining-skillset.md)。
