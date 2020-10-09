---
title: Azure Key Vault 日志记录 | Microsoft Docs
description: 了解如何通过启用 Azure Key Vault 的日志记录来监视对密钥保管库的访问权限，以便在提供的 Azure 存储帐户中保存信息。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739776"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 日志记录

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在创建一个或多个 Key Vault 之后，可能需要监视 Key Vault 的访问方式、时间和访问者。 为此，可以启用 Azure Key Vault 日志记录，以便在提供的 Azure 存储帐户中保存信息。 系统会自动为指定的存储帐户创建名为 **insights-logs-auditevent** 的新容器。 可以使用此同一个存储帐户来收集多个 Key Vault 的日志。

最多在执行 Key Vault 操作 10 分钟后，就能访问其日志记录信息。 但大多数情况下不用等待这么长时间。  存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。

有关 Key Vault的概述信息，请参阅[什么是 Azure Key Vault？](overview.md)。 有关 Key Vault 可用位置的信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/key-vault/)。 有关[将 Azure Monitor 用于 Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview) 的信息。

## <a name="interpret-your-key-vault-logs"></a>解释 Key Vault 日志

每个 Blob 存储为文本，并格式化为 JSON Blob。 让我们看一个示例日志项。 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出了字段的名称和描述：

| 字段名称 | 说明 |
| --- | --- |
| **time** |日期和时间 (UTC)。 |
| **resourceId** |Azure 资源管理器资源 ID。 对于密钥保管库日志而言，这始终是密钥保管库资源 ID。 |
| **operationName** |下一份表格中所述操作的名称。 |
| **operationVersion** |客户端请求的 REST API 版本。 |
| **category** |结果的类型。 对于 Key Vault 日志而言，`AuditEvent` 是唯一可用值。 |
| **resultType** |REST API 请求的结果。 |
| **resultSignature** |HTTP 状态。 |
| **resultDescription** |有关结果的其他描述（如果有）。 |
| **durationMs** |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| **callerIpAddress** |发出请求的客户端的 IP 地址。 |
| **correlationId** |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端 (Key Vault) 日志相关联。 |
| **identity** |在 REST API 请求中提供的令牌中的标识。 与通过 Azure PowerShell cmdlet 发出请求一样，这通常是“用户”、“服务主体”，或者“用户+应用 ID”的组合。 |
| **properties** |此字段根据操作 (**operationName**) 包含不同的信息。 在大多数情况下，此字段包含客户端信息（客户端传递的用户代理字符串）、具体 REST API 请求 URI 和 HTTP 状态代码。 此外，在根据请求（例如，KeyCreate 或 VaultGet）返回对象时，此字段还将包含密钥 URI（`id` 形式）、保管库 URI 或机密 URI。 |

**operationName** 字段值采用 *ObjectVerb* 格式。 例如：

* 所有 Key Vault 操作采用 `Vault<action>` 格式，例如 `VaultGet` 和 `VaultCreate`。
* 所有密钥操作采用 `Key<action>` 格式，例如 `KeySign` 和 `KeyList`。
* 所有机密操作采用 `Secret<action>` 格式，例如 `SecretGet` 和 `SecretListVersions`。

下表列出了 **operationName** 值和对应的 REST API 命令：

### <a name="operation-names-table"></a>操作名称表

| operationName | REST API 命令 |
| --- | --- |
| **身份验证** |通过 Azure Active Directory 终结点进行身份验证 |
| **VaultGet** |[获取有关密钥保管库的信息](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[创建或更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[删除密钥保管库](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[列出资源组中的所有密钥保管库](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[创建密钥](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[获取有关密钥的信息](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[将密钥导入保管库](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[备份密钥](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[删除密钥](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[还原密钥](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[使用密钥签名](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[使用密钥验证](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[包装密钥](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[解包密钥](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[使用密钥加密](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[使用密钥解密](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[更新密钥](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[列出保管库中的密钥](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[列出密钥的版本](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[创建机密](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[获取机密](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[更新机密](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[删除机密](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[列出保管库中的机密](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[列出机密的版本](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | 保管库访问策略更改事件已发布 |
| **SecretNearExpiryEventGridNotification** |机密即将过期事件已发布 |
| **SecretExpiredEventGridNotification** |机密已过期事件已发布 |
| **KeyNearExpiryEventGridNotification** |密钥即将过期事件已发布 |
| **KeyExpiredEventGridNotification** |密钥已过期事件已发布 |
| **CertificateNearExpiryEventGridNotification** |证书即将过期事件已发布 |
| **CertificateExpiredEventGridNotification** |证书已过期事件已发布 |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的 Key Vault 解决方案查看 Key Vault `AuditEvent` 日志。 在 Azure Monitor 日志中，可以使用日志查询来分析数据并获取所需的信息。 

有关详细信息，包括如何进行设置，请参阅 [Azure Monitor 中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a><a id="next"></a>后续步骤

有关在 .NET Web 应用程序中使用 Azure Key Vault 的教程，请参阅[从 Web 应用程序使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。

有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](developers-guide.md)。

有关 Azure Key Vault 的 Azure PowerShell 1.0 cmdlet 列表，请参阅 [Azure Key Vault cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)。
