---
title: 关于密钥 - Azure Key Vault
description: 概述了 Azure Key Vault REST 接口，并面向开发人员提供了有关密钥的详细信息。
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 29930a835297b0ddd3a91534dab9ccb6d74896e3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967559"
---
# <a name="about-keys"></a>关于密钥

Azure Key Vault 提供了两种类型的资源来存储和管理加密密钥：

|资源类型|密钥保护方法|数据平面终结点基 URL|
|--|--|--|
| **保管库** | 受软件保护<br/><br/>和<br/><br/>受 HSM 保护（含高级 SKU）</li></ul> | https://{vault-name}.vault.azure.net |
| **托管 HSM 池** | 受 HSM 保护 | https://{hsm-name}.managedhsm.azure.net |
||||

- **保管库** - 保管库提供低成本、易部署、多租户、区域复原（若可用）且高度可用的密钥管理解决方案，适用于最常见的云应用程序方案。
- **托管 HSM** - 托管 HSM 提供单租户、区域复原（若可用）且高度可用的 HSM 来存储和管理加密密钥。 非常适用于处理重要密钥的应用程序和使用方案。 还有助于满足最严格的安全性、合规性和法规要求。 

> [!NOTE]
> 除了加密密钥外，保管库还能让你存储和管理多种类型的对象（如机密、证书和存储帐户密钥）。

Key Vault 中的加密密钥表示为 JSON Web 密钥 [JWK] 对象。 JavaScript 对象表示法 (JSON) 和 JavaScript 对象签名和加密 (JOSE) 规范如下：

