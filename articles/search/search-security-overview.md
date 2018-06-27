---
title: Azure 搜索中的安全性和数据隐私 | Microsoft Docs
description: Azure 搜索符合 SOC 2、HIPAA 和其他认证。 在 Azure 搜索筛选器中通过用户和组安全标识符进行连接和数据加密、身份验证和标识访问。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: 888f7c3ced0ef48cff222bffdbf0f278fa5f42b3
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285723"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Azure 搜索中的安全性和数据隐私

Azure 搜索中内置了全面的安全功能和访问控制，以确保私有内容保持原有状态。 本文列举了 Azure 搜索中内置的安全功能和标准符合性。

Azure 搜索安全体系结构跨越物理安全性、加密传输、加密存储和全平台标准符合性。 在操作上，Azure 搜索仅接受经过身份验证的请求。 可以选择通过安全筛选器针对内容添加基于用户的访问控制。 本文会涉及每个层的安全性，但侧重于有关如何在 Azure 搜索中保护数据和操作。

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>标准符合性：ISO 27001、SOC 2、HIPAA

对于已正式发布的功能，标准符合性的部分列表包括 SOC 2 类型 2 和 HIPAA。 预览功能被认证为正式版的一部分，不能用于具有特定标准要求的解决方案。 符合性认证记录在 [Microsoft Azure 符合性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)和[信任中心](https://www.microsoft.com/en-us/trustcenter)中。 

以下标准的认证[于 2018 年 6 月公布](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)。

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 类型 2 符合性](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) 有关完整报告，请转到 [Azure - Azure 政府版 SOC 2 类型 II 报告](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)。 
+ [健康保险可携性和责任法案 (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP（CFR 第 21 篇第·11 部分）](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS 1 级](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [澳大利亚 IRAP 未分类 DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

## <a name="encrypted-transmission-and-storage"></a>加密的传输和存储

加密扩展到整个索引管道：从连接通过传输向下到存储在 Azure 搜索中的索引数据。

| 安全层 | 说明 |
|----------------|-------------|
| 传输中加密 | Azure 搜索在 HTTPS 端口 443 上侦听。 与 Azure 服务建立的跨平台连接经过加密。 |
| 静态加密 | 加密在索引过程中完全进行内部化处理，而不会显著影响完成索引所需的时间或索引大小。 加密自动对所有索引进行，包括对未完全加密的索引（在 2018 年 1 月前创建）的增量更新。<br><br>在内部，加密基于 [Azure 存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)，使用 256 位 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)进行。|

加密在 Azure 搜索内部进行，证书和加密密钥由 Microsoft 内部管理，并广泛应用。 无法在门户中或以编程方式打开或关闭加密、管理或替换为自己的密钥，或者查看加密设置。 

静态加密已于 2018 年 1 月 24 日宣布推出并应用于所有区域中的所有服务层，包括共享（免费）服务。 对于完全加密，必须删除该日期之前创建的索引并重新生成，以便进行加密。 否则，仅对 1 月 24 日以后添加的新数据进行加密。

## <a name="azure-wide-user-access-controls"></a>Azure 范围的用户访问控制

整个 Azure 提供多种安全机制，因此，创建的 Azure 搜索资源也会自动获得这些安全机制。

+ [订阅或资源级别的锁可防止删除](../azure-resource-manager/resource-group-lock-resources.md)
+ [基于角色的访问控制 (RBAC) 可以控制对信息和管理操作的访问](../role-based-access-control/overview.md)

所有 Azure 服务支持使用基于角色的访问控制 (RBAC) 在不同的服务之间以一致的方式设置访问级别。 例如，仅限“所有者”和“参与者”角色查看敏感数据（如管理密钥），而任何角色的成员都可以查看服务状态。 RBAC 提供“所有者”、“参与者”和“读取者”角色。 默认情况下，所有服务管理员是“所有者”角色的成员。

## <a name="service-access-and-authentication"></a>服务访问和身份验证

虽然 Azure 搜索继承了 Azure 平台的安全保护，但它还提供其自己的基于密钥的身份验证。 API 密钥是随机生成的数字和字母所组成的字符串。 密钥的类型（管理员或查询）确定访问的级别。 提交有效密钥被视为请求源自受信任实体的证明。 两种类型的密钥用于访问搜索服务：

* 管理员（适用于服务的任何读写操作）
* 查询（适用于只读操作，例如针对索引的查询）

预配服务时，会创建管理密钥。 有两个管理密钥，分别指定为*主要*和*辅助*密钥以将它们保持在各自的位置，但事实上是可互换的。 每个服务都有两个管理密钥，以便在转换其中一个时不会丢失服务的访问权限。 可以重新生成任一管理密钥，但无法添加到管理密钥总计数。 每个搜索服务最多有两个管理密钥。

查询密钥是根据需要创建的，专用于直接调用搜索的客户端应用程序。 最多可以创建 50 个查询密钥。 在应用程序代码中，可以指定搜索 URL 和查询 API 密钥，以允许对服务的只读访问。 应用程序代码还会指定应用程序使用的索引。 终结点、仅供只读访问的 API 密钥以及目标索引共同定义客户端应用程序连接的作用域和访问级别。

需要对每个请求进行身份验证，而每个请求由必需密钥、操作和对象组成。 链接在一起后，两个权限级别（完全或只读）加上上下文（例如，索引上的查询操作）便足以针对服务操作提供全面的安全性。 有关密钥的详细信息，请参阅[创建和管理 API 密钥](search-security-api-keys.md)。

## <a name="index-access"></a>索引访问

在 Azure 搜索中，单个索引不是安全对象。 对索引的访问权限是根据服务层（读取或写入访问）以及操作上下文确定的。

对于最终用户访问，可以使用查询密钥构建要连接的查询请求，这会将任何请求设置为只读，并包含应用使用的特定索引。 在查询请求中，没有联接索引或同时访问多个索引的概念，所有请求都会根据定义以单个索引为目标。 因此，查询请求本身的结构（密钥加上单个目标索引）定义了安全边界。

管理员和开发人员对索引的访问权限没有区别：两者都需要写访问权限才能创建、删除和更新服务管理的对象。 拥有服务管理密钥的任何人都可以读取、修改或删除同一服务中的任何索引。 为了防止意外删除或恶意删除索引，代码资产的内部源代码管理作为一种补救机制，可以还原意外的索引删除或修改。 Azure 搜索在群集中提供故障转移功能来确保可用性，但它不会存储或执行用于创建或加载索引的专属代码。

需要索引级安全边界的多租户解决方案通常包含一个中间层，客户可以使用它来处理索引隔离。 有关多租户用例的详细信息，请参阅[多租户 SaaS 应用程序与 Azure 搜索的设计模式](search-modeling-multitenant-saas-applications.md)。

## <a name="admin-access-from-client-apps"></a>从客户端应用进行管理访问

Azure 搜索管理 REST API 是 Azure 资源管理器的扩展并共享其依赖关系。 因此，要对 Azure 搜索进行服务管理，必须事先安装 Active Directory。 必须使用 Azure Active Directory 对客户端代码发出的所有管理请求进行身份验证，然后请求才能到达资源管理器。

针对 Azure 搜索服务终结点发出的数据请求（例如“创建索引（Azure 搜索服务 REST API）”或“搜索文档（Azure 搜索服务 REST API）”）在请求标头中使用 api-key。

如果应用程序代码需要处理针对搜索索引或文档执行的服务管理操作以及数据操作，请在代码中实现两种身份验证方法：Azure 搜索的本机访问密钥，以及资源管理器所需的 Active Directory 身份验证方法。 

有关在 Azure 搜索中构建请求的信息，请参阅 [Azure 搜索服务 REST](https://docs.microsoft.com/rest/api/searchservice/)。 有关资源管理器的身份验证要求的详细信息，请参阅[使用资源管理器身份验证 API 访问订阅](../azure-resource-manager/resource-manager-api-authentication.md)。

## <a name="user-access-to-index-content"></a>用户对索引内容的访问

可以通过查询中的安全筛选器实施对索引内容的基于用户的访问，并返回与给定安全标识关联的文档。 基于标识的访问控制不是预定义的角色和角色分配，它是作为一个筛选器实现的，该筛选器可以根据标识修整文档和内容的搜索结果。 下表描述了修整未经授权内容的搜索结果的两种方法。

| 方法 | 说明 |
|----------|-------------|
|[基于标识筛选器的安全修整](search-security-trimming-for-azure-search.md)  | 阐述实现用户标识访问控制的基本工作流。 该工作流包括将安全标识符添加到索引，然后解释如何针对该字段进行筛选，以修整受禁内容的结果。 |
|[Azure Active Directory 标识的安全修整](search-security-trimming-for-azure-search-with-aad.md)  | 此文延伸了前一篇文章的内容，提供了有关从 Azure Active Directory (AAD)（Azure 云平台中的一个[免费服务](https://azure.microsoft.com/free/)）检索标识的步骤。 |

## <a name="table-permissioned-operations"></a>表：权限操作

下表汇总了 Azure 搜索中允许的操作，以及哪个密钥可以解锁特定操作的访问。

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
+ [使用 Azure 搜索筛选器进行基于标识的访问控制](search-security-trimming-for-azure-search.md)
+ [使用 Azure 搜索筛选器进行 Active Directory 基于标识的访问控制](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure 搜索中的筛选器](search-filters.md)