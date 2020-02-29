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
ms.openlocfilehash: 6c4923e86f8678458d6301503043413fb8a5629b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197362"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault 限制指南

限制进程可在启动后，用于限定对 Azure 服务发出的并发调用数，以防止过度使用资源。 Azure Key Vault (AKV) 能够处理大量请求。 在请求数过多的情况下，限制客户端请求可帮助 AKV 服务保持最佳性能和可靠性。

限制值因情况而异。 例如，如果正在执行大量写入，则发生限制的可能性会比仅执行读取高。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault 如何处理其限制？

Key Vault 中的服务限制会阻止资源的滥用，并确保 Key Vault 的所有客户端的服务质量。 当超过服务阈值时，Key Vault 会在一段时间内限制该客户端发出的任何其他请求，返回 HTTP 状态代码429（请求过多），请求失败。 失败的请求将返回429计数，直至 Key Vault 跟踪的限制限制。 

Key Vault 最初设计用于在部署时存储和检索机密。  世界已发展，并 Key Vault 在运行时用于存储和检索机密，而应用和服务通常需要使用类似于数据库的 Key Vault。  当前限制不支持高吞吐量速率。

Key Vault 最初创建时具有[Azure Key Vault 服务限制](key-vault-service-limits.md)中指定的限制。  为了最大限度地提高您 Key Vault 的速度，下面是一些建议的指导原则/最佳做法，可实现吞吐量的最大化：
1. 确保已准备好阻止。  客户端必须遵循429的指数回退策略，并确保按照下面的指导进行重试。
1. 将 Key Vault 流量分为多个保管库和不同区域。   为每个安全/可用性域使用单独的保管库。   如果有五个应用，每个应用都位于两个区域中，则我们建议每个保管库，其中每个都包含应用和区域独有的机密。  所有事务类型的订阅范围限制为单个密钥保管库限制的5倍。 例如，每个订阅的 HSM-其他事务数限制5000为每个订阅10秒的事务数。 请考虑在服务或应用程序中缓存机密，同时将 RPS 直接减少到密钥保管库，并/或处理基于突发的流量。  你还可以将流量划分到不同的区域，以最大程度地减少延迟并使用不同的订阅/保管库。  不要向单个 Azure 区域中的 Key Vault 服务发送超过订阅限制。
1. 缓存从内存 Azure Key Vault 检索的机密，并尽可能地从内存中重新使用。  仅当缓存的副本停止工作（例如，因为它已在源旋转时），才能从 Azure Key Vault 重新读取。 
1. Key Vault 是为自己的服务机密设计的。   如果要存储客户的机密（特别是对于高吞吐量密钥存储方案），请考虑将密钥放置在数据库中或存储帐户中，并将主密钥存储在 Azure Key Vault 中。
1. 对 Key Vault 进行加密、包装和验证可在不访问的情况下执行公钥操作，这不仅会降低限制的风险，而且还可提高可靠性（前提是正确缓存了公钥材料）。
1. 如果使用 Key Vault 存储服务的凭据，请检查该服务是否支持 AAD 身份验证，以便直接进行身份验证。 这会减少 Key Vault 上的负载，提高可靠性并简化代码，因为 Key Vault 现在可以使用 AAD 令牌。  许多服务已迁移到使用 AAD 身份验证。 请参阅[支持 Azure 资源的托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)中的当前列表。
1. 请考虑在更长的一段时间内错开负载/部署，使其保持在当前 RPS 限制下。
1. 如果你的应用程序包含多个需要读取相同机密的节点，则考虑使用一个扇出模式，其中一个实体从 Key Vault 读取机密，并将风扇读入所有节点。   仅在内存中缓存检索的机密。
如果你发现以上仍不能满足你的需求，请填写下表并联系我们，以确定可添加哪些额外容量（例如，仅出于说明目的，请参阅下面的示例）。

| 保管库名称 | 保管库区域 | 对象类型（Secret、Key 或 Cert） | 操作 * | 键类型 | 密钥长度或曲线 | HSM 密钥？| 需要稳定状态 RPS | 需要的最大 RPS |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | 密钥 | 签名 | EC | P-256 | 否 | 200 | 1000 |

\* 获取可能值的完整列表，请参阅[Azure Key Vault 操作](/rest/api/keyvault/key-operations)。

如果已批准额外容量，请在容量增加时记下以下内容：
1. 数据一致性模型发生更改。 如果允许使用额外的吞吐量容量列出某个保管库，则 Key Vault 服务数据一致性保证会发生更改（需要满足更大的卷 RPS，因为基础 Azure 存储服务无法保持）。  简而言之：
  1. **不带允许列表**： Key Vault 服务将反映写操作的结果（例如 SecretSet，CreateKey）（例如 SecretGet, KeySign).
  1. **使用允许列表**： Key Vault 服务将反映写入操作的结果（例如 SecretSet，CreateKey）在后续调用中的60秒内（例如 SecretGet, KeySign).
1. 客户端代码必须服从429重试的回退策略。 调用 Key Vault 服务的客户端代码在收到429响应代码时不得立即重试 Key Vault 请求。  此处发布的 Azure Key Vault 限制指南建议在收到 429 Http 响应代码时应用指数回退。

如果出现限制值较高的有效业务用例，请与我们联系。

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>如何针对服务限制来限制应用

以下是你的服务受到限制时应实现的**最佳实践**：
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
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


在客户端C#应用程序中使用此代码非常简单。 

### <a name="recommended-client-side-throttling-method"></a>推荐的客户端限制方法

出现 HTTP 错误代码 429 时，请使用指数延迟方法开始限制客户端：

1. 等待 1 秒，然后重试请求
2. 如果仍受限制，请等待 2 秒，然后重试请求
3. 如果仍受限制，请等待 4 秒，然后重试请求
4. 如果仍受限制，请等待 8 秒，然后重试请求
5. 如果仍受限制，请等待 16 秒，然后重试请求

此时，应不会收到 HTTP 429 响应代码。

## <a name="see-also"></a>另请参阅

若要深入了解 Microsoft 云中的限制，请参阅[限制模式](https://docs.microsoft.com/azure/architecture/patterns/throttling)。

