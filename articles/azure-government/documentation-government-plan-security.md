---
title: "Azure 政府版安全性 |Microsoft Docs"
description: "提供 Azure Government 中可用服务的概述"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c3645bda-bf35-4232-a54d-7a0bfab2d594
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a9cc2bc044caddec63645932067f7a346945db78
ms.openlocfilehash: 11d0a67a4c368b50e2f2e8648c4fb2db8e0421c2


---
# <a name="security"></a>“安全”
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

### <a name="protecting-customer-data-using-encryption"></a>使用加密保护客户数据
缓解风险并满足监管义务使数据加密的关注度和重要性不断增加。 实施有效加密，加强当前网络和应用程序的安全措施，并降低云环境的整体风险。

#### <a name="encryption-at-rest"></a>静态加密
静态数据加密适用于保护保存在磁盘存储空间中的客户内容。 有以下几种方式可能有这种情况：

#### <a name="storage-service-encryption"></a>存储服务加密
Azure 存储服务加密可在存储帐户级别启用，使块 Blob 和页 Blob 可以在写入 Azure 存储空间时自动加密。 当你从 Azure 存储空间读取数据时，存储服务将在返回数据之前将其解密。 使用此方式可以保护数据，而无需修改代码或将代码添加到任何应用程序。

#### <a name="client-side-encryption"></a>客户端加密
客户端加密内置于 Java 和 .NET 存储客户端库，这些库利用 Azure 密钥保管库 API 让加密变得简单。 使用 Azure 密钥保管库可让你使用 Azure Active Directory 为特定用户获得 Azure 密钥保管库中密码的访问权限。

#### <a name="encryption-in-transit"></a>传输中加密
此基本加密可用于与 Azure Government 的连接，支持传输层安全 (TLS) 1.2 协议和 X.509 证书。 联邦信息处理标准 (FIPS) 140-2 级别 1 的加密算法还可用于 Azure Government 数据中心之间的基础结构网络连接。  Windows Server 2012 R2、Windows 8-plus 虚拟机和 Azure 文件共享可将 SMB 3.0 用于虚拟机和文件共享之间的加密。 在将数据传输到客户端应用程序中的存储之前，使用客户端加密对数据进行加密，并在从存储传出后对数据进行解密。

#### <a name="best-practices-for-encryption"></a>加密的最佳实践
* IaaS VM：使用 Azure 磁盘加密。 启用存储服务加密来加密在 Azure 存储空间中用于备份这些磁盘的 VHD 文件，但只会加密新写入的数据。 这意味着，如果创建 VM，然后对保存 VHD 文件的存储帐户启用存储服务加密，则只对更改内容进行加密，而不会加密原始 VHD 文件。
* 客户端加密：这是加密数据的最安全方法，因为它将在传输前加密数据，并加密静态数据。 但是，它需要使用存储将代码添加到应用程序，而你可能不想要这样做。 在这些情况下，可以针对传输中的数据使用 HTTPs，并使用存储服务加密来加密静态数据。 客户端加密还将在客户端上产生更多负载，这一点必须在扩展性计划中加以考虑，特别是要加密并传输大量数据时。

### <a name="protecting-customer-data-by-managing-secrets"></a>通过管理密钥来保护客户数据
安全的密钥管理对在云中保护数据必不可少。 客户应尽量简化密钥管理，并对云应用程序和服务用来加密数据的密钥保持控制。

#### <a name="best-practices-for-managing-secrets"></a>管理密匙的最佳实践
* 使用密钥保管库可在最大程度上减少密匙暴露在硬编码配置文件、脚本或源代码的风险。 Azure 密钥保管库加密密钥（如 Azure 磁盘加密的加密密钥）和机密（如密码）的方式是将它们存放在 FIPS 140-2 Level 2 已验证的硬件安全模块 (HSM) 中。 为了提升可靠性，可以在这些 HSM 中导入或生成密钥。
* 应用程序代码和模板应只包含机密的 URI 引用（这意味着实际机密不在代码、配置或源代码存储库中）。 这样可防止他人对内部或外部存储库（例如 GitHub 中的 harvest-bot）进行密钥钓鱼攻击。
* 利用密钥保管库中强大的 RBAC 控制。 如果受信任的操作人员离开公司或转到公司内的某个新团队，应阻止他们访问机密信息。

