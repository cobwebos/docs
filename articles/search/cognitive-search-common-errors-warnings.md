---
title: 索引器错误和警告
titleSuffix: Azure Cognitive Search
description: 本文提供有关在 Azure 认知搜索中进行 AI 扩充期间可能会遇到的常见错误和警告的信息及其解决方法。
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ed10e998ea05b6687190b1f87095f8bc28265905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086601"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>排查 Azure 认知搜索中的常见索引器错误和警告

本文提供有关在 Azure 认知搜索中进行索引编制与 AI 扩充期间可能会遇到的常见错误和警告的信息及其解决方法。

当错误计数超过 [maxFailedItems](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) 时，索引编制操作停止。 

如果你希望索引器忽略这些错误（并跳过“失败的文档”），请考虑按[此处](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)所述更新 `maxFailedItems` 和 `maxFailedItemsPerBatch`。

> [!NOTE]
> 每个失败的文档及其文档键（如果有）将在索引器执行状态中显示为错误。 如果已将索引器设置为容错，则以后可以使用[索引 API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 手动上传文档。

本文中的错误信息可帮助你解决错误，使索引编制能够继续。

出现警告时不会停止索引编制，但它们确实表示出现了可能导致意外结果的状况。 是否采取措施取决于具体的数据和场景。

从 API 版本 `2019-05-06` 开始，将会构建项级索引器错误和警告，使原因和后续措施变得更明朗。 这些信息包含以下属性：

| 属性 | 说明 | 示例 |
| --- | --- | --- |
| key | 受错误或警告影响的文档的文档 ID。 | https：\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | 操作名称，描述发生错误或警告的位置。 此属性由以下结构生成的：[category].[subcategory].[resourceType].[resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | 错误或警告的概要说明。 | 由于 Web API 请求失败，无法执行技能。 |
| 详细信息 | 可能有助于诊断问题的任何其他详细信息，例如，执行自定义技能失败时的 Web API 响应。 | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 source, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ...rest of stack trace... |
| documentationLink | 相关文档的链接，该文档包含用于调试和解决问题的详细信息。 此链接通常指向本页的以下部分之一。 | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>错误：无法读取文档

索引器无法从数据源中读取文档。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 不同文档中的字段类型不一致 | “值的类型与列类型不匹配。 无法将 `'{47.6,-122.1}'` 存储在 authors 列中。  预期的类型为 JArray。”  “从数据类型 nvarchar 转换为 float 时出错。”  “将 nvarchar 值 '12 months' 转换为数据类型 int 时转换失败。”  “将表达式转换为数据类型 int 时发生算术溢出错误。” | 确保不同文档中每个字段的类型相同。 例如，如果第一个文档的 `'startTime'` 字段是日期时间，而在第二个文档中，该字段是字符串，则就会出现此错误。 |
| 数据源的底层服务发生的错误 | （来自 Cosmos DB）`{"Errors":["Request rate is large"]}` | 检查存储实例，确保其正常运行。 可能需要调整缩放/分区。 |
| 暂时性问题 | 在接收来自服务器的结果时发生传输级错误。 （提供程序：TCP 提供程序，错误:0 - 远程主机强行关闭了现有连接 | 偶尔出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>错误：无法从文档中提取内容或元数据
具有 Blob 数据源的索引器无法从文档（例如 PDF 文件）中提取内容或元数据。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| Blob 超过大小限制 | 文档大小为 `'150441598'` 字节，这超过了当前服务层级支持的最大文档提取大小（`'134217728'` 字节）。 | [Blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob 采用了不受支持的内容类型 | 文档采用了不受支持的内容类型 `'image/png'` | [Blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob 已加密 | 无法处理文档 - 它可能已加密或者受密码保护。 | 可以使用 [Blob 设置](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)跳过 Blob。 |
| 暂时性问题 | “处理 Blob 时出错:请求已中止：请求已被取消。” “在处理期间文档超时。” | 偶尔出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>错误：无法分析文档
索引器从数据源中读取了文档，但在将文档内容转换为指定的字段映射架构时出现了问题。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 缺少文档键 | 文档键不能缺失或为空 | 确保所有文档具有有效的文档键 |
| 文档键无效 | 文档键的长度不能超过 1024 个字符 | 根据验证要求修改文档键。 |
| 无法将字段映射应用到某个字段 | 无法将映射函数 `'functionName'` 应用到字段 `'fieldName'`。 数组不能为 null。 参数名称: bytes | 请反复检查索引器中定义的[字段映射](search-indexer-field-mappings.md)，并与失败文档的指定字段的数据进行比较。 可能需要修改字段映射或文档数据。 |
| 无法读取字段值 | 无法读取列 `'fieldName'` 在索引 `'fieldIndex'` 处的值。 在接收来自服务器的结果时发生传输级错误。 （提供程序：TCP 提供程序，错误:0 - 远程主机强行关闭了现有连接。 | 这些错误的常见原因是数据源的底层服务出现了意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>错误：无法执行技能
索引器无法运行技能集中的某个技能。

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 暂时性连接问题 | 发生了暂时性错误。 请稍后重试。 | 偶尔出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |
| 潜在的产品 bug | 发生了意外错误。 | 这表示发生了未知类别的失败，也可能表示产品有 bug。 若要获得帮助，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)。 |
| 技能在执行期间遇到错误 | （来自合并技能）一个或多个偏移量值无效，无法对其进行分析。 项已插入到文本的末尾 | 使用错误消息中的信息来解决问题。 此类失败需要采取措施才能解决。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>错误：由于 Web API 请求失败，无法执行技能
由于对 Web API 的调用失败，未能执行技能。 通常，此类失败是在使用自定义技能时发生的，在这种情况下，需要调试自定义代码才能解决问题。 如果失败来源于某个内置技能，请参考错误消息获得解决问题的帮助。

调试此问题时，请务必注意此[技能的任何技能输入警告](#warning-skill-input-was-invalid)。 Web API 终结点可能失败，因为索引器传递它意外的输入。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>错误：无法执行技能，因为 Web API 技能响应无效
技能执行失败，因为对 Web API 的调用返回了无效的响应。 通常，当使用自定义技能时，会发生此类故障，在这种情况下，您需要调试自定义代码以解决此问题。 相反，如果失败来自内置技能，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助。

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>错误：技能未在时间限制内执行
有两种情况，您可能会遇到此错误消息，每个错误消息都应以不同的方式对待。 请按照以下说明操作，具体取决于您返回此错误的技能。

### <a name="built-in-cognitive-service-skills"></a>内置认知服务技能
许多内置认知技能（如语言检测、实体识别或 OCR）由认知服务 API 终结点支持。 有时这些终结点存在暂时性问题，请求超时。对于暂时性问题，除了等待和重试外，没有其他补救措施。 作为缓解措施，请考虑将索引器设置为[按计划运行](search-howto-schedule-indexers.md)。 计划索引在关闭的位置回升。 假设暂时性问题已解决，索引和认知技能处理应该能够在下一次计划运行中继续。

如果您在同一文档中继续看到此错误，以获得内置认知技能，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助，因为这是预料之中的。

### <a name="custom-skills"></a>自定义技能
如果您使用已创建的自定义技能遇到超时错误，您可以尝试以下几项操作。 首先，查看自定义技能，并确保不会卡在无限循环中，并且它一致返回结果。 确认情况后，确定技能的执行时间。 如果未在自定义技能定义上显式设置`timeout`值，则默认值`timeout`为 30 秒。 如果 30 秒不够长，无法执行技能，则可以在自定义技能定义上`timeout`指定较高的值。 下面是自定义技能定义的示例，其中超时设置为 90 秒：

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

可以为`timeout`参数设置最大值为 230 秒。  如果您的自定义技能无法在 230 秒内持续执行，则可以考虑减少自定义技能`batchSize`，以便在单个执行中处理的文档更少。  如果已设置为`batchSize`1，则需要重写技能，以便能够在 230 秒内执行，或者将其拆分为多个自定义技能，以便任何单个自定义技能的执行时间最多为 230 秒。 有关详细信息，请查看[自定义技能文档](cognitive-search-custom-skill-web-api.md)。

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>错误： 无法`MergeOrUpload`' |'`Delete`文档到搜索索引

文档已读取和处理，但索引器无法将其添加到搜索索引中。 这可能是由于：

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 字段包含太大的术语 | 文档中的术语大于[32 KB 限制](search-limits-quotas-capacity.md#api-request-limits) | 可以通过确保字段未配置为可筛选、可面可或可排序来避免此限制。
| 文档太大，无法编制索引 | 文档大于最大 api[请求大小](search-limits-quotas-capacity.md#api-request-limits) | [如何索引大型数据集](search-howto-large-index.md)
| 文档包含的集合中的对象太多 | 文档中的集合超过了[所有复杂集合的最大元素限制](search-limits-quotas-capacity.md#index-limits)"具有键`'1000052'`的文档包含集合中`'4303'`的对象 （JSON 数组）。 最多`'3000'`允许对象在整个文档中集合中。 请从集合中删除对象，然后尝试再次对文档进行索引。 | 我们建议将文档中复杂集合的大小减小到低于限制，并避免高存储利用率。
| 连接到目标索引（重试后仍然存在）时遇到问题，因为服务处于其他负载下，如查询或索引。 | 无法建立更新索引的连接。 搜索服务负载沉重。 | [扩展搜索服务](search-capacity-planning.md)
| 正在修补搜索服务以进行服务更新，或者正在进行拓扑重新配置。 | 无法建立更新索引的连接。 搜索服务当前正在关闭/搜索服务正在进行过渡。 | 配置至少 3 个副本的服务，使[SLA 文档](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的可用性达到 99.9%
| 基础计算/网络资源失败（罕见） | 无法建立更新索引的连接。 发生未知故障。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)，以便从失败状态中选取。
| 由于网络问题，在超时期间未确认对目标索引的索引请求。 | 无法及时建立与搜索索引的连接。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)，以便从失败状态中选取。 此外，如果此错误条件仍然存在，请尝试降低索引器[批处理大小](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters)。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>错误：无法索引文档，因为文档的某些数据无效

索引器读取和处理文档，但由于索引字段的配置不匹配以及索引器提取和处理的数据不匹配，无法将其添加到搜索索引中。 这可能是由于：

| 原因 | 详细信息/示例
| --- | ---
| 索引器提取的字段的数据类型与相应目标索引字段的数据模型不兼容。 | 数据字段"_数据_"在文档中的键"888"具有无效值"Edm.String"。 预期类型为"集合（Edm.String）"。 |
| 无法从字符串值中提取任何 JSON 实体。 | 无法将字段"_数据_"的值"Edm.String"解析为 JSON 对象。 错误：'在分析值后遇到意外字符："'"。 路径 '_路径_'， 线 1， 位置 3162. |
| 无法从字符串值中提取 JSON 实体的集合。  | 无法将字段"_数据_"的值"Edm.String"解析为 JSON 数组。 错误：'在分析值后遇到意外字符："'"。 路径"{0}"，第 1 行，位置 27。 |
| 在源文档中发现了未知类型。 | 未知类型"_未知_"无法编制索引 |
| 源文档中使用了地理点的不兼容表示法。 | 不支持 WKT POINT 字符串文本。 请使用 GeoJson 点文本代替 |

在所有这些情况下，请参阅索引器[的支持数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[数据类型映射](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)，以确保正确构建索引架构并设置了适当的[索引器字段映射](search-indexer-field-mappings.md)。 错误消息将包括可帮助跟踪不匹配源的详细信息。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>错误：无法使用集成的更改跟踪策略，因为表具有复合主键

这适用于 SQL 表，通常当键定义为复合键时，或者当表定义了唯一的群集索引（如 SQL 索引中，而不是 Azure 搜索索引）时发生。 主要原因是在[唯一的聚类索引](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)的情况下，键属性被修改为复合主键。 在这种情况下，请确保 SQL 表没有唯一的群集索引，或者将键字段映射到保证不具有重复值的字段。

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>错误：无法在索引器最大运行时处理文档

当索引器无法在允许的执行时间内完成从数据源处理单个文档时，将发生此错误。 使用技能集时[，最长运行时间](search-limits-quotas-capacity.md#indexer-limits)较短。 发生此错误时，如果将 maxFailedItem 设置为值小于 0，则索引器将绕过文档，以便将来运行，以便索引可以进行。 如果无法跳过任何文档，或者一致看到此错误，请考虑将文档分解为较小的文档，以便在单个索引器执行中进行部分进度。

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>错误：无法投影文档

当索引器尝试将数据[投影到知识存储中](knowledge-store-projection-overview.md)时，将发生此错误，并且我们尝试这样做时失败。  此故障可能是一致且可修复的，或者可能是投影输出接收器的暂时性故障，您可能需要等待和重试才能解决。  下面是一组已知的故障状态和可能的解决方案。

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 无法更新容器中的投影`'blobUri'`Blob`'containerName'` |指定的容器不存在。 | 索引器将检查指定容器是否以前已创建，并在必要时创建该容器，但它仅执行此检查一次，每个索引器运行。 此错误表示在此步骤后删除了某个容器。  要解决此错误，请尝试以下错误：仅保留存储帐户信息，等待索引器完成，然后重新运行索引器。 |
| 无法更新容器中的投影`'blobUri'`Blob`'containerName'` |无法将数据写入传输连接：远程主机强制关闭了现有连接。 | 这预计将是 Azure 存储的暂时性故障，因此应通过重新运行索引器来解决。 如果您一贯遇到此错误，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，以便进一步调查。  |
| 无法更新表中的`'projectionRow'`行`'tableName'` | 该服务器正忙。 | 这预计将是 Azure 存储的暂时性故障，因此应通过重新运行索引器来解决。 如果您一贯遇到此错误，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，以便进一步调查。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>警告： 技能输入无效
缺少对技能的输入、错误的类型或其他无效。 警告消息将指示影响：
1) 无法执行技能
2) 技能已执行，但可能有意外的结果

认知技能需要输入和可选输入。 例如，[键短语提取技能](cognitive-search-skill-keyphrases.md)有两个必需的`text`输入`languageCode`，但没有可选的输入。 自定义技能输入都被视为可选输入。

如果缺少任何必需的输入，或者任何输入不是正确的类型，则技能将跳过并生成警告。 跳过的技能不会生成任何输出，因此，如果其他技能使用跳过的技能的输出，它们可能会生成其他警告。

如果缺少可选输入，则技能仍将运行，但由于缺少输入，可能会生成意外输出。

在这两种情况下，由于数据的形状，可能需要使用此警告。 例如，如果您有一个文档，其中包含有关具有`firstName`字段 的人的信息`middleName`，`lastName`和 ， 可能有一些文档没有 的`middleName`条目。 如果要将输入作为`middleName`输入传递到管道中的技能，则预计此技能输入可能会错过一些时间。 您需要评估您的数据和方案，以确定是否需要执行此警告。

如果要在缺少输入的情况下提供默认值，可以使用[条件技能](cognitive-search-skill-conditional.md)生成默认值，然后将[条件技能](cognitive-search-skill-conditional.md)的输出用作技能输入。


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

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 技能输入类型错误 | "所需的技能输入不是预期类型`String`。 名称: `text`，源: `/document/merged_content`。”  所需的技能输入不是预期格式。 名称： `text`，`/document/merged_content`来源： .  "不能迭代非数组`/document/normalized_images/0/imageCelebrities/0/detail/celebrities`。  "无法`0`在非阵列`/document/normalized_images/0/imageCelebrities/0/detail/celebrities`中选择" | 某些技能需要特定类型的输入，例如[情绪技能](cognitive-search-skill-sentiment.md)预期`text`是字符串。 如果输入指定非字符串值，则技能不会执行，也不会生成输出。 确保数据集的输入值在类型上是一致的，或者使用[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)预处理输入。 如果要在数组上迭代技能，请检查技能上下文和输入`*`位于正确位置。 通常，对于数组，上下文和输入源都应`*`以数组结尾。 |
| 缺少技能输入 | 缺少所需的技能输入。 名称： `text`，`/document/merged_content`来源：" 缺少`/document/normalized_images/0/imageTags`值 .  无法以长度`0``/document/pages``0`数组进行选择。 | 如果所有文档都收到此警告，则最有可能输入路径中存在拼写错误，您应该仔细检查属性名称大小写，在路径中额外或缺失`*`，并确保数据源中的文档提供所需的输入。 |
| 技能语言代码输入无效 | 技能输入`languageCode`具有以下语言代码`X,Y,Z`，其中至少有一个无效。 | 查看[以下](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)更多详细信息 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告： 技能输入"语言代码"具有以下语言代码"X、Y、Z"，其中至少有一个语言无效。
不支持传递到下游技能的可选`languageCode`输入中的一个或多个值。 如果将[语言检测技能](cognitive-search-skill-language-detection.md)的输出传递给后续技能，并且输出包含的语言比下游技能中支持的语言多，则可能会发生这种情况。

如果您知道数据集全部采用一种语言，则应删除[语言检测技能](cognitive-search-skill-language-detection.md)以及`languageCode`技能输入，并改为使用`defaultLanguageCode`技能参数，假设该技能支持该语言。

如果您知道数据集包含多种语言，因此需要[语言检测技能](cognitive-search-skill-language-detection.md)并`languageCode`输入，请考虑添加[条件技能](cognitive-search-skill-conditional.md)，在将文本传递到下游技能之前，使用不支持的语言筛选出文本。  下面是实体识别技能的外观示例：

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

以下是当前支持的语言的一些参考，这些技能可能会生成此错误消息：
* [文本分析支持的语言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)（用于[关键短语提取技能](cognitive-search-skill-keyphrases.md)、[实体识别技能](cognitive-search-skill-entity-recognition.md)、[情感技能](cognitive-search-skill-sentiment.md)和[PII 检测技能](cognitive-search-skill-pii-detection.md)）
* [翻译支持的语言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)（用于[文本翻译技能](cognitive-search-skill-text-translation.md)）
* [文本拆分技能](cognitive-search-skill-textsplit.md)支持的语言：`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>警告： 技能输入被截断
认知技能对可同时分析的文本长度有限制。 如果这些技能的文本输入超过该限制，我们将截断文本以达到限制，然后对截断的文本执行扩充。 这意味着技能被执行，但不是在所有数据上执行。

在下面的示例语言检测技能中，如果`'text'`输入字段超过字符限制，则可能会触发此警告。 您可以在[技能文档中](cognitive-search-predefined-skills.md)找到技能输入限制。

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

如果要确保所有文本都得到分析，请考虑使用[拆分技能](cognitive-search-skill-textsplit.md)。

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告： Web API 技能响应包含警告
索引器能够在技能集中运行技能，但来自 Web API 请求的响应表明在执行过程中存在警告。 查看警告以了解数据受到的影响以及是否需要执行操作。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告： 当前索引器配置不支持增量进度

此警告仅适用于 Cosmos DB 数据源。

索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。

恢复未完成的索引作业的能力取决于`_ts`由列订购文档。 索引器使用时间戳来确定接下来要选取的文档。 如果缺少`_ts`列，或者索引器无法确定是否按该列排序了自定义查询，则索引器从开头开始，您将看到此警告。

可以重写此行为，启用增量进度，并使用`assumeOrderByHighWatermarkColumn`配置属性禁止此警告。

有关详细信息，请参阅[增量进度和自定义查询](search-howto-index-cosmosdb.md#IncrementalProgress)。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告： 某些数据在投影过程中丢失。 表"Y"中的"X"行具有字符串属性"Z"，该字符串属性太长。

[表存储服务](https://azure.microsoft.com/services/storage/tables)对[实体属性](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types)可以有多大有限制。 字符串可以有 32，000 个字符或更少。 如果投影字符串属性超过 32，000 个字符的行，则仅保留前 32，000 个字符。 要解决此问题，请避免投影字符串属性超过 32，000 个字符的行。

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告： 截断的提取文本到 X 字符
索引器限制可以从任何一个文档中提取的文本量。 此限制取决于定价层：免费套餐为 32，000 个字符，基本套餐为 64，000 个字符，标准版为 400 万字符，标准 S2 为 800 万字符，标准 S3 为 1600 万字符。 截断的文本将不会编制索引。 为了避免此警告，请尝试将大量文本的文档分解为多个较小的文档。 

有关详细信息，请参阅[索引器限制](search-limits-quotas-capacity.md#indexer-limits)。

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告： 无法将输出字段"X"映射到搜索索引
引用不存在/空数据的输出字段映射将为每个文档生成警告，并导致空索引字段。 要解决此问题，请仔细检查输出字段映射源路径中可能存在的拼写错误，或使用[条件技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)设置默认值。

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告： 数据更改检测策略配置为使用键列"X"
[数据更改检测策略](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies)对于用于检测更改的列有特定要求。 这些要求之一是每次更改源项时更新此列。 另一个要求是此列的新值大于上一个值。 键列不符合此要求，因为它们不会在每个更新时更改。 要解决此问题，请为更改检测策略选择其他列。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告： 文档文本似乎已编码为 UTF-16，但缺少字节顺序标记

[索引器解析模式](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters)在分析文本之前需要知道文本的编码方式。 对文本进行编码的两种最常见的方法是 UTF-16 和 UTF-8。 UTF-8 是一种可变长度编码，其中每个字符的长度在 1 字节和 4 字节之间。 UTF-16 是一种固定长度的编码，其中每个字符长 2 个字节。 UTF-16有两种不同的变体，"大末人"和"小末人"。 文本编码由"字节顺序标记"确定，该标记是文本前的一系列字节。

| 编码 | 字节顺序标记 |
| --- | --- |
| UTF-16 大恩迪安 | 0xFE 0xFF |
| UTF-16 小恩迪安 | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

如果不存在字节顺序标记，则假定文本编码为 UTF-8。

要解决此警告，请确定此 Blob 的文本编码是什么，并添加相应的字节顺序标记。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告： Cosmos 数据库集合"X"具有延迟索引策略。 某些数据可能会丢失

无法一致地查询具有[延迟](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)索引策略的集合，从而导致索引器丢失数据。 要解决此警告，请将索引策略更改为"一致"。
