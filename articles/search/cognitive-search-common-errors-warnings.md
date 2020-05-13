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
ms.openlocfilehash: b5e18fcc5dc23bdbd9027de62a5bee0fb7d4ceff
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125088"
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
| key | 受错误或警告影响的文档的文档 ID。 | https： \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
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

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"/>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>错误：无法将输出字段 " `xyz` " 映射到搜索索引，因为在应用映射函数 "" 时出现反序列化问题 `abc`
输出映射可能已失败，因为输出数据的格式不适合你所使用的映射函数。 例如，对二进制数据应用进行 base64encode 处理映射函数会生成此错误。 若要解决此问题，请在不指定映射函数的情况下重新运行索引器，或确保映射函数与输出字段数据类型兼容。 有关详细信息，请参阅[输出字段映射](cognitive-search-output-field-mapping.md)。

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>错误：无法执行技能
索引器无法在技能组合中运行技能。

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 暂时性连接问题 | 发生暂时性错误。 请稍后重试。 | 偶尔会出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |
| 潜在的产品 bug | 发生了意外错误。 | 这表示未知的失败类别，并可能表示存在产品错误。 请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助。 |
| 技能在执行过程中遇到错误 | （从合并技能）一个或多个偏移值无效，无法对其进行分析。 项已插入到文本的末尾 | 使用错误消息中的信息解决此问题。 这种类型的故障将需要采取措施来解决问题。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>错误：无法执行技能，因为 Web API 请求失败
未能执行技能，因为对 Web API 的调用失败。 通常，当使用自定义技能时，会发生此类故障，在这种情况下，您需要调试自定义代码来解决问题。 相反，如果失败来自内置的技能，请参阅错误消息以获取解决此问题的帮助。

