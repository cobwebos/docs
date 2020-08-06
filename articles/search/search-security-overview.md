---
title: 安全概述
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索符合 SOC 2、HIPAA 和其他认证的要求。 在筛选器表达式中通过用户和组安全标识符进行连接和数据加密、身份验证和标识访问。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: fb265f8a8ab34972dac8529d267e41edaf0acb4c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87829282"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Azure 认知搜索中的安全性 - 概述

本文介绍 Azure 认知搜索中可以保护内容和操作的关键安全功能。

+ 在存储层上，静态加密是为保存到磁盘的所有服务托管内容内置的，包括索引、同义词映射以及索引器、数据源和技能集的定义。 Azure 认知搜索还支持添加客户托管的密钥 (CMK) ，用于对索引内容进行补充加密。 对于8月 1 2020 日后创建的服务，CMK 加密扩展到临时磁盘上的数据，以对索引内容进行完全双重加密。

+ 入站安全性通过不断提升的安全性级别来保护搜索服务终结点：从请求所使用的 API 密钥到防火墙中的入站规则，再到全面保护服务不受公共 Internet 影响的专用终结点。

+ 出站安全适用于从外部源提取内容的索引器。 对于出站请求，请设置托管标识，使其在访问 Azure 存储、Azure SQL、Cosmos DB 或其他 Azure 数据源的数据时搜索受信任的服务。 托管标识用于替代连接上的凭据或访问密钥。 本文未介绍出站安全性。 有关此功能的详细信息，请参阅[使用托管标识连接到数据源](search-howto-managed-identities-data-sources.md)。

观看此快速进度的视频，大致了解安全体系结构和每个功能类别。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>加密的传输和存储

在 Azure 认知搜索中，加密从连接和传输开始，并扩展到磁盘上存储的内容。 对于公共 Internet 上的搜索服务，Azure 认知搜索会侦听 HTTPS 端口 443。 客户端到服务的所有连接都使用 TLS 1.2 加密。 不支持更早的版本（1.0 或 1.1）。

