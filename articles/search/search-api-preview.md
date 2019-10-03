---
title: 适用于 Azure 搜索的预览版 REST API 2019-05-06-Preview - Azure 搜索
description: Azure 搜索服务 REST API 版本 2019-05-06-Preview 包括试验功能，例如知识存储和客户托管的加密密钥。
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649981"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure 搜索服务 REST API 版本 2019-05-06-Preview
本文介绍了 `api-version=2019-05-06-Preview` 版的 Azure 搜索服务 REST API，该版提供尚未正式发布的实验性功能。

> [!NOTE]
> 预览功能可用于目的为收集反馈的测试和试验，可能随时更改。 强烈建议不要在生产应用程序中使用预览版 API。


## <a name="new-in-2019-05-06-preview"></a>2019-05-06-Preview 中的新增功能

[**知识存储**](knowledge-store-concept-intro.md)是基于 AI 的扩充管道的新目标。 除了索引，现在还可以将填充的数据结构（在索引编制过程中创建）持久保存在 Azure 存储中。 可以通过技能集中的元素控制数据的物理结构，包括如何塑造数据、是将数据存储在表存储中还是 Blob 存储中，以及是否需要多个视图。

用于服务端静态加密的[**客户托管加密密钥**](search-security-manage-encryption-keys.md)也是一项新的预览版功能。 如果你是密钥的唯一所有者，则除了应用 Microsoft 管理的内置静态加密以外，还可以应用一个额外的加密层。

## <a name="other-preview-features"></a>其他预览功能

公共预览版中仍包含早期预览版中发布的功能。 如果你正在调用使用较早预览版 API 版本的 API，可以继续使用该版本或切换到 `2019-05-06-Preview`，而不会对预期行为作出任何更改。

+ [moreLikeThis 查询参数](search-more-like-this.md)查找与特定文档相关的文档。 早期预览版中已有此功能。 
* [CSV Blob 索引](search-howto-index-csv-blobs.md)每行创建一个文档，而不是每个文本 Blob 创建一个文档。
* [对 Cosmos DB 索引器的 MongoDB API 支持](search-howto-index-cosmosdb.md)为预览版。


## <a name="how-to-call-a-preview-api"></a>如何调用预览版 API

早期预览版仍然可用，但随着时间推移会变得过时。 如果代码调用 `api-version=2016-09-01-Preview` 或 `api-version=2017-11-11-Preview`，那些调用仍然有效。 但是，只有最新预览版会获得改进。 

以下示例语法说明了对预览 API 版本的调用。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 搜索服务在多个版本内可用。 有关详细信息，请参阅 [API 版本](search-api-versions.md)。

## <a name="next-steps"></a>后续步骤

查看 Azure 搜索服务 REST API 参考文档。 如果遇到问题，请通过 [StackOverflow](https://stackoverflow.com/) 向我们寻求帮助，或[联系支持人员](https://azure.microsoft.com/support/community/?product=search)。

> [!div class="nextstepaction"]
> [搜索服务 REST API 参考](https://docs.microsoft.com/rest/api/searchservice/)