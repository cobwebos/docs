---
title: "Azure 资源策略的 RequestDisallowedByPolicy 错误 | Microsoft Docs"
description: "说明 RequestDisallowedByPolicy 错误的原因。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 2e821c0369c6f01a7f09361c1093259429a79fa6
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 资源策略的 RequestDisallowedByPolicy 错误

本文说明了 RequestDisallowedByPolicy 错误的原因，它还提供了此错误的解决方案。

## <a name="symptom"></a>症状

尝试在部署过程中执行操作时，可能会收到阻止完成操作的 **RequestDisallowedByPolicy** 错误。 以下示例显示错误：

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

### <a name="method-1"></a>方法 1

在 PowerShell 中，提供该策略标识符作为 `Id` 参数，检索有关阻止部署的策略的详细信息。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>方法 2 

在 Azure CLI 2.0 中，提供策略定义的名称： 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解决方案

为了安全性或合规性，IT 部门可能会强制执行禁止创建公共 IP 地址、网络安全组、用户定义路由或路由表的资源策略。 **症状**部分中的错误消息显示名为 **regionPolicyDefinition** 的策略。 你的策略可能具有不同的名称。
若要解决此问题，请协助 IT 部门查看资源策略，并确定如何执行符合这些策略的请求操作。

有关详细信息，请参阅以下文章：

- [资源策略概述](resource-manager-policy.md)
- [通过门户查看策略分配](resource-manager-policy-portal.md)
