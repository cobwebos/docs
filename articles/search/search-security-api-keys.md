---
title: 创建、管理和保护 Azure 搜索的管理员和查询 API 密钥 | Microsoft Docs
description: API 密钥控制服务终结点的访问权限。 管理员密钥授予写入权限。 可为只读访问权限创建查询密钥。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 83a082eb7a18c65a5824bf272e0397b18883277f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>创建和管理 Azure 搜索服务的 API 密钥

对搜索服务的所有请求都需要专为服务生成的 API 密钥。 此 API 密钥是用于验证搜索服务终结点的访问的唯一机制。 

API 密钥是随机生成的数字和字母所组成的字符串。 通过[基于角色的权限](search-security-rbac.md)，可以删除或读取密钥，但无法将密钥替换为用户定义的密码或使用 Active Directory 作为访问搜索操作的主要身份验证方法。 

使用了两种类型的密钥来访问搜索服务：管理员（只写）和查询（只读）。

|密钥|说明|限制|  
|---------|-----------------|------------|  
|管理员|授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。<br /><br /> 创建服务时，会在门户中生成两个管理密钥（称为主密钥和辅助密钥），且可按需单独重新生成。 由于有两个密钥，可以在滚动其中一个密钥时，使用另一个密钥来持续访问服务。<br /><br /> 只能在 HTTP 请求标头中指定管理密钥。 不能在 URL 中放置管理 API 密钥。|每个服务最多有 2 个密钥|  
|查询|授予对索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。<br /><br /> 按需创建查询密钥。 可以在门户中手动创建查询密钥，或者通过[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 以编程方式创建查询密钥。<br /><br /> 可以在 HTTP 请求标头中为搜索、建议或查找操作指定查询密钥。 或者，可以在 URL 中以参数形式传递查询密钥。 根据客户端应用程序编写请求的方式，以查询参数的形式传递密钥可能更方便：<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|每个服务 50 个密钥|  

 在表面上，管理密钥与查询密钥之间没有区别。 这两个密钥都是由 32 个随机生成的字母数字字符组成的字符串。 如果无法跟踪应用程序中指定了哪种类型的密钥，可以[在门户中检查密钥值](https://portal.azure.com)，或使用 [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 返回值和密钥类型。  

> [!NOTE]  
>  在请求 URI 中传递敏感数据（例如 `api-key`）被认为是不良的安全做法。 为此，Azure 搜索只接受查询字符串中 `api-key` 形式的查询密钥。除非必须公开索引的内容，否则应避免这样做。 作为经验法则，我们建议以请求标头的形式传递 `api-key`。  

## <a name="find-api-keys-for-your-service"></a>查找用于服务的 API 密钥

可以在门户中或通过[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 获取访问密钥。 有关详细信息，请参阅[管理管理员和查询 API 密钥](search-security-api-keys.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 列出订阅的[搜索服务](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。
3. 在服务页上选择服务，找到“设置” >“密钥”即可查看管理密钥和查询密钥。

![门户页上的“设置”>“密钥”部分](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>重新生成管理员密钥

为每项服务生成两种管理员密钥，以便可以滚动更新主密钥，从而使用辅助密钥继续访问。

如果同时重新生成主密钥和辅助密钥，任何使用任意密钥访问服务操作的应用程序将不再有权访问该服务。

1. 在“设置” >“密钥”页中，复制辅助密钥。
2. 对于所有应用程序，更新 API 密钥设置以使用辅助密钥。
3. 重新生成主密钥。
4. 更新所有应用程序以使用新的主密钥。

## <a name="secure-api-keys"></a>保护 API 密钥
通过门户或 Resource Manager 界面（PowerShell 或命令行接口）以限制访问，从而保护密钥安全。 如前所述，订阅管理员可以查看和重新生成所有 API 密钥。 作为预防措施，查看角色分配以了解谁有权访问管理密钥。

+ 在服务仪表板中，单击“访问控制 (IAM)”可查看服务的角色分配。

以下角色的成员可以查看和重新生成密钥：所有者、参与者和[搜索服务参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> 如果要实现针对搜索结果的、基于标识的访问，可创建安全筛选器按标识来细化结果，由此去除请求者不应具有访问权限的那些文档。 有关详细信息，请参阅[安全筛选器](search-security-trimming-for-azure-search.md)和[使用 Active Directory 进行保护](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="see-also"></a>另请参阅

+ [Azure 搜索中基于角色的访问控制](search-security-rbac.md)
+ [使用 PowerShell 进行管理](search-manage-powershell.md) 
+ [性能和优化文章](search-performance-optimization.md)