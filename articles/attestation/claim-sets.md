---
title: Azure 证明- 声明集
description: Azure 证明的声明集。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 555dccbd3c2dfe61bac5891514deface6f8a877d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236826"
---
# <a name="claim-sets"></a>声明集

在证明 enclave 的过程中使用 Microsoft Azure 证明生成的声明可以分为以下几类：

- **传入声明**：分析证明证据后 Microsoft Azure 证明生成的声明。

- **传出声明**：作为 Azure 证明的输出创建的声明。 它包含应最终出现在证明令牌中的所有声明。

- **属性声明**：作为 Azure 证明的输出创建的声明。 它包含表示证明令牌的属性的所有声明，如报表的编码、报表的有效持续时间等。

以下声明由 JWT RFC 定义，并在响应对象中由 Azure 证明使用：

- **“iss”（证书颁发者）声明**：“iss”（证书颁发者）声明指定颁发 JWT 的主体。 此声明的处理通常特定于应用程序。 “iss”值是一个区分大小写的字符串，其中包含 StringOrURI 值。
- **“iat”（颁发时间）声明**：“iat”（颁发时间）声明指定颁发 JWT 的时间。 此声明可用于确定 JWT 的期限。 它的值必须是包含 NumericDate 值的数字。
- **“exp”（到期时间）声明**：“exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 “exp”（到期时间）声明要求当前日期/时间必须早于“exp”声明中列出的到期日期/时间。

  注意：颁发时间 (iat) 增加了 5 分钟的余量，以适应时钟偏差。
- **“nbf”（不早于）声明**：“nbf”（不早于）声明指定将在哪个时间之后接受 JWT 的处理。 处理“nbf”声明要求当前日期/时间必须晚于或等于“nbf”声明中列出的非过去日期/时间。
  注意：颁发时间 (iat) 增加了 5 分钟的余量，以适应时钟偏差。

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>SGX enclave 中由 Azure 证明颁发的声明

### <a name="incoming-claims"></a>传入声明 

- **$is-debuggable**：布尔值，指示 enclave 是否已启用调试
- **$sgx-mrsigner**：Quote 的“mrsigner”字段的十六进制编码值
- **$sgx-mrenclave**：Quote 的“mrenclave”字段的十六进制编码值
- **$product-id**
- **$svn**：在 Quote 中编码的安全版本号 
- **$tee**：enclave 的类型 

### <a name="outgoing-claims"></a>传出声明

- **is-debuggable**：布尔值，指示 enclave 是否已启用调试
- **sgx-mrsigner**：Quote 的“mrsigner”字段的十六进制编码值
- **sgx-mrenclave**：Quote 的“mrenclave”字段的十六进制编码值
- **product-id**
- **svn**：在 Quote 中编码的安全版本号 
- **tee**：enclave 的类型 
- **maa-ehd**：证明请求中指定的“Enclave Held Data”的 Base64Url 编码版本 
- **ehd**：证明请求中指定的“Enclave Held Data”的 Base64Url 编码版本 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>VBS enclave 中由 Azure 证明颁发的声明

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>传入声明（也可用作传出声明）

- **aikValidated**：布尔值，包含证明标识密钥 (AIK) 证书是否已经过验证的信息。
- **aikPubHash**：包含 base64(SHA256(DER 格式的 AIK 公钥)) 的字符串。
- **tpmVersion**： 包含受信任的平台模块 (TPM) 主版本的整数值。
- **secureBootEnabled**：布尔值，指示是否启用安全启动。
- **iommuEnabled**：布尔值，指示是否启用输入输出内存管理单元 (Iommu)。
- **bootDebuggingDisabled**：布尔值，指示是否禁用启动调试。
- **notSafeMode**：布尔值，指示 Windows 是否未在安全模式下运行。
- **notWinPE**：布尔值，指示 Windows 是否未在 WinPE 模式下运行。
- **vbsEnabled**：布尔值，指示是否启用 VBS。
- **vbsReportPresent**：布尔值，指示 VBS enclave 报表是否可用。
- **enclaveAuthorId**：包含 enclave 创作者 ID 的 Base64Url 编码值的字符串值 - enclave 主模块的创作者标识符。
- **enclaveImageId**：包含 enclave 映像 ID 的 Base64Url 编码值的字符串值 - enclave 主模块的映像标识符。
- **enclaveOwnerId**：包含 enclave 所有者 ID 的 Base64Url 编码值的字符串值 - enclave 的所有者标识符。
- **enclaveFamilyId**：包含 enclave 系列 ID 的 Base64Url 编码值的字符串值。Enclave 的主模块的系列标识符。
- **enclaveSvn**：包含 enclave 主模块的安全版本号的整数值。
- **enclavePlatformSvn**：包含承载 enclave 的平台的安全版本号的整数值。
- **enclaveFlags**：EnclaveFlags 声明是一个整数值，其中包含用于描述 enclave 的运行时策略的标志。
  
### <a name="outgoing-claims"></a>传出声明

- **policy_hash**：字符串值，其中包含由 BASE64URL(SHA256(BASE64URL(UTF8(Policy text)))) 计算的策略文本的 SHA256 哈希值。
- **policy_signer**：字符串值，其中包含具有公钥的 JWK，或签名策略标头中存在的证书链。
- **ver（版本）** :包含报表说明的字符串值。 当前为 1.0。
- **cnf（确认）Claim**：“Cnf”声明用于指定所有权证明密钥。 RFC 7800 中定义的确认声明包含表示为 JSON Web 密钥 (JWK) 对象 (RFC 7517) 的证明 enclave 密钥的公共部分。
- **rp_data（信赖方数据）** ：在请求中指定的信赖方数据（如有），由信赖方用作 nonce，以保证报表的新鲜度。
- **“jti”(JWT ID) 声明**：“jti”(JWT ID) 声明为 JWT 提供唯一标识符。 标识符值的赋值方式可确保几乎不会将相同的值意外分配给不同的数据对象。

### <a name="property-claims"></a>属性声明

- **report_validity_in_minutes**：表示令牌有效时间的整数声明。
  - **默认值（时间）** ：一天，以分钟为单位。
  - **最大值（时间）** ：一年，以分钟为单位。
- **omit_x5c**：布尔声明，指示 Azure 证明是否应忽略用于证明服务真实性的证书。 如果为 true，则 x5t 将添加到证明令牌。 如果为 false（默认值），则 x5c 将添加到证明令牌。

## <a name="next-steps"></a>后续步骤
- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
