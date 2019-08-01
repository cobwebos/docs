---
title: Azure 澳大利亚的数据安全
description: 在澳大利亚地区配置 Azure, 以满足澳大利亚政府政策、法规和法规的特定要求。
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608349"
---
# <a name="data-security-in-azure-australia"></a>Azure 澳大利亚的数据安全

用于保护客户数据安全的首要原则包括：

* 使用加密保护数据
* 管理机密
* 限制数据访问

## <a name="encrypting-your-data"></a>加密数据

数据加密可以应用于磁盘级别 (静态)、数据库中的数据库 (静态和传输中)、应用程序中的数据加密 (传输中) 以及网络上的数据 (传输内)。 可以通过多种方式在 Azure 中实现加密:

|服务/功能|描述|
|---|---|
|存储服务加密|Azure 存储服务加密可在存储帐户级别启用，使块 Blob 和页 Blob 可以在写入 Azure 存储时自动加密。 从 Azure 存储读取数据时，存储服务会在返回数据之前将其解密。 使用 SSE 来保护数据, 无需修改代码或将代码添加到任何应用程序。|
|Azure 磁盘加密|使用 Azure 磁盘加密对 Azure 虚拟机使用的 OS 磁盘和数据磁盘进行加密。 与 Azure Key Vault 的集成使你可以控制和帮助管理磁盘加密密钥。|
|客户端应用程序级别的加密|客户端加密内置于 Java 和 .NET 存储客户端库, 这些库可利用 Azure Key Vault Api, 使其易于实现。 使用 Azure Key Vault 获取对使用 Azure Active Directory 的特定人员 Azure Key Vault 的机密。|
|传输中加密|可用于与 Azure 澳大利亚连接的基本加密支持传输层安全 (TLS) 1.2 协议和 x.509 证书。 美国联邦信息处理标准 (FIPS) 140-2 级别1加密算法还用于 Azure 澳大利亚数据中心之间的基础结构网络连接。  Windows Server 2016、Windows 10、Windows Server 2012 R2、Windows 8.1 和 Azure 文件共享可以使用 SMB 3.0 对 VM 和文件共享之间的加密进行加密。 在将数据传输到客户端应用程序中的存储之前, 使用客户端加密对数据进行加密, 并在将数据传输到存储后对其进行解密。|
|IaaS Vm|使用 Azure 磁盘加密。 启用存储服务加密来加密在 Azure 存储中用于备份这些磁盘的 VHD 文件，但只会加密新写入的数据。 这意味着，如果创建 VM，并对保存 VHD 文件的存储帐户启用存储服务加密，则只对更改内容进行加密，而不会加密原始 VHD 文件。|
|客户端加密|这是加密数据的最安全方法, 因为它会在传输前加密数据, 并加密静态数据。 但是，它需要使用存储将代码添加到应用程序，而你可能不想要这样做。 在这些情况下, 你可以在传输中为数据使用 HTTPS, 并存储服务加密加密静态数据。 客户端加密还包括客户端上的更多负载, 必须在缩放性计划中考虑到这一点, 尤其是在对大量数据进行加密和传输时。|
|

有关 Azure 中的加密选项的详细信息, 请参阅[存储安全指南](https://docs.microsoft.com/azure/storage/storage-security-guide)。

## <a name="protecting-data-by-managing-secrets"></a>通过管理机密来保护数据

安全的密钥管理对在云中保护数据必不可少。 客户应尽量简化密钥管理，并对云应用程序和服务用来加密数据的密钥保持控制。

### <a name="managing-secrets"></a>管理机密

* 使用密钥保管库可在最大程度上减少密匙暴露在硬编码配置文件、脚本或源代码的风险。 Azure 密钥保管库加密密钥（如 Azure 磁盘加密的加密密钥）和机密（如密码）的方式是将它们存放在 FIPS 140-2 Level 2 已验证的硬件安全模块 (HSM) 中。 为了提升可靠性，可以在这些 HSM 中导入或生成密钥。
* 应用程序代码和模板应该只包含对机密的 URI 引用 (这意味着实际机密不在代码、配置或源代码存储库中)。 这样可防止他人对内部或外部存储库（例如 GitHub 中的 harvest-bot）进行密钥钓鱼攻击。
* 利用密钥保管库中强大的 RBAC 控制。 如果受信任的操作人员离开公司或转到公司内的某个新团队，应阻止他们访问机密信息。  

有关详细信息, 请参阅[Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>隔离以限制数据访问

隔离是有关使用边界、分段和容器来限制仅对授权用户、服务和应用程序的数据访问。 例如, 租户之间的隔离是多租户云平台 (如 Microsoft Azure) 的基本安全机制。 逻辑隔离有助于防止一个租户干扰任何其他租户的操作。

#### <a name="per-customer-isolation"></a>每客户隔离

Azure 通过第2层 VLAN 隔离、访问控制列表、负载均衡器和 IP 筛选器来实施网络访问控制和隔离。

客户可以在订阅、资源组、虚拟网络和子网之间进一步隔离其资源。

有关 Microsoft Azure 中的隔离的详细信息, 请参阅[Azure 公有云中的隔离](../security/fundamentals/isolation-choices.md)。

## <a name="next-steps"></a>后续步骤

查看[AZURE VPN 网关上](vpn-gateway.md)的文章