---
title: 常见错误和警告
titleSuffix: Azure Cognitive Search
description: 本文提供了有关在 Azure 认知搜索中的 AI 扩充期间可能遇到的常见错误和警告的信息和解决方案。
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 540e72a4472fce626822f0b22bfac11a23aea205
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466778"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-cognitive-search"></a>Azure 认知搜索中的 AI 扩充管道的常见错误和警告

本文提供了有关在 Azure 认知搜索中的 AI 扩充期间可能遇到的常见错误和警告的信息和解决方案。

## <a name="errors"></a>错误
当错误计数超过["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)时，索引将停止。 

如果希望索引器忽略这些错误（并跳过 "失败的文档"），请考虑更新 `maxFailedItems` 和 `maxFailedItemsPerBatch`，如[此处](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述。

> [!NOTE]
> 每个失败的文档及其文档键（如果有）将显示为索引器执行状态中的错误。 如果已将索引器设置为允许失败，则可以利用[索引 api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)在以后手动上载文档。

以下各节可帮助你解决错误，允许继续进行索引。

### <a name="could-not-read-document"></a>无法读取文档
索引器无法从数据源中读取文档。 这可能是由于以下原因导致的：

| 原因 | 示例 | 行动 |
| --- | --- | --- |
| 不同文档中的字段类型不一致 | 值的类型与列类型不匹配。 无法将 `'{47.6,-122.1}'` 存储在作者列中。  预期类型为 JArray。 | 确保每个字段的类型在不同的文档中是相同的。 例如，如果第一个文档 `'startTime'` 字段是日期时间，在第二个文档中为字符串，则会命中此错误。 |
| 来自数据源的基础服务的错误 | （从 Cosmos DB） `{"Errors":["Request rate is large"]}` | 检查存储实例，确保其正常运行。 可能需要调整缩放/分区。 |
| 暂时性问题 | 在接收来自服务器的结果时发生传输级错误。 （提供程序： TCP 提供程序，错误： 0-远程主机强行关闭了现有连接 | 偶尔会出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

### <a name="could-not-extract-document-content"></a>无法提取文档内容
具有 Blob 数据源的索引器无法从文档中提取内容（例如，PDF 文件）。 这可能是由于以下原因导致的：

| 原因 | 示例 | 行动 |
| --- | --- | --- |
| blob 大于大小限制 | 文档是 `'150441598'` 字节，超过了当前服务层的文档提取的最大大小 `'134217728'` 字节。 | [blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 的内容类型不受支持 | 文档具有不受支持的内容类型 `'image/png'` | [blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob 已加密 | 无法处理文档-它可能已加密或受密码保护。 | [blob 设置](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| 暂时性问题 | 处理 blob 时出错：请求已中止：请求已取消。 | 偶尔会出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

### <a name="could-not-parse-document"></a>无法分析文档
索引器从数据源中读取文档，但将文档内容转换为指定的字段映射架构时出现问题。 这可能是由于以下原因导致的：

| 原因 | 示例 | 行动 |
| --- | --- | --- |
| 缺少文档键 | 文档键不能为空或为空 | 确保所有文档都具有有效的文档键 |
| 文档键无效 | 文档键的长度不能超过1024个字符 | 修改文档键以满足验证要求。 |
| 未能将字段映射应用于字段 | 无法将映射函数 `'functionName'` 应用于字段 `'fieldName'`。 数组不能为 null。 参数名称：字节 | 仔细检查在索引器上定义的[字段映射](search-indexer-field-mappings.md)，并将与已失败文档的指定字段的数据进行比较。 可能需要修改字段映射或文档数据。 |
| 无法读取字段值 | 无法在索引 `'fieldIndex'`读取列 `'fieldName'` 的值。 在接收来自服务器的结果时发生传输级错误。 （提供程序： TCP 提供程序，错误： 0-现有连接被远程主机强行关闭。） | 这些错误通常是由于数据源的基础服务的意外连接问题导致的。 稍后再次尝试通过索引器运行文档。 |

### <a name="could-not-execute-skill"></a>无法执行技能
索引器无法在技能组合中运行技能。

| 原因 | 示例 | 行动 |
| --- | --- | --- |
| 暂时性连接问题 | 发生暂时性错误。 请稍后重试。 | 偶尔会出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |
| 潜在的产品 bug | 发生了意外错误。 | 这表示未知的失败类别，并可能表示存在产品错误。 请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助。 |
| 技能在执行过程中遇到错误 | （从合并技能）一个或多个偏移值无效，无法对其进行分析。 项已插入到文本的末尾 | 使用错误消息中的信息解决此问题。 这种类型的故障将需要采取措施来解决问题。 |

### <a name="could-not-execute-skill-because-the-web-api-request-failed"></a>未能执行技能，因为 Web API 请求失败
未能执行技能，因为对 Web API 的调用失败。 通常，当使用自定义技能时，会发生此类故障，在这种情况下，您需要调试自定义代码来解决问题。 相反，如果失败来自内置的技能，请参阅错误消息以获取解决此问题的帮助。

### <a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>无法执行技能，因为 Web API 技能响应无效
技能执行失败，因为调用 Web API 时返回了无效的响应。 通常，当使用自定义技能时，会发生此类故障，在这种情况下，您需要调试自定义代码来解决问题。 如果出现故障的原因是内置了技能，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助。

### <a name="skill-did-not-execute-within-the-time-limit"></a>未在时间限制内执行技能
在以下两种情况下，你可能会遇到此错误消息，每个情况都应以不同的方式处理。 请按照下面的说明操作，具体取决于为你返回此错误的技能。

#### <a name="built-in-cognitive-service-skills"></a>内置认知服务技能
许多内置认知技巧，如语言检测、实体识别或 OCR，都由认知服务 API 终结点支持。 有时，这些终结点存在暂时性问题，请求将超时。对于暂时性问题，没有任何补救措施，请等待，然后重试。 作为缓解措施，请考虑将索引器设置为按[计划运行](search-howto-schedule-indexers.md)。 计划索引从中断的位置继续进行。 假设已解决暂时性问题，则在下一次计划运行时，索引和认知技能处理应能继续。

#### <a name="custom-skills"></a>自定义技能
如果遇到与已创建的自定义技能有关的超时错误，可以尝试以下几个事项。 首先，请查看您的自定义技能，并确保它不会陷入无限循环，并且它将以一致的方式返回结果。 一旦您确认了这种情况，就会确定您的技能执行时间。 如果未在自定义技能定义上显式设置 `timeout` 值，则默认 `timeout` 为30秒。 如果要执行的技能不够长30秒，可以在自定义技能定义上指定较高的 `timeout` 值。 下面是一个自定义技能定义的示例，其中超时设置为90秒：

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

可以为 `timeout` 参数设置的最大值为230秒。  如果自定义技能无法在230秒内持续执行，则可以考虑降低自定义技能的 `batchSize`，使其在单个执行中处理的文档更少。  如果已将 `batchSize` 设置为1，则需要重写可在230秒内执行的技能，或将其拆分为多个自定义技能，以便任何单个自定义技能的执行时间最大为230秒。 有关详细信息，请查看[自定义技能文档](cognitive-search-custom-skill-web-api.md)。

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>不能 "`MergeOrUpload`" |"`Delete`" 文档到搜索索引

文档已读取并处理，但索引器无法将其添加到搜索索引。 这可能是由于以下原因导致的：

| 原因 | 示例 | 行动 |
| --- | --- | --- |
| 文档中的术语大于[32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 字段包含的字词太大 | 可以确保字段未配置为可筛选、可查找或可排序，从而避免此限制。
| 文档大于[最大 api 请求大小](search-limits-quotas-capacity.md#api-request-limits) | 文档太大，无法建立索引 | [如何为大型数据集编制索引](search-howto-large-index.md)
| 连接到目标索引时出现问题（重试后仍存在），因为该服务处于其他负载下，如查询或索引。 | 未能建立与更新索引的连接。 搜索服务负载过重。 | [向上缩放搜索服务](search-capacity-planning.md)
| 搜索服务正在为服务更新进行修补，或者正在重新配置拓扑。 | 未能建立与更新索引的连接。 搜索服务当前处于关闭状态，搜索服务正在进行转换。 | 为服务配置至少3个副本，每个[SLA 文档](https://azure.microsoft.com/support/legal/sla/search/v1_0/)99.9% 的可用性
| 基础计算/网络资源失败（极少） | 未能建立与更新索引的连接。 出现未知错误。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)以从失败状态中选取。
| 由于网络问题，在超时期限内未确认对目标索引进行的索引请求。 | 未能及时建立与搜索索引的连接。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)以从失败状态中选取。 此外，如果此错误情况持续存在，请尝试降低索引器[批处理大小](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters)。

### <a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>由于索引的索引器数据无效，无法为文档编制索引

文档已读取和处理，但由于索引字段的配置与索引器提取的数据的性质不匹配，因此无法将其添加到搜索索引。 这可能是由于以下原因导致的：

| 原因 | 示例
| --- | ---
| 索引器提取的字段的数据类型与相应目标索引字段的数据模型不兼容。 | 带有键 "_data_" 的文档中的数据字段 "_data_" 具有无效值 "" 的类型 "Edm. 字符串"。 预期类型为 "Collection （Edm）"。 |
| 未能从字符串值中提取任何 JSON 实体。 | 无法将字段 "_data_" 的类型 "Edm. 字符串" "的值" 分析为 JSON 对象。 错误：在分析值后，遇到意外的字符： ""。 路径 "_path_"，第1行，位置3162。 |
| 未能从字符串值提取 JSON 实体的集合。  | 无法将字段 "_data_" 的类型 "Edm. 字符串" "的值" 分析为 JSON 数组。 错误：在分析值后，遇到意外的字符： ""。 路径 "[0]"、第1行、位置27。 |
| 在源文档中发现未知类型。 | 无法为未知类型 "_unknown_" 建立索引 |
| 在源文档中使用了不兼容的地理点表示法。 | 不支持 WKT 点字符串。 请改用 GeoJson point 文本 |

在所有这些情况下，请参考索引器[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[数据类型映射](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)，以确保正确生成索引架构，并设置相应的[索引器字段映射](search-indexer-field-mappings.md)。 错误消息将包括有助于跟踪不匹配源的详细信息。

### <a name="could-not-process-document-within-indexer-max-run-time"></a>无法在索引器最大运行时间内处理文档

当索引器无法在允许的执行时间内完成处理来自数据源的单个文档时，将出现此错误。 当使用技能集时，[最大运行时间](search-limits-quotas-capacity.md#indexer-limits)较短。 发生此错误时，如果将 maxFailedItems 设置为0以外的值，则索引器将在以后运行时跳过该文档，以便可以进行索引编制。 如果您无法跳过任何文档，或者如果您一直看到此错误，请考虑将文档拆分为较小的文档，以便在单个索引器执行中执行部分进度。

##  <a name="warnings"></a>列出
警告不会停止索引，但它们会指示可能导致意外结果的条件。 你是否采取措施取决于数据和你的方案。

### <a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>无法执行技能，因为技能输入无效
由于缺少技能的输入、错误的类型或无效，索引器无法在技能组合中运行技能。

认知技能需要输入和可选输入。 例如，[关键短语提取技能](cognitive-search-skill-keyphrases.md)`text`、`languageCode`和无可选输入提供两个必需的输入。 如果任何必需的输入无效，则会跳过该技能，并生成警告。 跳过的技能不会生成任何输出，因此，如果其他技能使用跳过的技能的输出，则他们可能会生成其他警告。

如果要在缺少输入的情况下提供默认值，则可以使用[条件技能](cognitive-search-skill-conditional.md)生成默认值，然后使用[条件技能](cognitive-search-skill-conditional.md)的输出作为技能输入。


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 原因 | 示例 | 行动 |
| --- | --- | --- |
| 技能输入的类型错误 | 所需的技能输入 `X` 不是所需的类型 `String`。 所需的技能输入 `X` 未采用预期格式。 | 某些技能需要特定类型的输入，例如，[情绪技能](cognitive-search-skill-sentiment.md)要求 `text` 是一个字符串。 如果输入指定非字符串值，则不会执行技能，也不会生成任何输出。 确保你的数据集在类型中具有一致的输入值，或使用[自定义的 WEB API 技能](cognitive-search-custom-skill-web-api.md)对输入进行预处理。 如果要在数组上循环访问技能，请检查技能上下文和输入在正确位置 `*`。 通常，上下文和输入源都应以数组的 `*` 结束。 |
| 缺少技能输入 | 缺少必需的技能输入 `X`。 | 如果你的所有文档均收到此警告，则很可能是输入路径中有一个拼写错误，你应仔细检查属性名称大小写、路径中的多余或缺失 `*`，以及来自数据源的文档定义所需的输入。 |
| 技能语言代码输入无效 | 技能输入 `languageCode` 具有以下语言代码 `X,Y,Z`，其中至少有一个是无效的。 | 请参阅[下面](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)的详细信息 |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>技能输入 "languageCode" 具有以下语言代码 "X"、"Y"、"Z"，其中至少有一个是无效的。
不支持传递给下游技能的可选 `languageCode` 输入的一个或多个值。 如果将[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)的输出传递给后续技能，并且输出包含的语言比这些下游技能支持的语言多，则会发生这种情况。

如果你知道数据集全部使用一种语言，则应删除[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 技能输入，并改为使用该技能 `defaultLanguageCode` 技能参数，前提是该技术支持该语言。

如果你知道你的数据集包含多种语言，因而你需要[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 输入，请考虑添加[ConditionalSkill](cognitive-search-skill-conditional.md)以筛选出不受支持的语言的文本，然后传入向下游技能文本。  下面是有关 EntityRecognitionSkill 的示例：

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

### <a name="web-api-skill-response-contains-warnings"></a>Web API 技能响应包含警告
索引器能够在技能组合中运行技能，但 Web API 请求的响应指示在执行过程中出现警告。 查看警告，了解你的数据是如何受影响的，以及是否需要执行操作。

### <a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>当前索引器配置不支持增量进度
仅对 Cosmos DB 数据源发生此警告。

索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。

恢复未完成的索引作业的功能是依据的文档是否按 `_ts` 列排序。 索引器使用时间戳来确定下一次选取哪个文档。 如果缺少 `_ts` 列，或者如果索引器无法确定它是否按顺序排序了自定义查询，索引器将从开头开始，你会看到此警告。

可以重写此行为，启用增量进度，并使用 `assumeOrderByHighWatermarkColumn` 配置属性禁止显示此警告。

[有关 Cosmos DB 增量进度和自定义查询的详细信息。](https://go.microsoft.com/fwlink/?linkid=2099593)

### <a name="could-not-map-output-field-x-to-search-index"></a>无法将输出字段 "X" 映射到搜索索引
引用不存在/null 数据的输出字段映射将为每个文档生成警告，并产生空的索引字段。 若要解决此问题，请仔细检查输出字段映射源路径是否存在拼写错误，或使用[条件技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)设置默认值。

索引器能够在技能组合中运行技能，但 Web API 请求的响应指示在执行过程中出现警告。 查看警告，了解你的数据是如何受影响的，以及是否需要执行操作。
