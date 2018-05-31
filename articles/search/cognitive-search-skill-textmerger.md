---
title: 文本合并认知搜索技能（Azure 搜索）| Microsoft Docs
description: 将字段集合中的文本合并到合并字段中。 在 Azure 搜索扩充管道中使用此认知技能。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f349158873acca9d50d4d6e5fdfa3539f26207fe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362561"
---
#    <a name="text-merge-cognitive-skill"></a>文本合并认知技能

文本合并技能会将字段集合中的文本合并到单个字段中。 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称     | 说明 |
|--------------------|-------------|
| insertPreTag  | 每次插入之前要包含的字符串。 默认值为 `" "`。 要忽略空格，请将值设置为 `""`。  |
| insertPostTag | 每次插入后要包含的字符串。 默认值为 `" "`。 要忽略空格，请将值设置为 `""`。  |


##  <a name="sample-input"></a>示例输入
为此技能提供可用输入的 JSON 文档有：

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>示例输出
此示例显示之前输入的输出，假设将 insertPreTag 设置为 `" "`并将 insertPostTag 设置为 `""`。 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="extended-sample-skillset-definition"></a>扩展的示例技能集定义

使用文本合并的一个常见场景是将图像的文本表示形式（OCR 技能中的文本或图像的描述文字）合并到文档的内容字段中。 

以下示例技能使用 OCR 技能从文档中嵌入的图像中提取文本。 接下来，它会创建 merged_text 字段以包含每个图像的原始和 OCRed 文本。 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
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
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
以上示例假设存在规范化的图像字段。 要获取规范化的图像字段，请将索引器定义中的 imageAction 配置设置为 generateNormalizedImages，如下所示：

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技术集](cognitive-search-defining-skillset.md)
+ [创建索引器 (REST)](ref-create-indexer.md)