---
title: 在知识存储中定义投影
titleSuffix: Azure Cognitive Search
description: 有关如何将扩充的文档投影到知识存储，以便在 Power BI 或 Azure ML 中使用的常见模式示例。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f40841887b8116ad1384dc2b827d8215ea659490
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537491"
---
# <a name="how-to-shape-and-export-enrichments"></a>如何整形和导出扩充内容

投影是知识存储中扩充的文档的物理表达形式。 有效利用扩充的文档需要结构。 本文探讨结构和关系，介绍如何生成投影属性，以及如何关联所创建的不同投影类型的数据。 

若要创建投影，请使用[整形器技能](cognitive-search-skill-shaper.md)为数据整形以创建自定义对象，或者在投影定义中使用内联的整形语法。 

数据形状包含要投影的所有数据，其形式为节点的层次结构。 本文将介绍多种数据整形方法，使用户能够将数据投影到有利于报告、分析或下游处理的物理结构。 

本文中演示的示例可在此 [REST API 示例](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)中找到，可以在 HTTP 客户端中下载和运行该示例。

## <a name="introduction-to-projection-examples"></a>投影示例简介

有三种类型的[投影](knowledge-store-projection-overview.md)：

+ 表
+ 对象
+ 文件

表投影存储在 Azure 表存储中。 对象和文件投影将写入 Blob 存储，在此存储中，对象投影保存为 JSON 文件，可以包含源文档中的内容，以及任何技能输出或扩充内容。 扩充管道还可以提取图像等二进制文件，这些二进制文件投影为文件投影。 将二进制对象投影为对象投影时，只有与之关联的元数据才保存为 JSON Blob。 

为了理解数据整形与投影之间的相互关系，我们将使用以下技能集作为探索各种配置的基础。 此技能集处理原始图像和文本内容。 将会根据所需的方案，基于文档的内容以及技能的输出定义投影。

> [!IMPORTANT] 
> 体验投影时，[设置索引器缓存属性](search-howto-incremental-index.md)以确保控制成本的做法会很有用。 如果未设置索引器缓存，则编辑投影会导致再次扩充整个文档。 设置缓存并仅更新投影后，对以前已扩充的文档执行技能集不会产生任何新的认知服务费用。

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

使用此技能集并将其 null `knowledgeStore` 用作基础，第一个示例将填充 `knowledgeStore` 对象，该对象是使用创建表格数据结构的投影配置的，可在其他方案中使用这些结构。 

## <a name="projecting-to-tables"></a>投影到表

投影到 Azure 存储中的表对于使用 Power BI 等工具进行报告和分析非常有用。 Power BI 可从表中读取数据，并基于投影期间生成的键来发现关系。 如果你正在尝试生成仪表板，准备好相关的数据可以简化该任务。 

让我们生成一个仪表板，以便将从文档中提取的关键短语可视化为字云。 若要创建适当的数据结构，请在技能组中添加一个整形器技能，以创建包含文档特定的详细信息和关键短语的自定义形状。 该自定义形状在 `document` 根节点上称为 `pbiShape`。

> [!NOTE] 
> 表预测是 Azure 存储表，受限于 Azure 存储施加的存储限制。 有关详细信息，请参阅[表存储限制](/rest/api/storageservices/understanding-the-table-service-data-model)。 最好是知道实体大小不能超过 1 MB，且单个属性不能大于 64 KB。 这些约束使得表成了用于存储大量小型实体的适当解决方案。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用整形器技能创建自定义形状

创建可投影到表存储的自定义形状。 如果没有自定义形状，投影只能引用单个节点（每个输出对应一个投影）。 创建自定义形状可将各个元素聚合成新的逻辑整体，而该整体又可以投影为单个表，或者在一系列表之间进行切片和分布。 

在此示例中，自定义形状合并了元数据并标识了实体和关键短语。 对象称为 `pbiShape`，是 `/document` 下的父级。 