在调试此问题时，请务必注意此技能的任何[技能输入警告](#warning-skill-input-was-invalid)。 你的 Web API 终结点可能会失败，因为索引器正在向其传递意外输入。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>错误：无法执行技能，因为 Web API 技能响应无效
技能执行失败，因为调用 Web API 时返回了无效的响应。 通常，当使用自定义技能时，会发生此类故障，在这种情况下，您需要调试自定义代码来解决问题。 如果出现故障的原因是内置了技能，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助。

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>错误：未在时间限制内执行技能
在以下两种情况下，你可能会遇到此错误消息，每个情况都应以不同的方式处理。 请按照下面的说明操作，具体取决于为你返回此错误的技能。

### <a name="built-in-cognitive-service-skills"></a>内置认知服务技能
许多内置认知技巧，如语言检测、实体识别或 OCR，都由认知服务 API 终结点支持。 有时，这些终结点存在暂时性问题，请求将超时。对于暂时性问题，没有任何补救措施，请等待，然后重试。 作为缓解措施，请考虑将索引器设置为按[计划运行](search-howto-schedule-indexers.md)。 计划索引从中断的位置继续进行。 假设已解决暂时性问题，则在下一次计划运行时，索引和认知技能处理应能继续。

如果对内置认知技能的相同文档继续看到此错误，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获取帮助，因为这不是预期的。

### <a name="custom-skills"></a>自定义技能
如果遇到与已创建的自定义技能有关的超时错误，可以尝试以下几个事项。 首先，请查看您的自定义技能，并确保它不会陷入无限循环，并且它将以一致的方式返回结果。 一旦您确认了这种情况，就会确定您的技能执行时间。 如果未显式设置 `timeout` 自定义技能定义上的值，则默认值 `timeout` 为30秒。 如果要执行的技能不够长30秒，可以 `timeout` 在自定义技能定义上指定较高的值。 下面是一个自定义技能定义的示例，其中超时设置为90秒：

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

可以为参数设置的最大值 `timeout` 为230秒。  如果您的自定义技能在230秒内无法持续执行，则可以考虑减少 `batchSize` 您的自定义技能，使其在单个执行中处理的文档更少。  如果已将设置 `batchSize` 为1，则需要重写可在230秒内执行的技能，或将其拆分为多个自定义技能，以便任何单个自定义技能的执行时间最大为230秒。 有关详细信息，请查看[自定义技能文档](cognitive-search-custom-skill-web-api.md)。

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>错误：无法 " `MergeOrUpload` " |" `Delete` " 文档到搜索索引

文档已读取并处理，但索引器无法将其添加到搜索索引。 这可能是由于以下原因导致的：

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 字段包含的字词太大 | 文档中的术语大于[32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 可以确保字段未配置为可筛选、可查找或可排序，从而避免此限制。
| 文档太大，无法建立索引 | 文档大于[最大 api 请求大小](search-limits-quotas-capacity.md#api-request-limits) | [如何为大型数据集编制索引](search-howto-large-index.md)
| 文档包含集合中的对象太多 | 文档中的集合超出了[跨所有复杂集合的最大元素数限制](search-limits-quotas-capacity.md#index-limits)"带有键的文档 `'1000052'` 具有 `'4303'` 集合中的对象（JSON 数组）。 在 `'3000'` 整个文档中，最多允许对象位于集合中。 请从集合中删除对象，然后重新尝试对文档进行索引。 " | 建议将文档中的复杂集合大小减小到低于限制，并避免高存储利用率。
| 连接到目标索引时出现问题（重试后仍存在），因为该服务处于其他负载下，如查询或索引。 | 未能建立与更新索引的连接。 搜索服务负载过重。 | [向上缩放搜索服务](search-capacity-planning.md)
| 搜索服务正在为服务更新进行修补，或者正在重新配置拓扑。 | 未能建立与更新索引的连接。 搜索服务当前处于关闭状态，搜索服务正在进行转换。 | 为服务配置至少3个副本，每个[SLA 文档](https://azure.microsoft.com/support/legal/sla/search/v1_0/)99.9% 的可用性
| 基础计算/网络资源失败（极少） | 未能建立与更新索引的连接。 发生未知故障。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)以从失败状态中选取。
| 由于网络问题，在超时期限内未确认对目标索引进行的索引请求。 | 未能及时建立与搜索索引的连接。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)以从失败状态中选取。 此外，如果此错误情况持续存在，请尝试降低索引器[批处理大小](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters)。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>错误：无法对文档编制索引，因为某些文档的数据无效

文档由索引器读取和处理，但由于索引字段的配置与索引器提取并处理的数据不匹配，因此无法将其添加到搜索索引。 这可能是由于以下原因导致的：

| 原因 | 详细信息/示例
| --- | ---
| 索引器提取的字段的数据类型与相应目标索引字段的数据模型不兼容。 | 具有键 "888" 的文档中的数据字段 "_data_" 包含无效的值 "Edm. 字符串" "。 预期类型为 "Collection （Edm）"。 |
| 未能从字符串值中提取任何 JSON 实体。 | 无法将字段 "_data_" 的类型 "Edm. 字符串" "的值" 分析为 JSON 对象。 错误：在分析值后，遇到意外的字符： ""。 路径 "_path_"，第1行，位置3162。 |
| 未能从字符串值提取 JSON 实体的集合。  | 无法将字段 "_data_" 的类型 "Edm. 字符串" "的值" 分析为 JSON 数组。 错误：在分析值后，遇到意外的字符： ""。 路径 "[0]"、第1行、位置27。 |
| 在源文档中发现未知类型。 | 无法为未知类型 "_unknown_" 建立索引 |
| 在源文档中使用了不兼容的地理点表示法。 | 不支持 WKT 点字符串。 请改用 GeoJson point 文本 |

在所有这些情况下，请参考索引器[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[数据类型映射](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)，以确保正确生成索引架构，并设置相应的[索引器字段映射](search-indexer-field-mappings.md)。 错误消息将包括有助于跟踪不匹配源的详细信息。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>错误：无法使用集成的更改跟踪策略，因为表具有复合主键

这适用于 SQL 表，通常在该键定义为组合键时，或在表定义唯一聚集索引（如 SQL 索引，而不是 Azure 搜索索引）时发生。 主要原因是在[唯一聚集索引](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)的情况下，将键属性修改为组合主键。 在这种情况下，请确保您的 SQL 表不具有唯一的聚集索引，或者您将键字段映射到一个字段，该字段保证不包含重复值。

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>错误：无法在索引器最大运行时间内处理文档

当索引器无法在允许的执行时间内完成处理来自数据源的单个文档时，将出现此错误。 当使用技能集时，[最大运行时间](search-limits-quotas-capacity.md#indexer-limits)较短。 发生此错误时，如果将 maxFailedItems 设置为0以外的值，则索引器将在以后运行时跳过该文档，以便可以进行索引编制。 如果您无法跳过任何文档，或者如果您一直看到此错误，请考虑将文档拆分为较小的文档，以便在单个索引器执行中执行部分进度。

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>错误：无法投影文档

当索引器尝试将[数据投影到知识存储区](knowledge-store-projection-overview.md)中，并且我们尝试执行此操作时，会出现此错误。  此故障可能是一致且可修复的，或者是可能需要等待并重试才能解决的投影输出接收器导致的暂时性故障。  下面是一组已知的失败状态和可能的解决方法。

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 无法 `'blobUri'` 在容器中更新投影 blob`'containerName'` |指定的容器不存在。 | 索引器将检查是否已创建了指定的容器，并在必要时创建它，但它只会在每个索引器运行时执行此检查一次。 此错误表示在执行此步骤后删除了容器。  若要解决此错误，请尝试以下操作：仅保留存储帐户信息，等待索引器完成，然后重新运行索引器。 |
| 无法 `'blobUri'` 在容器中更新投影 blob`'containerName'` |无法将数据写入传输连接：现有连接被远程主机强行关闭。 | 这应该是 Azure 存储的暂时性故障，因此应通过重新运行索引器来解决此问题。 如果持续遇到此错误，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，以便进一步调查。  |
| 无法更新 `'projectionRow'` 表中的行`'tableName'` | 该服务器正忙。 | 这应该是 Azure 存储的暂时性故障，因此应通过重新运行索引器来解决此问题。 如果持续遇到此错误，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，以便进一步调查。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>警告：技能输入无效
缺少技能的输入、错误的类型或其他无效类型。 警告消息将指示影响：
1) 无法执行技能
2) 已执行的技能，但可能产生意外结果

认知技能需要输入和可选输入。 例如，[关键短语提取技能](cognitive-search-skill-keyphrases.md)有两个必需的输入 `text` ， `languageCode` 和无可选输入。 自定义技能输入都被视为可选输入。

如果缺少任何必需的输入，或者任何输入不是正确的类型，则会跳过该技能，并生成警告。 跳过的技能不会生成任何输出，因此，如果其他技能使用跳过的技能的输出，则他们可能会生成其他警告。

如果缺少可选输入，则该技能仍将运行，但由于缺少输入，可能会产生意外的输出。

在这两种情况下，此警告可能是由于你的数据形状所致。 例如，如果你有一个文档，其中包含有关字段、和的人员的信息， `firstName` `middleName` `lastName` 则你可能有一些没有条目的文档 `middleName` 。 如果要将 `middleName` 作为输入传递给管道中的技能，则预计这项技术输入可能会丢失一些时间。 你将需要评估你的数据和方案，以确定此警告是否需要任何操作。

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

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 技能输入的类型错误 | "所需的技能输入不属于预期类型 `String` 。 Name： `text` ，Source： `/document/merged_content` 。 "  "所需的技能输入不是预期的格式。 Name： `text` ，Source： `/document/merged_content` 。 "  "无法循环访问非数组 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 。"  "无法 `0` 在非数组中选择 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` " | 某些技能需要特定类型的输入，例如，[情绪技能](cognitive-search-skill-sentiment.md)应为 `text` 字符串。 如果输入指定非字符串值，则不会执行技能，也不会生成任何输出。 确保你的数据集在类型中具有一致的输入值，或使用[自定义的 WEB API 技能](cognitive-search-custom-skill-web-api.md)对输入进行预处理。 如果要在数组上循环访问技能，请检查技能上下文并输入 `*` 正确的位置。 通常，上下文和输入源都应以 `*` for 数组结尾。 |
| 缺少技能输入 | "缺少所需的技能输入。 名称： `text` ，源： `/document/merged_content` "" 缺少值 `/document/normalized_images/0/imageTags` 。 "  "无法 `0` 在长度为的数组中选择 `/document/pages` `0` "。 | 如果你的所有文档均收到此警告，则很可能是输入路径中有拼写错误，你应仔细检查路径中的属性名称大小写、多余或缺失 `*` ，并确保数据源中的文档提供所需的输入。 |
| 技能语言代码输入无效 | 技能输入 `languageCode` 具有以下语言代码 `X,Y,Z` ，其中至少有一个是无效的。 | 请参阅[下面](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)的详细信息 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告：技术输入 "languageCode" 具有以下语言代码 X、Y 和 Z，其中至少有一个无效。
传递到下游技能的可选输入的一个或多个值 `languageCode` 不受支持。 如果将[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)的输出传递给后续技能，并且输出包含的语言比这些下游技能支持的语言多，则会发生这种情况。

如果你知道数据集全部使用一种语言，则应删除[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 技能输入，并 `defaultLanguageCode` 改为使用该技能的技能参数，前提是该技术支持该语言。

如果你知道你的数据集包含多个语言，因而你需要[LanguageDetectionSkill](cognitive-search-skill-language-detection.md)和 `languageCode` 输入，请考虑添加[ConditionalSkill](cognitive-search-skill-conditional.md) ，以使用在将文本传递到下游技能之前不受支持的语言来筛选出文本。  下面是有关 EntityRecognitionSkill 的示例：

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
* [文本分析支持的语言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)（适用于[KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)、 [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)、 [SentimentSkill](cognitive-search-skill-sentiment.md)和[PIIDetectionSkill](cognitive-search-skill-pii-detection.md)）
* [翻译工具支持的语言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)（用于[文本 TranslationSkill](cognitive-search-skill-text-translation.md)）
* [文本 SplitSkill](cognitive-search-skill-textsplit.md)支持的语言：`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>警告：技能输入已截断
认知技能限制了一次可以分析的文本长度。 如果这些技能的文本输入超过此限制，我们将截断文本以满足限制，然后对截断后的文本执行扩充。 这意味着将执行该技能，而不是对所有数据执行该技能。

在下面的示例 LanguageDetectionSkill 中， `'text'` 如果输入字段超出字符限制，则可能会触发此警告。 可以在[技能文档](cognitive-search-predefined-skills.md)中找到技能输入限制。

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

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告： Web API 技能响应包含警告
索引器能够在技能组合中运行技能，但 Web API 请求的响应指示在执行过程中出现警告。 查看警告，了解你的数据是如何受影响的，以及是否需要执行操作。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告：当前索引器配置不支持增量进度

仅对 Cosmos DB 数据源发生此警告。

索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。

恢复未完成的索引作业的功能在依据的文档按 `_ts` 列排序。 索引器使用时间戳来确定下一次选取哪个文档。 如果 `_ts` 缺少列，或者如果索引器无法确定它是否按顺序排序了自定义查询，索引器将从开头开始，你会看到此警告。

可以重写此行为，启用增量进度，并使用配置属性禁止显示此警告 `assumeOrderByHighWatermarkColumn` 。

有关详细信息，请参阅[增量进度和自定义查询](search-howto-index-cosmosdb.md#IncrementalProgress)。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告：投影期间某些数据丢失。 表 "Y" 中的行 "X" 的字符串属性 "Z" 太长。

[表存储服务](https://azure.microsoft.com/services/storage/tables)对多个[实体属性](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types)可以有多大的限制。 字符串的长度不能超过32000个字符。 如果正在投影字符串属性长度超过32000个字符的行，则仅保留前32000个字符。 若要解决此问题，请避免将字符串属性的行投影到超过32000个字符。

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告：将提取的文本截断为 X 个字符
索引器限制可以从任何一个文档中提取的文本量。 此限制取决于定价层： "免费" 层的32000个字符、"4000000 基本"、"64000"、"标准"、"标准"、"8000000" 16000000 和 "标准 S3"。 不会为被截断的文本编制索引。 若要避免此警告，请尝试将包含大量文本的文档拆分为多个较小的文档。 

有关详细信息，请参阅[索引器限制](search-limits-quotas-capacity.md#indexer-limits)。

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告：无法将输出字段 "X" 映射到搜索索引
引用不存在/null 数据的输出字段映射将为每个文档生成警告，并产生空的索引字段。 若要解决此问题，请仔细检查输出字段映射源路径是否可能出现错误，或使用[条件技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)设置默认值。 有关详细信息，请参阅[输出字段映射](cognitive-search-output-field-mapping.md)。

| 原因 | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 无法循环访问非数组 | "无法循环访问非数组 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 。" | 当输出不是数组时，会发生此错误。 如果认为输出应为数组，请检查指示的输出源字段路径中是否存在错误。 例如，你可能 `*` 在源字段名称中缺少或额外。 此技能的输入也可能为 null，从而导致空数组。 在[技能输入](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)中查找相似的详细信息节无效。    |
| 无法 `0` 在非数组中选择 | "无法 `0` 在非数组中选择 `/document/pages` "。 | 如果技能输出不产生数组，并且输出源字段名称具有数组索引或其路径，则可能会发生这种情况 `*` 。 请仔细检查输出源字段名称中提供的路径以及所指示的字段名称的字段值。 在[技能输入](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)中查找相似的详细信息节无效。  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告：数据更改检测策略已配置为使用键列 "X"
[数据更改检测策略](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies)对用于检测更改的列有特定要求。 其中一项要求是，每当源项发生更改时，都会更新此列。 另一要求是此列的新值大于先前值。 键列不满足此要求，因为它们不会在每次更新时都更改。 若要解决此问题，请为更改检测策略选择其他列。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告：文档文本看似 UTF-16 编码，但缺少字节顺序标记

在分析文本之前，[索引器分析模式](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters)需要知道文本如何编码。 编码文本最常见的两种方法是 UTF-16 和 UTF-8。 UTF-8 是长度可变的编码，其中每个字符的长度介于1个字节和4个字节之间。 UTF-16 是固定长度的编码，其中每个字符长度为2个字节。 UTF-16 具有两个不同的变量： "big endian" 和 "little endian"。 文本编码由 "字节顺序标记" 确定，这是文本前面的一系列字节。

| 编码 | 字节顺序标记 |
| --- | --- |
| UTF-16 大字节序 | 0xFE 0xFF |
| UTF-16 小字节序 | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

如果不存在字节顺序标记，则假定文本编码为 UTF-8。

若要解决此警告，请确定此 blob 的文本编码，并添加相应的字节顺序标记。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告： Cosmos DB 集合 "X" 具有延迟索引策略。 某些数据可能丢失

不能一致地查询具有[延迟](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)索引策略的集合，导致索引器丢失数据。 若要解决此警告，请将索引策略更改为 "一致"。
