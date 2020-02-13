---
title: 定义知识库中的投影
titleSuffix: Azure Cognitive Search
description: 有关如何将已将文档纳入到知识库以与 Power BI 或 Azure ML 一起使用的常见模式的示例。
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165509"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>知识存储投影：如何将根据形状和导出到知识库

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前提供有限的门户支持，不提供 .NET SDK 支持。

投影是在知识存储中丰富文档的物理表达式。 有效地使用已扩充的文档需要结构。 在本文中，你将浏览结构和关系，学习如何构建出投影属性，以及如何跨你创建的投影类型建立数据关联。 

若要创建投影，必须使用整形技术创建一个自定义对象或使用内联定形语法来形成数据。 数据形状包含要投影的所有数据。 本文档提供了有关每个选项的示例，你可以选择使用创建的两个选项中的任意一个。


有三种类型的投影：
+ 表
+ 对象
+ 文件

表投影存储在 Azure 表存储中。 对象和文件投影将写入 blob 存储，对象投影保存为 JSON 文件，并且可以包含文档内容和任何技能输出或根据。 扩充管道还可以提取类似于映像的二进制文件，这些二进制文件投影为文件投影。 将二元对象投影为对象投影时，只会将与之关联的元数据保存为 JSON blob。 

为了理解数据定形与投影之间的交集，我们将使用以下技能组合作为浏览各种配置的基础。 此技能组合处理原始图像和文本内容。 对于我们要支持的方案，将从文档的内容和技能的输出中定义投影。

此外，还可以下载并使用本演练中的所有调用的[REST API 示例](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)。

> [!IMPORTANT] 
> 当试验投影时，[设置索引器缓存属性](search-howto-incremental-index.md)以确保成本控制很有用。 如果未设置索引器缓存，编辑投影将导致重新生成整个文档。 设置缓存并仅更新投影时，以前已技能组合的文档的执行执行不会导致任何认知服务费用。


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

现在，可以添加 `knowledgeStore` 对象，并根据需要配置每个方案的投影。 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>投影到 Power BI 等方案的表

> [!NOTE] 
> 由于知识存储是 Azure 存储帐户，因此表预测是 Azure 存储表，由表的存储限制来控制。有关详细信息，请参阅[表存储限制](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)。 知道实体大小不能超过 1 MB，并且单个属性不能大于 64 KB，这会很有用。 这些约束使表成为存储大量小型实体的良好解决方案。

Power BI 可以从表中读取数据，并根据知识库所创建的键来发现关系，这样，在您尝试在您的数据上构建仪表板时，这使得表成为项目数据的一个不错选择。 假设我们要尝试生成一个仪表板，以便可以将从文档中提取的关键短语可视化为 word cloud，我们可以向技能组合添加一个整形程序技能，以创建具有特定于文档的详细信息和关键短语的自定义形状。 将整形技能添加到技能组合，以便在 ```document```上创建名为 ```pbiShape``` 的新扩充。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>使用整形技能创建自定义形状

创建可投影到表存储的自定义形状。 如果没有自定义形状，投影只能引用单个节点（每个输出一个投影）。 通过创建自定义形状，您可以将各种元素聚合为一个可以投影为单个表或在一组表中切片和分布的新的逻辑整体。 在此示例中，自定义形状组合了元数据和标识的实体和关键短语。 对象称为 pbiShape，其父级为 `/document`。 

> [!IMPORTANT] 
> 在投影之前，根据的源路径必须是格式正确的 JSON 对象。 扩充树可以表示不是格式正确的 JSON 的根据，例如，当扩充是像字符串这样的基元的父级。 请注意如何使用 `sourceContext`将 `KeyPhrases` 和 `Entities` 包装到有效的 JSON 对象中，这是必需的，`entities` `keyphrases` 因为在基元上根据并需要将其转换为有效的 JSON，然后才能对其进行投影。

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
将我们刚才定义的整形者技能添加到技能组合中的技能列表。 

现在，我们已经拥有了投影到表所需的所有数据，请使用表定义更新 knowledgeStore 对象。 

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

将 ```storageConnectionString``` 属性设置为有效的 V2 常规用途存储帐户连接字符串。 在此方案中，我们通过设置 ```tableName```、```source``` 和 ```generatedKeyName``` 属性，在投影对象中定义三个表。 你现在可以通过发出 PUT 请求来更新技能组合。

### <a name="slicing"></a>切片 

当从一个合并的形状开始，其中所有需要投影的内容位于单个形状（或扩充节点）中时，切片提供将单个节点切片为多个表或对象的功能。 此处，```pbiShape``` 对象将切片到多个表中。 利用切片功能，您可以将形状的各个部分（```keyPhrases``` 和 ```Entities``` 拖放到单独的表中。 这非常有用，因为多个实体与每个文档关联 keyPhrases。 切片 implicity 将使用父表中的 ```generatedKeyName``` 在子表中创建具有相同名称的列，从而生成父表和子表之间的关系。 

