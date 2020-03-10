---
title: 定义知识库中的投影
titleSuffix: Azure Cognitive Search
description: 有关如何将已将文档纳入到知识库以与 Power BI 或 Azure ML 一起使用的常见模式的示例。
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943683"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>知识存储投影：如何调整和导出根据

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

投影是在知识存储中丰富文档的物理表达式。 有效地使用已扩充的文档需要结构。 在本文中，你将浏览结构和关系，学习如何构建出投影属性，以及如何跨你创建的投影类型建立数据关联。 

若要创建投影，必须使用[整形技术](cognitive-search-skill-shaper.md)创建自定义对象，或在投影定义内使用内联定形语法来形成数据。 

数据形状包含要投影的所有数据，这些数据构成为节点的层次结构。 本文介绍了几种用于调整数据的方法，以便将数据投影到有利于报告、分析或下游处理的物理结构。 

本文中介绍的示例可在此[REST API 示例](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)中找到，可在 HTTP 客户端中下载并运行。

## <a name="introduction-to-the-examples"></a>示例简介

如果你熟悉[投影](knowledge-store-projection-overview.md)，你会想起以下三种类型：

+ 表
+ 对象
+ 文件

表投影存储在 Azure 表存储中。 对象和文件投影将写入 blob 存储，在这种存储中，对象投影保存为 JSON 文件，并且可以包含源文档中的内容，以及任何技能输出或根据。 扩充管道还可以提取类似于映像的二进制文件，这些二进制文件投影为文件投影。 将二元对象投影为对象投影时，只会将与之关联的元数据保存为 JSON blob。 

为了理解数据定形与投影之间的交集，我们将使用以下技能组合作为浏览各种配置的基础。 此技能组合处理原始图像和文本内容。 对于我们要支持的方案，将从文档的内容和技能的输出中定义投影。

> [!IMPORTANT] 
> 当试验投影时，[设置索引器缓存属性](search-howto-incremental-index.md)以确保成本控制很有用。 如果未设置索引器缓存，编辑投影将导致重新生成整个文档。 设置缓存并仅更新投影后，以前已技能组合的文档的执行的执行不会导致任何新的认知服务费用。

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

使用此技能组合，并将其作为其 null `knowledgeStore` 作为基础，我们的第一个示例将填充 `knowledgeStore` 对象，并使用创建可在其他方案中使用的表格数据结构的投影进行配置。 

## <a name="projecting-to-tables"></a>投影到表

在 Azure 存储中投影到表格对于使用 Power BI 等工具进行报告和分析非常有用。 Power BI 可以从表中读取，并基于在投影期间生成的键来发现关系。 如果你正在尝试构建仪表板，则具有相关数据将简化该任务。 

假设我们要构建一个仪表板，以便可以将从文档中提取的关键短语可视化为 word cloud。 若要创建正确的数据结构，可以在技能组合中添加一个整形程序技能，以创建具有特定于文档的详细信息和关键短语的自定义形状。 自定义形状将在 `document` 根节点上 `pbiShape` 调用。

> [!NOTE] 
> 表预测是 Azure 存储表，受 Azure 存储施加的存储限制的制约。 有关详细信息，请参阅[表存储限制](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)。 知道实体大小不能超过 1 MB，并且单个属性不能大于 64 KB，这会很有用。 这些约束使表成为存储大量小型实体的良好解决方案。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用整形技能创建自定义形状

创建可投影到表存储的自定义形状。 如果没有自定义形状，投影只能引用单个节点（每个输出一个投影）。 通过创建自定义形状，您可以将各种元素聚合为一个可以投影为单个表或在一组表中切片和分布的新的逻辑整体。 

在此示例中，自定义形状组合了元数据和标识的实体和关键短语。 对象被调用 `pbiShape`，并以 `/document`为父级。 

