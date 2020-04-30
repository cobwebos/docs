---
title: 表单识别器的新增功能
titleSuffix: Azure Cognitive Services
description: 了解窗体识别器 API 的最新更改。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531076"
---
# <a name="whats-new-in-form-recognizer"></a>表单识别器的新增功能

表单识别器服务将持续更新。 使用本文可了解最新功能的增强功能、修复和文档更新。

> [!NOTE]
> 除非指定，否则窗体识别器的快速入门和指南始终使用最新版本的 API。

## <a name="march-2020"></a>2020 年 3 月 

### <a name="new-features"></a>新增功能

* **用于标记的值类型**你现在可以指定用窗体识别器示例标记工具标记的值的类型。 目前支持以下值类型和变体：
  * `string`
    * 默认、 `no-whitespaces`、`alphanumeric`
  * `number`
    * 缺省值`currency`
  * `date` 
    * 默认、 `dmy`、 `mdy`、`ymd`
  * `time`
  * `integer`

  若要了解如何使用此功能，请参阅[示例标记工具](./quickstarts/label-tool.md#specify-tag-value-types)指南。


* **表可视化**示例标记工具现在显示在文档中识别的表。 这使您可以在标记和分析之前查看已在文档中识别和提取的表。 可以使用 "层" 选项开启/关闭此功能。

  下面是如何识别和提取表的示例：

  > [!div class="mx-imgBorder"]
  > ![使用示例标签工具的表可视化效果](./media/whats-new/formre-table-viz.png)

    提取的表在下`"pageResults"`的 JSON 输出中可用。

  > [!IMPORTANT]
  > 不支持标记表。 如果无法识别表并自动 extrated，则只能将它们标记为键/值对。 如果将表标记为键/值对，则将每个单元标记为唯一值。

### <a name="extraction-enhancements"></a>提取增强功能

此版本包括提取增强功能和准确性改进，特别是在同一文本行中标记和提取多个键/值对的功能。 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>示例标记工具现在为开源

窗体识别器示例标记工具现在作为开源项目提供。 你可以将其集成到你的解决方案中，并根据需要进行客户特定的更改。

有关表单识别器示例标记工具的详细信息，请查看[GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)上提供的文档。

### <a name="tls-12-enforcement"></a>强制执行 TLS 1.2

现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅[Azure 认知服务安全性](../cognitive-services-security.md)。

## <a name="january-2020"></a>2020 年 1 月

此版本引入了窗体识别器2.0 （预览版）。 在下面的部分中，你将找到有关新功能、增强功能和更改的详细信息。 

### <a name="new-features"></a>新增功能

* **自定义模型**
  * **标签定型**你现在可以使用手动标记的数据来训练自定义模型。 这会生成表现更好的模型，并且可以生成处理复杂表单或所含值没有键的表单的模型。
  * **异步 API**可以使用异步 API 调用来训练和分析大型数据集和文件。
  * **TIFF 文件支持**你现在可以训练和提取 TIFF 文档中的数据。
  * **提取准确性改进**

* **预生成的回执模型**
  * **Tip 数量**你现在可以提取 tip 金额和其他手写值。
  * **行项提取**您可以从收据中提取行项值。
  * **置信度值**您可以查看模型对每个提取值的置信度。
  * **提取准确性改进**

* **布局提取**现在可以使用布局 API 从窗体中提取文本数据和表数据。

### <a name="custom-model-api-changes"></a>自定义模型 API 更改

所有用于定型和使用自定义模型的 Api 都已重命名，并且一些同步方法现在是异步的。 下面是重大更改：

* 模型定型过程现在是异步的。 通过 **/Custom/models** API 调用启动培训。 此调用将返回一个操作 ID，你可以将该 ID 传递到**自定义/模型/{modelID}** 以返回定型结果。
* 键/值提取现在由 **/Custom/models/{modelID}/analyze** API 调用启动。 此调用将返回一个操作 ID，你可以将该 ID 传递到**自定义/模型/{modelID}/analyzeResults/{resultID}** 以返回提取结果。
* 训练操作的操作 Id 现在位于 HTTP 响应的**Location**标头中，而不是位于**操作位置**标头。

### <a name="receipt-api-changes"></a>接收 API 更改

用于读取销售收据的 Api 已重命名。

* 接收数据提取现在由 **/Prebuilt/receipt/analyze** API 调用启动。 此调用将返回一个操作 ID，你可以将该 ID 传递到 **/prebuilt/receipt/analyzeResults/{resultID}** 以返回提取结果。

### <a name="output-format-changes"></a>输出格式更改

所有 API 调用的 JSON 响应都具有新格式。 添加、删除或重命名了某些键和值。 请参阅快速入门，了解当前 JSON 格式的示例。

## <a name="next-steps"></a>后续步骤

完成一个[快速入门](quickstarts/curl-train-extract.md)，开始使用[表单识别器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)。