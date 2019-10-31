---
title: 图像分析认知搜索技能 - Azure 搜索
description: 在 Azure 搜索扩充管道中使用 ImageAnalysis 认知技能通过图像分析来提取语义文本。
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: luisca
ms.openlocfilehash: e2c8f0519ffcbdbc2445d1fed2725b6f6b948cd1
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064075"
---
#   <a name="image-analysis-cognitive-skill"></a>图像分析认知技能

图像分析技能根据图像内容提取一组丰富的可视特征。 例如，可从图像生成标题栏、生成标记或识别名人和地标。 此技能使用认知服务中的[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)提供的机器学习模型。 

> [!NOTE]
> 小型卷（20个事务下）可在 Azure 搜索中免费执行，但较大的工作负荷需要[附加可计费认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API，以及在 Azure 搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 描述 |
|--------------------|-------------|
| defaultLanguageCode   |  表示要返回的语言的字符串。 该服务以指定的语言返回识别结果。 如果未指定此属性，则默认值为“en”。 <br/><br/>支持的语言为： <br/>en - 英语（默认） <br/> zh - 简体中文|
|visualFeatures |   表示要返回的可视特征类型的一组字符串。 有效的可视特征类型包括：  <ul><li> *类别*-根据认知服务[计算机视觉文档](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)中定义的分类对图像内容进行分类。 </li><li> tags - 使用与图像内容相关字词的详细列表来标记图像。</li><li>*说明*-使用完整的英语句子描述图像内容。</li><li>人*脸*-检测是否存在面部。 如果存在，则生成位置、性别和年龄。</li><li>    *imageType* -检测图像是否为剪贴画或线条绘图。</li><li>  *颜色*-确定强调颜色、基准颜色以及图像是否为黑色 & 白色。</li><li>*成人*-检测图像在本质上是色情的（描述裸体或性爱法案）。 也检测性暗示内容。</li></ul> 可视特征的名称区分大小写。|
| 详细信息   | 表示要返回的特定于域的详细信息的一组字符串。 有效的可视特征类型包括： <ul><li>*名人*-识别映像中检测到的名人。</li><li>*特征点*-识别映像中检测到的特征点。 </li></ul> |

## <a name="skill-inputs"></a>技能输入

| 输入名称      | 描述                                          |
|---------------|------------------------------------------------------|
| image         | 复杂类型。 当前仅适用于“/document/normalized_images”字段，当 ```imageAction``` 设置为非 ```none``` 值时由 Azure Blob 索引器生成。 请参阅[此示例](#sample-output)获取详细信息。|



##  <a name="sample-skill-definition"></a>示例技能定义

```json
        {
            "description": "Extract image analysis.",
            "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
            "context": "/document/normalized_images/*",
            "defaultLanguageCode": "en",
            "visualFeatures": [
                "Tags",
                "Categories",
                "Description",
                "Faces"
            ],
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "categories"
                },
                {
                    "name": "tags"
                },
                {
                    "name": "description"
                },
                {
                    "name": "faces"
                }
            ]
        }
```
### <a name="sample-index-for-only-the-categories-description-faces-and-tags-fields"></a>示例索引（仅适用于类别、说明、面部和标签字段）
```json
{
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true,
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "blob_uri",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "facetable": false,
            "sortable": true
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": false,
            "searchable": true,
            "filterable": false,
            "facetable": false
        },
        {
            "name": "categories",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "score",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "detail",
                    "type": "Edm.ComplexType",
                    "fields": [
                        {
                            "name": "celebrities",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "faceBoundingBox",
                                    "type": "Collection(Edm.ComplexType)",
                                    "fields": [
                                        {
                                            "name": "x",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        },
                                        {
                                            "name": "y",
                                            "type": "Edm.Int32",
                                            "searchable": false,
                                            "filterable": false,
                                            "facetable": false
                                        }
                                    ]
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        },
                        {
                            "name": "landmarks",
                            "type": "Collection(Edm.ComplexType)",
                            "fields": [
                                {
                                    "name": "name",
                                    "type": "Edm.String",
                                    "searchable": true,
                                    "filterable": false,
                                    "facetable": false
                                },
                                {
                                    "name": "confidence",
                                    "type": "Edm.Double",
                                    "searchable": false,
                                    "filterable": false,
                                    "facetable": false
                                }
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "name": "description",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "tags",
                    "type": "Collection(Edm.String)",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "captions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "confidence",
                            "type": "Edm.Double",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "faces",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "age",
                    "type": "Edm.Int32",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "gender",
                    "type": "Edm.String",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "faceBoundingBox",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "x",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        },
                        {
                            "name": "y",
                            "type": "Edm.Int32",
                            "searchable": false,
                            "filterable": false,
                            "facetable": false
                        }
                    ]
                }
            ]
        },
        {
            "name": "tags",
            "type": "Collection(Edm.ComplexType)",
            "fields": [
                {
                    "name": "name",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "facetable": false
                },
                {
                    "name": "confidence",
                    "type": "Edm.Double",
                    "searchable": false,
                    "filterable": false,
                    "facetable": false
                }
            ]
        }
    ]
}

```
### <a name="sample-output-field-mapping-for-the-above-index"></a>示例输出字段映射（对于以上索引）
```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/*",
            "targetFieldName": "categories"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/tags/*",
            "targetFieldName": "tags"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/description",
            "targetFieldName": "description"
        },
        {
            "sourceFieldName": "/document/normalized_images/*/faces/*",
            "targetFieldName": "faces"
        }
```
### <a name="variation-on-output-field-mappings-nested-properties"></a>输出字段映射（嵌套属性）的变体

您可以将输出字段映射定义为较低级别的属性，例如仅特征点或名人。 在这种情况下，请确保索引架构中有一个字段专门用于包含特征点。

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/normalized_images/*/categories/detail/celebrities/*",
            "targetFieldName": "celebrities"
        }
```
##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "image": {
                    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                    "width": 500,
                    "height": 300,
                    "originalWidth": 5000,
                    "originalHeight": 3000,
                    "rotationFromOriginal": 90,
                    "contentOffset": 500,
                    "pageNumber": 2
                }
            }
        }
    ]
}
```

##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "categories": [
                    {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                        "name": "people_",
                        "score": 0.83984375,
                        "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": [
                                        {
                                            "x": 273,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 309
                                        },
                                        {
                                            "x": 395,
                                            "y": 431
                                        },
                                        {
                                            "x": 273,
                                            "y": 431
                                        }
                                    ],
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                        "faceBoundingBox": [
                            {
                                "x": 1601,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 395
                            },
                            {
                                "x": 1653,
                                "y": 447
                            },
                            {
                                "x": 1601,
                                "y": 447
                            }
                        ]
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
            }
        }
    ]
}
```


## <a name="error-cases"></a>错误案例
在以下错误案例中，未提取任何元素。

| 错误代码 | 描述 |
|------------|-------------|
| NotSupportedLanguage | 不支持提供的语言。 |
| InvalidImageUrl | 图片 URL 格式不正确或无法访问。|
| InvalidImageFormat | 输入数据不是有效的图像。 |
| InvalidImageSize | 输入的图像太大。 |
| NotSupportedVisualFeature  | 指定的特征类型无效。 |
| NotSupportedImage | 不受支持的图片，例如儿童色情内容。 |
| InvalidDetails | 不受支持的特定于域的模型。 |

如果收到类似于 `"One or more skills are invalid. Details: Error in skill #<num>: Outputs are not supported by skill: Landmarks"`的错误，请检查路径。 名人和特征点都是 `detail`的属性。

```json
"categories":[  
      {  
         "name":"building_",
         "score":0.97265625,
         "detail":{  
            "landmarks":[  
               {  
                  "name":"Forbidden City",
                  "confidence":0.92013400793075562
               }
            ]
```

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建索引器 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
