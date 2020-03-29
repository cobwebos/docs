---
title: 定义知识存储中的投影
titleSuffix: Azure Cognitive Search
description: 有关如何将富集文档投影到知识存储中以便与 Power BI 或 Azure ML 一起使用的常见模式示例。
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943683"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>知识库预测：如何塑造和导出浓缩

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

投影是知识存储中丰富文档的物理表达。 有效利用富集文档需要结构。 在本文中，您将探讨结构和关系，了解如何构建投影属性，以及如何跨您创建的投影类型关联数据。 

要创建投影，必须使用[Shaper 技能](cognitive-search-skill-shaper.md)来创建自定义对象或使用投影定义中的内联整形语法来塑造数据。 

数据形状包含您要投影的所有数据，这些数据形成为节点层次结构。 本文介绍几种塑造数据的技术，以便将其投影到有利于报告、分析或下游处理的物理结构中。 

本文中介绍的示例可以在此 REST API[示例中](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)找到，您可以在 HTTP 客户端中下载和运行该示例。

## <a name="introduction-to-the-examples"></a>示例简介

如果您熟悉[投影](knowledge-store-projection-overview.md)，您会记得有三种类型：

+ 表
+ 对象
+ 文件

表投影存储在 Azure 表存储中。 对象和文件投影写入 blob 存储，其中对象投影保存为 JSON 文件，可以包含源文档中的内容以及任何技能输出或扩充。 扩充管道还可以提取二进制文件，如图像，这些二进制文件投影为文件投影。 当二进制对象投影为对象投影时，只有与其关联的元数据保存为 JSON Blob。 

为了了解数据整形和投影之间的交集，我们将使用以下技能集作为探索各种配置的基础。 此技能集处理原始图像和文本内容。 投影将从文档的内容和技能的输出中定义，用于我们想要支持的方案。

> [!IMPORTANT] 
> 试验投影时，[设置索引器缓存属性](search-howto-incremental-index.md)以确保成本控制非常有用。 如果未设置索引器缓存，则编辑投影将导致整个文档再次丰富。 设置缓存且仅更新投影时，以前丰富的文档的技能集执行不会导致任何新的认知服务费用。

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

使用此技能集（以 null`knowledgeStore`为基础），我们的第一个示例填充`knowledgeStore`对象，配置了创建可在其他方案中使用的表格数据结构的预测。 

## <a name="projecting-to-tables"></a>投影到表

投影到 Azure 存储中的表对于使用 Power BI 等工具进行报告和分析非常有用。 Power BI 可以从表中读取，并根据投影期间生成的键发现关系。 如果您尝试构建仪表板，则拥有相关数据将简化该任务。 

假设我们正在尝试构建一个仪表板，您可以在其中将从文档中提取的关键短语可视化为单词云。 为了创建正确的数据结构，我们可以向技能集中添加 Shaper 技能，以创建具有文档特定详细信息和关键短语的自定义形状。 将在`pbiShape``document`根节点上调用自定义形状。

> [!NOTE] 
> 表投影是 Azure 存储表，受 Azure 存储施加的存储限制的约束。 有关详细信息，请参阅[表存储限制](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)。 了解实体大小不能超过 1 MB 且单个属性不能大于 64 KB 是很有用的。 这些约束使表成为存储大量小实体的良好解决方案。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用沙珀技能创建自定义形状

创建可投影到表存储的自定义形状。 如果没有自定义形状，投影只能引用单个节点（每个输出一个投影）。 通过创建自定义形状，可以将各种元素聚合到一个新的逻辑整体中，该集合可以投影为单个表，也可以在表集合中切片和分发。 

在此示例中，自定义形状结合了元数据和标识的实体和关键短语。 对象在 下`pbiShape``/document`调用并处于父级。 

> [!IMPORTANT] 
> 整形的一个目的是确保所有富集节点都以格式良好的 JSON 表示，这是投影到知识存储所必需的。 当富集树包含格式不正确的 JSON 的节点（例如，当富集父为像字符串这样的基元时），尤其如此。
>
> 请注意最后两个节点`KeyPhrases`和`Entities`。 这些将用 打包到有效的 JSON`sourceContext`对象中。 这在基元上`keyphrases`需要`entities`并且是扩充，并且需要转换为有效的 JSON，然后才能投影它们。
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

将上述沙珀技能添加到技能集中。 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

现在，我们已经拥有投影到表所需的所有数据，请使用表定义更新知识存储对象。 在此示例中，我们有三个表，由 设置`tableName`和`source``generatedKeyName`属性定义。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

您可以按照以下步骤处理您的工作：

1. 将```storageConnectionString```属性设置为有效的 V2 通用存储帐户连接字符串。  

1. 通过发出 PUT 请求更新技能集。

1. 更新技能集后，运行索引器。 

现在，您有一个包含三个表的工作投影。 将这些表导入 Power BI 应会导致 Power BI 自动发现关系。

在继续下一个示例之前，让我们重新访问表投影的各个方面，以了解切片和关联数据的机制。

### <a name="slicing"></a>切片 

切片是将整个合并形状细分为各个组成部分的技术。 结果由单独但相关的表组成，您可以单独使用这些表。

在此示例中，`pbiShape`是合并形状（或扩充节点）。 在投影定义中，`pbiShape`将切片到其他表中，这使您能够拉出形状的部分，```keyPhrases```和```Entities```。 在 Power BI 中，这非常有用，因为多个实体和键短语与每个文档相关联，如果您可以看到实体和键短语作为分类数据，您将获得更多见解。

隐式切片会在父表和子表之间生成关系，使用```generatedKeyName```父表中的 创建子表中具有相同名称的列。 

### <a name="naming-relationships"></a>命名关系

