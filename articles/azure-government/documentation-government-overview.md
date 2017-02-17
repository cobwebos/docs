---
title: "Azure 政府版文档概述 | Microsoft 文档"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 56d84e26-947e-4f3b-8e33-18247f1c7944
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/10/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 8e5dd57f4ff55e350aebce339f9f723cc6ca917b
ms.openlocfilehash: 2a1e9c6e001280451ae01330ee659d1e27c2d89a


---
# <a name="azure-government-documentation-overview"></a>Azure Government 文档概述
## <a name="introduction-to-azure-government-documentation"></a>Azure Government 文档简介
此网站描述了 [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) 服务的功能，并提供了适用于所有客户的一般性指南。 在 Azure Government 订阅中包含特定管制的数据之前，应熟悉 Azure Government 功能，并在有任何疑问时咨询你的客户团队。

你应参考 [Microsoft Azure 信任中心法规遵从页](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)，了解在特定合格证和法规涵盖下关于 Azure Government 服务的当前信息。 虽然可能也提供了其他 Microsoft 服务，但不在 Azure Government 涵盖服务的范围内，也不在本文讨论范围内。 Azure Government 服务可能还允许你使用不同的其他资源、应用程序或由第三方 - 或根据单独的使用条款和隐私策略由 Microsoft 提供的服务，但这些不在本文讨论范围内。 你有责任审阅所有此类“外接”产品（如应用商店中的产品/服务）的条款，以确保它们满足你在法规遵从方面的需要。

Azure Government 适用于受特定政府法规和要求（如 NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS）约束来进行数据处理的实体。这些实体在使用 Azure Government 时需要遵守这些法规。 Azure Government 客户必须接受资格验证。

对 Azure Government 的资格验证有疑问的实体应咨询其客户团队。

## <a name="principles-for-securing-customer-data-in-azure-government"></a>在 Azure Government 中保护客户数据的原则
Azure Government 提供了一系列功能和服务，可用于构建云解决方案以满足你在管控数据方面的需要。 一个兼容的客户解决方案就是有效实施现成可用的 Azure Government 功能，辅以可靠的数据安全实践。
在 Azure Government 中托管解决方案时，Microsoft 将在云基础结构级别处理许多这些需求。

下图显示了 Azure 深层防御模型。 例如，Microsoft 提供了基本的云基础结构 DDOS 以及客户功能，例如可满足客户特定应用程序 DDOS 需要的安全设备。

![替换文字](./media/azure-government-Defenseindepth.png)

此页概述了用于保护你的服务和应用程序安全的基本原则，提供了如何应用这些原则的指南和最佳实践；换言之，客户应如何明智地利用 Azure Government 来满足解决方案处理 ITAR 信息时所需要的义务和职责。

用于保护客户数据安全的首要原则包括：

* 使用加密保护数据
* 管理机密
* 隔离以限制数据访问

## <a name="protecting-customer-data-using-encryption"></a>使用加密保护客户数据
缓解风险并满足监管义务使数据加密的关注度和重要性不断增加。 实施有效加密，加强当前网络和应用程序的安全措施，并降低云环境的整体风险。

### <a name="a-nameoverviewaencryption-at-rest"></a><a name="Overview"></a>静态加密
静态数据加密适用于保护保存在磁盘存储空间中的客户内容。 有以下几种方式可能有这种情况：

### <a name="a-nameoverviewastorage-service-encryption"></a><a name="Overview"></a>存储服务加密
Azure 存储服务加密可在存储帐户级别启用，使块 Blob 和页 Blob 可以在写入 Azure 存储空间时自动加密。 当你从 Azure 存储空间读取数据时，存储服务将在返回数据之前将其解密。 使用此方式可以保护数据，而无需修改代码或将代码添加到任何应用程序。

### <a name="a-nameoverviewaazure-disk-encryption"></a><a name="Overview"></a>Azure 磁盘加密
使用 Azure 磁盘加密对 Azure 虚拟机使用的 OS 磁盘和数据磁盘进行加密。 与 Azure 密钥保管库的集成使你拥有控制权，并帮助你管理磁盘加密密钥。

### <a name="a-nameoverviewaclient-side-encryption"></a><a name="Overview"></a>客户端加密
客户端加密内置于 Java 和 .NET 存储客户端库，这些库利用 Azure 密钥保管库 API 让加密变得简单。 使用 Azure 密钥保管库可让你使用 Azure Active Directory 为特定用户获得 Azure 密钥保管库中密码的访问权限。

