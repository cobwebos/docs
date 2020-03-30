---
title: 文档提取认知技能（预览）
titleSuffix: Azure Cognitive Search
description: 从浓缩管道中的文件中提取内容。 此技能当前处于公共预览版中。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837725"
---
# <a name="document-extraction-cognitive-skill"></a>文档提取认知技能

> [!IMPORTANT] 
> 此技能当前处于公共预览版中。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前不支持门户或 .NET SDK。

**文档提取**技能从浓缩管道中的文件中提取内容。 这允许您利用文档提取步骤，该步骤通常发生在技能集执行之前，这些文件可能由其他技能生成。

> [!NOTE]
> 随着通过增加处理频率、添加更多文档或添加更多 AI 算法来扩大范围，您需要[附加计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 在认知服务中调用 API 时会产生费用，以及作为索引中文档破解阶段的一部分的图像提取。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价在[定价页面上](https://go.microsoft.com/fwlink/?linkid=2042400)进行了描述。
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入            | 允许的值 | 描述 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 设置为`default`从不是纯文本或 json 的文件提取文档。 设置为`text`提高纯文本文件的性能。 设置为`json`从 json 文件中提取结构化内容。 如果未`parsingMode`显式定义，则将其设置为`default`。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 设置为`contentAndMetadata`从每个文件中提取所有元数据和文本内容。 设置为`allMetadata`仅提取[特定于内容类型的元数据](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)（例如，仅 .png 文件所特有的元数据）。 如果未`dataToExtract`显式定义，则将其设置为`contentAndMetadata`。 |
| `configuration` | 请参阅下文。 | 调整文档提取执行方式的可选参数字典。 有关支持的配置属性的说明，请参阅下表。 |

| 配置参数   | 允许的值 | 描述 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 设置为`none`忽略数据集中的嵌入图像或图像文件。 这是默认值。 <br/>对于[使用认知技能的图像分析](cognitive-search-concept-image-scenarios.md)，设置为`generateNormalizedImages`让技能创建一系列规范化的图像，作为文档破解的一部分。 此操作要求`parsingMode`设置为`default`和`dataToExtract`设置为 。 `contentAndMetadata` 规范化的图像是指在视觉搜索结果中包含图像时，对图像进行额外的处理，使图像的输出一致，并通过调整大小和旋转方向使图像在呈现时更一致（例如，使图像控件中的照片大小一致，如 [JFK 演示](https://github.com/Microsoft/AzureSearch_JFK_Files)中所示）。 使用此选项时，将为每个图像生成此信息。  <br/>如果设置为`generateNormalizedImagePerPage`，PDF 文件将以不同的方式处理，因为而不是提取嵌入的图像，每个页面将呈现为图像并相应地规范化。  非 PDF 文件类型的处理方式`generateNormalizedImages`与设置相同。
| `normalizedImageMaxWidth` | 50-10000 之间的任何整数 | 生成的规范化图像的最大宽度（以像素为单位）。 默认为 2000。 | 
| `normalizedImageMaxHeight` | 50-10000 之间的任何整数 | 生成的规范化图像的最大高度（以像素为单位）。 默认为 2000。 |

> [!NOTE]
> 将规范化图像的最大宽度和高度默认设置为 2000 像素是考虑到 [OCR 技术](cognitive-search-skill-ocr.md)所能够支持的最大大小以及[图像分析技术](cognitive-search-skill-image-analysis.md)。 [OCR 技能](cognitive-search-skill-ocr.md)支持非英语语言的最大宽度和高度为 4200，支持英语语言的最大宽度和高度为 10000。  如果增加最大限制，则根据技能组定义和文档语言，对较大的图像进行处理可能会失败。 
## <a name="skill-inputs"></a>技能输入

| 输入名称     | 描述 |
|--------------------|-------------|
| file_data | 应从中提取内容的文件。 |

"file_data"输入必须是定义如下的对象：

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

可以生成此文件引用对象，这三种方法之一：

 - 将`allowSkillsetToReadFileData`索引器定义的参数设置为"true"。  这将创建一个路径`/document/file_data`，该路径是表示从 Blob 数据源下载的原始文件数据的对象。 此参数仅适用于 Blob 存储中的数据。

 - 将`imageAction`索引器定义的参数设置为 以外的`none`值。  这将创建一个图像数组，该数组遵循输入此技能所需的约定（如果单独传递（即`/document/normalized_images/*`）。

 - 具有自定义技能返回与上述完全一样定义的 json 对象。  参数`$type`必须设置为精确，`file``data`并且参数必须是文件内容的基础 64 编码字节数组数据。

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 描述 |
|--------------|-------------|
| content | 文档的文本内容。 |
| normalized_images | 当`imageAction`设置为 其他值时`none`，新*normalized_images*字段将包含图像数组。 有关每个图像的输出格式的更多详细信息，请参阅[图像提取文档](cognitive-search-concept-image-scenarios.md)。 |

##  <a name="sample-definition"></a>示例定义

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>示例输入

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
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
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何处理和提取认知搜索方案中的图像中的信息](cognitive-search-concept-image-scenarios.md)