> [!IMPORTANT] 
> 整形的目的之一是确保所有扩充节点以适当格式的 JSON 表示，只有这样才能投影到知识存储。 当扩充树包含不是正确格式的 JSON 的节点时（例如，当扩充是字符串等基元的父级时），更是如此。
>
> 请注意最后两个节点 `KeyPhrases` 和 `Entities`。 这些节点通过 `sourceContext` 包装成有效的 JSON 对象。 之所以需要这样做，是因为 `keyphrases` 和 `entities` 是针对基元的扩充，只有在转换为有效的 JSON 后才能投影。
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

将上述整形器技能添加到技能集。 

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

准备好投影到表所需的所有数据后，接下来请使用表定义更新 knowledgeStore 对象。 此示例包含通过设置 `tableName`、`source` 和 `generatedKeyName` 属性定义的三个表。

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

可以执行以下步骤来处理工作：

1. 将 ```storageConnectionString``` 属性设置为有效的 V2 常规用途存储帐户连接字符串。  

1. 通过发出 PUT 请求来更新技能集。

1. 更新技能集后，运行索引器。 

现已获得一个包含三个表的正常运行的投影。 将这些表导入 Power BI 后，Power BI 应会自动发现关系。

在继续学习下一个示例之前，让我们回顾一下表投影的各个方面，以理解进行数据切片和关联的机制。

### <a name="slicing"></a>切片 

切片是将合并的整个形状细分为组成部分的技术。 结果包括独立但相关的、可单独使用的表。

在此示例中，`pbiShape` 是合并的形状（或扩充节点）。 在投影定义中，`pbiShape` 切片为附加的表，这样你就可以提取形状的组成部分：```keyPhrases``` 和 ```Entities```。 此技术在 Power BI 中非常有用，因为多个实体和关键短语与每个文档相关联，如果可以看到分类数据形式的实体和关键短语，则会获得更多的见解。

切片在父表与子表之间隐式生成关系，使用父表中的 ```generatedKeyName``` 在子表中创建同名的列。 

### <a name="naming-relationships"></a>命名关系

```generatedKeyName``` 和 ```referenceKeyName``` 属性用于关联表之间的数据，甚至可以关联投影类型之间的数据。 子表/投影中的每一行都有一个指向回父级的属性。 子级中的列或属性的名称是来自父级的 ```referenceKeyName```。 如果未提供 ```referenceKeyName```，服务默认使用来自父级中 ```generatedKeyName``` 的名称。 

Power BI 依赖于这些生成的键来发现表中的关系。 如果需要以不同的方式命名子表中的列，请在父表中设置 ```referenceKeyName``` 属性。 例如，将 ```generatedKeyName``` 设置为 pbiDocument 表的 ID，并将 ```referenceKeyName``` 设置为 DocumentID。 这将导致 pbiEntities 和 pbiKeyPhrases 表中包含名为 DocumentID 的文档 ID 的列。

## <a name="projecting-to-objects"></a>投影到对象

对象投影不存在表投影那样的限制，更适合用于投影大型文档。 此示例将整个文档作为对象投影发送。 对象投影限制为容器中的单个投影，并且无法切片。

为了定义对象投影，将在投影中使用 ```objects``` 数组。 可以使用整形器技能或使用对象投影的内联整形来生成新形状。 表示例演示的是创建形状和切片的方法，而本示例演示的是内联整形的用法。 

内联整形是指在投影输入的定义中创建新形状的功能。 内联整形创建一个匿名对象，该对象与整形器技能生成的对象相同（在本例中为 `pbiShape`）。 定义一个你不打算重复使用的形状时，内联整形非常有用。

投影属性是一个数组。 此示例将一个新的投影实例添加到数组，其中的 knowledgeStore 定义包含内联投影。 使用内联投影时，可以省略整形器技能。

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

文件投影是从源文档提取的图像，或者是可从扩充过程中投影出来的扩充的输出。 类似于对象投影，文件投影实现为 Azure 存储中的 Blob，并包含图像。 

