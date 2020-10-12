---
title: 常见问题解答
description: 有关 Microsoft Azure 证明的常见问题的解答
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89236597"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Microsoft Azure 证明常见问题

本文提供了有关 [Azure 认证](overview.md)的一些最常见问题的解答。

如果本文未解决你的 Azure 问题，还可以在 [azure 支持页](https://azure.microsoft.com/support/options/)上提交 azure 支持请求。

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>什么是 Azure PCK 缓存服务及其在 enclave 认证中的角色

Azure PCK cache service 定义 azure [机密计算 () ACC ](../confidential-computing/overview.md) 的 azure 安全基线，并缓存数据。  (TEEs) 验证受信任的执行环境时，Azure 证明会进一步使用缓存的信息。  

Azure PCK 缓存服务：
   - 提供高可用性 
   - 减少外部托管服务和 internet 连接上的依赖关系。
   - 提取最新版本的 Intel 证书、Crl、受信任的计算基础 (TCB) 信息并将 ACC 节点 Enclave 标识从 Intel 中引用。 这样，在验证 TEEs 时，该服务会确认 azure 证明所引用的 Azure 安全基准，大大减少了 Intel 证书的失效或吊销导致的证明失败  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>Azure 证明在非 Azure 环境中是否支持 SGX 证明

Azure 证明依赖于 Azure PCK 缓存服务所述的安全基准来验证 TEEs。 Azure PCK 缓存服务当前设计为仅支持 Azure 机密计算节点。 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Azure 证明对证明 SGX enclaves 执行了哪些验证

Azure 证明是一个统一的框架，可用于远程证明不同类型的 TEEs。 Azure 证明：

   - 验证已签名 enclave 的受信任的根是否属于 Intel。
   - 验证 enclave 报价是否满足 Azure PCK 缓存服务定义的 Azure 安全基线。
   - 验证证明请求对象中的 Enclave 保存数据 (EHD) 的 SHA256 哈希是否与 Enclave 引号中 reportData 字段的前32个字节匹配。
   - 允许客户创建证明提供者并配置自定义策略。 除了上述验证外，Azure 证明还会针对策略评估 enclave 引号。 策略定义 enclave 的授权规则，并规定用于生成证明令牌的颁发规则。 若要确认所需软件是否在 enclave 中运行，客户可以添加授权规则，以验证 enclave 引号中的 **mrsigner** 和 **mrenclave** 字段是否与客户二进制文件的值匹配。

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>验证程序如何获取 Azure 证明支持的 SGX 证明的宣传品

通常，对于 Intel 为信任根的证明模型，证明客户端会与 enclave Api 通信，以获取 enclave 证据。 Enclave Api 在内部调用 Intel PCK 缓存服务，以获取要证明的节点的 Intel 证书。 证书用于对 enclave 的证据进行签名，从而生成远程 attestable 的宣传品。  

可以为 Azure 证明实现相同的过程。 但是，若要利用 Azure PCK 缓存服务提供的权益，请在安装 ACC 虚拟机后，安装 [AZURE DCAP 库](https://www.nuget.org/packages/Microsoft.Azure.DCAP)。 根据与 Intel 协议的协议，在安装 Azure DCAP 库时，生成 enclave 证据的请求将从 Intel PCK 缓存服务重定向到 Azure PCK 缓存服务。 Azure DCAP 库在基于 Windows 和 Linux 的环境中受支持。

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>如何从其他证明模型转移到 Azure 证明

- 安装 Azure 机密计算虚拟机后，安装 Azure DCAP 库 ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) 以利用 Azure PCK 缓存服务提供的权益。
- 需要创作远程认证客户端，以便检索 enclave 证据并向 Azure 证明发送请求。 请参阅 [代码示例](/samples/browse/?expanded=azure&terms=attestation) 以供参考 
- 可将证明请求发送到默认提供程序的 REST API 终结点或自定义证明提供程序 
- Azure 证明 Api 受到 Azure AD 身份验证的保护。 因此，调用证明 Api 的客户端必须能够获取和传递证明请求中的有效 Azure AD 访问令牌 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>依赖方如何验证证明令牌的完整性

使用自签名证书对 Azure 证明生成的证明令牌进行签名。 证书通过 [OpenID 元数据终结点](/rest/api/attestation/metadataconfiguration/get)公开。 依赖方可以从此终结点检索签名证书，并执行证明令牌的签名验证。 证明令牌的有效时间为8小时。 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>如何从 OpenID metadata 终结点标识用于签名验证的证书

OpenID 元数据终结点中公开的多个证书对应于不同的用例 (例如，Azure 证明支持的 SGX 认证) 。 根据 [RFC 7515](https://tools.ietf.org/html/rfc7515)指定的标准，具有密钥 ID (与证明令牌标头中的 *童趣* 参数) 相匹配的证书将用于签名验证。 如果未找到匹配的 **孩子** ，则应该尝试使用 OpenID 元数据终结点公开的所有证书。

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>依赖方能否与验证的受信任执行环境共享机密 (TEEs) 

在 enclave 中生成的公钥可以在 Enclave 保存的数据 (EHD 客户端发送到 Azure 证明的证明请求对象的) 属性中表示。 确认 EHD 的 SHA256 哈希是否以引号的 reportData 字段表示时，Azure 证明在证明令牌中包含 EHD。 依赖方可以使用已验证的证明响应中的 EHD 加密机密，并与 enclave 共享。 有关详细信息，请参阅 [Azure 证明基本概念](basic-concepts.md) 。
