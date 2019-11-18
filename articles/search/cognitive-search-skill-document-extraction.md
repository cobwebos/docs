---
title: 文档提取认知技能（预览版）
titleSuffix: Azure Cognitive Search
description: 从扩充管道内的文件中提取内容。 此技能目前为公共预览版。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: a79b0476fccbd2e2b9d3cf47ecfdc99c17c2862d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113341"
---
# <a name="document-extraction-cognitive-skill"></a>文档提取认知技能

> [!IMPORTANT] 
> 此技能目前为公共预览版。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供预览版功能。 目前没有门户或 .NET SDK 支持。

**文档提取**技能从扩充管道内的文件中提取内容。 这使您可以利用通常在技能组合执行之前发生的文档提取步骤，而这些文件可能会由其他技能生成。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 在认知服务中调用 Api 时，会产生费用，并在索引时将图像提取作为文档解密阶段的一部分。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 [定价页](https://go.microsoft.com/fwlink/?linkid=2042400)上介绍了图像提取定价。
## <a name="odatatype"></a>@odata.type  
Util. DocumentExtractionSkill

## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 输入            | 允许的值 | 说明 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 设置为从非纯文本或 json 文件提取文档 `default`。 设置为 `text` 以提高纯文本文件的性能。 设置为 `json` 从 json 文件中提取结构化内容。 如果未显式定义 `parsingMode`，则将其设置为 `default`。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 设置为 "`contentAndMetadata`" 可从每个文件提取所有元数据和文本内容。 设置为 "`allMetadata`" 以仅提取[特定于内容类型的元数据](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)（例如，仅对 .png 文件唯一的元数据）。 如果未显式定义 `dataToExtract`，则将其设置为 `contentAndMetadata`。 |
| `configuration` | 请参阅下文。 | 用于调整文档提取执行方式的可选参数的字典。 请参阅下表以了解支持的配置属性的说明。 |

| 配置参数   | 允许的值 | 说明 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 设置为 "`none`" 可忽略数据集中的嵌入图像或图像文件。 这是默认值。 <br/>对于[使用认知技能的图像分析](cognitive-search-concept-image-scenarios.md)，设置为 "`generateNormalizedImages`"，以使技能在文档破解过程中创建一组规范化图像。 此操作要求将 `parsingMode` 设置为 `default` 并将 `dataToExtract` 设置为 `contentAndMetadata`。 规范化的图像是指在视觉搜索结果中包含图像时，对图像进行额外的处理，使图像的输出一致，并通过调整大小和旋转方向使图像在呈现时更一致（例如，使图像控件中的照片大小一致，如 [JFK 演示](https://github.com/Microsoft/AzureSearch_JFK_Files)中所示）。 使用此选项时，将为每个映像生成此信息。  <br/>如果将设置为 `generateNormalizedImagePerPage`，PDF 文件将以不同方式处理，而不是提取嵌入图像，每个页面都将呈现为图像并相应地规范化。  非 PDF 文件类型将被视为与 `generateNormalizedImages` 设置相同。
| `normalizedImageMaxWidth` | 介于50-10000 之间的任何整数 | 生成的规范化图像的最大宽度（以像素为单位）。 默认为 2000。 | 
| `normalizedImageMaxHeight` | 介于50-10000 之间的任何整数 | 生成的规范化图像的最大高度（以像素为单位）。 默认为 2000。 |

> [!NOTE]
> 将规范化图像的最大宽度和高度默认设置为 2000 像素是考虑到 [OCR 技术](cognitive-search-skill-ocr.md)所能够支持的最大大小以及[图像分析技术](cognitive-search-skill-image-analysis.md)。 [OCR 技能](cognitive-search-skill-ocr.md)支持非英语语言的最大宽度和高度为 4200，支持英语语言的最大宽度和高度为 10000。  如果增加最大限制，则根据技能组定义和文档语言，对较大的图像进行处理可能会失败。 
## <a name="skill-inputs"></a>技能输入

| 输入名称     | 说明 |
|--------------------|-------------|
| file_data | 应从中提取内容的文件。 |

"File_data" 输入必须是定义的对象，如下所示：

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

可以通过以下三种方法之一生成此文件引用对象：

 - 将索引器定义上的 `allowSkillsetToReadFileData` 参数设置为 "true"。  这将创建一个路径 `/document/file_data` 该路径是一个对象，该对象表示从 blob 数据源下载的原始文件数据。 此参数仅适用于 Blob 存储中的数据。

 - 将索引器定义上的 `imageAction` 参数设置为除 `none`之外的值。  这将创建一个图像数组，该数组遵循在为此技能输入所需约定的情况下（例如 `/document/normalized_images/*`）。

 - 自定义技能返回完全按如上所述定义的 json 对象。  `$type` 参数必须设置为完全 `file` 并且 `data` 参数必须是文件内容的基本64编码字节数组数据。

## <a name="skill-outputs"></a>技能输出

| 输出名称    | 说明 |
|--------------|-------------|
| 内容 | 文档的文本内容。 |
| normalized_images | 将 `imageAction` 设置为其他值，然后 `none`时，新*normalized_images*字段将包含一组图像。 有关每个映像的输出格式的详细信息，请参阅[图像提取的文档](cognitive-search-concept-image-scenarios.md)。 |

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

## <a name="see-also"></a>另请参阅

+ [内置技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何在认知搜索方案中处理和提取图像中的信息](cognitive-search-concept-image-scenarios.md)