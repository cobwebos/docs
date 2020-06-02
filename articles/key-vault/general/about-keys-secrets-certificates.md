---
title: 关于 Azure 密钥保管库密钥、机密和证书 - Azure 密钥保管库
description: Azure Key Vault REST 接口概述以及密钥、机密和证书的开发人员详细信息。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 627dfee81cb10e4e442b3cefb10d786d87d5c81d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005874"
---
# <a name="about-keys-secrets-and-certificates"></a>关于密钥、机密和证书

凭借 Azure Key Vault，Microsoft Azure 应用程序和用户能够存储和使用多种类型的机密/密钥数据：

- 加密密钥：支持多种密钥类型和算法，可以对高价值的密钥使用硬件安全模块 (HSM)。 有关详细信息，请参阅[关于密钥](../keys/about-keys.md)。
- 机密：提供机密（例如密码和数据库连接字符串）的安全存储。 有关详细信息，请参阅[关于机密](../secrets/about-secrets.md)。
- 证书：支持基于密钥和机密并且添加了自动续订功能的证书。 有关详细信息，请参阅[关于证书](../certificates/about-certificates.md)。
- Azure 存储：可以管理 Azure 存储帐户的密钥。 在内部，Key Vault 可以使用 Azure 存储帐户列出（同步）密钥，并定期重新生成（轮换）密钥。 有关详细信息，请参阅[使用 Key Vault 管理存储帐户密钥](../secrets/overview-storage-keys.md)。

有关 Key Vault 的更多常规信息，请参阅[关于 Azure Key Vault](overview.md)。

## <a name="data-types"></a>数据类型

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

## <a name="objects-identifiers-and-versioning"></a>对象、标识符和版本控制

对于存储在 Key Vault 中的对象，在创建了某一对象的新实例后，这些对象就会受到版本控制。 每个版本都分配有唯一标识符和 URL。 首次创建一个对象时，该对象被赋予了一个唯一的版本标识符，并标记为当前版本的对象。 创建与对象同名的新实例会向新对象赋予一个唯一的版本标识符，并使其成为当前版本。  

可以通过指定版本对 Key Vault 中的对象进行寻址，或者通过忽略版本对对象的当前版本进行操作。 例如，给定名称为 `MasterKey` 的密钥，执行操作而不指定版本会导致系统使用最新的可用版本。 使用特定于版本的标识符执行操作会导致系统使用该特定版本的对象。  

Key Vault 中的对象通过 URL 唯一标识。 不管地理位置如何，系统中都不存在两个具有相同 URL 的对象。 对象的完整 URL 称为对象标识符。 URL 由标识 Key Vault 的前缀、对象类型、用户提供的对象名称和对象版本组成。 对象名称不区分大小写且不可变。 不包括对象版本的标识符称为基本标识符。  

有关详细信息，请参阅[身份验证、请求和响应](authentication-requests-and-responses.md)

对象标识符具有以下常规格式：  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

其中：  

|||  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault 服务中的保管库名称。<br /><br /> Key Vault 名称由用户选择，并且全局唯一。<br /><br /> Key Vault 的名称必须是 3-24 个字符，且仅包含 0-9、a-z、A-Z 和 - 的字符串。|  
|`object-type`|对象的类型（“密钥”、“机密”或“证书”）。|  
|`object-name`|`object-name` 是用户提供名称，在 Key Vault 中必须保持唯一。 该名称必须是 1-127 个字符的字符串，以字母开头且仅包含 0-9、a-z、A-Z 和 -。|  
|`object-version`|`object-version` 是系统生成的 32 个字符的字符串标识符，可以选择用来对某个对象的唯一版本进行寻址。|  

## <a name="next-steps"></a>后续步骤

- [关于密钥](../keys/about-keys.md)
- [关于机密](../secrets/about-secrets.md)
- [关于证书](../certificates/about-certificates.md)
- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
