---
title: 创建、管理和保护管理员和查询 api 密钥
titleSuffix: Azure Cognitive Search
description: Api 密钥控制对服务终结点的访问。 管理员密钥授予写入权限。 可为只读访问权限创建查询密钥。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794369"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>创建和管理 Azure 认知搜索服务的 api 密钥

对搜索服务的所有请求都需要专为服务生成的只读 API 密钥。 API 密钥是用于验证搜索服务终结点的访问的唯一机制，必须包含在每个请求中。 在 [REST 解决方案](search-get-started-postman.md)中，API 密钥通常在请求标头中指定。 在 [.NET 解决方案](search-howto-dotnet-sdk.md#core-scenarios)中，密钥通常以配置设置的形式指定，然后在 [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) 上作为[凭据](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials)（管理密钥）或 [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials)（查询密钥）传递。

在服务预配期间，将使用搜索服务创建密钥。 可以在 [Azure 门户](https://portal.azure.com)中查看和获取密钥值。

![门户页上的“设置”>“密钥”部分](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>什么是 API 密钥？

API 密钥是随机生成的数字和字母所组成的字符串。 通过[基于角色的权限](search-security-rbac.md)，可以删除或读取密钥，但无法将密钥替换为用户定义的密码或使用 Active Directory 作为访问搜索操作的主要身份验证方法。 

使用了两种类型的密钥来访问搜索服务：管理员（只写）和查询（只读）。

|密钥|描述|Limits|  
|---------|-----------------|------------|  
|管理员|授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。<br /><br /> 创建服务时，会在门户中生成两个管理密钥（称为主密钥和辅助密钥），且可按需单独重新生成。 由于有两个密钥，可以在滚动其中一个密钥时，使用另一个密钥来持续访问服务。<br /><br /> 只能在 HTTP 请求标头中指定管理密钥。 不能在 URL 中放置管理 API 密钥。|每个服务最多有 2 个密钥|  
|Query|授予对索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。<br /><br /> 按需创建查询密钥。 可以在门户中手动创建查询密钥，或者通过[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 以编程方式创建查询密钥。<br /><br /> 可以在 HTTP 请求标头中为搜索、建议或查找操作指定查询密钥。 或者，可以在 URL 中以参数形式传递查询密钥。 根据客户端应用程序编写请求的方式，以查询参数的形式传递密钥可能更方便：<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|每个服务 50 个密钥|  

 在表面上，管理密钥与查询密钥之间没有区别。 这两个密钥都是由 32 个随机生成的字母数字字符组成的字符串。 如果无法跟踪应用程序中指定了哪种类型的密钥，可以[在门户中检查密钥值](https://portal.azure.com)，或使用 [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 返回值和密钥类型。  

> [!NOTE]  
>  在请求 URI 中传递敏感数据（例如 `api-key`）被认为是不良的安全做法。 出于此原因，Azure 认知搜索仅接受作为查询字符串中的 `api-key` 的查询密钥，应避免这样做，除非索引的内容应公开可用。 作为经验法则，我们建议以请求标头的形式传递 `api-key`。  

## <a name="find-existing-keys"></a>查找现有密钥

可以在门户中或通过[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 获取访问密钥。 有关详细信息，请参阅[管理管理员和查询 API 密钥](search-security-api-keys.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 列出订阅的[搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 选择该服务，然后在 "概述" 页上，单击 "**设置**" >**密钥**，查看管理和查询密钥。

   ![门户页上的“设置”>“密钥”部分](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>创建查询密钥

查询键用于对索引中的文档进行只读访问，以执行针对文档集合的操作。 搜索、筛选和建议查询是采用查询密钥的所有操作。 任何返回系统数据或对象定义的只读操作（如索引定义或索引器状态）都需要一个管理密钥。

在客户端应用中限制访问和操作对于保护服务上的搜索资产至关重要。 对于源自客户端应用的任何查询，始终使用查询键，而不是管理密钥。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 列出订阅的[搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 选择该服务，然后在 "概述" 页上，单击 "**设置**" >**项**"。
4. 单击 "**管理查询密钥**"。
5. 使用已为服务生成的查询密钥，或创建最多50个新查询密钥。 默认查询密钥未命名，但是可以通过命名附加查询密钥来实现可管理性。

   ![创建或使用查询密钥](media/search-security-overview/create-query-key.png) 

> [!Note]
> 可在[中C#查询 Azure 认知搜索索引](search-query-dotnet.md)中找到显示查询密钥用法的代码示例。

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>重新生成管理员密钥

为每个服务创建了两个管理密钥，以便您可以使用辅助密钥来旋转主键，以实现业务连续性。

1. 在“设置” >“密钥”页中，复制辅助密钥。
2. 对于所有应用程序，更新 API 密钥设置以使用辅助密钥。
3. 重新生成主密钥。
4. 更新所有应用程序以使用新的主密钥。

如果不小心同时重新生成这两个密钥，则使用这些密钥的所有客户端请求都将失败，并出现 HTTP 403 禁止访问。 但是，不会删除内容，也不会永久锁定。 

你仍可以通过门户或管理层（[REST API](https://docs.microsoft.com/rest/api/searchmanagement/)、 [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)或 Azure 资源管理器）访问该服务。 管理功能通过订阅 ID 而不是服务 api 密钥进行合适，因此即使你的 api 密钥不是也是如此。 

通过门户或管理层创建新密钥后，只要有新密钥并在请求中提供这些密钥，就会将访问还原到内容（索引、索引器、数据源、同义词映射）。

## <a name="secure-api-keys"></a>保护 API 密钥
通过门户或 Resource Manager 界面（PowerShell 或命令行接口）以限制访问，从而保护密钥安全。 如前所述，订阅管理员可以查看和重新生成所有 API 密钥。 作为预防措施，查看角色分配以了解谁有权访问管理密钥。

+ 在服务仪表板中，依次单击“访问控制(IAM)”和“角色分配”选项卡可查看服务的角色分配。

以下角色的成员可以查看和重新生成密钥：所有者、参与者和[搜索服务参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> 如果要实现针对搜索结果的、基于标识的访问，可创建安全筛选器按标识来细化结果，由此去除请求者不应具有访问权限的那些文档。 有关详细信息，请参阅[安全筛选器](search-security-trimming-for-azure-search.md)和[使用 Active Directory 进行保护](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="see-also"></a>另请参阅

+ [Azure 中基于角色的访问控制认知搜索](search-security-rbac.md)
+ [使用 PowerShell 进行管理](search-manage-powershell.md) 
+ [性能和优化文章](search-performance-optimization.md)