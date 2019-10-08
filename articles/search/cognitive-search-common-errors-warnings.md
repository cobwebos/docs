---
title: 常见错误和警告-Azure 搜索
description: 本文提供了有关在 Azure 搜索中进行 AI 扩充时可能遇到的常见错误和警告的信息和解决方案。
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028318"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure 搜索中的 AI 扩充管道的常见错误和警告

本文提供了有关在 Azure 搜索中进行 AI 扩充时可能遇到的常见错误和警告的信息和解决方案。

## <a name="errors"></a>错误
当错误计数超过["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)时，索引将停止。 

如果希望索引器忽略这些错误（并跳过 "失败的文档"），请考虑更新 `maxFailedItems` 并 `maxFailedItemsPerBatch`，如[此处](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述。

> [!NOTE]
> 每个失败的文档及其文档键（如果有）将显示为索引器执行状态中的错误。 如果已将索引器设置为允许失败，则可以利用[索引 api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)在以后手动上载文档。

以下各节可帮助你解决错误，允许继续进行索引。

### <a name="could-not-read-document"></a>无法读取文档
索引器无法从数据源中读取文档。 这可能是由于以下原因导致的：

| Reason | 示例 | 操作 |
| --- | --- | --- |
| 不同文档中的字段类型不一致 | 值的类型与列类型不匹配。 无法将 @no__t 0 存储在作者列中。  预期类型为 JArray。 | 确保每个字段的类型在不同的文档中是相同的。 例如，如果第一个文档 `'startTime'` 字段是日期时间，在第二个文档中为字符串，则会命中此错误。 |
| 来自数据源的基础服务的错误 | （从 Cosmos DB） `{"Errors":["Request rate is large"]}` | 检查存储实例，确保其正常运行。 可能需要调整缩放/分区。 |
| 暂时性问题 | 在接收来自服务器的结果时发生传输级错误。 （提供程序：TCP 提供程序，错误：0-远程主机强行关闭了现有连接 | 偶尔会出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

### <a name="could-not-extract-document-content"></a>无法提取文档内容
具有 Blob 数据源的索引器无法从文档中提取内容（例如，PDF 文件）。 这可能是由于以下原因导致的：

| Reason | 示例 | 操作 |
| --- | --- | --- |
| blob 大于大小限制 | 文档 `'150441598'` 个字节，超过了最大大小 @no__t 为当前服务层提取文档的最大大小为1字节。 | [blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 的内容类型不受支持 | 文档具有不受支持的内容类型 `'image/png'` | [blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 已加密 | 无法处理文档-它可能已加密或受密码保护。 | [blob 设置](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 暂时性问题 | 处理 blob 时出错：请求已中止：请求已取消。 | 偶尔会出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

### <a name="could-not-parse-document"></a>无法分析文档
索引器从数据源中读取文档，但将文档内容转换为指定的字段映射架构时出现问题。 这可能是由于以下原因导致的：

| Reason | 示例 | 操作 |
| --- | --- | --- |
| 缺少文档键 | 文档键不能为空或为空 | 确保所有文档都具有有效的文档键 |
| 文档键无效 | 文档键的长度不能超过1024个字符 | 修改文档键以满足验证要求。 |
| 未能将字段映射应用于字段 | 无法将映射函数 `'functionName'` 应用于字段 `'fieldName'`。 数组不能为 null。 参数名称：字节 | 仔细检查在索引器上定义的[字段映射](search-indexer-field-mappings.md)，并将与已失败文档的指定字段的数据进行比较。 可能需要修改字段映射或文档数据。 |
| 无法读取字段值 | 无法读取索引 `'fieldIndex'` 的列 @no__t 的值。 在接收来自服务器的结果时发生传输级错误。 （提供程序：TCP 提供程序，错误：0-现有连接被远程主机强行关闭。） | 这些错误通常是由于数据源的基础服务的意外连接问题导致的。 稍后再次尝试通过索引器运行文档。 |

### <a name="could-not-index-document"></a>无法为文档编制索引
文档已读取并处理，但索引器无法将其添加到搜索索引。 这可能是由于以下原因导致的：

| Reason | 示例 | 操作 |
| --- | --- | --- |
| 字段包含的字词太大 | 文档中的术语大于[32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 可以确保字段未配置为可筛选、可查找或可排序，从而避免此限制。
| 文档太大，无法建立索引 | 文档大于[最大 api 请求大小](search-limits-quotas-capacity.md#api-request-limits) | [如何为大型数据集编制索引](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>技能输入 "languageCode" 具有以下语言代码 "X"、"Y"、"Z"，其中至少有一个是无效的。
不支持传递给下游技能的可选 `languageCode` 输入的一个或多个值。 如果将[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)的输出传递给后续技能，并且输出包含的语言比这些下游技能支持的语言多，则会发生这种情况。

如果你知道数据集全部使用一种语言，则应删除[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 @no__t 技能输入，并改为使用该技能的 @no__t 技能参数，前提是该技术支持该语言。

如果你知道数据集包含多种语言，因而需要[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 输入，请考虑添加[ConditionalSkill](cognitive-search-skill-conditional.md) ，以筛选出在传入之前不受支持的语言的文本向下游技能文本。  下面是有关 EntityRecognitionSkill 的示例：

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

下面是可能产生此错误消息的每项技能当前支持的语言的参考资料：
* [文本分析支持的语言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)（适用[于 KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、 [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)和[SentimentSkill](cognitive-search-skill-sentiment.md)）
* [翻译工具支持的语言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)（用于[文本 TranslationSkill](cognitive-search-skill-text-translation.md)）
* [文本 SplitSkill](cognitive-search-skill-textsplit.md)支持的语言： `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>未在时间限制内执行技能
在以下两种情况下，你可能会遇到此错误消息，每个情况都应以不同的方式处理。 请按照下面的说明操作，具体取决于为你返回此错误的技能。

#### <a name="built-in-cognitive-service-skills"></a>内置认知服务技能
许多内置认知技巧，如语言检测、实体识别或 OCR，都由认知服务 API 终结点支持。 有时，这些终结点存在暂时性问题，请求将超时。对于暂时性问题，没有任何补救措施，请等待，然后重试。 作为缓解措施，请考虑将索引器设置为按[计划运行](search-howto-schedule-indexers.md)。 计划索引从中断的位置继续进行。 假设已解决暂时性问题，则在下一次计划运行时，索引和认知技能处理应能继续。

#### <a name="custom-skills"></a>自定义技能
如果遇到与已创建的自定义技能有关的超时错误，可以尝试以下几个事项。 首先，请查看您的自定义技能，并确保它不会陷入无限循环，并且它将以一致的方式返回结果。 一旦您确认了这种情况，就会确定您的技能执行时间。 如果未显式设置自定义技能定义上的 @no__t 0 值，则默认 @no__t 为30秒。 如果30秒不够长，无法满足您的技能需要，您可以在您的自定义技能定义上指定一个更高的 @no__t 0 值。 下面是一个自定义技能定义的示例，其中超时设置为90秒：

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

可以为 @no__t 参数设置的最大值为230秒。  如果自定义技能在230秒内无法持续执行，则可以考虑减少自定义技能的 @no__t 0，使其在单个执行中处理的文档更少。  如果已将 @no__t 0 设置为1，则需要重写可在230秒内执行的技能，或将其拆分为多个自定义技能，以便任何单个自定义技能的执行时间最大为230秒。 有关详细信息，请查看[自定义技能文档](cognitive-search-custom-skill-web-api.md)。

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>不能 "`MergeOrUpload`" |"`Delete`" 文档到搜索索引

文档已读取并处理，但索引器无法将其添加到搜索索引。 这可能是由于以下原因导致的：

| Reason | 示例 | 操作 |
| --- | --- | --- |
| 文档中的术语大于[32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 字段包含的字词太大 | 可以确保字段未配置为可筛选、可查找或可排序，从而避免此限制。
| 文档大于[最大 api 请求大小](search-limits-quotas-capacity.md#api-request-limits) | 文档太大，无法建立索引 | [如何为大型数据集编制索引](search-howto-large-index.md)
| 连接到目标索引时出现问题（重试后仍存在），因为该服务处于其他负载下，如查询或索引。 | 未能建立与更新索引的连接。 搜索服务负载过重。 | [向上缩放搜索服务](search-capacity-planning.md)
| 搜索服务正在为服务更新进行修补，或者正在重新配置拓扑。 | 未能建立与更新索引的连接。 搜索服务当前处于关闭状态，搜索服务正在进行转换。 | 为服务配置至少3个副本，每个[SLA 文档](https://azure.microsoft.com/support/legal/sla/search/v1_0/)99.9% 的可用性
| 基础计算/网络资源失败（极少） | 未能建立与更新索引的连接。 出现未知错误。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)以从失败状态中选取。

##  <a name="warnings"></a>列出
警告不会停止索引，但它们会指示可能导致意外结果的条件。 你是否采取措施取决于数据和你的方案。

### <a name="skill-input-was-truncated"></a>技能输入已截断
认知技能限制了一次可以分析的文本长度。 如果这些技能的文本输入超过此限制，我们将截断文本以满足限制，然后对截断后的文本执行扩充。 这意味着将执行该技能，而不是对所有数据执行该技能。

在下面的示例 LanguageDetectionSkill 中，如果 "`'text'`" 输入字段超出字符限制，则可能会触发此警告。 可以在[技能文档](cognitive-search-predefined-skills.md)中找到技能输入限制。

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

如果要确保分析所有文本，请考虑使用[拆分的技能](cognitive-search-skill-textsplit.md)。