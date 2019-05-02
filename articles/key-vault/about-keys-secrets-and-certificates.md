---
title: 关于 Azure 密钥保管库密钥、机密和证书 - Azure 密钥保管库
description: Azure Key Vault REST 接口概述以及密钥、机密和证书的开发人员详细信息。
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: b50f98c4abaeda3ac1805b73aa18fe6c29596426
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708689"
---
# <a name="about-keys-secrets-and-certificates"></a>关于密钥、机密和证书

凭借 Azure Key Vault，Microsoft Azure 应用程序和用户能够存储和使用多种类型的机密/密钥数据：

- 加密密钥：支持多种密钥类型和算法，可以对高价值的密钥使用硬件安全模块 (HSM)。 
- 机密：提供机密（例如密码和数据库连接字符串）的安全存储。
- 证书：支持基于密钥和机密并且添加了自动续订功能的证书。
- Azure 存储：可以管理 Azure 存储帐户的密钥。 在内部，Key Vault 可以使用 Azure 存储帐户列出（同步）密钥，并定期重新生成（轮换）密钥。 

有关 Key Vault 的更多常规信息，请参阅[什么是 Azure Key Vault？](/azure/key-vault/key-vault-whatis)

## <a name="azure-key-vault"></a>Azure 密钥保管库

以下部分提供在实现 Key Vault 服务中可以用到的常规信息。

### <a name="supporting-standards"></a>支持标准

JavaScript 对象表示法 (JSON) 与 JavaScript 对象的签名和加密 (JOSE) 规范是重要的背景信息。  

