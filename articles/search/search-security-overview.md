---
title: 安全和数据隐私
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索符合 SOC 2、HIPAA 和其他认证。 在筛选器表达式中通过用户和组安全标识符进行连接和数据加密、身份验证和标识访问。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2e509535473fa50fd3150965e1513e056ead18a6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794333"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Azure 认知搜索中的安全性和数据隐私

Azure 认知搜索中内置了全面的安全功能和访问控制，以确保私有内容保持不变。 本文枚举 Azure 认知搜索中内置的安全功能和标准符合性。

Azure 认知搜索安全体系结构跨越了物理安全、加密传输、加密存储和平台范围标准相容性。 在操作上，Azure 认知搜索仅接受经过身份验证的请求。 可以选择通过安全筛选器针对内容添加基于用户的访问控制。 本文涉及每个层的安全性，但主要重点介绍如何在 Azure 认知搜索中保护数据和操作。

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>标准符合性：ISO 27001、SOC 2、HIPAA

Azure 认知搜索按以下标准认证，如[2018 年6月公布](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)：

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 类型 2 符合性](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) 有关完整报告，请转到 [Azure - Azure 政府版 SOC 2 类型 II 报告](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)。 
+ [健康保险可携性和责任法案 (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP（CFR 第 21 篇第·11 部分）](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS 1 级](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [澳大利亚 IRAP 未分类 DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

标准符合性应用于正式版功能。 预览版功能在转变为正式版时进行认证，不能用于具有严格标准要求的解决方案中。 符合性认证记录在 [Microsoft Azure 符合性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)和[信任中心](https://www.microsoft.com/en-us/trustcenter)中。 

## <a name="encrypted-transmission-and-storage"></a>加密的传输和存储

加密在整个索引管道中进行扩展：从连接、传输和到 Azure 认知搜索中存储的索引数据。

| 安全层 | 描述 |
|----------------|-------------|
| 传输中加密 <br>(HTTPS/SSL/TLS) | Azure 认知搜索侦听 HTTPS 端口443。 与 Azure 服务建立的跨平台连接经过加密。 <br/><br/>客户端到服务的所有 Azure 认知搜索交互都是支持 SSL/TLS 1.2 的交互。  请务必为你的服务的 SSL 连接使用 TLSv1.2。|
| 静态加密 <br>Microsoft 托管密钥 | 加密在索引过程中完全进行内部化处理，而不会显著影响完成索引所需的时间或索引大小。 加密自动对所有索引进行，包括对未完全加密的索引（在 2018 年 1 月前创建）的增量更新。<br><br>在内部，加密基于 [Azure 存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)，使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)进行。<br><br> 加密是 Azure 认知搜索内部的，证书和加密密钥由 Microsoft 内部管理，并采用全局应用。 无法在门户中或以编程方式打开或关闭加密、管理或替换为自己的密钥，或者查看加密设置。<br><br>静态加密已在2018年1月24日公布，适用于所有地区的所有服务层，包括免费级别。 对于完全加密，必须删除该日期之前创建的索引并重新生成，以便进行加密。 否则，仅对 1 月 24 日以后添加的新数据进行加密。|
| 静态加密 <br>客户管理的密钥 | 使用客户托管密钥进行加密是一项**预览**功能，该功能不可用于免费服务。 对于付费服务，它仅适用于2019年1月或之后的搜索服务，使用最新的预览版 api 版本（api 版本 = 2019-05-06-01.5.1）。<br><br>Azure 认知搜索索引和同义词映射现在可以在 Azure Key Vault 中通过客户密钥管理密钥进行静态加密。 若要了解详细信息，请参阅[管理 Azure 认知搜索中的加密密钥](search-security-manage-encryption-keys.md)。<br>此功能不会替代静态的默认加密，而是应用于该功能。<br>启用此功能将提高索引大小并降低查询性能。 基于观察到目前为止，你可以在查询时间中看到增加 30%-60%，但实际性能会因索引定义和查询类型而有所不同。 由于对性能的影响，我们建议您只对真正需要此功能的索引启用此功能。

## <a name="azure-wide-user-access-controls"></a>Azure 范围的用户访问控制

几种安全机制在 Azure 范围内可用，并因此自动提供给创建的 Azure 认知搜索资源。

+ [订阅或资源级别的锁可防止删除](../azure-resource-manager/resource-group-lock-resources.md)
+ [基于角色的访问控制 (RBAC) 可以控制对信息和管理操作的访问](../role-based-access-control/overview.md)

所有 Azure 服务支持使用基于角色的访问控制 (RBAC) 在不同的服务之间以一致的方式设置访问级别。 例如，仅限“所有者”和“参与者”角色查看敏感数据（如管理密钥），而任何角色的成员都可以查看服务状态。 RBAC 提供“所有者”、“参与者”和“读取者”角色。 默认情况下，所有服务管理员是“所有者”角色的成员。

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>服务访问和身份验证

虽然 Azure 认知搜索继承 Azure 平台的安全保护，但它还提供了其自己的基于密钥的身份验证。 API 密钥是随机生成的数字和字母所组成的字符串。 密钥的类型（管理员或查询）确定访问的级别。 提交有效密钥被视为请求源自受信任实体的证明。 

有两个搜索服务访问级别，可通过两种类型的密钥启用它们：

* 管理员访问（适用于服务的任何读写操作）
* 查询访问（针对索引的文档集合对只读操作（如查询）有效）

预配服务时会创建*管理员密钥*。 有两个管理密钥，分别指定为*主要*和*辅助*密钥以将它们保持在各自的位置，但事实上是可互换的。 每个服务都有两个管理密钥，以便在转换其中一个时不会丢失服务的访问权限。 你可以根据 Azure 安全最佳做法定期[重新生成管理密钥](search-security-api-keys.md#regenerate-admin-keys)，但无法添加到总的管理密钥计数。 每个搜索服务最多可以有两个管理密钥。

*查询密钥*是按需创建的，并且是为发出查询的客户端应用程序设计的。 最多可以创建 50 个查询密钥。 在应用程序代码中，可以指定搜索 URL 和查询 api 密钥以允许对特定索引的文档集合进行只读访问。 终结点、仅供只读访问的 API 密钥以及目标索引共同定义客户端应用程序连接的作用域和访问级别。

需要对每个请求进行身份验证，而每个请求由必需密钥、操作和对象组成。 链接在一起后，两个权限级别（完全或只读）加上上下文（例如，索引上的查询操作）便足以针对服务操作提供全面的安全性。 有关密钥的详细信息，请参阅[创建和管理 API 密钥](search-security-api-keys.md)。

## <a name="index-access"></a>索引访问

在 Azure 认知搜索中，单个索引不是安全对象。 对索引的访问权限是根据服务层（读取或写入访问）以及操作上下文确定的。

对于最终用户访问，可以使用查询密钥构建要连接的查询请求，这会将任何请求设置为只读，并包含应用使用的特定索引。 在查询请求中，没有联接索引或同时访问多个索引的概念，所有请求都会根据定义以单个索引为目标。 因此，查询请求本身的结构（密钥加上单个目标索引）定义了安全边界。

管理员和开发人员对索引的访问权限没有区别：两者都需要写访问权限才能创建、删除和更新服务管理的对象。 拥有服务管理密钥的任何人都可以读取、修改或删除同一服务中的任何索引。 为了防止意外删除或恶意删除索引，代码资产的内部源代码管理作为一种补救机制，可以还原意外的索引删除或修改。 Azure 认知搜索在群集内进行故障转移以确保可用性，但它不存储或执行用于创建或加载索引的专有代码。

需要索引级安全边界的多租户解决方案通常包含一个中间层，客户可以使用它来处理索引隔离。 有关多租户用例的详细信息，请参阅多[租户 SaaS 应用程序和 Azure 认知搜索的设计模式](search-modeling-multitenant-saas-applications.md)。

## <a name="admin-access"></a>管理员访问权限

[基于角色的访问（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)确定您是否有权访问服务及其内容的控件。 如果你是 Azure 认知搜索服务的所有者或参与者，则可以使用门户或 PowerShell **Az. 搜索**模块来创建、更新或删除服务中的对象。 你还可以使用[Azure 认知搜索管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)。

## <a name="user-access"></a>用户访问权限

默认情况下，由查询请求中的访问密钥确定用户对索引的访问权限。 大部分开发人员会针对客户端搜索请求创建并分配[*查询密钥*](search-security-api-keys.md)。 查询密钥授予对索引内的所有内容的读取访问权限。

如果需要对内容进行精细的基于每个用户的控制，可以在查询中生成安全筛选器，返回与给定安全标识关联的文档。 基于标识的访问控制不是预定义的角色和角色分配，它作为*筛选器*实现，该筛选器可以根据标识修整文档和内容的搜索结果。 下表描述了修整未经授权内容的搜索结果的两种方法。

| 方法 | 描述 |
|----------|-------------|
|[基于标识筛选器的安全修整](search-security-trimming-for-azure-search.md)  | 阐述实现用户标识访问控制的基本工作流。 该工作流包括将安全标识符添加到索引，然后解释如何针对该字段进行筛选，以修整受禁内容的结果。 |
|[Azure Active Directory 标识的安全修整](search-security-trimming-for-azure-search-with-aad.md)  | 此文延伸了前一篇文章的内容，提供了有关从 Azure Active Directory (AAD)（Azure 云平台中的一个[免费服务](https://azure.microsoft.com/free/)）检索标识的步骤。 |

## <a name="table-permissioned-operations"></a>表：权限操作

下表总结了 Azure 认知搜索中所允许的操作，以及哪些密钥会解锁特定操作。

| Operation | 权限 |
|-----------|-------------------------|
| 创建服务 | Azure 订阅持有者|
| 缩放服务 | 管理密钥，资源中的 RBAC 所有者或参与者  |
| 删除服务 | 管理密钥，资源中的 RBAC 所有者或参与者 |
| 创建、修改、删除服务中的对象： <br>索引和组件部分（包括分析器定义、评分配置文件、CORS 选项）、索引器、数据源、同义词、建议器。 | 管理密钥，资源中的 RBAC 所有者或参与者  |
| 查询索引 | 管理密钥或查询密钥（RBAC 不适用） |
| 查询系统信息，例如返回统计信息、计数和对象列表。 | 管理密钥，资源的 RBAC（所有者、参与者、读取者） |
| 管理管理密钥 | 管理密钥，资源中的 RBAC 所有者或参与者。 |
| 管理查询密钥 |  管理密钥，资源中的 RBAC 所有者或参与者。  |

## <a name="physical-security"></a>物理安全性

Microsoft 数据中心提供行业领先的物理安全性，符合广泛的标准和法规要求。 若要了解详细信息，请转到[全球数据中心](https://www.microsoft.com/cloud-platform/global-datacenters)页，或观看有关数据中心安全性的简短视频。

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>另请参阅

+ [.NET 入门（演示如何使用管理密钥创建索引）](search-create-index-dotnet.md)
+ [REST 入门（演示如何使用管理密钥创建索引）](search-create-index-rest-api.md)
+ [使用 Azure 认知搜索筛选器进行基于标识的访问控制](search-security-trimming-for-azure-search.md)
+ [使用 Azure 认知搜索筛选器 Active Directory 基于标识的访问控制](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure 认知搜索中的筛选器](search-filters.md)