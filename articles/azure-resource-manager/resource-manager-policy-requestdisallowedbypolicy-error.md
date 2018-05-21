---
title: Azure 资源策略的 RequestDisallowedByPolicy 错误 | Microsoft Docs
description: 说明 RequestDisallowedByPolicy 错误的原因。
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 474400d92660b68fd7fef906216b8e37c6e8c94d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 资源策略的 RequestDisallowedByPolicy 错误

本文说明了 RequestDisallowedByPolicy 错误的原因，它还提供了此错误的解决方案。

## <a name="symptom"></a>症状

部署过程中，可能会收到阻止创建资源的 RequestDisallowedByPolicy 错误。 以下示例显示错误：

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>故障排除

若要检索有关阻止部署的策略的详细信息，请使用以下方法之一：

### <a name="powershell"></a>PowerShell

在 PowerShell 中，提供该策略标识符作为 `Id` 参数，检索有关阻止部署的策略的详细信息。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 2.0 中，提供策略定义的名称：

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解决方案

为了安全性和符合性，订阅管理员可能会分配限制资源部署方式的策略。 例如，订阅可能具有阻止创建公共 IP 地址、网络安全组、用户定义的路由或路由表的策略。 “症状”部分中的错误消息显示策略的名称。
要解决此问题，请查看资源策略，并确定如何部署符合这些策略的资源。

有关详细信息，请参阅以下文章：

- [什么是 Azure 策略？](../azure-policy/azure-policy-introduction.md)
- [创建和管理策略以强制实施符合性](../azure-policy/create-manage-policy.md)
