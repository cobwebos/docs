---
title: 排查 Azure 密钥保管库访问策略问题
description: 排查 Azure 密钥保管库访问策略问题
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 270e5ba1879b229fbe9f5e6c8692bd8b4e9eebc7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688634"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>排查 Azure 密钥保管库访问策略问题

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>如何确定访问密钥保管库的方式和时间？
在创建一个或多个 Key Vault 之后，可能需要监视 Key Vault 的访问方式、时间和访问者。 为此，可以为 Azure Key Vault 启用日志记录，有关启用日志记录的分步指南，请[了解详细信息](https://docs.microsoft.com/azure/key-vault/general/logging)。

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>如何监视保管库可用性、服务延迟周期或密钥保管库的其他性能指标？
如果开始扩展服务，则发送到密钥保管库的请求数将增加。 这可能会增加请求的延迟时间，并且在极端情况下，会导致请求被限制，从而影响服务的性能。 你可以监视密钥保管库性能指标，并获得特定阈值的警报，有关配置监视的分步指南，请[了解详细信息](https://docs.microsoft.com/azure/key-vault/general/alert)。

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>如何为每个密钥保管库对象分配访问控制？ 
将在此处通知每个机密/密钥/证书访问控制功能的可用性。[了解详细信息](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>如何使用访问控制策略提供密钥保管库身份验证？
在 Key Vault 中对基于云的应用程序进行身份验证的最简单方法是使用托管标识；有关详细信息，请参阅[使用应用服务托管标识访问 Azure Key Vault]( https://docs.microsoft.com/azure/key-vault/general/managed-identity)。
创建本地应用程序、执行本地开发或者无法使用托管标识时，可以改为手动注册服务主体，并使用访问控制策略提供对密钥保管库的访问权限。[了解详细信息](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)。


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>如何为 AD 组授予对密钥保管库的访问权限？
使用 Azure CLI az keyvault set-policy 命令或 Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet，为 AD 组授予对密钥保管库的权限。 有关示例，请参阅[为应用程序、Azure AD 组或用户授予对密钥保管库的访问权限](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault)。

应用程序还需要将至少一个标识和访问管理 (IAM) 角色分配给密钥保管库。 否则，它将无法登录并且会失败，因为没有足够权限来访问订阅。 具有托管标识的 Azure AD 组可能最多需要 8 小时才能刷新令牌并生效。

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>如何在不删除现有访问策略的情况下，使用 ARM 模板重新部署 Key Vault？
目前，重新部署 Key Vault ARM 会删除 Key Vault 中的任何访问策略，并将其替换为 ARM 模板中的访问策略。 Key Vault 访问策略没有增量选项。 若要在 Key Vault 中保留访问策略，需要读取 Key Vault 中现有的访问策略，并使用这些策略填充 ARM 模板以避免任何访问中断。

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>以下错误类型的建议故障排除步骤
* HTTP 401：未经身份验证的请求 - [故障排除步骤](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403：权限不足 - [故障排除步骤](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429：请求过多 - [故障排除步骤](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* 检查你是否具有对密钥保管库的删除访问权限：[密钥保管库访问策略](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* 如果在代码中向密钥保管库进行身份验证时遇到问题，请使用[身份验证 SDK](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>如果密钥保管库受到限制，我应该实施哪些最佳做法？
请遵循[此处](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits)记录的最佳做法

## <a name="next-steps"></a>后续步骤

了解如何对密钥保管库身份验证错误进行故障排除。 [Key Vault 故障排除指南](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