> [!IMPORTANT] 
> 调整的一个目的是确保所有扩充节点都用格式正确的 JSON 表示，这对于投影到知识库而言是必需的。 当扩充树包含不是格式正确的 JSON 的节点时（例如，当扩充为基元（如字符串）时，更是如此。
>
> 请注意最后两个节点，`KeyPhrases` 和 `Entities`。 这些将使用 `sourceContext`包装到有效的 JSON 对象。 这是必需的，因为 `keyphrases` 和 `entities` 在基元上根据，需要先将其转换为有效的 JSON，然后才能进行投影。
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

将上面的整形技术添加到技能组合。 

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

现在，我们已经拥有了投影到表所需的所有数据，请使用表定义更新 knowledgeStore 对象。 在此示例中，我们有三个表，通过设置 `tableName`、`source` 和 `generatedKeyName` 属性来定义。

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

可以通过执行以下步骤来处理工作：

1. 将 ```storageConnectionString``` 属性设置为有效的 V2 常规用途存储帐户连接字符串。  

1. 通过发出 PUT 请求来更新技能组合。

1. 更新技能组合后，运行索引器。 

现在，你有了一个包含三个表的工作投影。 将这些表导入到 Power BI 应导致 Power BI 自动发现关系。

在继续下一个示例之前，让我们回顾一下表投影的各个方面，以了解切片和相关数据的机制。

### <a name="slicing"></a>切片 

切片是一种技术，它将整个合并的形状细分构成组成部分。 结果由可单独处理的单独但相关的表组成。

在此示例中，`pbiShape` 是合并形状（或扩充节点）。 在投影定义中，将 `pbiShape` 切片为其他表，这样您就可以提取形状的各个部分，```keyPhrases``` 和 ```Entities```。 在 Power BI 中，这种方法非常有用，因为多个实体和 keyPhrases 都与每个文档相关联，如果你可以看到实体和 keyPhrases 作为已分类数据，你将获得更多见解。

切片隐式生成父表和子表之间的关系，使用父表中的 ```generatedKeyName``` 在子表中创建具有相同名称的列。 

### <a name="naming-relationships"></a>命名关系

"```generatedKeyName```" 和 "```referenceKeyName```" 属性用于跨表甚至跨投影类型建立数据关联。 子表/投影中的每一行都有一个返回父级的属性。 子中的列或属性的名称是来自父级的 ```referenceKeyName```。 如果未提供 ```referenceKeyName```，服务会默认从父项的 ```generatedKeyName```。 

Power BI 依赖于这些生成的密钥来发现表中的关系。 如果需要以不同方式命名的子表中的列，请设置父表的 ```referenceKeyName``` 属性。 例如，将 ```generatedKeyName``` 设置为 pbiDocument 表的 ID，并将 ```referenceKeyName``` 设置为 DocumentID。 这将导致 pbiEntities 和 pbiKeyPhrases 表中包含名为 DocumentID 的文档 id 的列。

## <a name="projecting-to-objects"></a>投影到对象

对象投影没有与表投影相同的限制，并且更适合用于投影大型文档。 在此示例中，我们将整个文档投影到对象投影。 对象投影限制为容器中的单个投影，并且无法切片。

为了定义对象投影，我们将在投影中使用 ```objects``` 数组。 您可以使用整形技能或使用对象投影的内联造型来生成新的形状。 尽管 "表" 示例演示了创建形状和切片的方法，但此示例演示如何使用内联定形。 

内联造型是指在投影的输入定义中创建新形状的功能。 内联定形创建的匿名对象与创建者技能的方式相同（在本例中为 `pbiShape`）。 如果要定义一个不打算重复使用的形状，则内联造型非常有用。

投影属性是一个数组。 在此示例中，我们将向数组中添加一个新的投影实例，其中 knowledgeStore 定义包含内联投影。 使用内联投影时，可以忽略整形能力的技能。

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

文件投影是从源文档中提取的图像，或者是可从扩充进程中提取的扩充的输出。 类似于对象投影的文件投影作为 Azure 存储中的 blob 实现，并包含映像。 

