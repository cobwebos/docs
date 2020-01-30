---
title: 新功能公告
titleSuffix: Azure Cognitive Search
description: 新功能和增强功能的公告，其中包括将 Azure 搜索的服务重命名为 Azure 认知搜索。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 24114defe5e78bb7adbd006adc4ddbf8250b3188
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844966"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure 认知搜索中的新增功能

了解服务中的新增功能。 将此页加入书签，以便与服务保持最新状态。

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新服务名称

Azure 搜索现已重命名为**azure 认知搜索**，以反映核心操作中的认知技能和 AI 处理的扩展（但可选）使用。 API 版本、NuGet 包、命名空间和终结点保持不变。 新的和现有的搜索解决方案不受服务名称更改的影响。

## <a name="feature-announcements"></a>功能公告

### <a name="february-2020"></a>2020年2月

+ [PII 检测](cognitive-search-skill-pii-detection.md)是在编制索引期间使用的一项认知技能，可从输入文本中提取个人身份信息，并提供以各种方式从文本中屏蔽该文本的选项。

### <a name="january-2020"></a>2020 年 1 月

+ [客户托管的加密密钥](search-security-manage-encryption-keys.md)现已正式发布。 如果使用的是 REST，则可以使用 `api-version=2019-05-06`访问该功能。 对于托管代码，正确的包仍然是[.NET SDK 版本 8.0-预览](search-dotnet-sdk-migration-version-9.md)，即使该功能不是预览版。 

+ 可以通过两种机制获得对搜索服务的私有访问权限：

  + 你可以通过使用管理 REST API `api-version=2019-10-01-Preview` 来创建服务，从而限制对特定 IP 地址的访问。 预览 API 在[CREATEORUPDATE api](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate)中具有新的**IpRule**和**NetworkRuleSet**属性。 此预览功能在所选区域中可用。 有关详细信息，请参阅[如何使用管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)。

  + 当前可通过受限访问预览版提供，你可以预配 Azure 搜索服务，该服务支持来自同一虚拟网络上的客户端的连接的 Azure 专用终结点。 有关详细信息，请参阅[创建安全连接的专用终结点](service-create-private-endpoint.md)。

### <a name="december-2019"></a>2019 年 12 月

+ "[创建应用" （预览版）](search-create-app-portal.md)是门户中的一个新向导，用于生成可下载的 HTML 文件。 该文件附带嵌入的脚本，该脚本可呈现操作 "localhost" 样式的 web 应用，并将其绑定到搜索服务中的索引。 页面在向导中可配置，并且可以包含搜索栏、结果区域、边栏导航和 typeahead 查询支持。 您可以将 HTML 脱机修改为扩展或自定义工作流或外观。

+ [创建用于安全连接的专用终结点（预览）](service-create-private-endpoint.md)说明如何设置专用链接，以便安全连接到搜索服务。 此预览功能按要求提供，并使用[Azure 专用链接](../private-link/private-link-overview.md)和[azure 虚拟网络](../virtual-network/virtual-networks-overview.md)作为解决方案的一部分。

### <a name="november-2019---ignite-conference"></a>2019年11月-Ignite 会议

+ [增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)将缓存和 statefullness 添加到扩充管道，使你可以处理特定的步骤或阶段，而不会丢失已处理的内容。 以前，对扩充管道进行的任何更改都需要完全重新生成。 对于增量扩充，会保留成本高昂分析的输出，尤其是图像分析。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [文档提取（预览版）](cognitive-search-skill-document-extraction.md)是在编制索引期间使用的一种认知技巧，可用于从技能组合中提取文件的内容。 以前，文档破解仅在技能组合执行之前发生。 添加了这一技能后，还可以在执行技能组合的过程中执行此操作。

+ [文本翻译](cognitive-search-skill-text-translation.md)是在编制索引时使用的一项认知技巧，用于计算文本，对于每个记录，将返回转换为指定目标语言的文本。

+ [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)可以在 Power BI 桌面的知识存储中快速学习对已丰富内容的可视化和分析。 此模板专用于通过 "[导入数据" 向导](knowledge-store-create-portal.md)创建的 Azure 表投影。

+ 索引器中现在支持[Azure Data Lake Storage Gen2 （预览版）](search-howto-index-azure-data-lake-storage.md)、 [Cosmos DB Gremlin API （预览）](search-howto-index-cosmosdb.md)和[Cosmos DB Cassandra API （预览版）](search-howto-index-cosmosdb.md) 。 你可以使用[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)进行注册。 一旦您接受了预览计划，您将收到一封确认电子邮件。

### <a name="july-2019"></a>2019 年 7 月

+ 在[Azure 政府版云中](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)公开发布。

## <a name="service-updates"></a>服务更新

可在 Azure 网站上找到 Azure 认知搜索的[服务更新公告](https://azure.microsoft.com/updates/?product=search&status=all)。