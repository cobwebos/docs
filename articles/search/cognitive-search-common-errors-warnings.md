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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329378"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Azure 搜索中的 AI 扩充管道的常见错误和警告

本文提供了有关在 Azure 搜索中进行 AI 扩充时可能遇到的常见错误和警告的信息和解决方案。

## <a name="errors"></a>错误
当错误计数超过["maxfaileditems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)时，索引将停止。 以下各节可帮助你解决错误，允许继续进行索引。

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

##  <a name="warnings"></a>警告
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