有关详细信息，请参阅 [Azure Key Vault public documentation](../key-vault/index.md)（Azure 密钥保管库公共文档）。

### <a name="isolation-to-restrict-data-access"></a>隔离以限制数据访问
隔离是有关使用边界、分段和容器来限制仅对授权用户、服务和应用程序的数据访问。 例如，租户之间的隔离是一种用于多租户云平台（如 Microsoft Azure）的基本安全机制。 逻辑隔离有助于防止一个租户干扰任何其他租户的操作。

#### <a name="environment-isolation"></a>环境隔离
Azure Government 环境是与 Microsoft 网络的其余部分隔离的物理实例。 这可以通过包含以下项的一系列物理和逻辑控件来实现：

* 使用生物识别设备和摄像头保护物理屏障。
* 对需要逻辑访问生产环境的 Microsoft 专业人员使用特定凭据和多重身份验证。
* Azure Government 的所有服务基础结构都位于美国境内。

#### <a name="per-customer-isolation"></a>按客户隔离
Azure 通过 VLAN 隔离、ACL、负载平衡器和 IP 筛选器来实施网络访问控制和隔离

客户可以在订阅、资源组、虚拟网络和子网之间进一步隔离其资源。

## <a name="screening"></a>筛查
我们最近宣布已获得了 FedRAMP 高基线和美国国防部 (DoD) 4 级影响认证。 这提高了 Azure 政府版环境中安全性和合规性的门槛。

现在，我们正按照美国国家机构法律和信用审核 (National Agency Check with Law and Credit (NACLC)) 对所有的运营商进行筛查，如 DoD 云计算安全要求指南 (SRG) 中的 5.6.2.2 部分所定义的：

> [!NOTE]
> 对基于“非关键敏感”（如 DoD 的 ADP-2）的 4 级 和 5 级信息具有访问权限的 CSP 人员所需进行的最低背景调查是美国国家机构法律和信用审核 (NACLC)（对于“非关键敏感”签约方）或适用于“中等风险”定位指示的中等风险背景调查 (MBI)。
> 
> 

下表总结了我们当前对 Azure 政府版运营商的筛查：

| Azure Gov 筛查和背景检查 | 说明 |
| --- | --- |
| 美国公民身份 |美国公民身份验证。 |
| Microsoft 云背景检查（每隔两年） |社会安全号码搜索、犯罪记录检查、海外资产控制办公室 (OFAC) 名单、美国商务部工业与安全局 (BIS) 名单、国防贸易管制办公室禁止人员名单。 |
| 美国国家机构法律和信用审核 (NACLC)（每隔五年） |基于 FBI 数据库的指纹背景检查。 有关其他信息，请转到 <a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/">Office Personnel Management 站点</a>。 |
| <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS">刑事司法信息服务 (CJIS) </a> |CJIS 是对可获得关键刑事司法信息 (CJI) 数据访问权限的人员进行处理指纹记录和验证犯罪记录的州、地方和 FBI 政府筛选。  每个州对可能访问 CJI 的所有员工进行其自己的背景检查和后续审批。 |

以下访问原则适用于 Azure 运营人员：

* 职责定义明确，能单独对请求、审批和部署更改负责。
* 通过具有特定功能的定义接口访问。
* 访问为实时 (JIT) 访问，仅根据每个事件或特定维护事件授予访问权限，并且该权限始终有一个有限期。
* 访问权限基于规则，具有仅分配有进行故障排除所需的权限的定义角色。

筛查标准包括在获得访问 Azure 政府版托管的系统前，验证所有 Microsoft 支持人员和运营人员的美国公民身份。 需要传输数据的支持人员使用 Azure 政府版中的安全功能。 安全数据传输需要一组单独的身份验证凭据来获取访问权限。 例如，若要访问系统元数据，运营人员可使用特定的基于 Web 的内部管理工具、只读 API 和 JIT 提升。

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客</a>。




<!--HONumber=Nov16_HO3-->


