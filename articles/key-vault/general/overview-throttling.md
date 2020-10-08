---
title: Azure Key Vault 限制指南
description: Key Vault 限制可限制并发调用数，以防止过度使用资源。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7aa33bb062abf748031b27df46d42e8f13aabfc3
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819965"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 限制指南

限制进程可在启动后，用于限定对 Azure 服务发出的并发调用数，以防止过度使用资源。 Azure Key Vault (AKV) 能够处理大量请求。 在请求数过多的情况下，限制客户端请求可帮助 AKV 服务保持最佳性能和可靠性。

限制值因情况而异。 例如，如果正在执行大量写入，则发生限制的可能性会比仅执行读取高。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何处理其限制？

Key Vault 中的服务限制可防止资源滥用，确保所有 Key Vault 客户端的服务质量。 当超过服务阈值时，Key Vault 会在一段时间内限制客户端发出其他任何请求，返回 HTTP 状态代码 429（请求过多），而请求会失败。 Key Vault 会跟踪向限制值返回 429 计数的失败请求。 

Key Vault 最初设计用于在部署时存储和检索机密。  世界已经演变，Key Vault 现在是在运行时用于存储和检索机密，而应用和服务往往希望将 Key Vault 当作数据库使用。  当前的限制不支持高吞吐率。

Key Vault 最初是根据 [Azure Key Vault 服务限制](service-limits.md)中指定的限制创建的。  下面是最大化 Key Vault 吞吐率的一些建议指导原则/最佳做法：
1. 确保已实施限制。  客户端必须遵循 429 指数退避策略，并确保按照下面的指导执行重试。
1. 将 Key Vault 流量划分到多个保管库和不同区域。   为每个安全性/可用性域使用单独的保管库。   如果你有 5 个应用，每个应用已划分到 2 个区域，则我们建议配置 10 个保管库，其中每个保管库包含应用和区域特有的机密。  所有事务类型的订阅范围限制是单个 Key Vault 限制的 5 倍。 例如，每个订阅的 HSM-其他事务数限制为 10 秒内 5,000 个事务。 考虑在服务或应用中缓存机密，以便同时减少 Key Vault 的直接 RPS，并/或处理基于突发的流量。  还可以将流量划分到不同的区域，以最大程度地减少延迟并使用不同的订阅/保管库。  不要将超过订阅限制的流量发送到单个 Azure 区域中的 Key Vault 服务。
1. 在内存中缓存从 Azure Key Vault 检索的机密，并尽可能地从内存中重新使用机密。  仅当缓存的副本停止工作（例如，因为它已在源中轮换）时，才从 Azure Key Vault 重新读取。 
1. Key Vault 是为你自己的服务机密设计的。   如果存储客户的机密（尤其是对于高吞吐量密钥存储方案），请考虑将密钥放在支持加密的数据库中或存储帐户中，并只将主密钥存储在 Azure Key Vault 中。
1. 可以在不访问 Key Vault 的情况下加密、包装和验证公钥操作，这不仅可以降低限制的风险，而且还能提高可靠性（前提是正确缓存公钥材料）。
1. 如果使用 Key Vault 存储服务的凭据，请检查该服务是否支持使用 Azure AD 身份验证直接进行身份验证。 这可以减少 Key Vault 中的负载，提高可靠性并简化代码，因为 Key Vault 现在可以使用 Azure AD 令牌。  许多服务已改用 Azure AD 身份验证。在[支持 Azure 资源托管标识的 Azure 服务](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)中查看最新列表。
1. 考虑在一个较长的时间段内错开负载/部署，使其不会超过当前的 RPS 限制。
1. 如果应用包含多个需要读取相同机密的节点，请考虑使用扇出模式，让一个实体从 Key Vault 读取机密，并扇出到所有节点。   仅在内存中缓存检索的机密。
如果你发现上述方案仍不能满足需求，请填写下表并联系我们，以确定可以添加多少附加容量（例如，以下示例仅供演示目的）。

| 保管库名称 | 保管库区域 | 对象类型（机密、密钥或证书） | 操作* | 键类型 | 密钥长度或曲线 | HSM 密钥？| 所需的稳定状态 RPS | 所需的峰值 RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | 键 | 签名 | EC | P-256 | 否 | 200 | 1000 |

\* 有关可能值的完整列表，请参阅 [Azure Key Vault 操作](/rest/api/keyvault/key-operations)。

如果增加容量已获批准，请注意容量增加后的以下考虑因素：
1. 数据一致性模型更改。 将吞吐量容量更高的保管库加入允许列表后，Key Vault 服务数据一致性保证会发生更改（需要满足更大量的 RPS，因为底层 Azure 存储服务无法跟进）。  简而言之：
  1. **不使用允许列表**：Key Vault 服务在后续调用（例如 SecretGet、KeySign）中会立即反映写入操作（例如 SecretSet、CreateKey）的结果。
  1. **使用允许列表**：Key Vault 服务在 60 秒内在后续调用（例如 SecretGet、KeySign）中反映写入操作（例如 SecretSet、CreateKey）的结果。
1. 客户端代码必须遵循 429 次重试的退避策略。 调用 Key Vault 服务的客户端代码在收到 429 响应代码时，不得立即重试 Key Vault 请求。  此处发布的 Azure Key Vault 限制指导建议在收到 429 Http 响应代码时立即应用指数退避。

如果出现限制值较高的有效业务用例，请与我们联系。

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何针对服务限制来限制应用

以下是在服务受到限制时应实施的**最佳做法**：
- 减少每个请求的操作数。
- 减少请求频率。
- 避免立即重试。 
    - 发出的所有请求要符合使用限制。

实现应用的错误处理时，请使用 HTTP 错误代码 429 检测是否需要限制客户端。 如果请求再次失败，错误代码为 HTTP 429，则仍会遇到 Azure 服务限制。 请继续使用推荐的客户端限制方法，重试请求直至成功。

实现指数退避的代码如下所示。 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


在客户端 C# 应用程序中使用此代码很简单。 

### <a name="recommended-client-side-throttling-method"></a>推荐的客户端限制方法

出现 HTTP 错误代码 429 时，请使用指数延迟方法开始限制客户端：

1. 等待 1 秒，然后重试请求
2. 如果仍受限制，请等待 2 秒，然后重试请求
3. 如果仍受限制，请等待 4 秒，然后重试请求
4. 如果仍受限制，请等待 8 秒，然后重试请求
5. 如果仍受限制，请等待 16 秒，然后重试请求

此时，应不会收到 HTTP 429 响应代码。

## <a name="see-also"></a>请参阅

若要深入了解 Microsoft 云中的限制，请参阅[限制模式](https://docs.microsoft.com/azure/architecture/patterns/throttling)。

