---
title: 表单识别器的新增功能
titleSuffix: Azure Cognitive Services
description: 了解表单识别器 API 的最新更改。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531076"
---
# <a name="whats-new-in-form-recognizer"></a>表单识别器的新增功能

表单识别器服务会持续更新。 使用本文可随时了解功能增强功能、修补程序和文档更新的最新动态。

> [!NOTE]
> 除非指定，否则表单识别器的快速启动和指南始终使用最新版本的 API。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新增功能

* **用于标签的值类型**现在，您可以使用窗体识别器示例标签工具指定要标记的值的类型。 当前支持以下值类型和变体：
  * `string`
    * 默认值`no-whitespaces`，`alphanumeric`
  * `number`
    * 默认`currency`
  * `date` 
    * 默认值`dmy`， `mdy`，`ymd`
  * `time`
  * `integer`

  请参阅[示例标签工具](./quickstarts/label-tool.md#specify-tag-value-types)指南，了解如何使用此功能。


* **表可视化**示例标签工具现在显示文档中识别的表。 这样，您就可以在标记和分析之前查看从文档中识别和提取的表。 可以使用图层选项打开和关闭此功能。

  这是如何识别和提取表的示例：

  > [!div class="mx-imgBorder"]
  > ![使用示例标记工具进行表可视化](./media/whats-new/formre-table-viz.png)

    提取的表在 下的`"pageResults"`JSON 输出中可用。

  > [!IMPORTANT]
  > 不支持标记表。 如果表未自动识别和外推，则只能将它们标记为键/值对。 将表标记为键/值对时，将每个单元格标记为唯一值。

### <a name="extraction-enhancements"></a>提取增强功能

此版本包括提取增强和精度改进，特别是在同一行文本中标记和提取多个键/值对的功能。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>示例标签工具现在是开源的

表单识别器示例标签工具现在可作为开源项目提供。 您可以将其集成到解决方案中，并针对客户进行更改，以满足您的需求。

有关表单识别器示例标签工具的详细信息，请查看[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)上提供的文档。

### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2

TLS 1.2 现在对对此服务的所有 HTTP 请求强制执行。 有关详细信息，请参阅[Azure 认知服务安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本引入了表单识别器 2.0（预览）。 在以下各节中，您将找到有关新功能、增强功能和更改的详细信息。 

### <a name="new-features"></a>新增功能

* **自定义模型**
  * **带标签的列车**现在，您可以使用手动标记的数据训练自定义模型。 这会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。
  * **异步 API**您可以使用异步 API 调用来训练和分析大型数据集和文件。
  * **TIFF 文件支持**您现在可以使用 TIFF 文档进行训练和提取数据。
  * **提取精度提高**

* **预生成的回执模型**
  * **小费金额**您现在可以提取小费金额和其他手写值。
  * **行项目提取**可以从收货中提取行项目值。
  * **置信度值**您可以查看模型对每个提取值的置信度。
  * **提取精度提高**

* **布局提取**现在可以使用布局 API 从窗体中提取文本数据和表数据。

### <a name="custom-model-api-changes"></a>自定义模型 API 更改

所有用于培训和使用自定义模型的 API 都已重命名，并且某些同步方法现在是异步的。 以下是主要更改：

* 训练模型的过程现在是异步的。 您可以通过 **/自定义/模型**API 调用启动培训。 此调用返回操作 ID，您可以将其传递到**自定义/模型/{模型 ID}** 以返回培训结果。
* 键/值提取现在由 **/自定义/模型/[模型 ID]/分析**API 调用启动。 此调用返回操作 ID，您可以将其传递到**自定义/模型/模型 ID}/分析结果/[结果 ID]** 以返回提取结果。
* 现在，在 HTTP 响应**的位置**标头中可以找到"训练"操作 ID，而不是 **"操作位置**"标头。

### <a name="receipt-api-changes"></a>收货 API 更改

用于读取销售收据的 API 已重命名。

* 接收数据提取现在由 **/预构建/接收/分析**API 调用启动。 此调用返回操作 ID，您可以将该操作 ID 传递到 **/预构建/接收/分析结果/{结果 ID}** 以返回提取结果。

### <a name="output-format-changes"></a>输出格式更改

所有 API 调用的 JSON 响应都有新的格式。 已添加、删除或重命名某些键和值。 有关当前 JSON 格式的示例，请参阅快速入门。

## <a name="next-steps"></a>后续步骤

完成一个[快速入门](quickstarts/curl-train-extract.md)，开始使用[表单识别器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)。