为了生成文件投影，将在投影对象中使用 `files` 数组。 此示例将从文档中提取的所有图像投影到名为 `samplefile` 的容器。

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

## <a name="projecting-to-multiple-types"></a>投影到多个类型

更复杂的方案可能要求跨投影类型投影内容。 例如，如果需要将关键短语和实体等某些数据投影到表，请将文本的 OCR 结果和布局文本保存为对象，然后将图像投影为文件。 

此示例通过以下更改更新了技能组：

1. 为每个文档创建包含一行的表。
1. 创建与文档表相关的表，并将每个关键短语标识为此表中的一行。
1. 创建与文档表相关的表，并将每个实体标识为此表中的一行。
1. 创建一个对象投影，其中包含每个图像的布局文本。
1. 创建一个文件投影，用于投影提取的每个图像。
1. 创建一个交叉引用表，其中包含对文档表、带有布局文本的对象投影以及文件投影的引用。

这些更改进一步反映在 knowledgeStore 定义中。 

### <a name="shape-data-for-cross-projection"></a>为交叉投影整形数据

若要获取这些投影所需的形状，请首先添加一个新的整形器技能来创建名为 `crossProjection` 的形状对象。 

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

在合并的 crossProjection 对象中，将对象切片成多个表，将 OCR 输出捕获为 Blob，然后将图像另存为文件（也在 Blob 存储中）。

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

对象投影要求每个投影（对象投影或文件投影）的容器名称不能共享某个容器。 

### <a name="relationships-among-table-object-and-file-projections"></a>表投影、对象投影与文件投影之间的关系

此示例还重点演示了投影的另一项功能。 通过在同一投影对象内定义多种类型的投影，可以在不同类型（表、对象、文件）之内和之间表达一种关系。 这样你就可以从文档的表行开始，在对象投影中查找该文档中的图像的所有 OCR 文本。 

如果你不希望关联数据，请在不同的投影对象中定义投影。 例如，以下代码片段会导致关联表，但不会在表与对象（OCR 文本）投影之间建立关系。 

根据不同的需求投影不同形状中的相同数据时，投影组非常有用。 例如，Power BI 仪表板的投影组，以及用于捕获数据（这些数据用于训练自定义技能中包装的机器学习模型）的另一个投影组。

生成不同类型的投影时，首先会生成文件和对象投影，并将路径添加到表中。

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

定义投影时，有几个常见问题可能会导致意外的结果。 如果知识存储中的输出不符合预期，请检查是否存在这些问题。

+ 无法将字符串扩充整形为有效的 JSON。 扩充字符串后（例如，使用关键短语扩充 `merged_content`），扩充的属性将表示为扩充树中 `merged_content` 的子级。 默认的表示形式不是适当格式的 JSON。 因此在投影时，请确保将扩充转换为包含名称和值的有效 JSON 对象。

+ 省略源路径末尾的 ```/*```。 如果投影的源是 `/document/pbiShape/keyPhrases`，则关键字短语数组将投影为单个对象/行。 请改为将源路径设置为 `/document/pbiShape/keyPhrases/*`，以便为每个关键短语生成单个行或对象。

+ 路径语法错误。 路径选择器区分大小写，如果不对选择器使用确切的大小写，可能会导致出现“缺少输入”警告。

## <a name="next-steps"></a>后续步骤

本文中的示例演示了有关如何创建投影的常用模式。 充分理解概念后，接下来可以运用更丰富的知识来为特定的方案生成投影。

在探索新功能时，请考虑将增量扩充作为下一步。 增量扩充基于缓存，可让你重复使用任何不受技能集修改所影响的扩充。 增量扩充对于包含 OCR 和图像分析的管道特别有用。

> [!div class="nextstepaction"]
> [增量扩充和缓存简介](cognitive-search-incremental-indexing-conceptual.md)

有关投影的概述，请详细了解组和切片等功能，以及[如何在技能组中对其进行定义](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [知识存储中的投影](knowledge-store-projection-overview.md)