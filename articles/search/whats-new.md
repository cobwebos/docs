---
title: Azure 认知搜索中的新增功能
description: 新增功能和增强功能的通告，包括 Azure 搜索服务已重命名为 Azure 认知搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 60efc645b1b5824306c1d4aa3e763eb9d2bc745d
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553915"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 认知搜索中的新增功能

了解服务中的新增功能。 请将本页加入书签，以随时了解该服务的最新信息。

## <a name="feature-announcements-in-2020"></a>2020 版功能公告

### <a name="august-2020"></a>2020 年 8 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [双重加密](search-security-overview.md#encryption) | 安全性 | 通过对新的搜索服务配置客户管理的密钥 (CMK) 加密，在存储层启用双重加密。 创建新服务，[配置客户管理的密钥并应用于](search-security-manage-encryption-keys.md)索引或同义词映射，可受益于对该内容进行的双重加密。 | 2020 年 8 月 1 日后在以下区域创建的所有搜索服务都公开可用：美国西部 2、美国东部、美国中南部、US Gov 弗吉尼亚州、US Gov 亚利桑那州。 使用门户、管理 REST API 或 SDK 创建服务。 |

### <a name="july-2020"></a>2020 年 7 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [Azure.Search.Documents 客户端库](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) | 用于 .NET 的 Azure SDK | .NET 客户端库，由 Azure SDK 团队发布，旨在保持与其他 .NET 客户端库的一致性。 <br/><br/>版本 11 面向搜索搜索 REST api-version=2020-06-30，但尚不支持知识存储、异地筛选器或 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet)。 | 正式发布。 </br> 从 NuGet 安装 [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)。 |
| [azure.search.documents 客户端库](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme?view=azure-python)  | 用于 Python 的 Azure SDK| Python 客户端库，由 Azure SDK 团队发布，旨在保持与其他 Python 客户端库的一致性。 <br/><br/>版本 11 面向搜索 REST api-version=2020-06-30。 | 正式发布。 </br> 从 PyPI 安装 [azure-search-documents 包](https://pypi.org/project/azure-search-documents/)。 |
| [@azure/search-documents 客户端库](https://docs.microsoft.com/javascript/api/overview/azure/search-documents-readme?view=azure-node-latest)  | 用于 JavaScript 的 Azure SDK | JavaScript 客户端库，由 Azure SDK 团队发布，旨在保持与其他 JavaScript 客户端库的一致性。 <br/><br/>版本 11 面向搜索 REST api-version=2020-06-30。 | 正式发布。 </br> 从 npm 安装 [@azure/search-documents 包](https://www.npmjs.com/package/@azure/search-documents)。 |

### <a name="june-2020"></a>2020 年 6 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
[知识存储](knowledge-store-concept-intro.md) | AI 扩充 | AI 扩充索引器的输出，将内容存储在 Azure 存储中以供其他应用和进程使用。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更高版本，或门户。 |
| [搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) | REST | REST API 的新稳定版本。 除了知识存储，此版本还包括搜索相关性和评分的增强功能。 | 正式发布。 |
| [Okapi BM25 相关性算法](https://en.wikipedia.org/wiki/Okapi_BM25) | 查询 | 新的相关性排名算法自动用于 7 月 15 日之后创建的所有新搜索服务。 对于先前创建的服务，可以通过在索引字段上设置 `similarity` 属性来选择加入。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更高版本，或 REST API 2019-05-06。 |
| **executionEnvironment** | 安全性（索引器） | 显式地将此索引器配置属性设置为 `private`，以强制通过专用终结点连接到外部数据源。 仅适用于使用 Azure 专用链接的搜索服务。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 设置此常规配置参数。 |

### <a name="may-2020-microsoft-build"></a>2020 年 5 月（Microsoft Build 大会）

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [调试会话](cognitive-search-debug-session.md) | AI 扩充 | 调试会话提供基于门户的接口来调查和解决现有技能组的问题。 在调试会话中创建的修补程序可以保存到生产技能集。 请先查看[此教程](cognitive-search-tutorial-debug-sessions.md)。 | 门户中的公共预览版。 |
| [**适用于入站防火墙支持的 IP 规则**](service-configure-firewall.md) | 安全性 | 将对搜索服务终结点的访问限制为特定的 IP 地址。 | 正式发布。 </br> 使用[管理 REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) 或更高版本，或门户。 |
| [专用搜索终结点的 Azure 专用链接](service-create-private-endpoint.md) | 安全性| 通过将搜索服务作为专用链接资源（仅可供同一虚拟网络上的客户端应用和其他 Azure 服务访问）运行，使其不受公共 Internet 的影响。 | 正式发布。 </br> 使用[管理 REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) 或更高版本，或门户。 |
| [系统托管的标识（预览版）](search-howto-managed-identities-data-sources.md) | 安全性（索引器） | 在 Azure Active Directory 中将搜索服务注册为受信任的服务，以设置到受支持的 Azure 数据源的连接，以编制索引。 适用于从 Azure 数据源（例如 Azure SQL 数据库、Azure Cosmos DB 和 Azure 存储）引入内容的[索引器](search-indexer-overview.md)。 | 公共预览版。 </br> 使用门户注册搜索服务。 |
| [**sessionId 查询参数**](index-similarity-and-scoring.md)，[scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics) | 查询（相关性） | 将 sessionID 添加到查询中，以建立一个用于计算搜索分数的会话，scoringStatistics=global 用于从所有分区收集分数，以实现更一致的搜索分数计算。 | 正式发布。 </br> 使用[搜索 REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) 或更高版本，或 REST API 2019-05-06。 |
| [featuresMode（预览版）](index-similarity-and-scoring.md#featuresMode-param) | 查询 | 添加此查询参数，展开相关性分数以显示详细信息：每个字段相似度得分、每个字段术语频率和每个字段匹配的唯一标记数。 你可以在自定义评分算法中使用这些数据点。 有关演示此功能的示例，请参阅[添加机器学习 (LearnToRank) 以搜索相关性](https://github.com/Azure-Samples/search-ranking-tutorial)。 | 公共预览版。 </br> 使用[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |

### <a name="march-2020"></a>2020 年 3 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [本机 blob 软删除（预览版）](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | 索引器 | Azure 认知搜索中的 Azure blob 存储索引器会识别处于软删除状态的 blob，并在编制索引过程中删除相应的搜索文档。 | 公共预览版。 </br> 使用[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 和 REST API 2019-05-06-Preview，并对启用了本机“软删除”的 Azure Blob 数据源运行索引器。 |
| [管理 REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | 用于创建和管理搜索服务的新的稳定 REST API。 添加 IP 防火墙和专用链接支持 | 正式发布。 |

### <a name="february-2020"></a>2020 年 2 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [PII 检测（预览版）](cognitive-search-skill-pii-detection.md) | AI 扩充 | 在编制索引期间使用的一项新的认知技能，它可以从输入文本中提取个人身份信息，并可让你通过多种方式在该文本中屏蔽此类信息。 | 公共预览版。 </br> 使用门户或[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |
| [自定义实体查找（预览版）](cognitive-search-skill-custom-entity-lookup.md )| AI 扩充 | 一项新的认知技能，可在用户自定义的单词和短语列表中查找文本。 它使用此列表为包含任何匹配实体的所有文档加上标签。 该技能还支持一定程度的模糊匹配，应用此匹配方法可以查找类似但不完全相同的匹配项。 | 公共预览版。 </br> 使用门户或[搜索 REST API 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) 或 REST API 2019-05-06-Preview。 |

### <a name="january-2020"></a>2020 年 1 月

|功能&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 类别 | 说明 | 可用性  |
|---------|------------------|-------------|---------------|
| [客户管理的加密密钥](search-security-manage-encryption-keys.md) |安全性 | 除了平台的内置加密外，还添加了额外的加密层。 使用你创建和管理的加密密钥，可以在有效负载到达搜索服务之前对索引内容和同义词映射进行加密。 | 正式发布。 </br> 使用 Search REST API 2019-05-06 或更高版本。 对于托管代码，即使该功能现在不再以预览版提供，但正确的包仍是 [.NET SDK 版本 8.0-preview](search-dotnet-sdk-migration-version-9.md)。 |
| [适用于入站防火墙支持的 IP 规则（预览版）](service-configure-firewall.md) | 安全性 | 将对搜索服务终结点的访问限制为特定的 IP 地址。 预览版 API 在 [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) 中提供 **IpRule** 和 **NetworkRuleSet** 属性。 此预览版功能可在选定的区域中使用。 |  使用 api-version=2019-10-01-Preview 的公共预览版。  |
| [专用搜索终结点的 Azure 专用链接（预览版）](service-create-private-endpoint.md) | 安全性| 通过将搜索服务作为专用链接资源（仅可供同一虚拟网络上的客户端应用和其他 Azure 服务访问）运行，使其不受公共 Internet 的影响。 | 使用 api-version=2019-10-01-Preview 的公共预览版。  |

## <a name="feature-announcements-in-2019"></a>2019 版的功能公告

### <a name="december-2019"></a>2019 年 12 月

+ [创建演示应用（预览版）](search-create-app-portal.md)是门户中用于生成可下载 HTML 文件的新向导，并可对索引进行查询（只读）访问。 文件附带了嵌入式脚本，用于呈现可正常运行的“localhost”式 Web 应用，该应用绑定到搜索服务中的索引。 页面在向导中可配置，可以包含搜索栏、结果区域、边栏导航和自动提示查询支持。 可以脱机修改 HTML 以扩展或者自定义工作流或外观。 演示应用并不易于扩展到包含生产方案中通常需要的安全层和托管层。 你应将其视为验证和测试工具，而不是通往完整客户端应用的捷径。

+ [创建专用终结点以建立安全连接（预览版）](service-create-private-endpoint.md)中介绍了如何设置专用链接，以便与搜索服务建立安全连接。 此预览版功能按请求提供，使用 [Azure 专用链接](../private-link/private-link-overview.md)和 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)作为解决方案的一部分。

### <a name="november-2019---ignite-conference"></a>2019 年 11 月- Ignite 大会

+ [增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)将缓存和有状态性添加到了扩充管道，使你能够处理特定的步骤或阶段，且不丢失已处理的内容。 以前，对扩充管道进行任何更改都需要重新生成整个管道。 使用增量扩充时，会保留高开销分析（尤其是图像分析）的输出。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [文档提取（预览版）](cognitive-search-skill-document-extraction.md)是编制索引期间使用的一项认知技能，用于从技能集中提取文件内容。 以前，文档破解只能在技能集执行之前发生。 使用此项新增技能，还可以在技能集的执行过程中执行此操作。

+ [文本翻译](cognitive-search-skill-text-translation.md)是编制索引期间使用的一项认知技能，它可以评估文本，并为每个记录返回翻译成指定目标语言的文本。

+ [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)可以在 Power BI Desktop 中立即开始可视化和分析知识存储中的扩充内容。 此模板适用于通过[导入数据向导](knowledge-store-create-portal.md)创建的 Azure 表投影。

+ 索引器现在支持 [Azure Data Lake Storage Gen2（预览版）](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API（预览版）](search-howto-index-cosmosdb.md)和 [Cosmos DB Cassandra API（预览版）](search-howto-index-cosmosdb.md)。 可以使用[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)进行注册。 在我们同意你参与预览计划后，你会收到确认电子邮件。

### <a name="july-2019"></a>2019 年 7 月

+ 在 [Azure 政府云](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)中推出了正式版。

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新服务名称

Azure 搜索现已重命名为 **Azure 认知搜索**，以反映认知技能和 AI 处理在核心操作中的更广泛用途（但仍为可选）。 API 版本、NuGet 包、命名空间和终结点未有变化。 新的和现有的搜索解决方案不受服务名称更改的影响。

## <a name="service-updates"></a>服务更新

在 Azure 网站上可以找到 Azure 认知搜索的[服务更新通告](https://azure.microsoft.com/updates/?product=search&status=all)。