### <a name="naming-relationships"></a>命名关系
"```generatedKeyName```" 和 "```referenceKeyName```" 属性用于跨表甚至跨投影类型建立数据关联。 子表/投影中的每一行都有一个返回父级的属性。 子中的列或属性的名称是来自父级的 ```referenceKeyName```。 如果未提供 ```referenceKeyName```，服务会默认从父项的 ```generatedKeyName```。 PowerBI 依赖于这些生成的密钥来发现表中的关系。 如果需要以不同方式命名的子表中的列，请设置父表的 ```referenceKeyName``` 属性。 例如，将 ```generatedKeyName``` 设置为 pbiDocument 表的 ID，并将 ```referenceKeyName``` 设置为 DocumentID。 这将导致 pbiEntities 和 pbiKeyPhrases 表中包含名为 DocumentID 的文档 id 的列。

保存已更新的技能组合并运行索引器，现在有一个包含三个表的工作投影。 将这些表导入到 Power BI 应导致 Power BI 自动发现关系。

## <a name="projecting-to-objects"></a>投影到对象

对象投影与表投影没有相同的限制，更适合用于投影大型文档。 在此示例中，我们将整个文档投影到对象投影。 对象投影限制为容器中的单个投影。
为了定义对象投影，我们将在投影中使用 ```objects``` 数组。 您可以使用整形技能或使用对象投影的内联造型来生成新的形状。 尽管 "表" 示例演示了创建形状和切片的方法，但此示例演示如何使用内联定形。 使用内联整形功能，您可以在投影的输入定义中创建新的形状。 内联定形创建与类似的整形程序产生的内容完全相同的匿名对象。 如果要定义一个不打算重复使用的形状，则内联造型非常有用。
投影属性是一个数组，在本示例中，我们要将新的投影实例添加到该数组。 使用以内联方式定义的投影更新 knowledgeStore 定义时，使用内联投影时无需使用整形技术。

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
## <a name="file-projections"></a>文件投影

文件投影是从源文档中提取的图像，或者是可从扩充进程中提取的根据的输出。 类似于对象投影的文件投影作为 blob 实现并包含映像。 若要生成文件投影，请在投影对象中使用 ```files``` 数组。 此示例将从文档中提取的所有图像投影到名为 `samplefile`的容器。

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

更复杂的方案可能要求你跨投影类型投影内容。 例如，如果需要将关键短语和实体等一些数据投影到表，请将文本和布局文本的 OCR 结果保存为对象，并将图像投影为文件。 对技能组合的此更新将：

1. 为每个文档创建一个包含行的表。
2. 创建与 "文档" 表相关的表，其中每个关键短语标识为此表中的一行。
3. 创建与文档表相关的表，其中每个实体标识为此表中的一行。
4. 创建一个对象投影，其中包含每个图像的布局文本。
5. 创建文件投影，投影每个提取的映像。
6. 创建一个交叉引用表，其中包含对文档表的引用、带有布局文本和文件投影的对象投影。

首先将新的整形程序技能添加到创建形状对象的技能数组。 

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

### <a name="relationships"></a>关系

此示例还通过在同一投影对象中定义了多个类型的投影，突出了投影的另一项功能，其中有一种在投影的不同类型（表、对象、文件）之间表示的关系，允许从文档的表行开始，并在对象投影中查找该文档内的图像的所有 OCR 文本。 如果您不希望数据相关，则在不同的投影对象中定义投影，例如，以下代码片段将导致表相关，但不会在表和 OCR 文本投影之间建立关系。 当您希望针对不同需求在不同形状中投影相同数据时，投影组非常有用。 例如，"Power BI" 仪表板的投影组和另一个投影组，用于使用数据训练技能的 AI 模型。
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

这些示例演示了如何使用投影的常见模式，现在还应充分了解针对特定方案生成投影的概念。

## <a name="common-issues"></a>常见问题

定义投影时，有几个常见问题会导致意外的结果。

1. 不造型字符串根据。 如果对字符串进行了丰富的处理（例如 ```merged_content``` 使用关键短语进行了丰富），则会在扩充树中将所丰富的属性表示为 merged_content 的子元素。 但在投影时，需要将其转换为具有名称和值的有效 JSON 对象。
2. 省略源路径末尾的 ```/*```。 例如，如果投影的源是 ```/document/pbiShape/keyPhrases``` 关键短语数组作为单个对象/行进行投影。 将源路径设置为 ```/document/pbiShape/keyPhrases/*``` 会为每个关键短语生成单个行或对象。
3. 如果未对选择器使用确切的大小写，则路径选择器区分大小写，并且可能会导致缺少输入警告。

