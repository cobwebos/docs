---
title: 关于 Azure Key Vault 密钥 - Azure Key Vault
description: 概述了 Azure Key Vault REST 接口，并面向开发人员提供了有关密钥的详细信息。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: f96ec80b529c594a383be8d668fd28b77372cd80
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900922"
---
# <a name="about-azure-key-vault-keys"></a>关于 Azure Key Vault 密钥

Azure Key Vault 支持多种密钥类型和算法，可以对高价值密钥使用硬件安全模块 (HSM)。

Key Vault 中的加密密钥表示为 JSON Web 密钥 [JWK] 对象。 JavaScript 对象表示法 (JSON) 和 JavaScript 对象签名和加密 (JOSE) 规范如下：

-   [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web 加密 (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web 算法 (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web 签名 (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

此外，还扩展了基本 JWK/JWA 规范，以启用对于 Key Vault 实现唯一的密钥类型。 例如，使用 HSM 供应商特定的包导入密钥，可以安全传输仅可在 Key Vault HSM 中使用的密钥。 

Azure Key Vault 同时支持软密钥和硬密钥：

- **“软”密钥**：由 Key Vault 在软件中处理，但使用 HSM 中的系统密钥进行静态加密的一种密钥。 客户端可以导入现有 RSA 或 EC（椭圆曲线）密钥，也可以请求 Key Vault 生成该密钥。
- **“硬”密钥**：在 HSM（硬件安全模块）中处理的密钥。 这些密钥在一个 Key Vault HSM 安全体系中受到保护（按地理位置设置安全体系，以保持隔离）。 客户端可以采用软性形式或通过从兼容 HSM 设备导出的方式来导入 RSA 或 EC 密钥。 此外，客户端还可以请求 Key Vault 生成该密钥。 此密钥类型将 key_hsm 属性添加到获得的 JWK 以携带 HSM 密钥材料。

有关地理边界的详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/privacy/)  

## <a name="cryptographic-protection"></a>加密保护

Key Vault 仅支持 RSA 和椭圆曲线密钥。 

-   **EC**：“软”椭圆曲线密钥。
-   **EC-HSM**：“硬”椭圆曲线密钥。
-   **RSA**：“软”RSA 密钥。
-   **RSA-HSM**：“硬”RSA 密钥。

Key Vault 支持大小为 2048、3072 和 4096 的 RSA 密钥。 Key Vault 支持类型为 P-256、P-384、P-521 和 P-256K (SECP256K1) 的椭圆曲线密钥。

Key Vault 使用的加密模块（HSM 或软件）经过 FIPS（美国联邦信息处理标准）验证。 不需执行任何特殊操作便可在 FIPS 模式下运行。 “创建”或“导入”为受 HSM 保护的密钥在 HSM 内处理，且验证为 FIPS 140-2 级别 2   。 “创建”或“导入”为受软件保护的密钥在加密模块内处理，且验证为 FIPS 140-2 级别 1   。

###  <a name="ec-algorithms"></a>EC 算法
 Key Vault 中的 EC 和 EC-HSM 密钥支持以下算法标识符。 

#### <a name="curve-types"></a>曲线类型

-   P-256  - NIST 曲线 P-256，在 [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) 中定义。
-   P-256K  - SEC 曲线 SECP256K1，在 [SEC 2：建议使用的椭圆曲线域参数](https://www.secg.org/sec2-v2.pdf)中定义。
-   P-384  - NIST 曲线 P-384，在 [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) 中定义。
-   P-521  - NIST 曲线 P-521，在 [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) 中定义。

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - 使用曲线 P-256 创建的 SHA-256 摘要和密钥的 ECDSA。 [RFC7518](https://tools.ietf.org/html/rfc7518) 中描述了此算法。
-   **ES256K** - 使用曲线 P-256K 创建的 SHA-256 摘要和密钥的 ECDSA。 此算法正在等待标准化。
-   **ES384** - 使用曲线 P-384 创建的 SHA-384 摘要和密钥的 ECDSA。 [RFC7518](https://tools.ietf.org/html/rfc7518) 中描述了此算法。
-   **ES512** - 使用曲线 P-521 创建的 SHA-512 摘要和密钥的 ECDSA。 [RFC7518](https://tools.ietf.org/html/rfc7518) 中描述了此算法。

###  <a name="rsa-algorithms"></a>RSA 算法  
 Key Vault 中的 RSA 和 RSA-HSM 密钥支持以下算法标识符。  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>包装密钥/解包密钥、加密/解密

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] 密钥加密  
-   **RSA-OAEP** - RSAES 使用最优非对称加密填充 (OAEP) [RFC3447] 以及 A.2.1. 节中 RFC 3447 指定的默认参数。 这些默认参数使用 SHA-1 哈希函数和 SHA-1 附带的 MGF1 掩码生成函数。  

#### <a name="signverify"></a>SIGN/VERIFY

-   **PS256** - 将 SHA-256 和 MGF1 与 SHA-256 配合使用的 RSASSA-PSS，如 [RFC7518](https://tools.ietf.org/html/rfc7518) 中所述。
-   **PS384** - 将 SHA-384 和 MGF1 与 SHA-384 配合使用的 RSASSA-PSS，如 [RFC7518](https://tools.ietf.org/html/rfc7518) 中所述。
-   **PS512** - 将 SHA-512 和 MGF1 与 SHA-512 配合使用的 RSASSA-PSS，如 [RFC7518](https://tools.ietf.org/html/rfc7518) 中所述。
-   **RS256** - RSASSA-PKCS-v1_5 使用 SHA-256。 必须使用 SHA-256 计算应用程序提供的摘要值，并且该值的长度必须为 32 字节。  
-   **RS384** - RSASSA-PKCS-v1_5 使用 SHA-384。 必须使用 SHA-384 计算应用程序提供的摘要值，并且该值的长度必须为 48 字节。  
-   **RS512** - RSASSA-PKCS-v1_5 使用 SHA-512。 必须使用 SHA-512 计算应用程序提供的摘要值，并且该值的长度必须为 64 字节。  
-   **RSNULL** - 请参阅一种用于实现某种 TLS 方案的特殊用例 [RFC2437]。  

##  <a name="key-operations"></a>密钥操作

Key Vault 支持对密钥对象执行以下操作：  

-   **创建**：允许客户端在 Key Vault 中创建密钥。 密钥的值由 Key Vault 生成，存储但不发布到客户端。 可在 Key Vault 中创建非对称密钥。  
-   **导入**：允许客户端将现有密钥导入到 Key Vault。 非对称密钥可以使用 JWK 构造中的多种不同的打包方法导入到 Key Vault。 
-   **更新**：允许具有足够权限的客户端修改与以前存储在 Key Vault 中的密钥相关联的元数据（密钥属性）。  
-   **删除**：允许具有足够权限的客户端删除 Key Vault 中的密钥。  
-   **列出**：允许客户端列出给定 Key Vault 中的所有项。  
-   **列出版本**：允许客户端列出给定 Key Vault 中的给定密钥的所有版本。  
-   **获取**：允许客户端检索 Key Vault 中的给定密钥的公共部分。  
-   **备份**：导出受保护窗体中的密钥。  
-   **还原**：导入以前备份的密钥。  

有关详细信息，请参阅 [Key Vault REST API 中的密钥操作参考](/rest/api/keyvault)。  

在 Key Vault 中创建密钥后，即可使用密钥执行以下加密操作：  

-   **签名并验证**：严格来讲，此操作应该为“签名哈希”或“验证哈希”，因为 Key Vault 不支持创建签名过程中的内容哈希。 应用程序应哈希要在本地签名的数据，然后请求 Key Vault 对哈希签名。 支持签名哈希的验证，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，VERIFY 操作应在本地执行。  
-   **密钥加密/包装**：Key Vault 中存储的一个密钥可以用来保护另一个密钥，通常是对称内容加密密钥 (CEK)。 如果 Key Vault 中的密钥是非对称密钥，将使用密钥加密。 例如，RSA-OAEP 和 WRAPKEY/UNWRAPKEY 操作等同于 ENCRYPT/DECRYPT。 如果 Key Vault 中的密钥是对称密钥，则使用密钥包装。 例如，AES-KW。 支持 WRAPKEY 操作，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，WRAPKEY 操作应在本地执行。  
-   **加密和解密**：存储在 Key Vault 中的密钥可用于加密或解密单个数据块。 块大小取决于密钥类型和所选加密算法。 支持加密操作，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，ENCRYPT 操作应在本地执行。  

虽然使用非对称密钥的 WRAPKEY/UNWRAPKEY 可能看似多余（因为操作等同于 ENCRYPT/DECRYPT），但使用不同的操作却非常重要。 此不同提供了这些操作的语义和授权分离，并在服务支持其他密钥类型时提供一致性。  

Key Vault 不支持“导出”操作。 在系统中设置密钥后，便无法提取该密钥，也无法修改其密钥材料。 但是，Key Vault 的用户可能需要将密钥用于其他用例，例如删除密钥后。 在这种情况下，可以使用“备份”和“还原”操作以受保护的形式导出/导入密钥。 “备份”操作创建的密钥无法在 Key Vault 外部使用。 或者，可能会对多个 Key Vault 实例使用“导入”操作。  

用户可以使用 JWK 对象的 key_ops 属性按密钥限制 Key Vault 支持的任何加密操作。  

有关 JWK 对象的详细信息，请参阅 [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)。  

## <a name="key-attributes"></a>密钥属性

除密钥材料外，还可以指定以下属性。 在 JSON 请求中，即使未指定任何属性，也需要属性关键字和大括号“{”“}”。  

- enabled：布尔型，可选，默认值为 true   。 指定密钥是否已启用并可用于加密操作。 enabled 属性结合 nbf 和 exp 使用    。如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作     。 nbf / exp 时段外的操作会自动禁止，[特定条件](#date-time-controlled-operations)下的某些操作类型除外   。
- *nbf*：IntDate，可选，默认值为“now”。 nbf（非过去）属性识别密钥不得用于加密操作以前的时间，[特定条件](#date-time-controlled-operations)下的某些操作类型除外  。 处理 nbf 属性要求当前日期/时间必须晚于或等于 nbf 属性中列出的非过去日期/时间   。 Key Vault 可能会稍微留有一些余地（通常不超过几分钟），以适应时钟偏差。 其值必须是包含 IntDate 值的数字。  
- *exp*：IntDate，可选，默认值为“forever”。 exp（过期时间）属性识别密钥不得用于加密操作当时或之后的过期时间，[特定条件](#date-time-controlled-operations)下的某些操作类型除外  。 处理 exp 属性要求当前日期/时间必须早于 exp 属性中列出的过期日期/时间   。 Key Vault 可能会稍微留有一些余地（通常不超过几分钟），以适应时钟偏差。 其值必须是包含 IntDate 值的数字。  

在包含密钥属性的任何响应中还包括以下其他只读属性：  

- *created*：IntDate，可选。 created 属性指示创建此版本的密钥的时间  。 如果密钥在添加此属性之前创建，此值为 NULL。 其值必须是包含 IntDate 值的数字。  
- *updated*：IntDate，可选。 updated 属性指示更新此版本的密钥的时间  。 如果密钥上次更新的时间早于添加此属性的时间，此值为 NULL。 其值必须是包含 IntDate 值的数字。  

有关 IntDate 和其他数据类型的详细信息，请参阅[关于密钥、机密和证书：[数据类型](../general/about-keys-secrets-certificates.md#data-types)。

### <a name="date-time-controlled-operations"></a>日期时间控制的操作

这些在 nbf / exp 时段外的尚未生效的密钥和过期密钥适合 decrypt、unwrap 和 verify 操作（不会返回“403 禁止访问”）      。 使用尚未生效状态的基本原理是允许在投入生产前测试密钥。 使用过期状态的基本原理是允许对秘钥有效期间创建的数据执行恢复操作。 此外，使用 Key Vault 策略，或通过将 enabled 密钥属性更新为 false 可以禁用访问密钥   。

有关数据类型的详细信息，请参阅[数据类型](../general/about-keys-secrets-certificates.md#data-types)。

有关其他可能的属性的详细信息，请参阅 [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)。

## <a name="key-tags"></a>密钥标记

可以用标记的形式指定其他特定于应用程序的元数据。 Key Vault 支持多达 15 种标记，每种标记可以有 256 个字符的名称和 256 个字符的值。  

>[!Note]
>如果调用方具有该对象类型（密钥、机密或证书）的列出或获取权限，则调用方可读取标记   。

##  <a name="key-access-control"></a>密钥访问控制

Key Vault 托管的密钥的访问控制是在充当密钥容器的 Key Vault 级别提供的。 在同一密钥保管库中，密钥的访问控制策略不同于机密的访问控制策略。 用户可以创建一个或多个保管库来保存密钥，并且需要维护方案相应的密钥分段和管理。 密钥的访问控制与机密的访问控制无关。  

在保管库上的密钥访问控制条目中可以按用户/服务主体授予以下权限。 这些权限对密钥对象上允许的操作采取严密的镜像操作。  向密钥保管库中的服务主体授予访问权限是一项一次性操作，并且对所有 Azure 订阅保持不变。 可以使用它来部署所需数量的证书。 

- 针对密钥管理操作的权限
  - *get*：读取密钥的公共部分及其属性
  - *list*：列出密钥保管库中存储的密钥或密钥版本
  - *update*：更新键的属性
  - *create*：新建密钥
  - *import*：将密钥导入到密钥保管库
  - *delete*：删除密钥对象
  - *recover*：恢复已删除的密钥
  - *backup*：备份密钥保管库中的密钥
  - *restore*：将备份密钥还原到密钥保管库

- 针对加密操作的权限
  - *decrypt*：使用密钥取消保护字节序列
  - *encrypt*：使用密钥保护任意字节序列
  - *unwrapKey*：使用密钥取消保护包装的对称密钥
  - *wrapKey*：使用密钥保护对称密钥
  - *verify*：使用密钥验证摘要  
  - *sign*：使用密钥签名摘要
    
- 针对特权操作的权限
  - *purge*：清除（永久删除）已删除的密钥

有关使用密钥的详细信息，请参阅 [Key Vault REST API 中的密钥操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。 

## <a name="next-steps"></a>后续步骤

- [关于 Key Vault](../general/overview.md)
- [关于键、密钥和证书](../general/about-keys-secrets-certificates.md)
- [关于机密](../secrets/about-secrets.md)
- [关于证书](../certificates/about-certificates.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