-   [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web 加密 (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web 算法 (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web 签名 (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature)  

### <a name="data-types"></a>数据类型

请参阅 JOSE 规范，了解密钥、加密和签名的相关数据类型。  

-   **algorithm** - 支持的密钥操作算法，例如 RSA1_5  
-   **ciphertext-value** - 密码文本八位组，使用 Base64URL 编码  
-   **digest-value** - 哈希算法的输出，使用 Base64URL 编码  
-   **key-type** - 一种支持的密钥类型，例如 RSA (Rivest-Shamir-Adleman)。  
-   **plaintext-value** - 纯文本位组，使用 Base64URL 编码  
-   **signature-value** - 签名算法的输出，使用 Base64URL 编码  
-   **base64URL** - Base64URL [RFC4648] 编码的二进制值  
-   **boolean** - 要么为 true，要么为 false  
-   **Identity** - Azure Active Directory (AAD) 的标识。  
-   **IntDate** - 一个 JSON 十进制值，表示从 1970-01-01T0:0:0Z UTC 到指定 UTC 日期/时间的秒数。 请参阅 RFC3339，了解有关日期/时间的常规信息和 UTC 的特别信息。  

### <a name="objects-identifiers-and-versioning"></a>对象、标识符和版本控制

对于存储在 Key Vault 中的对象，在创建了某一对象的新实例后，这些对象就会受到版本控制。 每个版本都分配有唯一标识符和 URL。 首次创建一个对象时，该对象被赋予了一个唯一的版本标识符，并标记为当前版本的对象。 创建与对象同名的新实例会向新对象赋予一个唯一的版本标识符，并使其成为当前版本。  

Key Vault 中的对象可以使用当前标识符或特定于版本的标识符进行寻址。 例如，给定一个名称为 `MasterKey` 的密钥，使用当前标识符执行操作会导致系统使用最新的可用版本。 使用特定于版本的标识符执行操作会导致系统使用该特定版本的对象。  

Key Vault 中的对象通过 URL 唯一标识。 不管地理位置如何，系统中都不存在两个具有相同 URL 的对象。 对象的完整 URL 称为对象标识符。 URL 由标识 Key Vault 的前缀、对象类型、用户提供的对象名称和对象版本组成。 对象名称不区分大小写且不可变。 不包括对象版本的标识符称为基本标识符。  

有关详细信息，请参阅[身份验证、请求和响应](authentication-requests-and-responses.md)

对象标识符具有以下常规格式：  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

其中：  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault 服务中的保管库名称。<br /><br /> Key Vault 名称由用户选择，并且全局唯一。<br /><br /> Key Vault 的名称必须是 3-24 个字符，且仅包含 0-9、a-z、A-Z 和 - 的字符串。|  
|`object-type`|对象的类型，要么为“密钥”，要么为“机密”。|  
|`object-name`|`object-name` 是用户提供名称，在 Key Vault 中必须保持唯一。 该名称必须是 1-127 个字符，且仅包含 0-9、a-z、A-Z 和 - 的字符串。|  
|`object-version`|`object-version` 是系统生成的 32 个字符的字符串标识符，可以选择用来对某个对象的唯一版本进行寻址。|  

## <a name="key-vault-keys"></a>Key Vault 密钥

### <a name="keys-and-key-types"></a>密钥和密钥类型

Key Vault 中的加密密钥表示为 JSON Web 密钥 [JWK] 对象。 此外，还扩展了基本 JWK/JWA 规范，以启用对于 Key Vault 实现唯一的密钥类型。 例如，使用 HSM 供应商特定的包导入密钥，可以安全传输仅可在 Key Vault HSM 中使用的密钥。  

- **“软”密钥**：由 Key Vault 在软件中处理，但使用 HSM 中的系统密钥进行静态加密的一种密钥。 客户端可以导入现有 RSA 或 EC（椭圆曲线）密钥，也可以请求 Key Vault 生成该密钥。
- **“硬”密钥**：在 HSM（硬件安全模块）中处理的密钥。 这些密钥在一个 Key Vault HSM 安全体系中受到保护（按地理位置设置安全体系，以保持隔离）。 客户端可以采用软性形式或通过从兼容 HSM 设备导出的方式来导入 RSA 或 EC 密钥。 此外，客户端还可以请求 Key Vault 生成该密钥。 此密钥类型可以将 T 属性添加到获得的 JWK 以携带 HSM 密钥材料。

     有关地理边界的详细信息，请参阅 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/privacy/)  

Key Vault 仅支持 RSA 和椭圆曲线密钥。 

-   **EC**：“软”椭圆曲线密钥。
-   **EC-HSM**：“硬”椭圆曲线密钥。
-   **RSA**：“软”RSA 密钥。
-   **RSA-HSM**：“硬”RSA 密钥。

Key Vault 支持大小为 2048、3072 和 4096 的 RSA 密钥。 Key Vault 支持类型为 P-256、P-384、P-521 和 P-256K (SECP256K1) 的椭圆曲线密钥。

### <a name="cryptographic-protection"></a>加密保护

Key Vault 使用的加密模块（HSM 或软件）经过 FIPS（美国联邦信息处理标准）验证。 因此不必执行任何特殊操作便可在 FIPS 模式下运行。 “创建”或“导入”为受 HSM 保护的密钥在 HSM 内处理，且验证为 FIPS 140-2 级别 2。 “创建”或“导入”为受软件保护的密钥在加密模块内处理，且验证为 FIPS 140-2 级别 1。 有关详细信息，请参阅[密钥和密钥类型](#keys-and-key-types)。

###  <a name="ec-algorithms"></a>EC 算法
 Key Vault 中的 EC 和 EC-HSM 密钥支持以下算法标识符。 

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
 Key Vault 中的 RSA 和 RSA-HSM 密钥支持以下算法标识符。  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>包装密钥/解包密钥、加密/解密

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] 密钥加密  
-   **RSA-OAEP** - RSAES 使用最优非对称加密填充 (OAEP) [RFC3447] 以及 A.2.1. 节中 RFC 3447 指定的默认参数。 这些默认参数使用 SHA-1 哈希函数和 SHA-1 附带的 MGF1 掩码生成函数。  

#### <a name="signverify"></a>SIGN/VERIFY

-   **RS256** - RSASSA-PKCS-v1_5 使用 SHA-256。 必须使用 SHA-256 计算应用程序提供的摘要值，并且该值的长度必须为 32 字节。  
-   **RS384** - RSASSA-PKCS-v1_5 使用 SHA-384。 必须使用 SHA-384 计算应用程序提供的摘要值，并且该值的长度必须为 48 字节。  
-   **RS512** - RSASSA-PKCS-v1_5 使用 SHA-512。 必须使用 SHA-512 计算应用程序提供的摘要值，并且该值的长度必须为 64 字节。  
-   **RSNULL** - 请参阅一种用于实现某种 TLS 方案的特殊用例 [RFC2437]。  

###  <a name="key-operations"></a>密钥操作

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

-   **签名并验证**：严格来讲，此操作应该为“签名哈希”或“验证哈希”，因为 Key Vault 不支持创建签名过程中的内容哈希。 应用程序应哈希要在本地签名的数据，然后请求 Key Vault 对哈希签名。 支持签名哈希的验证，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，请验证操作在本地执行。  
-   **密钥加密/包装**：Key Vault 中存储的一个密钥可以用来保护另一个密钥，通常是对称内容加密密钥 (CEK)。 如果 Key Vault 中的密钥是非对称密钥，将使用密钥加密。 例如，RSA-OAEP 和 WRAPKEY/UNWRAPKEY 操作等同于 ENCRYPT/DECRYPT。 如果 Key Vault 中的密钥是对称密钥，则使用密钥包装。 例如，AES-KW。 支持 WRAPKEY 操作，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，WRAPKEY 操作应在本地执行。  
-   **加密和解密**：存储在 Key Vault 中的密钥可用于加密或解密单个数据块。 块大小取决于密钥类型和所选加密算法。 支持加密操作，作为可能无法访问 [公共] 密钥材料的应用程序的一种便捷操作。 为获得最佳应用程序性能，加密操作应在本地执行。  

虽然使用非对称密钥的 WRAPKEY/UNWRAPKEY 可能看似多余（因为操作等同于 ENCRYPT/DECRYPT），但使用不同的操作却非常重要。 此不同提供了这些操作的语义和授权分离，并在服务支持其他密钥类型时提供一致性。  

Key Vault 不支持“导出”操作。 在系统中设置密钥后，便无法提取该密钥，也无法修改其密钥材料。 但是，Key Vault 的用户可能需要将密钥用于其他用例，例如删除密钥后。 在这种情况下，可以使用“备份”和“还原”操作以受保护的形式导出/导入密钥。 “备份”操作创建的密钥无法在 Key Vault 外部使用。 或者，可能会对多个 Key Vault 实例使用“导入”操作。  

用户可以使用 JWK 对象的 key_ops 属性按密钥限制 Key Vault 支持的任何加密操作。  

有关 JWK 对象的详细信息，请参阅 [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)。  

###  <a name="key-attributes"></a>密钥属性

除密钥材料外，还可以指定以下属性。 在 JSON 请求中，即使未指定任何属性，也需要属性关键字和大括号“{”“}”。  

- enabled：布尔型，可选，默认值为 true。 指定密钥是否已启用并可用于加密操作。 enabled 属性结合 nbf 和 exp 使用。如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作。 nbf / exp 时段外的操作会自动禁止，[特定条件](#date-time-controlled-operations)下的某些操作类型除外。
- *nbf*：IntDate，可选，默认值为“now”。 nbf（非过去）属性识别密钥不得用于加密操作以前的时间，[特定条件](#date-time-controlled-operations)下的某些操作类型除外。 处理 nbf 属性要求当前日期/时间必须晚于或等于 nbf 属性中列出的非过去日期/时间。 Key Vault 可能会稍微留有一些余地（通常不超过几分钟），以适应时钟偏差。 其值必须是包含 IntDate 值的数字。  
- *exp*：IntDate，可选，默认值为“forever”。 exp（过期时间）属性识别密钥不得用于加密操作当时或之后的过期时间，[特定条件](#date-time-controlled-operations)下的某些操作类型除外。 处理 exp 属性要求当前日期/时间必须早于 exp 属性中列出的过期日期/时间。 Key Vault 可能会稍微留有一些余地（通常不超过几分钟），以适应时钟偏差。 其值必须是包含 IntDate 值的数字。  

在包含密钥属性的任何响应中还包括以下其他只读属性：  

- *created*：IntDate，可选。 created 属性指示创建此版本的密钥的时间。 如果密钥在添加此属性之前创建，此值为 NULL。 其值必须是包含 IntDate 值的数字。  
- *updated*：IntDate，可选。 updated 属性指示更新此版本的密钥的时间。 如果密钥上次更新的时间早于添加此属性的时间，此值为 NULL。 其值必须是包含 IntDate 值的数字。  

有关 IntDate 和其他数据类型的详细信息，请参阅[数据类型](#data-types)  

#### <a name="date-time-controlled-operations"></a>日期时间控制的操作

这些在 nbf / exp 时段外的尚未生效的密钥和过期密钥适合 decrypt、unwrap 和 verify 操作（不会返回 403 禁止访问）。 使用尚未生效状态的基本原理是允许在投入生产前测试密钥。 使用过期状态的基本原理是允许对秘钥有效期间创建的数据执行恢复操作。 此外，使用 Key Vault 策略，或通过将 enabled 密钥属性更新为 false 可以禁用访问密钥。

有关数据类型的详细信息，请参阅[数据类型](#data-types)。

有关其他可能的属性的详细信息，请参阅 [JSON Web 密钥 (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)。

### <a name="key-tags"></a>密钥标记

可以用标记的形式指定其他特定于应用程序的元数据。 Key Vault 支持多达 15 种标记，每种标记可以有 256 个字符的名称和 256 个字符的值。  

>[!Note]
>如果调用方具有该对象类型（密钥、机密或证书）的列出或获取权限，则调用方可读取标记。

###  <a name="key-access-control"></a>密钥访问控制

Key Vault 托管的密钥的访问控制是在充当密钥容器的 Key Vault 级别提供的。 在同一 Key Vault 中，密钥的访问控制策略不同于机密的访问控制策略。 用户可以创建一个或多个保管库来保存密钥，并且需要维护方案相应的密钥分段和管理。 密钥的访问控制与机密的访问控制无关。  

在保管库上的密钥访问控制条目中可以按用户/服务主体授予以下权限。 这些权限对密钥对象上允许的操作采取严密的镜像操作：  

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

## <a name="key-vault-secrets"></a>Key Vault 机密 

### <a name="working-with-secrets"></a>使用机密

从开发人员的角度来看，Key Vault API 接受机密值并将其作为字符串返回。 在内部，Key Vault 存储机密并将其作为八位字节序列（8 位字节）管理，每个字节的最大大小为 25k 字节。 Key Vault 服务不提供机密的语义。 它只是接受数据，然后加密和存储该数据，最后返回机密标识符（“id”）。 该标识符可用于稍后检索机密。  

对于高度敏感的数据，客户端应考虑对数据进行额外的保护。 例如，在 Key Vault 中存储数据之前，使用单独的保护密钥加密数据。  

Key Vault 还支持机密的 contentType 字段。 客户端可以指定机密的内容类型，以帮助在检索时解释机密数据。 此字段的最大长度为 255 个字符。 没有预定义的值。 建议用于解释机密数据的提示。 例如，实现可以将密码和证书都存储为机密，然后使用此字段进行区分。 没有预定义的值。  

### <a name="secret-attributes"></a>机密属性

除机密数据外，还可以指定以下属性：  

- *exp*：IntDate，可选，默认值为 **forever**。 exp（过期时间）属性标识在不应检索机密数据当时或之后的过期时间，[特定情况](#date-time-controlled-operations)除外。 此字段仅供参考，因为它通知密钥保管库服务用户可能无法使用特定机密。 其值必须是包含 IntDate 值的数字。   
- *nbf*：IntDate，可选，默认值为 **now**。 nbf（非过去）属性标识在不应检索机密数据之前的时间，[特定情况](#date-time-controlled-operations)除外。 此字段仅供参考。 其值必须是包含 IntDate 值的数字。 
- enabled：布尔型，可选，默认值为 true。 此属性指定是否可以检索机密数据。 enabled 属性与 nbf 和 exp 结合使用，如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作。 nbf 和 exp 时段外的操作会自动禁止，[特定情况](#date-time-controlled-operations)除外。  

在包含机密属性的任何响应中还包括以下其他只读属性：  

- *created*：IntDate，可选。 created 属性指示创建此版本的机密的时间。 如果机密在添加此属性之前创建，此值为 NULL。 其值必须是包含 IntDate 值的数字。  
- *updated*：IntDate，可选。 updated 属性指示更新此版本的机密的时间。 如果机密上次更新的时间早于添加此属性的时间，此值为 NULL。 其值必须是包含 IntDate 值的数字。

#### <a name="date-time-controlled-operations"></a>日期时间控制的操作

机密的获取操作在 nbf / exp 时段外适合尚未生效的机密和过期的机密。 对于尚未生效的机密，调用机密的“获取”操作可用于测试目的。 检索（获取）过期的密钥可以用于恢复操作。

有关数据类型的详细信息，请参阅[数据类型](#data-types)。  

### <a name="secret-access-control"></a>机密访问控制

Key Vault 中托管的机密的访问控制是在包含这些机密的 Key Vault 级别提供的。 在同一 Key Vault 中，机密的访问控制策略不同于密钥的访问控制策略。 用户可以创建一个或多个保管库来保存机密，并且需要维护方案相应的机密分段和管理。   

在保管库上的机密访问控制条目中可以按主体使用以下权限，这些权限对机密对象上允许的操作采取严密的镜像操作：  

- 针对机密管理操作的权限
  - *get*：读取机密  
  - *list*：列出 Key Vault 中存储的机密或机密版本  
  - *set*：创建机密  
  - *delete*：删除机密  
  - *recover*：恢复已删除的机密
  - *backup*：备份密钥保管库中的机密
  - *restore*：将备份机密还原到密钥保管库

- 针对特权操作的权限
  - *purge*：清除（永久删除）已删除的机密

有关使用机密的详细信息，请参阅 [Key Vault REST API 中的机密操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。 

### <a name="secret-tags"></a>机密标记  
可以用标记的形式指定其他特定于应用程序的元数据。 Key Vault 支持多达 15 种标记，每种标记可以有 256 个字符的名称和 256 个字符的值。  

>[!Note]
>如果调用方具有该对象类型（密钥、机密或证书）的列出或获取权限，则调用方可读取标记。

## <a name="key-vault-certificates"></a>Key Vault 证书

密钥保管库证书支持提供 x509 证书的管理和下列行为：  

-   允许证书所有者通过密钥保管库创建过程或通过导入现有证书来创建证书。 包括自签名证书和证书颁发机构生成的证书。
-   允许密钥保管库证书所有者实现 X509 证书的安全存储和管理，无需与私钥材料交互。  
-   允许证书所有者创建一个策略，指示密钥保管库来管理证书的生命周期。  
-   允许证书所有者为有关证书的过期和续订生命周期事件的通知提供联系信息。  
-   支持向所选的颁发者（密钥保管库合作伙伴 X509 的证书提供者/证书颁发机构）进行自动续订。

>[!Note]
>也允许使用非合作伙伴提供者/颁发机构，但将不支持自动续订功能。

### <a name="composition-of-a-certificate"></a>证书组合

创建 Key Vault 证书后，还可以创建具有相同名称的可寻址密钥和机密。 Key Vault 密钥允许密钥操作，Key Vault 机密允许以机密的形式检索证书值。 Key Vault 证书还包含公共 x509 证书元数据。  

标识符和证书版本与密钥和机密的类似。 使用 Key Vault 证书版本创建的特定版本的可寻址密钥和机密可用于 Key Vault 证书响应。
 
![证书是复杂的对象](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>可导出或不可导出密钥

创建 Key Vault 证书后，可以使用 PFX 或 PEM 格式的私钥从可寻址机密中检索该证书。 用于创建证书的策略必须指示密钥可导出。 如果策略指示密钥不可导出，则在作为机密检索私钥时，该私钥不包括在值中。  

可寻址密钥与不可导出的 KV 证书的相关性变得更高。 可寻址 KV 密钥的操作从用于创建 KV 证书的 KV 证书策略的“密钥使用情况”字段映射。  

证书支持以下两种类型的密钥：RSA 或 RSA HSM。 仅 RSA 允许可导出，RSA HSM 不支持。  

### <a name="certificate-attributes-and-tags"></a>证书属性和标记

除了证书元数据、可寻址密钥和可寻址机密外，Key Vault 证书还包含属性和标记。  

#### <a name="attributes"></a>属性

证书属性将镜像到创建 KV 证书时创建的可寻址密钥和机密的属性。  

Key Vault 证书具有以下属性：  

-   enabled：布尔型，可选，默认值为 true。 可以指定，以指示证书数据是否可以作为机密进行检索，或者可以作为密钥进行操作。 还可与 nbf 和 exp 结合使用，如果在 nbf 和 exp 之间出现操作，只有在 enabled 设置为 true 时，才允许该操作。 nbf 和 exp 时段外的操作会自动禁止。  

在响应中还包括以下其他只读属性：

-   *created*：IntDate：指示创建此版本的证书的时间。  
-   *updated*：IntDate：指示更新此版本的证书的时间。  
-   *exp*：IntDate：包含 x509 证书的过期日期的值。  
-   *nbf*：IntDate：包含 x509 证书的日期的值。  

> [!Note] 
> 如果 Key Vault 证书过期，则它是可寻址密钥，机密会无法操作。  

#### <a name="tags"></a>标记

 客户端指定的键值对字典，类似于密钥和机密中的标记。  

 > [!Note]
> 如果调用方具有该对象类型（密钥、机密或证书）的列出或获取权限，则调用方可读取标记。

### <a name="certificate-policy"></a>证书策略

证书策略包含有关如何创建和管理 Key Vault 证书生命周期的信息。 具有私钥的证书导入到密钥保管库时，将通过阅读 x509 证书创建一个默认策略。  

从零开始创建 Key Vault 证书时，需要提供策略。 该策略指定如何创建此 Key Vault 证书版本或下一个 Key Vault 证书版本。 建立策略后，便不需要使用连续创建操作创建将来的版本。 所有版本的 Key Vault 证书只有一个策略实例。  

在高级别，证书策略包含以下信息：  

-   X509 证书属性：包含主题名称、主题备用名称以及用于创建 x509 证书请求的其他属性。  
-   密钥属性：包含密钥类型、密钥长度、可导出密钥字段和重用密钥字段。 这些字段指示密钥保管库如何生成密钥。  
-   机密属性：包含可寻址机密的内容类型等机密属性以生成机密值，用于以机密的形式检索证书。  
-   生存期操作：包含 KV 证书生命周期的操作。 每个生存期操作包含：  

     - 触发器：通过距离到期的天数或生存期范围百分比指定  

     - 操作：指定操作类型 - emailContacts 或 autoRenew  

-   颁发者：有关用于颁发 x509 证书的证书颁发者的参数。  
-   策略属性：包含与策略关联的属性  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 到 Key Vault 使用情况的映射

下表表示 x509 密钥使用策略映射到在创建 Key Vault 证书过程中创建的密钥的有效密钥操作。

|X.509 密钥使用情况标记|Key Vault 密钥的操作|默认行为|
|----------|--------|--------|
|DataEncipherment|加密、解密| 不适用 |
|DecipherOnly|解密| 不适用  |
|DigitalSignature|签名、验证| Key Vault 在创建证书时默认为无使用规范 | 
|EncipherOnly|encrypt| 不适用 |
|KeyCertSign|签名、验证|不适用|
|KeyEncipherment|包装密钥、解包密钥| Key Vault 在创建证书时默认为无使用规范 | 
|NonRepudiation|签名、验证| 不适用 |
|crlsign|签名、验证| 不适用 |

### <a name="certificate-issuer"></a>证书颁发者

Key Vault 证书对象包含与所选证书颁发者提供者进行通信的配置以订购 x509 证书。  

-   具有以下 SSL 证书的证书颁发者提供者的 Key Vault 合作伙伴

|提供者名称|**位置**|
|----------|--------|
|DigiCert|公有云和 Azure 政府中的所有密钥保管库服务位置均支持|
|GlobalSign|公有云和 Azure 政府中的所有密钥保管库服务位置均支持|

可以在 Key Vault 中创建的证书颁发者之前，必须成功完成以下必需的步骤 1 和 2。  

1. 加入证书颁发机构 (CA) 提供者  

    -   组织管理员必须将他们的公司（例如， Contoso）加入到至少一个 CA 提供者。  

2. 管理员创建 Key Vault 的请求者证书以注册（和续订）SSL 证书  

    -   提供用于在密钥保管库中创建提供程序的颁发者对象的配置  

有关从证书门户创建颁发者对象的详细信息，请参阅 [Key Vault 证书博客](https://aka.ms/kvcertsblog)  

Key Vault 允许使用其他颁发者提供者的配置创建多个颁发者对象。 在创建颁发者对象以后，即可在一个或多个证书的策略中引用其名称。 在创建和续订证书的过程中从 CA 提供者请求 x509 证书时，引用颁发者对象可以指示 Key Vault 按颁发者对象中的规定使用配置。  

颁发者对象在保管库中创建，并且仅可用于同一个保管库中的 KV 证书。  

### <a name="certificate-contacts"></a>证书联系人

证书联系人包含联系人信息以发送由证书生存期事件触发的通知。 密钥保管库中的所有证书共享联系人信息。 如果保管库中的任何证书发生事件，所有指定联系人都会收到通知。  

如果证书的策略设置为自动续订，则在发生以下事件时发送通知。  

- 证书续订之前
- 证书续订之后，指出是否已成功续订证书，或是否存在错误，需要手动续订证书。  

  如果证书策略设置为手动续订（仅限电子邮件），则在续订证书时发送通知。  

### <a name="certificate-access-control"></a>证书访问控制

 证书的访问控制由 Key Vault 托管，并且由包含这些证书的 Key Vault 提供。 在同一 Key Vault 中，证书的访问控制策略不同于密钥和机密的访问控制策略。 用户可以创建一个或多个保管库来保存证书，以维护方案相应的证书分段和管理。  

 在密钥保管库上的机密访问控制条目中可以按主体使用以下权限，这些权限对机密对象上允许的操作采取严密的镜像操作：  

- 针对证书管理操作的权限
  - *get*：获取最新版本的证书或任何版本的证书 
  - *list*：列出最新版本的证书或任何版本的证书  
  - *update*：更新证书
  - *create*：创建 Key Vault 证书
  - *import*：将证书材料导入到 Key Vault 证书
  - *delete*：删除证书、策略及其所有版本  
  - *recover*：恢复已删除的证书
  - *backup*：备份密钥保管库中的证书
  - *restore*：将备份证书还原到密钥保管库
  - *managecontacts*：管理 Key Vault 证书联系人  
  - *manageissuers*：管理 Key Vault 证书颁发机构/颁发者
  - *getissuers*：获取证书的颁发机构/颁发者
  - *listissuers*：列出证书的颁发机构/颁发者  
  - *setissuers*：创建或更新 Key Vault 证书的颁发机构/颁发者  
  - *deleteissuers*：删除 Key Vault 证书的颁发机构/颁发者  
 
- 针对特权操作的权限
  - *purge*：清除（永久删除）已删除的证书

有关详细信息，请参阅 [Key Vault REST API 中的证书操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="azure-storage-account-key-management"></a>Azure 存储帐户密钥管理

Key Vault 可以管理 Azure 存储帐户密钥：

- 在内部，Key Vault 可以使用 Azure 存储帐户列出（同步）密钥。 
- Key Vault 定期重新生成（轮换）密钥。
- 响应调用方时永远不会返回密钥值。
- Key Vault 管理存储帐户和经典存储帐户的密钥。

有关详细信息，请参阅 [Azure Key Vault 存储帐户密钥](key-vault-ovw-storage-keys.md)

### <a name="storage-account-access-control"></a>存储帐户访问控制

授权用户或应用程序主体对托管的存储帐户执行操作时，可以使用以下权限：  

- 针对托管存储帐户和 SaS 定义操作的权限
  - *get*：获取有关存储帐户的信息 
  - *list*：列出 Key Vault 托管的存储帐户
  - *update*：更新存储帐户
  - *delete*：删除存储帐户  
  - *recover*：恢复删除的存储帐户
  - *backup*：备份存储帐户
  - *restore*：将备份存储帐户还原到 Key Vault
  - *set*：创建或更新存储帐户
  - *regeneratekey*：为存储帐户重写指定的密钥值
  - *getsas*：获取有关存储帐户的 SAS 定义的信息
  - *listsas*：列出存储帐户的存储 SAS 定义
  - *deletesas*：从存储帐户中删除 SAS 定义
  - *setsas*：创建或更新存储帐户的新 SAS 定义/属性

- 针对特权操作的权限
  - *purge*：清除（永久删除）托管存储帐户

有关详细信息，请参阅 [Key Vault REST API 中的存储帐户操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="see-also"></a>另请参阅

- [身份验证、请求和响应](authentication-requests-and-responses.md)
- [Key Vault 版本](key-vault-versions.md)
- [Key Vault 开发人员指南](/azure/key-vault/key-vault-developers-guide)