### <a name="a-nameoverviewaencryption-in-transit"></a><a name="Overview"></a>传输中加密
此基本加密可用于与 Azure Government 的连接，支持传输层安全 (TLS) 1.2 协议和 X.509 证书。 联邦信息处理标准 (FIPS) 140-2 级别 1 的加密算法还可用于 Azure Government 数据中心之间的基础结构网络连接。  Windows Server 2012 R2、Windows 8-plus 虚拟机和 Azure 文件共享可将 SMB 3.0 用于虚拟机和文件共享之间的加密。 在将数据传输到客户端应用程序中的存储之前，使用客户端加密对数据进行加密，并在从存储传出后对数据进行解密。

### <a name="a-nameoverviewabest-practices-for-encryption"></a><a name="Overview"></a>加密的最佳实践
* IaaS VM：使用 Azure 磁盘加密。 启用存储服务加密来加密在 Azure 存储空间中用于备份这些磁盘的 VHD 文件，但只会加密新写入的数据。 这意味着，如果创建 VM，然后对保存 VHD 文件的存储帐户启用存储服务加密，则只对更改内容进行加密，而不会加密原始 VHD 文件。
* 客户端加密：这是加密数据的最安全方法，因为它将在传输前加密数据，并加密静态数据。 但是，它需要使用存储将代码添加到应用程序，而你可能不想要这样做。 在这些情况下，可以针对传输中的数据使用 HTTPs，并使用存储服务加密来加密静态数据。 客户端加密还将在客户端上产生更多负载，这一点必须在扩展性计划中加以考虑，特别是要加密并传输大量数据时。

有关 Azure 中加密选项的详细信息，请参阅[存储安全指南](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide)。

## <a name="protecting-customer-data-by-managing-secrets"></a>通过管理密钥来保护客户数据
安全的密钥管理对在云中保护数据必不可少。 客户应尽量简化密钥管理，并对云应用程序和服务用来加密数据的密钥保持控制。

### <a name="a-nameoverviewabest-practices-for-managing-secrets"></a><a name="Overview"></a>管理密匙的最佳实践
* 使用密钥保管库可在最大程度上减少密匙暴露在硬编码配置文件、脚本或源代码的风险。 Azure 密钥保管库加密密钥（如 Azure 磁盘加密的加密密钥）和机密（如密码）的方式是将它们存放在 FIPS 140-2 Level 2 已验证的硬件安全模块 (HSM) 中。 为了提升可靠性，可以在这些 HSM 中导入或生成密钥。
* 应用程序代码和模板应只包含机密的 URI 引用（这意味着实际机密不在代码、配置或源代码存储库中）。 这样可防止他人对内部或外部存储库（例如 GitHub 中的 harvest-bot）进行密钥钓鱼攻击。
* 利用密钥保管库中强大的 RBAC 控制。 如果受信任的操作人员离开公司或转到公司内的某个新团队，应阻止他们访问机密信息。  

有关其他信息，请参阅 [Azure Government 的密钥保管库](/azure-government/azure-government-tech-keyvault)

## <a name="isolation-to-restrict-data-access"></a>隔离以限制数据访问
隔离是有关使用边界、分段和容器来限制仅对授权用户、服务和应用程序的数据访问。 例如，租户之间的隔离是一种用于多租户云平台（如 Microsoft Azure）的基本安全机制。 逻辑隔离有助于防止一个租户干扰任何其他租户的操作。

### <a name="a-nameoverviewaenvironment-isolation"></a><a name="Overview"></a>环境隔离
Azure Government 环境是与 Microsoft 网络的其余部分隔离的物理实例。 这是通过一系列物理和逻辑控制来实现的，包括：使用生物识别设备和相机保护物理障碍。  对需要逻辑访问生产环境的 Microsoft 专业人员使用特定凭据和多重身份验证。  Azure Government 的所有服务基础结构都位于美国境内。

#### <a name="a-nameoverviewaper-customer-isolation"></a><a name="Overview"></a>按客户隔离
Azure 通过 VLAN 隔离、ACL、负载平衡器和 IP 筛选器来实施网络访问控制和隔离

客户可以在订阅、资源组、虚拟网络和子网之间进一步隔离其资源。

有关在 Microsoft Azure 中隔离的详细信息，请参阅 [Azure 安全指南的隔离部分](/azure-security-getting-started/#isolation)。

有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客</a>。




<!--HONumber=Jan17_HO5-->