若要生成文件投影，请在投影对象中使用 `files` 数组。 此示例将从文档中提取的所有图像投影到名为 `samplefile`的容器。

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

更复杂的方案可能要求你跨投影类型投影内容。 例如，如果需要将关键短语和实体等一些数据投影到表，请将文本和布局文本的 OCR 结果保存为对象，然后将图像投影为文件。 

在此示例中，技能组合的更新包括以下更改：

1. 为每个文档创建一个包含行的表。
1. 创建与 "文档" 表相关的表，其中每个关键短语标识为此表中的一行。
1. 创建与文档表相关的表，其中每个实体标识为此表中的一行。
1. 创建一个对象投影，其中包含每个图像的布局文本。
1. 创建文件投影，投影每个提取的映像。
1. 创建一个交叉引用表，其中包含对文档表的引用、带有布局文本和文件投影的对象投影。

这些更改将在 knowledgeStore 定义中进一步反映出来。 

### <a name="shape-data-for-cross-projection"></a>跨投影的形状数据

若要获取这些投影所需的形状，请首先添加一个新的整形程序技能，用于创建名为 `crossProjection`的形状对象。 

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

从合并的 crossProjection 对象中，我们可以将对象分割成多个表，将 OCR 输出捕获为 blob，然后将图像另存为文件（也在 Blob 存储中）。

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

对象投影需要每个投影使用容器名称，对象投影或文件投影不能共享容器。 

### <a name="relationships-among-table-object-and-file-projections"></a>表、对象与文件投影之间的关系

此示例还强调了投影的另一项功能。 通过在同一投影对象内定义多种类型的投影，可以在不同的类型（表、对象、文件）内和之间表示关系，使您能够从文档的表行开始，并查找图像的所有 OCR 文本在该文档中的对象投影中。 

如果您不希望数据相关，请在不同投影对象中定义投影。 例如，以下代码片段将导致表相关，但不会在表和对象（OCR 文本）投影之间建立关系。 

当您希望针对不同需求在不同形状中投影相同数据时，投影组非常有用。 例如，"Power BI" 仪表板的投影组，另一个用于捕获用于定型在自定义技能中包装的机器学习模型的数据的投影组。

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

定义投影时，有几个常见问题会导致意外的结果。 如果知识存储中的输出与预期不符，请检查是否存在这些问题。

+ 不将字符串根据为有效的 JSON。 如果对字符串进行了丰富的处理（例如 `merged_content` 使用关键短语进行了丰富），则会在扩充树中将所丰富的属性表示为 `merged_content` 的子元素。 默认表示形式不是格式正确的 JSON。 在投影时，请确保将扩充转换为有效的 JSON 对象，其中包含名称和值。

+ 省略源路径末尾的 ```/*```。 如果投影的源是 `/document/pbiShape/keyPhrases`，则将关键字短语数组投影为单个对象/行。 相反，请将源路径设置为 `/document/pbiShape/keyPhrases/*`，以便为每个关键短语生成单个行或对象。

+ 路径语法错误。 如果未对选择器使用确切的大小写，则路径选择器区分大小写，并且可能会导致缺少输入警告。

## <a name="next-steps"></a>后续步骤

本文中的示例演示如何创建投影的常见模式。 既然您已经充分了解这些概念，就能更好地为您的特定方案构建预测。

当你探索新功能时，请考虑增量扩充作为下一步。 增量扩充基于缓存，可让你重复使用任何不受技能组合修改影响的根据。 这对于包含 OCR 和图像分析的管道特别有用。

> [!div class="nextstepaction"]
> [增量扩充和缓存简介](cognitive-search-incremental-indexing-conceptual.md)

有关预测的概述，请了解有关组和切片等功能的详细信息，以及如何[在技能组合中定义这些](knowledge-store-projection-overview.md)功能

> [!div class="nextstepaction"]
> [知识存储中的投影](knowledge-store-projection-overview.md)