-   [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web 加密 (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web 算法 (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web 签名 (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

此外，还扩展了基本 JWK/JWA 规范，以启用对于 Azure Key Vault 和托管 HSM 实现独有的密钥类型。 

受 HSM 保护的密钥（也称为 HSM 密钥）在 HSM（硬件安全模块）中进行处理，并且始终保留 HSM 保护边界。 

- 保管库使用 FIPS 140-2 级别 2 验证的 HSM 来保护共享 HSM 后端基础结构中的 HSM 密钥。 
- 托管 HSM 池使用 FIPS 140-2 级别 3 验证的 HSM 模块来保护密钥。 每个 HSM 池都是独立的单租户实例，它有自己的[安全域](../managed-hsm/security-domain.md)，提供与共享同一硬件基础结构的其他所有 HSM 池之间的完全加密隔离。

这些密钥在单租户 HSM 池中受到保护。 可通过软形式或从受支持的 HSM 设备导出来导入 RSA、EC 和对称密钥。 也可在 HSM 池中生成密钥。 当通过 [BYOK（创建自己的密钥）规范](../keys/byok-specification.md)中所述的方法使用密钥导入 HSM 密钥时，可将密钥材料安全地传输到托管 HSM 池中。 

有关地理边界的详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-protection-methods-and-algorithms"></a>密钥类型、保护方法和算法

Key Vault 支持 RSA、EC 和对称密钥。 

### <a name="hsm-protected-keys"></a>HSM 保护的密钥

|密钥类型|保管库（仅适用于高级 SKU）|托管 HSM 池|
|--|--|--|--|
**EC-HSM**：椭圆曲线密钥|FIPS 140-2 级别 2 HSM|FIPS 140-2 级别 3 HSM
**RSA-HSM**：RSA 密钥|FIPS 140-2 级别 2 HSM|FIPS 140-2 级别 3 HSM
**oct-HSM**：对称|不支持|FIPS 140-2 级别 3 HSM
||||

### <a name="software-protected-keys"></a>受软件保护的密钥

|密钥类型|保管库|托管 HSM 池|
|--|--|--|--|
**RSA**：“受软件保护的”RSA 密钥|FIPS 140-2 级别 1|不支持
**EC**：“受软件保护的”椭圆曲线密钥|FIPS 140-2 级别 1|不支持
||||

### <a name="supported-algorithms"></a>支持的算法

|密钥类型/大小/曲线| 加密/解密<br>（包装/解包） | 签名/验证 | 
| --- | --- | --- |
|EC-P256、EC-P256K、EC-P384、EC-521|NA|ES256<br>ES256K<br>ES384<br>ES512|
|RSA 2K、3K、4K| RSA1_5<br>RSA-OAEP<br>RSA-OAEP-256|PS256<br>PS384<br>PS512<br>RS256<br>RS384<br>RS512<br>RSNULL| 
|AES 128 位、256 位| AES-KW<br>AES-GCM<br>AES-CBC| NA| 
|||

###  <a name="ec-algorithms"></a>EC 算法
 EC-HSM 密钥支持以下算法标识符

#### <a name="curve-types"></a>曲线类型

-   P-256 - NIST 曲线 P-256，在 [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) 中定义。
-   P-256K - SEC 曲线 SECP256K1，在 [SEC 2：建议使用的椭圆曲线域参数](https://www.secg.org/sec2-v2.pdf)中定义。
-   P-384 - NIST 曲线 P-384，在 [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) 中定义。
-   P-521 - NIST 曲线 P-521，在 [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) 中定义。

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - 使用曲线 P-256 创建的 SHA-256 摘要和密钥的 ECDSA。 [RFC7518](https://tools.ietf.org/html/rfc7518) 中描述了此算法。
-   **ES256K** - 使用曲线 P-256K 创建的 SHA-256 摘要和密钥的 ECDSA。 此算法正在等待标准化。
-   **ES384** - 使用曲线 P-384 创建的 SHA-384 摘要和密钥的 ECDSA。 [RFC7518](https://tools.ietf.org/html/rfc7518) 中描述了此算法。
-   **ES512** - 使用曲线 P-521 创建的 SHA-512 摘要和密钥的 ECDSA。 [RFC7518](https://tools.ietf.org/html/rfc7518) 中描述了此算法。

###  <a name="rsa-algorithms"></a>RSA 算法  
 RSA 和 RSA-HSM 密钥支持以下算法标识符  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>包装密钥/解包密钥、加密/解密

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] 密钥加密  
-   **RSA-OAEP** - RSAES 使用最优非对称加密填充 (OAEP) [RFC3447] 以及 A.2.1. 节中 RFC 3447 指定的默认参数。 这些默认参数使用 SHA-1 哈希函数和 SHA-1 附带的 MGF1 掩码生成函数。  
-  **RSA-OAEP-256** - RSAES 使用最优非对称加密填充，以及 SHA-256 哈希函数和 SHA-256 附带的 MGF1 掩码生成函数

#### <a name="signverify"></a>SIGN/VERIFY

-   **PS256** - 将 SHA-256 和 MGF1 与 SHA-256 配合使用的 RSASSA-PSS，如 [RFC7518](https://tools.ietf.org/html/rfc7518) 中所述。
-   **PS384** - 将 SHA-384 和 MGF1 与 SHA-384 配合使用的 RSASSA-PSS，如 [RFC7518](https://tools.ietf.org/html/rfc7518) 中所述。
-   **PS512** - 将 SHA-512 和 MGF1 与 SHA-512 配合使用的 RSASSA-PSS，如 [RFC7518](https://tools.ietf.org/html/rfc7518) 中所述。
-   **RS256** - RSASSA-PKCS-v1_5 使用 SHA-256。 必须使用 SHA-256 计算应用程序提供的摘要值，并且该值的长度必须为 32 字节。  
-   **RS384** - RSASSA-PKCS-v1_5 使用 SHA-384。 必须使用 SHA-384 计算应用程序提供的摘要值，并且该值的长度必须为 48 字节。  
-   **RS512** - RSASSA-PKCS-v1_5 使用 SHA-512。 必须使用 SHA-512 计算应用程序提供的摘要值，并且该值的长度必须为 64 字节。  
-   **RSNULL** - 请参阅 [RFC2437](https://tools.ietf.org/html/rfc2437)，这是一种用于实现某些 TLS 方案的特殊用例。  

###  <a name="symmetric-key-algorithms"></a>对称密钥算法
- **AES-KW** - AES 密钥包装 ([RFC3394](https://tools.ietf.org/html/rfc3394))。
- **AES-GCM** - Galois 计数器模式下的 AES 加密 ([NIST SP800-38d](https://csrc.nist.gov/publications/sp800))
- **AES-CBC** - 加密块链接模式下的 AES 加密 ([NIST SP800-38a](https://csrc.nist.gov/publications/sp800))

> [!NOTE] 
> 当前的 AES-GCM 实现及相应的 API 是实验性的。 在将来的迭代中，这些实现和 API 可能会发生重大更改。 

##  <a name="key-operations"></a>密钥操作

托管 HSM 支持对密钥对象执行以下操作：  

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

-   **签名并验证**：严格来讲，此操作应该为“签名哈希”或“验证哈希”，因为 Key Vault 不支持创建签名过程中的内容哈希。 应用程序应哈希要在本地签名的数据，然后请求 Key Vault 对哈希签名。 支持签名哈希的验证，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，应在本地执行 VERIFY 操作。  
-   **密钥加密/包装**：Key Vault 中存储的一个密钥可以用来保护另一个密钥，通常是对称内容加密密钥 (CEK)。 如果 Key Vault 中的密钥是非对称密钥，将使用密钥加密。 例如，RSA-OAEP 和 WRAPKEY/UNWRAPKEY 操作等同于 ENCRYPT/DECRYPT。 如果 Key Vault 中的密钥是对称密钥，则使用密钥包装。 例如，AES-KW。 支持 WRAPKEY 操作，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，WRAPKEY 操作应在本地执行。  
-   **加密和解密**：存储在 Key Vault 中的密钥可用于加密或解密单个数据块。 块大小取决于密钥类型和所选加密算法。 支持加密操作，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，ENCRYPT 操作应在本地执行。  

虽然使用非对称密钥的 WRAPKEY/UNWRAPKEY 可能看似多余（因为操作等同于 ENCRYPT/DECRYPT），但使用不同的操作却非常重要。 此不同提供了这些操作的语义和授权分离，并在服务支持其他密钥类型时提供一致性。  

Key Vault 不支持“导出”操作。 在系统中设置密钥后，便无法提取该密钥，也无法修改其密钥材料。 但是，Key Vault 的用户可能需要将密钥用于其他用例，例如删除密钥后。 在这种情况下，可以使用“备份”和“还原”操作以受保护的形式导出/导入密钥。 “备份”操作创建的密钥无法在 Key Vault 外部使用。 或者，可能会对多个 Key Vault 实例使用“导入”操作。  

用户可以使用 JWK 对象的 key_ops 属性按密钥限制 Key Vault 支持的任何加密操作。  

有关 JWK 对象的详细信息，请参阅 [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)。  

## <a name="key-attributes"></a>密钥属性

除密钥材料外，还可以指定以下属性。 在 JSON 请求中，即使未指定任何属性，也需要属性关键字和大括号“{”“}”。  

- enabled：布尔型，可选，默认值为 true。 指定密钥是否已启用并可用于加密操作。 enabled 属性结合 nbf 和 exp 使用  。如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作  。 nbf / exp 时段外的操作会自动禁止，[特定条件](#date-time-controlled-operations)下的某些操作类型除外 。
- *nbf*：IntDate，可选，默认值为“now”。 nbf（非过去）属性识别密钥不得用于加密操作以前的时间，[特定条件](#date-time-controlled-operations)下的某些操作类型除外。 处理 nbf 属性要求当前日期/时间必须晚于或等于 nbf 属性中列出的非过去日期/时间 。 Key Vault 可能会稍微留有一些余地（通常不超过几分钟），以适应时钟偏差。 其值必须是包含 IntDate 值的数字。  
- *exp*：IntDate，可选，默认值为“forever”。 exp（过期时间）属性识别密钥不得用于加密操作当时或之后的过期时间，[特定条件](#date-time-controlled-operations)下的某些操作类型除外。 处理 exp 属性要求当前日期/时间必须早于 exp 属性中列出的过期日期/时间 。 Key Vault 可能会稍微留有一些余地（通常不超过几分钟），以适应时钟偏差。 其值必须是包含 IntDate 值的数字。  

在包含密钥属性的任何响应中还包括以下其他只读属性：  

- *created*：IntDate，可选。 created 属性指示创建此版本的密钥的时间。 如果密钥在添加此属性之前创建，此值为 NULL。 其值必须是包含 IntDate 值的数字。  
- *updated*：IntDate，可选。 updated 属性指示更新此版本的密钥的时间。 如果密钥上次更新的时间早于添加此属性的时间，此值为 NULL。 其值必须是包含 IntDate 值的数字。  

有关 IntDate 和其他数据类型的详细信息，请参阅[关于密钥、机密和证书：[数据类型](../general/about-keys-secrets-certificates.md#data-types)。

### <a name="date-time-controlled-operations"></a>日期时间控制的操作

这些在 nbf / exp 时段外的尚未生效的密钥和过期密钥适合 decrypt、unwrap 和 verify 操作（不会返回“403 禁止访问”）   。 使用尚未生效状态的基本原理是允许在投入生产前测试密钥。 使用过期状态的基本原理是允许对秘钥有效期间创建的数据执行恢复操作。 此外，使用 Key Vault 策略，或通过将 enabled 密钥属性更新为 false 可以禁用访问密钥。

有关数据类型的详细信息，请参阅[数据类型](../general/about-keys-secrets-certificates.md#data-types)。

有关其他可能的属性的详细信息，请参阅 [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)。

## <a name="key-tags"></a>密钥标记

可以用标记的形式指定其他特定于应用程序的元数据。 Key Vault 支持多达 15 种标记，每种标记可以有 256 个字符的名称和 256 个字符的值。  

> [!NOTE] 
> 调用方如果具有该密钥的 list 或 get 权限，则可读取标记 。

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
- [关于托管 HSM](../managed-hsm/overview.md)
- [关于机密](../secrets/about-secrets.md)
- [关于证书](../certificates/about-certificates.md)
- [Key Vault REST API 概述](../general/about-keys-secrets-certificates.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)