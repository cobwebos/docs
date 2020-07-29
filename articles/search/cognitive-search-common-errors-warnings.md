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
ms.openlocfilehash: 83c3797cc3d9232f8589527285cc56c5cbff9a8a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221320"
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

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>错误：无法读取文档

索引器无法从数据源中读取文档。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 不同文档中的字段类型不一致 | “值的类型与列类型不匹配。 无法将 `'{47.6,-122.1}'` 存储在 authors 列中。  预期的类型为 JArray。”  “从数据类型 nvarchar 转换为 float 时出错。”  “将 nvarchar 值 '12 months' 转换为数据类型 int 时转换失败。”  “将表达式转换为数据类型 int 时发生算术溢出错误。” | 确保不同文档中每个字段的类型相同。 例如，如果第一个文档的 `'startTime'` 字段是日期时间，而在第二个文档中，该字段是字符串，则就会出现此错误。 |
| 数据源的底层服务发生的错误 | （来自 Cosmos DB）`{"Errors":["Request rate is large"]}` | 检查存储实例，确保其正常运行。 可能需要调整缩放/分区。 |
| 暂时性问题 | 在接收来自服务器的结果时发生传输级错误。 （提供程序：TCP 提供程序，错误:0 - 远程主机强行关闭了现有连接 | 偶尔出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>错误：无法从文档中提取内容或元数据
具有 Blob 数据源的索引器无法从文档（例如 PDF 文件）中提取内容或元数据。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| Blob 超过大小限制 | 文档大小为 `'150441598'` 字节，这超过了当前服务层级支持的最大文档提取大小（`'134217728'` 字节）。 | [Blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob 采用了不受支持的内容类型 | 文档采用了不受支持的内容类型 `'image/png'` | [Blob 索引错误](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| Blob 已加密 | 无法处理文档 - 它可能已加密或者受密码保护。 | 可以使用 [Blob 设置](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)跳过 Blob。 |
| 暂时性问题 | “处理 Blob 时出错:请求已中止：请求已被取消。” “在处理期间文档超时。” | 偶尔出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>错误：无法分析文档
索引器从数据源中读取了文档，但在将文档内容转换为指定的字段映射架构时出现了问题。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 缺少文档键 | 文档键不能缺失或为空 | 确保所有文档都有有效的文档键。 文档键是通过将 "key" 属性设置为[索引定义](https://docs.microsoft.com/rest/api/searchservice/create-index#request-body)的一部分来确定的。 在特定文档上找不到标记为 "key" 的属性时，索引器将发出此错误。 |
| 文档键无效 | 文档键的长度不能超过 1024 个字符 | 根据验证要求修改文档键。 |
| 无法将字段映射应用到某个字段 | 无法将映射函数 `'functionName'` 应用到字段 `'fieldName'`。 数组不能为 null。 参数名称: bytes | 请反复检查索引器中定义的[字段映射](search-indexer-field-mappings.md)，并与失败文档的指定字段的数据进行比较。 可能需要修改字段映射或文档数据。 |
| 无法读取字段值 | 无法读取列 `'fieldName'` 在索引 `'fieldIndex'` 处的值。 在接收来自服务器的结果时发生传输级错误。 （提供程序：TCP 提供程序，错误:0 - 远程主机强行关闭了现有连接。 | 这些错误的常见原因是数据源的底层服务出现了意外的连接问题。 稍后再次尝试通过索引器运行文档。 |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>错误：应用映射函数“`abc`”时，由于反序列化问题，无法将输出字段“`xyz`”映射到搜索索引
输出映射可能已失败，因为输出数据的格式与你使用的映射函数不兼容。 例如，对二进制数据应用 Base64Encode 映射函数就会生成此错误。 若要解决此问题，请重新运行索引器而不指定映射函数，或者确保映射函数与输出字段的数据类型兼容。 有关详细信息，请参阅[输出字段映射](cognitive-search-output-field-mapping.md)。

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>错误：无法执行技能
索引器无法运行技能集中的某个技能。

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 暂时性连接问题 | 发生了暂时性错误。 请稍后重试。 | 偶尔出现意外的连接问题。 稍后再次尝试通过索引器运行文档。 |
| 潜在的产品 bug | 发生了意外错误。 | 这表示发生了未知类别的失败，也可能表示产品有 bug。 若要获得帮助，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)。 |
| 技能在执行期间遇到错误 | （来自合并技能）一个或多个偏移量值无效，无法对其进行分析。 项已插入到文本的末尾 | 使用错误消息中的信息来解决问题。 此类失败需要采取措施才能解决。 |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>错误：由于 Web API 请求失败，无法执行技能
由于对 Web API 的调用失败，未能执行技能。 通常，此类失败是在使用自定义技能时发生的，在这种情况下，需要调试自定义代码才能解决问题。 如果失败来源于某个内置技能，请参考错误消息获得解决问题的帮助。

在调试此问题时，请务必注意此技能的所有[技能输入警告](#warning-skill-input-was-invalid)。 你的 Web API 终结点可能会失败，因为索引器正在向它传递意外的输入。

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>错误：由于 Web API 技能响应无效，无法执行技能
由于对 Web API 的调用返回了无效的响应，技能执行失败。 通常，此类失败是在使用自定义技能时发生的，在这种情况下，需要调试自定义代码才能解决问题。 如果失败来源于内置技能，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助。

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>错误：在时间限制内未执行技能
在两种情况下可能会出现此错误消息，每种情况应以不同的方式进行处理。 请根据返回此错误的技能，按照以下说明予以解决。

### <a name="built-in-cognitive-service-skills"></a>内置认知服务技能
许多内置认知技能（例如语言检测、实体识别或 OCR）由认知服务 API 终结点提供支持。 有时，这些终结点会出现暂时性问题，因而请求超时。对于暂时性的问题，除了等待再重试以外，没有其他补救措施。 作为缓解措施，请考虑将索引器设置为[按计划运行](search-howto-schedule-indexers.md)。 计划的索引编制将从中断的位置继续。 假设解决了暂时性问题，在下一次计划的运行时，索引编制和认知技能处理应可继续进行。

如果内置认知技能的同一文档仍然出现此错误，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)以获得帮助，因为此错误不是预期的。

### <a name="custom-skills"></a>自定义技能
如果创建的自定义技能出现超时错误，可以尝试多种解决方法。 首先，检查该自定义技能，确保它未陷入无限循环，并且可持续返回结果。 确认这是问题所在后，确定技能的执行时间。 如果未在自定义技能定义中显式设置 `timeout` 值，则默认的 `timeout` 为 30 秒。 如果 30 秒不足以完成技能的执行，可以在自定义技能定义中指定一个更高的 `timeout` 值。 下面是将超时设置为 90 秒的自定义技能定义示例：

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

可为 `timeout` 参数设置的最大值为 230 秒。  如果自定义技能无法在 230 秒内以一致的方式执行，你可以考虑减小自定义技能的 `batchSize`，以减少它在单次执行中处理的文档数。  如果已将 `batchSize` 设置为 1，则需要重写技能，使其能够在 230 秒内完成执行；或者将其拆分为多个自定义技能，使任何一个自定义技能的最长执行时间为 230 秒。 有关详细信息，请查看[自定义技能文档](cognitive-search-custom-skill-web-api.md)。

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>错误：无法在搜索索引中“`MergeOrUpload`”|“`Delete`”文档

已读取并处理文档，但索引器无法将其添加到搜索索引。 此错误的可能原因包括：

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 某个字段包含太大的字词 | 文档中的字词超过了 [32 KB 的限制](search-limits-quotas-capacity.md#api-request-limits) | 确保字段未配置为可筛选、可分面或可排序，即可规避此限制。
| 文档太大，无法为其编制索引 | 文档超过了[最大 API 请求大小](search-limits-quotas-capacity.md#api-request-limits) | [如何为大型数据集编制索引](search-howto-large-index.md)
| 文档的集合中包含太多的对象 | 文档中的某个集合超过了[所有复杂集合的最大元素数目限制](search-limits-quotas-capacity.md#index-limits)。“键为 `'1000052'` 的文档的集合(JSON 数组)包含 `'4303'` 个对象。 整个文档中的集合最多允许 `'3000'` 个对象。 请从集合中删除对象，然后重试为该文档编制索引。” | 我们建议将文档中复杂集合的大小减至限制以下，并避免较高的存储利用率。
| 由于服务正在承受其他负载（例如查询或索引编制），连接到目标索引时出现问题（重试后仍会出现）。 | 未能建立连接，因此无法更新索引。 搜索服务的负载过重。 | [扩展搜索服务](search-capacity-planning.md)
| 搜索服务正在根据服务更新进行修补，或者处于拓扑重新配置过程的中途。 | 未能建立连接，因此无法更新索引。 搜索服务当前已关闭/搜索服务正在过渡。 | 根据 [SLA 文档](https://azure.microsoft.com/support/legal/sla/search/v1_0/)，为服务至少配置 3 个可用性为 99.9% 的副本
| 底层计算/网络资源发生故障（罕见情况） | 未能建立连接，因此无法更新索引。 发生未知的失败。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)，以从失败状态继续工作。
| 由于出现网络问题，在超时期限内未确认对目标索引发出的索引编制请求。 | 无法及时与搜索索引建立连接。 | 将索引器配置为[按计划运行](search-howto-schedule-indexers.md)，以从失败状态继续工作。 此外，如果此错误持续出现，请尝试减小索引器的[批大小](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters)。

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>错误：由于文档的某些数据无效，无法为文档编制索引

文档已由索引器读取并处理，但由于索引字段的配置与索引器提取并处理的数据不匹配，无法将文档添加到搜索索引。 此错误的可能原因包括：

| Reason | 详细信息/示例
| --- | ---
| 索引器提取的字段的数据类型与相应目标索引字段的数据模型不兼容。 | 键为“888”的文档中的数据字段“_data_”包含“Edm.String”类型的无效值。 预期类型为“Collection(Edm.String)”。 |
| 未能从字符串值中提取任何 JSON 实体。 | 无法将字段“_data_”的“Edm.String”类型值分析为 JSON 对象。 错误:“分析某个值后遇到意外的字符: ''。 路径‘_path_’，行 1，位置 3162。” |
| 未能从字符串值中提取 JSON 实体的集合。  | 无法将字段“_data_”的“Edm.String”类型值分析为 JSON 数组。 错误:“分析某个值后遇到意外的字符: ''。 路径‘[0]’，行 1，位置 27。” |
| 在源文档中发现了未知类型。 | 无法为未知类型“_unknown_”编制索引 |
| 源文档中对地理位置点使用了不兼容的表示法。 | 不支持 WKT 点字符串文本。 请改用 GeoJson 点文本 |

对于所有这些情况，请参阅[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)和[索引器的数据类型映射](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search)，确保正确生成索引架构，并设置适当的[索引器字段映射](search-indexer-field-mappings.md)。 错误消息中的详细信息可帮助跟踪不匹配问题的起因。

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>错误：由于表采用了组合主键，无法使用集成的更改跟踪策略

这适用于 SQL 表，此错误通常发生在将键定义为组合键，或者在表定义了唯一聚集索引时（在 SQL 索引而不是 Azure 搜索索引中）。 主要原因是在使用[唯一聚集索引](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)的情况下将键属性修改为组合主键。 在这种情况下，请确保 SQL 表不采用唯一聚集索引，或者，请将键字段映射到某个保证不包含重复值的字段。

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>错误：在索引器最长运行时间内无法处理文档

当索引器无法在允许的执行时间内处理完数据源中的单个文档时，将发生此错误。 使用技能集时，[最长运行时间](search-limits-quotas-capacity.md#indexer-limits)更短。 发生此错误时，如果 maxFailedItems 设置为非 0 值，则索引器将在以后的运行中绕过该文档，使索引编制能够继续。 如果无法跳过任何文档，或者此错误一直出现，请考虑将文档分解为较小的文档，以便在索引器的单次执行中处理能够取得部分进展。

<名称 = "不能作为文档></a>

## <a name="error-could-not-project-document"></a>错误：无法投影文档

如果索引器在尝试[将项目数据投影到知识存储](knowledge-store-projection-overview.md)时发生失败，则会发生此错误。  这种失败可能会持续出现但可修复；或者，可能是投影输出接收器的暂时性失败，在这种情况下，需要等待并重试才能解决这种失败。  下面是一系列已知失败状态及其可能的解决方法。

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 无法在容器 `'containerName'` 中更新投影 Blob `'blobUri'` |指定的容器不存在。 | 索引器将检查是否已事先创建指定的容器，并会根据创建该容器，但在每个索引器运行周期，它只会执行此项检查一次。 此错误表示在执行此步骤后某个操作删除了容器。  若要解决此错误，请尝试以下方法：忽略存储帐户信息，等待索引器完成，然后重新运行索引器。 |
| 无法在容器 `'containerName'` 中更新投影 Blob `'blobUri'` |无法将数据写入传输连接:远程主机强行关闭了现有连接。 | 这是预期的 Azure 存储暂时性失败，因此应该通过重新运行索引器来予以解决。 如果此错误持续出现，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，让我们进一步调查。  |
| 无法更新表 `'tableName'` 中的行 `'projectionRow'` | 服务器繁忙。 | 这是预期的 Azure 存储暂时性失败，因此应该通过重新运行索引器来予以解决。 如果此错误持续出现，请提交[支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)，让我们进一步调查。  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>警告：技能输入无效
技能的某项输入缺少、类型错误或无效。 警告消息会指出影响：
1) 无法执行技能
2) 技能已执行，但可能出现了意外的结果

认知技能包含必需输入和可选输入。 例如，[关键短语提取技能](cognitive-search-skill-keyphrases.md)包含两个必需输入 `text` 和 `languageCode`，不包含可选输入。 自定义技能输入均被视为可选输入。

如果缺少任何必需输入，或者任何输入的类型不正确，则会跳过该技能并生成警告。 跳过的技能不会生成任何输出，因此，如果其他技能使用已跳过的技能的输出，则它们可能会生成其他警告。

如果缺少可选输入，则技能仍会运行，但由于缺少输入，可能会生成意外的输出。

在这两种情况下，由于数据的形状，此警告都可能是预期的。 例如，如果某个文档的 `firstName`、`middleName` 和 `lastName` 字段中包含有关人员的信息，则某些文档可能没有 `middleName` 对应的条目。 如果在管道中将 `middleName` 作为输入传递给技能，则预期此项技术输入有时会缺失。 需要评估自己的数据和场景，以确定在出现此警告后是否需要采取任何措施。

若要在缺少输入的情况下提供默认值，可以使用[条件技能](cognitive-search-skill-conditional.md)生成默认值，然后使用[条件技能](cognitive-search-skill-conditional.md)的输出作为技能输入。


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

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 技能输入的类型错误 | “必需的技能输入未采用预期类型 `String`。 名称: `text`，源: `/document/merged_content`。”  “必需的技能输入未采用预期格式。 名称: `text`，源: `/document/merged_content`。”  “无法迭代非数组 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`。”  “无法选择非数组 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` 中的 `0`” | 某些技能需要特定类型的输入，例如，[情绪技能](cognitive-search-skill-sentiment.md)要求 `text` 为字符串。 如果输入指定了非字符串值，则技能不会执行，且不会生成输出。 请确保数据集中的输入值在类型上一致，或者，请使用[自定义 Web API 技能](cognitive-search-custom-skill-web-api.md)来预处理输入。 如果在技能中迭代某个数组，请检查技能上下文和输入是否在正确的位置包含 `*`。 通常，上下文和输入源应以 `*` 作为数组的结尾。 |
| 缺少技能输入 | “缺少必需的技能输入。 名称: `text`，源: `/document/merged_content`”。“缺少值 `/document/normalized_images/0/imageTags`。”  “无法在长度为 `0` 的数组 `/document/pages` 中选择 `0`。” | 如果所有文档都出现此警告，则很有可能是输入路径中存在拼写错误。请反复检查属性名称大小写、路径中多余或缺少的 `*`，并确保数据源中的文档提供必需的输入。 |
| 技能语言代码输入无效 | 技能输入 `languageCode` 具有以下语言代码 `X,Y,Z`，其中至少有一个语言代码无效。 | 参阅[下面](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid)的更多详细信息 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>警告：技能输入“languageCode”具有以下语言代码“X,Y,Z”，其中至少有一个语言代码无效。
传入到下游技能的可选 `languageCode` 输入的一个或多个值不受支持。 如果将 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 的输出传递给后续技能，而该输出包含的语言数目超过了这些下游技能所能支持的数目，则会出现此警告。

如果你知道数据集全都采用一种语言，则应删除 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 和 `languageCode` 技能输入，并对该技能改用 `defaultLanguageCode` 技能参数，前提是该技能支持该语言。

如果你知道数据集包含多个语言，因而需要 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 和 `languageCode` 输入，请考虑添加一个 [ConditionalSkill](cognitive-search-skill-conditional.md)，以筛选出采用不受支持语言的文本，然后将文本传入到下游技能。  下面是 EntityRecognitionSkill 的用法示例：

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

下面是可能生成此错误消息的每项技能当前支持的语言的一些参考资源：
* [文本分析支持的语言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)（[KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)，[EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)、[SentimentSkill](cognitive-search-skill-sentiment.md) 和 [PIIDetectionSkill](cognitive-search-skill-pii-detection.md)）
* [翻译服务支持的语言](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)（[文本 TranslationSkill](cognitive-search-skill-text-translation.md)）
* [文本 SplitSkill](cognitive-search-skill-textsplit.md) 支持的语言：`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>警告：技能输入已截断
认知技能对每次可以分析的文本长度施加了限制。 如果这些技能的文本输入超过该限制，我们会根据限制截断文本，然后对截断后的文本执行扩充。 这意味着，技能将会执行，但不会针对所有数据执行。

在下面的示例 LanguageDetectionSkill 中，如果 `'text'` 输入字段超过字符数限制，则可能会触发此警告。 可以在[技能文档](cognitive-search-predefined-skills.md)中找到技能输入限制。

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

若要确保分析所有文本，请考虑使用[拆分技能](cognitive-search-skill-textsplit.md)。

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>警告：Web API 技能响应包含警告
索引器能够运行技能集中的技能，但 Web API 请求的响应指出执行期间出现了警告。 查看警告，了解数据受到了何种影响，以及是否需要采取措施。

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>警告：当前索引器配置不支持增量进度

只有 Cosmos DB 数据源才会出现此警告。

索引编制过程中的增量操作可确保由于暂时性故障或执行时间限制而中断索引器执行时，索引器能够在下次运行时从中断位置运行，而不是从头开始重新为整个集合编制索引。 在为大型集合编制索引时，这一点尤其重要。

可以使用按 `_ts` 列排序的文档，来预测未完成的索引编制作业的恢复能力。 索引器使用时间戳来确定下一次要选取哪个文档。 如果缺少 `_ts` 列，或者索引器无法确定某个自定义查询是否按该列排序，则索引器将从开头开始，同时会出现此警告。

可以使用 `assumeOrderByHighWatermarkColumn` 配置属性重写此行为，以启用增量进度并消除此警告。

有关详细信息，请参阅[增量进度和自定义查询](search-howto-index-cosmosdb.md#IncrementalProgress)。

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>警告：某些数据在投影期间已丢失。 表“Y”中行“X”的字符串属性“Z”太长。

[表存储服务](https://azure.microsoft.com/services/storage/tables)对[实体属性](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types)的大小施加了限制。 字符串最多只能包含 32,000 个字符。 如果所要投影的行中的字符串属性超过 32,000 个字符，只会保留前 32,000 个字符。 若要解决此问题，请避免投影其字符串属性超过 32,000 个字符的行。

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>警告：提取的文本已截断为 X 个字符
索引器会限制可从任一文档中提取的文本量。 此限制取决于定价层：免费层为 32,000 个字符，基本层为 64,000 个字符，标准层为 400 万个字符、标准 S2 层为 800 万个字符，标准 S3 层为 1600 万个字符。 不会为已截断的文本编制索引。 若要避免此警告，请尝试将包含大量文本的文档分解为多个较小的文档。 

有关详细信息，请参阅[索引器限制](search-limits-quotas-capacity.md#indexer-limits)。

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>警告：无法将输出字段“X”映射到搜索索引
引用不存在的数据/null 数据的输出字段映射会针对每个文档生成警告，并生成空索引字段。 若要解决此问题，请反复检查输出字段映射源路径是否存在拼写错误，或使用[条件技能](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)设置默认值。 有关详细信息，请参阅[输出字段映射](cognitive-search-output-field-mapping.md)。

| Reason | 详细信息/示例 | 解决方法 |
| --- | --- | --- |
| 无法循环访问非数组 | “无法迭代非数组 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`。” | 当输出不是数组时，将发生此错误。 如果你认为输出应该是数组，请检查指示的输出源字段路径是否有误。 例如，源字段名称中可能缺少或有多余的 `*`。 也有可能是因为此技能的输入为 null，从而导致数组为空。 请在[技能输入无效](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)部分中查找类似的详细信息。    |
| 无法选择非数组中的 `0` | “无法选择非数组 `/document/pages` 中的 `0`。” | 如果技能输出未生成数组，但输出源字段名称的路径中有数组索引或 `*`，则可能会发生这种情况。 请仔细检查输出源字段名称中提供的路径以及指示的字段名称的字段值。 请在[技能输入无效](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)部分中查找类似的详细信息。  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>警告：数据更改检测策略配置为使用键列“X”
[数据更改检测策略](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies)对它们用于检测更改的列提出了特定的要求。 其中的一项要求是，每当源项发生更改时，都要更新此列。 另一要求是，此列的新值大于以前的值。 键列不满足此要求，因为每次更新时它们不会更改。 若要解决此问题，请为更改检测策略选择另一个列。

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>警告：文档文本看似经过 UTF-16 编码，但缺少字节顺序标记

[索引器分析模式](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters)在分析文本之前需要知道文本的编码方式。 两种最常见的文本编码方式是 UTF-16 和 UTF-8。 UTF-8 是可变长度的编码，其中每个字符的长度为 1 字节到 4 字节。 UTF-16 是固定长度的编码，其中每个字符的长度为 2 字节。 UTF-16 具有两个不同的变体：“big endian”和“little endian”。 文本编码由“字节顺序标记”（文本前面的一系列字节）确定。

| 编码 | 字节顺序标记 |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 Little Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

如果不存在字节顺序标记，则假设文本以 UTF-8 编码。

若要解决此警告，请确定此 Blob 的文本编码，并添加相应的字节顺序标记。

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>警告：Cosmos DB 集合“X”采用延迟索引策略。 某些数据可能已丢失

无法以一致的方式查询采用[延迟](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)索引策略的集合，从而导致索引器缺少数据。 若要解决此警告，请将索引策略更改为“一致”。