和```generatedKeyName``````referenceKeyName```属性用于跨表甚至跨投影类型关联数据。 子表/投影中的每个行都有一个指向父行的属性。 子列或子属性的名称是从```referenceKeyName```父项。 如果未提供```referenceKeyName```时，服务将默认为 父服务中的```generatedKeyName```。 

Power BI 依赖于这些生成的键来发现表中的关系。 如果需要以不同方式命名的子表中的列，则在```referenceKeyName```父表中设置该属性。 例如，```generatedKeyName```在 pbiDocument 表上设置作为 ID，```referenceKeyName```将 设置为文档 ID。 这将导致 pbi实体和 pbiKeyPhrases 表中包含文档 ID 的列名为 DocumentID。

## <a name="projecting-to-objects"></a>投影到对象

对象投影与表投影没有相同的限制，更适合投影大型文档。 在此示例中，我们将整个文档投影到对象投影。 对象投影仅限于容器中的单个投影，不能切片。

要定义对象投影，我们将在投影中使用```objects```数组。 您可以使用 Shaper 技能生成新形状，或使用对象投影的内联整形。 虽然表示例演示了创建形状和切片的方法，但此示例演示了内联整形的使用。 

内联整形是在投影的输入定义中创建新形状的能力。 内联整形创建一个匿名对象，该对象与 Shaper 技能将生成的内容相同（`pbiShape`在我们的案例中， ）。 如果要定义不打算重用的形状，则内联整形非常有用。

投影属性是数组。 在此示例中，我们将向数组添加新的投影实例，其中知识存储定义包含内联投影。 使用内联投影时，可以省略 Shaper 技能。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>投影到文件

文件投影是从源文档中提取的图像，也可以是从浓缩过程中投影的扩充输出。 文件投影（类似于对象投影）在 Azure 存储中作为 blob 实现，并包含映像。 

要生成文件投影，我们使用投影对象中的`files`数组。 本示例将从文档中提取的所有图像项目到名为 的`samplefile`容器。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>投影到多种类型

更复杂的方案可能需要跨投影类型投影内容。 例如，如果需要将某些数据（如关键短语和实体）投影到表中，则将文本和布局文本的 OCR 结果另存为对象，然后将图像投影为文件。 

在此示例中，对技能集的更新包括以下更改：

1. 为每个文档创建具有行的表。
1. 创建与文档表相关的表，每个键短语在此表中标识为行。
1. 创建与文档表相关的表，每个实体在此表中标识为一行。
1. 使用每个图像的布局文本创建对象投影。
1. 创建文件投影，投影每个提取的图像。
1. 创建一个交叉引用表，其中包含对文档表的引用、带有布局文本的对象投影和文件投影。

这些更改将反映在知识存储定义中。 

### <a name="shape-data-for-cross-projection"></a>用于交叉投影的形状数据

要获取这些投影所需的形状，首先添加一个新的 Shaper 技能，该技能创建一个称为`crossProjection`的形状对象。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>定义表、对象和文件投影

从整合的 crossProjection 对象中，我们可以将对象切片到多个表中，将 OCR 输出捕获为 Blob，然后将图像另存为文件（也保存在 Blob 存储中）。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

对象投影需要每个投影的容器名称，对象投影或文件投影不能共享容器。 

### <a name="relationships-among-table-object-and-file-projections"></a>表、对象和文件投影之间的关系

此示例还突出显示了投影的另一个特征。 通过在同一投影对象中定义多种类型的投影，在不同类型的类型（表、对象、文件）内和跨类型（表、对象、文件）中表达了一种关系，允许您从文档的表行开始，并查找图像的所有 OCR 文本在对象投影中的文档中。 

如果不希望与数据相关，请在不同的投影对象中定义投影。 例如，以下代码段将导致表相关，但表和对象 （OCR 文本） 投影之间没有关系。 

当您希望针对不同需求以不同形状投影相同数据时，投影组非常有用。 例如，Power BI 仪表板的投影组以及用于捕获用于训练以自定义技能包装的机器学习模型的数据的另一个投影组。

生成不同类型的投影时，将首先生成文件和对象投影，并将路径添加到表中。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>常见问题

定义投影时，有几个常见问题可能会导致意外的结果。 如果知识存储中的输出不是您期望的，请检查这些问题。

+ 不将字符串扩充成有效的 JSON。 当字符串富集时（例如`merged_content`，用关键短语进行丰富）时，富集属性表示为`merged_content`富集树中的子项。 默认表示形式格式不正确。 因此，在投影时，请确保将富集转换为具有名称和值的有效 JSON 对象。

+ 省略源```/*```路径末尾的 。 如果投影的源为`/document/pbiShape/keyPhrases`，则关键短语数组将投影为单个对象/行。 相反，将源路径设置为`/document/pbiShape/keyPhrases/*`为每个关键短语生成单个行或对象。

+ 路径语法错误。 路径选择器区分大小写，如果不对选择器使用确切大小写，则可能导致缺少输入警告。

## <a name="next-steps"></a>后续步骤

本文中的示例演示了有关如何创建投影的常见模式。 现在，您已经很好地理解了这些概念，因此您更有能力为特定方案构建投影。

在探索新功能时，请考虑增量充实作为下一步。 增量扩充基于缓存，它允许您重用任何不受技能集修改影响的富集。 这对于包含 OCR 和图像分析的管道特别有用。

> [!div class="nextstepaction"]
> [增量浓缩和缓存简介](cognitive-search-incremental-indexing-conceptual.md)

有关投影的概述，请详细了解组和切片等功能，以及如何[在技能集中定义它们](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [知识商店中的投影](knowledge-store-projection-overview.md)