对于由搜索服务在内部处理的数据，下表介绍了[数据加密模型](../security/fundamentals/encryption-atrest.md#data-encryption-models)。 某些功能（如知识库、增量扩充和基于索引器的索引）、对其他 Azure 服务中的数据结构进行读取或写入。 这些服务独立于 Azure 认知搜索提供自己的加密支持级别。

| 模型 | 键&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 要求&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 限制 | 适用于 |
|------------------|-------|-------------|--------------|------------|
| 服务器端加密 | Microsoft 管理的密钥 | 无 (内置)  | 无，适用于所有区域，适用于 24 2018 年1月之后创建的内容。 | 内容 (索引和同义词映射) 和定义 (索引器、数据源、技能集)  |
| 服务器端加密 | 客户管理的密钥 | Azure Key Vault | 适用于所有区域中的可计费层，适用于2019年1月之后创建的内容。 | 数据磁盘) 内容 (索引和同义词映射 |
| 服务器端双加密 | 客户管理的密钥 | Azure Key Vault | 1 2020 年8月之后，在所选区域中的 "搜索服务" 上提供计费层。 | 内容 (索引和同义词映射) 数据磁盘和临时磁盘 |

### <a name="service-managed-keys"></a>服务托管的密钥

服务托管加密是一项基于[Azure 存储服务加密](../storage/common/storage-service-encryption.md)、使用256位[AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)的 Microsoft 内部操作。 它会自动对所有索引编制，包括对未完全加密 (在) 2018 年1月之前创建的索引的增量更新。

### <a name="customer-managed-keys-cmk"></a>客户管理的密钥 (CMK) 

客户托管的密钥需要额外的可计费服务，Azure Key Vault，它可以位于不同的区域，但在 Azure 认知搜索的同一订阅下。 启用 CMK 加密会增加索引大小并降低查询性能。 根据迄今为止的观察结果，查询时间预期会增加 30%-60%，不过，实际性能根据索引定义和查询类型而有所不同。 由于这种性能影响，我们建议仅对真正需要此功能的索引启用此功能。 有关详细信息，请参阅[在 Azure 中配置客户管理的加密密钥认知搜索](search-security-manage-encryption-keys.md)。

<a name="double-encryption"></a>

### <a name="double-encryption"></a>双加密 

在 Azure 认知搜索中，双加密是 CMK 的扩展。 它被理解为 CMK 的两倍加密 (一次，并再次通过服务托管的密钥) 和全面的作用域，其中包含写入到数据磁盘的长期存储以及写入临时磁盘的短期存储。 CMK 在 1 2020 年8月之前之间的差异，以及在 Azure 认知搜索中使 CMK 成为双重加密功能，这是临时磁盘上的静态数据加密。

在8月1日之后，当前在这些区域中创建的新服务中提供双加密：

+ 美国西部 2
+ 美国东部
+ 美国中南部
+ US Gov 弗吉尼亚州
+ US Gov 亚利桑那州

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>入站安全性和终结点保护

入站安全性功能通过不断提升的安全性和复杂性级别来保护搜索服务终结点。 首先，所有请求都需要 API 密钥才能进行经过身份验证的访问。 其次，你可以选择设置防火墙规则，以限制对特定 IP 地址的访问。 为了提供高级保护，第三个选项是启用 Azure 专用链接来保护服务终结点不受所有 Internet 流量的影响。

### <a name="public-access-using-api-keys"></a>使用 API 密钥进行公共访问

默认情况下，将使用基于密钥的身份验证（用于对搜索服务终结点进行管理员或查询访问）通过公有云来访问搜索服务。 API 密钥是随机生成的数字和字母所组成的字符串。 密钥的类型（管理员或查询）确定访问的级别。 提交有效密钥被视为请求源自受信任实体的证明。 

有两个搜索服务访问级别，可通过以下 API 密钥启用它们：

+ 管理密钥 (允许对搜索服务执行[创建-读取-更新-删除](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)操作的读写访问权限) 

+ 查询密钥（允许对索引的文档集合进行只读访问）

预配服务时会创建*管理员密钥*。 有两个管理密钥，分别指定为*主要*和*辅助*密钥以将它们保持在各自的位置，但事实上是可互换的。 每个服务都有两个管理密钥，以便在转换其中一个时不会丢失服务的访问权限。 可以根据 Azure 安全最佳做法定期[重新生成管理密钥](search-security-api-keys.md#regenerate-admin-keys)，但不能将其添加到管理密钥总数。 每个搜索服务最多有两个管理密钥。

*查询密钥*根据需要创建，专用于发出查询的客户端应用程序。 最多可以创建 50 个查询密钥。 在应用程序代码中，可以指定搜索 URL 和查询 API 密钥，以便对特定索引的文档集合进行只读访问。 终结点、仅供只读访问的 API 密钥以及目标索引共同定义客户端应用程序连接的作用域和访问级别。

需要对每个请求进行身份验证，而每个请求由必需密钥、操作和对象组成。 链接在一起后，两个权限级别（完全或只读）加上上下文（例如，索引上的查询操作）便足以针对服务操作提供全面的安全性。 有关密钥的详细信息，请参阅[创建和管理 API 密钥](search-security-api-keys.md)。

### <a name="ip-restricted-access"></a>IP 限制访问

为了进一步控制对搜索服务的访问，可以创建入站防火墙规则，以允许访问特定 IP 地址或某个范围的 IP 地址。 所有客户端连接必须通过允许的 IP 地址建立，否则连接将被拒绝。

可以使用门户[配置入站访问](service-configure-firewall.md)。 

或者，可以使用管理 REST API。 带有 [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule) 参数的 API 版本 2020-03-13 允许你标识希望向其授予搜索服务访问权限的 IP 地址（单个或在某个范围内），通过这种方式限制对该服务的访问。 

### <a name="private-endpoint-no-internet-traffic"></a>专用终结点 (无 Internet 流量) 

Azure 认知搜索的[专用终结点](../private-link/private-endpoint-overview.md)允许[虚拟网络](../virtual-network/virtual-networks-overview.md)上的客户端通过[专用链接](../private-link/private-link-overview.md)安全访问搜索索引中的数据。 

专用终结点使用虚拟网络地址空间中的 IP 地址来连接到搜索服务。 客户端与搜索服务之间的网络流量将在 Microsoft 主干网络上遍历虚拟网络和专用链接，从而消除了公共 internet 的泄露。 VNET 允许在资源之间通过本地网络和 Internet 进行安全通信。 

虽然这种解决方案是最安全的，但使用其他服务是一项额外的成本，因此请确保你清楚地了解其中的好处。 有关成本的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/private-link/)。 有关这些组件如何协同工作的详细信息，请观看本文顶部的视频。 专用终结点选项的范围从5:48 开始到视频。 有关如何设置终结点的说明，请参阅[为 Azure 认知搜索创建专用终结点](service-create-private-endpoint.md)。

## <a name="index-access"></a>索引访问

在 Azure 认知搜索中，单个索引不是安全对象。 相反，对索引的访问权限在服务层上确定 (对服务) 的读取或写入访问权限以及操作上下文。

对于最终用户访问，可以使用查询密钥构建要连接的查询请求，这会将任何请求设置为只读，并包含应用使用的特定索引。 在查询请求中，没有联接索引或同时访问多个索引的概念，所有请求都会根据定义以单个索引为目标。 因此，查询请求本身的结构（密钥加上单个目标索引）定义了安全边界。

管理员和开发人员对索引的访问权限没有区别：两者都需要写访问权限才能创建、删除和更新服务管理的对象。 拥有服务管理密钥的任何人都可以读取、修改或删除同一服务中的任何索引。 为了防止意外删除或恶意删除索引，代码资产的内部源代码管理作为一种补救机制，可以还原意外的索引删除或修改。 Azure 认知搜索在群集中提供故障转移功能来确保可用性，但它不会存储或执行用于创建或加载索引的专属代码。

需要索引级安全边界的多租户解决方案通常包含一个中间层，客户可以使用它来处理索引隔离。 有关多租户用例的详细信息，请参阅[多租户 SaaS 应用程序与 Azure 认知搜索的设计模式](search-modeling-multitenant-saas-applications.md)。

## <a name="user-access"></a>用户访问权限

用户访问索引和其他对象的方式由请求上的 API 密钥类型决定。 大部分开发人员会针对客户端搜索请求创建并分配[*查询密钥*](search-security-api-keys.md)。 查询密钥授予对索引中可搜索内容的只读访问权限。

如果需要对搜索结果进行特定于每个用户的控制，则可以在查询上生成安全筛选器，返回与给定安全标识相关联的文档。 基于标识的访问控制是根据标识来修整文档和内容的搜索结果的*筛选器*，而不是预定义的角色和角色分配。 下表描述了修整未经授权内容的搜索结果的两种方法。

| 方法 | 描述 |
|----------|-------------|
|[基于标识筛选器的安全修整](search-security-trimming-for-azure-search.md)  | 阐述实现用户标识访问控制的基本工作流。 该工作流包括将安全标识符添加到索引，然后解释如何针对该字段进行筛选，以修整受禁内容的结果。 |
|[Azure Active Directory 标识的安全修整](search-security-trimming-for-azure-search-with-aad.md)  | 此文延伸了前一篇文章的内容，提供了有关从 Azure Active Directory (AAD)（Azure 云平台中的一个[免费服务](https://azure.microsoft.com/free/)）检索标识的步骤。 |

## <a name="administrative-rights"></a>管理权限

Azure [RBAC) 的 azure 基于角色的访问控制 (](../role-based-access-control/overview.md)是在[azure 资源管理器](../azure-resource-manager/management/overview.md)上构建的用于预配 azure 资源的授权系统。 在 Azure 认知搜索中，资源管理器用于创建或删除服务、管理 API 密钥以及缩放服务。 因此，Azure 角色分配将确定谁可以执行这些任务，无论他们使用的是[门户](search-manage.md)、 [POWERSHELL](search-manage-powershell.md)还是[管理 REST api](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)。

相反，对服务上托管的内容（如创建或删除索引的能力）的管理权限是授予，如[前一部分](#index-access)中所述。

> [!TIP]
> 利用 Azure 范围内的机制，可以锁定订阅或资源，以防止具备管理员权限的用户意外或在未经授权的情况下删除搜索服务。 有关详细信息，请参阅[锁定资源以防止意外删除](../azure-resource-manager/management/lock-resources.md)。

## <a name="certifications-and-compliance"></a>认证和合规性

Azure 认知搜索已经过认证，适用于公有云和 Azure 政府的多种全球、区域和行业特定的标准。 有关完整列表，请从官方审核报告页下载[ **Microsoft Azure 符合性产品**白皮书](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)。

为实现符合性，你可以使用[Azure 策略](../governance/policy/overview.md)来实现[azure 安全基准](../security/benchmarks/introduction.md)的高安全性最佳实践。 Azure 安全基准是安全建议的集合，整理到安全控件中，这些安全控制映射到你应对服务和数据的威胁应采取的关键操作。 目前有11个安全控制，其中包括[网络安全性](../security/benchmarks/security-control-network-security.md)、[日志记录和监视](../security/benchmarks/security-control-logging-monitoring.md)以及[数据保护](../security/benchmarks/security-control-data-protection.md)。

Azure 策略是 Azure 中内置的一项功能，可帮助你管理多个标准（包括 Azure 安全基准）的符合性。 对于众所周知的基准，Azure 策略提供内置的定义，这些定义既提供条件，也提供可操作的响应，以解决不符合性问题。 

对于 Azure 认知搜索，当前有一个内置定义。 它用于诊断日志记录。 利用此内置功能，您可以分配一个策略，用于标识任何缺少诊断日志记录的搜索服务，然后将其打开。 有关详细信息，请参阅 azure[认知搜索的 Azure 策略合规性控制](security-controls-policy.md)。

## <a name="see-also"></a>另请参阅

+ [Azure 安全基础知识](../security/fundamentals/index.yml)
+ [Azure 安全性](https://azure.microsoft.com/overview/security)
+ [Azure 安全中心](../security-center/index.yml)