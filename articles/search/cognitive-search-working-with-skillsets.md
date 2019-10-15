---
title: 使用技能集-Azure 搜索
description: 技能集是在认知搜索中创作 AI 扩充管道、了解一些概念和技能集的工作方式如何允许构建简单或复杂的技能集
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: vikurpad
ms.openlocfilehash: f75e6dece376076d4aa5e33497aff7e4f9f56857
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265695"
---
# <a name="working-with-skillsets"></a>使用技能集
本文面向需要更深入地了解扩充管道工作方式的开发人员，并假设你对认知搜索过程有了大致了解。 如果你不熟悉认知搜索，请从开始：
+ [Azure 搜索中的 "认知搜索" 是什么？](cognitive-search-concept-intro.md)
+ [什么是 Azure 搜索中的知识存储？](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>指定技能组合
技能组合是 Azure 搜索中可重用的资源，用于指定在索引期间用于分析、转换和浓缩文本或图像内容的认知技能的集合。 通过创建技能组合，你可以在数据引入阶段中附加文本和图像根据，从原始内容中提取并创建新的信息和结构。

技能组合有三个属性：

+   ```skills```，一系列无序技能，平台根据每项技能所需的输入来确定执行顺序
+   ```cognitiveServices```，计费所调用认知技能所需的认知服务密钥
+   ```knowledgeStore```，将在其中投影已扩充的文档的存储帐户



技能集是采用 JSON 编写的。 您可以使用[表达式语言](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)通过循环和[分支](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-conditional)来构建复杂的技能集。 表达式语言使用[JSON 指针](https://tools.ietf.org/html/rfc6901)路径表示法进行一些修改，以标识扩充树中的节点。 遍历树中较低级别的，并```"*"```在上下文中充当每个运算符。 ```"/"``` 下面是一个示例，介绍了这些概念。 为了说明一些概念和功能，我们将指导你完成[宾馆审查示例](knowledge-store-connect-powerbi.md)技能组合。 若要在执行导入数据工作流后查看技能组合，需要使用 REST API 客户端[获取技能组合](https://docs.microsoft.com/en-us/rest/api/searchservice/get-skillset)。

### <a name="enrichment-tree"></a>扩充树

为了构想技能组合如何逐步丰富您的文档，让我们先从文档的外观开始，然后再进行任何扩充。 文档破解的输出取决于数据源以及所选的特定分析模式。 这也是在将数据添加到搜索索引时，[字段映射](search-indexer-field-mappings.md)可以从中源内容的文档的状态。
![“管道中的知识存储”示意图](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "“管道中的知识存储”示意图")

文档位于扩充管道中后，它将表示为内容树和关联的根据。 此树实例化为文档破解的输出。 使用扩充树格式，扩充管道可以将元数据附加到甚至基元数据类型，但它不是有效的 JSON 对象，但可以投影为有效的 JSON 格式。 下表显示了进入扩充管道的文档的状态：

|Data Source\Parsing 模式|默认|JSON，JSON 行 & CSV|
|---|---|---|
|Blob 存储|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|不可用 |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|不可用|

 随着技能的执行，他们将新节点添加到扩充树。 然后，这些新节点可用作下游技能的输入，投影到知识存储或映射到索引字段。 根据不可变：创建后，无法编辑节点。 由于您的技能集变得更加复杂，因此您的扩充树，但并非扩充树中的所有节点都需要将其设为索引或知识存储区。 可以有选择地仅将根据的一个子集保存到索引或知识存储区。

可以有选择地仅将根据的一个子集保存到索引或知识存储区。
对于本文档的其余部分，我们假设我们使用[宾馆审查示例](https://docs.microsoft.com/en-us/azure/search/knowledge-store-connect-powerbi)，但相同的概念也适用于从所有其他数据源中丰富的文档。

### <a name="context"></a>上下文
每项技能都需要一个上下文。 上下文确定：
+   根据所选节点执行技能的次数。 对于类型为集合的上下文值， ```/*```在结尾处添加将导致为集合中的每个实例调用一次技能。 
+   在扩充树中添加技能输出的位置。 输出始终作为上下文节点的子级添加到树中。 
+   输入的形状。 对于多级别集合，将上下文设置为父集合将影响输入技能的形状。 例如，如果你有一个包含国家/地区列表的扩充树，每个包含一个包含 zipcodes 列表的状态列表。

|上下文|输入|输入的形状|技能调用|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |该国家/地区中所有 zipcodes 的列表 |每个国家/地区 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |处于状态的 zipcodes 列表 | 每个国家/地区和状态组合一次|

### <a name="sourcecontext"></a>SourceContext

`sourceContext`仅用于[整形者技能](cognitive-search-skill-shaper.md)和[投影](knowledge-store-projection-overview.md)。 它用于构造多层嵌套对象。 `sourceContext`利用，您可以构造分层匿名类型的对象，如果您仅使用上下文，该对象将需要多项技能。 下`sourceContext`一节中显示了使用。

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

对于的技能上下文```"/document/reviews_text"```，此技能将为执行一次。 `reviews_text` 技能输出是一个列表，其中的`reviews_text`范围为5000个字符段。 拆分技能的输出将命名`pages`并添加到扩充树。 此`targetName`功能允许您在将技能输出添加到扩充树之前对其进行重命名。

现在，扩充树中有一个新节点放置在该技能的上下文中。 此节点可用于任何技能、预测或输出字段映射。


所有根据的根节点为`"/document"`。 使用 blob 索引器时， `"/document"`节点将具有和`"/document/normalized_images"`的`"/document/content"`子节点。 当使用 CSV 数据时，如本示例所示，列名称将映射到下面`"/document"`的节点。 若要访问通过技能添加到节点的任何根据，需要扩充的完整路径。 例如，如果要使用```pages```节点中的文本作为另一种技能的输入，则需要将其指定为。 ```"/document/reviews_text/pages/*"```
 
 ![技能 #1 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "执行技能 #1 后的扩充树")

### <a name="skill-2-language-detection"></a>技能 #2 语言检测
 尽管语言检测技能是在技能组合中定义的第三种（技能 #3）技术，但这是执行的下一项技能。 由于它不会被要求输入，因此它将与以前的技能并行执行。 就像前面的拆分技能一样，语言检测技能也只为每个文档调用一次。 扩充树现在具有新的语言节点。
 ![技能 #2 后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "执行技能 #2 后的扩充树")
 
 ### <a name="skill-3-key-phrases-skill"></a>技能 #3：关键短语技能 

对于```/document/reviews_text/pages/*``` 集合`pages`中的每个项，将为其调用一次关键短语技能的上下文。 技能的输出将是关联 page 元素下的一个节点。 

 现在，您应该可以查看技能组合中的其他技能，并直观显示根据的树如何随着每项技能的执行而不断增长。 一些技能（例如合并技能和整形者技能）还创建新节点，但只使用现有节点中的数据，而不创建 net new 根据。

![所有技能后的扩充树](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "所有技能后的扩充树")

上面树中的连接器颜色指示根据是由不同技能创建的，并且需要单独寻址节点，并且不会成为选择父节点时返回的对象的一部分。

## <a name="save-enrichments-in-a-knowledge-store"></a>将根据保存到知识库中 

技能集还定义了一个知识存储，您可以在其中将所扩充的文档投影为表或对象。 若要将您的已扩充数据保存在知识库中，可以定义一组丰富的文档的预测。 若要了解有关知识库的详细信息，请参阅[什么是 Azure 搜索中的知识存储？](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>切片投影

定义表投影组时，可以将扩充树中的单个节点切片为多个相关表。 如果您添加的表的源路径是现有表投影的子节点，则生成的子节点将不是现有表投影的子节点，而是投影到新的相关表中。 使用此切片技术，可以在一种整形程序技能中定义单个节点，这种技能可以是所有表投影的源。 

### <a name="shaping-projections"></a>定形投影

可以通过两种方式来定义投影。 您可以使用整形程序技能来创建新节点，该节点是要投影的所有根据的根节点。 然后，在您的计划中，只引用整形程序技能的输出。 您还可以在投影定义本身中内联绘制投影。

整形程序方法比内联整形更详细，但可确保扩充树的所有突变都包含在技能中，并确保输出是可重复使用的对象。 内联造型允许您创建所需的形状，但它是一个匿名对象，并且仅适用于其定义的投影。 这些方法可以一起使用，也可以单独使用。 门户工作流中为你创建的技能组合包含这两者。 它为表投影使用整形程序技能，但也使用内联造型来投影关键短语表。

若要扩展此示例，可以选择删除内联变形，并使用整形程序技能为关键短语创建新节点。 若要创建投影到三个表（即`hotelReviewsDocument` `hotelReviewsPages`、和`hotelReviewsKeyPhrases`）的形状，请参阅以下各节中所述的两个选项。


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

使用上述`tableprojection` `outputs`部分中定义的节点，现在可以使用切片功能将`tableprojection`节点的各个部分投影到不同的表中：

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
  
两种方法中的一种观察是如何`"Keyphrases"` `"sourceContext"`使用来投影的值。 `"Keyphrases"`节点（包含字符串集合）本身是页面文本的子节点。 但是，因为投影需要 JSON 对象，并且页面为基元（字符串）， `"sourceContext"`所以用于将密钥短语包装为具有命名属性的对象。 此方法允许对基元进行单独投影。

## <a name="next-steps"></a>后续步骤

作为下一步，请创建具有认知技能的第一个技能组合。

> [!div class="nextstepaction"]
> [创建第一个技能组合](cognitive-search-defining-skillset.md)。
