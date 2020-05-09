---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c1f91cf9de0ec7d4f4d3877d10cef7b5f9dac65c
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837940"
---
|名称 |说明 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[允许的存储帐户 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7433c107-6db4-4ad1-b57a-a76dce0154a1) |此策略可用于指定组织可部署的一组存储帐户 SKU。 |拒绝 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/AllowedStorageSkus_Deny.json) |
|[审核对存储帐户的无限制网络访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |在存储帐户防火墙设置中审核无限制的网络访问权限。 应该配置网络规则，以便只有来自许可网络的应用程序才能访问存储帐户。 若要允许来自特定 Internet 或本地客户端的连接，可以向来自特定 Azure 虚拟网络或到公共 Internet IP 地址范围的流量授予访问权限 |Audit, Deny, 已禁用 |1.1.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[在存储帐户上部署高级威胁防护](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361c2074-3595-4e5d-8cab-4f21dffc835c) |此策略会在存储帐户上启用高级威胁防护。 |DeployIfNotExists, 已禁用 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAdvancedThreatProtection_Deploy.json) |
|[应为存储帐户启用异地冗余存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |此策略将审核未启用异地冗余存储的任何存储帐户。 |Audit, 已禁用 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[应启用安全传输到存储帐户](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |审核存储帐户中安全传输的要求。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器和服务之间的身份验证并保护传输中的数据免受中间人攻击、窃听和会话劫持等网络层攻击 |Audit, Deny, 已禁用 |1.0.1 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[存储帐户应允许从受信任的 Microsoft 服务进行访问](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |某些与存储帐户交互的 Microsoft 服务在网络上运行，但这些网络无法通过网络规则获得访问权限。 若要帮助此类服务按预期方式工作，请允许受信任的 Microsoft 服务集绕过网络规则。 这些服务随后会使用强身份验证访问存储帐户。 |Audit, Deny, 已禁用 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |
|[存储帐户应迁移到新的 Azure 资源管理器资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |使用新的 Azure 资源管理器为存储帐户提供安全增强功能，例如：更强大的访问控制 (RBAC)、更好的审核、基于 Azure 资源管理器的部署和监管、对托管标识的访问权限、访问密钥保管库以获取机密、基于 Azure AD 的身份验证以及对标记和资源组的支持，以简化安全管理 |Audit, Deny, 已禁用 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |
